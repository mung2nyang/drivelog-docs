# docs/report.md — 현재 슬라이스 착수지시서

122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 115 기사 차량 번호 변경 때 번호만 서버에 바뀌고 나머지 칸·화면은 옛 값으로 남을 수 있음 `[x]`

외부 코드 리뷰(2026-10-26), 로드맵 115 `[확인: 2026-10-26 보리 — "세개 로드맵에 올려놔", 순서 "알아서"]`. 앞 슬라이스 122 `[x]`(배포 순서 규칙 — 이번이 그 규칙을 처음 쓰는 슬라이스). **DB 변경 있음(새 함수 `0029`).**

### 쉬운 요약
- 기사 차량 번호를 바꿔 저장하면 앱이 서버에 두 번 요청합니다: ① 번호·전용 거래처·차량 설정 이름 바꾸기 ② 톤수·기사 이름 같은 나머지 칸 저장. ②만 실패하면 서버는 새 번호, 화면은 옛 번호로 어긋납니다.
- 그 상태에서 전용 거래처를 저장하면 옛 번호가 서버에 다시 써져, 새로고침 뒤 그 거래처가 차량에서 빠질 수 있습니다.
- 두 요청을 서버 함수 하나로 묶어 둘 다 되거나 하나도 안 되게 합니다(114·69와 같은 방식, 보리 결정).

### 현재 상태 (코드 확인)
- `data/sync/vehicleMutations.ts:72-75` — 번호가 바뀌면 `renameSubVehicleOnServer`(`vehicleRename.ts:81-85`, 서버 함수 `rename_sub_vehicle` DB `0022`) 다음 `upsertVehicleFromList`(`syncVehiclesClients.ts:20-30`, `vehicles` 한 줄 update). 두 번째가 실패하면 첫 번째는 그대로 남음.
- 다시 저장하면 `0022`가 "이미 같은 번호"(`:31-34`)로 넘어가 복구됨 — 테스트 R-3b(`vehicleRename.test.js:118-138`)가 이 두 단계 동작을 기대값으로 둠.
- 두 번째 요청이 쓰는 칸 = `buildVehicleRow`(`data/cloud/cloudStorage.ts:13-33`): `legacy_log_id`·`number`·`type`·`tonnage`·`display_order`·`comm_enabled`·`comm_type`·`comm_value`·`driver_pay_mode`·`driver_salary_amount`·`driver_link_id`·`driver_name`·`archived`·`raw`(+ `user_id`).
- 번호를 안 바꾸는 저장·새 차량·메인 차량은 지금처럼 `upsertVehicleFromList` 한 번 — 이번 범위 밖.

### 목표 상태 (기대 동작)
1. 로그인 계정에서 기사 차량 번호를 바꿔 저장하면 서버 요청은 새 함수 `rename_and_save_sub_vehicle` 한 번뿐이다 — 번호·전용 거래처·설정 키·나머지 칸이 같이 바뀌거나 하나도 안 바뀐다.
2. 그 함수가 실패하면 화면·서버 모두 옛 값 그대로이고, 알림은 지금과 같다(서버가 정한 거절 문구는 그 문구, 그 밖은 "저장에 실패했습니다…").
3. 번호를 안 바꾸는 저장·비회원·새 차량은 지금과 같다(R-1·R-4 그대로).
4. 옛 함수 `rename_sub_vehicle`은 남겨 둔다 — DB를 먼저 바꿔도 지금 배포본이 그대로 돈다.

### 배포 순서 (플레이북 §9)
1. 착수 전 읽기 진단(아래 ①) → 결과로 함수 본문 칸·형 확정.
2. SQL Editor에서 `0029` 실행 → 사후검증(아래 ②)을 기대 출력과 대조 → 되돌림 시험(아래 ③).
3. 그다음 앱 push → CI 초록 → 배포본 확인. 앱을 먼저 올리면 새 함수가 없어 **번호를 바꾸는 저장이 모두 실패**한다(다른 저장은 그대로).
4. ①~③ 중 하나라도 기대와 다르면 push하지 않고, 함수를 지운 뒤 지시서를 고쳐 다시 승인받는다.

### 건드릴 파일
- 새 `react-app/supabase/migrations/0029_rename_and_save_sub_vehicle.sql` — 아래 완성본(진단 결과로 칸 이름만 확인).
- `react-app/src/data/sync/vehicleRename.ts` — `renameSubVehicleOnServer`가 새 함수를 부르고 나머지 칸 줄(`buildVehicleRow`)을 같이 보냄, 서버 차량 번호를 돌려줌. 인자 `(userId, vehicleSupabaseId, car, index, captured)`. 함수 주석 최종 문구: `/** 서버 차량 번호·전용 거래처·설정 키와 나머지 칸을 한 번에 저장한다(다 되거나 하나도 안 됨). */`. 파일 머리 주석 2줄째의 `rename_sub_vehicle(DB 0022)`를 `rename_and_save_sub_vehicle(DB 0029)`로.
- `react-app/src/data/sync/vehicleMutations.ts` — `:72-75`에서 번호를 바꿀 때는 `renameSubVehicleOnServer` 결과를 서버 번호로 쓰고 `upsertVehicleFromList`를 부르지 않음, 아니면 지금처럼.
- `react-app/src/data/sync/vehicleRename.test.js` — R-2·R-3·R-3b·R-5의 가짜 서버 함수 이름을 새 함수로, R-2는 "서버 함수 1회(넘긴 값에 `p_row`)·`vehicles` update 0회", R-3b는 이름을 `'R-3b 로그인: 함수 하나가 실패하면 차량 저장 0회·화면 옛 번호, 다시 저장하면 새 번호'`로 바꾸고 `vehicles` update 0회 확인 추가.
- 공용 스타일·공용 값 변경 없음.

`0029` 완성본:
```sql
-- 기사 차량 번호 바꾸기(0022)와 나머지 칸 저장을 한 번에 — 둘 다 되거나 하나도 안 됨(함수 하나 = 한 트랜잭션).
-- security invoker: 부르는 사람 권한 그대로(본인 차량 RLS). 표 변경 없음, create or replace라 다시 실행해도 안전. 0022는 옛 앱용으로 남김.
begin;

create or replace function public.rename_and_save_sub_vehicle(p_vehicle_id uuid, p_new_number text, p_row jsonb)
returns uuid
language plpgsql
security invoker
set search_path = public
as $$
declare
  r public.vehicles;
begin
  perform 1 from public.rename_sub_vehicle(p_vehicle_id, p_new_number);
  r := jsonb_populate_record(null::public.vehicles, p_row);
  update public.vehicles v
  set tonnage = r.tonnage,
      display_order = r.display_order,
      comm_enabled = r.comm_enabled,
      comm_type = r.comm_type,
      comm_value = r.comm_value,
      driver_pay_mode = r.driver_pay_mode,
      driver_salary_amount = r.driver_salary_amount,
      driver_link_id = r.driver_link_id,
      driver_name = r.driver_name,
      archived = r.archived,
      raw = r.raw
  where v.id = p_vehicle_id and v.user_id = auth.uid();
  if not found then
    raise exception '차량을 찾지 못했습니다.' using errcode = 'P0001';
  end if;
  return p_vehicle_id;
end;
$$;

revoke all on function public.rename_and_save_sub_vehicle(uuid, text, jsonb) from public, anon;
grant execute on function public.rename_and_save_sub_vehicle(uuid, text, jsonb) to authenticated;

commit;

-- 사후검증(기대: 1줄, security_definer = false, proconfig = {search_path=public}, anon_exec = false, auth_exec = true)
-- select p.proname, p.prosecdef as security_definer, p.proconfig,
--        has_function_privilege('anon', p.oid, 'execute') as anon_exec,
--        has_function_privilege('authenticated', p.oid, 'execute') as auth_exec
-- from pg_proc p where p.proname = 'rename_and_save_sub_vehicle';
```

SQL Editor에서 할 것(데스크톱 AI 또는 보리 — 지난 0023·0027·0028처럼):
- ① 읽기 진단: `select column_name, data_type from information_schema.columns where table_schema = 'public' and table_name = 'vehicles' order by ordinal_position;` — 위 11칸이 모두 있는지·형(`driver_link_id`가 uuid인지 등). `select tgname from pg_trigger where tgrelid = 'public.vehicles'::regclass and not tgisinternal;` — 차량 표 트리거(37-B 삭제 막기 등)가 update를 막지 않는지. `select proname from pg_proc where proname in ('rename_sub_vehicle', 'rename_and_save_sub_vehicle');` — `0022`만 있어야 함.
- ② 사후검증: 위 파일 끝 주석 `SELECT`.
- ③ 되돌림 시험: 테스터 계정으로 한 트랜잭션 안에서 테스트 기사 차량(`99가9998`)에 (가) 정상 줄이면 번호·톤수가 같이 바뀜 (나) 칸 형이 틀린 줄(`display_order`에 글자)이면 오류로 끝나고 번호도 안 바뀜 — 끝은 `rollback`(데이터 그대로).

### 안 건드릴 것
- `0022 rename_sub_vehicle` — 그대로(지우지 않음, 플레이북 §9 "옛 앱도 그대로").
- 번호를 안 바꾸는 차량 저장·새 차량 등록·사업자정보 저장(`requestCarBusinessInfoSave`) — 지금처럼 `upsertVehicleFromList`.
- 화면 쪽 묶인 것 옮기기(`planSubLogRename`·`planRenamedPlateEntries`, 지출·기사 연결·일지 키) — 서버가 성공한 뒤에만 반영하는 지금 방식 그대로.
- 세금계산서의 옛 차량 번호(로드맵 129, A6) — 다음 슬라이스.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(AGENTS §4 트리거 — 저장 경로), 특히 §5 세션·§9 배포 순서.
- 바꾼 R-2·R-3b가 고치기 전 코드에서 실패하는지 먼저 확인(옛 코드는 옛 함수 이름·`vehicles` update 1회라 실패해야 함), 고친 뒤 이 파일 전체 통과.
- 테스트·타입·빌드·코드 검사는 CI가 봄. 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `vehicleRename.ts` 85 → 약 90줄, `vehicleMutations.ts` 143 → 약 145줄(200 이하).
- `0029` 새 파일 약 45줄, `vehicleRename.test.js` 173 → 약 175줄(테스트 파일 예외).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음 — 두 요청을 한 요청으로 줄임). 실패하면 수정 착수지시서를 다시 써서 승인받음.

### 사용자 확인 방법
1. 로그인 테스터 계정 차량 관리에서 기사 차량 `99가9998`의 번호를 `99가9997`로 바꾸고 톤수도 하나 바꿔 [저장] → "차량을 수정했습니다." → 새로고침해도 새 번호·새 톤수, 그 차량 전용 거래처·앱 설정이 그대로 그 차량에 붙어 있음.
2. 번호·톤수를 원래대로 되돌려 [저장](시험한 것 되돌림).

### 정한 것
- 고치는 방법은 서버 함수 하나(DB `0029`) `[확인: 2026-10-27 보리 — "서버 함수 하나(DB 0029)로 가"]`.
- 고르지 않은 방법: 앱만 고치기(두 번째 저장이 실패하면 화면에 새 번호만 반영하고 따로 안내) — 실패가 두 갈래가 되고 번호가 바뀐 채 실패 알림이 떠 헷갈려서.
- DB 실행은 데스크톱 AI가 `[확인: 2026-10-27 보리 — "너가 진행해"]`. 앱 코드는 터미널 AI.

### 진행 기록
- 2026-10-27 DB(데스크톱 AI, Supabase 연결 도구로 같은 프로젝트 `wphlnkfymvpnklgbuxrk` — 앱 주소와 같음):
  - ① 읽기 진단: `vehicles` 20칸 — 함수가 쓰는 11칸 모두 있음(`display_order` integer, `comm_enabled`·`archived` boolean, `driver_link_id` text, `driver_salary_amount` numeric, `raw` jsonb, 나머지 text). 트리거는 `vehicles_guard_linked_delete`(BEFORE DELETE)뿐 — update 안 막음. 함수는 `rename_sub_vehicle(p_vehicle_id uuid, p_new_number text)`만, 새 함수 없음. 정책 수정 `user_id = auth.uid()`.
  - `0029` 본문(위 완성본 그대로) 실행 — 오류 없음.
  - ② 사후검증: `rename_and_save_sub_vehicle | false | {search_path=public} | anon_exec false | auth_exec true` — 기대와 같음.
  - ③ 되돌림 시험(테스터 계정 `7ba50d27…`, 기사 차량 `99가9998` id `5e993b14…`): (가) 한 트랜잭션 안에서 새 번호 `99가9997`·톤수 `7톤` 줄 → 돌려준 id 같음, `number`·`legacy_log_id`·`raw.number` 모두 `99가9997`, 톤수 `7톤`, 전용 거래처 1곳이 새 번호로(옛 번호 0곳) → `rollback`. (나) `display_order`에 `"abc"` → 오류 "invalid input syntax for type integer", 같은 호출 안의 번호 바꾸기도 함께 취소돼 번호 `99가9998` 그대로 → 전체 되돌림. 끝난 뒤 실제 줄: `99가9998`·`5톤`·전용 거래처 1곳 — 시험 전과 같음.
- 앱 코드도 데스크톱 AI가 `[확인: 2026-10-27 보리 — "너가 진행해"]`. 착수 전 플레이북 §5·§9 열람.
- 고치기 전 실패 확인: 테스트를 먼저 바꿔 옛 코드에서 돌림 — R-2·R-3·R-3b 실패(옛 함수 이름·`vehicles` update), 6개 중 3 통과(R-5는 가짜 서버가 모르는 함수도 성공으로 돌려줘 통과 — 로드맵 120의 약점).
- 고친 뒤: `vehicleRename.test.js` 6개 통과, 로컬 `npm test` 전체 통과(916·899개), `tsc --noEmit` 통과, 바뀐 파일 코드 검사·의존 방향 검사 통과. 줄 수 `vehicleRename.ts` 92·`vehicleMutations.ts` 142. `0029` 파일은 위 완성본과 줄마다 같음(45줄).
- 지시서 밖 작은 보완 1개: R-4(번호 안 바꿈)에 새 함수도 0회인지 확인 한 줄 추가(이름만 바뀐 함수를 놓치지 않게).
- 코드 커밋 react-app `c64f4bf`, push 뒤 CI verify·deploy 초록(run 40470767312).
- 배포본 확인(2026-10-27, 데스크톱 AI, 로그인 테스터 계정): 차량 관리에서 `99가9998`(운행일지 모드)을 `99가9997`·7톤으로 [저장] → "차량을 수정했습니다." → 서버 값 확인: `number`·`legacy_log_id`·`raw.number` `99가9997`, 톤수 `7톤`, 전용 거래처 1곳 새 번호(옛 번호 0곳), 차량 설정 키 새 번호(옛 키 없음) → 새로고침해도 `99가9997 (7톤)` → `99가9998`·5톤으로 되돌려 [저장] → 서버 값이 시험 전과 같음(번호·톤수·전용 거래처 1곳·설정 키).
- §5 점검: ① 범위 — 바뀐 파일 `vehicleRename.ts`·`vehicleMutations.ts`·`vehicleRename.test.js`·새 `0029` 4개, 지시서와 같음(R-4 한 줄 보완은 위에 적음) ② 새 저장 장치·저장 키 없음(새 서버 함수는 지시서·보리 승인 범위) ③ `any`·`unknown`·`@ts-ignore` 없음 ④ `vehicleRename.ts` 92·`vehicleMutations.ts` 142줄 ⑤ 바꾼 테스트는 지시서에 밝힌 동작 변경, R-1 그대로 ⑥ 공용 클래스·상태값 변경 없음, 함수 주석은 지시서 문구 그대로 ⑦ 목표 1은 R-2·배포본, 2는 R-3·R-3b·되돌림 시험 (나), 3은 R-1·R-4, 4는 진단(옛 함수 남음).

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
