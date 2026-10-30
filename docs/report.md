# docs/report.md — 현재 슬라이스 착수지시서

143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 운송비 내역서에서 산재보험료 빼기 `[x]`

- 2026-10-30 보리 지시: "정산카드에 운송내역서만 산재보험료가 안보이게 바꿔. 운송회사에 보내는건데 왜 산재보험료를 넣는거야."
- 로드맵 번호 없이 바로 지시한 일이다. 143 다음, 147 앞.

### 쉬운 요약
- 운송비 내역서는 운송회사에 보내는 영수증이다. 그런데 지금은 콜 상세에 적은 산재보험료가 "산재보험료 - N원" 줄로 나오고, 맨 아래 "계"에서도 빠진다.
- 운송비 내역서에서만 이 줄을 없애고, "계"에서도 빼지 않는다.
- 달력·홈·매출·하루 화면은 지금처럼 산재보험료 줄을 보여 주고 합계에서 뺀다.

### 현재 상태 (코드 확인)
- 요약 보기
  - `features/documents/api/reportSummary.js:118`이 `insuranceTotal`을 넘긴다. `total`은 달력과 같은 `monthSettlementSummary`의 합계라서 산재를 이미 뺀 값이다(`domain/monthSettlement.ts:130` `total = 운송료 + 파렛트 - 수수료 - 기사 차량 수수료 - 산재 + 부가세`).
  - 그 값을 `ui/ReportMonthSummaryCard.jsx:70-75`이 "산재보험료" 줄로 그린다.
- 세부 내역 보기
  - `api/reportDetail.js:104`가 산재를 더한다.
  - `:120`이 `grandTotal = 운송료 - 수수료 - 산재 + 부가세`로 계산한다.
  - `ui/ReportDetailView.jsx:160-165`이 줄을 그린다.
- 현재 sot: §4-12(2026-10-13 보리 확인, 로드맵 40)는 "콜 상세 산재보험료는 모든 화면 합계에서 뺀다(… 요약·세부 운송비 내역서 …)"라고 되어 있다. 이번 지시로 **운송비 내역서만 이 규칙에서 빠진다**. 문서 커밋 때 sot를 고친다.
- 기사 차량 수수료도 같은 이유로 이미 내역서에 넣지 않는다(sot §0 "운송내역서는 회사 제출용 영수증").
- 참고: 지금 세금계산서는 콜 상세 산재를 쓰지 않는다(sot §4-12 "기사 매입 계산서 삭제로 그 쓰임은 없어짐"). 이번에는 세금계산서를 바꾸지 않는다.

### 목표 상태 (기대 동작)
1. 운송비 내역서 요약(화면·PDF·이미지·공유)의 "월간 운송료 정산" 카드에 "산재보험료" 줄이 없다.
2. 그 카드의 "계"는 산재보험료를 빼지 않는다. 예: 운송료 150,000원 + 부가세 15,000원이면, 산재 1,500원이 있어도 계는 165,000원이다.
3. 세부 내역 보기(거래처 선택)도 같다. 줄이 없고, 계에서 빼지 않는다.
4. 달력 정산 카드·홈·매출·하루 화면(콜 목록 합계)·세금계산서는 바뀌지 않는다. 메인 차량에 산재가 있는 달은 내역서 "계"가 달력 "합계"보다 산재만큼 크다(기사 차량 수수료와 같은 종류의 차이).
5. 부가세·거래처 수수료·파렛트 줄과 금액은 그대로다.

### 건드릴 파일
- `react-app/src/features/documents/api/reportSummary.js`
  - `insuranceTotal`을 넘기지 않는다.
  - `total: settled.total + settled.insuranceTotal`로 바꾼다(달력 합계에서 뺀 산재를 되돌림).
  - 주석: `// 운송비 내역서는 회사 제출용이라 콜 상세 산재보험료를 줄·계에 넣지 않는다.`
- `react-app/src/features/documents/api/reportDetail.js`
  - 산재를 더하지 않는다. `insuranceTotal` 반환을 뺀다.
  - `grandTotal = 운송료 - 수수료 + 부가세`로 바꾼다.
  - 기존 `// 콜 상세 산재보험료도 뺀다(로드맵 40)` 주석 줄을 지운다.
- `react-app/src/features/documents/ui/ReportMonthSummaryCard.jsx` — 산재보험료 줄과 형식 설명의 `insuranceTotal`을 지운다. 첫 줄 주석은 `… 거래처별 운송료·파렛트·부가세·계.`로 바꾼다.
- `react-app/src/features/documents/ui/ReportDetailView.jsx` — 산재보험료 줄과 형식 설명의 `insuranceTotal`을 지운다.
- `react-app/src/features/documents/ui/ReportSummaryContent.jsx` — 형식 설명의 `insuranceTotal` 한 줄을 지운다.
- 테스트: `react-app/src/features/documents/api/report.test.js`의 기존 "산재보험료를 줄로 보이고 합계에서 뺀다" 2개를 반대로 바꾼다.
  - 요약·세부 모두 `insuranceTotal`이 없다.
  - 계는 `150000 + 15000`이다.
  - 한진만 고르면 `100000 + 10000`이다.

### 안 건드릴 것
- `domain/monthSettlement.ts`(달력·홈 합계), `features/day-log/ui/CallDetailList.jsx`(하루 콜 합계), 매출, `domain/financeTaxInvoiceGroups.ts`(세금계산서), 콜 상세 입력 칸 — 모두 지금처럼 산재를 보이고 뺀다.
- 기사 차량 월 단위 산재(§4-12 위쪽 규칙, 기사 정산) — 관련 없다.
- DB·서버는 바꾸지 않는다.

### 검증
- 자동 검사: `npm test`, `npm run typecheck`, `npm run lint`, `npm run depcheck`, `npm run build`.
- 고치기 전 실패 확인: 바꾼 테스트 2개를 고치기 전 코드로 돌리면 실패해야 한다(계에 산재가 빠져 있으므로).
- 로컬 화면(손님 모드): 콜 상세에 산재 1,000원이 있는 날을 임시로 넣는다.
  - 내역서 요약·세부에 "산재보험료" 줄이 없고 계가 산재만큼 큰지 본다.
  - 달력 정산 카드에는 줄이 그대로 있는지 본다.
  - 확인 뒤 원래대로 되돌린다.
- 배포본: seed 10월(산재 1,000원 있음)에서 읽기만 한다.
  - 내역서 계 3,349,010원, 줄 없음.
  - 달력 합계 3,348,010원, 줄 있음.

### 파일 길이
- `reportSummary.js`: 123 → 약 124
- `reportDetail.js`: 122 → 약 119
- `ReportMonthSummaryCard.jsx`: 86 → 약 79
- `ReportDetailView.jsx`: 177 → 약 170
- `ReportSummaryContent.jsx`: 149 → 148

### 실패 시 처리
- 새 저장 장치·재시도 장치는 만들지 않는다.
- 검사가 실패하면 고치지 않고 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법
- 서류 발급 → 운송비 내역서 [전체], 10월을 연다.
  - "월간 운송료 정산"에 산재보험료 줄이 없다.
  - 계가 3,349,010원이다(지금보다 1,000원 많음).
- [세부 내역]에도 산재보험료 줄이 없다.
- 달력 탭 10월 정산 카드에는 "산재보험료 - 1,000원" 줄과 합계 3,348,010원이 그대로다.

### 진행 기록
- 2026-10-30 보리가 "응 그렇게 진행해"로 승인했다. 계에서도 빼지 않고, 세부 내역도 같이 뺀다.
- react-app `5030e24` 코드 커밋(푸시 전).
- 자동 검사 모두 통과: `npm test`(957개, 938개 두 묶음), typecheck, lint, depcheck, build.
- 고치기 전 실패 확인: 바꾼 테스트 2개(요약·세부)는 고치기 전 코드에서 실패하고, 고친 뒤 통과한다.
- 로컬 화면(손님 모드): 10월 5일에 콜 2건(15만 원, 산재 1,000원·500원)을 임시로 넣고 확인했다.
  - 내역서 요약: 기본 운송료 150,000원, 부가세 15,000원, 계 165,000원. 산재 줄 없음.
  - 세부(전체): 계 165,000원. 산재 줄 없음.
  - 달력 정산 카드: 산재보험료 -1,500원 줄이 있고 합계 163,500원(그대로).
  - 확인 뒤 임시 기록은 원래대로(빈 값) 되돌렸다.
- 줄 수: `reportSummary.js` 123, `reportDetail.js` 119, `ReportMonthSummaryCard.jsx` 79, `ReportDetailView.jsx` 170, `ReportSummaryContent.jsx` 148.
- 처음 확인 때는 푸시가 아직 GitHub에 반영되기 전이었다. 다시 확인하니 `5030e24`가 올라가 있었다.
- 푸시 뒤 CI verify·deploy 초록(run 41071454572).
- 배포본 확인(seed 로그인 탭, 보기만)
  - 10월 내역서 요약: 산재 줄 없음, 계 3,349,010원.
  - 세부(전체): 산재 줄 없음, 계 2,296,510원(= 785,802 + 1,373,456 - 68,672 + 205,924).
  - 달력: "산재보험료 - 1,000원" 줄과 합계 3,348,010원이 그대로.
- §5 리뷰
  - 바뀐 파일은 지시서 "건드릴 파일" 6개와 같다.
  - 새 파일·새 저장 키·타입 꼼수는 없다.
  - 새 주석 1개는 지시서 문구 그대로다. 지운 주석은 로드맵 번호가 붙은 옛 주석 1개다.
  - 기존 테스트 2개는 지시서대로 반대 동작으로 바꿨다(약해진 것이 아니라 기대값을 바꾼 것이다).
  - 줄 수 제한 안이다.
