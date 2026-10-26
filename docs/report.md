# docs/report.md — 현재 슬라이스 착수지시서

113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 113-B 기사 차량 운송비 내역서 — 파렛트는 그 차량 고정 노선 스위치로 `[x]`

로드맵 113(②) 둘째 조각. 113-A `[x]`(react-app `bdd5565`) 조사 때 찾음, 보리 "고쳐야지 다음착수지시서에서 고치던가" `[확인: 2026-10-26 보리]`.

### 쉬운 요약
- 차주가 서류 발급에서 기사 차량의 운송비 내역서를 열면, 파렛트를 넣을지를 메인 차량 고정 노선 스위치로 판단한다. 그런데 그 기사 차량의 달력은 기사 차량 고정 노선 스위치로 판단한다.
- 그래서 두 스위치가 다르면 내역서만 파렛트가 빠지거나(메인 끔·기사 차량 켬), 넣으면 안 되는데 들어간다(반대).
- 내역서도 달력처럼 그 일지의 스위치를 보게 한다. 메인 차량 내역서와 연동 기사 본인 내역서는 지금 그대로다.

### 현재 상태 (코드 확인)
- 내역서 계산: `buildMonthReport`(`features/documents/api/reportSummary.js` 79줄~). 파렛트 조건 두 곳이 모두 `practiceSettings.fixedOn`(메인 스위치)이다.
  - 86줄 `showPallet`(날짜별 파렛트 장수·금액 칸, `buildReportDayRows`에 넘김)
  - 90줄 `activeFixedOn`(월 합계 `monthSettlementSummary`의 파렛트 — `monthSettlement.ts` 101~105줄은 이 값이 꺼지면 파렛트 0)
- 부르는 곳은 `ReportPage.jsx` 56줄 하나. 그 화면은 일지 키 `logKey`를 안다(37줄, 기사 차량이면 차량 번호, 아니면 `'main'`). 기사 차량이면 `cars`를 그 차 하나로 거른다(49줄).
- 달력은 일지 키로 고른다: `useMonthSettlement.ts` 30줄 `activeFixedOn = isMain ? settings.fixedOn : settings.subFixedOn`, 31~32줄 파렛트 단가도 같은 조건.
- 연동 기사 본인 앱: 일지 키는 `'main'`이라 달력은 `fixedOn`을 본다. 그런데 차량은 서버 행 종류대로 `'sub'`로 올 수 있다(`hydrateEmployedDriverCar.ts` 10줄). 그래서 판단은 차량 종류가 아니라 일지 키로 해야 한다 — 차량 종류로 하면 연동 기사 본인 내역서가 틀어진다.

### 목표 상태 (기대 동작)
1. 차주 계정의 기사 차량 내역서(서류 발급에서 그 차량, `/app/logs/{차량}/report`)는 파렛트 칸과 합계를 기사 차량 고정 노선 스위치(`subFixedOn`)로 판단한다 — 그 차 달력 월간 정산과 같다.
2. 메인 차량 내역서와 연동 기사 본인 내역서(일지 키 `'main'`)는 지금처럼 `fixedOn`이다.
3. 예: 메인 고정 노선 꺼짐, 기사 차량 켜짐, 그 차 고정 1회 + 파렛트 2장(5,000원) → 지금 내역서 파렛트 0원, 고친 뒤 10,000원(달력과 같음). 반대(메인 켜짐·기사 차량 꺼짐)면 지금 10,000원, 고친 뒤 0원.
4. 두 스위치가 같으면 지금과 결과가 똑같다.
5. 고정 노선 금액·거래처 스코프 단가·기사 차량 수수료 안 뗌(F-06) 등 다른 내역서 계산은 그대로다.

### 건드릴 파일
- `react-app/src/features/documents/api/reportSummary.js` — `buildMonthReport` 맨 끝에 인자 `logKey = 'main'`을 더하고(JSDoc `@param {string} [logKey]` 한 줄), 86·90줄의 `practiceSettings.fixedOn`을 그 일지 기준 값 하나로 바꾼다. 주석 최종 문구:
  `// 고정 노선 켜짐은 그 일지 기준 — 메인(연동 기사 본인 포함)은 fixedOn, 차주가 보는 기사 차량은 subFixedOn(달력과 같음).`
- `react-app/src/features/documents/ui/ReportPage.jsx` — 56줄 `buildMonthReport(...)` 끝에 `logKey`를 넘기고 `useMemo` 의존 목록에 `logKey`를 더한다. 주석 없음.
- 시험: 새 파일 `src/features/documents/api/reportPalletSwitch.test.js`(기존 `report.test.js`가 413줄이라 따로) — 목표 1~4: 기사 차량 일지 키로 두 스위치 조합 둘, 일지 키 `'main'`(연동 기사 본인처럼 차량 종류 `'sub'`)은 `fixedOn`, 같으면 그대로. 새 시험은 고치기 전 코드에서 실패하는지 확인한다.
- 문서(`[x]` 때): `docs/sot.md` 4-17에 "운송비 내역서의 파렛트도 그 일지 스위치(메인 `fixedOn`, 기사 차량 `subFixedOn`)" 한 줄, 로드맵 113-B 끝 표시, `STATUS.md` 완료 한 줄.

### 안 건드릴 것
- `monthSettlement.ts`·`useMonthSettlement.ts` — 달력은 이미 맞음.
- 내역서의 고정 노선 거래처 고르기(83~85줄 `fixedScopeKey`) — 차량 종류로 스코프를 고르는 것은 그대로(연동 기사 본인도 그 차량 스코프가 맞음, 기존 시험 `report.test.js` 261~289줄).
- 113-C(기사 관리 거래처 세금계산서 묶음) — 다음 슬라이스.

### 검증
- 자동 검사는 CI가 한다. 로컬에서도 lint·typecheck·npm test.
- 새 시험 revert-check.
- 화면: 로그인해 둔 탭(테스터 계정)에서 기사 차량 내역서와 그 차 달력 월간 정산의 파렛트를 비교한다(읽기만). 두 스위치가 같은 계정이면 결과가 같아야 정상이고, 다른 조합을 보려면 스위치를 바꿔야 해서 그건 보리에게 먼저 묻는다.

### 파일 길이
- `reportSummary.js` 119 → 약 122줄.
- `ReportPage.jsx` 166 → 166줄(같은 줄에 인자 추가).
- 새 시험 파일 약 50줄.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치를 만들지 않는다. 계획대로 안 되면 손대지 말고 지시서를 다시 써서 승인받는다.

### 사용자 확인 방법
1. 기사 차량이 있는 계정에서 앱 설정 → 기사 차량 운행일지 설정의 고정 노선과 메인 고정 노선 스위치가 다르게 켜진 상태를 만든다(이미 같으면 이 확인은 결과가 지금과 같음).
2. 그 기사 차량의 달력 월간 정산 "파렛트 회수 청구액"을 본다.
3. 서류 발급에서 그 차량 운송비 내역서를 연다 → 파렛트 금액이 2번과 같으면 성공.

### 진행 기록
- 2026-10-26 보리: "확정해 터미널ai가 할게" — 착수 승인, 터미널 AI 작업(시작 전 지시서 교차검증).
- 2026-10-26 보리: "착수지시서 확정,작업진행해" — 터미널 AI.
  - 교차검증: 고친 사실 없음. `reportSummary.js` 79·83~86·90줄, `ReportPage.jsx` 37·49·56줄, 달력 `data/store/useMonthSettlement.ts` 30~32줄, `hydrateEmployedDriverCar.ts` 10줄 모두 지시서와 같음. `buildMonthReport`를 부르는 곳은 `ReportPage.jsx` 하나(시험 제외).
  - 구현: react-app `cfa3231`(push 안 함). `reportSummary.js` 119 → 122줄, `ReportPage.jsx` 166줄 그대로. 주석 지시서 문구 그대로.
  - 고치기 전 실패 확인: 새 시험 4개 중 2개(기사 차량 두 조합)는 고치기 전 코드에서 실패, 고친 뒤 통과. 2개(일지 키 main은 fixedOn, 두 스위치가 같으면 그대로)는 바뀌면 안 되는 것을 지키는 안전 확인이라 고치기 전에도 통과.
  - 검증: lint·typecheck·depcheck(1813)·npm test(906·895) 통과.
  - 화면: 못 함. 이 터미널 세션의 Chrome은 5174에 로그인돼 있지 않았고(로그인 탭은 데스크톱 앱 내장 브라우저), 비회원으로는 시작하지 않음.
  - 남은 일: 보리 push → CI → 로그인 탭에서 기사 차량 내역서와 그 차 달력 월간 정산 파렛트 비교(읽기만).
- 2026-10-26 보리 push 뒤 CI verify·deploy 초록(run 40261945586). 데스크톱 AI 확인: 커밋 `cfa3231`는 지시서 두 파일 + 새 시험, 주석 문구 그대로. 로그인 탭(localhost:5174 테스터 계정, 읽기만): 기사 차량 22가2222 — 달력 파렛트 줄 없음, 내역서 파렛트 칸은 보이고(그 차량 스위치·거래처 파렛트 켜짐) 날마다 "-"·합계에 파렛트 없음, 같음. 99가9998 — 달력 파렛트 없음. 두 차 모두 파렛트 입력이 없고 메인·기사 차량 스위치가 같아, 고친 차이(스위치가 다를 때)는 계정 데이터를 바꾸지 않으면 화면으로 못 봄 — 시험 `reportPalletSwitch.test.js`로만 확인.
- 2026-10-26 보리 "테스터 계정에서 바꾸고 되돌리면서 화면으로도 확인해" — 로그인 탭(localhost:5174 테스터 계정). 시작 값: 메인 고정 노선 켜짐(세부 입력 켜짐), 기사 차량 고정 노선 켜짐, 22가2222 세부 입력 켜짐, 99가9998 세부 입력 꺼짐, 22가2222 10월 3일 파렛트 0. ① 22가2222 10월 3일 파렛트 2장(테스터거래처 5,000원) 넣음 → 둘 다 켜짐: 그 차 달력 파렛트 10,000원. ② 메인 끔·기사 차량 켬: 달력 파렛트 10,000원·합계 648,000원, 내역서 10월 3일 "2장 210,000원"·부가세 72,000원·계 792,000원(파렛트 포함) — 같음(고치기 전이면 내역서만 0원이던 경우). ③ 메인 켬·기사 차량 끔: 달력 파렛트 없음·부가세 71,000원·합계 639,000원, 내역서 파렛트 칸 없음·부가세 71,000원·계 781,000원 — 같음. 되돌림: 메인·기사 차량 고정 노선 켬, 기사 차량 끌 때 99가9998 세부 입력이 켜짐으로 바뀌어(기사 차량 고정 노선을 끄면 세부 입력 필수 규칙) 다시 끔, 파렛트 0으로 — 22가2222 달력 합계 639,000원·메인 달력 파렛트 50,000원·합계 3,348,010원으로 시작 값과 같음. 관찰(이번 범위 밖): 운송비 내역서 월간 정산에는 파렛트 줄이 따로 없음 — 금액은 날짜 칸·부가세·계에는 들어가지만 "파렛트 회수 청구액" 줄이 없어(달력엔 있음) `buildMonthReport`가 `palletFare`를 넘기지 않음.
- 2026-10-26 보리 "승인" — `[x]`. 위 관찰은 "응 그것도 고쳐" — 로드맵 113-D로 등재.

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
