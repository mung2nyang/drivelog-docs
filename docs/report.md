# docs/report.md — 현재 슬라이스 착수지시서

34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 114 일지 저장을 서버 함수 한 번으로 — 하루 기록 줄 + 콜 상세, DB `0028` `[x]`

> 2026-10-26 보리 "114 승인, [x] 처리하고 문서 커밋해".

보리 "117·118 권장순서대로, 34-C 끝났어 진행해"(2026-10-26). 근거: 외부 코드 리뷰(2026-10-26) → 로드맵 114~118 `[확인: 2026-10-26 보리]`, 순서 114 → 117 → 115 → 116 → 118. 34-C `[x]`(react-app `b154abb`·DB `0027`) 다음. 69(`0023` 콜 지우기·넣기 한 번)의 남은 반쪽. 이번엔 **일지 저장만** — 빈 날 삭제(116)·지출(118)은 따로.

### 쉬운 요약
- 로그인 계정이 일지를 저장하면 지금은 서버에 두 번 따로 보낸다: ① 그날 휴무·고정 노선 횟수·파렛트 횟수 ② 그날 콜 목록. ①만 되고 ②가 실패하면, 새로고침 뒤 "고정 2회인데 새 콜은 없음"처럼 새 값과 옛 콜이 섞인다.
- 서버에 둘을 한 번에 저장하는 함수를 하나 새로 두고, 앱은 그 함수를 한 번만 부른다. 그러면 둘 다 저장되거나, 둘 다 저장 전 그대로다.
- 화면·문구·저장 순서(성공해야 화면에 반영, 실패면 "저장에 실패했습니다…")는 그대로다.

### 현재 상태 (코드 확인)
- `data/sync/dayLogCloudCommit.ts` `writeDayKeyToServer`(61~73줄): `upsertDailyLog`(`data/sync/syncWorkData.ts` 6~21줄, `daily_logs` upsert 1회) → 세션 확인 → `replaceTransportDetails`(39~59줄, 서버 함수 `replace_transport_details` 1회) → 세션 확인. 두 요청 사이에 끊기면 ①만 남는다.
- `upsertDailyLog`를 부르는 곳은 이 파일뿐이다(`grep -rn "upsertDailyLog" src` — `dayLogCloudCommit.ts` 18·65줄). 지출 저장은 자기 함수(`syncExpenseItems.ts` `ensureDailyLog`)를 쓴다. `syncWorkData.ts` 1줄 주석 "지출 저장이 같이 쓴다"는 이미 틀렸다.
- 두 표의 서버 권한 규칙은 모양이 같다(`supabase/migrations/0010_driver_access_by_link_period.sql` 35~66줄: 작성·수정·삭제 모두 "차량 주인 또는 그 날짜 연동 기사", 작성은 `user_id = auth.uid()` 추가). 앱은 두 요청에 같은 차량·날짜·작성자를 보낸다 — 그래서 ②만 늘 거절되는 경우는 없고, 끊김·일시 오류 때만 생긴다.
- 작성자 칸 트리거(`0027`)는 "저장하는 본인으로 바꾸기"를 허락한다 — 지금 upsert와 같은 값을 보내므로 새 함수도 걸리지 않는다.
- 이 저장 길을 쓰는 곳: 일지 화면 저장(`commitMainDayLogToCloud`), 미수금 일괄 입금 처리·백업 계정으로 불러오기(`commitMainDayLogMapToCloud` — 날짜마다 같은 `writeDayKeyToServer`).

### 목표 상태 (기대 동작)
1. 로그인 일지 저장(날짜 하나·여러 날짜 모두)은 그 날짜마다 서버 요청 **한 번**(`save_day_log`)만 낸다. `daily_logs` upsert·`replace_transport_details`를 앱이 따로 부르지 않는다.
2. 서버 함수 안에서 하루 기록 줄 저장과 콜 바꾸기 중 하나라도 실패하면 둘 다 저장 전으로 돌아간다(함수 하나 = 한 트랜잭션).
3. 서버에 들어가는 값은 지금과 같다: 하루 기록 줄 7칸(`user_id`·`vehicle_id`·`work_date`·`is_off`·`fixed_count`·`pallet_count`·`raw` — `raw`에 콜·지출 목록 없음), 콜 줄은 지금 `replaceTransportDetails`가 만드는 칸 그대로.
4. 성공 때만 Store 반영, 실패면 지금 문구, 세션이 바뀌면 지금 문구 — 그대로.
5. 빈 날 삭제(`removeDayLogOnServer`)는 그대로(116).
6. 새 함수는 sot 4-20대로: `security invoker`(운행기록·콜상세 권한 그대로), `search_path = public`, `anon` 실행권 없음. 데이터는 한 줄도 안 바뀐다.

### 건드릴 파일
- 새 `react-app/supabase/migrations/0028_save_day_log.sql` — 아래 완성본(멱등). 끝에 사후검증 주석.
  ```sql
  -- 0028: 그날 하루 기록 줄 저장과 콜 상세 바꾸기를 함수 한 번(= 한 트랜잭션)으로 — 콜 바꾸기가 실패하면 하루 기록 줄도 저장 전으로 돌아간다.
  -- security invoker: 운행기록·콜상세 권한과 작성자 칸 트리거(0027) 그대로. 표 변경 없음, create or replace라 다시 실행해도 안전.
  begin;

  create or replace function public.save_day_log(p_log jsonb, p_rows jsonb)
  returns uuid
  language plpgsql
  security invoker
  set search_path = public
  as $$
  declare
    r public.daily_logs;
    v_id uuid;
  begin
    if jsonb_typeof(p_log) is distinct from 'object' then
      raise exception 'p_log는 객체여야 합니다.' using errcode = 'P0001';
    end if;
    r := jsonb_populate_record(null::public.daily_logs, p_log);

    insert into public.daily_logs (user_id, vehicle_id, work_date, is_off, fixed_count, pallet_count, raw)
    values (r.user_id, r.vehicle_id, r.work_date, r.is_off, r.fixed_count, r.pallet_count, r.raw)
    on conflict (vehicle_id, work_date) do update
      set user_id = excluded.user_id, is_off = excluded.is_off, fixed_count = excluded.fixed_count,
          pallet_count = excluded.pallet_count, raw = excluded.raw
    returning id into v_id;

    perform public.replace_transport_details(v_id, p_rows);
    return v_id;
  end;
  $$;

  revoke all on function public.save_day_log(jsonb, jsonb) from public, anon;
  grant execute on function public.save_day_log(jsonb, jsonb) to authenticated;

  commit;
  ```
- `react-app/src/data/sync/syncWorkData.ts` — `upsertDailyLog`(서버 요청)를 보낼 값만 만드는 `dailyLogRow(userId, vehicleId, workDate, record)`로 바꾼다(칸 계산은 글자 그대로). 1줄 주석 → `// 일지 한 줄(daily_logs)에 보낼 값 — 로그인 일지 저장이 쓴다.`
- `react-app/src/data/sync/dayLogCloudCommit.ts` — `replaceTransportDetails`를 콜 줄만 만드는 `transportDetailRows(...)`로(칸 그대로), `writeDayKeyToServer`의 두 요청을 `supabase.rpc('save_day_log', { p_log, p_rows })` 한 번 → `if (error) throw error` → `assertSessionStillCurrent(captured)`로. 41줄 주석은 지우고 rpc 위에 `// 하루 기록 줄과 콜 상세를 서버 함수 한 번으로 — 하나가 실패하면 둘 다 저장 전 그대로.`
- 테스트(가짜 서버를 `daily_logs` upsert·`replace_transport_details` → `rpc.save_day_log`로 바꾸는 것뿐, 확인 내용은 그대로): `data/sync/dayLogCloudCommit.test.js`(17곳)·`data/sync/receivablesCloudCommit.test.js`(16곳)·`app/App.test.js`(26곳)·`data/sync/assignedVehicleDay.test.js`(3곳)·`features/day-log/hooks/useDayDraft.cloudSerial.test.js`(3곳). "upsert 1회" → "save_day_log 1회", 보낸 칸 확인은 `p_log`·`p_rows`에서. `memberRestore.test.js`는 해당 0곳(grep).
- 새 테스트(`dayLogCloudCommit.test.js`): ⓐ 콜 2개·고정 2회 저장 → 서버 요청은 `save_day_log` 1회뿐, `daily_logs` upsert 0회·`replace_transport_details` 0회, `p_log`에 `callDetails` 없음·`p_rows` 순서대로 ⓑ `save_day_log`가 오류를 돌려주면 다른 쓰기 0회·Store 그대로·실패 문구.
- drivelog-docs `docs/sot.md` 4-20 끝에 한 줄([x] 때): 로그인 일지 저장은 하루 기록 줄과 콜 상세를 `save_day_log` 한 번으로(둘 다 되거나 둘 다 안 됨).

### 안 건드릴 것
- `0023` `replace_transport_details` — 새 함수가 안에서 그대로 부르고, 옛 앱(새로고침 전 열린 화면)도 계속 씀. 지우지 않는다.
- `dailyLogRemoval.ts`(116)·지출 저장 `syncExpenseItems.ts`(118)·`commitMainDayLogMapToCloud`의 계정 바뀜 반영(117) — 각자 슬라이스.
- 서버 권한 정책·`0027` 트리거 — 바꾸지 않음(위 현재 상태 근거).

### 검증
- CI(test·typecheck·build·lint) 초록.
- 고치기 전 실패 확인: `dayLogCloudCommit.ts`·`syncWorkData.ts`만 마지막 커밋으로 되돌려 새 테스트 ⓐⓑ가 실패하는지(revert-check), 원문 기록을 지시서에.
- DB(보리 승인 뒤 AI가 SQL Editor에서, 지난 0023·0027처럼): 실행 → 사후검증 ① 함수 1개·`security_definer = false`·`proconfig = {search_path=public}`·`anon_exec = false`·`auth_exec = true` ② 되돌림 시험 — 테스터 계정으로 한 트랜잭션 안에서 (가) 정상 `p_rows`면 하루 기록 줄·콜 둘 다 바뀜 (나) `p_rows`를 배열 아닌 값으로 주면 오류이고 그 날짜 하루 기록 줄이 저장 전 그대로, 끝에 `rollback`(데이터 안 남김).
- **순서: DB `0028` 실행·사후검증 → 앱 push.** 앱을 먼저 내보내면 함수가 없어 모든 로그인 일지 저장이 실패한다. 옛 함수는 남으니 DB를 먼저 바꿔도 지금 앱은 그대로 돈다.
- 되돌리기: `drop function public.save_day_log(jsonb, jsonb);` + 앱 커밋 되돌리기(앱을 먼저 되돌린 뒤 함수 삭제).

### 파일 길이
- `dayLogCloudCommit.ts` 164 → 약 160줄. `syncWorkData.ts` 21 → 약 20줄. 새 `0028_save_day_log.sql` 약 45줄. 테스트 파일은 예외(AGENTS §6). 200줄 넘는 파일 없음.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치를 만들지 않는다. 실패는 지금처럼 문구 후 중단(sot §5 Fail-Fast).
- DB 실행·사후검증·되돌림 시험 중 하나라도 기대와 다르면 앱을 push하지 않고 함수를 지운 뒤 지시서를 고쳐 다시 승인받는다.

### 사용자 확인 방법
1. 배포 뒤 테스터 계정으로 로그인 → 일지에서 아무 날짜를 연다.
2. 고정 노선 횟수를 바꾸고 콜을 하나 추가해 저장 → 새로고침 → 둘 다 보이면 성공.
3. 같은 날 추가한 콜을 지우고 횟수를 되돌려 저장 → 새로고침 → 원래대로면 성공.
4. 미수금 화면에서 서로 다른 날짜의 콜 두 건 이상을 한꺼번에 입금 처리 → 새로고침 → 모두 입금 완료면 성공(여러 날짜 저장 길).
5. "끊겼을 때 둘 다 그대로"는 화면에서 만들기 어려워 위 DB 되돌림 시험 (나)로 대신한다.

### 진행 기록
- 보리 "너가 진행해, 착수지시서 확정, 작업 진행해"(2026-10-26) — 이번엔 데스크톱 AI가 코드까지.
- react-app `3505ff6`(push 전): `0028_save_day_log.sql` 새로, `dayLogCloudCommit.ts`(165줄)·`syncWorkData.ts`(17줄), 테스트 5개 파일 가짜 서버를 `save_day_log`로(확인 내용 그대로 — 69-1 "서버 함수 1회"는 `replace_transport_details` 1회 → `save_day_log` 1회, 미수금 H-1 "replace_transport_details 1회"는 "따로 0회"로), 새 테스트 114-1·114-2.
- 로컬 검사: typecheck 통과, lint 경고 0, `npm test` 914 + 898 전부 통과.
- 고치기 전 실패 확인(`dayLogCloudCommit.ts`·`syncWorkData.ts`만 HEAD로): 종료 코드 1, 새 테스트 2개 모두 실패 → 원래대로 복구(내용 일치 확인).
  ```
  ✖ 114-1: 콜 2건·고정 2회 — 서버 요청은 save_day_log 1회뿐, 표에 따로 쓰지 않음
    AssertionError [ERR_ASSERTION]: Expected values to be strictly equal: 0 !== 1   (dayLogCloudCommit.test.js:252, save_day_log 횟수)
  ✖ 114-2: save_day_log가 오류면 다른 쓰기 0회, Store 저장 전 값, 실패 문구
    + ok: true, toast: null  - ok: false, toast: '저장에 실패했습니다. 네트워크 상태를 확인해 주세요.'   (dayLogCloudCommit.test.js:270)
  ℹ tests 16  ℹ pass 7  ℹ fail 9  ℹ duration_ms 1655.7
  ```
  (함께 실패한 69-1~3·서브 차량·throw·error·성공 4개는 가짜 서버가 새 함수로 바뀐 탓 — 옛 코드는 `save_day_log`를 안 부름.)
- DB(데스크톱 AI, 로그인된 SQL Editor): 착수 전 읽기 — `daily_logs` 8칸(id uuid·fixed_count/pallet_count numeric·raw jsonb), `UNIQUE (vehicle_id, work_date)`, `save_day_log` 없음. `0028` 실행 "Success". 사후검증 ① `save_day_log | false | ["search_path=public"] | false | true` — 기대와 같음. ② 되돌림 시험 "시험 결과: (가) 정상: 고정 77, 새 콜 1건 / (나) 콜 실패: 막힘 — p_rows는 배열이어야 합니다., 고정 77, 새 콜 1건" — 실패한 호출의 고정 88이 남지 않음(둘 다 저장 전 그대로), 전부 rollback, 뒤에 시험 값 남음 0·0 확인.
- 앱 저장 시험(로그인된 localhost:5174, 새 코드 확인): 11가1111 10월 10일 고정 5 → 6 저장 → 서버 6·콜 1건(200,000) 그대로·작성자 차주 → 5로 되돌림 → 서버 5·콜 1건. "저장 실패"·콘솔 오류 없음.
- 보리 push 뒤 CI verify·deploy 초록(run 40287863681).
- 배포본 확인(보리 "배포본 확인도 네가 해", 보리가 앱 브라우저 창에서 로그인, getdrivelog.com 앱 파일 `index-XZhAaaUL.js`에 `save_day_log` 있음):
  ① 메인 10월 9일 고정 5 → 6 + 콜 "114시험상차→114시험하차" 11,400원 저장("세부 입력을 저장했습니다.") → 새로고침: 6회·콜 있음·합계 311,400원, 서버 고정 6·콜 1건(11400).
  ② 같은 날 5회로 되돌리고 시험 콜 삭제 → 새로고침: 5회·콜 없음·250,000원, 서버 고정 5·콜 0건(시험 전과 같음).
  ③ 미수금 화면 "2222 테스트 2026년 10월분 미수 2건 500,000원" [입금 완료] → "테스트 10월분 미수금을 수금 완료 처리했습니다.", 새로고침 뒤 카드 없음, 서버 10월 7일·12일 두 콜 수금 완료(입금 기록 각 1건) → 일지에서 한 건씩 [수금] 눌러 되돌림("수금을 취소했습니다.") → 서버 두 콜 미수·입금 기록 없음(시험 전과 같음).
  - 저장 실패 문구·저장 실패 콘솔 오류 없음. 콘솔 404는 주소를 직접 열 때 GitHub Pages가 내는 원래 응답(앱 파일은 200).
  - 따로 본 것: 10월 12일 같은 콜 번호의 "미수" 줄이 하나 더 있으나 테스터 계정이 아닌 다른 계정 차량 22가2222의 줄(시험 전부터, 안 건드림).
- 보리 최종 승인(2026-10-26) → `[x]`, sot 4-20 한 줄, 로드맵 114 `[x]`. 다음 117.

---

## 로드맵 94 `.ts` 전환 — 남은 조각 (94-A·B·C `[x]`, D~G는 출시 전 순서 뒤)

지난 지시서·진행 기록: 94-A drivelog-docs `3ee010c`, 94-B `90ef647`, 94-C `0c0eafe`(`git show <해시> -- docs/report.md`).

| 조각 | 범위 | 운영 파일 | 비고 |
|---|---|---|---|
| ~~94-A~~ `[x]` | 설정 + 시범 2개 | 2 | react-app `a009f2b` |
| ~~94-B~~ `[x]` | `domain/` | 58 | react-app `855fb6a` |
| ~~94-C~~ `[x]` | `data/` | 81 | react-app `079aee8` |
| 94-D | `shared/` + `app/` + `main.jsx` + `testSupport/` | 69 | |
| 94-E | `features/` 일지·달력·지출 | 39 | |
| 94-F | `features/` 서류·미수금·매출 | 38 | |
| 94-G | `features/` 나머지 8개 | 64 | 마무리: `allowJs` 끄기, `// @ts-check` 규칙 정리 |

- 순서 `[확인: 2026-10-22 보리 — "그럼 반만, 응"]`: D~G는 출시 전 순서(23·24 → 34 → 79) 뒤.
- 방식(B·C와 같음): AI가 손으로, 밖 파일은 경로 끝만(기계 비교), `mock.module` 지정 포함. 증명: 타입 설명 파일(`.d.ts`, 저장소 밖에서 뽑음) 비교 + 빌드 결과. 옛 JSDoc 형 변환 주석이 빠지면 압축 변수 이름이 바뀌어 해시가 달라질 수 있음(94-C) — 그때는 저장소 밖에서 옛·새를 압축 없이 빌드해 같은지로 증명(보리 승인 방식).
- 의존 검사 연결 수는 JSDoc `import('…')`이 `import type`이 되며 타입 전용 연결만큼 늘어남 — 늘어난 것이 그것뿐인지 확인.
- 94-D: `// @ts-check` 없는 4개(`app/guestSessionPersist.js`·`app/routes/ComingSoonRoute.jsx`·`app/routes/lazyPages.js`·`app/routes/sessionGate.js`)는 처음 타입 검사를 받음. `.jsx`는 `.tsx`로 — 화면 테스트 로더가 이미 읽음(94-A).
