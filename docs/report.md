# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 4-F — 이력형 주석 놓친 곳 마무리 `[x]`

보리 결정 2026-10-11 "추천대로"(4-E-2 결과에서 찾은 범위 밖 2곳을 작은 슬라이스로). 앞 슬라이스 4-E-2 `[x]`(react-app `ce6e118`). 4번(이력형 주석)의 진짜 마지막.

### 쉬운 요약
- 앞 슬라이스들의 검색 방식이 놓친 작업 기록 주석을 마저 지운다. 처음 찾은 2곳에, 검색을 넓혀 더 찾은 5곳을 더한 7곳.
- 그중 1곳(`useExpenseForm.js`)은 "아직 검사 대상이 아니라서"라는 지금과 다른 설명과, 그 설명 때문에 붙여 둔 쓸모없는 타입 표시도 같이 지운다(4-D-2의 기사 연동 화면과 같은 경우).
- 동작은 그대로다.

### 현재 상태 (코드 확인)
- 검색을 `/** … */` 한 줄 주석·영어 "slice"·"이번 슬라이스" 같은 표현까지 넓혀 운영 코드 전체를 다시 봄. 상태 조각 뜻의 "슬라이스"(`expenseTypes.js:36` 등)와 코드 이름(`WithdrawStep`·`.slice(`)은 제외.
- `useExpenseForm.js:17~20`: "store/app-store.js가 아직 @ts-check 대상이 아니라 … 좁힌다" — **지금은 사실이 아님**(근거: `store/app-store.js:1` `// @ts-check`, `store/ownerDataHooks.js:28` `useOwnerExpenses` `@returns {Array<ExpenseItem>}`).
- `owner-state.js:97~98`: "cloudSync.js의 collectPracticeSnapshot과 같은 모양" — 그 파일·함수 없음(근거: `collectPracticeSnapshot` 검색 결과 이 주석 1곳뿐).

### 목표 상태 (기대 동작)
1. 아래 표대로 7곳 주석이 바뀐다.
2. `useExpenseForm.js`의 `/** @type {Array<ExpenseItem>} */ (useOwnerExpenses(ownerKey))` → `useOwnerExpenses(ownerKey)`. 실행 코드는 같다(괄호·주석만 빠짐), 타입 검사로 확인.
3. 운영 코드 전체를 넓힌 검색으로 다시 봤을 때 작업 기록 주석 0곳.

| 위치 | 바뀔 문구(최종) |
|---|---|
| `components/SideMenu.jsx:14` | `/** 메뉴를 처음 열 때 그림 칸이 비지 않게 앱이 뜬 뒤 미리 받아 둔다. */` |
| `components/cars/CarDriverConnectPanel.jsx:7` | ` * 서브차량 "기사 연동 / 운행일지" 패널.` |
| `components/calendar/CalendarPage.jsx:43` | `  // 서브차량 실거리: subDistanceOn 설정이 없어 메인만.` |
| `components/day-log/useExpenseForm.js:17~21` | 주석 4줄 삭제, `const expenses = useOwnerExpenses(ownerKey)` |
| `domain/driverIncomeDeductions.js:39` | ` * 기사차량 폼 draft → 저장 필드(새 필드 4개 + 기존 insuranceOn).` |
| `lib/hydrateEmployedDriver.js:9` | `// 나머지는 버린다(다중 배정 화면·집계를 만들 때 함께 처리).` |
| `store/owner-state.js:97~98` | ` * owner 전체 상태를 스냅샷으로 원자적으로 교체한다. 각 필드는 있을 때만 반영 — 부분 스냅샷도` (다음 줄부터 그대로) |

### 건드릴 파일 (7개)
`src/components/SideMenu.jsx`, `src/components/cars/CarDriverConnectPanel.jsx`, `src/components/calendar/CalendarPage.jsx`, `src/components/day-log/useExpenseForm.js`, `src/domain/driverIncomeDeductions.js`, `src/lib/hydrateEmployedDriver.js`, `src/store/owner-state.js`
- 공용 스타일·값 변경 없음.

### 안 건드릴 것
- 상태 조각 뜻의 "슬라이스" 주석(`expenseTypes.js:36`, `financeTaxInvoiceEntries.js:34`, `ownerProfileDriversHooks.js:2`), `directMutations.js:101`의 DB 변경 파일 안내, 테스트 파일.

### 검증
- 7개 파일 HEAD 판과 비교(주석·공백만 제거, 이름 유지) 글자 하나까지 같은지.
- `typecheck` 오류 0(목표 2의 근거), 엄격 검사 목록 438 이하, `npm test`·`build` 통과.

### 파일 길이
- 모두 줄어들거나 같음(`useExpenseForm.js` 4줄 줄어듦). 200줄 넘는 파일 없음.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 실패하면 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법 (휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
1. 일지에서 주유·정비 지출 1건 추가 → 목록에 보이고 껐다 켜도 그대로면 성공.
2. 사이드 메뉴 열기 → 위쪽 그림이 전처럼 보이면 성공.

### 진행 결과
- 표대로 7개 파일 주석 정리(7줄 추가·12줄 삭제), 못 찾은 문구 0.
- 목표 2: `useExpenseForm.js` 설명 4줄·쓸모없는 타입 표시 삭제 → `typecheck` 오류 0으로 `useOwnerExpenses`의 타입이 그대로 통함 확인.
- **실행 코드 같음 확인**: 7개 파일 모두 HEAD 판과 비교(주석·공백만 제거, 이름 유지) 글자 하나까지 같음.
- 목표 3: 넓힌 검색(로드맵 N·이번/이 슬라이스·slice X·Step N·재감사·바닐라·보리 지시·사용자 지시·migration-·AGENTS·mockup·cloudSync.js·FAIL 지적)으로 운영 코드 전체 주석 0곳.
- 로컬 `npm test` 전부 통과(단위 832 / 화면 365), 엄격 검사 목록 438 그대로, `build` 성공.
- react-app `fca9751`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-11).
