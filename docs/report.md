# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-F1 기능 폴더 3 — 달력·홈·알림·차량 유지비를 `src/features/`로 `[x]`

보리 승인(2026-10-20, 59-E `[x]`). 59번 여섯 번째 조각. 59-D·E의 **자리 정하는 규칙** 그대로: ① 화면·부품·디자인 → `features/<기능>/ui/`, 훅 → `hooks/`, 그 기능만 쓰는 함수 → `api/` ② `lib/` 함수를 두 기능 이상(또는 앱 틀·`data/`)이 쓰면 → `data/sync/` ③ 다른 기능이 쓰는 부품·훅은 주인 기능에 두고 "기능 사이 부르기" 목록만(보리 결정 2026-10-20). 옮기기·경로만, `git mv`, 경로 줄만 바뀌는 200줄 넘는 파일은 §6 예외.
**계획 바꿈(보리 결정 2026-10-20, 아래 확인할 점 1)**: 59-F(달력·홈·알림·계정·설정·고객센터)는 조사해 보니 옮길 파일이 100개를 넘어, F1(달력·홈·알림 + 계획에 빠져 있던 차량 유지비)과 F2(계정·설정·고객센터)로 나눔. 이 지시서는 F1.

> **진행(2026-10-20)**: 보리 "착수지시서 확정,작업진행해". 코드 커밋 react-app `80821cb`(49개 파일: 옮김 38 + 경로 고침 11).
> - 교차검증(AGENTS §1): 표의 파일·쓰는 곳 모두 코드와 같음(옮길 파일 38개 — 달력 15·홈 10·알림 4 + `lib/notifications` 4·차량 유지비 5). `lib/notifications.js`는 알림 폴더 파일과 그 테스트만 부름(앱 틀 `AppShell`은 `useNotifications` 훅을 부름). 고친 것 없음.
> - 같은 대본으로 옮기기·경로 다시 계산(38개 파일). 손으로 고친 줄 1개: 주석 속 옛 경로 `domain/practiceSettings.js:12`(`lib/notifications.js` → `features/notifications/api/notifications.js`). `homeMonthCard.test.js`는 같은 폴더 `home.css`를 읽어 그대로. `package.json` 안 바뀜.
> - 목표 2: 옛 경로 grep 0건, 세 폴더 없어짐.
> - 목표 3: `shared/`가 `components/`·`features/`를 부르는 곳은 59-E 예외 아이콘(`shared/ui/CardActionButtons.jsx:2`)과 테스트 `PageHeader.test.js:18-19`뿐, 새로 생긴 것 없음.
> - 빌드 비교: 옮기기 전·후 48개 파일, 해시 지우고 비교 → 이름·내용 모두 같음.
> - `git diff -M`: 38개 모두 이름 바뀜으로 잡힘, 경로 아닌 바뀐 줄은 위 주석 1줄뿐.
> - `npm test` 926 + 577 = 1503 통과(합계 같음 — `lib/notifications` 테스트가 `features/`(app) 범위로), 타입 검사·빌드 통과.
> - 개발 서버(비회원, 운행 1건): 홈(할 일 미수금 1건·이번 달 합계), 달력(10월 → 월 고르기 창 → 11월로 바뀜), 알림 화면, 차량 유지비 정비·주유·기타 탭, [+ 추가] → "정비 내역 추가" 창 → [취소]로 닫힘, 콘솔 오류 없음.
> - 목표 5 "기능 사이 부르기"(59-D·E 목록에 더해짐): `drivers/ui/SettlementSummaryCard.jsx:2`·`revenue/ui/RevenueNav.jsx:3`·`documents/ui/MonthNavigator.jsx:4`·`expenses/ui/MaintFuelPage.jsx:5` → `calendar/ui/CalendarDateSelect.jsx` / `documents/ui/TaxInvoiceTab.jsx:21` → `calendar/ui/SummaryInfoToggle.jsx` / `home/ui/HomePage.jsx:7` → `calendar/hooks/useMonthSettlement.js`, `:9` → `receivables/hooks/useReceivablesData.js` / (테스트) `home/ui/HomePage.test.js:29` → `calendar/ui/CalendarPage.jsx`. 기능 밖에서: `components/day-log/DayLogExpenses.jsx:3` → `expenses/ui/ExpenseFormModal.jsx`, (테스트) `components/modalOutsideClick.test.js:19` → 같은 파일. 앱 틀(`AppShell`·`AppShellRoutes`·`lazyPages`·`MainPageRoute`)에서 기능 부르기는 허용 방향.
> - push 뒤 CI verify·deploy 초록(run 39388702159).
> - §5 리뷰(데스크톱 AI): ①범위 = 옮김 38(모두 이름 바뀜) + 경로 11 ②새 파일 0, 새 저장 키 0 ③타입 꼼수 0 ④줄 수 그대로(§6 예외) ⑤테스트 수 1,424 → 1,424 ⑥공용 값 — 해당 없음 ⑦목표 1~5 대조(`shared/` 위로 부르기는 59-E 예외 아이콘 1곳뿐). 경로 아닌 바뀐 줄 1(주석). `src/features/` 10개 기능.
> - 배포본 로그인 확인(2026-10-20, 내장 브라우저): 묶음 파일 이름이 59-E 배포와 같음(빌드 결과 같음과 맞음). 홈(미수금 10건 2,509,258원·이번 달 정산 2,645,513원) 그대로, 달력 10월 → 월 선택 창(1~12월) → 다음 달 11월 → 이전 달, 알림 화면 열림, 차량 유지비 정비(50,000원)·주유(100,000원) 탭 → [+ 추가] "주유 내역 추가" 창 → [취소](저장 안 함), 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-20).

### 쉬운 요약
- 달력·홈·알림 화면과, 계획 목록에서 빠져 있던 차량 유지비(정비·주유·기타) 화면을 기능 폴더 넷으로 옮깁니다.
- 알림 계산 파일(`lib/notifications.js`)은 알림 기능만 써서 알림 폴더로 갑니다.
- 화면·저장 동작은 그대로입니다.

### 현재 상태 (코드 확인 — 파일마다 쓰는 곳 grep)
| 옮길 곳 | 파일 | 쓰는 곳 |
|---|---|---|
| `features/calendar/ui` | `calendar/` 의 `CalendarPage`·`CalendarGrid`·`CalendarCell`·`CalendarHeader`·`CalendarMonthSummary`·`CalendarSubLogBanner`·`CalendarDateSelect`※·`SummaryInfoToggle`※·디자인 3(`calendar`·`calendar-grid`·`calendar-date-select`※) + 테스트 3 | ※`CalendarDateSelect`(+디자인)는 서류·기사·매출·차량 유지비가, `SummaryInfoToggle`은 서류가 씀 |
| `features/calendar/hooks` | `useMonthSettlement`※ | ※홈도 씀 |
| `features/home/ui` | `home/` 의 `HomePage`·`HomeAssistantBar`·`HomeMonthCard`·`HomeTodayCard`·`HomeTodoCard`·디자인 2 + 테스트 2(`HomePage`·`homeMonthCard` — 같은 폴더 `home.css`를 경로로 읽음, 같이 옮기면 그대로) | 홈 안 |
| `features/home/hooks` | `useTodayInspectionMissing` | 홈 |
| `features/notifications/ui` | `notifications/NotificationsPage`·`notifications-page.css` + 테스트 | 알림 화면 |
| `features/notifications/hooks` | `useNotifications` | 앱 틀 `AppShell`(위층→기능, 허용 방향)·알림 화면 |
| `features/notifications/api` | `lib/notifications.js` + 테스트 3(`notifications`·`notifications.dailyInspection`·`notifications.dateLabel`) | 알림 기능만 |
| `features/expenses/ui` | `components/MaintFuelPage`·`ExpenseFormModal`※(+ 테스트)·`maint-fuel.css`·`expense-form.css` | ※`ExpenseFormModal`은 일지 `DayLogExpenses`도 씀 |

- 이번 조각 밖: `components/day-log/useExpenseForm.js`(일지 `DayLogExpenses`·`DayLogPage`가 씀 — 59-G), `lib/calendar.js`·`lib/expenses.js`(재수출 껍데기 겸 저장 — 59-H), 앱 틀 부품(`BottomNav`·`SideMenu` 등 — 59-H), 계정·설정·고객센터(59-F2).

### 목표 상태 (기대 동작)
1. 위 표대로 옮김(짝 테스트 포함, 약 40개). `components/calendar/`·`home/`·`notifications/` 폴더 없어짐.
2. 옛 경로 grep 0건: `components/(calendar|home|notifications)/`, `components/(MaintFuelPage|ExpenseFormModal|maint-fuel|expense-form)`, `lib/notifications(\.js)?'`.
3. `shared/`에서 `features/`·`components/`를 부르는 곳 — 59-E 예외 1곳(아이콘, 59-G)만, 새로 생기지 않음(grep).
4. 테스트 수 그대로, 전부 통과(`package.json` 안 바뀜, 확인만). 빌드 결과 내용 같음(묶음 이름이 바뀌면 진행 기록에).
5. "기능 사이 부르기" 목록(규칙 ③)을 진행 기록에 grep 결과로 — 59-D·E 목록에 이어 적음.

### 건드릴 파일
- 옮김(`git mv`) 약 40개, 경로만 고침(부르는 파일 + 옮긴 파일 안 상대 경로 + 테스트 `mock.module`·`import()`·경로로 읽는 테스트). 개수는 진행 기록에.
- 동작 코드·문구는 안 바꿈.

### 안 건드릴 것
- 위 "이번 조각 밖", 기능 사이 부르기 정리(59 끝), 92번(59-I).
- 59-E 예외 아이콘(59-G).

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 2·3 grep, 목표 5 목록을 진행 기록에.
- `git diff -M --stat`: 옮긴 파일이 이름 바뀜으로 잡히는지, 경로 아닌 바뀐 줄 목록을 진행 기록에.
- 빌드 결과 내용 비교(이름 해시 뺌) — 차이를 진행 기록에.
- 개발 서버(비회원): 홈, 달력(월 이동·날짜 고르기 창), 알림 화면, 차량 유지비(정비·주유·기타 탭, [+ 추가] 창 열고 닫기) → 콘솔 오류 없음.

### 파일 길이
- 옮기기만 해서 줄 수 그대로. 200줄 넘는 파일(`MaintFuelPage.jsx` 245)은 경로 줄만 — §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(위치·경로만).
- 테스트가 하나라도 실패하면 멈추고 어느 경로인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 홈(오늘 카드·할 일·이번 달 금액) 그대로, 알림 숫자.
2. 달력 열기·달 바꾸기·날짜 고르기 창.
3. 알림 화면 열기.
4. 차량 유지비 정비·주유·기타 탭, [+ 추가] 창 열고 [취소], 콘솔 오류 없음.

### 확인할 점 — 답 받음 `[확인: 2026-10-20 보리 — "나눠"]`: 59-F를 F1(이 지시서)·F2(계정·설정·고객센터)로 나눔
1. 59-F를 F1(이 지시서: 달력·홈·알림·차량 유지비, 약 40개)과 F2(계정·설정·고객센터 + 그 저장 함수, 약 60개)로 나눠도 될까요? **권함: 예** — 한 번에 하면 100개가 넘어 확인이 어렵습니다. 차량 유지비는 처음 계획 목록에 빠져 있던 기능이라 F1에 넣었습니다. 안 나누면 F2 범위를 이 지시서에 합쳐 다시 씁니다.
