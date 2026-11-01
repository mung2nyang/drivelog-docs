# docs/report.md — 현재 슬라이스 착수지시서

120-A: drivelog-docs `81f927a`. 119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 120-B. 서버 쪽 테스트 10개 파일을 엄격으로 `[x]`

- 근거: 로드맵 120(③). 120-A(`3e59665`)의 다음이다.
- 2026-11-01 보리가 "응"으로 지시서를 요청했다. 같은 날 확정했다 `[확인: 2026-11-01 보리 — "착수지시서 확정"]`.
- 120-A에서 "예전처럼"(`allowUnregistered`) 표시를 단 13개 중 `data/` 아래 10개를 이번에 옮긴다. 남은 3개는 120-C다.
- "오류만 나면 통과하는 시험에 오류 문구 확인"(`[확인: 2026-11-01 보리 — "응고쳐"]`)은 이번 10개 파일 안의 4곳만 한다. 이미 엄격한 파일 쪽 10곳은 120-C로 넘긴다. 아래 "안 건드릴 것"에 목록이 있다.

### 쉬운 요약
- 지금 서버 쪽 테스트 10개 파일은 미리 정해 두지 않은 호출에도 "성공"을 받고 있다.
- 이번에는 그 파일들에서 "예전처럼" 표시를 떼고, 테스트마다 실제로 쓰는 서버 응답을 정해 둔다. 그러면 앱이 새로운 서버 호출을 하거나 엉뚱한 표를 건드릴 때 이 테스트들이 잡아낸다.
- 같은 파일 안에서 "오류만 나면 통과"하던 시험 4곳은 "그 오류인지"까지 보게 한다.
- 앱 코드는 바꾸지 않는다.

### 현재 상태 (코드 확인)
- 엄격 실험(임시 사본에서 10개 파일의 표시만 뗌, 원래 폴더는 안 바꿈): 32개 시험이 실패한다(120-A 실험과 같은 수).

| 파일 | 먼저 걸린 등록 안 한 호출 |
|---|---|
| `data/cloud/fetchAllRows.saves.test.js` | `maintenance_records.select` |
| `data/hydrate/hydrate.test.js` | `daily_logs`·`transport_details`·`fuel_records`.select, `rpc.settle_expired_driver_unlinks`(앱이 받아 로그만 남김) |
| `data/hydrate/employedDriverClients.test.js` | `rpc.get_linked_owner_business_info` |
| `data/hydrate/hydrateRetry.test.js` | `rpc.get_linked_owner_business_info` |
| `data/sync/cloudMemorySave.test.js` | `profiles.upsert`, `fuel_records`·`maintenance_records`·`tax_invoices`.select |
| `data/sync/directMutationActions.test.js` | `vehicles.delete`, `transport_details.update`, `driver_links.update` |
| `data/sync/expensesSave.noVehicle.test.js` | `daily_logs.select` |
| `data/sync/expensesSave.order.test.js` | `maintenance_records.select` |
| `data/sync/invoicesSave.renamedCar.test.js` | `rpc.rename_and_save_sub_vehicle` |
| `data/sync/vehicleRename.test.js` | `vehicles.update`, `fuel_records.select` |

- 시험 하나에서 첫 번째 오류만 보이므로, 실제로 등록할 호출은 위보다 많을 수 있다.
- 지금 엄격한 37개 파일: 전체 테스트 출력에 "등록 안 한 호출"이 시험 이름 말고는 한 번도 나오지 않는다(2026-11-01 `npm test` 출력 grep). 앱이 로그로 남기며 삼킨 경우도 없다는 뜻이다.
- 이번 파일 안의 "오류만 나면 통과" 시험 4곳
  - `hydrate.test.js:108` 차량 읽기 실패
  - `cloudMemorySave.test.js:353` 일지 읽기 실패
  - `cloudMemorySave.test.js:388` 계산서 넣기 실패
  - `expensesSave.order.test.js:75` 메인 넣기 실패
  - 네 곳 모두 앞 줄에서 서버 오류(`first attempt fails`·`daily down`·`tax down`·`insert down`)를 일부러 만든다. 그런데 오류 문구는 확인하지 않는다.

### 목표 상태 (기대 동작)
1. 위 10개 파일에서 `createFakeSupabase({ allowUnregistered: true })`가 `createFakeSupabase()`로 돌아간다. 저장소 전체의 `allowUnregistered: true`가 13개에서 3개로 줄어든다(120-C 몫).
2. 10개 파일의 모든 시험이 통과한다. 각 시험은 실제로 부르는 표·동작·서버 함수만 등록한다.
   - 응답은 예전 기본값과 같다. 읽기는 빈 목록, 나머지는 빈 성공이다. 시험이 따로 값을 정해 둔 곳은 그대로 둔다.
   - 같은 파일의 여러 시험이 같은 호출을 쓰면, 그 파일의 기존 준비 단계(`beforeEach` 또는 이미 있는 준비 함수)에 한 번 등록한다.
3. 10개 파일을 돌린 출력에 "등록 안 한 호출"이 한 번도 나오지 않는다. 시험 실패뿐 아니라 앱이 로그로 남기는 것도 포함한다(`hydrate.test.js`의 `settle_expired_driver_unlinks` 같은 경우).
4. 위 4곳의 `assert.rejects`가 그 시험이 만든 오류인지까지 확인한다. 앱이 문구를 바꿔 던지면 바뀐 문구로 확인한다.
5. 기존 확인(`assert`)은 하나도 지우거나 느슨하게 하지 않는다.

### 건드릴 파일
- 위 표의 테스트 파일 10개만 바꾼다. 바꾸는 것은 세 가지다.
  - 한 줄 표시 떼기
  - 응답 등록 더하기
  - 4곳 오류 문구 확인
- 공용 영향: `emptyOkHandlers`는 바꾸지 않는다. 이 묶음은 30개 파일이 같이 쓴다(`grep -c "emptyOkHandlers()"`). 여기에 표를 더하면 엄격한 다른 파일에서도 그 표가 "등록됨"이 되어 엄격 검사가 약해진다. 등록은 파일 안에서만 한다.

### 안 건드릴 것
- 앱 코드(운영 파일)와 `testSupport/fakeSupabaseClient.js`는 바꾸지 않는다.
- `app/App.clientsCars.test.js`·`app/App.test.js`·`features/expenses/ui/expenseSaveOnce.test.js`의 표시는 120-C에서 뗀다.
- 이미 엄격한 파일의 "오류만 나면 통과" 10곳은 120-C로 넘긴다.
  - `data/cloud/inquiryNotices.test.js:38·57`
  - `data/sync/dailyInspections.test.js:30·109·113·129·130`
  - `data/sync/dailyLogRemoval.test.js:35`
  - `data/sync/settingsSave.serial.test.js:104`
  - `features/support/api/adminInquiries.test.js:48`
  - 넘기는 이유: 이번 묶음을 10개 파일 안에 묶기 위해서다.
  - `inviteCode.security.test.js:78`·`employedDriverClients.test.js:222`는 이미 문구를 확인하므로 대상이 아니다.

### 검증
- 자동 검사: `npm test`, `npm run typecheck`, `npm run lint`, `npm run depcheck`, `npm run build`.
- 10개 파일 출력에 "등록 안 한 호출"이 없는지 grep한다(목표 3).
- 엄격 동작 실험
  - `directMutationActions.test.js`와 `hydrate.test.js`에서 이번에 더한 등록 한 줄씩을 잠깐 지운다. "등록 안 한 호출"로 실패하는지 본다.
  - 4곳 중 하나(`cloudMemorySave.test.js:388`)에서 오류를 만드는 줄(`tax down`)을 잠깐 지운다. 이제는 문구가 달라 실패하는지 본다. 예전에는 "등록 안 한 호출" 오류로도 통과했을 자리다.
  - 실험한 줄은 바로 되돌리고 `git diff`로 실험 흔적이 없는지 확인한다.
- 고치기 전 실패 확인은 해당 없다. 바꾸는 것이 테스트뿐이라, 위 실험이 그 역할을 한다.
- 화면 변화 없음. CI 초록만 본다.

### 파일 길이
- 바뀌는 파일은 모두 테스트 파일이라 200줄 제한 밖이다. 늘어나는 줄은 파일마다 대략 1~15줄이다.

### 실패 시 처리
- 등록을 더해도 통과하지 않는 시험이 나오면 멈춘다. 앱 코드가 예상과 다르게 서버를 부르는 것일 수 있으니, 원인을 적은 수정 착수지시서로 다시 승인받는다.
- 시험을 통과시키려고 확인을 지우거나 `allowUnregistered`를 남기지 않는다.
- 새 공용 도움 함수나 장치는 만들지 않는다.

### 사용자 확인 방법
- 화면이 바뀌는 일이 아니다. CI 초록과 위 실험 결과(등록을 빼면 잡아냄, 엉뚱한 오류로는 통과 못 함)로 확인한다.

### 확인할 점
1. "오류만 나면 통과" 14곳 중 이번 10개 파일 안의 4곳만 B에서 한다. 이미 엄격한 파일의 10곳은 C로 넘긴다. C는 그만큼 커진다(3개 → 8개 파일). 이렇게 나눠도 될까? → 이대로 나눈다 `[확인: 2026-11-01 보리 — "착수지시서 확정"]`.

### 진행 기록
- 2026-11-01 보리 "너가해"로 이 세션이 구현했다. react-app `b05cca6`. 2026-11-01 보리 "응 예외로 푸시까지"로 이 세션이 push했고, CI verify·deploy 초록(run 41295716673)이다.
- 10개 파일에서 표시를 뗐다. 저장소의 `allowUnregistered: true`는 13개에서 3개가 됐다(`fakeSupabaseClient.test.js`의 옵션 자체 시험은 원래 셈에서 빠져 있다).
- 등록한 것은 실제 호출만이다. 응답은 예전 기본값(읽기 빈 목록, 나머지 빈 성공)이다. 파일마다 등록 위치는 이렇다.
  - 기존 준비 함수가 있는 파일은 그 함수에 넣었다. 대상은 `fetchAllRows.saves`(beforeEach), `expensesSave.order`(seedMoveToMain), `invoicesSave.renamedCar`(seedRenamable), `cloudMemorySave`(seedSubVehicleSave)다.
  - 나머지는 그 호출을 쓰는 시험 안에 넣었다.
  - 지시서와 다른 점 1: `hydrate.test.js`는 21개 시험이 모두 `settle_expired_driver_unlinks`를 부르는데 공통 준비 단계가 없다. 시험마다 처음에 `resetHandlers()`를 불러서 beforeEach에 넣어도 지워진다. 그래서 파일 안에 `settleOk()` 한 줄을 두고 21곳의 `emptyOkHandlers()` 옆에 붙였다. 공용 함수는 아니다.
- 문구 확인: 4곳에 더해 `cloudMemorySave.test.js:139`(`profiles down`)도 고쳤다. 지시서와 다른 점 2다. 지시서를 쓸 때 조사에서 빠뜨린 곳인데, 이번 10개 파일 안에 있어 "파일 안은 B" 원칙에 따라 같이 했다.
  - 앱이 서버 오류 객체를 그대로 던져서, 정규식이 아니라 `{ message: '...' }`로 확인한다. hydrate는 앱이 만든 `hydrate 조회 실패: vehicles`로 확인한다.
- 검증
  - `npm test`: 964+942 통과, 실패 0. 출력에 "등록 안 한 호출"은 시험 이름 2개에만 나오고, 로그에는 없다.
  - typecheck·lint·depcheck·build는 통과했다.
- 엄격 실험(바로 되돌림, `git diff`에 흔적 없음)
  - `directMutationActions`의 `driver_links.update` 등록을 빼면 실패 1이다(등록 안 한 호출: driver_links.update).
  - `hydrate`에서 H-1의 `fuel_records.select`를 빼면 실패 1이다.
  - `cloudMemorySave`의 `tax down` 줄을 빼면 실패 1이다. 문구가 "등록 안 한 호출: tax_invoices.insert"로 달라서 걸린다.
  - 확인된 한계: `hydrate`의 `settle_expired_driver_unlinks` 등록을 빼도 시험은 통과한다. 앱이 그 오류를 로그로만 남기기 때문이다. 이 경우는 출력 grep(목표 3)으로만 잡힌다.
- §5 리뷰
  - 범위: 바뀐 파일은 테스트 10개뿐이다(`git show --stat`).
  - 몰래 늘린 것: 새 파일·장치는 없다.
  - 타입 꼼수: `any`·무시 주석·이중 단언이 없다(diff grep 0건).
  - 주석: 새 주석은 1줄이고 로드맵 번호·날짜가 없다.
  - 200줄: 테스트 파일이라 제한 밖이다.
  - 테스트 진실성: 지운 assert가 없다. 문구 없는 rejects 5곳은 더 엄격해졌다.
  - 공용 영향: `emptyOkHandlers`와 fake는 그대로다.
  - 목표 1~5: 모두 충족했다. 목표 3은 grep으로 확인했다.
