# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59 폴더 구조를 목표 구조로 — 나누는 방식(1회 보고) + 첫 조각 59-A `[x]`

보리 승인(2026-10-19, 60 `[x]`). 보리 확정 순서 "… 76 → 60 → **59**". 로드맵 59 `[확인: 2026-10-12 보리 — 목표 구조로 변경]`, 목표는 `docs/architecture-target.md` §4. 59에 같이 하기로 한 것: 92(같은 값 손으로 넘기기 → 공용 값), 88-B(`outboxTypes`·`outboxErrors`·`outboxCommit` 이름·자리), `directMutations.js:2` 옛 주석, `EmployerLinkCard`(60에서 넘어옴).

### 1. 나누는 방식 (AGENTS §3 — 하위 슬라이스 1회 보고)

**아래층부터 옮김**(공용 → 저장 엔진 → 기능 → 앱 틀). 문서 §5 예시는 "기능 폴더(`cars/`)부터"였지만, 기능을 먼저 옮기면 그 기능이 부르는 공용 부품 경로를 나중에 한 번 더 고쳐야 함 → 아래층부터면 경로를 한 번씩만 고침.

| 조각 | 하는 일 | 대략 파일 |
|---|---|---|
| **59-A** | `shared/` 만들기 — 맨 위 디자인 파일 17개 → `shared/styles/`, `components/shared/` → `shared/ui/`, 공용 부품 `PageHeader`·`ConfirmModal`·`SwitchRow` → `shared/ui/` | 옮김 약 45 + 경로 고침 약 80 |
| 59-B | `data/store/` ← `store/`, `data/cloud/` ← `supabaseClient.js`·`cloudSession.js`·`cloudStorage.js` | 약 25 + 경로 |
| 59-C | `data/hydrate/`·`data/sync/` ← `lib/`의 불러오기·저장 창구(`hydrate*`·`*CloudCommit`·`*Mutations`·`pending*`·`durable*` 등) + 88-B 이름 정리 | 약 40 + 경로 |
| 59-D | 기능 폴더 1 — `features/cars`·`clients`·`drivers` (+ `EmployerLinkCard`의 앱 시작 코드 부르기 해소) | 약 35 |
| 59-E | 기능 폴더 2 — `features/receivables`·`revenue`·`documents` | 약 35 |
| 59-F | 기능 폴더 3 — `features/calendar`·`home`·`notifications`·`account`·`settings`·`support` | 약 45 |
| 59-G | 기능 폴더 4 — `features/day-log`(가장 큼, 43개) | 약 45 |
| 59-H | `lib/` 남은 것 정리(내보내기·엑셀·PDF는 쓰는 기능 폴더로, 재수출 껍데기 5개·`domain/`과 같은 이름 11쌍 정리), `app/routes`·`app/shell` | 약 30 |
| 59-I | 92번 — `useToast()`·`ownerKey`·`session`·이동 함수를 공용 값(Context)으로, 화면 46개의 손 넘기기 삭제 | 약 50 |

- 모든 조각 공통: `git mv`로 옮겨 기록 유지, 동작 변화 0(경로만), 경로는 지금처럼 상대 경로(`../`) — 별칭(`@/…`) 같은 도구 설정은 안 들임. 테스트 실행 범위(`package.json`의 `test:unit`·`test:app` 파일 목록)는 새 폴더가 생기는 조각에서 같이 넓힘. 파일을 경로로 읽는 테스트 10개(`readFileSync` — 예: `components/shared/colorContrast.test.js`가 `variables.css`를 읽음)는 그 파일이 옮겨지는 조각에서 경로를 고침.
- 의존 방향 검사(아래층이 위층을 부르면 실패) 자동화는 마지막에 — CI에 넣을지는 그때 보리 결정(`architecture-target.md` §4).
- 조각마다 따로 착수지시서·승인·커밋 2개(규칙 그대로).

### 2. 이번 조각: 59-A `shared/` 만들기

> **진행(2026-10-19)**: 보리 "착수지시서 확정,작업진행해". 코드 커밋 react-app `716424e`(124개 파일: 옮김 50 + 경로만 고침 74).
> - 교차검증(AGENTS §1) 고친 것: `components/shared/` 디자인 파일은 7개가 아니라 6개, 테스트는 7개가 아니라 8개(`colorContrast`·`CardActionButtons`·`ClearableInput`·`confirmDialog`·`CurrencyInput`·`ModalShell`·`Skeleton`·`TemporalInput`) → 옮긴 파일 약 45가 아니라 50개(맨 위 디자인 17 + `components/shared/` 29 + `PageHeader`·`PageHeader.test`·`ConfirmModal`·`SwitchRow` 4). `PageHeader`·`ConfirmModal`·`SwitchRow` 전용 디자인 파일은 없음. 경로로 읽는 테스트 중 이번에 고칠 것은 2개(`colorContrast.test.js`·`CardActionButtons.test.js`의 `management-common.css`), 나머지는 옮기지 않는 파일을 읽음. 목표 2의 "맨 위에 남는 것"에 그림 폴더 `src/assets/`(파일 아닌 폴더, 59 어느 조각에도 없음)도 남음.
> - 옮기기는 `git mv`, 경로는 옮긴 위치 기준으로 다시 계산(대본으로 79개 파일, 손으로 고친 것은 위 테스트 2줄과 `package.json` `test:app` 범위 1줄).
> - 목표 3: 옛 경로 grep(`components/shared`, `src/*.css` 꼴, `components/PageHeader`·`ConfirmModal`·`SwitchRow`, `../variables.css` 꼴) 0건.
> - 목표 4: 옮기기 전·후 `npm run build` 디자인 결과 15개 파일 내용(이름 해시 뺌) 비교 — 모두 같음.
> - `git diff -M`: 50개 모두 이름 바뀜으로 잡힘(42개 100%, 나머지 8개는 안의 경로 줄만 바뀜), 바뀐 줄 146개 중 `import`·`mock.module` 경로 아닌 줄은 위 손으로 고친 3줄뿐.
> - `npm test` 1004 + 499 통과(옮기기 전과 같은 수), 타입 검사·빌드 통과.
> - 개발 서버(비회원): 홈·달력·일지·차량 관리·마이페이지·앱 설정·알림 화면 열기, 차량 추가 → 삭제 확인 창(`ConfirmModal`) 뜸, 모양 그대로(화면 캡처), 콘솔 오류 없음.
> - 지시서 오류(데스크톱 AI): `components/shared/` 디자인·테스트 개수를 틀리게 적음(7·7 → 실제 6·8) — 교차검증 정정이 맞음.
> - push 뒤 CI verify·deploy 초록(run 39241663001).
> - §5 리뷰(데스크톱 AI): ①범위 = 옮김 50(이름 바뀜 R100 42·R89~99 8) + 경로만 고침 74 ②새 파일 0(옮김만), 새 저장 키 0 ③타입 꼼수 0 ④줄 수 그대로(경로 줄만 — 200줄 넘는 파일도 §6 예외 결정대로) ⑤테스트 수 그대로, 바뀐 테스트 줄은 경로뿐 ⑥공용 디자인 영향 — 빌드 결과 디자인 내용 같음(아래) ⑦목표 1~5 대조. 경로(`import`·`mock.module`·`import()`·JSDoc `import(...)`) 아닌 바뀐 줄은 `package.json` `test:app` 1줄과 경로로 읽는 테스트 2줄뿐. 옛 경로 grep 0, `src/` 맨 위 파일은 `main.jsx`·`supabaseClient.js`뿐, `components/shared/` 없음.
> - 배포본 로그인 확인(2026-10-19 20:04, 내장 브라우저): 새 묶음 파일(`index-BH1dhE45.js`)인데 **디자인 파일 이름이 옮기기 전 배포와 같음**(`index-WIA-vpHP.css` — 내용 해시라 디자인 결과가 글자까지 같다는 뜻). 홈·차량 관리·앱 설정 화면 모양 그대로(캡처), 알림 숫자 1은 오후 8시라 "오늘 일지 비어 있음" 알림, 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-19).

#### 쉬운 요약
- 어디서나 쓰는 공용 디자인 파일과 공용 화면 부품을 `shared/` 한 곳에 모읍니다(지금은 맨 위·`components/` 바로 아래·`components/shared/` 세 군데에 흩어짐).
- 파일 위치와 그 파일을 부르는 경로만 바뀌고, 화면·동작은 그대로입니다.

#### 현재 상태 (코드 확인)
- 맨 위 디자인 파일 17개(`src/*.css`: `variables`·`app-base`·`account-flow`·`auth-form`·`onboarding`·`switch`·`modal-base`·`toast`·`side-menu`·`management-common`·`controls-common`·`app-shell-base`·`header-controls`·`shared-controls`·`modal-form`·`offline-banner`·`index`) — 부르는 곳 `app/App.jsx:21-35`, `app/OfflineBanner.jsx:5`, `main.jsx:5`(grep).
- `components/shared/` 운영 15개 + 디자인 7개 + 테스트 7개 — 부르는 파일 46개(grep `/shared/…`).
- `components/PageHeader.jsx`(부르는 파일 24개)·`ConfirmModal.jsx`(12)·`SwitchRow.jsx`(2).
- 경로로 읽는 테스트: `components/shared/colorContrast.test.js:8`(`variables.css`) 등.

#### 목표 상태 (기대 동작)
1. `src/shared/styles/`에 맨 위 디자인 파일 17개, `src/shared/ui/`에 `components/shared/`의 모든 파일(테스트 포함)과 `PageHeader`·`ConfirmModal`·`SwitchRow`(+ 각 테스트·디자인 파일이 있으면 함께).
2. `src/` 맨 위에 남는 것은 `main.jsx`·`supabaseClient.js`(59-B에서 옮김)뿐. `components/shared/` 폴더 없어짐.
3. 옮긴 파일을 부르는 모든 경로가 새 위치를 가리킴. 옛 경로 grep(`components/shared/`, `'../variables.css'` 꼴의 맨 위 디자인 경로, `components/PageHeader` 등) 0건.
4. 화면 결과 그대로 — 빌드 결과물의 디자인 규칙 내용이 옮기기 전과 같음(글자 단위 비교).
5. 테스트 수 그대로, 전부 통과. `test:app` 실행 범위에 `src/shared/**/*.test.js` 추가.

#### 건드릴 파일
- 옮김(`git mv`): 위 1번 목록(약 45개).
- 경로만 고침: 옮긴 파일을 부르는 파일 약 80개(목록은 진행 기록에 grep 결과로), 옮긴 파일 안의 상대 경로, 경로로 읽는 테스트, `package.json`(`test:app` 범위 1줄).
- 동작 코드·문구·디자인 내용은 한 글자도 안 바꿈.

#### 안 건드릴 것
- 기능 화면 폴더(`components/day-log` 등)의 위치 — 59-D~G. 기능 전용 디자인 파일(`components/*.css`, `components/<기능>/*.css`) — 그 기능을 옮길 때.
- `supabaseClient.js` — 59-B. `lib/`·`store/` — 59-B·C.
- `components/shared/` 안이라도 사실상 한 기능 전용인 부품(예: `AppTopBar`는 홈, `ShareFileModal`은 서류 발급) — 이번엔 폴더째 옮기고, 기능 폴더로 내릴지는 그 기능 조각에서 판단.

#### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 3 grep 0건을 진행 기록에.
- 목표 4: 옮기기 전·후 `npm run build` 결과의 CSS 파일 내용 비교(파일 이름 해시 말고 내용) — 같음을 진행 기록에.
- `git diff -M --stat`로 옮긴 파일이 "이름만 바뀜(100%)"인지, 바뀐 줄은 `import` 경로뿐인지 확인해 진행 기록에.
- 개발 서버(비회원): 홈·달력·일지·차량 관리·마이페이지·앱 설정 열기 → 모양 그대로, 콘솔 오류 없음.

#### 파일 길이
- 옮기기만 해서 줄 수 그대로(경로 줄 글자만 바뀜). 200줄 넘는 파일(`DayLogPage.jsx` 등)은 경로 줄만 바뀜 — §6의 "200줄 넘는 파일 수정 → 분리설계" 대상 아님(보리 결정 2026-10-19, 아래 확인할 점 2).

#### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(파일 위치·경로만).
- 빌드 결과 디자인 내용이 하나라도 다르면(불러오는 순서가 바뀐 경우 등) 멈추고 무엇이 다른지 이 파일에 적어 다시 승인받는다.

#### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 홈·달력·일지·차량 관리·마이페이지·앱 설정·알림 화면을 차례로 열어 모양이 전과 같은지(화면 캡처 비교).

### 확인할 점 — 답 받음 `[확인: 2026-10-19 보리 — "응 권하는대로"]`: 1 아래층부터 9조각, 2 경로 줄만 바뀌는 200줄 넘는 파일은 §6 분리설계 예외
1. 위 나누는 방식(아래층부터, 59-A~I 9조각)으로 진행할까요? 다른 안: 문서 예시대로 기능 폴더(`cars/`)부터.
2. 200줄 넘는 파일에서 **경로 줄만** 바뀌는 경우(59 전체에서 반복됨)는 §6 분리설계 대상이 아닌 것으로 봐도 될까요? 권함: 예(줄 수·내용 그대로라). 안 되면 해당 파일마다 분리설계부터 해야 해서 59가 크게 늘어납니다.
