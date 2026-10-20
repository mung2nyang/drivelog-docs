# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-D 기능 폴더 1 — 차량·거래처·기사를 `src/features/`로 `[x]`

보리 승인(2026-10-20, 59-C `[x]`). 59번 네 번째 조각(로드맵 59 `[확인: 2026-10-19 보리]`). 59-A~C와 같은 방식(옮기기·경로만, 상대 경로, `git mv`, 경로 줄만 바뀌는 200줄 넘는 파일은 §6 예외). 기능 폴더는 처음이라 자리 정하는 규칙을 아래에 적음.

> **진행(2026-10-20)**: 보리 "착수지시서 진행해, 확인할점 두개는 권하는점으로". 코드 커밋 react-app `ba0b845`(86개 파일: 옮김 65 + 지움 2 + 경로 고침 19).
> - 확인할 점 답 `[확인: 2026-10-20 보리 — "권하는점으로"]`: 1 이번엔 세 폴더로 옮기기만, 기능 사이 부르기는 목록으로 남겨 59 끝에서 결정 / 2 `EmployerLinkCard`의 앱 시작 코드 부르기 바로잡기는 59-H로.
> - 교차검증(AGENTS §1): 표의 파일·쓰는 곳 모두 코드와 같음(`carInviteFromDraft`는 차량 화면 2곳만, `driverInviteSms`는 `DriverFormModal`만, `clientCloudSave`·`vehicleDeletion`·`vehicleRename`·`requestDriverInviteSave`는 `data/sync`로 가는 파일들이 부름). 고친 것: 옮길 파일 "약 50"이 아니라 65개(차량 15·거래처 11·기사 24·`data/sync` 15, 짝 테스트 포함).
> - 순서: `app/lazyPages.js` 2줄을 진짜 화면으로 바꾸고 넘겨주기 파일 2개 `git rm` → 59-A~C와 같은 대본으로 옮기기·경로 다시 계산(75개 파일).
> - 손으로 고친 줄: `lazyPages.js` 2줄(위), `package.json` `test:app`에 `src/features/**/*.test.js`, 옛 경로가 남은 2줄 — `features/drivers/ui/DriverConnectionPage.jsx:2` 주석 속 `lib/directMutationActions.js`, `shared/ui/CardActionButtons.test.js:40` 읽는 경로 `components/clients/client-management.css`.
> - 목표 3: 옛 경로 grep 0건. `components/cars|clients|drivers` 폴더 없어짐.
> - 빌드 비교(옮기기 전 빌드 원본 보관 → 후): 48개 파일, 해시 지우고 비교 → 내용 모두 같음. 다른 것은 묶음 이름 `CarManagementPage`→`CarListPage`(JS·디자인), `ClientManagementPage`→`ClientListPage` 3개뿐(넘겨주기 파일이 없어져서).
> - `git diff -M`: 65개 모두 이름 바뀜으로 잡힘, 경로 아닌 바뀐 줄은 지운 넘겨주기 파일 2개(5줄)와 위 손으로 고친 줄뿐.
> - `npm test` 997 + 506 = 1503 통과(전과 합계 같음 — `carInviteFromDraft.test` 7개가 `lib/`(unit)에서 `features/`(app) 범위로 옮겨 감), 타입 검사·빌드 통과.
> - 개발 서버(비회원): 차량 2대 추가 → 삭제 확인 창, 거래처 추가 → 삭제 확인 창, 기사 연동 관리 화면 열림(비회원 안내 문구), 콘솔 오류 없음.
> - 목표 6 "기능 사이 부르기"(grep): `cars/ui/CarDriverConnectPanel.jsx:4` → `drivers/ui/driver-connection.css` / `clients/ui/ScopedClientsList.jsx:7` → `drivers/ui/linked-driver.css` / `drivers/ui/LinkedDriverClientsPage.jsx:10-11` → `clients/ui/ScopedClientsList.jsx`·`client-management.css` / (테스트) `clients/ui/ScopedClientsPages.test.js:19` → `drivers/ui/LinkedDriverClientsPage.jsx`. 기능 밖에서 부르기: `components/day-log/CallClientQuickAdd.jsx` → `clients/ui/ClientFormModal.jsx`, `components/PersonalInfoPage.jsx` → `drivers/ui/EmployerLinkCard.jsx`(그 기능이 옮겨질 때 기능 사이 부르기가 됨).
> - 지시서 오류(데스크톱 AI): 옮길 파일을 "약 50"으로 적음(실제 65) — 교차검증 정정이 맞음.
> - push 뒤 CI verify·deploy 초록(run 39382757501).
> - §5 리뷰(데스크톱 AI): ①범위 = 옮김 65(모두 이름 바뀜)·지움 2(넘겨주기)·경로 19 ②새 파일 0, 새 저장 키 0 ③타입 꼼수 0 ④줄 수 그대로(§6 예외) ⑤테스트 수 1,424 → 1,424(`test(` 줄 기준), 테스트 바뀐 줄은 경로뿐 ⑥공용 디자인 — 차량 화면 디자인 묶음 이름 해시가 옮기기 전 `CarManagementPage-DMpQeZnp.css`와 같은 `CarListPage-DMpQeZnp.css`(내용 같음) ⑦목표 1~6 대조. 경로 아닌 바뀐 줄 = `package.json` 1·주석 1·읽는 경로 1·지운 넘겨주기 5줄. `src/features/`는 `cars`(api·hooks·ui)·`clients`(hooks·ui)·`drivers`(api·ui).
> - 배포본 로그인 확인(2026-10-20, 내장 브라우저): 새 묶음(`index-BS5lM7qj.js`, 차량 화면 `CarListPage-ktuK8OE_.js`), 차량 3·거래처 1·연동 기사 1 그대로, 연동 기사 관리 화면(세금계산서 묶음 "64번시험 1건, 22가2222, 합계 11,000원") 열림, 기사 [수정] 창·차량 [수정] 창 열고 [취소], 마이페이지 개인정보 열림(차주 계정이라 소속 연결 카드는 안 나옴 — 기사 계정 화면은 `EmployerLinkCard.test`로만), 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-20).

### 쉬운 요약
- 차량 관리·거래처·기사 연동 화면과 그 화면만 쓰는 부품·훅·저장 함수를 `features/cars`·`features/clients`·`features/drivers`로 모읍니다.
- 여러 기능이 같이 쓰는 저장 함수(차량 저장·거래처 저장·연동 해제 등)는 `data/sync`로 내립니다.
- 한 줄짜리 넘겨주기 파일 2개(`CarManagementPage`·`ClientManagementPage`)는 지우고 화면 주소 목록이 진짜 화면을 바로 부르게 합니다.
- 화면·저장 동작은 그대로입니다.

### 자리 정하는 규칙 (59-D~G 공통으로 씀)
1. 화면·부품·그 디자인 파일 → `features/<기능>/ui/`, 처리 흐름 훅(`use…`) → `features/<기능>/hooks/`, 그 기능만 쓰는 저장·서버 함수 → `features/<기능>/api/`. 짝 테스트는 본 파일 옆.
2. `lib/`의 저장 함수를 **두 기능 이상(또는 앱 틀·`data/`)이 쓰면** → `data/sync/`(목표 구조의 "같이 쓸 것은 아래로").
3. 화면 부품·디자인 파일을 다른 기능이 쓰는 경우(아래 표 ※) → 이번엔 주인 기능 폴더에 두고 "기능 사이 부르기"로 진행 기록에 목록만 남김. 정리할지는 59 끝 의존 방향 검사 때 결정(확인할 점 1).

### 현재 상태 (코드 확인 — 파일마다 쓰는 곳 grep)
| 옮길 곳 | 파일 | 쓰는 곳 |
|---|---|---|
| `features/cars/ui` | `CarListPage`·`CarListItem`·`CarFormModal`·`CarDriverConnectPanel`·`CarDriverIncomeFields`·`CarShareBaseField`·`car-management.css` + 테스트 5(`CarDriverConnectPanel`·`CarDriverIncomeFields`·`CarFormModal`·`CarListPage.dupKey`·`CarListPage.flows`) | 차량 화면 안 |
| `features/cars/hooks` | `useCarListActions` | `CarListPage` |
| `features/cars/api` | `lib/carInviteFromDraft` + 테스트 | 차량만 |
| `features/clients/ui` | `ClientListPage`·`ClientListItem`·`ClientFormModal`※·`ClientTradeFields`·`OwnerScopedClientsView`·`ScopedClientsList`※·`client-management.css`※ + 테스트 2(`ClientFormModal`·`ScopedClientsPages`) | ※`ClientFormModal`은 일지 `CallClientQuickAdd`도, `ScopedClientsList`·`client-management.css`는 기사 화면도 씀 |
| `features/clients/hooks` | `useClientReorder`·`useScopedClients` | 거래처 화면 |
| `features/drivers/ui` | `components/DriverConnectionPage`(+ 테스트 5)·`DriverFormModal`(+ 테스트)·`components/drivers/` 전부(`DriverUnlinkControls`·`LinkedDriverManagementPage`·`LinkedDriverClientsPage`·`CarBusinessInfoSection`·`ClientInvoiceGroups`·`SettlementSummaryCard`·`EmployerLinkCard`※·`linkedDriverLink.js` + 디자인 3※ + 테스트 4) | ※`EmployerLinkCard`는 계정 `PersonalInfoPage`가, `driver-connection.css`는 차량 화면이, `linked-driver.css`는 거래처 화면이 씀 |
| `features/drivers/api` | `lib/driverInviteSms` | `DriverFormModal`만 |
| `data/sync` | `lib/clientMutations`(+ `clientMutations.fixedRoute.test`)·`clientCloudSave`·`vehicleMutations`(+ 테스트 3)·`vehicleRename`(+ 테스트)·`vehicleDeletion`·`requestDriverInviteSave`·`directMutationActions`(+ 테스트)·`driverUnlink`(+ 테스트) | 두 기능 이상 — 예: `clientMutations`는 거래처·일지·앱 설정·서류, `driverUnlink`는 앱 시작·차량·기사·불러오기, `vehicleMutations`는 차량·기사·온보딩 |
| 지움 | `components/CarManagementPage.jsx`(2줄)·`ClientManagementPage.jsx`(3줄) — 넘겨주기만 | `app/lazyPages.js` |

- 이번 조각 밖(다른 기능 소속, 59-F): `LinkStatusChip`·`InviteRedeemModal`(마이페이지), `FixedRouteBlock`·`FixedRouteClientLink`·`FixedRouteClientModal`·`RoutePresetEditor`(앱 설정).
- 재수출 껍데기 `lib/cars.js`·`lib/drivers.js`·`lib/clients.js` — 59-H.
- `EmployerLinkCard`가 앱 시작 코드(`app/boot.js:4` `buildCloudAppSession` 등)를 부르는 것 — 이번엔 경로만, 바로잡기는 59-H(`supabaseClient`→`app/routerBasename`과 함께 "아래층이 위층 부르기" 정리). 로드맵 59 계획의 "59-D에서 해소"를 59-H로 옮김.

### 목표 상태 (기대 동작)
1. 위 표대로 옮김(약 50개, 짝 테스트 포함). `components/cars/`·`components/clients/`·`components/drivers/` 폴더 없어짐.
2. `CarManagementPage`·`ClientManagementPage` 삭제, `app/lazyPages.js`가 `features/cars/ui/CarListPage.jsx`·`features/clients/ui/ClientListPage.jsx`를 바로 부름(화면 주소·나중 불러오기 방식 그대로).
3. 옛 경로 grep 0건: `components/(cars|clients|drivers)/`, `components/(DriverConnectionPage|DriverFormModal|CarManagementPage|ClientManagementPage)`, `lib/(carInviteFromDraft|driverInviteSms|clientMutations|clientCloudSave|vehicleMutations|vehicleRename|vehicleDeletion|requestDriverInviteSave|directMutationActions|driverUnlink)`.
4. `package.json` `test:app`에 `src/features/**/*.test.js` 추가. `data/sync`로 간 테스트는 이미 `test:unit` 범위(`src/data/**`).
5. 테스트 수 그대로, 전부 통과. 빌드 결과는 경로·파일 이름 말고 같음(넘겨주기 파일 2개가 없어져 묶음 파일 이름이 바뀌는 정도는 허용 — 진행 기록에).
6. "기능 사이 부르기" 목록(규칙 3)을 진행 기록에 grep 결과로.

### 건드릴 파일
- 옮김(`git mv`) 약 50개, 지움 2개, 경로만 고침(부르는 파일 + 옮긴 파일 안 상대 경로 + 테스트 `mock.module`·`import()`·경로로 읽는 테스트) — 개수는 진행 기록에.
- `package.json` 1줄, `app/lazyPages.js`(경로 2줄 — 넘겨주기 대신 진짜 화면).
- 동작 코드·문구는 안 바꿈.

### 안 건드릴 것
- 위 "이번 조각 밖" 목록, `lib/` 재수출 껍데기, `EmployerLinkCard`의 앱 시작 코드 부르기(59-H).
- 기능 사이 부르기 정리(확인할 점 1).
- 92번(같은 값 손 넘기기) — 59-I.

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 3 grep 0건, 목표 6 목록을 진행 기록에.
- `git diff -M --stat`: 옮긴 파일이 이름 바뀜으로 잡히는지, 경로 아닌 바뀐 줄 목록(있으면 전부)을 진행 기록에.
- 빌드 결과 내용 비교(이름 해시 뺌) — 차이를 진행 기록에.
- 개발 서버(비회원): 차량 관리·거래처·기사 연동 관리 화면 열기, 차량·거래처 추가 → 삭제 확인 창, 콘솔 오류 없음.

### 파일 길이
- 옮기기만 해서 줄 수 그대로. 200줄 넘는 파일은 경로 줄만 — §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(위치·경로만, 넘겨주기 파일 2개 삭제).
- 테스트가 하나라도 실패하면 멈추고 어느 경로인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 차량 관리·거래처·기사 연동 관리·연동 기사 관리 화면 열기 → 목록 그대로.
2. 기사 [수정] 창 열고 [취소], 차량 [수정] 창 열고 [취소].
3. 마이페이지 개인정보(기사 계정이면 소속 연결 카드) 열림, 콘솔 오류 없음.

### 확인할 점
1. 차량·거래처·기사는 서로 화면 부품과 디자인 파일을 섞어 씁니다(거래처 입력 창을 일지 화면도 쓰고, 기사 화면 디자인을 차량 화면도 씀 등). 목표 구조의 "기능끼리 서로 부르지 않기"를 지금 지키려면 그런 부품을 `shared/`로 내리거나 세 기능을 한 폴더로 합쳐야 합니다. **권함: 이번엔 세 폴더로 옮기기만 하고, 서로 부르는 곳은 목록으로 남겨 59 끝(의존 방향 검사)에서 한꺼번에 정하기.** 다른 안: 지금 `shared/`로 내리기(범위 늘어남).
2. `EmployerLinkCard`의 앱 시작 코드 부르기 바로잡기를 계획(59-D)에서 59-H로 옮기는 것 — 권함: 예(이번 조각은 옮기기만으로 두어 확인이 쉽게).
