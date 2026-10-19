# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 60 차량 관리 화면의 여러 단계 저장 흐름을 훅으로 `[x]`

> **진행(2026-10-19)**: 보리 "착수지시서확정 작업진행해". 코드 커밋 react-app `9c1c218`(3개 파일).
> - 교차검증(AGENTS §1): `CarListPage.jsx` 231줄, 처리 흐름 5개 줄 번호(`:89-93`·`:95-105`·`:107-142`·`:144-174`·`:176-183`) 모두 같음. 고친 것 없음.
> - ① 먼저 붙인 테스트(`CarListPage.flows.test.js`, 서버 요청 함수 4개 모듈은 가짜로 바꿔 끼워 부른 순서만 기록): **옮기기 전 코드에서 4개 모두 통과** — 연동 중 → 해제 요청 1번·차량 저장 없음·창 닫힘 / 초대 대기 → 기사 삭제 → 차량 저장 → 초대 저장 순서·창 닫힘 / 차량 삭제 → 삭제 요청 1번·목록에서 빠짐 / 일지에서 연 차량 번호 바뀜 → 일지 주소 `/app/logs/33다3333/day/2026-10-05`.
> - ② 옮김: 새 `useCarListActions.js` 131줄(주석 지시서 문구 그대로, 받는 것·돌려주는 것 지시서 목표 1과 같음), `CarListPage.jsx` 231→129(주석 지시서 문구 그대로).
> - 옮긴 본문 비교(`git show HEAD` 89~183줄 ↔ 훅 25~119줄 diff): 다른 줄은 `setModalOpen(false)` → `closeModal()` 2곳과 `DriverRecord` 타입 표기 1곳뿐.
> - ③ 옮긴 뒤 같은 4개 통과, `npm test` 1004 + 499 통과(+4), 타입 검사·빌드 통과.
> - 개발 서버(비회원): 차량 2대 추가 → 기사 차량 수정 저장 → 삭제 확인 → 목록에서 빠짐, 콘솔 오류 없음. 연동 탭은 로그인에서만 보여 "운행일지로 바꾸기"는 위 테스트로만 확인.
> - push 뒤 CI verify·deploy 초록(run 39224695383).
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서 3개 파일 ②새 파일 2개(훅·테스트, 지시서대로), 새 저장 키 0 ③타입 꼼수 0 ④`CarListPage.jsx` 129·훅 131 — 200줄 이내 ⑤테스트 — 새 4개(옮기기 전 통과 확인), 기존 테스트 안 바뀜 ⑥공용 상태값 — `pendingDelete`·`pendingDisconnect`는 이 화면 안에서만 씀 ⑦목표 1~4 대조. 옮긴 본문(옛 89~183줄 ↔ 훅 25~119줄) 직접 diff: 다른 줄 3개(`closeModal()` 2·타입 표기 1)뿐, 그리기(JSX)도 옛 코드와 같음(부르는 이름만 `actions.*`).
> - 배포본 로그인 확인(2026-10-19, 내장 브라우저): 차량 3대 그대로, 22가2222 [수정] → [운행일지] 탭 → [저장] → "이 차량의 기사 연동을 해제하시겠습니까?" 확인 창 뜸 → [취소] → 수정 창 [취소](저장 안 함). 새로고침 뒤 차량·기사 연동 그대로(연동 중 1), 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-19). 이 문서 커밋에 `AGENTS.md`(이모지 금지 줄) 같이 넣음.

보리 승인(2026-10-19, 90 `[x]`). 보리 확정 순서(2026-10-18) "… 76 → 60 → 59". 로드맵 60 `[확인: 2026-10-12 보리]` — "처리 흐름은 훅으로, 화면은 그리기만. 착수 때 같은 모양의 다른 화면도 grep으로 찾아 범위 확정."
문서 커밋 메모: 지난 90번 문서 커밋에서 빠진 `AGENTS.md`(§6 이모지 금지 줄, 보리 수정)를 이 슬라이스 `[x]` 문서 커밋에 같이 넣음(보리 지시 2026-10-19).

### 쉬운 요약
- 차량 관리 화면이 "차량 저장 → (연동 기사가 있으면) 해제 요청 또는 기사 삭제 → 초대 저장 → 번호가 바뀌었으면 일지 주소 바꾸기" 순서를 화면 안에서 직접 지휘합니다(약 90줄). 화면 파일도 231줄로 기준(200줄)을 넘습니다.
- 이 순서를 차량 관리 전용 훅 하나로 옮기고, 화면은 그리기만 하게 합니다.
- 동작은 그대로입니다. 지금은 이 순서를 지키는 화면 테스트가 없어서, **옮기기 전에 지금 동작을 그대로 적은 테스트를 먼저 붙이고**, 옮긴 뒤에도 같은 테스트가 통과하는지로 확인합니다.

### 현재 상태 (코드 확인)
- `components/cars/CarListPage.jsx`(231줄): 그리기 + 상태(`modalOpen`·`editingId`·`draft`·`pendingDelete`·`pendingDisconnect`) + 처리 흐름 `linkedDriverIdForDraft`(`:89-93`)·`save`(`:95-105`)·`confirmDisconnect`(`:107-142` — 연동 중이면 `requestDriverUnlinkAction` 해제 요청, 아니면 `requestDriverDeletion` 뒤 저장)·`commitSave`(`:144-174` — `requestVehicleSave` → `saveInviteAfterVehicle` → 알림 문구 고르기 → 번호 바뀌면 일지 주소 교체 `navigate`)·`confirmRemove`(`:176-183` — `requestVehicleDeletion`).
- 같은 모양 찾기(grep — 화면 파일 안 `await` 개수): `CarListPage` 8, `DriverConnectionPage` 4(각 단추가 요청 1번 + 알림 — 여러 단계 아님), `MaintFuelPage`·`TaxInvoiceTab` 3(저장 1번 감싸기), `EmployerLinkCard` 3(해제 → 세션 다시 만들기 → 다시 불러오기 — 여러 단계지만 화면이 앱 시작 코드를 부르는 문제라 59번 목록에 이미 있음). → **이번 범위는 `CarListPage` 하나**.
- 테스트: `CarListPage.dupKey.test.js`(목록 key 1개), `accountPermissionUi.test.js:113`(단추 보임/숨김), `App.clientsCars.test.js`(차량 추가 저장). 연동 해제 확인·기사 삭제 뒤 저장·차량 삭제·번호 바뀐 뒤 일지 주소 교체 흐름은 **화면 테스트 0개**.

### 목표 상태 (기대 동작)
1. 새 훅 `components/cars/useCarListActions.js` — 위 처리 흐름 5개와 `pendingDelete`·`pendingDisconnect` 상태를 옮김. 받는 것: `ownerKey`·`cars`·`drivers`·`cloud`·`draft`·`editingId`·`showToast`·`closeModal`. 돌려주는 것: `save`·`confirmDisconnect`·`cancelDisconnect`·`pendingDisconnect`·`askDelete`·`cancelDelete`·`confirmRemove`·`pendingDelete`. 일지 주소 교체는 훅 안에서 `useNavigate`·`useLocation`.
2. `CarListPage.jsx`는 그리기 + 창 열기 상태(`modalOpen`·`editingId`·`draft`, `openAdd`·`openEdit`)만. 200줄 이하.
3. 부르는 서버 요청·순서·알림 문구·창 닫히는 시점이 지금과 글자까지 같음(코드는 옮기기만, 고치지 않음).
4. 먼저 붙이는 테스트(새 `components/cars/CarListPage.flows.test.js`) 4개가 **옮기기 전 코드에서 통과하고, 옮긴 뒤에도 통과**:
   ① 연동 중(`linked`) 기사 차량을 "일지만"으로 바꿔 저장 → 확인 창 → [확인] → 해제 요청 1번, 차량은 저장 안 됨, 창 닫힘.
   ② 초대 대기(`pending`) 기사 차량을 "일지만"으로 → [확인] → 기사 삭제 뒤 차량 저장, 창 닫힘.
   ③ 차량 삭제 → 확인 창 → [확인] → 삭제 요청 1번, 목록에서 빠짐.
   ④ 일지에서 열린 차량 번호를 바꿔 저장 → 그 일지 주소가 새 번호로 바뀜.
   (테스트 방식은 `accountPermissionUi.test.js`·`DriverConnectionPage.unlink.test.js`의 렌더·가짜 서버를 따름.)

### 건드릴 파일
- 새로: `components/cars/useCarListActions.js`(약 105줄), `components/cars/CarListPage.flows.test.js`.
- 고침: `components/cars/CarListPage.jsx`(처리 흐름·관련 가져오기를 훅으로 옮기고 부르기만).
- 분리설계(§6 — 200줄 넘는 파일 수정): 책임 경계 = 화면(그리기·창 열기) / 훅(저장·해제·삭제 흐름). 의존 방향 = 화면 → 훅 → `lib/*` 요청 함수(지금 화면이 부르던 그대로). 같은 폴더(`components/cars/`)에 둠.
- 코드 주석 최종 문구: `CarListPage.jsx:2` → `// 차량 목록·등록/수정 창 — 그리기와 창 열기만, 저장·연동 해제·삭제 흐름은 useCarListActions.`, `useCarListActions.js:2` → `// 차량 저장·연동 해제·삭제 흐름 — 서버 요청 순서와 알림 문구는 화면에서 옮긴 그대로.`

### 안 건드릴 것
- `lib/vehicleMutations.js`·`directMutationActions.js`·`driverUnlink.js`·`carInviteFromDraft.js` 등 요청 함수 — 그대로 부르기만.
- `CarFormModal.jsx`·`CarListItem.jsx`·`ConfirmModal` 그리기 — 그대로.
- `DriverConnectionPage`·`MaintFuelPage`·`TaxInvoiceTab`(여러 단계 아님), `EmployerLinkCard`(59번).
- 92번(같은 값 손으로 넘기기 → Context) — 59번 때.

### 검증
- 순서: ① 새 테스트 4개를 **지금 코드에 먼저** 넣어 통과 확인 → ② 옮김 → ③ 같은 4개 + 기존 테스트 통과. ①의 결과를 진행 기록에(어느 테스트가 지금 코드에서 통과했는지).
- `npm test`·타입 검사·빌드 통과.
- `git diff`로 옮긴 함수 본문이 글자까지 같은지(들여쓰기·받는 값 이름 바뀐 것 말고) 진행 기록에 한 줄.
- 개발 서버(비회원): 차량 추가·수정·삭제, 기사 차량 "일지만" 저장 → 콘솔 오류 없음.

### 파일 길이
- `CarListPage.jsx` 231 → 약 140, 새 `useCarListActions.js` 약 105, 새 테스트 파일(예외). 모두 200줄 이내.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(옮기기만).
- ①에서 새 테스트가 지금 코드에서 실패하면, 테스트가 틀렸거나 지금 코드에 버그가 있다는 뜻 — 코드를 고치지 말고 멈추고 어느 쪽인지 이 파일에 적어 보고(버그면 따로 등재).
- 옮긴 뒤 하나라도 실패하면 옮기다 동작이 바뀐 것 — 수정 계획을 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 차량 관리 → 차량 3대 그대로 → 기사 차량 [수정] 창 열고 [취소].
2. 기사 연동 차량을 "일지만"으로 바꾸고 저장 → 해제 요청 확인 창이 뜨는지만 보고 [취소](실제 해제 안 함).
