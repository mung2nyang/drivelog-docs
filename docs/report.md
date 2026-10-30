# docs/report.md — 현재 슬라이스 착수지시서

137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 139 운행 횟수에서 공차·연동 혼짐 빼기 — 기사 정산도 `[x]`

전수점검(2026-10-26) B3, 로드맵 ②. 근거 sot §4-21 "운행 횟수에 공차와 다른 운행에 묶인 혼짐(연동 혼짐)은 넣지 않는다 — … 기사 관리·기사 본인 화면·건당 기사 몫 계산 모두 같은 규칙" `[확인: 2026-10-26 보리 — "공차와 연동혼짐은 운행횟수에 넣지마"]`. 137(react-app `795dd6e`)과 같은 함수의 다른 값(`count`). 순서는 보리 결정으로 137 → 139 → 140 → 138(2026-10-30). DB 변경 없음.

### 쉬운 요약
- 달력·홈·매출은 운행 횟수를 셀 때 공차와 다른 운행에 묶인 혼짐을 빼는데, 기사 정산 계산만 콜마다 1회로 셉니다.
- 그래서 기사 관리 화면 "기사 정산 N건"이 달력의 "총 N회 운행"보다 많게 나올 수 있습니다.
- 기사 정산도 같은 규칙으로 세어 숫자를 맞춥니다. 금액(운송료·기사 몫 %)은 바뀌지 않습니다.

### 현재 상태 (코드 확인)
- `domain/financeCore.ts:96-104` `getMonthlyDriverTotals` — 콜 상세마다 `count += 1`(`:102`), `distanceType`을 안 봄.
- 정본 규칙 `domain/day-record.ts:73-89` `countCallTrips` — 공차 0, 혼짐은 `linkedLoadIndex`가 `'pending'`·`'-1'`·없음일 때만 1, 나머지 1. 하루 기록 단위라 콜마다 다른 `workDate`·계약 기간 거르기를 못 해서 그대로는 못 씀.
- 같은 규칙을 쓰는 곳: 달력 `monthSettlement.ts:78`, 매출 `financeOwnerDetail.ts:72-78`·`financeCore.ts` `getMonthlyFareRevenue`(`:153-163`, 같은 조건을 따로 적어 둠).
- `count`를 쓰는 곳(grep `totals.count`): 기사 관리 `financeTaxInvoiceGroups.ts:150` `tripCount` → `SettlementSummaryCard.jsx:64` "기사 정산 N건", 그리고 `calculateDriverVehicleCommission`(`financeCore.ts:111-116`, 건당 차량일 때만 횟수를 씀 — 서버 건당 차량 0대, 138에서 지울 예정) 3곳(`driverRevenueShareExpense.ts:49`·`:79`, `driverSelfRevenue.ts:52`, `financeTaxInvoiceGroups.ts:144`).
- 기사 본인 화면 운행 횟수는 `getOwnerMonthlyFinanceDetail`(`driverSelfRevenue.ts:35`)에서 와서 이미 규칙대로.

### 목표 상태 (기대 동작)
1. `getMonthlyDriverTotals`의 `count`: 공차 0, 다른 운행에 묶인 혼짐 0, 묶일 운행을 안 고른 혼짐(`pending`·`-1`·없음) 1, 그 밖 1. 고정 노선 횟수는 지금처럼 더함.
2. 날짜·`workDate`·계약 기간 거르기, `grossAmount`·`insuranceAmount`는 그대로(공차 운송료가 있으면 금액에는 지금처럼 들어감).
3. 기사 관리 "기사 정산 N건"이 같은 달 그 차량 달력 "총 N회 운행"과 같은 규칙.
4. 건당 차량(옛 자료)의 기사 몫도 같은 횟수로.
5. `countCallTrips` 결과는 그대로(달력·홈·매출 숫자 안 바뀜, 기존 테스트 그대로 통과).

### 건드릴 파일
- `src/domain/day-record.ts` — 콜 한 건 판단을 작은 함수 `callCountsAsTrip(detail)`로 꺼내고 `countCallTrips`가 그것을 씀(결과 같음). 주석 최종 문구: `/** 콜 한 건이 운행 횟수에 들어가는지 — 공차·다른 운행에 묶인 혼짐은 빼고, 묶일 운행을 안 고른 혼짐은 넣는다. */`
- `src/domain/financeCore.ts` — `:102` `count += 1`을 `if (callCountsAsTrip(detail)) count += 1`로, `:12` 불러오기 줄에 `callCountsAsTrip` 추가.
- `src/domain/finance.test.js` — 새 테스트: 편도 1·공차 1·연동 혼짐 1·안 묶인 혼짐 1인 날 → `getMonthlyDriverTotals` `count` 2(금액은 넷 다 더함), 기사 관리 `getLinkedDriverSettlementDetail` `tripCount`가 달력 `monthSettlementSummary`의 `trips + callTrips`와 같음.

### 안 건드릴 것
- `financeOwnerDetail.ts:72-78`·`getMonthlyFareRevenue`(`financeCore.ts:153-163`)의 같은 조건 — 이미 규칙대로 셈(sot §4-21 "지금 기사 정산만" 다름), 합치기는 이번 범위 밖.
- `calculateDriverVehicleCommission`의 건당 계산 — 138(건당 지우기)에서 다룸.
- 화면 파일(`SettlementSummaryCard.jsx`), DB·서버 함수.
- 순환 불러오기: `financeCore.ts` → `day-record.ts` → `finance.ts`가 새로 생기지만, 같은 모양이 이미 있고(`financeTaxInvoiceGroups.ts:9` → `day-record.ts:4` → `finance.ts:11`) 함수 안에서만 부르므로 동작 문제 없음. 의존 검사 설정에 순환 규칙 없음(`.dependency-cruiser.cjs` grep `circular` 0).

### 검증
- `npm test`, 타입 검사, 코드 검사, 의존 검사, 빌드(CI).
- 플레이북 열람(`domain/finance*` 해당) — 계산만, 저장·동기화 안 바뀜, §6 고치기 전 실패 확인.
- 새 테스트가 고치기 전 코드에서 실패하는지 확인(revert-check). `countCallTrips` 기존 테스트는 고치기 전·뒤 모두 통과(목표 5).
- 배포본: 아래 확인 방법.

### 진행 기록
- 2026-10-30 보리 "착수지시서 확정, 작업 진행해" → 데스크톱 AI가 코드. react-app `d397e8f`(push 안 함).
- 지시서와 다른 점: `countCallTrips`를 `filter(callCountsAsTrip).length`로 줄여 `day-record.ts`가 181 → 177줄(늘지 않고 줆). `financeCore.ts`는 불러오기를 `import { callCountsAsTrip, type DayRecordLike }` 한 줄로 합쳐 189줄 그대로.
- 로컬: 테스트 945·922 모두 통과, 타입 검사·코드 검사·의존 검사(위반 0)·빌드 통과.
- 고치기 전 실패 확인 — 새 테스트 2개 모두 고치기 전 코드에서 실패(둘 다 실제 4 / 기대 2). 되돌린 파일 2개 원래대로 복구 확인.
- 브라우저: 기사 관리는 연동 기사(로그인 계정)만 있어 로컬 비회원으로 못 봄 — 배포본은 push 뒤 확인.
- push(보리) 뒤 CI verify·deploy 초록(run 41004167704).
- 배포본(getdrivelog.com, 로그인 계정, 데스크톱 AI): 10월 22가2222 기사 관리 "기사 정산 7건"·기사 정산금 161,800원 = 그 차량 달력 "총 7회 운행". 이 달 자료에 공차·묶인 혼짐이 없어 건수는 전과 같음 — 빼는 동작은 자동 테스트로 확인(목표 확인 방법 2).
- §5 리뷰: ① 범위 — 바뀐 파일 3개, 지시서와 같음 ② 새 파일·저장 키·장치 없음 ③ 타입 꼼수 새로 없음(diff grep 0) ④ 200줄 아래(177·189) ⑤ 테스트는 추가만, 지운 줄 0, 주석에 로드맵 번호 없음 ⑥ `countCallTrips` 쓰는 곳(`day-record.ts:91`·`:116`, `monthSettlement.ts:78`)은 결과 같음 — 기존 테스트 통과 ⑦ 목표 1·2·3은 새 테스트, 4는 같은 `count`를 씀, 5는 기존 테스트.
- 2026-10-30 보리 "확정" → `[x]`.

### 파일 길이
- `day-record.ts` 181 → 약 184, `financeCore.ts` 189 → 약 189. 둘 다 200줄 감시 목록 — 넘으면 §6 분리설계 먼저 보고.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 계산만 바꿈. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법 (배포본, 로그인 계정)
1. 연동 기사 차량 일지에 공차나 묶인 혼짐 콜이 있는 달이면: 옆 메뉴 → 그 기사 관리 → "기사 정산 N건"이 그 차량 달력 아래 "총 N회 운행"과 같음.
2. 그런 콜이 없는 달이면 건수가 지금과 같은지만 확인(나머지는 자동 테스트로).

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
