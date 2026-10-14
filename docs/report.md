# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 69 그날 콜 상세 바꾸기를 서버에서 한 번에 `[x]`

코드 리뷰(2026-10-14)로 로드맵 69번 등재 `[확인: 2026-10-14 보리]`, 순서 ①(데이터 저장·유실). 68번 다음. 플레이북 트리거 해당(저장·동기화 경로) — `docs/testing-playbook.md` 열람함. DB 변경이 들어가 AGENTS §9 절차를 따릅니다.

### 쉬운 요약
- **무엇을:** 로그인 계정이 일지를 저장할 때, 그날 콜 상세를 "다 지우기 → 다시 넣기" 두 번에 나눠 하던 것을 서버에서 한 번에 처리하게 합니다.
- **왜:** 지금은 지운 직후, 다시 넣기 전에 인터넷이 끊기거나 앱이 닫히면 그날 콜이 서버에서 사라집니다. 화면엔 콜이 그대로 있어서 모르고 지나가다가, 다시 켜면 콜이 없습니다.
- **달라지는 것:** 서버가 지우기와 넣기를 한 묶음으로 처리합니다. 중간에 끊기면 둘 다 안 된 것으로 돌아가서, 옛 콜이 그대로 남습니다. 화면 모양은 바뀌지 않습니다.

### 현재 상태 (코드 확인)
- `lib/dayLogCloudCommit.js:49-72` `replaceTransportDetails` — `transport_details`를 `daily_log_id`로 지우고(`:50`), 따로 다시 넣습니다(`:54`). 서버 요청이 두 번이라, 그 사이에 끊기면 지운 것만 남습니다.
- 이 함수는 일지 저장 경로 두 곳이 같이 씁니다. 한 날짜 저장 `commitMainDayLogToCloud`(`:116`)와 여러 날짜 저장 `commitMainDayLogMapToCloud`(`:148`)입니다. 후자는 미수금 입금(64번)과 회원 불러오기(22번)가 씁니다.
- 실패하면 "저장에 실패했습니다" 안내가 뜨고 화면(Store)은 바뀌지 않습니다(Fail-Fast, `:134-137`). 그래서 화면엔 콜이 있는데 서버엔 없는 상태가 됩니다.
- 콜 상세 읽기·쓰기 권한은 서버 표 규칙(RLS)으로 정해져 있습니다(`supabase/migrations/0010_driver_access_by_link_period.sql:51-69`, 차주 본인·연동 기사 기간). 서버 함수는 이 규칙을 그대로 따르는 방식(SECURITY INVOKER)으로 만들어, 권한이 넓어지지 않게 합니다.
- `transport_details` 표 정의는 마이그레이션 파일에 없습니다(원본 앱에서 만든 표, `grep -l transport_details supabase/migrations/*.sql` 결과는 권한·외래키만). 그래서 칸 이름·타입은 진단 쿼리로 먼저 확인해야 합니다(§9 ①).

### 고치는 방향 — A로 결정(보리 2026-10-14, SQL Editor 진단·적용은 보리 지시로 AI 실행)
- **A. 서버 함수 하나로 묶기 (추천)** — 새 서버 함수 `replace_transport_details(일지 번호, 콜 목록)`가 지우기와 넣기를 한 번에 합니다. 끊기면 아무것도 안 바뀝니다. DB 변경이 필요하고, 배포 순서를 지켜야 합니다(아래 "검증").
- **B. 새 것 먼저 넣고 옛 것 지우기** — DB 변경은 없습니다. 하지만 넣은 뒤 지우기 전에 끊기면 그날 콜이 **두 배로** 남아 금액이 틀립니다(② 돈 문제). 그래서 추천하지 않습니다.

### 목표 상태 (기대 동작, A 기준)
1. 일지를 저장하면 그날 콜 상세는 서버 함수 한 번으로 바뀝니다. 지우기·넣기가 둘 다 되거나 둘 다 안 됩니다.
2. 서버 함수가 실패하면(오류 응답·끊김) 지금처럼 "저장에 실패했습니다" 안내가 뜨고, 화면은 저장 전 그대로입니다. 서버의 옛 콜도 그대로 남습니다.
3. 서버에 넣는 칸·값은 지금과 같습니다(`daily_log_id`·`user_id`·`vehicle_id`·`client_id`·`work_date`·`sequence`·`load_loc`·`unload_loc`·`fare_amount`·`vat_exempt`·`payment_status`·`payment_due_date`·`payments`·`commission_snapshot`·`raw`).
4. 권한은 지금과 같습니다. 차주는 자기 차량만, 연동 기사는 연동 기간 안 날짜만 바꿀 수 있습니다(함수가 표 규칙을 그대로 따름). 사후검증 쿼리로 확인합니다.
5. 콜이 0건이면 그날 콜을 지우기만 합니다(지금과 같음).

### 건드릴 파일
- `react-app/supabase/migrations/0023_replace_transport_details.sql` (새 파일) — 서버 함수. 진단 쿼리 회신을 받은 뒤 칸 타입을 확정해서 씁니다.
- `react-app/src/lib/dayLogCloudCommit.js` — `replaceTransportDetails`의 지우기·넣기 두 요청을 `supabase.rpc('replace_transport_details', …)` 한 번으로 바꿉니다. 칸 만드는 부분은 그대로입니다. 새 주석(최종 문구): `// 지우기·넣기를 서버 함수 한 번으로 — 중간에 끊겨도 그날 콜이 사라지지 않는다.`
- 테스트 — `lib/dayLogCloudCommit.test.js`에 새 테스트를 넣습니다(서버 함수 1회 호출·칸 값 그대로 / 함수 오류면 안내·화면 그대로 / 콜 0건). 지금 `transport_details` 지우기·넣기를 흉내 내는 기존 테스트는 같은 뜻의 서버 함수 흉내로 바꿉니다. 해당: `app/App.test.js`(실패 주입 2곳), `lib/receivablesCloudCommit.test.js:298`. 단언을 약하게 하지 않고 같은 내용을 확인합니다.

### 안 건드릴 것
- `lib/dailyLogRemoval.js`(빈 날 삭제) — 이번 범위가 아닙니다. 그날을 지우려는 동작이라 중간에 끊겨도 원하지 않은 기록이 사라지지는 않습니다. 다음 저장 때 다시 지웁니다(멱등, `:21` 설명).
- `lib/syncWorkData.js` `upsertDailyLog` — 하루 기록 줄 저장은 그대로입니다. 그 뒤 콜 바꾸기 전에 끊기면 서버 콜은 옛 것 그대로라 사라지지 않습니다.
- 읽기 경로(`lib/hydrate.js:134`, `hydrateEmployedDriver.js:152`) — 표 모양이 그대로라 영향이 없습니다.
- 지출 저장(70번)은 다음 슬라이스입니다.

### 검증
- **DB (§9):** ① 읽기 전용 진단 `SELECT`(표 칸·타입·권한 규칙)를 보리에게 먼저 드립니다. ② 회신받은 실제 칸으로 멱등 SQL(`create or replace function`, `BEGIN…COMMIT`)을 씁니다. ③ 붙여넣기만 하면 되는 완성본을 드립니다. ④ 사후검증 `SELECT`와 기대 출력을 드립니다.
- **배포 순서:** SQL을 먼저 적용하고 → 그다음 코드를 push합니다. 순서가 바뀌면 배포된 앱이 아직 없는 함수를 불러서 로그인 일지 저장이 전부 실패합니다.
- 로컬 `npm test`. 새 테스트는 고치기 전 코드에서 실패하는지 확인합니다. CI verify 초록.
- 브라우저: 시험 계정으로만 확인합니다(실제 계정 로그인 상태인지 먼저 확인). 콜 2건 저장 → 1건으로 고쳐 저장 → 새로고침 뒤 1건이면 정상입니다.

### 파일 길이
- `lib/dayLogCloudCommit.js` 199줄 → 약 196줄(요청 두 개를 하나로).
- `0023_…sql` 새 파일 약 40줄. 테스트 파일은 예외입니다.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 실패하면 지금처럼 안내만 하고 화면은 그대로입니다.
- 검증이 실패하면 수정 착수지시서를 써서 다시 승인받습니다. DB는 `create or replace`만 쓰고, 표·데이터는 안 건드립니다(파괴적 쿼리 없음).

### 사용자 확인 방법
1. Supabase SQL Editor에서 AI가 드리는 진단 쿼리 → 결과 회신 → 완성 SQL 붙여넣기 → 사후검증 결과 확인.
2. 그다음 push → CI 초록.
3. https://getdrivelog.com 시험 계정 → 어느 날 콜 2건 저장 → 그중 1건 지우고 저장 → 새로고침 → 1건만 보이면 성공입니다.

---

## 진행 기록 (69)

- **DB 0023** (2026-10-14, SQL Editor, 보리 지시로 AI 실행)
  - 진단: `transport_details`의 번호 칸은 전부 uuid, RLS 켜짐(조회·작성·수정·삭제 규칙 = 차주 본인 차량 또는 `driver_can_access_vehicle_date`), 트리거 없음, 같은 이름 함수 없음, `daily_logs.id` uuid.
  - 적용: Supabase가 "destructive" 경고(함수 안 `delete` 글자) → 실행. 결과 `Success`.
  - 사후검증: `definer=false config=search_path=public anon=false auth=true args=p_daily_log_id uuid, p_rows jsonb` — 기대와 같음.
  - 되돌림 시험(`do` 블록 끝에 일부러 오류): 옛 2건 → 새 2건, 합계 12,346(옛 콜 안 남음), 빈 칸 기본값(`vat_exempt` false·`미수`·`[]`). 실행 뒤 그날 콜 그대로 2건·시험 행 0건 확인.
- 코드 커밋 react-app `d3a0fbb`(push 안 함). 건드린 파일은 지시서와 같음: `0023_…sql`(새, 43줄), `dayLogCloudCommit.js`(199→196줄), 테스트 3개 파일.
- 새 테스트 69-1~3: 수정 전 코드로 돌리면 3개 모두 실패, 고친 뒤 통과. 기존 테스트 3곳은 같은 뜻으로 흉내 위치만 옮김(App.test 실패 주입 2곳 → `handlers.rpc.replace_transport_details`, receivables H-1 `countOf('transport_details','insert')` → `countOf('rpc','replace_transport_details')`).
- 로컬 `npm test` 901+400 통과, `tsc` 0. 새 `any`/`unknown`/`@ts-ignore` 없음.
- 브라우저(앱 화면): 아직 안 함. 로그인 계정이 필요해서 배포 뒤 보리 시험 계정으로(사용자 확인 방법 3).
- 남은 것: 보리 push(DB는 이미 적용 — 순서 지켜짐) → CI verify 초록 → 배포본 화면 확인 → §5 리뷰 → 최종 `[x]`.
