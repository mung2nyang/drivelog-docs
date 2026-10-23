# docs/report.md — 현재 슬라이스 착수지시서

24-C-1 지시서·기록은 drivelog-docs `a26815e`, 24-A2는 `2ea514e`, 24-B-3-B는 `0548640`, 24-B-3-A는 `a0f2b64`, 24-B-2는 `c115e2b`, 24-B-1은 `18646c9`, 24-A는 `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 24-C-2 운비서 → 지출 입력 창 채워 열기 `[x]` `[확인: 2026-10-23 보리 — "승인"]`

보리 지시(2026-10-23, 24-C-1 `[x]` 뒤): "작성해". 24-C 둘째 조각 — 서버가 나눠 준 지출 칸(24-C-1, sot §4-16)으로 카드에 문장·[기록하기]를 보이고, 그 날짜 일지의 **지출 입력 창을 채워 열기**. 저장은 지금 지출 입력 창의 [저장] 그대로. 운행(24-B-2·3)과 같은 길을 지출에도 씀.

### 쉬운 요약
- "어제 경유 12만원"이라고 하면 카드에 "10월 21일 주유, 120,000원으로 기록할게요." + [기록하기]. 누르면 어제 일지로 가서 차량 유지비의 **주유 입력 창이 금액 120,000원으로 채워진 채** 열립니다. 확인하고 [저장].
- 휴무 날이면 운행처럼 "휴무를 취소하고 지출을 기록할까요?" + [휴무 취소하고 기록](휴무면 지출 칸도 흐려져 못 누름), 연동 기사의 계약 기간 밖이면 기간 안내.

### 현재 상태 (코드 확인)
- 서버는 지출을 `{ kind: 'expense', expense: { date, type, cost, name, category, fuelType, payment } }`로 줌(24-C-1, 진짜 서버 10문장 확인). 앱 `features/home/api/unbiseoParse.ts`(114줄) `readAnswer`는 `expense`면 `{ kind: 'expense' }`만, 문장 "지출 기록은 곧 할 수 있게 됩니다."(`describeResult`).
- 카드: `useUnbiseoAssistant.ts`(80줄)는 `answer.call`만 기억, 기간·휴무는 운행 날짜로(`:75-77`). `HomeAssistantPanel.tsx`(77줄) `actionFor(call)` — 기간 → 콜 상세 꺼짐 → 휴무 → [기록하기].
- 이동: `unbiseoContext.ts`(35줄) `openCall(call, { cancelOff })` → `AppShell.jsx`(198줄, 감시 목록) `navigate('/app/day/날짜', { state: assistantCallNavState(call, options) })` → `assistantCallState.js`(40줄)가 `assistantCall` 주소 상태를 만들고 읽음 → `MainPageRoute.jsx`(110줄)가 읽고 지움 → `DayLogPage.jsx`(176줄) → `useAssistantPrefill.js`(57줄, 운행만 — 콜 상세 창 열기·휴무 끄기·같은 날짜 다시 열기·입력 중 확인 창).
- 일지 지출 창: `useExpenseForm.js`(75줄) `openAdd(kind)`가 `emptyExpenseDraft(kind, dateKey)`로 빈 창을 엶(`:28-39`, 종류 고르기 중이면 0.42초 뒤), 저장은 `saveExpenseDraft`(바로 서버). 화면 `DayLogExpenses.jsx` → `ExpenseFormModal.jsx`(199줄, 감시 목록)는 `draft`를 받아 그림. 지출 칸에는 켜기/끄기 설정 없음(`DayLogExpenses.jsx` 늘 보임). 휴무면 `.modal-work-details.is-off`라 지출 칸도 못 누름(`DayLogPage.jsx` 지출이 그 안에 있음).

### 목표 상태 (기대 동작)
1. **문장**(앱이 정한 틀): "{오늘|M월 D일} {항목}, {금액}원, {결제}로 기록할게요." — 항목은 주유면 주유 종류(없으면 "주유"), 정비·기타면 항목명 → 분류 → "정비"/"기타" 순. 빈 칸은 뺌. 예: "10월 21일 주유, 120,000원으로 기록할게요.", "오늘 엔진오일 교환, 150,000원, 현금으로 기록할게요.", "오늘 와이퍼로 기록할게요."(금액 없음).
2. **버튼**: 지출도 운행과 같은 자리·순서 — 기간 밖 안내 → (콜 상세 꺼짐은 운행만) → 휴무면 "휴무를 취소하고 지출을 기록할까요?"+[휴무 취소하고 기록] → [기록하기]. 홈 카드·떠 있는 버튼 둘 다.
3. **이동**: [기록하기] → `/app/day/{날짜}`, 주소 상태 `assistantExpense: { type, cost, name, category, fuelType, payment, cancelOff }`(운행은 지금처럼 `assistantCall`). 일지가 칸마다 모양 확인(종류는 셋 중 하나 아니면 정비, 금액 0 이상 정수, 글자 100자, 분류·주유 종류·결제는 앱 목록 안만) 뒤 바로 지움.
4. **지출 창 열기**: 주소 상태가 지워진 뒤 한 번 — `cancelOff`이고 휴무면 휴무 끄기(운행과 같은 동작) → 콜 상세 창은 닫고 `openAdd(종류, 처음 값)`으로 그 종류 지출 창을 엶. 처음 값: 금액·항목명·분류·주유 종류·결제(빈 칸은 지금 빈 창의 기본값 그대로 — 분류 첫 항목·"주유"·"카드"). 날짜는 그 일지 날짜(지금처럼). 저장·닫기는 지금 그대로, 다시 "+ 추가"는 빈 창.
5. **같은 날짜**: 이미 그 날짜 일지에서 다시 [기록하기]면 운행과 같이 이동 키로 알아보고, 입력 칸이 열려 있으면 기존 확인 창 먼저.
6. 운행 흐름·문장·버튼은 그대로. 서브 차량 일지로는 안 감(운행과 같음 — 메인 일지).

### 건드릴 파일
- `src/features/home/api/unbiseoParse.ts` — `UnbiseoExpense` 타입·지출 칸 읽기(모양 확인)·지출 문장(114 → 약 155).
- `src/features/home/hooks/useUnbiseoAssistant.ts` — `answer.expense`도 기억, 기간·휴무는 운행 또는 지출 날짜로(80 → 약 84).
- `src/features/home/hooks/unbiseoContext.ts` — 이동 함수가 운행 또는 지출을 받게 타입만(35 → 약 36).
- `src/features/home/ui/HomeAssistantPanel.tsx` — 지출에도 버튼(콜 상세 확인은 운행만), 휴무 문구 "운행/지출"(77 → 약 85).
- `src/app/routes/assistantCallState.js` — `readAssistantExpense`·이동 상태 만들 때 지출이면 `assistantExpense`(40 → 약 70). `AppShell.jsx`는 안 건드림(이동 상태를 이 파일이 고르므로 — 198줄 그대로).
- `src/app/routes/MainPageRoute.jsx` — `assistantExpense`도 읽어 넘기고 지울 때 같이(110 → 약 113).
- 새 `src/features/day-log/hooks/useAssistantExpense.js`(약 50줄) — 지출 창 채워 열기·휴무 끄기·같은 날짜 다시(운행 쪽 `useAssistantPrefill.js`는 안 건드려 운행 회귀 위험을 줄임).
- `src/features/day-log/hooks/useExpenseForm.js` — `openAdd(kind, preset)` 처음 값 받기(75 → 약 78).
- `src/features/day-log/ui/DayLogPage.jsx` — 새 훅 부르기(176 → 약 180).
- 테스트: `unbiseoParse.test.js`(지출 읽기·문장), `HomeAssistantPanel.test.js`(지출 버튼·휴무·기간), 일지 시험(`MainPageRoute.assistantCall.test.js`에 지출 — 채워 열림·저장 1건·닫으면 0건·휴무 끄기·주소 상태 지움·이상한 값 걸러냄), `UnbiseoDock.test.js`에 같은 날짜 지출 1개.
- 문서(`[x]` 때): sot §4-16 앱 줄에 지출, 로드맵 24-C 끝.
- 파일 수 9개(새 1, 테스트 따로)로 큼 — 문장·버튼·이동·창 열기가 하나라도 빠지면 쓸 수 없는 한 흐름. 대부분 운행 때 만든 길에 지출 칸을 더하는 몇 줄.

### 안 건드릴 것
- 서버 함수(24-C-1 끝), `ExpenseFormModal.jsx`(199줄 — 처음 값은 훅에서 넣어 화면 파일은 그대로), `AppShell.jsx`(198줄), 지출 저장 길(`saveExpenseDraft`·`expensesSave.ts`), 운행 쪽 `useAssistantPrefill.js`, 차량 유지비 화면(`MaintFuelPage`), 질문(24-D).

### 검증
- 위 테스트(실제 누르기·이동). 기존 테스트 전부 통과·경고 늘지 않음(특히 운비서·일지·지출 창). 타입·빌드·의존 방향은 CI.
- 플레이북: 지출 저장 길은 안 바꾸지만 일지 화면·지출 창 여는 훅을 건드림 → §2(호출 경로) 확인 — 채운 값은 화면 상태일 뿐 [저장] 전엔 어디에도 안 남음.
- 브라우저(보리 계정): "어제 경유 12만원" → [기록하기] → 어제 일지 주유 창 120,000 → [저장] → 차량 유지비에 1건. "통행료 5천원" → 기타 창·통행료. 휴무 날 → 질문·[휴무 취소하고 기록].

### 파일 길이
- 위 "건드릴 파일" 예상 — 모두 200 이하. `ExpenseFormModal.jsx`(199)·`AppShell.jsx`(198)는 안 건드림(감시 목록). `DayLogPage.jsx`는 약 180.

### 코드에 넣을 주석(최종 문구)
- `useAssistantExpense.js`: `// 운비서가 넘긴 지출로 그 종류 지출 창을 채워 한 번 연다(로드맵 24-C-2). 저장은 지금 지출 창 그대로.`
- `useExpenseForm.js`(`openAdd` 위): `// preset — 운비서가 채운 처음 값(빈 칸은 기본값 그대로)`

### 실패 시 처리
- 새 저장 장치·재시도 없음(AGENTS §7). 기대와 다르면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 진행 기록
- 2026-10-23 터미널 AI 교차검증 — 지시서의 파일·줄 수·현재 동작이 모두 코드와 맞음(`AppShell.jsx:156`이 `call.date`로 주소를 만들고 이동 상태는 `assistantCallNavState`가 고름 → AppShell 안 건드려도 됨 확인). 정한 것: ① 서버가 `{ kind: 'expense' }`만 보내고 지출 칸이 없으면 운행과 같이 "알아듣지 못했습니다". ② 문장 항목이 "기타" 종류에 분류·항목명이 없으면 "기타". ③ 같은 날짜 다시(목표 5)는 처음 들어올 때와 같은 한 길로 — 열린 입력 칸이 없으면 확인 창이 바로 통과. ④ 일지 쪽 새 훅은 앱 틀 파일을 가져올 수 없어(의존 방향) 지출 칸 타입을 훅 안에 따로 적음(운행 쪽 `useAssistantPrefill.js`와 같은 방식).
- 결과: `unbiseoParse.ts` 152·`useUnbiseoAssistant.ts` 85·`unbiseoContext.ts` 35·`HomeAssistantPanel.tsx` 78·`assistantCallState.js` 88·`MainPageRoute.jsx` 112·새 `useAssistantExpense.js` 48·`useExpenseForm.js` 77·`DayLogPage.jsx` 179 — 모두 200 이하. `AppShell.jsx`·`ExpenseFormModal.jsx`·`useAssistantPrefill.js`·저장 길 안 바뀜.
- 기존 테스트 중 바꾼 것(동작이 바뀌어서, 약화 아님): "지출은 종류만" → 지출 칸이 없으면 알아듣지 못함 목록에 넣음, "지출 기록은 곧…" 문구 2곳 → 새 지출 문장, "지출·실패에는 버튼 없음" → "질문·실패에는 버튼 없음"(지출은 이제 버튼이 있음 — 새 시험으로 확인).
- 시험: 전체 884 + 744 모두 통과, 타입 검사·빌드·의존 검사 통과, lint 경고 49 그대로. 새·바뀐 테스트 18개 중 15개는 고치기 전 코드로 돌리면 실패, 3개(휴무 그대로·다시 그리면 안 열림·질문에는 버튼 없음)는 안전 확인, 복구 확인. 플레이북 §2: 채운 값은 지출 창 화면 상태일 뿐 [저장] 전엔 어디에도 안 남음, 저장은 기존 `saveExpenseDraft`, "휴무 취소"만 운행과 같은 저장.
- 코드 커밋 react-app `b736571`(2026-10-23, 13개 파일 — 운영 9·테스트 4, push 안 함). 남은 것: 보리 push → CI → 브라우저 확인(아래 1~4) → §5 리뷰 → 최종 승인.
- 보리 push 뒤 CI verify·deploy 초록(run 39882898397). 데스크톱 AI 배포본 확인(보리 계정, 매출 화면 운비서 버튼 → 글로 "어제 경유 12만원"): "10월 22일 주유, 120,000원으로 기록할게요." + [기록하기] → `/app/day/2026-10-22`, "주유 내역 추가" 창에 날짜 2026.10.22·종류 주유·비용 120,000·결제 카드, 운비서는 작은 버튼으로. [취소]로 닫음(저장 안 함 — 그날 기존 주유 내역 그대로). 휴무·현금·정비 창은 보리 휴대폰 확인 몫.
- §5 리뷰(데스크톱 AI): ① 범위 — 13개 파일(운영 9·테스트 4), 지시서 "건드릴 파일"과 같음, `AppShell.jsx`·`ExpenseFormModal.jsx`·`useAssistantPrefill.js`·저장 길 안 바뀜 ② 몰래 증설 없음(채운 값은 지출 창 화면 상태, 휴대폰 저장 0) ③ 타입 꼼수 0 ④ 200줄 — 최대 `DayLogPage.jsx` 179 ⑤ 테스트 — 지운 줄은 "지출은 곧…"·"지출엔 버튼 없음"처럼 동작이 바뀐 확인을 새 확인으로 바꾼 것(진행 기록), 새·바뀐 18개 중 15개는 고치기 전 코드에서 실패 ⑥ 공용 영향 — `openAdd`의 두 번째 값은 운비서만 넘김(다른 부르는 곳은 그대로 빈 창) ⑦ 목표 1~6 충족(진행 기록의 정한 것 4가지 포함). 남은 것: 보리 휴대폰 확인 → 최종 승인.

### 사용자 확인 방법
1. 운비서에 "어제 경유 12만원" → "10월 ○일 주유, 120,000원으로 기록할게요." + [기록하기].
2. 누르면 어제 일지로 가서 차량 유지비 주유 입력 창이 120,000원으로 채워진 채 열림 → [저장] → 지출 1건.
3. "엔진오일 교환 15만원 현금" → 정비 창, 항목명·분류 소모품·현금. [닫기]면 아무것도 안 생김.
4. 휴무 날짜로 말하면 "휴무를 취소하고 지출을 기록할까요?" + [휴무 취소하고 기록].

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
