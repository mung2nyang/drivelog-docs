# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-I1 (92번) 알림 문구 띄우기(`showToast`)를 손으로 넘기지 않고 `useToast()`로 `[x]`

보리 지시 "지금 작성해줘"(2026-10-20, 59-H2 `[x]` 뒤). 59번 마지막 조각 = 로드맵 92 `[확인: 2026-10-19 보리 — 전부 로드맵에 올림]`(같은 값 손 넘기기 → 앱 공용 값, 59번과 같이). 목표 구조 문서의 "알림은 손으로 넘기지 않고 필요한 곳에서 `useToast()`로 꺼내 쓴다"(`architecture-target.md` §4).
**나누기 제안(아래 확인할 점 1)**: 92번은 알림(`showToast`)과 `ownerKey`·`session`·이동 함수 두 갈래이고 각각 파일 50~60개라 I1(알림)·I2(나머지)로 나눔. 이 지시서는 I1. 동작 변화 0.

> **진행(2026-10-20)**: 보리 "착수지시서 작업진행해, 권하는대로진행" → 확인할 점 답 `[확인: 2026-10-20 보리 — "권하는대로"]`: 1 I1·I2로 나눔 / 2 경로 아닌 줄이 바뀌는 200줄 넘는 파일도 **줄이 줄어들기만 하면** §6 예외.
> - 교차검증(AGENTS §1) 고친 것: `showToast`를 인자로 받는 훅은 7개가 아니라 8개(`useReceivablesActions`도 받음). 테스트 쪽은 23개 파일 + 훅 테스트 4개(`useDayDraft.cloudSerial`·`useBackToExit`·`historyMoves`가 `showToast`를 위치 인자로 넘김 — 지시서 grep에 안 걸린 것).
> - 한 일(코드는 바뀌어 있고 **커밋 안 함**): 새 `shared/hooks/useToast.js`(20줄, 머리 주석 지시서 문구 그대로)·`testSupport/withToast.js`(12줄)·`shared/hooks/useToast.test.js`(2개). `app/App.jsx`를 `ToastProvider value={showToast}`로 감쌈. 화면·부품·훅 37개 파일이 `useToast()`로 꺼냄, `showToast={showToast}` 넘기기·`showToast` prop·인자 삭제. 훅 인자 바뀜: `useDayDraft(ownerKey, dateKey, onCommitted, logId)`, `useExpenseForm(ownerKey, dateKey, logId)`, `useClientReorder(ownerKey, clients)`, `useBackToExit(windowMs)`, 나머지 4개는 받는 객체에서 `showToast` 뺌. `taxInvoiceActions.js` 2개는 인자 그대로(`TaxInvoiceTab`이 `useToast()`로 꺼내 넘김). 테스트는 모으는 함수를 `withToast(…)`로 감싸 넘김, 단언 그대로.
> - 같은 함수 그대로: 공급자 값은 `useAppSession`의 `showToast`(렌더마다 새 함수 — 원래도 그랬음)를 그대로 넘겨서, `CustomerCenterPage`의 `[userId, showToast]` 의존 효과가 다시 도는 때도 전과 같음.
> - 목표 5 grep: `showToast={` 0건, 운영 파일 prop·인자 목록의 `showToast` 0건(`taxInvoiceActions.js` 2개 제외), `useToast()` 쓰는 운영 파일 37개.
> - `npm test` 864 + 641 = 1505 통과(1503 + 새 2개), 타입 검사 통과. 린트 경고 수 전후 같음(50, 새 경고 0).
> - **멈춘 까닭(지시서 "늘면 멈추고 보고")**: 200줄 넘는 운영 파일 줄 수 — `DayLogPage.jsx` 236 → 234(줄어듦), `ReportPage.jsx` 235 → 235·`CustomerCenterPage.jsx` 228 → 228(같음), **`MaintFuelPage.jsx` 246 → 247(1줄 늘어남)** — 받는 줄 설명(`@param`) 1줄이 빠지고 `useToast` 가져오기 1줄 + `const showToast = useToast()` 1줄이 들어옴. 줄일 수 있는 다른 줄 없음(동작 줄을 건드려야 함). 200줄 넘는 테스트 파일 3개도 1줄씩 늘어남(`PersonalInfoPage.typing` 223·`DailyInspectionNotice` 206·`FixedRouteClientLink` 221 — 가져오기 줄).
> - 보리 결정 필요(아래 중 하나): ① `MaintFuelPage.jsx` 1줄 늘어남을 이번만 예외로(권함 — 늘어난 줄이 가져오기 한 줄이고, 이 파일은 이미 246줄이라 나누기는 따로 등재) ② `MaintFuelPage.jsx` 나누기(§6 분리설계)를 먼저 하고 이 조각을 이어서 ③ `MaintFuelPage.jsx`만 이번에서 빼고 `showToast`를 계속 손으로 넘김(목표 5 예외 1곳).
> - **보리 결정(2026-10-20) "1번 권하는대로"** `[확인: 2026-10-20 보리]`: `MaintFuelPage.jsx` 246 → 247(가져오기 1줄)을 이번만 예외로, 나누기는 따로 등재.
> - 코드 커밋 react-app `496c52d`(64개 파일).
> - 개발 서버(비회원): 차량 추가 "차량을 등록했습니다.", 거래처 추가 "거래처를 등록했습니다.", 거래처 삭제 "거래처를 삭제했습니다.", 일지 주유 추가 "내역을 등록했습니다." — 알림 문구 그대로, 콘솔 오류 없음. 앱 설정 스위치는 원래 실패할 때만 알림(`AppSettingsPage.jsx:50` `SERVER_SAVE_FAIL`)이라 성공 때 알림 없음이 맞음. 뒤로가기 두 번 종료 안내는 설치 앱에서만 — 테스트(`useBackToExit.test.js`·`historyMoves.test.js`)로 확인.
> - 지시서 오류(데스크톱 AI): `showToast`를 받는 훅을 7개로 적음(실제 8 — `useReceivablesActions`), 위치 인자로 넘기는 테스트 4개를 빠뜨림, 사용자 확인의 "다크 모드 저장 알림 문구"는 원래 실패할 때만 뜸 — 교차검증·진행 기록대로가 맞음.
> - push 뒤 CI verify·deploy 초록(run 39423248980).
> - §5 리뷰(데스크톱 AI): ①범위 = 87개 파일(새 3: `shared/hooks/useToast.js`·그 테스트·`testSupport/withToast.js`) ②새 저장 키 0, 새 저장 장치 0(알림 함수 전달 방식만) ③타입 꼼수 0 ④200줄 넘는 운영 파일: `DayLogPage` 236→234·`ReportPage` 235→235·`CustomerCenterPage` 228→228·`MaintFuelPage` 246→247(보리 결정 예외, 나누기는 로드맵 등재) ⑤테스트 1,424 → 1,426(새 2), `assert.` 5,238 → 5,239, 테스트에서 지운 단언 0 ⑥공용 값 — `showToast={` 넘기기 0건, `useToast()` 쓰는 운영 파일 37개 ⑦목표 1~7 대조. 새 파일 내용: 공급자 밖 기본값은 아무 일 안 하는 함수(지금 `showToast?.()`와 같음), 테스트 도우미는 공급자로 감싸기만.
> - 배포본 로그인 확인(2026-10-20, 내장 브라우저): 새 묶음(`index-BmjduEeU.js`), 거래처 [수정] → 값 그대로 [저장] → 화면 아래 "거래처를 수정했습니다." 알림(공급자 경로로 뜸, 거래처 값 그대로), 기사 [수정] 창 열고 [취소], 콘솔 오류 없음. 휴대폰 뒤로가기 두 번 종료 안내는 설치 앱에서만 — 테스트로.
> - 보리 최종 승인(2026-10-20). `MaintFuelPage.jsx` 나누기는 로드맵 97로 등재.

### 쉬운 요약
- 화면 아래 알림 문구를 띄우는 함수(`showToast`)를 지금은 앱 맨 위에서 화면마다 손으로 건네줍니다(받는 화면·부품·훅 약 55곳). 중간 화면은 쓰지도 않으면서 아래로 넘겨주기만 합니다.
- 앱 맨 위에 한 번 놓아두고, 필요한 곳에서 `useToast()`로 꺼내 쓰게 바꿉니다. 넘겨주기만 하던 줄이 없어집니다.
- 알림 문구·띄우는 때는 그대로입니다.

### 현재 상태 (코드 확인 — grep)
- 만드는 곳: `app/useAppSession.js:40` `showToast(message)` → `setToast` → `app/App.jsx:160` `<ToastMessage message={toast} />`. `App.jsx:74·146`에서 `AuthRoute`·`AppShell`로 넘김.
- 받는 곳(운영 63개 파일에 이름이 나옴): 화면·부품 약 45개가 `({ …, showToast })`로 받음(예: `app/shell/AppShell.jsx:57` → `app/routes/AppShellRoutes.jsx` → 화면 21개 → 그 하위 부품), 훅 7개가 인자로 받음(`useDayDraft(ownerKey, dateKey, onCommitted, showToast, logId)`·`useExpenseForm`·`useClientReorder`·`useScopedClients`·`usePersonalInfoDraft`·`useCarListActions`·`useBackToExit`), React 밖 함수 2개가 인자로 받음(`features/documents/api/taxInvoiceActions.js:46·92`).
- 부르는 쪽은 거의 모두 `showToast?.(…)`(없으면 아무 일 안 함).
- 테스트: 23개 파일 42줄이 `showToast`를 넘겨 받은 문구를 모아 검사(예: `CarListPage.flows.test.js`, `NotificationsPage.test.js`).

### 목표 상태 (기대 동작)
1. 새 `shared/hooks/useToast.js` — `ToastProvider`(값: 알림 띄우는 함수)와 `useToast()`(그 함수를 꺼냄). 공급자 밖에서 부르면 아무 일 안 하는 함수(지금 `showToast?.()`가 없을 때와 같음).
2. `app/App.jsx`가 앱 전체를 `ToastProvider value={showToast}`로 감쌈(만드는 곳 `useAppSession`은 그대로).
3. 화면·부품·훅은 `const showToast = useToast()`로 꺼내 씀. `showToast`를 받는 prop·인자와 넘겨주는 줄 삭제. 훅 7개의 `showToast` 인자도 빼고 안에서 `useToast()`(예: `useDayDraft(ownerKey, dateKey, onCommitted, logId)` — `onCommitted` 빈 칸 정리는 91번).
4. React 밖 함수(`taxInvoiceActions.js` 2개)는 인자 그대로 — 부르는 화면이 `useToast()`로 꺼내 넘김.
5. grep: `showToast=\{` 0건, prop·인자 목록의 `showToast` 0건(React 밖 함수 2개 제외), `useToast()` 쓰는 곳 목록을 진행 기록에.
6. 테스트: 넘기던 `showToast` 대신 같은 모으는 함수를 `ToastProvider`로 감싸 넘김(공용 테스트 도우미 하나로) — 단언은 그대로. 테스트 수 그대로, 전부 통과.
7. 화면 동작·알림 문구 그대로. 빌드 결과는 달라짐(공급자 추가) — 진행 기록에 묶음 크기 전후.

### 건드릴 파일
- 새로: `shared/hooks/useToast.js`(약 25줄), 테스트 도우미(예: `testSupport/withToast.js`, 약 15줄).
- 고침: `app/App.jsx`, `app/useAppSession.js`(필요하면 반환만), `app/shell/*`·`app/routes/*`의 넘기는 줄, `features/**`의 받는 화면·부품·훅 약 55개, 테스트 23개.
- 문구·알림 띄우는 때는 안 바꿈.
- 코드 주석 최종 문구(`useToast.js` 머리): `// 화면 아래 알림 문구 — 앱 맨 위 공급자에서 꺼내 쓴다(손으로 넘기지 않음). 공급자 밖에선 아무 일 안 함.`

### 안 건드릴 것
- `ownerKey`·`session`·이동 함수(`navigate`·`goBack`·`goToPage`)·`onOpenMenu` 손 넘기기 — 59-I2.
- 알림 문구 모양(`ToastMessage`)·실패 문구 내용.
- 일지 저장 훅의 `onCommitted` 빈 칸(91번).

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 5 grep 결과를 진행 기록에.
- 새 테스트 1개: 공급자 밖에서 `useToast()`를 불러도 오류 없이 아무 일 안 함, 공급자 안에선 넘긴 함수가 불림. revert-check 해당 없음(새 파일).
- 개발 서버(비회원): 차량 저장("저장했습니다" 류)·거래처 삭제·설정 저장·일지 지출 저장 실패 흉내 없이 성공 문구, 뒤로가기 두 번 종료 안내 문구(`useBackToExit`), 콘솔 오류 없음.

### 파일 길이
- 줄어들거나 거의 그대로(받는 줄·넘기는 줄이 빠지고 `useToast()` 한 줄). 200줄 넘는 파일은 이 줄들만 — 경로 줄은 아니므로 **§6 예외가 아님** → 해당 파일(`DayLogPage.jsx` 236 등)은 줄이 줄어들기만 하는지 진행 기록에(늘면 멈추고 보고).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(알림 함수 전달 방식만).
- 테스트가 하나라도 실패하면 멈추고 어느 곳인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 앱 설정 다크 모드 켜고 끄기 → 저장 알림 문구, 기사 [수정] 창 열고 닫기.
2. 알림 화면 알림 하나 끄고 다시 켜기(문구), 휴대폰에서 뒤로가기 두 번 → 종료 안내(보리).
3. 콘솔 오류 없음.

### 확인할 점
1. 92번을 I1(이 지시서: 알림 `showToast`)과 I2(`ownerKey`·`session`·이동 함수)로 나눠도 될까요? **권함: 예** — 각각 50~60개 파일이라 합치면 100개가 넘어 확인이 어렵습니다.
2. 200줄 넘는 파일에서 이번처럼 경로가 아닌 줄(받는 prop 한 줄을 빼고 `useToast()` 한 줄을 넣는 것)이 바뀌는 경우도, **줄이 줄어들기만 하면** §6 예외로 봐도 될까요? 권함: 예(지난번 "지우기만 하는 수정" 예외와 같은 취지). 안 되면 `DayLogPage.jsx`(236)·`ReportPage.jsx`(235)·`MaintFuelPage.jsx`(246)·`CustomerCenterPage.jsx`(228)를 이번에서 빼고 따로 해야 합니다.
