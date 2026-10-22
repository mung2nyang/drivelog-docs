# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 101-B 지출 입력 흐름 한 벌로 `[x]`

보리 지시 "진행해"(2026-10-21). 101 분리설계는 승인됨(`git show e1c2775:docs/report.md`의 "분리설계" 절, `[확인: 2026-10-21 보리 — "권하는대로 101-A, 101-B 둘로"]`). 101-A `[x]`(react-app `f9cd2ec`) 뒤 그 설계 그대로. 다음은 102(부가세 버림).

> **진행(2026-10-22, 데스크톱 AI — 터미널 AI가 첫 단계에서 멈춰 보리 지시 "101-B 이어서 해줘"로 이어받음)** `[x]` 코드 커밋 react-app `3aa8bfa`, CI 초록, 보리 최종 승인(2026-10-22).
> - 터미널 AI가 남긴 것: 일지 비용 저장 성공 테스트(`App.guestDurable.test.js`) + 확인용 줄. 확인용 줄 걷어냄.
> - 지시서와 다른 점 2개: ① 그 테스트의 "창 닫힘" 확인을 `#expenseName` 없음 → 칸 `.maint-fuel-inline-host`의 `aria-hidden="true"`로(일지 칸은 닫히는 애니메이션이 끝날 때까지 내용을 남기고, 테스트 환경엔 애니메이션 끝이 없음. 옛 확인은 틀리면 화면 요소 전체를 글로 풀다 메모리가 넘쳐 다른 테스트 뒤에서 멈췄음). ② `MaintFuelPage.test.js`가 저장 파일 전체를 가짜로 바꿔 끼우던 것을 실제 저장 함수 + 기기 저장소 지출 키 쓰기 막기로(가짜엔 새 함수가 없어 불러오기부터 실패) — 7개 테스트 내용은 그대로.
> - 테스트 먼저: 일지 성공 테스트가 바꾸기 전 코드에서 통과 확인 뒤 코드 수정.
> - 한 일: `expenseToDraft`(domain/expenseDraft.js 99 → 116), `saveExpenseDraft`·`deleteExpense`(data/sync/expensesSave.js 58 → 101, 최신 목록은 `getState()`로), `useExpenseForm.js` 100 → 75, `MaintFuelPage.jsx` 153 → 126. 목표 4 grep(`readOwnerExpenses`·`upsertExpense`·`saveExpenses`가 두 화면 파일에서) 0. 새 테스트: `expenseToDraft` 2개, `data/sync/expensesSave.draft.test.js` 6개.
> - 결과: `npm test` unit 874·app 662 전부 통과, 타입 검사·빌드·`npm run depcheck`(위반 0) 통과, lint 경고 50 그대로(바뀐 파일엔 0).
> - 브라우저 확인(2026-10-22, 데스크톱 AI, 개발 서버 5174, 비회원 — 보리 지시): 차량 유지비 빈 칸 저장 "항목명 또는 비용을 입력해 주세요." → "확인용정비" 12,345원 저장 "내역을 등록했습니다."·목록에 생김 → 수정 창에 내역 날짜·값 그대로, 이름 바꿔 저장 "내역을 수정했습니다." → 삭제 "내역을 삭제했습니다."·"이번 달 등록된 정비 내역이 없습니다.". 운행일지 10월 22일 [+ 정비 추가] → "일지확인용" 5,000원 저장 "내역을 등록했습니다."·칸 닫힘·기기 저장 날짜 2026-10-22 → 수정 칸 날짜 2026-10-22 잠김, 6,000원 저장 "내역을 수정했습니다." → 삭제하면 빠짐(성공 알림 없음 — 전과 같음). 콘솔·서버 오류 0건. 넣은 데이터와 비회원 표시는 브라우저 저장소에서 지워 되돌림.
> - 코드 커밋 react-app `3aa8bfa`(2026-10-22, 101-B 파일 8개만, push 안 함 — push는 보리).
> - push 뒤 CI verify·deploy 초록(run 39707905479).
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서 "건드릴 파일" + `MaintFuelPage.test.js`(지시서와 다른 점 ②로 적음) ②새 파일은 지시서의 `expensesSave.draft.test.js` 하나, 새 저장 키·층 0 ③타입 꼼수 0(커밋 추가 줄 grep) ④`expenseDraft.js` 116·`expensesSave.js` 101·`useExpenseForm.js` 75·`MaintFuelPage.jsx` 126 ⑤지운 테스트·단언 0(바뀐 건 가짜 저장 파일 줄과 일지 "닫힘" 확인 방법) ⑥새 함수 3개를 부르는 곳은 두 화면뿐(grep), `saveExpenses` 본문 그대로 ⑦목표 1~6 대조 — 4번 grep 0, 6번 depcheck 위반 0.
> - 다음 작업 커밋에 같이 넣을 것(보리 지시 2026-10-22): react-app의 `CLAUDE.md`, `privacy-draft/privacy.html`, 지워진 `design-assets/assistant/unbiseo-basic-original.png`.
> - 보리 최종 승인(2026-10-22). **101번 끝.** 다음 102(부가세 버림).

### 쉬운 요약
- 차량 유지비 화면과 운행일지 비용 칸이 "고치기 창 열 때 내역 옮겨 담기, 최신 목록 다시 읽고 저장, 실패하면 같은 알림"을 각자 똑같이 갖고 있습니다. 이 부분을 한 곳에 두고 두 화면이 같이 씁니다.
- 화면 모양, 알림 문구, 저장되는 값은 그대로입니다. 두 화면이 진짜로 다른 점(일지는 날짜 고정·종류 고르기 창, 유지비는 날짜 자유·삭제 성공 알림)은 각자 남깁니다.

### 현재 상태 (코드 확인, react-app `f9cd2ec`)
- 내역 → 입력값 바꾸기 두 벌: `features/expenses/ui/MaintFuelPage.jsx:75~91` `openEdit`(날짜는 내역 날짜, `liters: item.liters || ''`) ↔ `features/day-log/hooks/useExpenseForm.js:53~65`(날짜는 그날 `dateKey`, `liters: String(item.liters || '')`). 나머지 칸 9개는 같은 식.
- 저장 두 벌: 둘 다 `readOwnerExpenses(ownerKey)` → `upsertExpense` → 검사 문구면 알림 → `saveExpenses` → 실패면 `console.error('비용 저장 실패:')` + `saveFailMessage(getCloudOwnerKey() === ownerKey)` 알림·창 유지 → 성공이면 창 닫고 "내역을 수정했습니다."/"내역을 등록했습니다."(`MaintFuelPage.jsx:93~102`, `useExpenseForm.js:77~91`). 넘기는 입력값만 다름: 유지비 `{ ...draft, kind: draft.kind || kind }`, 일지 `{ ...draft, date: dateKey }`.
- 삭제 두 벌: 최신 목록에서 빼고 저장, 실패면 같은 알림. 다른 점: 유지비만 성공 알림 "내역을 삭제했습니다."(`MaintFuelPage.jsx:105~108`), 실패 콘솔 앞말이 유지비 `비용 저장 실패:`(`:62`)·일지 `비용 삭제 실패:`(`useExpenseForm.js:72`).
- 빼는 목록 함수도 두 벌: 유지비 `removeExpense`(`domain/expenseDraft.js:97~99`), 일지는 같은 식을 직접 `filter`(`useExpenseForm.js:70`).
- `readOwnerExpenses`를 쓰는 화면 파일은 이 두 개뿐(grep, 그 밖은 백업·복원·내역서 — 범위 밖). `readOwnerExpenses`는 `getState().expenses[ownerKey] || EMPTY_EXPENSES`(`data/store/ownerDataHooks.js:21~23`)라, `data/sync/expensesSave.js`는 이미 쓰는 `getState()`로 같은 값을 읽을 수 있음(훅 파일을 안 불러도 됨).
- `data/sync`가 `shared/utils/failureMessages.js`를 부르는 곳이 이미 10개 파일(grep `failureMessages` in `data/`) — 층 규칙 안.
- 테스트: 유지비 `MaintFuelPage.test.js` 7개(등록·수정·삭제 성공 알림, 저장 실패 포함). 일지 비용 저장·삭제 실패 `app/App.guestDurable.test.js:551`·`:608`(콘솔 앞말을 셈). **일지 비용 저장 성공 알림 테스트는 없음**(grep "내역을 등록했습니다" — 유지비·알림 띄우기 테스트뿐).

### 목표 상태 (기대 동작)
1. `domain/expenseDraft.js`에 `expenseToDraft(item, date = item.date)` — 내역 → 입력값. `liters`는 `String(item.liters || '')`(일지 방식, 유지비 칸 표시·저장 값 같음 — `upsertExpense:61`이 `parseFloat(String(...))`로 읽음).
2. `data/sync/expensesSave.js`에 `saveExpenseDraft(ownerKey, draft, editingId)`·`deleteExpense(ownerKey, id)`. 둘 다 `Promise<string|null>` — 알림에 띄울 문구(검사 문구 또는 실패 문구), 성공이면 `null`. 최신 목록은 `getState().expenses[ownerKey] || []`로 다시 읽고 `upsertExpense`/`removeExpense` → `saveExpenses`. 콘솔 앞말은 저장 `비용 저장 실패:`·삭제 `비용 삭제 실패:`.
3. 두 화면은 위 함수를 부르고 알림만 띄움. 창 닫기·성공 알림·일지 종류 고르기 창(420ms)·날짜 고정·유지비 삭제 성공 알림은 화면에 그대로.
4. grep `readOwnerExpenses`·`upsertExpense`·`saveExpenses`가 `MaintFuelPage.jsx`·`useExpenseForm.js`에서 0.
5. 알림 문구·저장 값·창 닫힘·화면 모양 변화 0. 바뀌는 것은 유지비 **삭제 실패 콘솔 앞말** `비용 저장 실패:` → `비용 삭제 실패:` 하나(화면에 안 보임, 일지와 맞춤).
6. `npm run depcheck` 위반 0, 허용 예외 목록 그대로.

### 건드릴 파일
- `domain/expenseDraft.js` — `expenseToDraft` 추가
- `data/sync/expensesSave.js` — `saveExpenseDraft`·`deleteExpense` 추가(`upsertExpense`·`removeExpense`·`saveFailMessage` 가져오기)
- `features/day-log/hooks/useExpenseForm.js` — `persist` 지움, `openEdit`·`save`·`remove` 본문을 위 함수 부르기로
- `features/expenses/ui/MaintFuelPage.jsx` — 같음
- 테스트: `domain/expenseDraft.test.js`(`expenseToDraft`), 새 `data/sync/expensesSave.draft.test.js`(검사 문구·성공 `null`·실패 문구·콘솔 앞말), `app/App.guestDurable.test.js`(일지 비용 저장 성공 테스트 1개 — 551줄 실패 테스트와 같은 준비)

### 안 건드릴 것
- `saveExpenses` 본문(서버 칸 나누기), `upsertExpense`·`removeExpense`·`emptyExpenseDraft`·`checkExpenseDraft` 본문, `ExpenseFormModal.jsx`, 일지 `DayLogExpenses`·`ExpenseSelectPanel`·`inlinePanelActions`, `readOwnerExpenses`를 쓰는 백업·복원·내역서(`features/account/api/*`·`features/documents/api/reportSummary.js`), 새 공용 훅(두 화면 창 상태가 달라 함수로 충분 — 분리설계 결정).

### 검증
- **테스트 먼저**: 일지 비용 저장 성공 테스트를 바꾸기 전 코드에서 통과시켜 둠(지금 동작 고정). 기존 유지비 7개·일지 실패 2개는 그대로 통과해야 함(고치거나 약하게 하지 않음).
- 새 함수 테스트: 검사 문구, 성공 `null`·저장소 반영, 저장 실패 문구·저장소 그대로, 삭제 콘솔 앞말 `비용 삭제 실패:`.
- `npm test`·타입 검사·빌드·`npm run depcheck` 통과.

### 파일 길이
- `domain/expenseDraft.js` 99 → 약 117
- `data/sync/expensesSave.js` 58 → 약 90
- `features/day-log/hooks/useExpenseForm.js` 100 → 약 78
- `features/expenses/ui/MaintFuelPage.jsx` 153 → 약 125
- 전부 200줄 이하, 나누기 설계 필요 없음.

### 코드에 넣을 주석(최종 문구)
- `useExpenseForm.js` 3줄째: `// 화면은 useOwnerExpenses로 구독하고, 저장·삭제는 saveExpenseDraft·deleteExpense(최신 목록을 다시 읽어 고침).` — 27~31줄 `persist` 설명 주석은 `persist`와 같이 지움. 83~84줄 "모달을 안 닫는다" 주석은 남김.
- `expensesSave.js` 새 함수 위: `/** 입력값을 최신 목록에 반영해 저장. 알림 문구(검사·실패)를 돌려주고, 성공이면 null. */`, `/** 최신 목록에서 빼고 저장. 실패 문구를 돌려주고, 성공이면 null. */`
- `expensesSave.js` 머리 주석: `// 지출 저장(saveExpenses)과 두 화면이 같이 쓰는 입력값 저장·삭제. 화면 읽기는 useOwnerExpenses, 계산 규칙은 domain/expenses.js.`

### 실패 시 처리
- 새 저장 장치·재시도·임시 보관 없음. 저장 실패는 지금처럼 알림 + 창 유지 + 저장소 그대로. 검증에서 실패하면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 차량 유지비 → [+ 추가] → 빈 칸으로 저장: "항목명 또는 비용을 입력해 주세요."
2. 항목명·비용 넣고 저장: "내역을 등록했습니다.", 목록에 생김 → 눌러 고치고 저장: "내역을 수정했습니다." → 삭제: "내역을 삭제했습니다.", 목록에서 빠짐.
3. 운행일지 아무 날 → [+ 정비 추가] → 항목명·비용 넣고 저장: "내역을 등록했습니다.", 그날 정비 칸에 보임 → 고치기 창 날짜가 그날로 나옴 → 삭제하면 빠짐(성공 알림 없음 — 지금과 같음).
4. 넣은 확인용 내역은 지워서 되돌림.
