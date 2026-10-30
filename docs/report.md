# docs/report.md — 현재 슬라이스 착수지시서

140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 138-A 기사 차량 "건당" 수수료 — 계산·표시에서 빼기 `[x]`

보리 "건당을 지우는게 낫지않아?", "응 그렇게 진행해"(2026-10-30), 전수점검 M2. 순서 137 → 139 → 140 → **138**. 두 조각: **이번엔 A(계산·표시)만**, B(차량 창·저장)는 다음. DB 변경 없음. 거래처 수수료의 "금액(건당)" 방식은 그대로.

### 쉬운 요약
- 기사 차량 수수료를 "운행 1건당 N원"으로 정하는 방식은 9월에 차량 창에서 빠졌는데, 계산과 화면 글씨는 아직 그 방식을 압니다.
- 이번엔 계산·화면에서 그 방식을 지웁니다. 옛날에 건당으로 저장된 기사 차량이 있으면 "수수료 미설정"(기사 몫 0원)으로 봅니다.
- 서버에는 건당 기사 차량이 0대라(읽기 조회 2026-10-30, 차량 16대 중 0) 회원 화면 숫자는 바뀌지 않습니다.

### 현재 상태 (코드 확인)
- 계산 `domain/financeCore.ts:111-116` `calculateDriverVehicleCommission` — 건당이면 `금액 × 운행 횟수`. 달력(`monthSettlement.ts:129`)·매출 기사 급여·기사 관리·기사 본인 화면(`driverSelfRevenue.ts:52`)이 모두 이 함수 하나를 씀(`driverShareBase.test.js` `fourScreens`).
- 달력 정산 줄 이름 `monthSettlement.ts:130-134` — 건당이면 "3456 차량 건당 5,000원".
- 달력 (i) 설명 `CalendarMonthSummary.jsx:24-28` `subCarCommInfo` — 줄 이름에서 "건당 …"도 떼어 냄. 같은 파일 `perTripSetting`(12-15줄)은 거래처 줄 설명이라 건당 금액이 맞음.
- 기사 본인 화면 이름 `driverSelfRevenue.ts:19` — 건당이 아닐 때만 "기사 정산(N%)".
- 차량 목록 `CarListItem.jsx:24-26` — 건당이면 ", 수수료 5,000원".
- 설명 주석 `revenueFormat.js:25` "매출제(건당·비율 미확정)".
- 서버 함수는 `comm_type`을 그대로 넘기기만 함(마이그레이션 0003·0024 등, 계산 없음).

### 목표 상태 (기대 동작)
1. 기사 차량이 옛 건당 자료(`commType: 'direct'`)면 기사 몫 0원 — 달력·매출 기사 급여·기사 관리·기사 본인 화면 네 곳 모두. 수수료가 비어 있는 차량과 같음.
2. 비율(%) 기사 차량 계산은 지금과 같음(금액·줄 이름 "3456 차량 10%").
3. 달력 정산 줄 이름·(i) 설명, 차량 목록, 기사 본인 화면 이름에 기사 차량 "건당"이 더는 나오지 않음. 옛 건당 차량은 차량 목록에 수수료 글씨 없음.
4. 거래처 수수료의 금액(건당) 방식, 달력 거래처 줄 (i) 설명(`perTripSetting`)은 그대로.
5. 월급제 기사 차량은 그대로(월급 금액).

### 건드릴 파일
- `src/domain/financeCore.ts` — `calculateDriverVehicleCommission`의 건당 계산 줄을 지우고, 첫 줄 조건에 `car.commType === 'direct'`를 더해 0원. 주석 최종 문구: `// 옛 건당 자료는 수수료 미설정으로 읽는다(기사 몫 0원).`
- `src/domain/monthSettlement.ts` — 줄 이름을 비율 한 가지로(`${차량} 차량 N%`). 옛 건당 차량은 금액 0이라 줄이 안 보임(`CalendarMonthSummary.jsx:103` `subCarComm > 0`).
- `src/features/calendar/ui/CalendarMonthSummary.jsx` — `subCarCommInfo` 정규식에서 `건당 |`를 빼고, 위 설명 주석을 `라벨 "3456 차량 10%" → 설정 부분만, 기사 몫 기준에 맞는 설명.`으로.
- `src/domain/driverSelfRevenue.ts` — 19줄 `car.commType !== 'direct' &&` 지움(옛 건당이면 금액 0이라 `shareAmount > 0`에서 걸러짐).
- `src/features/cars/ui/CarListItem.jsx` — 수수료 글씨를 `N%` 한 가지로, 옛 건당 차량은 글씨 없음.
- `src/features/revenue/ui/revenueFormat.js` — 주석 "매출제(건당·비율 미확정)" → "매출제(비율 미설정)".
- 테스트(기존 건당 테스트를 새 규칙으로 바꿈):
  - `domain/finance.test.js:146-150` — 옛 건당 차량은 운행이 있어도 0.
  - `domain/driverShareBase.test.js:50-54` — 옛 건당 차량은 네 화면 모두 0.
  - `domain/monthSettlement.test.js:131-162` — 건당 차량 금액 0.
  - `features/calendar/ui/CalendarMonthSummary.test.js:242-247` — 예시 줄 이름을 "3456 차량 10%"로.
  - `features/revenue/ui/OwnerMonthlyCards.driverSelf.test.js:24` — 테스트 이름만 "매출제(비율 미설정)".
  - 새 테스트 파일 `features/cars/ui/CarListItem.test.js`(지금 차량 목록 글씨 테스트 없음) — 옛 건당 차량은 수수료 글씨 없음, 비율은 ", 수수료 10%".

### 안 건드릴 것
- 차량 창·저장(`CarFormModal.jsx`·`CarListPage.jsx`·`carDriverFields.ts`) — 138-B. 그때 옛 건당 차량을 열면 수수료 빈 칸으로 보이게 함.
- 불러오기·저장 형식(`hydrateMergeCars.ts`·`hydrateEmployedDriverCar.ts`·`cloudStorage.ts`·`persistDomainRecords.ts`의 `COMM_TYPES`) — 거래처와 같이 쓰는 값이라 그대로. 서버 `comm_type` 칸도 그대로.
- 거래처 수수료(`clients.ts`·`ClientTradeFields.jsx`·`fixedRouteCommission.ts`·`reportDetail.js`·`monthSettlement.ts`의 거래처 줄), 운비서 거래처 등록.
- `finance.fixtures.ts`의 월급제 건당 차량 — 월급제는 월급 금액만 써서(`driverIncomeDeductions.ts:57-58`) 결과 같음.

### 검증
- `npm test`, 타입 검사, 코드 검사, 의존 검사, 빌드(CI).
- 바뀐 테스트(옛 건당 → 0, 차량 목록 글씨 없음)는 고치기 전 코드에서 실패하는지 확인(revert-check). 비율 계산은 안전 확인.
- 로컬 비회원 화면: 기사 차량(비율)을 만들어 달력 정산 줄·(i)·차량 목록 글씨가 그대로인지.
- 배포본: 아래 확인 방법.
- [x] 때 sot §4-21의 "건당 기사 몫 계산"·지난 139 문장 정리, 기사 차량 수수료는 비율만이라는 규칙을 기록.

### 진행 기록
- 2026-10-30 보리 "응"(이대로 진행) → 데스크톱 AI가 코드.
- 로컬: 테스트 949·925 모두 통과, 타입 검사·의존 검사(위반 0)·빌드 통과. **코드 검사 실패 1건** — `financeCore.ts:111` `calculateDriverVehicleCommission`의 운행 횟수 입력(`count`)이 건당 줄을 지우면서 안 쓰이게 됨("쓰지 않는 입력" 경고, `--deny-warnings`라 실패). 코드 커밋 전.

#### 수정 착수지시서 — 안 쓰는 운행 횟수 입력 지우기
- 원인: 운행 횟수는 건당 계산에만 쓰였음. 비율 계산은 금액만 봄.
- 고칠 것: `calculateDriverVehicleCommission(car, grossAmount)`로 입력을 둘로 줄이고, 부르는 곳의 세 번째 값을 지움.
  - `domain/driverRevenueShareExpense.ts:49`·`:79`, `domain/financeTaxInvoiceGroups.ts:144`, `domain/driverSelfRevenue.ts:52`, `domain/monthSettlement.ts:129`(이번에 이미 건드리는 파일 2개 + 새로 2개), 테스트 `domain/finance.test.js:148`.
- 동작은 그대로(값을 받기만 하고 안 쓰던 입력). `totals.count`·`trips`·`callTrips`는 다른 곳(계산서 운행 횟수 `financeTaxInvoiceGroups.ts:150`, 달력 카드)에서 계속 씀.
- 이름에 `_`를 붙여 경고만 끄는 방법은 안 씀 — 안 쓰는 입력이 남아 헷갈림.
- 파일 길이: `driverRevenueShareExpense.ts` 88 → 88, `financeTaxInvoiceGroups.ts` 180 → 180, 나머지 그대로.
- 검증: 위 검사 전부 다시, 고치기 전 실패 확인.
- 2026-10-30 보리 "응"(수정 착수지시서대로) → 고침. react-app `0be8c3b`(push 안 함).
- 로컬: 테스트 949·925 모두 통과, 타입 검사·코드 검사·의존 검사(위반 0)·빌드 통과. 파일 길이 188·147·173·93·53·36·88·180.
- 고치기 전 실패 확인 — 바꾼·새 테스트 중 3개(달력 "5. 서브차량 수수료"의 건당 0원, 네 화면 0원, 차량 목록 건당 글씨 없음)는 고치기 전 코드에서 실패. 나머지(비율 글씨, 달력 (i) 예시, 이름만 바꾼 테스트)는 안전 확인. **지시서와 다른 점**: `finance.test.js` "옛 건당 … 0"은 운행 횟수 입력을 지우면서 세 번째 값을 안 넘기게 돼 고치기 전 코드에서도 0으로 통과 — 0원 동작은 네 화면 테스트가 잡음. 되돌린 파일 8개 원래대로 복구 확인.
- 브라우저(로컬 5175, 비회원): 기사 차량 12가3456(5톤, 매출제 10%) 등록 → 차량 목록 "(5톤), 수수료 10%" → 그 차량 달력 정산 카드 오류 없음(운행 0이라 수수료 줄 없음 — 줄 이름·(i)는 자동 테스트), 콘솔 오류 없음 → 시험 차량 지움.
- push 뒤 CI verify·deploy 초록(run 41017213161).
- 2026-10-30 보리 "확정" → `[x]`, sot §4-23에 규칙 기록, §4-21 지난 문장 정리.
- 배포본(getdrivelog.com, 로그인 탭): 차량 관리 22가2222 "(25톤), 수수료 20%" 그대로 → 22가2222 달력 정산 카드 "2222 차량 20%" -161,800원(137 확인 때와 같음), (i) "차량 관리에서 설정한 이 차량의 수수료(20%)입니다. 운송료 전체에서 계산해 차감합니다." → 매출 기사 탭 기사 급여 -161,800원. 콘솔의 404는 주소로 바로 연 화면 문서 요청(배포 방식, 이번 수정과 무관).
- §5 리뷰: 바뀐 파일 14개 = 처음 지시서 12개 + 수정 착수지시서 2개(`driverRevenueShareExpense.ts`·`financeTaxInvoiceGroups.ts`), 새 파일은 지시서에 적은 `CarListItem.test.js`만, 새 저장 키·대비 장치·타입 꼼수·로드맵 번호 주석 없음(`CalendarMonthSummary.jsx`의 기존 "(로드맵 61)"은 지움), 지운 테스트 3개는 새 규칙으로 바꾼 것, 파일 길이 모두 200 이하, 목표 1~5 충족(1은 네 화면 테스트, 2는 배포본·테스트, 3은 테스트·로컬 목록, 4·5는 안 건드림·기존 테스트 통과).

### 파일 길이
- `financeCore.ts` 189 → 약 189(200줄 감시 목록), `monthSettlement.ts` 149 → 약 147, `CalendarMonthSummary.jsx` 173 → 173, `driverSelfRevenue.ts` 93 → 93, `CarListItem.jsx` 55 → 55, `revenueFormat.js` 36 → 36. 모두 줄거나 같음.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 계산·글씨만 줄임. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 배포본에서 기사 차량(비율 수수료)이 있는 계정으로 차량 관리를 엶 → 목록에 ", 수수료 N%"가 그대로.
2. 그 차량 운행일지 달력 → 정산 카드의 "기사 차량 수수료" 줄 금액·(i) 설명이 전과 같음.
3. 매출 → 기사 급여 금액이 전과 같음. (옛 건당 차량은 서버에 없어 화면에서 볼 일이 없음 — 0원 처리는 자동 테스트로 확인.)
