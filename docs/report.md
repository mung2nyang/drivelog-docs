# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 101 지출 입력 흐름 두 벌을 한 벌로 + `domain/expenses.js`(203줄) 나누기 — 분리설계 + 101-A·101-B

> **결정 `[확인: 2026-10-21 보리 — "권하는대로 101-A, 101-B 둘로 나눠서"]`**: 확인할 점 1 권함대로 — 이 분리설계는 한 번에 승인, 만드는 일은 **101-A(파일 나누기, 경로만) 먼저 → 101-B(흐름 합치기)** 두 슬라이스. 슬라이스마다 코드 커밋·`[x]` 문서 커밋 따로. 101-B는 101-A `[x]` 뒤 이 설계 그대로 짧은 지시서로.
> 다음: 터미널 AI가 교차검증 뒤 **101-A만** 착수(아래 "건드릴 파일"의 101-A 줄까지).

> **101-A 진행(2026-10-21) — 코드 커밋 `f9cd2ec`** `[x]`: 보리 "착수지시서 확정,작업진행해".
> - 교차검증(AGENTS §1): 203줄·옮길 함수 4개 줄 위치(53~144)·가져다 쓰는 곳 모두 지시서와 같음. 지시서와 다른 점: `domain/expenses.test.js` 첫 묶음 "정비/기타 저장 조건"에 옮길 함수 테스트 5개와 남는 함수(`groupExpensesByDate`·`dedupeExpensesById`) 테스트 2개가 섞여 있어, **같은 묶음 이름으로 두 파일에 나눔**(새 파일엔 5개, 옛 파일엔 2개). "로드맵 77"(지출 번호, `dedupeExpensesById`도 씀)·"checkExpenseDraft" 묶음은 새 파일로.
> - 한 일: 새 `domain/expenseDraft.js`(99줄 — 머리 주석 1줄, `todayKey`·분류 상수는 `expenses.js`에서 가져옴, 함수 4개와 그 위 주석 2줄 그대로). `domain/expenses.js` 203 → **108줄**(머리 주석에 "입력값·검사·목록 고치기는 expenseDraft.js", 안 쓰이게 된 `generateLocalId` 가져오기·`ExpenseDraft` 타입 설명 지움). 경로만: `useExpenseForm.js`, `MaintFuelPage.jsx`(가져오기 4줄 → 2줄, 155 → 153줄), `ExpenseFormModal.jsx`(198 → 199줄), `owner-state.persistRoundtrip.test.js`.
> - 대조: 옮긴 92줄이 옛 53~144줄과 글자까지 같음(diff 0). 테스트·단언 줄을 옛 파일과 새 두 파일 합으로 비교 — 다른 것은 묶음 이름 줄 하나가 두 파일에 있는 것뿐. 테스트 수 unit 866·app 661 그대로.
> - 결과: `npm test` 1527 전부 통과, 타입 검사·빌드·`npm run depcheck`(위반 0) 통과, lint 경고 50 그대로. 빌드 결과(이름 해시 뺌) 49개 파일 이름 같음, `index.js`만 다름 → 압축 없이(`--minify false`) 옛 커밋과 새 코드를 빌드해 줄을 정렬 비교하니 **다른 줄 0**(함수 순서만 바뀜).
> - 개발 서버 확인은 안 함(101-A 검증 항목에 없고, 빌드 내용이 줄까지 같음).
> - 코드 커밋 react-app `f9cd2ec`(push 안 함).
> - push 뒤 CI verify·deploy 초록(run 39549745495) — 테스트·타입 검사·빌드·의존 방향 검사 모두 성공.
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서 101-A 줄의 8개 파일 ②새 층·저장 키 0, 새 파일 2개(`expenseDraft.js`·그 테스트)는 지시서대로 ③타입 꼼수 0 ④`expenses.js` 203 → 108·`expenseDraft.js` 99·`ExpenseFormModal.jsx` 199·`MaintFuelPage.jsx` 153 ⑤테스트 수 10 = 10, 단언 줄 31 = 31(옛 파일 대 새 두 파일 합) ⑥공용 영향: `expenseDraft.js` → `expenses.js`(상수·`todayKey`) 한 방향만, 다른 기능이 `expenses.js`에서 가져오는 것(`todayKey`·`KINDS`·`expenseTitle`·`dedupeExpensesById`) 그대로 ⑦목표 1 대조. 옮긴 본문을 데스크톱에서 따로 비교 — 옛 53~144줄과 커밋된 `expenseDraft.js` 8줄째부터가 같음(줄 끝 문자만 다른 것 빼고 diff 0). 경로 바뀐 4개 파일은 가져오기 줄만.
> - 배포본 로그인 확인(데스크톱 AI, 새 빌드 `index-BcQU6fQj.js`): 차량 유지비 [+ 추가] → 빈 칸 저장 "항목명 또는 비용을 입력해 주세요."(검사 문구) → "확인용정비" 저장 "내역을 등록했습니다."·10월 21일 묶음 생김 → 삭제 "내역을 삭제했습니다."·원래 10월 10일 소모품 50,000원만 남음(넣은 데이터 되돌림). 운행일지 10월 21일 [+ 정비 추가] → 정비 항목명 칸 열림 → 취소. 콘솔 오류 0건.
> - 보리 최종 승인(2026-10-21). 다음 101-B(이 설계 그대로 짧은 지시서).

보리 지시 "작성해"(2026-10-21). 로드맵 101 `[확인: 2026-10-21 보리 — 97·99 착수 때 "권하는대로", 99 다음·100 앞]`. 순서 99 끝 → **101** → 100 → 91 → 94.

### 쉬운 요약
- 차량 유지비 화면과 운행일지의 비용 칸이 "내역 고치기 창 열기 → 최신 목록 다시 읽기 → 검사 → 저장 → 실패하면 알림"을 **각자 똑같이** 갖고 있습니다. 이 공통 부분을 한 곳에 두고 두 화면이 같이 쓰게 합니다.
- 지출 계산 파일(`domain/expenses.js`)이 200줄을 넘어서, "입력값 만들기·검사·목록 고치기"를 새 파일로 나눕니다.
- 화면 모양·알림 문구·저장 결과는 그대로입니다. 두 화면의 진짜로 다른 점(일지는 날짜 고정·종류 고르기 창, 유지비는 날짜 자유·삭제 알림)은 각자 남깁니다.

### 현재 상태 (코드 확인)
- `domain/expenses.js` 203줄, §6 예외 표시 없음. 섞인 것: ① 상수(`KINDS`·분류 3개) ② 날짜·달 계산(`todayKey`·`monthPrefix`·`filterMonth`·`monthTotal`) ③ **입력값·검사·목록 고치기**(`emptyExpenseDraft` 58~75·`checkExpenseDraft` 84~89·`upsertExpense` 96~136·`removeExpense` 142~144, 약 90줄) ④ 화면 표시(`groupExpensesByDate`·`expenseTitle`·`filterByDate`) ⑤ 불러오기용 `dedupeExpensesById`.
- ③을 가져다 쓰는 곳: `features/day-log/hooks/useExpenseForm.js`, `features/expenses/ui/MaintFuelPage.jsx`, `features/expenses/ui/ExpenseFormModal.jsx`(`checkExpenseDraft`, 지금 198줄), 테스트 `domain/expenses.test.js`·`data/store/owner-state.persistRoundtrip.test.js`.
- 두 벌인 흐름:
  - 창 열 때 내역 → 입력값 바꾸기 11줄: `MaintFuelPage.jsx` `openEdit` ↔ `useExpenseForm.js:53~65`. 다른 점은 날짜(유지비는 내역 날짜, 일지는 그날로 고정)와 `liters`를 글자로 바꾸는지뿐.
  - 저장: 최신 목록 다시 읽기(`readOwnerExpenses`) → `upsertExpense` → 검사 문구 알림 → `saveExpenses` → 실패면 콘솔 + `saveFailMessage(getCloudOwnerKey() === ownerKey)` 알림 → 성공이면 창 닫고 "내역을 등록/수정했습니다." — 양쪽 같음.
  - 삭제: 최신 목록에서 빼고 저장, 실패면 같은 실패 알림. 다른 점: 유지비만 성공 알림 "내역을 삭제했습니다.", 콘솔 앞말이 유지비는 `비용 저장 실패:`·일지는 `비용 삭제 실패:`.
- 두 기능 폴더(`expenses`·`day-log`)가 같이 쓸 코드는 기능 폴더에 둘 수 없음(층 규칙·허용 예외 8곳 — 98-B). 여러 기능이 쓰는 저장 함수 자리는 `data/sync`(59-D에서 정한 규칙).
- 테스트: 유지비는 `MaintFuelPage.test.js` 7개(97), 일지 비용 저장·삭제 **실패**는 `App.guestDurable.test.js` 2개(콘솔 앞말 `비용 저장 실패:`·`비용 삭제 실패:`를 셈). 일지 비용 저장 **성공** 알림을 보는 테스트는 착수 때 확인(없으면 101-B 테스트 먼저에 더함).

### 분리설계 (AGENTS §6 — 책임 경계·모듈 구조·의존성)
**101-A `domain/expenses.js` 나누기** — 203 → 약 110
- 새 `domain/expenseDraft.js`(약 95줄): `emptyExpenseDraft`·`checkExpenseDraft`·`upsertExpense`·`removeExpense`를 **내용 그대로** 옮김("입력값 만들기·저장 전 검사·목록 고치기"). 분류 상수는 `expenses.js`에서 가져옴(`domain` → `domain`).
- `domain/expenses.js`에 남는 것: 상수·날짜·달 계산·화면 표시·`dedupeExpensesById`. 머리 주석에 "입력값·검사는 expenseDraft.js" 1줄.
- 가져오는 경로만 바뀌는 곳: `useExpenseForm.js`·`MaintFuelPage.jsx`·`ExpenseFormModal.jsx`(198줄 — 가져오기 줄이 둘로 나뉘어 199줄, 200 이하)·테스트 2개. `domain/expenses.test.js`에서 옮긴 함수의 테스트 묶음은 새 `domain/expenseDraft.test.js`로 그대로 옮김(테스트·단언 수 같음). 동작 변화 0.

**101-B 지출 입력 흐름 한 벌로** — 공통 부분만 아래층으로
- `domain/expenseDraft.js`에 `expenseToDraft(item, date = item.date)`(약 15줄): 내역 → 입력값. 일지는 그날, 유지비는 내역 날짜를 넘김. `liters`는 일지처럼 글자로(유지비 칸 표시는 같음).
- `data/sync/expensesSave.js`(58 → 약 90줄)에 두 함수: `saveExpenseDraft(ownerKey, draft, editingId)`·`deleteExpense(ownerKey, id)`. 둘 다 최신 목록을 다시 읽고(`readOwnerExpenses`) → `upsertExpense`/`removeExpense` → `saveExpenses`. **실패 알림 문구를 돌려줌**(검사 문구 또는 `saveFailMessage(...)`, 성공이면 `null`), 콘솔은 저장 `비용 저장 실패:`·삭제 `비용 삭제 실패:`. 알림 띄우기·창 닫기·성공 알림은 화면 몫으로 남김(`data` → `shared`는 실패 문구 함수만 — 이미 11곳에서 씀).
- `useExpenseForm.js`(100 → 약 75)·`MaintFuelPage.jsx`(155 → 약 125): `persist`·`openEdit` 본문·`save`·`remove` 본문이 위 함수 부르기 + 알림으로 줄어듦. 일지의 종류 고르기 창(420ms 뒤 열기)·날짜 고정, 유지비의 삭제 성공 알림은 그대로.
- 새 공용 훅은 만들지 않음 — 두 화면의 창 상태가 다르고(일지는 종류 고르기 + 인라인 칸, 유지비는 모달), 같은 것은 "읽고·고치고·저장·실패 문구"뿐이라 함수로 충분. 기능 사이 허용 예외도 안 늘어남.

### 목표 상태 (기대 동작)
1. (101-A) `domain/expenses.js` 200줄 이하, `domain/expenseDraft.js` 200줄 이하, 옮긴 함수 본문이 옛 줄과 같음. 테스트 수·단언 수 그대로.
2. (101-B) 내역 → 입력값 바꾸기, 저장·삭제 흐름이 `domain/expenseDraft.js`·`data/sync/expensesSave.js` 한 곳에만 있음(grep `readOwnerExpenses(ownerKey)`가 두 화면 파일에서 0).
3. 알림 문구·저장 값·창 닫힘·화면 모양 변화 0. 바뀌는 것은 유지비 **삭제 실패 콘솔 앞말**이 `비용 저장 실패:` → `비용 삭제 실패:`(화면에 안 보임, 일지와 맞춤) 하나.
4. `npm run depcheck` 통과(허용 예외 목록 변경 없음).

### 건드릴 파일
- 101-A: `domain/expenses.js`, 새 `domain/expenseDraft.js`, 경로만 `features/day-log/hooks/useExpenseForm.js`·`features/expenses/ui/MaintFuelPage.jsx`·`features/expenses/ui/ExpenseFormModal.jsx`, 테스트 `domain/expenses.test.js`·새 `domain/expenseDraft.test.js`·`data/store/owner-state.persistRoundtrip.test.js`(경로)
- 101-B: `domain/expenseDraft.js`, `data/sync/expensesSave.js`, `features/day-log/hooks/useExpenseForm.js`, `features/expenses/ui/MaintFuelPage.jsx`, 테스트(`domain/expenseDraft.test.js`에 `expenseToDraft`, 새 `data/sync/expensesSave.draft.test.js`, 필요하면 일지 비용 성공 테스트)

### 안 건드릴 것
- `saveExpenses` 본문(서버 칸 나누기), 불러오기(`data/hydrate`), `ExpenseFormModal`의 칸·검사 표시, 일지 `DayLogExpenses`·`ExpenseSelectPanel`·`inlinePanelActions`, `todayKey`를 다른 기능이 `domain/expenses.js`에서 가져오는 것(알림·일상점검표 — 그대로 둠), 100·91번.

### 검증
- 101-A: `git diff -M`로 옮긴 함수 본문이 같은지, 테스트 수·단언 수 그대로. `npm test`·타입 검사·빌드·`depcheck`.
- 101-B: **고치기 전** 새 `expensesSave.draft.test.js`는 새 함수라 옛 코드엔 없으므로, 대신 화면 테스트로 옛 동작을 고정 — `MaintFuelPage.test.js` 7개 + `App.guestDurable.test.js` 비용 실패 2개(콘솔 앞말 셈 포함) + 일지 비용 저장 성공 테스트(없으면 먼저 붙여 옛 코드에서 통과)가 옛 코드·새 코드 모두 통과. 유지비 삭제 실패 콘솔 앞말을 세는 테스트가 있으면 그 기대값만 `비용 삭제 실패:`로(진행 기록에 적음). 새 함수 테스트: 검사 문구 돌려줌·저장 실패면 실패 문구·성공이면 `null`·최신 목록 기준으로 고침.
- 배포본 로그인 확인(데스크톱 AI): 차량 유지비 추가 → 수정 → 삭제, 운행일지 비용 칸 정비 추가 → 수정 → 삭제(넣은 데이터 되돌림), 알림 문구 확인.

### 파일 길이
- `domain/expenses.js` 약 110, `domain/expenseDraft.js` 약 95(B 뒤 약 110), `data/sync/expensesSave.js` 약 90, `useExpenseForm.js` 약 75, `MaintFuelPage.jsx` 약 125, `ExpenseFormModal.jsx` 199. 테스트 파일은 §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(저장은 지금 `saveExpenses` 그대로, 실패는 지금처럼 알림만).
- 옛 동작을 고정한 테스트가 옛 코드에서 실패하면 화면을 고치지 말고 멈추고 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법
- 화면은 바뀌지 않음. push 뒤 AI가 CI와 배포본(차량 유지비·운행일지 비용 칸 추가·수정·삭제)을 확인해 보고.

### 확인할 점
1. **한 슬라이스로 할까요, 둘로 나눌까요?** **권함: 둘로 — 101-A(파일 나누기, 경로만 바뀌는 기계적 일) 먼저, 101-B(흐름 합치기) 다음.** A는 동작 변화가 0이라 검사가 쉽고, B는 저장 흐름을 바꾸니 따로 보는 게 안전합니다(§3 "1~3개 파일"에도 가까움). 다른 안: 한 번에(커밋 하나, 파일 약 11개).
