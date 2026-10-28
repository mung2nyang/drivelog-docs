# docs/report.md — 현재 슬라이스 착수지시서

130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 131-A 차량 창 [저장]·연동 해제·삭제를 두 번 눌러도 한 번만 `[x]`

폴더별 전수점검(2026-10-26) M3, 로드맵 131 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 130 다음 차례(① 데이터 저장·유실). DB 변경 없음.
131은 고칠 파일이 6개 넘게 흩어져 있어 둘로 나눔: **131-A 차량 창(이번)**, 131-B 첫 설정·거래처·기사 초대 창 [저장](다음 — `OnboardingPage.jsx`·`ClientFormModal.jsx`·`DriverFormModal.jsx`). 이번엔 A만.

### 쉬운 요약
- 차량 창의 [저장], 연동 해제 확인 창의 [연동 해제], 삭제 확인 창의 [삭제]는 서버 저장이 끝날 때까지 버튼이 막혀 있지 않습니다. 빨리 두 번 누르면 같은 차량이 두 대 등록되거나, 기사 초대·해제 요청이 두 번 갑니다. 서버에도 같은 번호 차량을 막는 장치가 없어 그대로 두 대가 됩니다(아래 서버 확인).
- 지출 창(로드맵 111)과 같은 방식으로, 처리 중에는 다시 눌러도 무시하고 [저장] 버튼은 "저장 중…"으로 잠급니다.

### 현재 상태 (코드·서버 확인, 2026-10-28)
- `features/cars/hooks/useCarListActions.js:33-43` `save` → `commitSave`(`:82-112`) → `requestVehicleSave` → 초대 저장(`saveInviteAfterVehicle`) → 창 닫기. 처리 중 막기 없음(grep: 이 파일에 `busy`·`saving` 없음).
- `:45-79` `confirmDisconnect`(해제 요청 또는 기사 삭제 뒤 차량 저장), `:114-121` `confirmRemove`(차량 삭제)도 막기 없음.
- `features/cars/ui/CarFormModal.jsx:188` [저장] 버튼은 늘 눌림. `features/cars/ui/CarListPage.jsx:102-125`가 차량 창과 확인 창 두 개(`shared/ui/ConfirmModal.jsx` — 여러 화면 공용, 잠금 기능 없음)를 그림.
- 서버(테스트 프로젝트, 읽기 조회 2026-10-28): `vehicles`·`clients` 표는 고유 번호(`id`) 말고 겹침을 막는 장치가 없음(같은 사용자·같은 번호 막기 없음, `vehicles` 트리거는 연동 중 삭제 막기 하나뿐). 번호 바꾸기만 같은 번호를 막음(0022). → 두 번 저장되면 같은 번호 차량 두 대.
- 같은 방식의 앞선 수정: 로드맵 111 react-app `9aa37d8` `useExpenseForm.js`의 `once`(진행 표시 `useRef` + `saving` 상태, `try … finally`로 풂), sot 쓰기 규칙 2 "지출 창 [저장]·지우기는 저장 중 다시 눌러도 무시".

### 목표 상태 (기대 동작)
1. 로그인 계정에서 새 차량 창 [저장]을 서버 저장이 끝나기 전에 두 번 눌러도 서버에 차량이 한 대만 생긴다.
2. 저장 중에는 [저장] 버튼이 잠기고 "저장 중…"으로 보인다. 끝나면 지금처럼 창이 닫힌다.
3. 저장이 실패하면 지금처럼 안내가 뜨고 창이 그대로 있으며, 잠금이 풀려 다시 누를 수 있다.
4. 연동 해제 확인 창 [연동 해제], 삭제 확인 창 [삭제]도 처리 중 다시 누르면 무시한다(서버 요청 한 번). 확인 창 버튼 모양은 그대로(공용 창이라 안 바꿈).
5. 비회원 저장·검사 오류 표시·연동 해제 확인 창이 뜨는 조건은 지금과 같다.

### 건드릴 파일
- `react-app/src/features/cars/hooks/useCarListActions.js` — 111과 같은 `once`를 넣고, 돌려주는 `save`·`confirmDisconnect`·`confirmRemove`를 `once`로 감쌈. `saving`도 돌려줌. 주석 최종 문구: `// 저장·연동 해제·삭제 중엔 다시 눌러도 무시 — 같은 차량이 두 번 등록되지 않게.`
- `react-app/src/features/cars/ui/CarFormModal.jsx` — `saving` 받기(기본 `false`), [저장] `disabled={saving}`·글자 `저장 중…`. 형 설명 한 줄: `@param {boolean} [props.saving] 저장 중이면 [저장] 잠금`. `onSave` 형 설명을 `() => void|Promise<void>`로.
- `react-app/src/features/cars/ui/CarListPage.jsx` — 차량 창에 `saving={actions.saving}` 넘김.
- 새 `react-app/src/features/cars/ui/CarListPage.saveOnce.test.js` — 111 테스트처럼 가짜 서버가 차량 넣기를 붙잡은 채: [저장] 두 번 → 넣기 1번·버튼 "저장 중…" 잠김(기대 1·2), 첫 넣기 실패 → 창 그대로·잠금 풀림·다시 저장 1번 더(기대 3), 삭제 [삭제] 두 번 → 삭제 요청 1번(기대 4), 연동 해제 [연동 해제] 두 번 → 해제 요청 1번(기대 4).
- 공용 값 영향: `saving`은 새 값이라 쓰는 곳이 `CarListPage`→`CarFormModal` 하나. `CarFormModal`은 `CarListPage.jsx`에서만 씀(grep). 공용 `ConfirmModal`·스타일은 안 바꿈.

### 안 건드릴 것
- `shared/ui/ConfirmModal.jsx` — 여러 화면이 같이 써서 잠금 모양을 넣지 않음. 두 번 눌림은 `useCarListActions`의 `once`가 막음.
- 서버에 같은 번호 차량 막기(고유 조건 추가) — 기존 데이터 확인과 DB 변경이 필요한 다른 작업. 이번엔 앱 잠금만.
- 131-B 첫 설정·거래처·기사 초대 창 — 다음 조각.
- `data/sync/vehicleMutations.ts`·`carInviteFromDraft.js` 저장 순서 — 그대로.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(§2 호출 경로·§6 테스트 진실성).
- 새 테스트가 고치기 전 코드에서 실패하는지 먼저 확인(넣기 2번), 고친 뒤 통과. 기존 `CarListPage.flows.test.js`·`CarListPage.dupKey.test.js`·`CarFormModal.test.js`·`carInvite*.test.js` 그대로 통과. 로컬 `npm test` 전체 통과. 타입·빌드는 CI.

### 파일 길이
- `features/cars/hooks/useCarListActions.js` 133 → 약 145
- `features/cars/ui/CarFormModal.jsx` 192 → 약 194 (감시 목록 파일 — 200줄을 넘게 되면 그때 §6 분리설계를 먼저 보고)
- `features/cars/ui/CarListPage.jsx` 128 → 약 129
- 테스트 파일은 예외.

### 실패 시 처리
- 새 저장 장치·재시도·임시 보관 없음. 실패 안내는 지금 문구 그대로, 잠금만 풂.
- 테스트나 배포본 확인이 기대와 다르면 코드를 더 고치지 않고 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법 (배포본, 로그인 계정)
1. 내 차량 관리 → [+ 추가] → 기사 차량(운행일지)으로 시험 번호 입력 → [저장]을 빠르게 두 번 → 목록에 그 차량이 한 대만 있고, 새로고침해도 한 대면 성공. 누르는 순간 버튼이 "저장 중…"으로 바뀌는지도 봄.
2. 그 시험 차량 삭제 → 확인 창 [삭제]를 빠르게 두 번 → 목록에서 빠지고 오류 안내가 없으면 성공.
3. 확인용 차량이 남아 있으면 지움.

### 진행 기록
- 2026-10-28 착수 승인(보리 "확정, 진행해"). 플레이북 §2·§6 열람.
- 새 테스트 `CarListPage.saveOnce.test.js` 4개를 고치기 전 코드에서 실행(종료 코드 1). 두 번 눌림 3개 실패, 실패 뒤 다시 저장 1개는 원래도 통과(잠금이 풀리는지 지키는 확인):
  ```
  ✖ 새 차량 [저장]을 저장 중에 또 눌러도 저장 요청은 한 번, 버튼은 "저장 중…"으로 잠김 (87.484ms)   actual: [ 'vehicleSave', 'vehicleSave' ]
  ✔ 저장이 실패하면 창 그대로, 잠금이 풀려 다시 저장하면 요청이 한 번 더 (74.5184ms)
  ✖ 삭제 확인 창 [삭제]를 처리 중에 또 눌러도 삭제 요청은 한 번 (61.267ms)                       actual: [ 'vehicleDeletion', 'vehicleDeletion' ]
  ✖ 연동 해제 확인 창 [연동 해제]를 처리 중에 또 눌러도 해제 요청은 한 번 (90.4619ms)           actual: [ 'unlinkRequest', 'unlinkRequest' ]
  ℹ tests 4 / pass 1 / fail 3 / duration_ms 1712.0522
  ```
- 고친 뒤: 새 테스트 4개 통과, 차량 폴더 테스트 30개 통과. 로컬 `npm test` 전체 통과(927 + 913), 타입 검사 통과.
- 코드 커밋 react-app `b87b810`(push 전). 파일 길이: `useCarListActions.js` 144, `CarFormModal.jsx` 193, `CarListPage.jsx` 129.
- 리뷰 점검: 바뀐 파일이 지시서 "건드릴 파일"과 같음, 공용 `ConfirmModal` 안 바뀜, 새 저장 장치 없음, 타입 꼼수 없음, 주석은 지시서 최종 문구 그대로.
- 원래 있던 것: `CarDriverConnectPanel.test.js`에서 React act 경고 2건(이번에 안 바꾼 파일·테스트).
- push(보리) 뒤 CI verify·deploy 초록(run 40642665323).
- 배포본 확인(데스크톱 AI, 로그인 계정, 보리 지시): 배포 파일에 "저장 중…" 들어간 것 확인. 원래 차량 3대(11가1111·22가2222·99가9998).
  1. [+ 추가] → 99가9131, 운행일지 방식 → [저장] 곧바로 두 번 → 첫 누름 직후 버튼 "저장 중…"·잠김, 창 닫히고 목록에 한 대, 새로고침 뒤 한 대, 서버 조회 `vehicles`에 그 번호 1줄. 통과.
  2. 99가9131 삭제 → 확인 창 [삭제] 곧바로 두 번 → "차량을 삭제했습니다." 한 번, 오류 안내 없음, 목록에서 빠짐, 서버 조회 0줄. 통과.
  3. 새로고침 뒤 차량 3대, 원래 상태와 같음.
- 2026-10-28 보리 최종 승인.

---

## 로드맵 94 `.ts` 전환 — 남은 조각 (94-A·B·C `[x]`, D~G는 출시 전 순서 뒤)

지난 지시서·진행 기록: 94-A drivelog-docs `3ee010c`, 94-B `90ef647`, 94-C `0c0eafe`(`git show <해시> -- docs/report.md`).

| 조각 | 범위 | 운영 파일 | 비고 |
|---|---|---|---|
| ~~94-A~~ `[x]` | 설정 + 시범 2개 | 2 | react-app `a009f2b` |
| ~~94-B~~ `[x]` | `domain/` | 58 | react-app `855fb6a` |
| ~~94-C~~ `[x]` | `data/` | 81 | react-app `079aee8` |
| 94-D | `shared/` + `app/` + `main.jsx` + `testSupport/` | 69 | |
| 94-E | `features/` 일지·달력·지출 | 39 | |
| 94-F | `features/` 서류·미수금·매출 | 38 | |
| 94-G | `features/` 나머지 8개 | 64 | 마무리: `allowJs` 끄기, `// @ts-check` 규칙 정리 |

- 순서 `[확인: 2026-10-22 보리 — "그럼 반만, 응"]`: D~G는 출시 전 순서(23·24 → 34 → 79) 뒤.
- 방식(B·C와 같음): AI가 손으로, 밖 파일은 경로 끝만(기계 비교), `mock.module` 지정 포함. 증명: 타입 설명 파일(`.d.ts`, 저장소 밖에서 뽑음) 비교 + 빌드 결과. 옛 JSDoc 형 변환 주석이 빠지면 압축 변수 이름이 바뀌어 해시가 달라질 수 있음(94-C) — 그때는 저장소 밖에서 옛·새를 압축 없이 빌드해 같은지로 증명(보리 승인 방식).
- 의존 검사 연결 수는 JSDoc `import('…')`이 `import type`이 되며 타입 전용 연결만큼 늘어남 — 늘어난 것이 그것뿐인지 확인.
- 94-D: `// @ts-check` 없는 4개(`app/guestSessionPersist.js`·`app/routes/ComingSoonRoute.jsx`·`app/routes/lazyPages.js`·`app/routes/sessionGate.js`)는 처음 타입 검사를 받음. `.jsx`는 `.tsx`로 — 화면 테스트 로더가 이미 읽음(94-A).
