# docs/report.md — 현재 슬라이스 착수지시서

136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 137 기사 급여·산재 차주 몫 계산 `[x]`

전수점검(2026-10-26) B2·B4, 로드맵 ②(돈·세무 금액). 근거 sot §4-12 "달력·매출 기사 급여·기사 관리·연동 기사 본인 매출이 모두 `driverShareBase` 한 곳으로 같은 금액"과 sot 296줄 "미연동 기사 거래처는 그 차량 태그로 저장". "계약 밖 운송료" 문구 계산도 같은 원인이라 같이 `[확인: 2026-10-30 보리 — "응 같이넣어서"]`. DB 변경 없음.

### 쉬운 요약
- 매출 화면(전체·기사 탭)의 "기사 급여"가 미연동 기사 차량이면 그 차량 전용 고정 노선 단가 대신 차주 단가로 계산됩니다. 달력 정산과 금액이 다릅니다.
- 같은 화면의 "산재보험(차주 부담)"은 차량에 정한 "기사 몫 기준(수수료 뗀 뒤)"을 무시하고 운송료 전체로 계산합니다. 기사 관리·기사 본인 화면과 금액이 다릅니다.
- 둘 다 고치면 매출 화면 기사 급여·산재·"계약 밖 운송료" 문구가 달력·기사 관리와 같은 기준이 됩니다. 화면 모양은 그대로입니다.

### 현재 상태 (코드 확인)
- 기사 급여 합계: `domain/financeCore.ts:79-86` `getMonthlyDriverTotals`가 고정 노선 거래처를 `link?.vehicleNumber`로 찾음(`getFixedRouteClient`, `clients.ts:12-20` — 키가 없으면 차주 것). 미연동 차량은 `link`가 `null`이라 차주 단가.
- 거래처 수수료 합: `domain/driverShareBase.ts:23-25` `getMonthlyDriverClientCommission`도 같은 방식.
- 달력은 차량 번호로 찾음: `data/store/useMonthSettlement.ts:24` `fixedScopeKey = clientScopeKey || logId`.
- 매출 기사 급여: `domain/driverRevenueShareExpense.ts:46-48` 위 두 함수를 `link`로 부름. `:52` "계약 밖 운송료"는 `link` 자리에 `null`을 넘겨 다시 계산 → 연동 차량도 차주 단가로 계산한 값에서 전용 단가 합계를 빼서 금액이 틀림.
- 산재 차주 몫: 같은 파일 `:76-77` `getMonthlyDriverInsuranceOwnerShare`가 `driverShareBaseAmount`를 거치지 않고 `totals.grossAmount`로 기사 몫 계산(같은 파일 `:48`과 `driverSelfRevenue.ts:51`·`financeTaxInvoiceGroups.ts:143`은 거침).
- 부르는 곳(grep `getMonthlyDriverRevenueShareExpense`·`getMonthlyDriverInsuranceOwnerShare`, 운영 코드): `financeOwnerDetail.ts:106-107` 하나, `scope !== 'owner'`일 때만 → 매출 화면 `features/revenue/ui/OwnerRevenueView.jsx:72`·`:77`(달·연). 운비서(`unbiseoNumbers.ts:59`)는 `'owner'`라 이 두 함수를 안 부름.
- 예(전수점검 기록): 전용 100,000원·차주 250,000원·고정 1회·기사 10% → 매출 기사 급여 25,000원, 달력 10,000원. 운송료 100만원·거래처 수수료 10%·기사 30%·"수수료 뗀 뒤"·산재 켬 → 기사 몫 27만원인데 산재 차주 몫은 30만원 기준 1,877원(맞는 값 1,689원).

### 목표 상태 (기대 동작)
1. 매출 화면 기사 급여: 미연동 기사 차량의 고정 노선 금액·거래처 수수료가 그 차량 전용 거래처 기준(없으면 지금처럼 차주 것). 달력 정산 기사 몫과 같은 금액.
2. 매출 화면 산재 차주 몫: 기사 급여와 같은 기준 금액("기사 몫 기준")으로 계산. 기사 관리 정산 카드·기사 본인 화면과 같은 금액.
3. "계약 밖 운송료 N원 제외" 문구: 같은 차량 단가로 계산해, 계약 기간 안 운행만 있으면 문구가 붙지 않음.
4. 연동 차량·전용 거래처가 없는 차량·"운송료 전체" 기준 차량은 금액 그대로(기존 테스트 그대로 통과).
5. 기사 관리 화면(`getLinkedDriverSettlementDetail`)·기사 본인 화면(`driverSelfRevenue.ts`)은 지금처럼 연동 정보의 차량 번호로 찾음 — 결과 그대로.

### 건드릴 파일
- `src/domain/financeCore.ts` — `getMonthlyDriverTotals` 끝에 선택 인자 `scopeKey` 추가, 거래처 찾기·단가에 `scopeKey || link?.vehicleNumber`. 주석 최종 문구: `// scopeKey: 고정 노선 거래처를 찾을 차량 번호(없으면 연동 정보의 차량 번호).`
- `src/domain/driverShareBase.ts` — `getMonthlyDriverClientCommission`에 같은 선택 인자, 같은 주석.
- `src/domain/driverRevenueShareExpense.ts` — `:47`·`:48`·`:52`에 `car.number`를 넘김. 산재 차주 몫(`:76-77`)은 `:47-49`와 같이 `driverShareBaseAmount`를 거친 기준 금액으로 기사 몫 계산(`car.number`도 넘김).
- `src/domain/driverRevenueShareExpense.test.js` — 새 테스트: ① 미연동 차량 전용 단가로 기사 급여(위 예 10,000원), 달력 `monthSettlementSummary` 기사 몫과 같음 ② "수수료 뗀 뒤" 기준 산재 차주 몫(위 예 1,689원), 기사 관리 `getLinkedDriverSettlementDetail` 기준 금액과 같음 ③ 연동 차량 전용 단가 + 계약 시작 전 운행 1회 → "계약 밖 운송료" 금액이 전용 단가 1회분.

### 안 건드릴 것
- `driverSelfRevenue.ts`·`financeTaxInvoiceGroups.ts`(`getLinkedDriverSettlementDetail`·`flattenLinkedDriverTrips`) — 연동 기사만 다루고 연동 정보의 차량 번호로 찾음(목표 5), 새 인자를 안 넘김.
- `financeOwnerDetail.ts`·`OwnerRevenueView.jsx` — 같은 함수를 불러 따라 바뀜(위 grep).
- 운행 횟수에서 공차·연동 혼짐 빼기(로드맵 139, 같은 `getMonthlyDriverTotals`의 `count`) — 다음 슬라이스.
- 산재 계산식(`driverIncomeDeductions.ts`), 화면 모양, DB·서버 함수.

### 검증
- `npm test`, 타입 검사, 코드 검사, 빌드(CI).
- 플레이북 열람(`domain/finance*` 해당) — 계산만 바뀌고 저장·동기화는 안 바뀜, §6 고치기 전 실패 확인 적용.
- 새 테스트 ①②③이 고치기 전 코드에서 실패하는지 확인(revert-check).
- 배포본: 아래 확인 방법.

### 진행 기록
- 2026-10-30 보리 "너가 진행해, 확정, 진행" → 데스크톱 AI가 코드. react-app `795dd6e`(push 안 함).
- 지시서와 다른 점: 새 테스트 ①에 "수수료 뗀 뒤" 기준 경우를 한 번 더 확인(전용 거래처 수수료 10% → 9,000원, 달력과 같음) — `getMonthlyDriverClientCommission`의 차량 번호 찾기까지 확인하려고.
- 로컬: 테스트 943·922 모두 통과, 타입 검사·코드 검사·빌드 통과. 파일 길이 189·41·88.
- 고치기 전 실패 확인 — 새 테스트 3개 모두 고치기 전 코드에서 실패: ① 실제 25,000 / 기대 10,000, ③ "계약 밖 운송료 400,000원" / 기대 100,000원, ② 실제 1,877 / 기대 1,689. 되돌린 파일 3개 원래대로 복구 확인.
- 브라우저: 로컬은 비회원이라 기사 차량·전용 거래처 자료 없음 — 배포본은 push 뒤 확인.
- push(보리) 뒤 CI verify·deploy 초록(run 41000138269).
- 배포본(getdrivelog.com, 로그인 계정, 데스크톱 AI): 10월 22가2222(기사 "기사연동", 연동 중 계약 아님 — 매출 줄 이름에 기간 없음, 전용 고정 노선 거래처 "테스트") — 매출 [기사] 탭 기사 급여 161,800원 = 그 차량 운행일지 달 정산 "2222 차량 20%" 161,800원 = 기사 관리 기사 정산금 161,800원. 산재는 이 차량에서 꺼져 있고 전용 거래처 수수료도 없어 목표 2는 자동 테스트로만 확인(설정은 바꾸지 않음).
- §5 리뷰: ① 범위 — 바뀐 파일 4개, 지시서 "건드릴 파일"과 같음 ② 새 파일·저장 키·장치 없음 ③ `any`·`unknown`·`@ts-ignore` 새로 없음(diff grep 0) ④ 200줄 아래(189·41·88) ⑤ 테스트는 추가만, 지운 줄 0 ⑥ 새 선택 인자 — 부르는 곳 전부 grep, 넘기는 곳은 `driverRevenueShareExpense.ts`뿐, 나머지는 지금처럼 ⑦ 목표 1·3·4는 테스트와 배포본, 2는 테스트, 5는 부르는 쪽 안 바뀜(`driverSelfRevenue.ts`·`financeTaxInvoiceGroups.ts` diff 없음).
- 2026-10-30 보리 "확정" → `[x]`.

### 파일 길이
- `financeCore.ts` 187 → 약 189(200줄 감시 목록 — 넘으면 §6 분리설계 먼저 보고), `driverShareBase.ts` 39 → 약 41, `driverRevenueShareExpense.ts` 86 → 약 88. 모두 200줄 아래.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 계산만 바꿈. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법 (배포본, 로그인 계정)
1. 미연동 기사 차량에 그 차량 전용 고정 노선 거래처(차주 것과 다른 단가)가 있는 계정이면: 매출 → [전체] 또는 [기사] 탭 → 그달 지출 "기사 급여"의 그 기사 줄 금액이 달력(그 차량) 월 정산의 기사 몫과 같음.
2. 연동 기사 차량 창에서 "기사 몫 기준"을 "거래처 수수료 뗀 뒤"로, 산재 켬 → 매출 "산재보험(차주 부담)" 줄이 기사 관리 정산 카드의 산재 공제 줄(기사 몫)과 같거나 1원 많음(총액이 홀수면 남는 1원은 차주 몫, `driverIncomeDeductions.ts:70`·`:77`) → 설정 되돌림.
3. 해당하는 자료가 없는 계정이면 금액이 그대로인지만 확인(나머지는 자동 테스트로).

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
