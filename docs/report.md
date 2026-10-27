# docs/report.md — 현재 슬라이스 착수지시서

115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 116 빈 날 삭제 때 콜 상세만 먼저 지워지고 하루 기록 줄이 남을 수 있음 `[x]`

외부 코드 리뷰(2026-10-26), 로드맵 116 `[확인: 2026-10-26 보리 — "세개 로드맵에 올려놔", 순서 "알아서"]`. 앞 슬라이스 115 `[x]`(react-app `c64f4bf`·DB `0029`). **DB 변경 있음(새 함수 `0030`).** 114(저장)와 같은 "하루 저장" 경로의 지우기 쪽.

### 쉬운 요약
- 일지에서 그날을 다 비우면, 앱이 서버에 세 번 요청합니다: ① 그날 콜 상세 지우기 ② 그날 지출이 있는지 보기 ③ 하루 기록 줄을 빈 기록으로 남기거나 지우기. 중간에 끊기면 콜만 지워지고 휴무·고정 노선·파렛트 횟수가 남은 "반쯤 지워진 날"이 될 수 있습니다.
- 세 단계를 서버 함수 하나로 묶어 다 되거나 하나도 안 되게 합니다(114·115와 같은 방식).
- 원래 지우려던 날이라 원치 않는 유실은 아니고, "반쯤 지워진 날"만 막습니다(로드맵 표시 "낮음").

### 현재 상태 (코드·서버 확인, 2026-10-27)
- `data/sync/dailyLogRemoval.ts:16-28` `removeDayLogOnServer`: `transport_details` delete → 지출 3표 `select`(`hasExpenseRows`) → 있으면 `daily_logs` update(`is_off=false, fixed_count=0, pallet_count=0, raw={}`), 없으면 `daily_logs` delete. 부르는 곳은 `dayLogCloudCommit.ts:73` 한 곳(그 날짜가 비었을 때).
- 서버(읽기만): 지출 3표·콜 상세의 `daily_log_id`가 `daily_logs`에 `ON DELETE CASCADE` — 그래서 지출이 있으면 줄을 지우지 않음(0-3-A 규칙). 다섯 표 모두 `vehicle_id` uuid·`work_date` date. 트리거는 작성자 칸 막기(`guard_log_writer_change`, `UPDATE OF user_id`)뿐 — 이번 update는 `user_id`를 안 건드림. 삭제·수정 정책은 `daily_logs`·`transport_details` 모두 "본인 차량 또는 연동 기사 기간 안 날짜". `remove_day_log` 함수 없음.
- 테스트: `dailyLogRemoval.test.js` 5개(지우기·지출 3표별 빈 기록·지출 조회 실패), `dayLogCloudCommit.test.js` 2개(`:133-145` 빈 날 삭제, `:147-159` 지출 있으면 빈 기록)가 앱 쪽 세 요청을 기대값으로 둠.

### 목표 상태 (기대 동작)
1. 로그인 일지에서 그날이 비면 서버 요청은 새 함수 `remove_day_log` 한 번 — 콜 상세 지우기와 하루 기록 줄 비우기/지우기가 다 되거나 하나도 안 된다.
2. 규칙은 지금과 같다: 그날 지출이 있으면 줄을 빈 기록으로 남기고(지출을 지키려고), 없으면 줄을 지운다.
3. 실패하면 지금처럼 화면은 옛 값·실패 알림, Store 반영 없음. 성공하면 Store에서 그 날짜를 뺀다(지금과 같음).
4. 옛 앱은 함수를 안 부르므로 DB를 먼저 바꿔도 지금 배포본이 그대로 돈다.

### 배포 순서 (플레이북 §9)
1. 착수 전 읽기 진단(위 "서버" 줄을 다시 한 번 — 함수 없음·칸·트리거 그대로인지).
2. SQL Editor에서 `0030` 실행 → 사후검증(파일 끝 주석) → 되돌림 시험(아래).
3. 그다음 앱 push → CI 초록 → 배포본 확인. 앱을 먼저 올리면 함수가 없어 **그날을 비우는 저장이 모두 실패**한다(다른 저장은 그대로).
4. ①~③ 중 하나라도 기대와 다르면 push하지 않고, 함수를 지운 뒤 지시서를 고쳐 다시 승인받는다.

### 건드릴 파일
- 새 `react-app/supabase/migrations/0030_remove_day_log.sql` — 아래 완성본.
- `react-app/src/data/sync/dailyLogRemoval.ts` — `removeDayLogOnServer`가 `remove_day_log` 한 번 부르기로(지출 조회 함수·표 목록 삭제). 머리 주석·함수 주석 최종 문구:
  - 머리: `// 빈 날이 된 그 날짜 지우기 — 서버 함수 remove_day_log(DB 0030) 한 번. 그날 지출이 있으면 서버가 줄을 빈 기록으로 남긴다.`
  - 함수: `/** 빈 날이 된 그 날짜의 콜 상세와 하루 기록 줄을 한 번에 지운다(지출이 있으면 줄은 빈 기록으로, 멱등). */`
- `react-app/src/data/sync/dailyLogRemoval.test.js` — 다시 씀: ⓐ 서버 함수 1회·넘긴 값 `{ p_vehicle_id: 700, p_work_date: '2026-10-10' }`·표에 직접 쓰기 0회(`transport_details` delete·`daily_logs` delete/update·지출 select 모두 0) ⓑ 서버 함수가 오류를 돌려주면 던짐. 빈 기록/지우기 갈림은 앱이 아니라 서버가 하므로 되돌림 시험 (가)·(나)가 맡음.
- `react-app/src/data/sync/dayLogCloudCommit.test.js` — `:133-145`는 "서버 함수 `remove_day_log` 1회·`daily_logs` delete 0회·`save_day_log` 0회·Store에서 빠짐", `:147-159`는 이름을 `'0-3-A 빈 날 저장: 서버가 빈 기록으로 남겨도(blanked) Store에선 빠진다'`로 바꾸고 가짜 서버 함수가 `'blanked'`를 돌려주게, `daily_logs` update·delete 0회.
- 공용 스타일·공용 값 변경 없음.

`0030` 완성본:
```sql
-- 0030: 빈 날이 된 그 날짜 지우기를 함수 한 번(= 한 트랜잭션)으로 — 콜 상세 지우기·지출 확인·하루 기록 줄 비우기/지우기가 다 되거나 하나도 안 됨.
-- 그날 지출이 있으면 줄을 지우지 않고 빈 기록으로 남긴다(지출 행이 daily_logs에 ON DELETE CASCADE로 묶여 있어서 — 0-3-A 규칙 그대로).
-- security invoker: 운행기록·콜상세·지출 권한과 작성자 칸 트리거(0027) 그대로. 표 변경 없음, create or replace라 다시 실행해도 안전.
begin;

create or replace function public.remove_day_log(p_vehicle_id uuid, p_work_date date)
returns text
language plpgsql
security invoker
set search_path = public
as $$
begin
  delete from public.transport_details where vehicle_id = p_vehicle_id and work_date = p_work_date;
  if exists (select 1 from public.fuel_records where vehicle_id = p_vehicle_id and work_date = p_work_date)
     or exists (select 1 from public.maintenance_records where vehicle_id = p_vehicle_id and work_date = p_work_date)
     or exists (select 1 from public.misc_expense_records where vehicle_id = p_vehicle_id and work_date = p_work_date) then
    update public.daily_logs
    set is_off = false, fixed_count = 0, pallet_count = 0, raw = '{}'::jsonb
    where vehicle_id = p_vehicle_id and work_date = p_work_date;
    return 'blanked';
  end if;
  delete from public.daily_logs where vehicle_id = p_vehicle_id and work_date = p_work_date;
  return 'deleted';
end;
$$;

revoke all on function public.remove_day_log(uuid, date) from public, anon;
grant execute on function public.remove_day_log(uuid, date) to authenticated;

commit;

-- 사후검증(기대: 1줄, security_definer = false, proconfig = {search_path=public}, anon_exec = false, auth_exec = true)
-- select p.proname, p.prosecdef as security_definer, p.proconfig,
--        has_function_privilege('anon', p.oid, 'execute') as anon_exec,
--        has_function_privilege('authenticated', p.oid, 'execute') as auth_exec
-- from pg_proc p where p.proname = 'remove_day_log';

-- 되돌리기(앱을 먼저 되돌린 뒤): drop function if exists public.remove_day_log(uuid, date);
```

되돌림 시험(테스터 계정, 한 트랜잭션 안에서 시험 줄을 만들고 끝은 `rollback` — 실제 데이터 그대로):
- (가) 지출 없는 시험 날짜에 하루 기록 줄 + 콜 상세 1줄 → 함수 → `'deleted'`, 두 표 모두 0줄.
- (나) 지출(주유 1줄)이 있는 시험 날짜에 하루 기록 줄(고정 2) + 콜 상세 1줄 → 함수 → `'blanked'`, 콜 상세 0줄, 하루 기록 줄 남고 `fixed_count = 0`·`raw = {}`, 주유 1줄 그대로.
- (다) 함수 안에서 오류가 나면 앞서 지운 콜 상세도 함께 취소되는지 — 하루 기록 줄을 다른 사람 차량 id로 부르면 정책상 0줄 처리라 오류가 아님을 확인하고(지금 앱과 같음), 형이 틀린 날짜(`'abc'`)는 부르기 전에 오류로 끝나 아무것도 안 바뀜을 확인.

### 안 건드릴 것
- 하루 저장 `save_day_log`(0028)·콜 교체 `replace_transport_details`(0023) — 그대로.
- 지출 저장(118) — 다음 슬라이스.
- 비회원·기기 저장 경로(서버 안 씀) — 그대로.
- 화면·알림 문구 — 그대로(실패면 지금 실패 문구).

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(§5 세션·§9 배포 순서).
- 바꾼 테스트가 고치기 전 코드에서 실패하는지 먼저 확인(옛 코드는 표에 직접 세 번 써서 실패해야 함), 고친 뒤 통과. 로컬 `npm test` 전체 통과.
- 테스트·타입·빌드·코드 검사는 CI가 봄. 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `dailyLogRemoval.ts` 28 → 약 12줄, `0030` 새 파일 약 40줄, 테스트 두 파일은 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음 — 세 요청을 한 요청으로 줄임). 실패하면 수정 착수지시서를 다시 써서 승인받음.

### 사용자 확인 방법
1. 로그인 테스터 계정에서 비어 있는 날짜(예: 10-19)에 [2회] → 저장 → 운행 횟수 0으로 비움 → 저장 → 새로고침해도 빈 칸.
2. 지출이 있는 날을 비우는 경우는 되돌림 시험 (나)로 확인(실제 지출을 건드리지 않게).

### 누가 하나
- 데스크톱 AI가 SQL·앱 코드까지 `[확인: 2026-10-27 보리 — "응 너가 진행해"]`.

### 진행 기록
- 2026-10-27 DB(데스크톱 AI, 프로젝트 `wphlnkfymvpnklgbuxrk`):
  - 읽기 진단: `remove_day_log` 없음, 다섯 표 `vehicle_id` uuid·`work_date` date, 필수 칸은 `daily_logs`(vehicle_id·work_date)·`transport_details`·`fuel_records`(daily_log_id·vehicle_id), 트리거·정책은 위 "현재 상태"와 같음.
  - `0030` 실행(위 완성본 그대로) — 오류 없음. 사후검증: `remove_day_log | false | {search_path=public} | anon_exec false | auth_exec true` — 기대와 같음.
  - 되돌림 시험(테스터 계정 `7ba50d27…` 메인 차량 `9a4465b3…`, 시험 날짜 2030-01-01·02, 한 트랜잭션 → `rollback`): (가) 지출 없는 날 → `'deleted'`, 하루 기록 0줄·콜 0줄. (나) 주유 1줄 있는 날(고정 2) → `'blanked'`, 콜 0줄, 하루 기록 남고 `fixed_count 0`·`raw {}`·`is_off false`, 주유 1줄 그대로. (다) 다른 사람 차량(`da63b172…`, 3줄) 날짜로 부름 → 권한상 0줄 처리(`'deleted'`, 지금 앱과 같음), 그 차량 3줄 그대로. 날짜 형식 오류는 부르기 전에 끝남(함수 하나 = 한 문장이라 안에서 오류가 나면 앞 지우기도 함께 취소). 끝난 뒤 2030 시험 날짜 0줄·다른 차량 3줄 — 시험 전과 같음.
- 앱(데스크톱 AI): 착수 전 플레이북 §5·§9 열람. 테스트를 먼저 바꿔 옛 코드에서 돌림 — `dailyLogRemoval.test.js` 2개·`dayLogCloudCommit.test.js` 빈 날 2개 모두 실패(옛 코드는 표에 직접 써서). 고친 뒤 로컬 `npm test` 전체 통과(913·899개 — 옛 `dailyLogRemoval` 5개를 2개로 바꿔 3개 줄어듦), `tsc --noEmit`·코드 검사·의존 방향 검사 통과. `dailyLogRemoval.ts` 28 → 8줄, `0030` 파일은 위 완성본과 줄마다 같음.
- 코드 커밋 react-app `1357315`, push는 보리 예외 지시로 데스크톱 AI `[확인: 2026-10-27 보리 — "예외로 푸시까지 진행해"]`(DB `0030` 먼저 끝난 뒤, `c64f4bf..1357315`). CI verify·deploy 초록(run 40475951795).
- 배포본 확인(2026-10-27, 로그인 테스터 계정 메인 차량): 빈 날 10-19에 [2회] → 서버 하루 기록 줄 `fixed_count 2` 확인 → 운행 횟수 0으로 비움 → 저장 표시 → 서버 하루 기록 0줄·콜 0줄(새 함수로 지워짐) — 시험 전과 같은 빈 날.
- §5 점검: ① 범위 — 바뀐 파일 `dailyLogRemoval.ts`·`dailyLogRemoval.test.js`·`dayLogCloudCommit.test.js`·새 `0030` 4개, 지시서와 같음 ② 새 저장 장치·저장 키 없음(새 서버 함수는 지시서 범위) ③ `any`·`unknown`·`@ts-ignore` 없음 ④ `dailyLogRemoval.ts` 8줄 ⑤ `dailyLogRemoval` 테스트 5 → 2는 지시서에 밝힌 이동(갈림은 서버 — DB 되돌림 시험 (가)·(나)가 맡음), 나머지 테스트 그대로 ⑥ 공용 클래스·상태값 변경 없음, 주석은 지시서 문구 그대로 ⑦ 목표 1·3은 새 테스트·배포본, 2는 되돌림 시험 (가)·(나), 4는 옛 앱이 함수를 안 부름(진단).

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
