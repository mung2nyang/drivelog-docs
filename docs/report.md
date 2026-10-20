# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-H1 `lib/` 폴더 없애기 — 재수출 껍데기 삭제·저장 함수와 도우미를 제자리로 `[x]`

보리 승인(2026-10-20, 59-G `[x]`). 59-H("`lib/` 남은 것 정리 + `app/routes`·`app/shell` + 위층 부르기 7곳")가 커서 둘로 나눔(보리 결정 2026-10-20, 아래 확인할 점 1) — 이 지시서는 앞쪽 H1(`lib/`만). 동작 변화 0, `git mv`, 경로 줄만 바뀌는 200줄 넘는 파일은 §6 예외(보리 결정 2026-10-19).
처음 계획의 "`domain/`과 같은 이름 파일 11쌍" 문제(로드맵 59)를 여기서 끝냄.
플레이북 트리거(`lib/*` 저장 창구) — **위치·파일 이름만**, 저장 동작은 그대로.

> **진행(2026-10-20)**: 보리 "착수지시서 확정,작업진행해". 코드 커밋 react-app `01af085`(152개 파일: 지움 8·새로 2·옮김 30·고침 112).
> - 교차검증(AGENTS §1) 고친 것 ① `lib/` 남은 운영 파일은 24가 아니라 23개(도우미가 11이 아니라 10개 — `failureMessages`·`assetPath`·`callDetailSchema`·`syncExpenseItems`·`syncTaxInvoicesTable`·`profileCloudCommit`·`chunkReload`·`errorReport`·`haptic`·`registerServiceWorker`) ② `registerServiceWorker`의 테스트 이름은 `serviceWorker.test.js` ③ 지울 함수 `loadCars`·`saveCars`·`loadDrivers`·`loadProfile` 쓰는 곳 0 확인(테스트 문구 속 이름만).
> - 순서: 1단계 대본으로 옮기기·경로(32개 옮김, 99개 고침) → 2단계 대본으로 `import {…}`·`await import()`를 이름별로 나눔(70개 파일 — 저장 함수는 `*Save.js`, 나머지는 같은 이름 `domain/` 파일, `workData` 이름은 `day-record`·`call-details`·`payments`로) → 저장 파일 7개에서 재수출 줄·`loadDrivers`·`loadProfile`·쓰지 않게 된 `readJsonKey` 가져오기 지움 → 껍데기 5 + `cars.js` `git rm`.
> - 저장 파일 7개 본문: `git diff -M` — `expensesSave`·`invoicesSave`·`settingsSave`·`profileSave`·`workDataSave`는 이름 바뀜으로 잡히고 경로·머리 주석·재수출 줄·`loadProfile` 말고 다른 줄 0. `clientsSave`·`driversSave`(11줄)는 파일이 작아 git이 지움+새로 만듦으로 봄 — 내용 비교로 `saveClients`·`saveDrivers` 본문 글자 그대로 확인.
> - 여러 파일 검사 테스트 6개 자리: `assignedVehicleDay`·`cloudMemorySave`·`inviteCode.security` → `data/sync/`, `employedDriverClients` → `data/hydrate/`, `practiceSettings.serial` → `data/sync/settingsSave.serial.test.js`, `workData` → `data/sync/workDataSave.test.js`.
> - 테스트의 가짜 모듈(`memberRestore.test.js`·`memberRestoreRecords.test.js`)은 원래 `saveExpenses`·`saveInvoices`만 바꿔 끼웠어서 새 `expensesSave`·`invoicesSave`로 경로만 바뀌고 그대로 맞음.
> - 손으로 고친 것: `shared/utils/failureMessages.test.js:22,30` 읽는 경로·훑는 폴더(`..` → `../..`), `package.json` `test:unit`에서 `src/lib/**` 뺌, 주석 속 옛 이름 12줄(`domain/cars|clients|drivers|expenses|invoices|practiceSettings|taxInvoices|workDataTombstones|financeTaxInvoiceEntries.js`, `data/store/persistDomainSchema.js:54`, `data/sync/pendingWorkDataWritesTypes.js:5`, `features/day-log/ui/dayLogTypes.js:3`), 저장 파일 7개 머리 주석(재수출 배럴 → 저장만).
> - 목표 5·6: `src/lib/` 없어짐, `lib/` grep 0건, `domain/`과 같은 이름 저장 파일 0쌍.
> - 빌드 비교: 48개 파일. 묶음 이름 `invoices` → `invoicesSave` 1개(저장 파일 이름을 따름). 내용은 `index` 묶음 하나만 달랐고, 줄여 쓰지 않은 빌드로 비교하니 **줄 순서만 바뀌고 줄 내용은 모두 같음**(모듈을 부르는 순서가 바뀌어 `call-details`의 함수 13개와 함수 8개·상수 1개 묶음 자리만 옮겨짐 — 함수 선언과 고정 값뿐이라 동작 같음).
> - 줄 수: `MaintFuelPage.jsx` 245 → 246(가져오기 한 줄이 저장·계산 둘로 나뉨, §6 예외 — 경로 줄). 그 밖에 1줄 늘어난 5개는 모두 200줄 이하.
> - `npm test` 864 + 639 = 1503 통과(같은 수), 타입 검사·빌드 통과.
> - 개발 서버(비회원): 거래처·차량 추가, 일지 콜 저장 → 새로고침 뒤 그대로, 차량 유지비 정비 5만 원 추가 → 새로고침 뒤 그대로 → 삭제 → 새로고침 뒤 없음, 다크 모드 켬 → 새로고침 뒤 켜짐 → 끔 → 새로고침 뒤 꺼짐, 개인정보 상호 입력 → 새로고침 뒤 그대로 → 지움 → 새로고침 뒤 빈 칸, 콘솔 오류 없음.
> - 지시서 오류(데스크톱 AI): 남은 `lib/` 운영 파일·도우미 수를 24·11로 적음(실제 23·10), `registerServiceWorker` 테스트 이름을 틀림 — 교차검증 정정이 맞음.
> - push 뒤 CI verify·deploy 초록(run 39399633016).
> - §5 리뷰(데스크톱 AI): ①범위 = 지움 8(껍데기 5·`cars`·`clients`·`drivers` — 뒤 둘은 `clientsSave`·`driversSave` 새 파일로)·새 2·이름 바뀜 30·고침 112 ②새 파일 2(지시서의 저장 파일 7개 중 git이 새 파일로 본 것), 새 저장 키 0 ③타입 꼼수 0 ④200줄 — `MaintFuelPage.jsx` 245→246(가져오기 줄이 둘로 나뉨, §6 예외 — 경로 줄) ⑤테스트 수 1,424 → 1,424 ⑥공용 값 — 저장 함수 쓰는 곳 전부 새 출처(grep) ⑦목표 1~8 대조. 저장 함수 11개(`saveClients`·`saveDrivers`·`saveExpenses`·`saveInvoices`·`savePracticeSettings`·`applyTheme`·`saveProfile`·`saveWorkData`·`saveWorkDataWithTombstoneCheck`·`saveLogWorkDataWithTombstoneCheck`·`EMPTY_PROFILE`)의 본문을 옛 `lib/` 파일과 하나씩 비교 — 모두 같음(다른 1줄은 `saveWorkDataWithTombstoneCheck` 안 JSDoc 타입 경로 `../domain` → `../../domain`). `src/lib/` 없어짐, `lib/` grep 0건, `test:unit` 범위에서 `src/lib` 빠짐.
> - 배포본 로그인 확인(2026-10-20, 내장 브라우저): 새 묶음(`index-ChUfW5kI.js`). 오늘 일지 2회 저장 → 새로고침 뒤 그대로 → 비워서 되돌림(`workDataSave` 경로), 앱 설정 다크 모드 켬 → 새로고침 뒤 어두운 화면 → 끔 → 새로고침 뒤 밝은 화면(원래대로, `settingsSave`), 매출 당월 순이익 2,284,586원·차량 유지비 정비 합계 50,000원 그대로, 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-20).

### 쉬운 요약
- `lib/`에 남은 24개 파일을 정리해 `lib/` 폴더를 없앱니다.
- `domain/`의 계산 규칙을 그대로 다시 내보내기만 하는 껍데기 5개는 지우고, 부르는 곳이 `domain/`을 바로 부르게 합니다.
- 저장 함수와 계산 규칙 다시 내보내기가 섞인 8개는 저장 함수만 `data/sync`의 새 이름 파일로 옮기고(계산 규칙과 이름이 겹치지 않게), 다시 내보내기 부분은 지웁니다. 아무도 안 쓰는 옛 읽기 함수 4개도 같이 지웁니다.
- 나머지 도우미 11개는 쓰는 곳에 맞는 폴더로 옮깁니다.
- 화면·저장 동작은 그대로입니다.

### 현재 상태 (코드 확인 — grep)
- 재수출 껍데기 5개(2줄, `export * from '../domain/…'`만): `calendar`(부르는 파일 7)·`finance`(9)·`formatPhone`(3)·`money`(21)·`receivables`(6).
- 저장 + 재수출 8개: `cars`(부르는 파일 2 — 남은 함수 `loadCars`·`saveCars`는 쓰는 곳 0, 주석·테스트 문구에만 이름)·`clients`(4, `saveClients` ← `data/sync/clientMutations`)·`drivers`(5, `saveDrivers` ← `drivers/ui/DriverConnectionPage`, `loadDrivers` 쓰는 곳 0)·`expenses`(13, `saveExpenses` ← 회원 복원·일지 지출 훅·차량 유지비)·`invoices`(5, `saveInvoices` ← 회원 복원·세금계산서 탭)·`practiceSettings`(9, `savePracticeSettings`·`applyTheme` ← 온보딩·일지·알림·앱 설정·앱 틀)·`profile`(8, `EMPTY_PROFILE`·`saveProfile` ← `data/store/ownerProfileDriversHooks`·개인정보 훅, `loadProfile` 쓰는 곳 0)·`workData`(5, `saveWorkData*` 3개 ← `ownerFinance`·`pendingWorkDataWrites`·`dayDraftLifecycle`, 계산 규칙 이름 나열 재수출 `:63-71`).
- 도우미 11개: `failureMessages`(부르는 파일 23 — 화면·`data/` 모두)·`assetPath`(7 — 화면·공용)·`callDetailSchema`(+ 테스트 2, `data/store/persistDayRecord`·`data/sync/durablePatchSchema`)·`syncExpenseItems`(+ 테스트, `lib/expenses`만)·`syncTaxInvoicesTable`(`lib/invoices`만)·`profileCloudCommit`(+ 테스트, `practiceSettings`·`profile`)·`chunkReload`·`errorReport`·`haptic`·`registerServiceWorker`(각 테스트, 앱 틀·`PageLoadErrorBoundary`만).
- 여러 파일을 함께 검사하는 테스트 6개: `assignedVehicleDay`·`cloudMemorySave`·`employedDriverClients`·`inviteCode.security`·`practiceSettings.serial`·`workData`.
- `package.json` `test:unit`에 `src/lib/**/*.test.js`.

### 목표 상태 (기대 동작)
1. 재수출 껍데기 5개 삭제. 부르던 곳은 같은 이름의 `domain/` 파일을 바로 부름(가져오는 이름 그대로).
2. 저장 + 재수출 8개:
   - `cars.js` 삭제(남은 함수 쓰는 곳 0) — 부르던 2곳은 `domain/cars.js`로.
   - 나머지 7개의 저장 함수는 `data/sync/`의 새 이름 파일로: `clientsSave.js`(`saveClients`), `driversSave.js`(`saveDrivers`), `expensesSave.js`(`saveExpenses`), `invoicesSave.js`(`saveInvoices`), `settingsSave.js`(`savePracticeSettings`·`applyTheme`), `profileSave.js`(`EMPTY_PROFILE`·`saveProfile`), `workDataSave.js`(`saveWorkData`·`saveWorkDataWithTombstoneCheck`·`saveLogWorkDataWithTombstoneCheck`). 함수 본문은 글자 그대로.
   - 쓰는 곳 0인 `loadDrivers`·`loadProfile`(과 `cars.js`의 `loadCars`·`saveCars`)는 옮기지 않고 지움.
   - 이 8개를 통해 `domain/` 이름을 가져오던 곳은 `domain/` 파일을 바로 부름.
3. 도우미: `failureMessages`·`assetPath` → `shared/utils/`, `callDetailSchema`(+ 테스트 2) → `data/store/`, `syncExpenseItems`(+ 테스트)·`syncTaxInvoicesTable`·`profileCloudCommit`(+ 테스트) → `data/sync/`, `chunkReload`·`errorReport`·`haptic`·`registerServiceWorker`(+ 각 테스트) → `app/`.
4. 여러 파일 검사 테스트 6개 → 주로 검사하는 파일 옆(`data/sync`·`data/hydrate`·`features/…` 중) — 자리는 진행 기록에. `practiceSettings.serial`·`workData` 테스트는 새 `settingsSave`·`workDataSave` 옆으로(이름도 맞춤).
5. `src/lib/` 폴더 없어짐. `package.json` `test:unit`에서 `src/lib/**` 빼고 `src/shared/**`가 `test:app`에 이미 있는지 확인(`shared/utils` 테스트 생기면).
6. 옛 경로 grep 0건(`/lib/`). `domain/`과 같은 이름의 저장 파일 0쌍.
7. 테스트 수 그대로, 전부 통과. 빌드 결과 내용 같음(묶음 이름이 바뀌면 진행 기록에).
8. 주석 속 옛 이름(`domain/cars.js:3`의 "`lib/cars.js`가 재수출" 등)은 새 위치·사실에 맞게 고침. 테스트 문구 속 `loadCars`·`loadDrivers`·`loadProfile`("옛 방식이면")은 설명이라 그대로.

### 건드릴 파일
- 삭제 6(껍데기 5 + `cars.js`), 새 파일 7(`data/sync/*Save.js` — 옛 파일에서 함수를 옮긴 것, 가능하면 `git mv` 뒤 재수출 줄만 지워 기록 유지), 옮김(`git mv`) 도우미 11 + 테스트 약 12, 경로만 고침(부르는 파일 약 100). `package.json` 1줄.
- 함수 본문·문구는 안 바꿈. 가져오는 쪽은 경로·가져오는 출처만.

### 안 건드릴 것
- 앱 틀 정리(`app/routes`·`app/shell`, `components/`의 앱 틀 부품)와 위층 부르기 7곳 — 59-H2.
- 기능 사이 부르기 정리(59 끝), 92번(59-I).
- 저장 함수의 동작(91번 포함).

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 6 grep 0건을 진행 기록에.
- `git diff -M --stat`: 옮긴 파일이 이름 바뀜으로 잡히는지, 새 저장 파일 7개의 함수 본문이 옛 파일과 같은지(diff), 경로·출처 아닌 바뀐 줄 목록을 진행 기록에.
- 빌드 결과 내용 비교(이름 해시 뺌) — 차이를 진행 기록에.
- 개발 서버(비회원): 거래처·차량 추가, 일지 저장, 지출 추가 → 삭제, 앱 설정 다크 모드 켜고 끄기, 개인정보 상호 입력 → 지우기, 새로고침 뒤 그대로, 콘솔 오류 없음.

### 파일 길이
- 줄어들거나 그대로. 200줄 넘는 파일은 경로 줄만 — §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(위치·이름·출처만).
- 테스트가 하나라도 실패하면 멈추고 어느 경로·이름인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 오늘 일지 2회 저장 → 새로고침 뒤 그대로 → 되돌림.
2. 앱 설정 다크 모드 켜기 → 끄기(원래대로), 차량 유지비·서류 발급·매출 금액 그대로.
3. 콘솔 오류 없음.

### 확인할 점 — 답 받음 `[확인: 2026-10-20 보리 — "응 나눠"]`: 59-H를 H1(이 지시서)·H2(앱 틀·위층 부르기 7곳)로 나눔
1. 59-H를 H1(이 지시서: `lib/` 폴더 없애기, 약 130개 파일)과 H2(앱 틀 `app/routes`·`app/shell` + `components/`에 남은 앱 틀 부품 + 기능·데이터가 `app/`을 부르는 7곳 바로잡기)로 나눠도 될까요? **권함: 예** — H2는 7곳을 바로잡느라 파일 위치만이 아니라 부르는 방향을 바꾸는 일이라, 옮기기만 하는 H1과 섞으면 확인이 어렵습니다.
