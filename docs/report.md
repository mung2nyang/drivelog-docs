# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 93-B 앱은 안 쓰고 테스트만 부르는 함수 정리 `[x]`

> **진행(2026-10-19)**: 보리 "착수지시서 확정 작업진행해". 코드 커밋 react-app `a24ded4`(18개 파일, +16 / −223).
> - 확인할 점 1(`domain/clients.js` `updateClientFixedUnitPrice`): 처음엔 답이 없어 지시서 "안 건드릴 것"대로 남겼다가, 보리 "응 지워"(권한 대로, 지우기만이라 §6 예외)로 **수정 커밋 react-app `5bb198c`** — 함수와 바로 위 설명 주석 4줄 삭제, `domain/clients.js` 246→232, 이름 grep 0건, `npm test` 1003 + 496·타입 검사·빌드 통과.
> - 교차검증(AGENTS §1): 표의 파일·줄·사용처 모두 코드와 같음(`ReportShareModal.test.js` 가져오기는 `:32`, 테스트 `:237`). 고친 것 없음.
> - 목표 1 grep(삭제 대상 11개 이름, `src` 전체 단어 일치): 코드 부르기·가져오기 **0건**. 남은 것은 예외로 둔 테스트 설명·주석 5곳 — `TaxInvoicePage.workInvoiceSoT.test.js:46`(loadWorkData)·`:91`(loadInvoices), `AppSettingsPage.settingsSoT.test.js:40`(loadPracticeSettings), `App.test.js:285·306`(loadWorkData).
> - 운영: `ReportShareModal.jsx` 65→63(다른 이름 줄 + 안 쓰게 된 `buildShareSmsUrl` 가져오기), `driverRevenueScope.js` 54→31(`phoneDigits`·`resolveDriverVehicleNumber`, 안 쓰게 된 `JsonValue`·`DriverRecord` 표기), `fuelRecords.js` 131→116·`maintenanceRecords.js` 125→110·`miscExpenseRecords.js` 122→107, `clientMutations.js` 145→128(함수 + `updateClientFixedUnitPrice` 가져오기), `invoices.js` 42→34·`workData.js` 78→71·`practiceSettings.js` 69→63(`load*` + `readJsonKey` 가져오기, `invoices.js`는 `JsonValue` 표기도), `commitHelpers.js` 106→98.
> - **지운 테스트**: `driverRevenueScope.test` "resolveDriverVehicleNumber matches digits and requires linked+vehicle", `vehicleMutations.test` "requestClientFixedUnitPrice는 로그인에서 서버 1회 후 Store만 바꾸고, 서버 실패는 Fail-Fast로 롤백한다". **뺀 단언**: `fuelRecords`·`maintenanceRecords`·`miscExpenseRecords` 테스트의 날짜별 묶음 3줄씩(같은 테스트의 "~만 교체" 단언 그대로, 이름에서 "날짜별 묶음과 " 뺌).
> - **옮긴 것(단언 그대로)**: SMS 주소 테스트 → `buildShareSmsUrl`(기본 `?`, iPhone `&`). `driverRevenueScope.test` 남은 테스트는 준비 줄만 `vehicleNumber = mine`(안 쓰게 된 `drivers` 준비 배열도 지움). `guestBackup.test`·`workData.test` 준비 줄은 `commitBatch([{ domain: 'workDataDeletedDates', … }])`.
> - `npm test` 1004 + 497 → **1003 + 496**(지운 테스트 2개만큼), 타입 검사·빌드 통과. 새 테스트 없음·revert-check 해당 없음(지시서대로).
> - 개발 서버(비회원): 차량 유지비 화면 열림, 매출 화면 300,000원, 서류 발급 → 운송비 내역서 → 공유 창 "문자로 보내기" 보임, 콘솔 오류 없음.
> - push 뒤 CI verify·deploy 초록(run 39208394409 — 두 커밋을 한 번에 push해 위 커밋 `5bb198c`에서 1회 실행, 그 안에 `a24ded4` 포함).
> - §5 리뷰(데스크톱 AI, `a24ded4~1..5bb198c`): ①범위 = 지시서 운영 10·테스트 8 + 보리 결정으로 `domain/clients.js` ②새 파일·저장 키 0 ③타입 꼼수 0 ④200줄 — `domain/clients.js` 246→232(아직 200 초과, 지우기만 예외는 보리 결정), 나머지 이내 ⑤테스트 1,422 → 1,420(지운 2개), `assert.` 5,247 → 5,226(−21: 지운 두 테스트 5·10 + 지출 묶음 2×3 — 파일별로 맞춰 봄), 더한 단언 0 ⑥공용 값 — 해당 없음 ⑦목표 1 grep 코드 0건(설명·주석 5곳은 예외대로).
> - 배포본 로그인 확인(2026-10-19, 내장 브라우저): 차량 유지비 → 10월 정비 날짜별 묶음(2026-10-10, 50,000원) 그대로, 매출 → 당월 순이익 2,284,586원 등 표시, 서류 발급 → 운송비 내역서 → [공유] 창에 "문자로 보내기" 보임(누르지 않고 닫음), 콘솔 오류 없음. 첫 묶음 파일 이름이 89-B 때와 같음 — 지운 함수는 원래 빌드에서 빠지던(안 쓰는 코드) 것이라 결과물이 거의 안 바뀌는 게 맞음.
> - 보리 최종 승인(2026-10-19).

보리 "승인"(2026-10-19, 93-A `[x]`). 93-A 지시서 "확인할 점"대로 바로 다음. 처리 원칙(93-A 때 권한 것): 그 테스트가 **앱이 실제로 쓰는 코드를 대신 지키면 → 테스트를 그 코드 쪽으로 옮김**, 아니면 → 함수와 테스트를 같이 지움.

### 쉬운 요약
- 앱 화면은 부르지 않는데 테스트만 부르는(또는 테스트 설명에 이름만 남은) 함수가 10개 있습니다.
- 하나씩 보니: 2개는 앱이 쓰는 진짜 함수의 "다른 이름"이라 테스트를 진짜 함수로 돌리고, 나머지는 앱에 대신하는 함수가 따로 있어 함수와 그 테스트만 지웁니다.
- 화면·저장 동작은 그대로입니다.

### 현재 상태 (코드 확인 — 함수마다 판단)
| # | 함수(파일) | 앱이 대신 쓰는 것 | 테스트 | 판단 |
|---|---|---|---|---|
| 1 | `buildReportSmsUrl`(`components/ReportShareModal.jsx:14`) | `= buildShareSmsUrl`의 다른 이름(`shared/ShareFileModal.jsx:10`, 앱이 씀) | `ReportShareModal.test.js:237` 1개 | **테스트를 `buildShareSmsUrl`로** 돌리고 다른 이름 줄 삭제 |
| 2 | `resolveDriverVehicleNumber`·`phoneDigits`(`components/revenue/driverRevenueScope.js:11·21`) | 없음(매출 화면은 `scopeWorkDataToVehicle`·`scopeSettingsToVehicle`만 가져옴, `OwnerRevenueView.jsx:11`). `phoneDigits`는 `resolve…` 안에서만 씀 | `driverRevenueScope.test.js:21` 전용 1개, `:33`은 준비에만 씀 | 둘 다 삭제. `:21` 테스트 삭제, `:33`은 준비 줄을 그 값(`mine`)으로 바꿔 `scopeWorkDataToVehicle` 검사 그대로 |
| 3~5 | `groupFuelExpensesByDate`·`groupMaintExpensesByDate`·`groupMiscExpensesByDate`(`domain/fuelRecords.js:90`·`maintenanceRecords.js:86`·`miscExpenseRecords.js:84`) | `domain/expenses.js:147` `groupExpensesByDate`(`MaintFuelPage.jsx:77`, 테스트 `expenses.test.js` 있음) | 각 파일 테스트 1곳(`:116`·`:110`·`:106`) | 셋 다 삭제 + 그 단언만 삭제 |
| 6 | `requestClientFixedUnitPrice`(`lib/clientMutations.js:118`) | 없음 — 화면에서 부르는 곳 0. 같은 저장 길(`saveClientsToCloud`·Fail-Fast 되돌리기·막힘 이유)은 `requestClientTaxInfo` 테스트(`vehicleMutations.test.js:733`)와 `cloudMemorySave`·`directMutationActions` 테스트가 계속 봄 | `vehicleMutations.test.js:676` 1개 | 함수·테스트 삭제(가져오기 `:9`에서 이름만) |
| 7~9 | `loadInvoices`(`lib/invoices.js:17`)·`loadWorkData`(`lib/workData.js:12`)·`loadPracticeSettings`(`lib/practiceSettings.js:17`) | Store 구독(`useOwnerInvoices`·`useOwnerWorkData`·`useOwnerSettings`) | **부르는 테스트 0** — 테스트 설명 문구·주석에 옛 방식 이름만 남음(`TaxInvoicePage.workInvoiceSoT.test.js:46·91`, `AppSettingsPage.settingsSoT.test.js:40`, `App.test.js:285·306`) | 함수 삭제(같이 안 쓰게 되는 `readJsonKey` 가져오기·`JsonValue` 표기도). 테스트 문구는 "옛 방식이면 안 바뀐다"는 설명이라 그대로 |
| 10 | `commitWorkDataDeletedDates`(`store/commitHelpers.js:~100`) | 앱은 `lib/workData.js:49`에서 `commitBatch`로 같이 씀 | `guestBackup.test.js:248`·`workData.test.js:36`이 **준비용**으로 씀 | 함수 삭제, 두 준비 줄은 `commitBatch([{ domain: 'workDataDeletedDates', ownerKey, value }])`로 |

### 목표 상태 (기대 동작)
1. 위 표의 삭제 대상(`buildReportSmsUrl`·`resolveDriverVehicleNumber`·`phoneDigits`·`group{Fuel,Maint,Misc}ExpensesByDate`·`requestClientFixedUnitPrice`·`loadInvoices`·`loadWorkData`·`loadPracticeSettings`·`commitWorkDataDeletedDates`) 정의가 없고, `src` **코드**(테스트 포함)에서 부르기·가져오기 0건. 테스트 설명 문자열·주석 속 이름(7~9번)은 예외로 진행 기록에 줄 번호.
2. 1번 SMS 주소 테스트는 같은 단언(기본 `?`, iPhone `&`)으로 `buildShareSmsUrl`을 검사.
3. 2번 `:33` 테스트·10번 두 테스트는 준비 방법만 바뀌고 단언 그대로.
4. 지우는 테스트는 위 표의 것만(2번 `:21`, 3~5번 단언, 6번 `:676`) — 진행 기록에 이름.
5. 그 함수만 쓰던 가져오기 줄·타입 표기 정리(타입 검사가 "안 쓰는 이름"을 안 잡으므로 손으로).

### 건드릴 파일
- 운영 10개: `components/ReportShareModal.jsx`, `components/revenue/driverRevenueScope.js`, `domain/fuelRecords.js`, `domain/maintenanceRecords.js`, `domain/miscExpenseRecords.js`, `lib/clientMutations.js`, `lib/invoices.js`, `lib/workData.js`, `lib/practiceSettings.js`, `store/commitHelpers.js`.
- 테스트 8개: `ReportShareModal.test.js`, `driverRevenueScope.test.js`, `fuelRecords.test.js`, `maintenanceRecords.test.js`, `miscExpenseRecords.test.js`, `vehicleMutations.test.js`, `guestBackup.test.js`, `workData.test.js`(위 표 그대로).

### 안 건드릴 것
- `domain/clients.js` `updateClientFixedUnitPrice`(`:159`) — 6번을 지우면 이것도 쓰는 곳 0이 되지만, 파일이 **246줄(200 초과)**이라 AGENTS §6상 고치려면 분리설계안이 먼저 — 아래 "확인할 점" 1.
- 테스트용 손잡이 `reset*ForTests`·`writeJsonKey`·`pendingDayWriteCount`, 91번 대상 `durableWriteGuard.js` — 93-A와 같은 이유.
- `lib/clients.js`·`invoices.js`·`workData.js`·`practiceSettings.js`의 `save*`·재수출 — 계속 쓰임.

### 검증
- `npm test`·타입 검사·빌드 통과. 테스트 수는 지운 만큼(2번 1개 + 6번 1개 + 3~5번이 통째 테스트면 그만큼)만 줄어듦 — 진행 기록에 전후 수.
- 목표 1 grep을 진행 기록에.
- 새 테스트 없음(동작 변화 없음 — revert-check 해당 없음).
- 개발 서버(비회원): 지출 화면(정비·주유 날짜별 묶음), 서류 발급 → 운송비 내역서 공유 창 열기(문자 단추), 매출 화면 → 콘솔 오류 없음.

### 파일 길이
- 줄어들기만 함: `driverRevenueScope.js` 54 → 약 35, `fuelRecords.js` 131 → 약 120, `maintenanceRecords.js` 125 → 약 114, `miscExpenseRecords.js` 122 → 약 111, `clientMutations.js` 145 → 약 132, `invoices.js` 42 → 약 35, `workData.js` 78 → 약 72, `practiceSettings.js` 69 → 약 65, `commitHelpers.js` 106 → 약 100, `ReportShareModal.jsx` 65 → 64.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(지우기만).
- 지우고 나서 테스트·타입 검사가 하나라도 실패하면 앱 어딘가에서 아직 쓴다는 뜻 — 되살리지 말고 멈추고 어디서 쓰는지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 차량 유지비 화면 → 날짜별 묶음 그대로.
2. 서류 발급 → 운송비 내역서 공유 창 → 문자 단추 보임(누르지 않음).
3. 매출 화면 → 금액 그대로.

### 확인할 점
1. `domain/clients.js`(246줄)에서 쓸모없어지는 함수 하나(`updateClientFixedUnitPrice`, 약 10줄)를 지우는 것도 AGENTS §6의 "200줄 넘는 파일을 고치면 분리설계안 먼저"에 걸립니다. **지우기만 하는 수정(줄이 줄어듦)은 예외로 이번에 같이 지우기**를 권합니다. 다른 안: 이번엔 남기고 `domain/clients.js` 분리설계 때 지우기.
