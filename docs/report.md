# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-G 기능 폴더 5 — 일지(`components/day-log/`)를 `src/features/day-log/`로 + 아이콘을 `shared/ui`로 `[x]`

보리 승인(2026-10-20, 59-F2 `[x]`). 59번 여덟 번째 조각. 59-E에서 넘어온 할 일 `[확인: 2026-10-20 보리]`: `components/day-log/icons.jsx` → `shared/ui`(59-E 목표 3 예외 1곳). 59-D~F2의 **자리 정하는 규칙** 그대로(화면·부품·디자인 → `ui/`, 훅 → `hooks/`, 여러 기능이 쓰면 아래로). 옮기기·경로만, `git mv`, 경로 줄만 바뀌는 200줄 넘는 파일은 §6 예외.
플레이북 트리거 — 일지 저장 훅(`useDayDraft`·`dayDraftLifecycle`)이 있으나 **위치만** 바뀌고 저장 동작은 그대로.

> **진행(2026-10-20)**: 보리 "착수지시서 확정,작업진행해". 코드 커밋 react-app `68c021b`(68개 파일: 옮김 60 + 경로 고침 8).
> - 교차검증(AGENTS §1): 60개(화면·부품 22·디자인 13·`.js` 8·테스트 17), 폴더 밖에서 부르는 곳 모두 지시서와 같음. 지시서에 없던 것: 폴더 밖 테스트 3개도 일지 파일을 부름(`app/App.legacyCallInit.test.js`·`App.unsafeQuotaFailSyncSpy.test.js`, `shared/ui/CardActionButtons.test.js:34`가 `day-log-shell.css`를 경로로 읽음) — 앞 둘은 대본이, 뒤 하나는 손으로 고침.
> - 나눔: `hooks/` 12개(`useDayDraft`+테스트 3, `useExpenseForm`, `usePanelBackGuard`+테스트, `dayDraftLifecycle`+테스트, `day-log-reducer`+테스트), `ui/` 47개, `shared/ui/icons.jsx` 1개.
> - 손으로 고친 줄 2개: `shared/ui/CardActionButtons.test.js:34` 읽는 경로(`components/day-log/day-log-shell.css` → `features/day-log/ui/…`), 주석 속 경로 `data/sync/pendingWorkDataWritesTypes.js:4`(`day-log/dayLogTypes.js` → `features/day-log/ui/dayLogTypes.js`). `inlineSheetCss.test.js`는 읽는 파일과 같이 `ui/`로 가서 그대로. `package.json` 안 바뀜.
> - 목표 4: `components/day-log/` 없어짐, 옛 경로 grep 0건.
> - 목표 5: `shared/` 운영 파일이 `components/`·`features/`를 부르는 곳 **0건**(59-E 예외 해소). 테스트만 남음 — `shared/ui/CardActionButtons.test.js:34,40`(일지·거래처 디자인 파일을 경로로 읽음), `PageHeader.test.js:18-19`(`MyPage`·`RevenuePage`).
> - 빌드 비교: 옮기기 전·후 48개 파일, 해시 지우고 비교 → 이름·내용 모두 같음.
> - `git diff -M`: 60개 모두 이름 바뀜으로 잡힘, 경로 아닌 바뀐 줄은 위 2줄뿐.
> - `npm test` 885 + 618 = 1503 통과(같은 수), 타입 검사·빌드 통과.
> - 개발 서버(비회원): 거래처 카드 연필·휴지통 아이콘 보임(수정/삭제), 일지에 콜 1건 저장 → 콜 카드 보임, [+ 추가] → "일지 세부 입력" 창 → [취소], 운행 횟수 2 입력 → 새로고침 뒤 2, [+ 주유 추가] → "주유 내역 추가" 칸 → [취소]로 닫힘, 휴무 켬 → 새로고침 뒤 켜짐(횟수는 비워짐 — 원래 동작) → 휴무 끄고 횟수 비워 되돌림, 콘솔 오류 없음.
> - 목표 7 기능 사이 부르기 갱신: `clients/ui/ClientFormModal.jsx:11`·`ClientListItem.jsx:4` → 이제 `shared/ui/icons.jsx`(기능 사이 아님). 일지가 새로 기능 사이로: `day-log/ui/CallClientQuickAdd.jsx:4` → `clients/ui/ClientFormModal.jsx`, `day-log/ui/DayLogExpenses.jsx:3` → `expenses/ui/ExpenseFormModal.jsx`, (테스트) `day-log/ui/AssignmentPeriodPage.test.js:21` → `drivers/ui/EmployerLinkCard.jsx`. 다른 기능이 일지를 부르는 곳 0.
> - 59-H로 넘기는 "위층(app) 부르기"(운영 파일 grep): `account/ui/InviteRedeemModal.jsx:5`·`drivers/ui/EmployerLinkCard.jsx:4` → `app/boot.js` / `account/ui/PersonalInfoPage.jsx:5`·`settings/ui/AppSettingsPage.jsx:7` → `app/useHydrationLock.js` / `day-log/hooks/usePanelBackGuard.js:5`·`day-log/ui/DayLogPage.jsx:21` → `app/historyMoves.js` / `data/cloud/supabaseClient.js:3` → `app/routerBasename.js`. 모두 7곳, 지시서 목록과 같음.
> - 지시서 오류(데스크톱 AI): 폴더 밖 테스트 3개가 일지 파일을 부르는 것을 목록에서 빠뜨림 — 교차검증이 채움.
> - push 뒤 CI verify·deploy 초록(run 39393798757).
> - §5 리뷰(데스크톱 AI): ①범위 = 옮김 60(모두 이름 바뀜 — `hooks/` 11·`ui/` 48·`shared/ui/icons.jsx` 1) + 경로 8 ②새 파일 0, 새 저장 키 0 ③타입 꼼수 0 ④줄 수 그대로(§6 예외) ⑤테스트 수 1,424 → 1,424 ⑥공용 값 — `icons.jsx` 쓰는 곳 전부 새 경로(grep) ⑦목표 1~7 대조, **`shared/` 운영 파일의 위층 부르기 0건**(59-E 예외 해소). 경로 아닌 바뀐 줄 2(주석 1·읽는 경로 1). `components/`에는 앱 틀 부품·여러 화면 검사 테스트 3만 남음.
> - 배포본 로그인 확인(2026-10-20, 내장 브라우저): 묶음 파일 이름이 이전 배포와 같음(빌드 결과 같음과 맞음). 오늘 일지 2회 저장 → 새로고침 뒤 그대로 → 비워서 되돌림, 10월 10일 일지 콜 카드·문자 단추 보임 → 콜 상세 [수정] "10일 일지 세부 입력 수정" → [취소](저장 안 함, 운송료 200,000원 그대로), 거래처 카드 연필·휴지통 아이콘 보임(그림 들어 있음), 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-20).

### 쉬운 요약
- 일지 화면 폴더(60개, 가장 큼)를 통째로 `features/day-log`로 옮깁니다. 화면·부품·디자인은 `ui/`, 저장·뒤로가기 처리 훅은 `hooks/`로 나눕니다.
- 연필·휴지통 같은 그림 아이콘 파일은 거래처 화면과 공용 단추도 쓰므로 공용 폴더(`shared/ui`)로 옮깁니다. 이걸로 공용 폴더가 기능 폴더를 거꾸로 부르던 마지막 1곳이 없어집니다.
- 화면·저장 동작은 그대로입니다.

### 현재 상태 (코드 확인 — grep)
- `components/day-log/` 60개: 화면·부품 22(`.jsx`, `icons.jsx` 포함), 디자인 13(`.css`), 그 밖 `.js` 8(`useDayDraft`·`useExpenseForm`·`usePanelBackGuard`·`dayDraftLifecycle`·`day-log-reducer`·`callDetailFormHelpers`·`inlinePanelActions`·`dayLogTypes`), 테스트 17.
- 폴더 밖에서 부르는 곳: `DayLogPage.jsx` ← `app/MainPageRoute.jsx:7`(앱 틀→기능, 허용), `icons.jsx` ← `shared/ui/CardActionButtons.jsx:2`·`features/clients/ui/ClientFormModal.jsx`·`ClientListItem.jsx`. 그 밖 0.
- 경로로 읽는 테스트: `inlineSheetCss.test.js`(같은 폴더 디자인 파일을 읽음 — 함께 옮기면 경로 그대로인지 확인).
- 기능이 앱 틀(`app/`)을 부르는 곳(위층 부르기, 59-H 정리 목록): `DayLogPage.jsx:21`·`usePanelBackGuard.js:5` → `app/historyMoves.js`. 같은 종류가 다른 기능에도 있음 — `account/ui/InviteRedeemModal.jsx:5`·`drivers/ui/EmployerLinkCard.jsx:4` → `app/boot.js`, `account/ui/PersonalInfoPage.jsx:5`·`settings/ui/AppSettingsPage.jsx:7` → `app/useHydrationLock.js`, `data/cloud/supabaseClient.js:3` → `app/routerBasename.js`.

### 목표 상태 (기대 동작)
1. `features/day-log/hooks/` ← `useDayDraft`(+ 테스트 3)·`useExpenseForm`·`usePanelBackGuard`(+ 테스트)·`dayDraftLifecycle`(+ 테스트)·`day-log-reducer`(+ 테스트) — 훅과, 훅이 안에서 쓰는 상태·저장 흐름 파일.
2. `features/day-log/ui/` ← 나머지 전부(화면·부품 21·디자인 13·`callDetailFormHelpers`·`inlinePanelActions`(+ 테스트)·`dayLogTypes`·남은 테스트).
3. `shared/ui/icons.jsx` ← `components/day-log/icons.jsx`. 부르는 곳(일지 안 + `CardActionButtons`·거래처 2곳) 경로 고침.
4. `components/day-log/` 폴더 없어짐. 옛 경로 grep 0건(`components/day-log/`).
5. `shared/`가 `components/`·`features/`를 부르는 곳 **0건**(운영 파일 grep — 59-E 예외 해소). 테스트 `PageHeader.test.js`의 부르기는 기록만.
6. 테스트 수 그대로, 전부 통과. 빌드 결과 내용 같음(묶음 이름이 바뀌면 진행 기록에).
7. "기능 사이 부르기" 목록에 `features/clients/ui` → `shared/ui/icons.jsx`가 기능 사이가 아니게 됨을 반영해 진행 기록에 갱신. 위 "위층 부르기" 목록을 진행 기록에 적어 59-H로 넘김.

### 건드릴 파일
- 옮김(`git mv`) 60개(그중 `icons.jsx`는 `shared/ui`로), 경로만 고침(`app/MainPageRoute.jsx`, `shared/ui/CardActionButtons.jsx`, 거래처 2곳, 옮긴 파일 안 상대 경로, 테스트 `mock.module`·`import()`·경로로 읽는 테스트).
- 동작 코드·문구는 안 바꿈.

### 안 건드릴 것
- 일지 저장 훅의 내용(91번 — 저장 실패 대비 장치·`onCommitted` 빈 칸 정리는 91에서).
- 위층 부르기 바로잡기(59-H), 기능 사이 부르기 정리(59 끝), 92번(59-I).
- `lib/` 재수출 껍데기(일지가 부르는 `lib/clients`·`expenses`·`finance`·`money`·`practiceSettings`·`workData`·`failureMessages`) — 59-H.

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 4·5 grep 0건, 목표 7 목록을 진행 기록에.
- `git diff -M --stat`: 60개가 이름 바뀜으로 잡히는지, 경로 아닌 바뀐 줄 목록을 진행 기록에.
- 빌드 결과 내용 비교(이름 해시 뺌) — 차이를 진행 기록에.
- 개발 서버(비회원): 일지 열기 → 횟수 입력·콜 상세 추가 창 열고 닫기·지출 추가 창·휴무 토글(되돌림) → 새로고침 뒤 그대로, 거래처 카드의 연필·휴지통 아이콘 보임, 콘솔 오류 없음.

### 파일 길이
- 옮기기만 해서 줄 수 그대로. 200줄 넘는 파일(`DayLogPage.jsx` 236 등)은 경로 줄만 — §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(위치·경로만).
- 테스트가 하나라도 실패하면(특히 일지 저장 훅 테스트의 가짜 모듈 경로) 멈추고 어느 경로인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 오늘 일지 열기 → 운행 2회 저장 → 새로고침 뒤 그대로 → 비워서 되돌림.
2. 콜 상세가 있는 날(10월 10일) 열기 → 콜 카드·문자 단추 보임, 콜 상세 수정 창 열고 [취소].
3. 거래처 화면 카드의 연필·휴지통 아이콘 보임, 콘솔 오류 없음.
