# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 37-B — 서버에서도 연동 중인 차량 삭제 거절 + 앱 삭제 순서 바꾸기 `[x]`

> **진행(2026-10-13):** 착수 승인, SQL은 보리 지시로 AI가 실행.
> - 서버: 0020 실행 성공. 사후검증 1 — `vehicles_guard_linked_delete` 1줄 확인. 사후검증 2 — 연동 차량 삭제 시 위 문구 `P0001` 오류 확인(지시서의 `begin…rollback` 대신, 끝에서 항상 예외를 내는 `do` 블록으로 바꿔 실행 — 트리거가 안 걸려도 데이터가 절대 안 바뀌게. 파일 주석에 기록).
> - 앱: `directMutations.js`(191→178)·`vehicleDeletion.js`(87→90)·테스트 2파일 수정, `typecheck` 통과.
> - **`npm test` 2개 실패 → 아래 수정 착수지시서.**

> - 37-B-1 승인 → syncSpy 2개 기대값 수정, `npm test` 838+370 전부 통과. 코드 커밋 react-app `3cfd99a`(0020 파일 포함, push 전). 보리 push → CI 초록(verify·deploy).
> - 배포본 확인(AI, 보리 지시, 차주 계정): ① 연동 차량 22가2222 삭제 → 안내 뜨고 그대로. ② 시험 차량 99하9999(운행일지 방식) 등록 → 10/13 일지 3회 저장(서버 `daily_logs` 1줄 확인) → 삭제 → 서버 차량 0·일지 0(CASCADE 정상). 연동 기록 5건·연동 차량 일지 3줄 그대로. §5 리뷰 → 보리 최종 승인(2026-10-13).

### 수정 착수지시서 (37-B-1) `[x]` 승인·반영
- **무엇이 실패했나:** `src/lib/vehicleMutations.syncSpy.test.js`의 2개.
  - `:124` "Supabase 차량 삭제 성공은 … delete 6회다" → 이제 1회.
  - `:144` "원격 삭제 throw는 … 자식 delete만 나간다"(delete 4회 기대) → 이제 vehicles 1회만 나가고 실패.
- **왜:** 둘 다 예전 "딸린 표 5개 먼저 지우기" 순서의 호출 수를 세던 테스트다. 이번 슬라이스가 그 순서를 없앴다. 지시서 "건드릴 파일"에 이 파일을 빠뜨렸다(조사 때 `countOf(...)` 형태만 찾고 `stubSupabaseCallCounts.delete` 형태를 못 찾음).
- **고칠 것:** 이 파일의 두 테스트만 기대값을 바뀐 순서로 바꾼다. 6 → 1, 4 → 1. 두 번째 테스트 제목의 "자식 delete만 나간다"는 "vehicles delete 1회만 나간다"로 바꾼다. 나머지 단언(실패 표시·문구·Store 그대로·동기화 0회·다른 호출 0회)은 그대로 둔다. 약화 아님.
- **건드릴 파일 추가:** `src/lib/vehicleMutations.syncSpy.test.js`(테스트 파일). 운영 코드 추가 변경 없음.
- 확인: `npm test` 전체 통과 → 코드 커밋 1번(0020 파일 포함).

37(`77cd15d`)은 앱에서만 막았다. 앱 검사는 "이 기기의 기사 목록" 기준이라, 목록이 늦으면 못 막는다. 보리 결정(2026-10-13): 서버에서도 막고, 추천 방식(서버 규칙 + 앱 삭제 순서 바꾸기)으로.

### 쉬운 요약
- **무엇을:** 서버에 "기사가 연동된 차량은 삭제 거절" 규칙을 단다. 그리고 앱이 차량을 지울 때 **차량을 먼저** 지우게 바꾼다(딸린 기록은 서버가 같이 지운다).
- **왜:** 지금 앱은 일지·지출을 먼저 지우고 차량을 마지막에 지운다. 서버 규칙만 달면 차량은 거절돼도 일지·지출은 이미 지워진 상태가 된다.
- **달라지는 점:** 어느 기기·어느 경로에서든 연동 중인 차량은 지워지지 않는다. 거절되면 아무것도 지워지지 않는다.

### 현재 상태 (서버·코드 확인, 2026-10-13 읽기 전용 조회)
- `vehicles` 표 트리거 0개, 삭제 정책 `본인 차량 삭제: user_id = auth.uid()`(조건 없음).
- 차량을 가리키는 표: `daily_inspections`·`daily_logs`·`driver_links`·`fuel_records`·`maintenance_records`·`misc_expense_records`·`transport_details`는 전부 `ON DELETE CASCADE`, `tax_invoices`만 `SET NULL`. → **차량 한 줄만 지우면 딸린 기록은 서버가 다 지운다.**
- 앱 `deleteVehicleFromSupabase`(`directMutations.js:13-39`)는 `transport_details`·정비·주유·기타를 먼저 지우고(`:16-21`), `daily_logs`(`:25`), 마지막에 `vehicles`(`:33`) 순서. 위 CASCADE 때문에 앞의 5번 삭제는 결과가 같은 중복이다.
- 같은 함수를 즉시 삭제(`vehicleDeletion.js`)와 예전 대기열 재시도(`outboxFlush.js:71`)가 같이 쓴다.
- 앱의 차량 삭제 실패 안내는 항상 "저장에 실패했습니다. 네트워크 상태를 확인해 주세요."(`vehicleDeletion.js` catch)라 서버 거절 이유가 안 보인다.
- 비슷한 서버 거절 선례: 연동 중 탈퇴 거절 `0009_block_withdraw_while_linked.sql`(`raise exception … using errcode = 'P0001'`).

### 목표 상태 (기대 동작)
1. 서버: `vehicles` 줄을 지우려 할 때 그 차량에 `status = 'linked'` 연동 기록이 있으면 "기사가 연동된 차량은 삭제할 수 없습니다. 기사 관리에서 연동을 먼저 해제해 주세요."로 거절한다. 누가 어떤 경로로 지우든(앱·대기열 재시도·대시보드) 같다.
2. 서버: 연동 기록이 `pending`이거나 없으면 지금처럼 지워지고, 딸린 기록도 CASCADE로 같이 지워진다.
3. 앱: 차량 삭제는 `vehicles` 한 번만 부른다. 거절되면 딸린 기록도 하나도 안 지워진다.
4. 앱: 서버가 위 문구로 거절하면(오류 코드 `P0001`) 그 문구를 그대로 안내한다. 다른 실패는 지금 문구 그대로.
5. 37의 앱 검사(먼저 막기)는 그대로 둔다. 서버 규칙은 그 뒤의 마지막 안전망이다.
6. 0행 삭제(이미 없음) 처리, 로그인 바뀜 처리는 지금과 같다.

### 건드릴 파일
- **서버(보리가 SQL 실행, §9):** `react-app/supabase/migrations/0020_block_vehicle_delete_while_linked.sql` — 새 트리거 함수 `guard_vehicle_delete_while_linked()`(`security definer`, `set search_path = public`) + `BEFORE DELETE` 트리거 `vehicles_guard_linked_delete`. `create or replace`·`drop trigger if exists`로 다시 실행해도 안전, 데이터 변경 없음. 파일은 실행 기록용(0009와 같은 방식).
- `react-app/src/lib/directMutations.js` — `deleteVehicleFromSupabase`에서 딸린 표 5개 삭제 줄 제거, `vehicles` 삭제만 남김. 0행 검사·로그인 확인 유지.
- `react-app/src/lib/vehicleDeletion.js` — catch에서 `P0001`이면 서버 문구로 안내.
- 테스트:
  - `src/lib/outboxFlush.test.js` — **기존 2개를 고쳐 씀**(약화 아님, 없어지는 단계를 검사하던 것): ① "daily_logs만 실패 후 재시도" → "vehicles 삭제만 부르고 재시도해도 안전"으로, ② "자식 표 처리 중 로그아웃하면 다음 단계 0회" → "vehicles 삭제 중 로그아웃하면 op 보존"으로.
  - `src/lib/directMutationActions.test.js` — 추가: 차량 삭제 시 딸린 표 delete 0회·`vehicles` 1회 / 서버 `P0001` 거절이면 그 문구 안내·Store 그대로.

### 안 건드릴 것
- RLS 정책(`본인 차량 삭제`) — 그대로. 정책에 조건을 넣으면 거절 대신 "0행"이 돼서 "이미 삭제됐거나…"로 잘못 안내되므로 트리거 방식.
- 37의 앱 검사, `CarListPage.jsx`(230줄) — 안내는 기존 `result.toast`로 뜬다.
- 연동 기록·해제 절차(0011), 탈퇴 거절(0009) — 그대로. 탈퇴는 0009가 연동 중이면 먼저 막으므로 이 트리거와 안 겹친다. (참고: 대시보드에서 연동 중인 차주를 직접 지우면 이제 거절됨.)
- 세금계산서(`SET NULL`) — 그대로.
- 플레이북(삭제·`lib/*` 경로라 필수, §1·§2·§3): 거절 시 서버 호출 이후 실패 → Store·저장소 불변을 테스트로 확인.

### 검증
- 서버 사후검증(읽기 전용):
  - `select tgname from pg_trigger where tgrelid = 'public.vehicles'::regclass and not tgisinternal;` → `vehicles_guard_linked_delete` 1줄
  - 동작 확인(지우지 않음, 되돌림 포함): `begin; delete from public.vehicles where id = (select vehicle_id from public.driver_links where status = 'linked' limit 1); rollback;` → 위 문구 오류. 오류가 안 나도 `rollback`이라 데이터는 그대로.
- `npm test` — 위 테스트. 고쳐 쓴 2개는 바뀐 단계 기준으로 실제 호출 수·op 보존을 단언.
- 화면: 개발 서버(비회원)는 서버 삭제를 안 타서 확인 불가 → 앱 쪽은 테스트로, 서버 쪽은 위 SQL로.

### 파일 길이
- `directMutations.js` **191 → 약 181**(200 미만 유지)
- `vehicleDeletion.js` 87 → 약 91
- `0020_…sql` 새 파일 약 30줄(SQL 기록)
- 테스트 파일은 예외

넣을 주석(최종 문구):
- `directMutations.js`: `// 딸린 표는 전부 vehicles ON DELETE CASCADE라 차량 한 줄만 지운다 — 서버가 거절하면 아무것도 안 지워진다(로드맵 37-B).`
- `vehicleDeletion.js`: `// 서버가 연동 중 삭제를 거절하면(P0001, 0020) 그 문구를 그대로 보여 준다.`

### 실패 시 처리
- 새 저장 장치·재시도·임시 보관은 만들지 않는다(서버 거절 규칙 1개, 앱은 삭제 호출을 줄이고 문구만 연결).
- SQL 실행이 실패하거나 사후검증이 기대와 다르면 앱 코드는 손대지 않고 멈춘 뒤, 수정 착수지시서를 써서 다시 승인받는다.

### 진행 순서
1. AI가 0020 SQL 완성본을 드림 → 보리가 Supabase SQL Editor에 붙여 실행(또는 보리 지시로 AI 실행) → 사후검증 2개.
2. 통과하면 앱 코드·테스트 수정 → `npm test` → 코드 커밋 1번(0020 파일 포함).

### 사용자 확인 방법 (배포본, 차주 "테스트")
1. (37의 앱 검사 때문에 화면으로는 서버 거절까지 안 감) 서버 쪽은 위 사후검증 SQL 결과로 확인.
2. 연동 기사가 없는 서브 차량을 하나 만들어 지운다 → 지워지고, 그 차량 일지도 사라지면 성공(삭제 순서 바꾼 뒤에도 정상).
3. 테스트2가 연동된 차량 [삭제] → 37과 같은 안내, 차량 그대로.
