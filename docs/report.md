# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 40-A — 콜 상세 산재보험료를 달력·홈·운송비 내역서 합계에서도 뺀다 `[x]`

> **보리 답(2026-10-13):** 질문 1 — 추천과 달리 **내역서에서도 뺀다**. 이어서 확인: **요약·세부 내역서 둘 다** "산재보험료 - X원" 줄을 넣고 합계에서 뺀다. 착수 승인.
> 그래서 아래 계획이 이렇게 바뀐다: `invoiceTotal`은 만들지 않음(내역서도 `total` 그대로), `reportSummary.js`는 `insuranceTotal`만 넘김. 건드릴 파일 추가 — `components/ReportSummaryContent.jsx`(192→약 198, 줄 추가), `lib/reportDetail.js`(세부 내역서 산재 합·`grandTotal`에서 뺌, 거래처 필터 따름), `components/ReportDetailView.jsx`(171→약 177, 줄 추가). 목표 5 → "요약·세부 내역서도 산재 줄을 보이고 합계에서 뺀다". 주석의 "내역서는 빼지 않은 invoiceTotal" 문구는 쓰지 않는다.
> **작업 중 바꾼 것:** `callInsuranceTotal`은 `domain/callDetail.js` 대신 `domain/call-details.js`(134→141)에 넣음 — `callDetail.js`는 "타입만, 런타임 코드 없음" 파일이라.
> 결과: `npm test` 852+370 통과, `typecheck` 통과. 테스트 4개 추가(달력 산재 빼기·0이면 그대로, 요약·세부 내역서). 줄 수: `CallDetailList.jsx` 82, `monthSettlement.js` 160, `CalendarMonthSummary.jsx` 166, `reportSummary.js` 119, `reportDetail.js` 122, `ReportSummaryContent.jsx` 199, `ReportDetailView.jsx` 178(모두 200 이하).
> AI 개발 서버 대조(비회원, 10/20 콜 상세 12,345원에 산재 1,000원): 하루 합계 113,580→112,580원, 달력 "산재보험료 - 1,000원" 줄·합계 662,580원, 홈 662,580원, 요약 내역서 같은 줄·계 662,580원, 세부 내역서 같은 줄·계 112,580원.
> 코드 커밋 react-app `c07ff46`(push 전). 보리 push → CI 초록(verify·deploy) → §5 리뷰 → 보리 최종 승인(2026-10-13). 다음 40-B(매출).

외부 검수(2026-10-12) 결과, 급한 순서 ②(돈 금액), 재현됨. 근거: `docs/roadmap.md` 40번.
보리 결정(2026-10-13): **콜 상세 "산재보험료"는 모든 화면에서 뺀다**(플랫폼이 실제로 떼고 주는 돈으로 봄).
고칠 곳이 계산 3곳·화면 3곳이라 한 번에 하면 크다 → **40-A(하루 화면 계산 정리 + 달력·홈)**, **40-B(매출)** 두 개로 나눈다. **이번엔 40-A만.**

### 쉬운 요약
- **무엇을:** 콜 상세에 적은 산재보험료를 달력 월간 정산과 홈 정산 합계에서도 뺀다. 달력에는 "산재보험료" 줄이 새로 보인다(0원이면 숨김).
- **왜:** 지금은 하루 화면만 빼서, 1,000원을 적으면 하루 합계와 홈 합계가 1,000원 다르다.
- **달라지는 점:** 하루 화면·달력·홈 합계가 같아진다. 매출은 다음 슬라이스(40-B)에서 맞춘다.

### 현재 상태 (코드 확인)
- 하루 화면: `components/day-log/CallDetailList.jsx:33-34`가 화면 안에서 직접 산재보험료 합을 내고 합계에서 뺀다(`grandTotal = 운송료 - 수수료 - 산재 + 부가세`). 로드맵 60번 메모: 이 계산은 40번 때 `domain/`으로 옮긴다.
- 달력·홈: `domain/monthSettlement.js` `total = 운송료 + 파렛트 - 수수료 - 기사 차량 수수료 + 부가세` — 산재보험료를 안 본다. 홈 정산 카드(`HomeMonthCard.jsx`)는 이 `total`을 그대로 보여 준다.
- 운송비 내역서 요약: `lib/reportSummary.js:116 total: settled.total` — 같은 `total`을 쓴다. 그래서 그대로 고치면 회사에 내는 내역서 합계에서도 산재가 빠진다(아래 질문 1).
- 콜 상세 산재 칸은 기사연동 계정에선 숨김(`CallDetailForm`, 4-2 슬라이스 3). 저장 값은 `detail.insuranceFee`(문자열 금액).
- `day-record.js`는 199줄이라 함수를 더 넣을 수 없다. `domain/callDetail.js`는 44줄.
- 기사차량 매입 세금계산서(`financeTaxInvoiceGroups.js:141`)는 이미 산재를 따로 뺀다 — 이번 범위 아님.

### 목표 상태 (기대 동작)
1. 하루 날짜 하나의 콜 상세 산재보험료 합을 내는 함수 `callInsuranceTotal(record)`를 `domain/callDetail.js`에 둔다(휴무 날은 0). 하루 화면이 이 함수를 쓴다. 하루 화면 금액은 지금과 같다.
2. 달력 월간 정산에 "산재보험료 - X원" 줄이 생기고(0원이면 숨김), 합계에서 그만큼 뺀다.
3. 홈 정산 합계도 같은 값으로 줄어든다(달력 `total`을 그대로 씀).
4. 같은 날·같은 데이터에서 하루 화면 합계(그날)와 달력 합계(그 달)의 산재보험료 차감액이 같다.
5. 운송비 내역서는 질문 1의 답대로 한다(추천: 그대로 — 산재를 빼지 않은 금액).
6. 산재보험료를 안 적은 달은 모든 금액이 지금과 같다.

### 예상 화면 (달력 월간 운송료 정산 카드, 새 줄만 추가)
```
고정시험 기본 운송료             550,000원
고정시험 수수료 (10%)           - 55,000원
산재보험료                       - 1,000원   ← 새 줄(0원이면 안 보임)
부가세 (공급가액 기준 10%)        56,235원
합계                            550,235원   ← 산재만큼 줄어듦
```
줄 모양·글씨는 바로 위 "수수료" 줄과 같은 형식(`summary-row`, 금액 앞 "- ").

### 건드릴 파일
- `react-app/src/domain/callDetail.js` — `callInsuranceTotal(record)` 추가(약 +8줄).
- `react-app/src/components/day-log/CallDetailList.jsx` — `:33` 직접 계산을 위 함수로.
- `react-app/src/domain/monthSettlement.js` — 날마다 `callInsuranceTotal` 합 → `insuranceTotal` 반환, `total`에서 뺌. 내역서용으로 산재를 빼지 않은 `invoiceTotal`도 반환(질문 1을 추천대로 할 때).
- `react-app/src/components/calendar/CalendarMonthSummary.jsx` — "산재보험료" 줄 추가(위 시안).
- `react-app/src/lib/reportSummary.js` — 질문 1 추천대로면 `total: settled.invoiceTotal`(1줄).
- 테스트: `monthSettlement.test.js`(산재 빼기·0이면 같음), `callDetail` 함수 테스트, 내역서 테스트(`reportSummary.test.js`) 1개.
- 공용 CSS 변경 없음(기존 `summary-row` 그대로 씀).

### 안 건드릴 것
- 매출(`financeOwnerDetail.js`·`OwnerMonthlyCards.jsx`) — **40-B**에서.
- 기사차량 산재·3.3%(sot §4-12 월 단위 규칙)·매입 세금계산서 — 다른 규칙, 그대로.
- `day-record.js`(199줄) — 함수 추가 안 함.
- 홈 화면 파일 — 받은 `total`만 보여 준다.
- 플레이북: 계산·표시만(저장 경로 아님) → 참고만.

### 검증
- `npm test` — 새 테스트, 기존 달력·내역서 테스트 그대로 통과.
- 개발 서버(비회원): 콜 상세 하나에 산재보험료 1,000원 입력 → 하루 화면 합계·달력 합계·홈 합계에서 똑같이 1,000원 빠지는지, 달력에 "산재보험료 - 1,000원" 줄이 보이는지, 내역서 합계는 질문 1 답대로인지.

### 파일 길이
- `callDetail.js` 44 → 약 52
- `CallDetailList.jsx` 82 → 약 82
- `monthSettlement.js` 156 → 약 162(파일 머리 §6 ~210 허용)
- `CalendarMonthSummary.jsx` 160 → 약 166
- `reportSummary.js` 118 → 118
- 테스트 파일은 예외

넣을 주석(최종 문구):
- `callDetail.js`: `// 콜 상세 산재보험료(1인 차주가 건마다 적는 칸) 하루 합 — 하루 화면·달력이 같이 쓴다(로드맵 40, 모든 화면에서 뺀다).`
- `monthSettlement.js`: `// 콜 상세 산재보험료는 합계에서 뺀다(로드맵 40). 운송비 내역서는 회사 청구용이라 빼지 않은 invoiceTotal을 쓴다.`

### 실패 시 처리
- 새 저장 장치·재시도는 해당 없음(계산·표시만). 테스트·대조 결과가 목표와 다르면 멈추고 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법
1. 아무 날 일지에 콜 상세 하나를 열고 산재보험료 1,000원 입력 후 저장.
2. 하루 화면 "세부 내역 합계"가 1,000원 줄었는지 → 달력 아래 "산재보험료 - 1,000원" 줄과 합계 → 홈 정산 합계가 같이 1,000원 줄었는지.
3. 서류 발급 → 운송비 내역서 합계는 질문 1 답대로(추천: 그대로).
4. 시험 입력은 지운다.

### 확인할 점 (보리 답 필요)
1. **운송비 내역서(회사에 내는 서류)에서도 산재보험료를 뺄까요?** 내역서는 회사에 운송료를 청구하는 서류라, 플랫폼이 기사에게서 떼는 산재보험료는 회사와 상관없는 돈이에요. 그래서 **내역서는 빼지 않는 걸 추천**해요.
