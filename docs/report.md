# docs/report.md — 현재 슬라이스 착수지시서

120-B: drivelog-docs `503a3d3`. 120-A: drivelog-docs `81f927a`. 119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 120-C. 남은 3개 파일 엄격으로 + 오류 문구 확인 11곳 `[x]`

- 근거: 로드맵 120(③). 120-B(`b05cca6`)의 다음이자 120의 마지막 슬라이스다.
- 2026-11-01 보리가 "응"으로 지시서를 요청했다. 같은 날 확정했다 `[확인: 2026-11-01 보리 — "착수지시서 확정, 너가 작업 진행해"]`.
- 120-A에서 "예전처럼"(`allowUnregistered`) 표시를 단 13개 중 마지막 3개를 옮긴다.
- 120-B 지시서에서 C로 넘긴 "오류만 나면 통과" 10곳을 고친다(`[확인: 2026-11-01 보리 — "착수지시서 확정"]`). 다시 훑어보니 1곳이 더 있어 모두 11곳이다.

### 쉬운 요약
- 앱 화면 테스트 2개 파일과 지출 저장 테스트 1개 파일은 아직 미리 정해 두지 않은 서버 호출에도 "성공"을 받는다. 이번에 표시를 떼고, 실제로 쓰는 서버 응답만 정해 둔다.
- 이미 엄격한 6개 파일에 남은 "오류만 나면 통과" 시험 11곳은 "그 오류인지"까지 보게 한다.
- 끝나면 "예전처럼" 표시를 쓰는 테스트가 없어진다. 표시 자체를 시험하는 가짜 서버 시험 1곳만 남는다.
- 바꾸는 것은 테스트뿐이다. 앱 코드와 가짜 서버는 그대로 둔다.

### 현재 상태 (코드 확인)
- 엄격 실험: 임시 사본에서 3개 파일의 표시만 뗐다(원래 폴더는 안 바꿈). 시험 20개가 실패한다.

| 파일 | 실패 | 부르는데 등록 안 한 호출(테스트 전체 합) |
|---|---|---|
| `app/App.test.js` | 16개 중 9 | `rpc.is_admin`·`rpc.settle_expired_driver_unlinks`·`support_inquiries.select`(앱이 켜질 때마다), `fuel_records`·`maintenance_records`·`misc_expense_records`·`transport_details`·`daily_logs`.select, `maintenance_records`·`misc_expense_records`.insert, `profiles.upsert`, `rpc.save_day_log`·`rpc.remove_day_log` |
| `app/App.clientsCars.test.js` | 15개 중 8 | 앱이 켜질 때 부르는 위 3개, `clients.update`, `fuel_records`·`maintenance_records`·`misc_expense_records`·`transport_details`·`daily_logs`.select, `rpc.rename_and_save_sub_vehicle` |
| `features/expenses/ui/expenseSaveOnce.test.js` | 3개 중 3 | `fuel_records`·`misc_expense_records`.select |

- 두 App 파일은 앱 전체를 그려서, 시험마다 앱이 켜질 때 부르는 호출 3개가 공통으로 나온다.
- `App.test.js`의 일지 저장 시험은 `handlers.rpc`를 바꿔 끼운다. 이때 이미 있던 서버 함수 응답을 펼쳐 넣는 방식(`...previous`)이어서, 공통 등록을 위에 두어도 지워지지 않는다(`App.test.js:160~168`).
- "오류만 나면 통과" 11곳: 고칠 문구는 임시 사본에서 실제로 던져지는 오류를 찍어 확인했다.

| 위치 | 확인할 문구 |
|---|---|
| `data/cloud/inquiryNotices.test.js:38` | `x` |
| `data/cloud/inquiryNotices.test.js:57` | `관리자만 볼 수 있습니다.` |
| `data/sync/dailyInspections.test.js:30` | `down` |
| `data/sync/dailyInspections.test.js:109` | `아직 기록을 불러오는 중입니다. 잠시 후 다시 시도해 주세요.` |
| `data/sync/dailyInspections.test.js:113` | `세션이 바뀌어 남은 원격 작업을 중단합니다.` |
| `data/sync/dailyInspections.test.js:129` | `down` |
| `data/sync/dailyInspections.test.js:130` | `일상점검표를 지우지 못했습니다.` |
| `data/sync/dailyLogRemoval.test.js:35` | `down` |
| `data/sync/settingsSave.serial.test.js:104` | `profiles down` |
| `data/sync/syncExpenseItems.test.js:128` | `insert down` |
| `features/support/api/adminInquiries.test.js:48` | `관리자만 볼 수 있습니다.` |

- `syncExpenseItems.test.js:128`은 120-B 지시서 목록에 없던 곳이다. 이번에 괄호를 끝까지 따라가 둘째 인자가 있는지 보는 방식으로 다시 훑었다. 대상은 가짜 서버를 쓰는 테스트 50개 파일 전부다. 그 결과 위 11곳만 남았다.

### 목표 상태 (기대 동작)
1. 3개 파일에서 `createFakeSupabase({ allowUnregistered: true })`가 `createFakeSupabase()`로 바뀐다. 저장소의 `allowUnregistered: true`는 `fakeSupabaseClient.test.js`의 옵션 자체 시험 1곳만 남는다.
2. 3개 파일의 모든 시험이 통과한다. 각 시험은 실제로 부르는 호출만 등록하고, 응답은 예전 기본값이다(읽기는 빈 목록, 나머지는 빈 성공). 등록 위치는 이렇게 정한다.
   - 앱이 켜질 때마다 부르는 호출과 여러 시험이 같이 쓰는 호출은 기존 준비 단계에 한 번 넣는다.
     - `App.test.js`는 파일 위쪽의 준비 줄(33~41줄)에 넣는다.
     - `App.clientsCars.test.js`는 위쪽 준비 줄(27~32줄)과 `restoreDefaultHandlers()` 두 곳에 같이 넣는다. 두 곳은 지금도 같은 내용을 따로 적고 있다.
     - `expenseSaveOnce.test.js`는 `beginLoggedIn()`에 넣는다.
   - 한 시험만 쓰는 호출은 그 시험 안에 넣는다.
3. 3개 파일을 돌린 출력에 "등록 안 한 호출"이 한 번도 나오지 않는다. 시험 실패뿐 아니라 앱이 로그로 남기는 것도 포함한다.
4. 위 11곳의 `assert.rejects`가 표의 문구까지 확인한다. 앱이 `Error`가 아니라 서버 오류 객체를 그대로 던지는 곳이 섞여 있어서, 모두 `{ message: '...' }` 모양으로 확인한다(120-B와 같은 방식).
5. 기존 확인(`assert`)은 하나도 지우거나 느슨하게 하지 않는다.

### 건드릴 파일
- 표시 떼기와 등록 더하기
  - `src/app/App.test.js`
  - `src/app/App.clientsCars.test.js`
  - `src/features/expenses/ui/expenseSaveOnce.test.js`
- 문구 확인만
  - `src/data/cloud/inquiryNotices.test.js`
  - `src/data/sync/dailyInspections.test.js`
  - `src/data/sync/dailyLogRemoval.test.js`
  - `src/data/sync/settingsSave.serial.test.js`
  - `src/data/sync/syncExpenseItems.test.js`
  - `src/features/support/api/adminInquiries.test.js`
- 모두 테스트 파일이고 9개다.
- 공용 영향: `emptyOkHandlers`는 바꾸지 않는다. 이 묶음은 30개 파일이 같이 쓴다. 여기에 더하면 다른 엄격한 파일의 검사까지 약해진다.

### 안 건드릴 것
- 앱 코드(운영 파일)와 `testSupport/fakeSupabaseClient.js`는 바꾸지 않는다.
- `allowUnregistered` 옵션 자체는 남긴다. 옵션을 지우는 일은 이번 범위가 아니다. 지울지는 120을 끝낸 뒤 따로 묻는다.
- `fakeSupabaseClient.test.js`의 옵션 시험은 그대로 둔다.

### 검증
- 자동 검사: `npm test`, `npm run typecheck`, `npm run lint`, `npm run depcheck`, `npm run build`.
- 전체 테스트 출력에서 "등록 안 한 호출"을 grep한다. 시험 이름 말고는 0건이어야 한다(목표 3).
- `grep -rn "allowUnregistered: true" src`가 `fakeSupabaseClient.test.js` 1곳만 보여야 한다(목표 1).
- 엄격 동작 실험. 실험한 줄은 바로 되돌리고 `git diff`로 흔적이 없는지 본다.
  - `App.test.js`와 `expenseSaveOnce.test.js`에서 이번에 더한 등록 한 줄씩을 잠깐 지우고, 실패하는지 본다.
  - `dailyInspections.test.js:129`에서 오류를 만드는 `upsert` 응답을 잠깐 성공으로 바꾸고, 실패하는지 본다.
- 화면 변화 없음. CI 초록만 본다.

### 파일 길이
- 바뀌는 파일은 모두 테스트 파일이라 200줄 제한 밖이다.
- 예상 증가: App 2개 파일은 파일마다 10~25줄, `expenseSaveOnce`는 2줄 정도다. 문구 확인 6개 파일은 줄 수가 그대로다(같은 줄에 덧붙임).

### 실패 시 처리
- 등록을 더해도 통과하지 않는 시험이 나오면 멈추고, 원인을 적은 수정 착수지시서로 다시 승인받는다.
- 통과시키려고 확인을 지우거나 `allowUnregistered`를 남기지 않는다.
- 새 공용 도움 함수나 장치는 만들지 않는다.

### 사용자 확인 방법
- 화면이 바뀌는 일이 아니다. CI 초록과 위 실험 결과로 확인한다.

### 확인할 점
1. 120-B 목록에 없던 `syncExpenseItems.test.js:128`도 이번에 함께 고친다. 120-B에서도 1곳이 빠졌던 것과 같은 경우다. 같이 해도 될까? → 같이 한다 `[확인: 2026-11-01 보리 — "착수지시서 확정, 너가 작업 진행해"]`.

### 진행 기록
- 2026-11-01 보리 "너가 작업 진행해"로 이 세션이 구현했다. react-app `20676b0`. 2026-11-01 보리 "푸시해"로 이 세션이 push했고, CI verify·deploy 초록(run 41302263373)이다. 9개 파일이 바뀌었고 모두 테스트다.
- 등록 위치
  - 앱이 켜질 때마다 부르는 3개(`rpc.is_admin`·`rpc.settle_expired_driver_unlinks`·`support_inquiries.select`)와 여러 시험이 같이 쓰는 지출 읽기 3개는 기존 준비 단계에 넣었다.
    - `App.test.js`는 위쪽 준비 줄에 넣었다. 일지 화면 시험 여러 개가 같이 쓰는 `daily_logs`·`transport_details` 읽기와 `rpc.save_day_log`도 여기에 넣었다.
    - `App.clientsCars.test.js`는 위쪽 준비 줄과 `restoreDefaultHandlers()` 두 곳에 같은 6줄을 넣었다. `clients.update`도 같이 넣었다.
    - `expenseSaveOnce.test.js`는 `beginLoggedIn()`에 넣었다.
  - 한 시험만 쓰는 호출은 그 시험 안에 넣었다. `App.test.js`에서는 `rpc.remove_day_log`, 정비·기타 넣기, `profiles.upsert`다. `App.clientsCars.test.js`에서는 `rpc.rename_and_save_sub_vehicle`, 일지·콜 상세 읽기다.
  - 알아 둘 점: `App.test.js`는 원래 시험마다 응답을 초기화하지 않는다. 그래서 시험 안에서 넣은 응답은 그 뒤 시험에도 남는다. 뒤 시험들은 그 호출을 부르지 않으므로 지금 결과는 같다.
- 문구 확인 11곳은 모두 `{ message: '...' }`로 바꿨다.
- 검증
  - `npm test`: 964+942 통과, 실패 0. 출력에 "등록 안 한 호출"은 시험 이름 2개에만 나오고, 로그에는 없다.
  - typecheck·lint·depcheck·build는 통과했다.
  - `allowUnregistered: true`는 `fakeSupabaseClient.test.js` 1곳만 남았다.
  - 문구 없는 rejects를 다시 훑었다. 50개 파일에서 0곳이다.
- 엄격 실험(바로 되돌림, `git diff`에 흔적 없음)
  - `App.test.js`에서 정비 넣기 등록을 빼면 실패 1이다(등록 안 한 호출: maintenance_records.insert).
  - `expenseSaveOnce.test.js`에서 기타 읽기 등록을 빼면 실패 3이다.
  - `dailyInspections.test.js:129`의 오류 응답을 성공으로 바꾸면 실패 1이다(오류가 안 남).
- §5 리뷰
  - 범위: 바뀐 파일은 지시서의 테스트 9개뿐이다(`git show --stat`).
  - 몰래 늘린 것: 새 파일·장치는 없다.
  - 타입 꼼수: `any`·무시 주석·이중 단언이 없다(diff grep 0건).
  - 주석: 새 주석은 없다.
  - 200줄: 테스트 파일이라 제한 밖이다.
  - 테스트 진실성: 지운 assert가 없다. 문구 없는 rejects 11곳은 더 엄격해졌다.
  - 공용 영향: `emptyOkHandlers`와 fake는 그대로다.
  - 목표 1~5: 모두 충족했다.
