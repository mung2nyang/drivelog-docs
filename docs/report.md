# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-F2 기능 폴더 4 — 계정·설정·고객센터를 `src/features/`로 `[x]`

보리 승인(2026-10-20, 59-F1 `[x]`). 59-F를 둘로 나눈 뒤쪽(보리 결정 2026-10-20). 59-D~F1의 **자리 정하는 규칙** 그대로: ① 화면·부품·디자인 → `features/<기능>/ui/`, 훅 → `hooks/`, 그 기능만 쓰는 함수 → `api/` ② `lib/` 함수를 두 기능 이상(또는 앱 틀·`data/`)이 쓰면 → `data/sync/` ③ 다른 기능이 쓰는 부품·훅은 주인 기능에 두고 "기능 사이 부르기" 목록만. 옮기기·경로만, `git mv`, 경로 줄만 바뀌는 200줄 넘는 파일은 §6 예외.

> **진행(2026-10-20)**: 보리 "착수지시서 확정,작업진행해". 코드 커밋 react-app `3891b02`(81개 파일: 옮김 67 + 경로 고침 14).
> - 교차검증(AGENTS §1): 표의 파일·쓰는 곳 모두 코드와 같음(옮길 파일 67개 — 계정 35·설정 19·고객센터 7·`data/sync` 6). `guestBackup`은 설정·알림, `onboardingFinish`는 `app/App.jsx`·온보딩, `messageTemplates`는 일지·서류·문자 문구 설정이 씀. 고친 것 없음.
> - 같은 대본으로 옮기기·경로 다시 계산(70개 파일). 손으로 고친 줄 0개. `package.json` 안 바뀜.
> - 목표 1: `components/auth/` 없어짐. `components/` 바로 아래 남은 것: 앱 틀 부품(`BootRetryScreen`·`BottomNav`·`ComingSoonPage`·`LoadFailScreen`·`LoadingScreen`·`PageLoadErrorBoundary`·`PullRefreshIndicator`·`SideMenu`·`side-menu/` + 디자인·테스트), 여러 화면 검사 테스트 3개(`accountPermissionUi`·`modalOutsideClick`·`taxInvoiceMenuMoved`), `day-log/`.
> - 목표 2: 옛 경로 grep 0건.
> - 목표 3: `shared/`가 위층을 부르는 곳은 59-E 예외 아이콘(`shared/ui/CardActionButtons.jsx:2`)과 테스트 `PageHeader.test.js:18-19`뿐, 새로 생긴 것 없음.
> - 빌드 비교: 옮기기 전·후 48개 파일, 해시 지우고 비교 → 이름·내용 모두 같음.
> - `git diff -M`: 67개 모두 이름 바뀜으로 잡힘, 경로 아닌 바뀐 줄 0.
> - `npm test` 885 + 618 = 1503 통과(합계 같음 — `lib/` 테스트 일부가 `features/`(app) 범위로), 타입 검사·빌드 통과.
> - 개발 서버: 로그아웃 첫 화면(`/auth`) → 비회원 시작, 마이페이지, 앱 설정(백업 칸, 고정 노선 거래처 [+ 추가] → "고정 노선 거래처" 창 → [취소]로 닫힘), 문자 문구 설정, 고객센터, 공지, 개인정보 열림, 콘솔 오류 없음.
> - 목표 5 "기능 사이 부르기"(이전 목록에 더해짐): `account/ui/PersonalInfoPage.jsx:8` → `drivers/ui/EmployerLinkCard.jsx`(59-D에 "기능 밖에서"로 적었던 것이 기능 사이로 바뀜). 그 밖에 새 셋을 부르는 다른 기능 없음. 기능 밖에서(테스트만): `components/accountPermissionUi.test.js:20`·`taxInvoiceMenuMoved.test.js:17` → `account/ui/MyPage.jsx`, `components/modalOutsideClick.test.js:23-24` → `account/ui/InviteRedeemModal.jsx`·`settings/ui/FixedRouteClientModal.jsx`. 앱 틀(`App`·`AuthRoute`·`lazyPages`)에서 부르기는 허용 방향.
> - push 뒤 CI verify·deploy 초록(run 39390943665).
> - §5 리뷰(데스크톱 AI): ①범위 = 옮김 67(모두 이름 바뀜) + 경로 14 ②새 파일 0, 새 저장 키 0 ③타입 꼼수 0 ④줄 수 그대로(§6 예외) ⑤테스트 수 1,424 → 1,424 ⑥공용 값 — 해당 없음 ⑦목표 1~5 대조(`shared/` 위로 부르기는 59-E 예외 아이콘 1곳뿐). 경로 아닌 바뀐 줄 0. `src/features/` 13개 기능, `components/` 바로 아래는 앱 틀 부품·여러 화면 검사 테스트 3·`day-log/`만.
> - 배포본 로그인 확인(2026-10-20, 내장 브라우저): 묶음 파일 이름이 이전 배포와 같음(빌드 결과 같음과 맞음). 마이페이지(메뉴 목록 그대로) → 개인정보(사업자등록번호 등 값 그대로), 데이터 다운로드 창 열고 닫음(내려받지 않음), 앱 설정(일상점검표 켜짐 그대로) → 고정 노선 거래처 [수정] 창("테스터거래처") → [취소], 문자 문구 설정, 고객센터(FAQ), 공지사항 열림, 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-20).

### 쉬운 요약
- 로그인·처음 정보 입력·마이페이지·개인정보·데이터 내려받기(계정), 앱 설정·문자 문구 설정(설정), 고객센터·공지(고객센터) 화면과 그 화면만 쓰는 부품·훅·저장 함수를 기능 폴더 셋으로 옮깁니다.
- 여러 곳이 같이 쓰는 함수 3개(비회원 백업 `guestBackup`, 처음 정보 저장 마무리 `onboardingFinish`, 문자 문구 `messageTemplates`)는 `data/sync`로 내립니다.
- 화면·저장 동작은 그대로입니다.

### 현재 상태 (코드 확인 — 파일마다 쓰는 곳 grep)
| 옮길 곳 | 파일 | 쓰는 곳 |
|---|---|---|
| `features/account/ui` | `components/AuthPage`(+ `AuthPage.google.test`)·`auth/AuthIntroView`·`auth/WelcomeProfileView`(+ 테스트)·`auth/welcome-profile.css`·`OnboardingPage`·`MyPage`(+ 테스트 2)·`mypage.css`·`mypage-shortcuts.css`·`PersonalInfoPage`(+ 테스트 5)·`PersonalInfoPage.css`·`DataDownloadModal`·`InviteRedeemModal`·`InviteRedeemModal.css`·`LinkStatusChip`(+ 테스트) | 앱 틀(`AuthRoute`·`App`·`lazyPages`)과 계정 화면 안 |
| `features/account/hooks` | `components/usePersonalInfoDraft` | `PersonalInfoPage` |
| `features/account/api` | `lib/accountWithdrawal`·`memberBackup`·`memberRestore`·`memberRestoreInspections`·`memberRestoreRecords`(각 테스트) | 개인정보(탈퇴)·데이터 내려받기 창만 |
| `features/settings/ui` | `components/AppSettingsPage`(+ 테스트 3)·`AppSettingsBackupSection`·`app-settings.css`·`app-settings-groups.css`·`FixedRouteBlock`·`FixedRouteClientLink`(+ 테스트)·`FixedRouteClientModal`(+ 테스트)·`RoutePresetEditor`(+ 테스트)·`RunCountChips`(+ 테스트)·`MessageSettingsPage`(+ 테스트)·`message-settings.css` | 앱 설정·문자 문구 설정 화면 안 |
| `features/support/ui` | `components/CustomerCenterPage`(+ 테스트)·`NoticePage`(+ 테스트)·`customer-center.css` | 고객센터·공지 |
| `features/support/api` | `lib/supportInquiryMutations`(+ 테스트) | 고객센터만 |
| `data/sync` | `lib/guestBackup`(+ 테스트)·`onboardingFinish`(+ 테스트)·`messageTemplates`(+ 테스트) | `guestBackup`: 설정·알림 / `onboardingFinish`: 앱 틀 `App`·온보딩 / `messageTemplates`: 일지·서류·문자 문구 설정 |

- 이번 조각 밖: 앱 틀 부품(`BottomNav`·`SideMenu`·`side-menu/SideMenuIcons`·`BootRetryScreen`·`LoadFailScreen`·`LoadingScreen`·`PageLoadErrorBoundary`·`PullRefreshIndicator`·`ComingSoonPage`과 그 테스트·디자인) — 59-H. 여러 화면을 함께 검사하는 테스트(`accountPermissionUi`·`modalOutsideClick`·`taxInvoiceMenuMoved`) — 위치 그대로, 경로만. `lib/` 재수출 껍데기 겸 저장(`practiceSettings`·`profile`·`profileCloudCommit` 등) — 59-H. 일지(`components/day-log/`) — 59-G.

### 목표 상태 (기대 동작)
1. 위 표대로 옮김(짝 테스트 포함, 약 65개). `components/auth/` 폴더 없어짐. `components/` 바로 아래에는 앱 틀 부품·여러 화면 검사 테스트·`day-log/`만 남음.
2. 옛 경로 grep 0건: `components/auth/`, `components/(AuthPage|OnboardingPage|MyPage|PersonalInfoPage|usePersonalInfoDraft|DataDownloadModal|InviteRedeemModal|LinkStatusChip|AppSettingsPage|AppSettingsBackupSection|FixedRouteBlock|FixedRouteClientLink|FixedRouteClientModal|RoutePresetEditor|RunCountChips|MessageSettingsPage|CustomerCenterPage|NoticePage)`, 해당 디자인 파일 이름, `lib/(accountWithdrawal|memberBackup|memberRestore|memberRestoreInspections|memberRestoreRecords|supportInquiryMutations|guestBackup|onboardingFinish|messageTemplates)(\.js)?'`.
3. `shared/`가 위층을 부르는 곳 — 59-E 예외 아이콘 1곳만(grep, 새로 생기지 않음).
4. 테스트 수 그대로, 전부 통과(`package.json` 안 바뀜, 확인만). 빌드 결과 내용 같음(묶음 이름이 바뀌면 진행 기록에).
5. "기능 사이 부르기" 목록을 진행 기록에 grep 결과로 — 이전 목록에 이어 적음.

### 건드릴 파일
- 옮김(`git mv`) 약 65개, 경로만 고침(부르는 파일 + 옮긴 파일 안 상대 경로 + 테스트 `mock.module`·`import()`·경로로 읽는 테스트). 개수는 진행 기록에.
- 동작 코드·문구는 안 바꿈.

### 안 건드릴 것
- 위 "이번 조각 밖", 기능 사이 부르기 정리(59 끝), 92번(59-I), `EmployerLinkCard`(기사 폴더, 59-H에서 앱 시작 코드 부르기 정리).

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 2·3 grep, 목표 5 목록을 진행 기록에.
- `git diff -M --stat`: 옮긴 파일이 이름 바뀜으로 잡히는지, 경로 아닌 바뀐 줄 목록을 진행 기록에.
- 빌드 결과 내용 비교(이름 해시 뺌) — 차이를 진행 기록에.
- 개발 서버(비회원): 마이페이지, 앱 설정(고정 노선 거래처 연결 창 열고 닫기, 백업 칸), 문자 문구 설정, 고객센터, 공지 → 콘솔 오류 없음. 로그인 첫 화면은 로그아웃 상태라 개발 서버에서 열림.

### 파일 길이
- 옮기기만 해서 줄 수 그대로. 200줄 넘는 파일(`CustomerCenterPage.jsx` 228)은 경로 줄만 — §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(위치·경로만).
- 테스트가 하나라도 실패하면 멈추고 어느 경로인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 마이페이지 → 개인정보(사업자 정보 그대로), 데이터 내려받기 창 열고 닫기.
2. 앱 설정(일상점검표 켜짐 그대로), 고정 노선 거래처 연결 [수정] 창 열고 닫기, 문자 문구 설정 열기.
3. 고객센터·공지 열기, 콘솔 오류 없음.
