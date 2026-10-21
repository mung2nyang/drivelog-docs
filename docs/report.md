# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 98-A 여러 기능이 같이 쓰는 달력 부품을 아래로 — `shared/ui`·`data/store` `[x]`

> **진행(2026-10-21) — 코드 커밋 `d820a4d`, push·CI 대기**: 보리 "착수지시서 작업진행해줘, 정할건 권하는걸로 진행" → `[확인: 2026-10-21 보리]` 확인할 점 1은 권함대로(디자인 파일 3개는 이번에 안 옮기고 98-B 허용 예외 목록에 함께).
> - 교차검증(AGENTS §1): 부르는 곳 9개 파일·옮길 파일 4개·직접 검사 테스트 0개 모두 지시서와 같음. 고친 것 없음.
> - 한 일: `git mv` 4개(`shared/ui/CalendarDateSelect.jsx`·`calendar-date-select.css`·`SummaryInfoToggle.jsx`, `data/store/useMonthSettlement.js`), 옮긴 파일 안 상대 경로와 부르는 곳 9개 파일 경로만. `features/calendar/hooks` 폴더는 비어서 없어짐.
> - `git diff -M --numstat`: 4개 모두 이름 바뀜으로 잡힘. 경로 아닌 바뀐 줄 0(`useMonthSettlement.js` 5줄은 가져오기 4줄 + JSDoc 타입 경로 1줄).
> - 결과: `npm test` unit 866 + app 641 = 1507 전부 통과(수 그대로), 타입 검사·빌드 통과, lint 경고 50 그대로. 빌드 결과 48개 파일 이름·내용 모두 같음(해시 뺌, 묶음 이름 바뀜 없음).
> - 목표 2 grep — 남은 기능 사이 부르기 8곳뿐: 코드 `PersonalInfoPage:8`→`EmployerLinkCard`, `CallClientQuickAdd:4`→`ClientFormModal`, `DayLogExpenses:3`→`ExpenseFormModal`, `LinkedDriverClientsPage:10`→`ScopedClientsList`, `HomePage:9`→`useReceivablesData` / 디자인 `CarDriverConnectPanel:4`→`driver-connection.css`, `ScopedClientsList:7`→`linked-driver.css`, `LinkedDriverClientsPage:11`→`client-management.css`.
> - 목표 3 grep: `shared/`·`data/`가 `features/`·`app/`을 부르는 곳 0건.
> - 파일 길이: 경로 줄만 바뀜, `MaintFuelPage.jsx` 248 그대로.
> - 개발 서버(비회원, 거래처·콜 1건 넣은 뒤): 달력·서류 발급·매출·차량 유지비 날짜 고르기 2개씩, 높이 44px(옮긴 디자인 파일 적용), 눌러서 펼침·항목 21개. 달력 요약 (i)·세금계산서 탭 "홈택스 발급 방법" (i) 펼침, 홈 이번 달 정산 합계 330,000원 표시. 콘솔 오류 0건.
> - 코드 커밋 react-app `d820a4d`(push 안 함).
> - push 뒤 CI verify·deploy 초록(run 39530150588).
> - §5 리뷰(데스크톱 AI): ①범위 = 이름 바뀜 4·경로 9(지시서와 같음) ②새 파일·저장 키 0 ③타입 꼼수 0 ④경로 줄만(`MaintFuelPage` 248 그대로) ⑤테스트 1,428 그대로 ⑥공용 부품 — 쓰는 곳 전부 새 경로 ⑦목표 1~4 대조, `shared/`·`data/`·`domain/`의 위층 부르기 0건, 경로 아닌 바뀐 줄 0.
> - 배포본 로그인 확인(2026-10-21, 내장 브라우저): 묶음 파일 이름이 59-I2 배포와 같음(빌드 결과 같음과 맞음). 달력·서류 발급·매출·차량 유지비 모두 년도·월 고르기 단추 높이 44px(옮긴 디자인 파일 적용), 달력 요약 설명 토글 펼침(`aria-expanded` true), 홈 이번 달 정산 합계 2,645,513원 그대로, 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-21). 다음 98-B(허용 예외 목록 8곳 — 코드 5·디자인 3 — 과 의존 방향 검사 CI).

보리 "권하는대로"(2026-10-21) — 로드맵 98 `[확인: 2026-10-21 보리]`: 59번 끝에 남은 "기능 사이 부르기" 중 여러 기능이 같이 쓰는 부품을 아래층으로. 98-B(허용 예외 목록 + 의존 방향 검사 CI)는 다음. 옮기기·경로만, `git mv`, 동작 변화 0.
**지난 답변의 수 정정(데스크톱 AI)**: "공용 폴더로 옮길 8곳·예외 5곳"이라 했으나 다시 세니 **코드 부르기 11곳 + 디자인 파일 부르기 3곳 = 14곳** — 공용 부품 부르기 6곳, 화면 안 끼워 넣기 5곳, 차량·거래처·기사끼리 디자인 파일 3곳(아래 확인할 점 1).

### 쉬운 요약
- 날짜 고르기 창·요약 설명 토글·월 정산 계산 훅은 달력 폴더에 있지만 서류·매출·기사·차량 유지비·홈 화면도 씁니다.
- 공용 화면 부품은 `shared/ui`로, 월 정산 계산 훅은 앱 데이터 훅들이 있는 `data/store`로 옮깁니다.
- 화면 동작은 그대로입니다.

### 현재 상태 (코드 확인 — grep)
- `features/calendar/ui/CalendarDateSelect.jsx`(24줄, `shared/ui/AppDropdown`·같은 폴더 `calendar-date-select.css`만 부름) — 쓰는 곳: 달력 `CalendarHeader` + 서류 `MonthNavigator`·기사 `SettlementSummaryCard`·차량 유지비 `MaintFuelPage:5`·매출 `RevenueNav` (기능 밖 4곳).
- `features/calendar/ui/SummaryInfoToggle.jsx`(24줄, `shared/ui/InfoTip`만) — 쓰는 곳: 달력 `CalendarMonthSummary` + 서류 `TaxInvoiceTab:22` (기능 밖 1곳).
- `features/calendar/hooks/useMonthSettlement.js`(41줄, `domain/*`·`data/store/ownerDataHooks`만) — 쓰는 곳: 달력 `CalendarPage` + 홈 `HomePage:7` (기능 밖 1곳).
- 세 파일을 직접 검사하는 테스트 0개(grep).
- 그 밖 기능 사이 부르기(이번 범위 밖, 98-B 예외 후보): 일지 `CallClientQuickAdd:4` → 거래처 `ClientFormModal`, 일지 `DayLogExpenses:3` → 차량 유지비 `ExpenseFormModal`, 홈 `HomePage:9` → 미수금 `useReceivablesData`, 기사 `LinkedDriverClientsPage:10` → 거래처 `ScopedClientsList`, 계정 `PersonalInfoPage:8` → 기사 `EmployerLinkCard`. 디자인 파일: 차량 `CarDriverConnectPanel:4` → 기사 `driver-connection.css`, 거래처 `ScopedClientsList:7` → 기사 `linked-driver.css`, 기사 `LinkedDriverClientsPage:11` → 거래처 `client-management.css`.

### 목표 상태 (기대 동작)
1. `shared/ui/` ← `CalendarDateSelect.jsx`·`calendar-date-select.css`·`SummaryInfoToggle.jsx`. `data/store/` ← `useMonthSettlement.js`(이름 그대로).
2. 부르는 곳 경로 고침(달력 안 포함). 기능 사이 부르기에서 이 6곳이 없어짐 — 남는 기능 사이 부르기는 위 "그 밖" 목록 8곳(코드 5·디자인 3)뿐임을 grep으로 진행 기록에.
3. `shared/`·`data/`가 `features/`·`app/`을 부르는 곳 0건 그대로(새로 생기지 않음).
4. 테스트 수 그대로, 전부 통과. 빌드 결과 내용 같음(묶음 이름이 바뀌면 진행 기록에).

### 건드릴 파일
- 옮김(`git mv`) 4개, 경로만 고침: `CalendarHeader`·`CalendarMonthSummary`·`CalendarPage`(달력 안), `MonthNavigator`·`SettlementSummaryCard`·`MaintFuelPage`·`RevenueNav`·`TaxInvoiceTab`·`HomePage`, 옮긴 파일 안 상대 경로.
- 동작 코드·문구는 안 바꿈.

### 안 건드릴 것
- 98-B(허용 예외 목록·의존 방향 검사 CI), 위 "그 밖" 8곳(확인할 점 1).
- 97·99번(200줄 넘는 화면 나누기).

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 2·3 grep을 진행 기록에.
- `git diff -M --stat`: 4개가 이름 바뀜으로 잡히는지, 경로 아닌 바뀐 줄 목록.
- 빌드 결과 내용 비교(이름 해시 뺌).
- 개발 서버(비회원): 달력 월 고르기 창, 서류 발급·매출·차량 유지비의 날짜 고르기, 달력·세금계산서 요약 설명(i) 펼치기, 홈 이번 달 정산 합계 → 콘솔 오류 없음.

### 파일 길이
- 경로 줄만 바뀜. 200줄 넘는 `MaintFuelPage.jsx`(248)는 경로 한 줄만 — §6 예외(경로 줄, 보리 결정 2026-10-19).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(위치·경로만).
- 테스트가 하나라도 실패하면 멈추고 어느 경로인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 달력·서류 발급·매출·차량 유지비에서 날짜(월) 고르기 창 열기.
2. 달력 요약·세금계산서 탭 요약 설명 펼치기, 홈 이번 달 정산 합계 그대로.
3. 콘솔 오류 없음.

### 확인할 점
1. 차량·거래처·기사끼리 서로 부르는 디자인 파일 3개(`driver-connection.css`·`linked-driver.css`·`client-management.css`)는 지난 답변 때 제가 수에서 빠뜨렸습니다. **권함: 98-B의 허용 예외 목록에 함께 넣기** — 셋 다 그 기능 화면의 모양을 정하는 파일이라 공용으로 내리면 오히려 주인이 흐려지고, 화면 안 끼워 넣기 5곳과 같은 성격입니다. 다른 안: 이번에 `shared/styles`로 옮기기.
