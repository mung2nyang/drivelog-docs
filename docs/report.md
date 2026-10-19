# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 90 알림 숫자를 손으로 다시 세게 하는 방식 → 구독으로 `[x]`

> **진행(2026-10-19)**: 보리 "작업해줘". 코드 커밋 react-app `8aa427a`(14개 파일 — 첫 커밋 `a701154`은 보리 지시로 push 전 취소, 코드의 그림 이모지 제거와 합쳐 다시 커밋).
> - 교차검증(AGENTS §1)으로 고친 것: ① 신호 목록에 `AppShellRoutes.jsx:97` 기사 연결 화면 `onBack`의 `bumpNotifTick()`이 빠져 있어 같이 지움. ② 새 테스트 "거래처 결제 조건만 바뀌어도" → 연체 여부는 거래처 설정이 아니라 운행 건의 `paymentDueDate`로 정함(`financeReceivables.js:104`)이라 거래처만 바꿔선 알림 숫자가 안 바뀜. 같은 목적(신호 없는 저장도 반영)을 **일지 저장**으로 검사 — 옛 코드는 일지 변화를 의존성에 안 넣어 실패.
> - 목표 3 grep: `bumpNotifTick|notifTick` 0건, `onChanged` 0건, `onWorkChanged`는 지시서대로 `DayLogPage.jsx:55·62·63`·`useDayDraft.js:28(주석)`만(91번에서 지움).
> - 바뀐 파일: 새 `notifications/useNotifications.js` 31줄(일지·설정·기사·거래처·프로필·닫은 알림 구독, 주석 지시서 문구 그대로), `AppShell.jsx` 195→191(알림 숫자는 `useNotifications(ownerKey, null, location.pathname)` — 지금처럼 세션 없이 셈, 안 쓰게 된 `settings` 구독 삭제), `AppShellRoutes.jsx` 106→104, `MainPageRoute.jsx` 110→107, `NotificationsPage.jsx` 106→100, 미수금 4개 파일(`onWorkChanged` 칸·부르기 삭제).
> - 테스트: `NotificationsPage.test` — `onChanged` 넘기기·`changed` 횟수 단언 2곳 삭제(신호가 없어짐, 같은 테스트의 목록·스위치·알림 숫자 계산 단언 그대로), 첫 테스트 이름에서 "알림 숫자 다시 세기" 뺌. 새 테스트 "신호 없이 일지만 바뀌어도 알림 목록이 다시 센다" — 고치기 전 코드에서 **실패**, 고친 뒤 통과, 나머지 6개는 양쪽 통과(revert-check, 8개 파일 복구 일치).
> - `npm test` 1004 + 495 통과(+1), 타입 검사·빌드 통과.
> - 개발 서버(비회원, 오늘·백업 알림 끔): 알림 숫자 0 → 연체 2건 일지 저장(신호 없이, 화면 이동 없이) → **알림 숫자 2** → 알림 화면에서 1건 [닫기] → 목록 1 → 홈 알림 숫자 1 → 입금 완료 저장 → 알림 숫자 0. 콘솔 오류 없음.
> - 이모지 제거(2026-10-19, 보리 "코드에 이모지넣은거 다 제거해"): 코드(`src`)의 그림 이모지(종·트럭) 9곳을 글자로 — `useNotifications.js:2`, `NotificationsPage.jsx:2`, `NotificationsPage.test.js` 테스트 이름 2·메시지 1, `lib/notifications.js:153`, `App.test.js:1111·1112`, `LoadingScreen.jsx:2`·`LoadingScreen.test.js:1`(주석의 옛 "트럭 그림 + 운행일지" — 화면엔 안 나옴). 남은 그림 이모지 grep 0건. 화면에 보이는 기호(➜ 출발·도착, ↺ 바로 전 내역, ✓ 체크 표시)와 주석의 ↔는 그림 이모지가 아니라 그대로. `npm test` 1004 + 495·타입 검사·빌드 통과.
> - 지시서 오류 1건(데스크톱 AI): 새 테스트를 "거래처 결제 조건만 바뀌어도"로 적었으나 연체는 운행 건의 입금 예정일로 정해져 거래처만 바꿔선 안 바뀜 — 교차검증의 "일지 저장"으로 바꾼 것이 맞음. 신호 목록에서 기사 연결 화면 `onBack` 1곳도 빠뜨렸음(교차검증이 채움).
> - push 뒤 CI verify·deploy 초록(run 39221083807).
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서 + 교차검증 1곳 + 보리 지시 이모지 제거(`LoadingScreen` 2·`lib/notifications.js` 1·`App.test` 2 — 글자만) ②새 파일 1(`useNotifications.js`, 지시서대로), 새 저장 키 0 ③타입 꼼수 0(`eslint-disable` 주석 1줄 — 일부러 넣은 의존성 설명, 기존 같은 모양 7곳과 같음) ④모두 200줄 이내 ⑤테스트 — 뺀 단언은 없어진 신호 횟수 2곳뿐, 새 테스트 고치기 전 실패 확인 ⑥공용 상태값 `onWorkChanged`·`bumpNotifTick` — 남은 곳은 91번으로 넘긴 `DayLogPage.jsx`·`useDayDraft.js`뿐(grep) ⑦목표 1~5 대조. `src` 그림 이모지 0(남은 `✓`·`➜`는 글자 기호).
> - 배포본 로그인 확인(2026-10-19 17:15, 내장 브라우저): 새 묶음 파일, 알림 0개 상태에서 앱 설정 [일상점검표 사용] 끄기 → 새로고침 없이 홈 알림 숫자 **1** → 알림 화면에 "일상점검표가 의무화되었습니다" → [바로 사용하기]로 다시 켬 → "일상점검표를 켰습니다", 목록 0·홈 알림 숫자 0(원래대로). 콘솔 오류 없음.
> - 이모지 규칙(AGENTS §6 새 줄, 2026-10-19): 이 파일의 그림 이모지는 글자로 바꿈(데스크톱 AI). `STATUS.md`·`roadmap.md`·`sot.md`의 옛 기록에 남은 것은 그대로 둠(보리 결정). 앞으로 안 쓰고, 코드는 작업하며 고치는 곳에서 만난 것만 고침.
> - 보리 최종 승인(2026-10-19).

보리 승인(2026-10-19, 95 `[x]`). 순서는 로드맵 "88~95" 줄 — 88·89·93·95 끝, 다음 90(⑤). 로드맵 90 `[확인: 2026-10-19 보리 — 전부 로드맵에 올림]`.

### 쉬운 요약
- 위쪽 알림 숫자와 알림 화면 목록은 "데이터가 바뀌었다"는 신호를 화면들이 손으로 보내야만 다시 셉니다. 신호를 안 보내는 화면에서 바꾸면 숫자가 옛값으로 남습니다(예: 거래처 결제일을 바꿔 연체가 생겨도 다른 저장이 있을 때까지 알림 숫자 그대로).
- 알림이 필요한 데이터를 직접 지켜보게(구독) 바꾸고, 손으로 보내던 신호 줄을 지웁니다.
- 알림 내용·끄기·닫기 동작은 그대로이고, 숫자가 더 빨리 맞게 됩니다.

### 현재 상태 (코드 확인)
- 계산: `lib/notifications.js:45` `collectNotifications(ownerKey, session)` — 구독 없이 그때그때 꺼내 읽음. 쓰는 값: 일지 전체(`loadWorkDataByLogId` → `readOwnerWorkDataByLogId`), 설정·기사·거래처·프로필(`buildFinanceSettings` `lib/ownerFinance.js:91-95`, `readOwnerDrivers`·`readOwnerSettings`), 닫은 알림(`loadDismissed` `:26` — 기기 저장소, `dismissNotification`이 Store와 같이 씀), 시각(오후 6시·백업 경과일·D-day).
- 알림 숫자: `app/AppShell.jsx:92·114-115` — `notifTick` 상태 + `useMemo(() => collectNotifications(ownerKey), [ownerKey, notifTick, drivers, settings])`(계산에 안 쓰는 `notifTick`·`drivers`·`settings`를 넣어 억지로 다시 계산, 거래처·일지·프로필 변화는 못 봄).
- 손으로 보내는 신호(`bumpNotifTick`): `AppShell.jsx:164·167` → `AppShellRoutes.jsx:34·46·59`(메인 일지 `onWorkChanged`)·`:92`(미수금 `onWorkChanged` + `onBack`에서 한 번 더)·`:102`(알림 화면 `onChanged`) → `MainPageRoute.jsx:22·31·80` → `DayLogPage.jsx:55·62-63` → `useDayDraft`의 `onCommitted`. 미수금: `ReceivablesPage.jsx:13·16-17` → `ReceivablesListPage.jsx:23·26·33`·`ReceivablesDetailPage.jsx:21·23·32` → `useReceivablesActions.js:32·36·60·66·113·117`.
- 알림 화면: `components/notifications/NotificationsPage.jsx:27-30` — 자기 `tick` 상태 + 같은 억지 의존성, 닫기 때 `setTick`(`:56`)·`onChanged`(`:47·57·62`).

### 목표 상태 (기대 동작)
1. 새 훅 `useNotifications(ownerKey, session, refreshKey)` 하나 — 일지·설정·기사·거래처·프로필·닫은 알림을 Store에서 구독(`useOwnerWorkDataByLogId`·`useOwnerSettings`·`useOwnerDrivers`·`useOwnerClients`·`useOwnerProfile` + `dismissedNotifications` 칸)하고, 그중 하나라도 바뀌면 `collectNotifications`를 다시 부름. `collectNotifications`는 그대로.
2. 알림 숫자(`AppShell`)와 알림 화면 목록(`NotificationsPage`)이 이 훅 하나를 씀. `AppShell`은 `refreshKey`로 화면 주소를 넘김 → 시간이 지나 생기는 알림(오후 6시 일지 알림 등)은 지금처럼 화면을 옮길 때 다시 셈(지금 "알림 열기 때 다시 세기"를 대신).
3. 손으로 보내던 신호 전부 삭제: `notifTick`·`bumpNotifTick`, `AppShellRoutes`의 `bumpNotifTick` 칸, 메인 일지·미수금의 `onWorkChanged`, 미수금 `onBack`의 다시 세기, 알림 화면의 `tick`·`onChanged`. grep `bumpNotifTick|notifTick` 0건, `onWorkChanged`는 아래 "안 건드릴 것"의 2곳만.
4. 거래처 결제 조건처럼 신호를 안 보내던 데이터가 바뀌어도 알림 숫자가 바로 맞음(지금은 틀림 — 새 테스트로 확인).
5. 알림 끄기·닫기·[바로 사용하기]·눌러서 화면 이동은 지금과 같음.

### 건드릴 파일
- 새로: `components/notifications/useNotifications.js`(약 30줄).
- 고침: `app/AppShell.jsx`, `app/AppShellRoutes.jsx`, `app/MainPageRoute.jsx`, `components/notifications/NotificationsPage.jsx`, `components/ReceivablesPage.jsx`, `components/receivables/ReceivablesListPage.jsx`, `components/receivables/ReceivablesDetailPage.jsx`, `components/receivables/useReceivablesActions.js`.
- 테스트: `NotificationsPage.test.js`(`onChanged` 넘기기·`changed` 횟수 단언 1줄 — 신호가 없어지므로 삭제, 같은 테스트의 목록·숫자 단언 그대로). 새 테스트 1개(아래 검증).
- 코드 주석 최종 문구(`useNotifications.js` 머리): `// 알림 숫자·알림 목록 — 알림에 쓰는 Store 값을 구독해 바뀌면 다시 센다. refreshKey는 시간이 지나 생기는 알림용(화면 주소).`
- 공용 상태값 영향 목록(§5-6, `onWorkChanged`/`bumpNotifTick`을 읽고 넘기는 곳): 위 "현재 상태"의 줄이 전부(grep).

### 안 건드릴 것
- `components/day-log/DayLogPage.jsx`(236줄, 200 초과)·`useDayDraft.js`·`dayDraftLifecycle.js` — `MainPageRoute`가 `onWorkChanged`를 더 안 넘기므로 DayLogPage의 `onWorkChanged` 칸과 `useDayDraft`의 `onCommitted` 인자는 받기만 하고 비게 됨. 일지 저장 훅을 다시 짜는 **91번에서 같이 지움**(로드맵 91에 적음) — 200줄 넘는 파일을 이번에 안 건드리려고.
- `lib/notifications.js` 계산 내용, 닫은 알림을 기기 저장소에서 읽는 방식(`loadDismissed`) — 저장은 Store와 함께 되므로 그대로.
- 92번(같은 값 손으로 넘기기 → Context) — 59번 때.

### 검증
- `npm test`·타입 검사·빌드 통과.
- 목표 3 grep 결과를 진행 기록에.
- 새 테스트 1개: "거래처 결제 조건만 바뀌어도 알림 숫자가 바뀐다" — 미수 운행을 넣고 거래처 결제일을 지난 날로 Store에서 바꿈(손으로 다시 세기 없이) → 알림 숫자 +1. **고치기 전 코드에서 실패, 고친 뒤 통과**(revert-check).
- 기존 알림 테스트(`NotificationsPage.test.js`·`notifications*.test.js`·`App.test.js` 알림 숫자)가 그대로 통과.
- 개발 서버(비회원): 일지 저장·미수금 입금 완료·알림 닫기 뒤 알림 숫자가 바로 바뀜, 콘솔 오류 없음.

### 파일 길이
- `app/AppShell.jsx` 195 → 약 190, `AppShellRoutes.jsx` 106 → 약 103, `MainPageRoute.jsx` 110 → 약 107, `NotificationsPage.jsx` 106 → 약 102, `ReceivablesPage.jsx` 24 → 약 22, `ReceivablesListPage.jsx` 132 → 약 130, `ReceivablesDetailPage.jsx` 106 → 약 104, `useReceivablesActions.js` 160 → 약 154, 새 `useNotifications.js` 약 30. 모두 200줄 이내.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(구독으로 바꾸고 신호 줄을 지우기만).
- 기존 테스트 중 알림 숫자가 "신호를 보내야만 바뀐다"를 전제로 한 단언이 나오면 빼지 말고 멈추고 보고. 실패가 목록 밖 파일로 번지면 고치지 않고 수정 계획을 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 홈 알림 숫자 확인 → 미수금 화면에서 한 건 [입금 완료] → 홈으로 돌아오면 숫자 줄어듦 → 되돌림.
2. 알림 화면에서 알림 하나 끄기 스위치 → 목록·알림 숫자에서 빠짐 → 다시 켬.
