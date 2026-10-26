# docs/report.md — 현재 슬라이스 착수지시서

운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 113-A 세금계산서에 파렛트 금액 넣기 `[x]`

보리 지시(2026-10-26): "파렛트계산해야해" → 계산 비교 문서 `docs/external-calc-comparison.md` 5절 C(정해 둔 규칙 없음)를 보이자 "넣어야지 당연히" `[확인: 2026-10-26 보리]`. 같은 지시의 "파렛트가 빠진게 또잇는지봐" 조사 결과는 대화로 따로 보고했다(이 지시서 범위 아님).

### 쉬운 요약
- 고정 노선 거래처에 파렛트를 받는 날, 달력·매출 화면에는 파렛트 금액이 들어가는데 세금계산서에는 빠져 있다. 세금계산서에도 넣는다.
- 예: 고정 노선 1회 25만 원 + 파렛트 2장(1장 5천 원) → 지금 세금계산서 공급가액 250,000원, 고친 뒤 260,000원(달력·매출과 같음). 부가세도 1,000원 늘어난다.
- 운비서에게 "거래처 매출"을 물으면 세금계산서와 같은 계산을 쓰기 때문에 그 답도 같이 맞춰진다.

### 현재 상태 (코드 확인)
- 세금계산서 금액은 `getTaxInvoiceSourceGroups`(`domain/financeTaxInvoiceGroups.ts` 15~79줄)가 낸다. 차량(메인·기사 차량)마다, 거래처마다 콜 상세 운송료(53~63줄)와 고정 노선 횟수 × 단가(65~75줄)만 더하고, 파렛트는 안 본다.
  - 확인: 위 예를 그대로 넣으면 세금계산서 `['한진', 250000, 25000]`, 달력 `monthSettlementSummary` 운송료 + 파렛트 260000(스크래치패드 시험 스크립트).
- 다른 화면은 모두 같은 조건으로 파렛트를 넣는다: 그 차량 고정 노선 켜짐(메인 `fixedOn`, 기사 차량 `subFixedOn`) + 고정 노선 거래처 `palletOn`, 금액은 장수 × `palletPrice`.
  - 달력 `monthSettlement.ts` 101~105줄(부가세 날마다 `vatOf`), 매출 `financeOwnerDetail.ts` 65~69줄(그 거래처 매출에 더함), 기사 몫 `financeCore.ts` 147~149줄, 하루 금액 `calendarBadges.ts` `dayFareTotal`(sot 4-17).
- 파렛트 장수는 휴무 날 저장 때 0이 된다(`day-record.ts` 138줄). 그래서 휴무 확인은 따로 안 넣어도 된다.
- 쓰는 곳: 서류 발급 세금계산서 탭(`listTaxInvoiceEntries` → `invoices.ts` 39줄), 운비서 거래처 매출 답(`unbiseoNumbers.ts` 47줄).
- 세금계산서 목록은 `...saved, ...group` 순서라(`financeTaxInvoiceEntries.ts` 71~80줄) "발급 완료"로 표시한 달도 금액은 매번 다시 계산한다. 지금도 운행을 고치면 발급 완료분 금액이 바뀌는 방식이다.

### 목표 상태 (기대 동작)
1. 고정 노선 거래처 계산서의 공급가액에 그달 파렛트 금액(장수 × 파렛트 단가)이 더해진다. 조건은 달력·매출과 같다(그 차량 고정 노선 켜짐 + 거래처 파렛트 켜짐).
2. 부가세는 고정 노선 금액과 파렛트 금액을 날마다 따로 버림해서 더한다(달력 `monthSettlement.ts`와 같은 규칙, sot 4-14).
3. 건수(`count`)는 그대로다. 파렛트는 운행 횟수가 아니다(sot 4-17 마지막 줄).
4. 고정 노선 횟수가 0이고 파렛트만 있는 날도 그 거래처 계산서에 들어간다(달력과 같음).
5. 기사 차량 계산서도 같은 규칙이고, 그 차량 스코프 고정 노선 거래처의 파렛트 단가를 쓴다(`getFixedRouteClient(settings, source.logId)` 그대로).
6. 고정 노선 거래처 이름이 없으면 지금처럼 고정 노선도 파렛트도 안 들어간다.
7. 운비서 "거래처 매출" 답이 세금계산서 탭 숫자와 같다(파렛트 포함).
8. 시험 바탕 데이터(`finance.fixtures.ts` — 메인 5월 10일 고정 2회 + 파렛트 3장, 단가 10,000원)로 메인 한진 계산서가 630,000 → 660,000원, 부가세 63,000 → 66,000원, 합계 726,000원, 건수 5 그대로.

### 건드릴 파일
- `react-app/src/domain/financeTaxInvoiceGroups.ts` — `getTaxInvoiceSourceGroups`의 차량 반복 안에 그 차량 고정 노선 켜짐·거래처 파렛트 켜짐·파렛트 단가를 구하고, 날마다 고정 노선 계산 바로 뒤에서 파렛트 금액을 같은 거래처 묶음에 더한다(공급가액·부가세만, 건수는 안 더함). 주석 최종 문구:
  `// 파렛트도 고정 노선 거래처 계산서에 — 달력·매출과 같은 조건(고정 노선 켜짐 + 거래처 파렛트 켜짐), 부가세는 날마다.`
- 시험:
  - `src/domain/finance.test.js` 120~135줄 확정 금액 표, `src/domain/receivables-invoices.test.js` 105~112줄 확정 금액 표 — 메인 한진 값을 목표 8대로 바꾼다. 지금 값은 파렛트가 빠진 버그 값이라 바꾸는 것이다(지우거나 약하게 하지 않음).
  - 새 시험(같은 `finance.test.js`나 `receivables-invoices.test.js` 안): 위 쉬운 요약 예 → 260,000/26,000, 건수 1. 거래처 파렛트 꺼짐이면 250,000. 그 차량 고정 노선 꺼짐이면 파렛트 없음. 파렛트만 있는 날(고정 0)도 들어감. 기사 차량 스코프 거래처 단가.
  - `src/features/home/api/unbiseoNumbers.test.js` 105줄 근처 — 세금계산서와 같은 함수를 비교하므로 값이 같이 움직이는지 확인만, 필요하면 기대값만 고침.
  - 새 시험은 고치기 전 코드에서 실패하는지 확인한다.
- 문서(`[x]` 때): `docs/sot.md`에 "세금계산서에도 파렛트 포함(달력·매출과 같은 조건, 부가세 날마다, 건수 안 셈), 파렛트엔 거래처 수수료 안 뗌" 한 줄(4-17 아래), `docs/external-calc-comparison.md` 5절 C에 끝 표시, `STATUS.md` 완료 한 줄.

### 안 건드릴 것
- 콜 상세·고정 노선 계산(53~75줄의 기존 금액), 계산서 저장 형식·발급 상태 — 금액 더하기만.
- 휴무 날 콜이 세금계산서에 남는 문제(같은 문서 5절 B) — 이번 범위 아님.
- 파렛트 금액에는 거래처 수수료를 안 뗀다 `[확인: 2026-10-26 보리 — "파렛트는 수수료를 안때"]` — 지금 달력·매출 그대로, 세금계산서는 수수료와 무관(공급가액만).
- 기사 차량 운송비 내역서 파렛트 스위치(113-B), 기사 관리 거래처 세금계산서 묶음의 고정 노선·파렛트(113-C) — 다음 슬라이스.

### 검증
- 자동 검사는 CI가 한다(테스트·타입·빌드·검사기). 로컬에서도 lint·typecheck·npm test를 돌린다.
- 새 시험은 고치기 전 코드로 되돌려 실패하는지 확인(revert-check).
- 저장·동기화 경로는 안 건드린다(계산 함수 하나) — 플레이북 §4 해당 없음.

### 파일 길이
- `financeTaxInvoiceGroups.ts` 161 → 약 170줄. 200줄 안.
- 시험 파일: `finance.test.js` 445줄, `receivables-invoices.test.js` 186줄 — 새 시험은 줄이 적은 `receivables-invoices.test.js`에 넣는다(200줄 넘으면 새 시험 파일 `financeTaxInvoicePallet.test.js`로).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치를 만들지 않는다. 계산이 다른 화면과 안 맞으면 손대지 말고 지시서를 다시 써서 승인받는다.

### 사용자 확인 방법
1. 파렛트를 넣은 날이 있는 달의 서류 발급 → [세금계산서] 탭을 연다.
2. 고정 노선 거래처 계산서의 공급가액이 그 달 달력(또는 매출 화면)의 그 거래처 운송료 + 파렛트와 같으면 성공.
3. 운비서에게 "이번 달 (거래처 이름) 매출"을 묻는다 → 세금계산서 탭의 합계와 같으면 성공.

### 진행 기록
- 2026-10-26 보리: "확정하고 터미널ai가할거야" — 착수 승인, 터미널 AI 작업(시작 전 지시서 교차검증).
- 2026-10-26 보리: "착수지시서 확정,작업진행해" — 터미널 AI.
  - 교차검증 고친 사실: `financeTaxInvoiceGroups.ts` 콜 상세는 49~59줄, 고정 노선은 61~71줄(지시서 53~63·65~75). 플레이북은 "해당 없음"이 아니라 AGENTS §4 트리거(`domain/finance*`)라 열람함 — 저장·동기화 절(§1~5)은 계산 함수 하나라 해당 없고, §6 고치기 전 실패 확인만 해당. 새 시험은 `receivables-invoices.test.js`에 넣으면 200줄을 넘어 지시서대로 새 `financeTaxInvoicePallet.test.js`. 그 밖(달력·매출·기사 몫 파렛트 조건, `day-record.ts` 138줄 휴무 0, 시험 바탕 데이터)은 맞음.
  - 구현: react-app `bdd5565`(push 안 함). `financeTaxInvoiceGroups.ts` 161 → 173줄, 주석 지시서 문구 그대로. 파렛트 장수는 달력과 같은 `getPalletCount`로 읽음.
  - 시험: 새 시험 7개(달력 금액·부가세와 같음 포함). 확정 금액 표 메인 한진 630,000/63,000 → 660,000/66,000(합계 726,000, 건수 5 그대로), 작성 전 목록 합계 1,160,000 → 1,190,000. `unbiseoNumbers.test.js`는 고칠 것 없이 통과.
  - 고치기 전 실패 확인: 새 시험 7개 중 5개는 고치기 전 코드에서 실패, 고친 뒤 통과. 2개(거래처 파렛트 꺼짐·고정 노선 꺼짐이면 파렛트 없음)는 바뀌면 안 되는 것을 지키는 안전 확인이라 고치기 전에도 통과.
  - 검증: lint·typecheck·depcheck(1813)·npm test(906·891) 통과.
  - 남은 일: 보리 push → CI → 서류 발급 세금계산서 탭·운비서 거래처 매출 확인(사용자 확인 방법 1~3).
- 2026-10-26 보리 push 뒤 CI verify·deploy 초록(run 40259484062). 데스크톱 AI 확인: 커밋 `bdd5565`는 지시서 범위(계산 파일 1개 + 시험), 주석 문구 그대로, 시험 기대값 변경은 파렛트가 빠진 버그 값 3곳만.
- 2026-10-26 보리 "고정노선 계산서를 안켯는데 어케확인해 니가해" — 데스크톱 AI가 로컬 미리보기(`bdd5565`와 같은 코드) 비회원 모드로 확인(이 브라우저에만 저장, 서버 안 감): 거래처 "시험고정" 고정 노선 1회 250,000원·파렛트 5,000원, 10월 20일 고정 1회 + 파렛트 2장 → 일지 운송료 합계 260,000원, 달력 월간 정산 250,000 + 파렛트 10,000 + 부가세 26,000 = 286,000원, 매출 운송료 260,000원·부가세 26,000원, 서류 발급 세금계산서 1건 공급가액 260,000원·부가세 26,000원·합계 286,000원 — 세 화면 같음. (비회원으로 연 것은 AI 실수 — 보리가 로그인해 둔 탭이 있었음).
- 2026-10-26 로그인된 탭(localhost:5174 테스터 계정, 읽기만): 10월 달력 테스터거래처 기본 운송료 2,323,456원 + 파렛트 회수 청구액 50,000원 → 서류 발급 세금계산서 테스터거래처(메인 차량, 22건) 공급가액 2,373,456원·세액 237,345원·합계 2,610,801원, 운비서 "이번 달 테스터거래처 매출" 답 공급가액 2,373,456원·부가세 포함 2,610,801원 — 모두 같음.
- 2026-10-26 보리 "승인" — `[x]`.

---

## 로드맵 24-K-2 운비서 앱 — 칸 읽기 나누기 + [휴무로 하기]·[휴무 취소하기] `[x]`

> 2026-10-25 보리 "24-K-2 [x] 승인. 커밋은 아직 하지 마." — 문서 커밋 전이라 지우지 않고 남겨 둔다.

보리 지시(2026-10-25, 24-K-1 `[x]` 뒤): "24-K-2 착수지시서 작성해(커밋은 나중에 한 번에)". 24-K의 둘째(마지막) 조각이다. 서버(K-1, react-app `6a52421`)는 이미 배포됐다.
- 확인할 점 1~4는 K-1 때 모두 "가"로 정해졌다 `[확인: 2026-10-25 보리 — "확인할 점 1~4 전부 가로"]`. 이번에 새로 물을 것은 없다.
- 규칙은 sot "휴무 말 알아듣기" 줄과 아래 목표를 따른다.

### 쉬운 요약
- "오늘 휴무"라고 하면 카드에 "오늘을 휴무로 할게요."와 [휴무로 하기]가 보인다. 누르면 그 날짜 일지로 가서 휴무 단추를 누른 것과 똑같이 바뀐다. "휴무 취소"면 [휴무 취소하기]로 같은 길을 간다.
- 콜이 있는 날을 휴무로 하면 일지 휴무 단추와 같은 확인 창이 뜬다. 고정 횟수는 0이 된다.
- 그 전에 앱 파일 `unbiseoParse.ts`(193줄)에서 종류별 칸 읽기를 새 파일로 떼어 낸다. 하는 일은 바뀌지 않는다.

### 현재 상태 (코드 확인)
- 앱은 휴무 답을 읽지 못한다. `readAnswer`(`features/home/api/unbiseoParse.ts` 116줄~)에 dayOff 갈래가 없어 마지막 줄에서 "알아듣지 못했습니다"가 된다.
- `unbiseoParse.ts`는 193줄이다.
  - 칸 읽기: 상수, `record`·`text`·`dateOf`·`wonOf`·`readExpense`·`readFixed`·`readClient`·`readWeather`(45~114줄)
  - 나머지: 형, 실패 문구, `readAnswer`, `clientNamesForAi`·`routeNamesForAi`, `requestUnbiseoParse`
- 휴무는 일지 화면의 저장 장치로만 바뀐다. 운비서가 이미 쓰는 길이 있다.
  - [기록하기]·[휴무 취소하고 기록]은 주소 상태로 그 날짜 일지(`/app/day/{날짜}`)에 값을 넘긴다(`app/routes/assistantCallState.js` `assistantNavTarget`).
  - 일지 훅이 받아 `patchDraft({ isOff, fixedCount })`로 바꾼다(`features/day-log/hooks/useAssistantFixed.js` 30~31줄). 휴무 단추와 같은 저장이다(`DayLogPage.jsx` 111줄).
- 콜이 있는 날 휴무 확인은 `confirmOffWithCalls`(`features/day-log/ui/OffToggle.jsx`)다.
- 연동 기사가 계약 기간 밖 날짜를 열면 일지 전체가 기간 안내 화면이다(`DayLogPage.jsx` 102줄). 카드는 이동 전에 `assignmentPeriodNotice`로 같은 판단을 한다(운행 카드, 24-B-3-A).

### 목표 상태 (기대 동작)
1. **나누기(동작 그대로)**: `unbiseoParse.ts`의 칸 읽기(위 45~114줄 목록)를 새 `unbiseoFields.ts`로 옮긴다. 옮기기 전과 후에 기존 시험이 모두 같아야 한다.
2. 서버 답이 dayOff면 앱이 `{ date, off }`를 읽는다. 날짜 모양이 틀리면 오늘이고, 앞날은 그대로 둔다. `off`가 boolean이 아니면 "알아듣지 못했습니다"다.
3. 카드 문장과 버튼 — 차례로 보고 처음 걸리는 것 하나만 보인다. 날짜 말은 오늘이면 "오늘", 아니면 "M월 D일"이다(`dateLabelOf`).
   1. 연동 기사이고 계약 기간 밖 → 운행 카드와 같은 기간 안내, 버튼 없음
   2. 휴무로 하기인데 그날 이미 휴무 → "{날짜}은 이미 휴무예요.", 버튼 없음
   3. 휴무 취소인데 그날 휴무가 아님 → "{날짜}은 휴무가 아니에요.", 버튼 없음
   4. 그 밖 → "{날짜}을 휴무로 할게요." + [휴무로 하기] / "{날짜} 휴무를 취소할게요." + [휴무 취소하기]
   - 조사는 받침을 따른다(`withParticle`, 예: "오늘을"·"10월 26일을"·"오늘은").
4. 버튼을 누르면 [기록하기]처럼 떠 있는 카드를 접고 그 날짜 일지로 간다(주소 상태 `assistantDayOff: { off }`). 일지가 값을 받은 뒤 한 번만:
   - 휴무로 하기: 콜이 있으면 `confirmOffWithCalls`로 묻는다. [취소]면 바꾸지 않는다. 확인하면 `patchDraft({ isOff: true, fixedCount: 0 })`(휴무 단추와 같음).
   - 휴무 취소하기: `patchDraft({ isOff: false, fixedCount: 그대로 })`.
   - 바뀌면 알림 "{M월 D일}을 휴무로 바꿨습니다." / "{M월 D일} 휴무를 취소했습니다."를 띄운다. 이미 그 상태면 아무것도 바꾸지 않는다.
   - 저장은 일지의 평소 저장이 한다(새 저장 길 없음).
5. 마이크로 물었으면 3번 문장을 소리로 읽는다(지금 읽기 방식).
6. 계정이 바뀌면 받아 온 답을 버린다(지금 `send` 그대로). 홈 카드·떠 있는 운비서 둘 다 같다.
7. 다른 종류(운행·지출·질문·고정·거래처·문자·날씨·점검표)는 지금과 같다.

### 건드릴 파일
- `src/features/home/api/unbiseoFields.ts`(새): `unbiseoParse.ts`에서 옮긴 칸 읽기 + 휴무 칸 읽기.
- `src/features/home/api/unbiseoParse.ts`: 칸 읽기를 떼어 내고 가져오기, `UnbiseoResult`·`UnbiseoRecord`에 휴무, `readAnswer`에 한 줄.
- `src/features/home/api/unbiseoSentence.ts`: 휴무 문장 함수, `describeResult`에 한 줄.
- `src/features/home/hooks/useUnbiseoAssistant.ts`: 답에 휴무 칸, 이미 휴무·휴무 아님 판단(그날 `workData`), 기간 판단에 휴무 날짜 포함.
- `src/features/home/ui/HomeAssistantPanel.tsx`: [휴무로 하기]·[휴무 취소하기] 버튼(버튼 자리 순서: 기간 안내 → 휴무 버튼).
- `src/app/routes/assistantCallState.js`: 휴무 주소 상태 만들기·읽기(`readAssistantDayOff`).
- `src/app/routes/MainPageRoute.jsx`: 일지에 휴무 값 넘기기.
- `src/features/day-log/ui/DayLogPage.jsx`: 휴무 값 받기와 훅 한 줄.
- `src/features/day-log/hooks/useAssistantDayOff.js`(새): 넘겨받은 휴무를 한 번만 적용(콜 확인·고정 0·알림). `useAssistantFixed.js`와 같은 모양.
- 시험: `unbiseoParse.test.js`(휴무 읽기, 나누기 뒤 기존 그대로), `assistantCallState` 시험(휴무 상태), `HomeAssistantPanel.test.js`(문장·버튼·이미 휴무·휴무 아님·기간 밖), 일지 쪽 휴무 적용 시험(콜 있음 확인·취소·고정 0·이미 같은 상태).
- 크기: 9개 파일(새 파일 2개). 24-J-2처럼 한 기능의 앱 조각이라 나누지 않는다. 나누기(1번)는 먼저 따로 확인하고 기능을 붙인다.

### 안 건드릴 것
- 서버 함수(K-1에서 끝남, 배포본 지문이 커밋과 같음).
- 휴무 단추 `OffToggle.jsx`(확인 함수를 그대로 부름), 일지 저장 장치(`dayDraftLifecycle.js` 등 — 평소 저장 그대로).

### 검증
- 로컬 `npm test`·타입·검사기(CI가 다시 함). 새 시험은 고치기 전 코드에서 실패하는지 확인한다.
- 나누기만 한 상태에서 기존 시험 전부 통과를 먼저 본다.
- 일지 저장을 건드리는 경로라 `docs/testing-playbook.md`를 열람한다(AGENTS §4). 새 저장 길은 없고 `patchDraft`만 부르므로, 같은 값을 두 번 적용하지 않는지(다시 그려도 한 번)와 계정이 바뀐 경우를 본다.
- 배포본: 데스크톱 AI가 테스트 계정으로 한다. 날짜는 보리가 정한다(일지 데이터가 바뀌므로). 그 날짜를 휴무로 → 일지 휴무 켜짐·알림 → 같은 말 다시 "이미 휴무예요" → 휴무 취소 → 원래대로. 콜이 있는 날 확인 창은 [취소]만 눌러 본다.

### 파일 길이
| 파일 | 지금 | 예상 |
|---|---|---|
| `home/api/unbiseoParse.ts` | 193 | ~110 |
| `home/api/unbiseoFields.ts`(새) | — | ~95 |
| `home/api/unbiseoSentence.ts` | 85 | ~95 |
| `home/hooks/useUnbiseoAssistant.ts` | 164 | ~175 |
| `home/ui/HomeAssistantPanel.tsx` | 85 | ~95 |
| `app/routes/assistantCallState.js` | 115 | ~130 |
| `app/routes/MainPageRoute.jsx` | 114 | ~117 |
| `day-log/ui/DayLogPage.jsx` | 184 | ~187 |
| `day-log/hooks/useAssistantDayOff.js`(새) | — | ~45 |

- 나누기 기준(AGENTS §6): `unbiseoParse.ts` = 서버 부르기·답 종류 가르기·실패 문구, `unbiseoFields.ts` = 종류별 칸 검사. `unbiseoFields.ts`는 `unbiseoParse.ts`를 가져오지 않는다(형은 `unbiseoFields.ts`에 두고 `unbiseoParse.ts`가 다시 내보냄).
- 200줄 감시 목록 파일은 없다. `DayLogPage.jsx`는 187줄이 되어 다음에 더할 때 나누기를 볼 것.

### 코드에 넣을 주석(최종 문구)
- `unbiseoFields.ts` 첫 줄: `// 운비서 칸 읽기 — 서버 답의 종류별 칸을 앱 목록·범위로 다시 검사한다. 틀린 칸은 빈 칸.`
- `useAssistantDayOff.js` 첫 줄: `// 운비서가 넘긴 휴무를 그 날짜 일지에 한 번 적용 — 휴무 단추와 같은 확인·같은 저장.`
- `HomeAssistantPanel.tsx` 휴무 버튼 위: `// 휴무는 그 날짜 일지로 가서 휴무 단추와 같이 바꾼다.`

### 실패 시 처리
- 새 저장·재시도·임시 보관은 만들지 않는다(AGENTS §7). 일지 저장 실패는 지금 일지 알림 그대로다.
- 시험이나 배포본이 기대와 다르면 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법
- 휴대폰에서 운비서 마이크로 "내일 휴무"라고 말한다 → "내일을 휴무로 할게요."가 들리고 [휴무로 하기]가 보인다 → 누른다 → 내일 일지가 열리고 휴무가 켜져 있으며 "M월 D일을 휴무로 바꿨습니다." 알림이 뜬다.
- 같은 말을 다시 하면 "이미 휴무예요"가 나온다.
- "내일 휴무 취소" → [휴무 취소하기] → 일지 휴무가 꺼진다.

### 진행 기록
- 2026-10-25 보리: "착수지시서 확정 작업진행해" — 터미널 AI.
  - 교차검증 고친 사실: "사용자 확인 방법"의 "내일을 휴무로 할게요."는 실제로 "10월 26일을 휴무로 할게요."처럼 나온다. 날짜 말(`dateLabelOf`)은 오늘이면 "오늘", 그 밖은 "M월 D일"이기 때문(목표 3과 같음). 그 밖(`readAnswer` 116줄~·칸 읽기 45~114줄, `assistantNavTarget`, `useAssistantFixed.js` 30~31줄, `DayLogPage.jsx` 102·111줄, `confirmOffWithCalls`)은 맞음. 플레이북 §2·§5·§7 열람.
  - 구현: react-app `7dec724`(push 안 함).
    - 나누기 먼저: `unbiseoParse.ts` 193 → 115줄, 새 `unbiseoFields.ts`. 옮긴 줄을 옛 파일과 한 줄씩 맞춰 봐 다른 줄은 첫 줄 주석뿐(`export`만 붙음). 나누기만 한 상태에서 타입·lint·npm test(896·874) 통과.
    - 휴무: `unbiseoFields.ts`(92줄) `UnbiseoDayOff`·`readDayOff`, `unbiseoParse.ts`(119줄) 결과·기록에 휴무와 `readAnswer` 3줄, `unbiseoSentence.ts`(93줄) `describeDayOff`.
    - `useUnbiseoAssistant.ts`(174줄): 이미 그 상태인지(그 순간 store 값, 연동 기사 기간 밖이면 기간 안내가 먼저), 답에 `dayOff`, 기간 판단 날짜에 휴무 날짜. `HomeAssistantPanel.tsx`(87줄): 기간 안내 다음에 휴무 버튼, 지시서 문구 주석.
    - `assistantCallState.js`(127줄) `readAssistantDayOff`·갈 곳, `MainPageRoute.jsx`(116줄) 넘기기·지우기, `DayLogPage.jsx`(187줄) 받기와 훅 한 줄, 새 `useAssistantDayOff.js`(42줄, `useAssistantFixed.js`와 같은 모양).
  - 지시서와 다른 점:
    1. 이미 그 상태인지·기간 밖인지는 훅의 화면 값이 아니라 묻는 그 순간 store에서 읽는다(24-J-2와 같은 이유 — `send` 안에서 연동 기사 화면 값을 읽으면 검사기 경고).
    2. `useAssistantDayOff.js`는 콜 확인 창을 기다리는 사이 일지 화면이 닫히면(계정 바뀜 등) 바꾸지 않는다(플레이북 §5). 지시서에 없던 막기.
    3. 일지 적용 시험은 기존 `MainPageRoute.assistantCall.test.js`(425줄)에 더하지 않고 새 `MainPageRoute.assistantDayOff.test.js`에 둠(주소 상태 읽기 시험 포함).
  - 검증: lint·typecheck·depcheck(1809)·npm test(896·884) 통과.
  - 고치기 전 실패 확인: 새 시험 10개(칸 읽기 1·카드 3·일지 적용·주소 상태 6) 중 8개는 고치기 전 코드에서 실패, 고친 뒤 통과. 2개("이미 그 상태면 아무것도 안 바꿈", "다시 그려도 또 바꾸지 않음")는 바뀌면 안 되는 것을 지키는 안전 확인이라 고치기 전에도 통과.
  - 남은 일: 보리 push → CI → 데스크톱 AI 배포본 시험(날짜는 보리가 정함, 콜이 있는 날 확인 창은 [취소]만) → 보리 휴대폰 확인.
- 2026-10-25 보리 push 뒤 CI verify·deploy 초록(run 40170715985).
- §5 리뷰(데스크톱 AI): 범위 = 지시서 9개 파일 + 시험 3개(일지 적용 시험은 새 파일 — 교차검증 3번), 새 저장 길 없음(`patchDraft`만), 타입 꼼수 없음, 200줄 안(최대 `DayLogPage.jsx` 187), 주석 세 줄 지시서 문구 그대로. "(로드맵 24-F-2)" 주석은 옛 `unbiseoParse.ts`에서 그대로 옮긴 것.
- 배포본 시험(데스크톱 AI, 테스트 계정, 2026-10-25, 날짜 10-26 — 보리 "내일(10월 26일)로 휴무 바꿨다가 취소해서 시험해"): 시작 전 10-26 휴무 아님·콜 없음 → 홈 "내일 휴무" → "10월 26일을 휴무로 할게요." + [휴무로 하기] → 누름 → 10-26 일지로 이동, 휴무 켜짐, 알림 "10월 26일을 휴무로 바꿨습니다." → 새로 고쳐도 휴무(저장됨) → 홈 "내일 휴무" 다시 → "10월 26일은 이미 휴무예요."(버튼 없음) → "내일 휴무 취소" → "10월 26일 휴무를 취소할게요." + [휴무 취소하기] → 누름 → 일지 휴무 꺼짐, 알림 "10월 26일 휴무를 취소했습니다." → 새로 고쳐도 꺼짐(원래대로). 콜이 있는 날 확인 창·연동 기사 기간 밖은 배포본에서 안 함(시험으로만). 소리 읽기는 보리 휴대폰 확인 남음.

---

## 로드맵 24-K-1 운비서 서버 — 답 읽기 나누기 + 휴무 말 알아듣기 `[x]`

> 2026-10-25 보리 "24-K-1 [x] 승인 … 커밋은 나중에 한 번에" — 문서 커밋 전이라 이 지시서를 지우지 않고 남겨 둔다(지울 때는 커밋 뒤).

보리 지시(2026-10-25): "ai추가할거잇어 휴무처리 아직불가능햄?" → "24-K 착수지시서 작성해".
- 24-F~J처럼 **둘로 나눈다(AI 판단)**. 순서는 `docs/unbiseo.md` 5절을 따른다.
  - **이번 K-1(서버)**: `unbiseo-parse/index.ts`를 먼저 나누고(194줄, AGENTS §6), 휴무 종류를 더한다.
  - **다음 K-2(앱)**: 앱 `unbiseoParse.ts`(193줄)도 먼저 나누고, 카드 문장과 버튼을 붙인다.
- 확인할 점은 K-2 것까지 한 번에 묻는다. 2번만 이번 서버에 걸린다.

### 확인할 점 (넷) — **1~4 모두 가로 확정** `[확인: 2026-10-25 보리 — "확인할 점 1~4 전부 가로"]`
1. **바꾸는 방식** (K-2)
   - **권함 가**: 카드에 "{날짜}을 휴무로 할게요."와 [휴무로 하기] 버튼을 보인다. 누르면 일지 휴무 단추와 같은 저장으로 바꾼다. 24-J [저장하기]와 같은 길이고, 버튼 한 번이 확인이 된다.
   - 나: 말하면 바로 바꾸고 알림만 띄운다.
2. **날짜** (K-1)
   - **권함 가**: 오늘·지난 날·**앞날 모두** 받는다. 말이 없으면 오늘이다. 휴무는 미리 정하는 일이 많고, 일지·달력도 앞날을 막지 않는다(`features/calendar`·`features/day-log`에서 앞날 막는 코드 0건).
   - 나: 운행·지출처럼 앞날은 오늘로 바꾼다.
3. **휴무 취소도 받을지** (K-1·K-2)
   - **권함 가**: 받는다. "오늘 휴무 취소" → "{날짜} 휴무를 취소할게요." + [휴무 취소하기]. 이미 휴무가 아니면 "{날짜}은 휴무가 아니에요."라고 문장만 보인다. 이미 휴무면 "{날짜}은 이미 휴무예요."라고 문장만 보인다.
   - 나: 휴무로 바꾸기만 받는다. 취소는 일지에서 한다.
4. **콜·고정 횟수가 있는 날을 휴무로** (K-2)
   - **권함 가**: 일지 휴무 단추와 똑같이 한다. [휴무로 하기]를 누를 때 콜이 있으면 같은 확인 창("휴무로 바꾸시겠습니까? / 이날 콜 n건이 있습니다…")을 띄우고, 고정 횟수는 0으로 한다(`OffToggle.jsx`·`DayLogPage.jsx` 111줄과 같은 규칙).
   - 나: 콜이 있는 날은 문장만 보인다("콜이 있는 날은 일지에서 바꿔 주세요.").

### 쉬운 요약
- "오늘 휴무", "내일 쉬어", "오늘 휴무 취소"를 운비서가 휴무 말로 알아듣고, 날짜와 휴무·취소를 돌려준다.
- 그 전에 서버 파일이 200줄에 닿아서, AI 답을 읽는 부분을 새 파일로 떼어 낸다. 하는 일은 바뀌지 않는다.
- 이번엔 서버만 바꾼다. 앱은 K-2 전까지 휴무 말에 지금처럼 "알아듣지 못했습니다"라고 한다.

### 현재 상태 (코드 확인)
- 배포본에서 "오늘 휴무"·"내일 쉬어"·"어제 휴무로 해줘"·"오늘 휴무 취소" 모두 unknown이다(2026-10-25 데스크톱 AI가 직접 부름).
- 서버 지시문(`grok.ts`)과 종류 목록(`RESPONSE_SCHEMA.kind.enum`, 87줄)에 휴무가 없다.
- `index.ts`는 194줄이다. 담긴 것:
  - 요청 받기: `reply`·`readInput`·`signedInUserId`·`publishableKey`·`handleRequest`
  - AI 답 읽기: `Call`·`Expense`·`Parsed` 형, 지출 목록 상수, `record`·`isDateKey`·`place`·`dayOf`·`won`·`clientOf`·`oneOf`·`readExpense`·`readAiAnswer`(109~144줄)
  - 한 줄만 더해도 195를 넘는다(24-J-1에서 정한 한도).
- `dayOf`(74줄)는 앞날을 오늘로 바꾼다. 날씨는 앞날을 그대로 두려고 따로 `weatherDraft.ts`의 날짜 검사를 쓴다.
- 날짜 표에는 내일·모레·글피 줄이 이미 있다(24-I-1, 날씨용). "11월 3일"처럼 월·일만 말하면 올해 그 날짜라는 지시도 있다(`grok.ts` 31줄).
- 말 막기 선례: "문자"(sms)·"점검"(inspection)이 없으면 서버 코드가 unknown으로 바꾼다(`index.ts` 121·124줄).

### 목표 상태 (기대 동작)
1. **나누기(동작 그대로)**: AI 답 읽기 부분(위 "AI 답 읽기" 목록 전부)을 새 `answer.ts`로 옮긴다. `index.ts`는 요청 받기·로그인·`handleRequest`만 남기고, `readInput`이 쓰는 `record`·`isDateKey`는 `answer.ts`에서 가져온다.
   - 옮기기 전과 후에 기존 시험 문장 전부의 답이 같아야 한다.
   - 저장소 밖 시험이 `readAiAnswer`를 `index.ts`에서 가져오고 있으면 `answer.ts`로 바꾼다(`index.ts`에서 다시 내보내지 않음).
2. **휴무 종류**: `kind: 'dayOff'`, 답은 `{ kind: 'dayOff', dayOff: { date, off } }`이다.
   - `off`는 휴무로 바꾸면 `true`, 휴무 취소면 `false`다(확인할 점 3). 새 칸은 `dayOffCancel`(boolean|null) 하나다.
   - `date`는 기존 `date` 칸을 쓰고, 확인할 점 2(가)대로 앞날도 그대로 둔다(날짜 모양만 검사, 틀리거나 없으면 오늘).
3. **말 막기**: 말에 "휴무"나 "쉬"가 없으면 AI가 dayOff라 해도 서버 코드가 unknown으로 바꾼다.
4. 운행·지출·질문·고정·거래처·문자·날씨·점검표 종류는 지금과 같다(기존 시험 문장 전부).
5. 저장하지 않는다. 함수 기록은 지금처럼 끝난 이유·시간만 남긴다.

### 건드릴 파일
- `supabase/functions/unbiseo-parse/answer.ts`(새): `index.ts`에서 옮긴 답 읽기 + 휴무 분기·휴무 날짜 읽기.
- `supabase/functions/unbiseo-parse/index.ts`: 답 읽기를 떼어 내고 `answer.ts`에서 가져오기.
- `supabase/functions/unbiseo-parse/grok.ts`: 종류 목록에 dayOff, 종류 설명 한 마디, 설명·예 1~2줄, 칸 `dayOffCancel`.
- 데스크톱 AI: 편집기 반영(새 `answer.ts` 추가, 바뀐 두 파일), 지문 확인, 보리 배포 뒤 배포본 지문·Verify JWT 꺼짐 확인과 문장 시험.

### 안 건드릴 것
- 앱 코드 전부(K-2). 배포 뒤 K-2 전까지 앱은 휴무 말에 "알아듣지 못했습니다"라고 한다. 앱 `readAnswer`(`api/unbiseoParse.ts`)에 dayOff 갈래가 없어 마지막 줄로 가기 때문이다.
- 다른 칸 읽기 파일(`question.ts`·`clientDraft.ts`·`fixedRoute.ts`·`smsDraft.ts`·`weatherDraft.ts`), `unbiseo-weather`·`unbiseo-voice`.

### 검증
- 함수 타입 검사, 저장소 밖 가짜 시험(24-A2부터 쌓은 문장 전부 + 휴무 문장). 고치기 전 코드에서 휴무 시험이 실패하는지 확인한다.
- 나누기는 **나누기만 한 상태**에서 기존 시험 전부가 같은 답을 내는지 먼저 본다. 그다음 휴무를 더한다.
- 배포 뒤 문장 시험(데스크톱 AI)
  - 휴무: "오늘 휴무"(오늘·true), "내일 쉬어"(내일·true), "어제 휴무로 해줘"(어제·true), "11월 3일 휴무"(그 날짜), "오늘 휴무 취소"(오늘·false), "오늘 쉬는 날 아니야 취소해" 등 취소 말, "휴게소에서 밥 먹었어"("휴무"·"쉬" 없음 → unknown 또는 다른 종류)
  - 기존 종류 문장들
  - Verify JWT 꺼짐 확인

### 파일 길이
| 파일 | 지금 | 예상 |
|---|---|---|
| `unbiseo-parse/index.ts` | 194 | ~85 |
| `unbiseo-parse/answer.ts`(새) | — | ~125 |
| `unbiseo-parse/grok.ts` | 176 | ~180 |

- 나누기 기준(AGENTS §6 분리 설계): `index.ts` = 요청 받기·로그인·응답, `answer.ts` = AI 답을 돌려줄 모양으로 읽기. `answer.ts`는 `index.ts`를 가져오지 않는다(한 방향).

### 코드에 넣을 주석(최종 문구)
- `answer.ts` 첫 줄: `// AI 답 읽기 — 종류마다 칸을 검사해 돌려줄 모양으로. 말에 꼭 있어야 할 단어는 여기서 막는다.`
- `answer.ts` 휴무 분기 위: `// "휴무"나 "쉬"가 없는 말은 AI가 휴무라 해도 관계없음으로.`

### 실패 시 처리
- 새 저장·재시도·임시 보관은 만들지 않는다(AGENTS §7).
- 다른 종류가 깨지면 `unbiseo-parse`를 이전 판(react-app `c992cb7`)으로 되돌려 올린다.

### 사용자 확인 방법
- 앱 화면은 바뀌지 않는다. 보리 할 일은 편집기에서 "Deploy updates" 한 번이고, 결과는 문장 시험 표로 본다.

### 다음 24-K-2 미리 보기(이번 범위 아님)
- 앱 `unbiseoParse.ts` 나누기(종류별 칸 읽기 떼기) → 휴무 카드 문장 → [휴무로 하기]·[휴무 취소하기](일지 휴무 단추와 같은 저장, 콜이 있으면 같은 확인 창) → 이미 휴무·휴무 아님·연동 기사 계약 기간 밖은 문장만.

### 진행 기록
- 2026-10-25 보리: "확인할 점 1~4 전부 가로." — K-1은 앞날도 받고 휴무 취소도 받음(`off` true/false). K-2는 [휴무로 하기]·[휴무 취소하기] 버튼, 콜이 있으면 일지 휴무 단추와 같은 확인 창·고정 횟수 0. 구현은 터미널 AI(시작 전 교차검증).
- 2026-10-25 보리: "착수지시서 확정 작업진행해" — 터미널 AI.
  - 교차검증 고친 사실: `readAiAnswer`는 109~143줄(지시서 109~144). 그 밖(`grok.ts` 87줄 종류 목록·31줄 월·일 지시, `index.ts` 74줄 `dayOf`·121·124줄 말 막기, 저장소 밖 시험은 `handleRequest`만 부름)은 맞음.
  - 구현: react-app `6a52421`(push 안 함).
    - 나누기 먼저: `index.ts` 194 → 91줄, 새 `answer.ts`. 옮긴 줄을 옛 `index.ts`와 한 줄씩 맞춰 봐 다른 줄은 첫 줄 주석뿐(`record`·`isDateKey`에 `export`만 붙음). 나누기만 한 상태에서 함수 타입 검사와 저장소 밖 시험(24-J-1까지 누적) 전부 통과.
    - 휴무: `answer.ts`(111줄)에 `Parsed` dayOff, 분기 한 줄과 지시서 문구 주석. 날짜는 `isDateKey`면 그대로(앞날 포함), 아니면 오늘. `off`는 `dayOffCancel`이 true일 때만 false.
    - `grok.ts`(179줄): 종류 줄에 dayOff, 설명 줄 1개(앞날도 표에서 그대로)와 예 1줄, 칸 `dayOffCancel`(필수·null 허용), 출력 상한 주석 "칸 서른두 개".
  - 지시서와 다른 점:
    1. `grok.ts` 61줄 주석 "앞날은 날씨에만 쓴다(운행·지출은 index.ts가 오늘로 바꿈)"이 틀리게 되어 "날씨·휴무에만 … answer.ts가"로 고침.
    2. 남은 것: `question.ts`·`smsDraft.ts`·`fixedRoute.ts` 주석의 "index.ts가 정리해 넘긴다"는 이제 `answer.ts`가 맞다. 지시서가 세 파일을 안 건드릴 것으로 정해 그대로 둠(동작 영향 없음). 고칠지 보리 판단.
  - 검증: 함수 타입 검사(`answer.ts` 추가) 통과. 저장소 밖 가짜 시험 전부 통과(휴무 확인 18개 추가, 고정 확인 3곳은 새 칸·종류·문구로 바꿈). lint·typecheck·depcheck(1802)·npm test(896·874) 통과.
  - 고치기 전 실패 확인: 휴무 확인 18개 중 16개는 고치기 전 코드에서 실패(502 또는 문구·칸 없음). 2개(점검표 앞날 → 오늘 그대로, 기록에 말 없음)는 고치기 전에도 통과하는 안전 확인.
- 2026-10-25 보리 push 뒤 CI verify·deploy 초록(run 40162631646).
- §5 리뷰(데스크톱 AI): 범위 = 지시서 파일 3개 그대로, 새 저장 없음, 타입 꼼수 없음, 200줄 안(`index.ts` 91·`answer.ts` 111·`grok.ts` 179), 새 주석 두 줄 지시서 문구 그대로. `answer.ts`의 "(로드맵 24-C-1)" 주석은 옛 `index.ts`에서 그대로 옮긴 것(새로 쓴 것 아님). 남은 주석 3곳("index.ts가 정리해 넘긴다")은 교차검증 2번대로 그대로.
- 데스크톱 AI 편집기 반영(배포 전): 편집기 내용이 이전 판 `c992cb7`과 같음 확인 → `grok.ts`·`index.ts` 바꾸고 새 `answer.ts` 추가(커밋에서 받아 넣음), 8개 파일 지문 커밋과 같음(`answer` `972adfc7`, `grok` `8acb2b45`, `index` `6de3b388`, 나머지 그대로). 보리 "Deploy updates" 대기.
- 보리 배포 → 배포본 8개 파일 지문 커밋과 같음("a few seconds ago"), Verify JWT 꺼짐 그대로, 공개 키만이면 401.
- 문장 시험(배포본, 2026-10-25, 로그인 계정, 오늘 10-25):
  - 휴무: 오늘 휴무 → dayOff 10-25·off true / 내일 쉬어 → 10-26·true / 어제 휴무로 해줘 → 10-24·true / 11월 3일 휴무 → 11-03·true / 모레 쉬는 날이야 → 10-27·true / 오늘 휴무 취소 → 10-25·false / 어제 휴무 취소해줘 → 10-24·false / 내일 안 쉬어 휴무 빼줘 → 10-26·false / 휴게소에서 밥 먹었어·오늘 놀아 → unknown.
  - 기존 종류 그대로: 점검표 → inspection / 날씨 → weather / 운행 → call 10-24 / 경유 → expense fuel / 이번 달 매출 → question / 고정 2번 → fixed / 거래처 등록 → client / 미수금 문자 → sms / 전부 양호야·뉴스 → unknown.
  - 남은 일: 보리 push → CI → 데스크톱 AI 편집기 반영(새 `answer.ts` 추가, 바뀐 두 파일)·보리 "Deploy updates" → 지문·Verify JWT 꺼짐 확인·문장 시험.

---

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
