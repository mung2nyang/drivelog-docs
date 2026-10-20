# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-H2 앱 틀 정리(`app/routes`·`app/shell`) + 기능·데이터가 `app/`을 부르는 7곳 바로잡기 `[x]`

보리 승인(2026-10-20, 59-H1 `[x]`). 59-H를 둘로 나눈 뒤쪽(보리 결정 2026-10-20). 59-D에서 넘어온 `EmployerLinkCard`의 앱 시작 코드 부르기 바로잡기 `[확인: 2026-10-20 보리]` 포함. `git mv`, 경로 줄만 바뀌는 200줄 넘는 파일은 §6 예외.
이번 조각은 옮기기만이 아니라 **파일 하나(`app/boot.js`)를 둘로 나눔** — 함수 본문은 그대로, 위치만.

> **진행(2026-10-20)**: 보리 "착수지시서 확정,작업진행해". 코드 커밋 react-app `a7e2b47`(76개 파일: 옮김 50·새로 1(`appSession.js`)·고침 25).
> - 교차검증(AGENTS §1): 위층 부르기 7곳·`boot.js` 줄 범위·`components/` 남은 목록 모두 코드와 같음. 고친 것 ① `boot.js`의 세션 만들기 부분은 `:16-121`이 아니라 `:13-115`(`restoreSessionOnBoot` 주석은 `:117`부터) ② 지시서에 없던 가짜 모듈: `features/drivers/ui/EmployerLinkCard.test.js:18`이 `app/boot.js`를 바꿔 끼움 → `data/cloud/appSession.js`로 경로만 바꿈(넣는 함수 `buildCloudAppSession`·`ownerKeyFromSession` 그대로) ③ `app/boot.js`에 남는 `restoreSessionOnBoot`이 안에서 `buildBootSession`(옛 파일 안에서만 쓰던 함수)을 불러서, 새 파일에서 `export`를 붙여 내보냄 — 바뀐 단어는 이 `export` 하나.
> - 나눔 확인(diff): 옛 `boot.js` 13~115줄 ↔ 새 `appSession.js` 10~112줄 — 다른 줄 2개(`buildBootSession` 앞 `export`, JSDoc 타입 경로 `../data/sync/` → `../sync/`). 옛 117줄~끝 ↔ 새 `boot.js` 10줄~끝 — 같음. `boot.js` 157 → 50줄, `appSession.js` 112줄.
> - `boot.js` 함수를 부르던 곳 경로: `app/App.jsx:12`·`useAppSession.js:11-12`(두 줄로 나눔)·테스트 `bootNeedsProfile`·`bootSettleUnlink`·`employedDriverSession` → `appSession.js`, `bootLinkCheck`는 `restoreSessionOnBoot`이라 그대로. `InviteRedeemModal`·`EmployerLinkCard` → `appSession.js`.
> - 나머지는 같은 대본으로 옮기기·경로(50개 옮김 — `shared/utils` 3·`data/store` 1·`app/routes` 11·`app/shell` 32(`side-menu/` 포함)·`app/` 3, 43개 파일 경로). `app/routes/lazyPages.js`는 21줄이 모두 경로라 git이 지움+새로 만듦으로 봄(내용은 경로 말고 같음).
> - 손으로 고친 것: `package.json` `test:app`에서 `src/components/**` 뺌(`src/app/**`가 `routes/`·`shell/` 덮음), 주석 속 옛 이름 4줄(`data/sync/expensesSave.js:30`·`cloudMemorySave.test.js:145`의 `boot.js ownerKeyFromSession` → `appSession.js`, `shared/styles/offline-banner.css:1`·`toast.css:1` → `app/shell/…`). `data/cloud/supabaseClient.js:24`의 "부트 복원(boot.js)"은 그대로 맞아 둠.
> - 목표 1: `features/`·`data/`·`shared/`·`domain/` 운영 파일이 `app/`을 부르는 곳 **0건**. 테스트만 2곳(`features/support/ui/CustomerCenterPage.test.js:20` → `app/shell/SideMenu.jsx`, `shared/utils/historyMoves.test.js:17` → `app/shell/useBackToExit.js`) — 기록만.
> - 목표 5: `src/components/` 없어짐, `components/` 경로 grep 0건. `src/` 맨 위: `app`·`assets`·`data`·`domain`·`features`·`shared`·`testSupport`·`main.jsx`.
> - 빌드 비교(`HEAD`를 따로 풀어 빌드): 48개 파일, 이름 같음. 줄여 쓴 결과에서 `index` 묶음 하나만 다르고, 줄여 쓰지 않은 빌드로 비교하면 **줄 순서만 바뀌고 줄 내용은 모두 같음** — 옮겨진 것은 `restoreSessionOnBoot`·`performRestoreSessionOnBoot` 함수 선언 자리(파일이 나뉘어 모듈 순서가 바뀜)와 나중 불러오기 화면 목록 자리뿐.
> - `npm test` 864 + 639 = 1503 통과(같은 수), 타입 검사·빌드 통과.
> - 개발 서버(비회원): 하단 메뉴 달력·매출·마이페이지·홈 이동, 차량 관리에서 옆 메뉴 열기 → 바깥 눌러 닫힘, `/app/soon` "준비 중인 화면입니다.", 홈에 당겨서 새로고침 표시 있음, 일지 [+ 추가] → 상차지 입력 → 뒤로가기 → "저장하지 않은 입력이 있습니다" 확인 창(`historyMoves` 새 자리), 콘솔 오류 없음. 지시서 검증의 "없는 주소 → 준비 중 화면"은 원래 그런 동작이 없음 — `/app/` 아래 없는 주소는 빈 칸(`app/routes/AppShellRoutes.jsx`에 그물 주소 없음, `App.jsx:155`의 `*`는 `/app` 밖만 `/auth`로) — 이번에 안 바뀜, `/app/soon`으로 확인.
> - 지시서 오류(데스크톱 AI): `boot.js` 나눌 줄 범위를 `:16-121`로 적음(실제 `:13-115`), 가짜 모듈 `EmployerLinkCard.test.js:18`을 빠뜨림, 검증 항목 "없는 주소 → 준비 중 화면"은 원래 없는 동작 — 교차검증 정정이 맞음.
> - push 뒤 CI verify·deploy 초록(run 39401866393).
> - §5 리뷰(데스크톱 AI): ①범위 = 이름 바뀜 49·새 2(`appSession.js`, `routes/lazyPages.js` — 경로만 달라 git이 새 파일로 봄, 경로 빼고 같음을 비교)·지움 1(옛 `lazyPages.js`)·고침 24 ②새 파일 `appSession.js`(지시서대로), 새 저장 키 0 ③타입 꼼수 0 ④`boot.js` 157→50, `appSession.js` 112 ⑤테스트 수 1,424 → 1,424 ⑥공용 값 — `buildCloudAppSession`·`ownerKeyFromSession` 쓰는 곳 전부 새 출처 ⑦목표 1~7 대조. 나눈 부분 직접 비교: 옛 `boot.js` 13~115줄 ↔ `appSession.js` 10~112줄 다른 줄 2(`buildBootSession` 앞 `export`·JSDoc 경로), 옛 117줄~끝 ↔ 새 `boot.js` 10줄~끝 같음. `features/`·`data/`·`shared/`·`domain/` 운영 파일의 `app/` 부르기 **0건**. `src/components/` 없어짐.
> - 배포본 로그인 확인(2026-10-20, 내장 브라우저): 새 묶음(`index-CB8_ibpw.js`), 새로고침 뒤 로그인 유지(세션 복원 `appSession`), 옆 메뉴 열기 → 바깥 눌러 닫힘, 하단 메뉴 달력·매출·마이페이지·홈 이동, 개인정보·앱 설정 열림(입력 잠김 0 — 불러오기 끝난 상태, `useHydrationLock` 새 자리), 일지 [+ 운행일지 추가] → 상차지 임시 입력 → 뒤로가기 → "저장하지 않은 입력이 있습니다" → [계속 입력](입력 남음) → [취소] → 새로고침 뒤 저장 안 됨, 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-20). 확인 중 발견한 "`/app/` 아래 없는 주소 빈 화면"은 로드맵 96으로 등재(보리 지시).

### 쉬운 요약
- 앱 틀(`app/`) 안을 "화면 주소 목록(`routes/`)"과 "화면 바깥 틀(`shell/` — 하단·옆 메뉴, 알림 띠, 오류 화면)"로 나누고, `components/`에 남은 앱 틀 부품도 `shell/`로 옮겨 `components/` 폴더를 없앱니다.
- 기능·데이터 폴더가 위쪽 앱 틀을 거꾸로 부르던 7곳을 바로잡습니다. 같이 쓰던 파일 4개를 아래층으로 내리는 방식이라, 화면 동작은 그대로입니다.

### 현재 상태 (코드 확인 — grep)
- 위층 부르기 7곳(59-G 진행 기록): 일지 `DayLogPage.jsx:21`·`usePanelBackGuard.js:5` → `app/historyMoves.js` / 계정 `InviteRedeemModal.jsx:5`·기사 `EmployerLinkCard.jsx:4` → `app/boot.js`(`buildCloudAppSession`·`ownerKeyFromSession`) / 계정 `PersonalInfoPage.jsx:5`·설정 `AppSettingsPage.jsx:7` → `app/useHydrationLock.js` / `data/cloud/supabaseClient.js:3` → `app/routerBasename.js`.
- 내릴 파일들: `historyMoves.js`(73줄, 가져오는 것 없음 — 주소 기록만 다룸, 앱 틀·일지가 씀), `useHydrationLock.js`(17줄, `data/store/app-store.js`만 부름), `routerBasename.js`(11줄, 가져오는 것 없음, `main.jsx`·`supabaseClient`가 씀), `boot.js`(157줄)의 세션 만들기 부분 `fetchAccountProfile`·`isProfileRowMissing`·`buildCloudAppSession`·`LINK_RETRY_MS`·`buildBootSession`·`sessionFromLink`·`ownerKeyFromSession`(`:16-121` — 서버·`data/` 함수만 부름). `restoreSessionOnBoot`(`:123-`)은 앱 시작 흐름이라 `app/`에 남김.
- `boot.js` 함수를 부르는 테스트: `bootLinkCheck`·`bootNeedsProfile`·`bootSettleUnlink`·`employedDriverSession`(`buildCloudAppSession`·`isProfileRowMissing`·`ownerKeyFromSession`·`restoreSessionOnBoot`).
- `components/`에 남은 것: `BottomNav`(+ 테스트 2·디자인)·`SideMenu`(+ 테스트)·`side-menu/SideMenuIcons`·`PullRefreshIndicator`(+ 디자인)·`BootRetryScreen`·`LoadFailScreen`·`LoadingScreen`(+ 테스트)·`PageLoadErrorBoundary`(+ 테스트)·`boot-retry.css`·`ComingSoonPage` + 여러 화면 검사 테스트 3(`accountPermissionUi`·`modalOutsideClick`·`taxInvoiceMenuMoved`).

### 목표 상태 (기대 동작)
1. 내리기(위층 부르기 7곳 해소):
   - `app/historyMoves.js`(+ 테스트) → `shared/utils/historyMoves.js`
   - `app/routerBasename.js` → `shared/utils/routerBasename.js`
   - `app/useHydrationLock.js` → `data/store/useHydrationLock.js`
   - `app/boot.js`의 세션 만들기 부분 → 새 `data/cloud/appSession.js`(위 함수들, 본문 글자 그대로). `app/boot.js`에는 `restoreSessionOnBoot`만 남고 새 파일에서 가져다 씀.
   - 결과: `features/`·`data/`·`shared/` 운영 파일이 `app/`을 부르는 곳 **0건**(grep).
2. `app/routes/` ← `AppShellRoutes`·`lazyPages`·`MainPageRoute`·`ComingSoonRoute`·`AuthRoute`(+ 테스트)·`RequireSession`·`sessionGate`(+ 테스트)·`workLogNavigation`(+ 테스트).
3. `app/shell/` ← `AppShell`·`HydrationRetryBanner`·`OfflineBanner`(+ 테스트)·`ToastMessage`(+ 테스트)·`subLogMenuItems`(+ 테스트)·`fromLogNavigation`·`useBackToExit`(+ 테스트)·`usePageTransition`(+ 테스트)·`usePullToRefresh`(+ 테스트) + `components/`의 앱 틀 부품 전부(위 목록, 디자인·테스트·`side-menu/` 포함).
4. `app/` 바로 아래에 남는 것: `App.jsx`·`providers.jsx`·`boot.js`·`bootHomeGuard`·`useAppSession`·`guestSessionPersist`·`pendingWriteRetryListeners`·`chunkReload`·`errorReport`·`haptic`·`registerServiceWorker`와 각 테스트, 앱 전체 테스트(`App.*.test.js`·`guestLoadFail`·`noMiddleDot`·`pwaManifest`), 여러 화면 검사 테스트 3(`components/`에서 옮김).
5. `components/` 폴더 없어짐. `package.json` `test:app`에서 `src/components/**` 뺌(`src/app/**`가 `routes/`·`shell/`까지 덮는지 확인).
6. 테스트 수 그대로, 전부 통과. 빌드 결과 내용 같음(묶음 이름이 바뀌면 진행 기록에).
7. `boot.js` 테스트 4개는 함수가 옮겨진 곳을 부르도록 가져오기 경로만 고침(단언 그대로).

### 건드릴 파일
- 옮김(`git mv`) 약 45개, 새 파일 1(`data/cloud/appSession.js` — `boot.js`에서 함수를 옮김, `git diff`로 본문 같음 확인), 고침: `app/boot.js`(옮긴 함수 빼고 가져오기 줄), 부르는 파일 경로(7곳 포함), `package.json` 1줄.
- 함수 본문·문구는 안 바꿈.
- 코드 주석 최종 문구(`data/cloud/appSession.js` 머리): `// 로그인 세션 만들기 — 프로필·연동 기사 여부로 AppSession과 ownerKey를 정한다(앱 시작·초대 코드 입력·연동 해제가 같이 씀).`, `app/boot.js` 머리는 "세션 복원 흐름만, 세션 만들기는 data/cloud/appSession.js"로 한 줄 고침.

### 안 건드릴 것
- 기능 사이 부르기 정리 — 59 끝(59-I 뒤) 의존 방향 검사 때 보리 결정.
- 92번(같은 값 손 넘기기 → 공용 값) — 59-I.
- 앱 시작 흐름의 동작(`restoreSessionOnBoot`·`useAppSession`).

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 1의 "`app/` 부르기 0건" grep, `components/` 경로 grep 0건을 진행 기록에.
- `git diff -M --stat`: 옮긴 파일이 이름 바뀜으로 잡히는지, `appSession.js` 함수 본문이 옛 `boot.js`와 같은지(diff), 경로 아닌 바뀐 줄 목록을 진행 기록에.
- 빌드 결과 내용 비교(이름 해시 뺌) — 차이를 진행 기록에.
- 개발 서버(비회원): 앱 켜기·하단 메뉴 네 칸 이동·옆 메뉴 열고 닫기·당겨서 새로고침 표시·없는 주소 → 준비 중 화면, 일지 입력 칸 연 채 뒤로 → 확인 창(뒤로가기 기록 `historyMoves`), 콘솔 오류 없음.

### 파일 길이
- 줄어들거나 그대로(`boot.js` 157 → 약 40, 새 `appSession.js` 약 120). 200줄 넘는 파일은 경로 줄만 — §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(위치·파일 나누기만).
- 테스트가 하나라도 실패하면(특히 앱 시작·세션 테스트의 가짜 모듈 경로) 멈추고 어느 경로인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 새로고침해도 로그인 유지(세션 복원), 하단 메뉴 네 칸·옆 메뉴.
2. 개인정보·앱 설정 화면 열림(불러오기 잠금 훅 새 자리).
3. 일지 입력 칸 연 채 뒤로가기 → 확인 창 → [계속 입력], 콘솔 오류 없음.
4. 기사 계정 소속 연결 카드·초대 코드 입력(`appSession` 쓰는 곳)은 차주 계정이라 화면 확인 불가 — 테스트로만(`EmployerLinkCard.test`·`MyPage.inviteModal.test`).
