# docs/report.md — 현재 슬라이스 착수지시서

139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 140 같은 이름 거래처 등록 막기 `[x]`

전수점검(2026-10-26) R1, 로드맵 ②. 순서 137 → 139 → **140** → 138(보리 2026-10-30). 칸 표시 방식은 로드맵 82 보리 결정("빠지거나 틀린 칸은 빨간 테두리·칸 아래 문구·커서, 아래 알림 안 띄움")을 따름. DB 변경 없음. 거래처를 이름으로 찾는 곳(A7·B8·F4·G9·H7)은 이번 범위 밖.

### 쉬운 요약
- 지금은 같은 차량 범위(차주 일반 거래처끼리, 또는 같은 기사 차량 전용끼리)에 이름이 같은 거래처를 몇 번이고 등록할 수 있습니다. 앱이 거래처를 이름으로 찾아서 수수료·결제 조건이 어느 쪽 것으로 잡힐지 목록 순서에 달립니다.
- 새로 등록하거나 이름을 바꿀 때 같은 범위에 같은 이름이 있으면 거래처명 칸에 빨간 테두리와 "같은 이름의 거래처가 이미 있습니다."를 보이고 저장하지 않습니다.
- 이미 겹쳐 있는 거래처는 지우지 않고, 이름을 그대로 두면 지금처럼 고칠 수 있습니다.

### 현재 상태 (코드 확인)
- 검사 `domain/clients.ts:47-61` `checkClientDraft` — 이름이 비었는지만 봄. 저장 `:64-84` `upsertClient`도 이 검사만 거침.
- 저장 경로 4곳 모두 `requestClientSave`(`data/sync/clientMutations.ts:43-46` → `upsertClient`): 거래처 화면 `ClientListPage.jsx:80`, 기사 차량 거래처 `useScopedClients.js:69`, 일지 빠른 추가 `CallClientQuickAdd.jsx:45`, 고정 노선 거래처 창 `FixedRouteClientModal.jsx:55`(이미 있는 거래처 고치기만).
- 거래처 창 `ClientFormModal.jsx:47` — 저장 전 `checkClientDraft(draft)`로 칸 표시, 거래처 목록은 안 받음. 창을 쓰는 곳 3곳: `ClientListPage.jsx:116`, `ScopedClientsList.jsx:40`, `CallClientQuickAdd.jsx:63`.
- 범위: 차주 일반 거래처는 `scopedToVehicleNumber` 없음, 기사 차량 전용은 그 차량 번호(`getClientsForLog`, `clients.ts:157-164`, sot 296줄 "그 운행일지 안에서").
- 운비서 거래처 등록은 이미 같은 이름을 막음(`useUnbiseoAssistant.ts:119`). [저장] 두 번 누름(R2)은 131-B에서 막음(`ClientFormModal.jsx:42-56` `busyRef`).
- 서버 읽기 조회(2026-10-30): 한 계정의 기사 차량 범위 하나에 같은 이름 거래처 2종이 3개씩(6줄) 이미 있음. 다른 계정은 없음.

### 목표 상태 (기대 동작)
1. 새 거래처: 같은 범위에 앞뒤 빈칸을 뺀 이름이 글자 그대로 같은 거래처가 있으면 막음 — 거래처명 칸 빨간 테두리·칸 아래 "같은 이름의 거래처가 이미 있습니다."·커서, 아래 알림 없음, 창 그대로.
2. 고치기: 이름을 다른 거래처와 같게 바꾸면 1과 같이 막음. 자기 이름 그대로면 통과.
3. 이미 겹쳐 있는 거래처: 이름을 안 바꾸면 고칠 수 있음(저장된 자기 이름과 같으면 검사 안 함). 지우지 않음.
4. 다른 범위의 같은 이름(차주 일반 "한진"과 기사 차량 전용 "한진", 기사 차량 둘)은 지금처럼 허용.
5. 창을 거치지 않는 저장(`requestClientSave` 직접)도 `upsertClient`가 같은 검사로 막고 알림 문구로 알림(지금 다른 검사와 같은 길).
6. 다른 입력 검사(결제 주기·수수료·단가)와 순서: 이름 칸 검사가 맨 앞이므로 이름 문제부터 보임.

### 건드릴 파일
- `src/domain/clients.ts` — `checkClientDraft(draft, clients = [], editingId = null)`: 이름이 비지 않았으면 같은 범위·같은 이름(앞뒤 빈칸 뺀 글자 그대로)·다른 id 거래처가 있는지 봄, 단 `editingId` 거래처의 저장된 이름과 같으면 건너뜀. `upsertClient`가 `checkClientDraft(draft, clients, editingId)`로 부름. 주석 최종 문구: `// 같은 범위(차주 일반 또는 같은 기사 차량)에 같은 이름이 있으면 막는다 — 이미 겹친 거래처는 이름을 안 바꾸면 고칠 수 있다.`
- `src/features/clients/ui/ClientFormModal.jsx` — 새 `clients` 속성(기본 빈 목록)을 받아 `checkClientDraft(draft, clients, editingId)`.
- 창을 쓰는 3곳에 목록 넘기기(한 줄씩): `ClientListPage.jsx:116`(`clients`), `ScopedClientsList.jsx:40`(`scoped.scopedClients` — 같은 범위만으로 충분), `CallClientQuickAdd.jsx:63`(`clients`).
- 테스트: `domain/clients.test.js`(목표 1~5 — 새·고치기·이미 겹친 것·다른 범위·`upsertClient`), `features/clients/ui/ClientFormModal.test.js`(같은 이름이면 거래처명 칸 표시·`onSave` 안 부름).

### 안 건드릴 것
- 이미 겹쳐 있는 서버 거래처 6줄 — 그 계정의 자료라 지우거나 합치지 않음(목표 3).
- 거래처를 이름으로 찾는 곳(A7·B8·F4·G9·H7), 띄어쓰기만 다른 이름("한진 택배"/"한진택배")을 같은 것으로 볼지 — 이번엔 글자 그대로만 비교.
- `useScopedClients.js`·`clientMutations.ts`·`FixedRouteClientModal.jsx` — 저장 길은 그대로, `upsertClient` 안에서 막힘(목표 5).
- DB(서버에 같은 이름 막는 규칙을 두지 않음 — 이미 겹친 자료가 있어 걸림), 운비서 거래처 등록.

### 검증
- `npm test`, 타입 검사, 코드 검사, 의존 검사, 빌드(CI).
- 새 테스트가 고치기 전 코드에서 실패하는지 확인(revert-check). 다른 범위 허용·이미 겹친 것 고치기는 안전 확인(고치기 전에도 통과).
- 로컬 비회원 화면: 거래처 "시험"을 등록 → 같은 이름으로 한 번 더 → 칸 표시·저장 안 됨 → 지움.
- 배포본: 아래 확인 방법.
- [x] 때 sot에 규칙 기록(같은 범위 같은 이름 금지, 이미 겹친 것은 이름 그대로면 수정 가능).

### 진행 기록
- 2026-10-30 보리 "착수지시서 확정, 작업 진행해" → 데스크톱 AI가 코드. react-app `d2f4b28`(push 안 함).
- 로컬: 테스트 949·923 모두 통과, 타입 검사·코드 검사·의존 검사(위반 0)·빌드 통과. 파일 길이 192·135·123·60·74.
- 고치기 전 실패 확인 — 새 테스트 5개 중 3개(새 거래처 막기·고치기 막기·거래처 창 칸 표시)는 고치기 전 코드에서 실패, 2개(다른 범위 허용·이미 겹친 것 고치기)는 안전 확인. 되돌린 파일 2개 원래대로 복구 확인.
- 브라우저(로컬 5175, 비회원): 거래처 "시험" 등록 → 같은 이름으로 다시 [저장] → 거래처명 칸 빨간 테두리·"같은 이름의 거래처가 이미 있습니다."·창 그대로 → [취소] → "시험" 지움, 콘솔 오류 없음.
- 겹친 거래처 정리(보리 "겹쳐잇는건 정리해", 2026-10-30) — 읽기 조회 결과: 기사 차량 부산92아5365 범위의 "주식회사 제일특수"·"(주)중부금오물류" 각 3줄 중 1줄씩(legacy `client_1787486548269_…`)만 담당자·사업자번호·결제 조건이 있고, 나머지 4줄은 이름·차량 번호만 있음(같은 시각 두 개씩 만들어진 두 묶음). 6줄 모두 운행 기록·세금계산서 연결 0(`transport_details`·`tax_invoices.client_id`, 지우면 연결은 비워지는 규칙 SET NULL). 해당 계정(`01051441805@runlog-user.com`, 가입 2026-08-23)은 실사용 계정이라 보리 결정으로 지우지 않고 그대로 둠(2026-10-30). 서버 자료 변경 없음.
- push 뒤 CI verify·deploy 초록(run 41008261605).
- 배포본(getdrivelog.com, 로그인 탭): 거래처 화면 [+] → 거래처명에 앞뒤 빈칸 붙인 " 테스터거래처 "(이미 있는 이름) → [저장] → 거래처명 칸 빨간 테두리·"같은 이름의 거래처가 이미 있습니다."·창 그대로 → [취소] → 목록 1개 그대로(서버에 저장 안 됨).
- 2026-10-30 보리 "확정" → `[x]`, sot §4-22에 규칙 기록.
- §5 리뷰: 바뀐 파일 7개가 "건드릴 파일"과 같음, 새 파일·저장 키·대비 장치 없음, 타입 꼼수·로드맵 번호 주석 없음, 지운 테스트 없음, 파일 길이 192·135·123·60·74(200 이하), 목표 1~6 충족(1·2·3·4·5는 테스트, 1은 로컬·배포본 화면, 6은 검사 순서상 이름이 맨 앞).

### 파일 길이
- `clients.ts` 184 → 약 192(200줄 감시 목록 — 넘으면 §6 분리설계 먼저 보고), `ClientFormModal.jsx` 134 → 약 136, `ClientListPage.jsx` 123 → 123, `ScopedClientsList.jsx` 59 → 약 60, `CallClientQuickAdd.jsx` 73 → 약 74.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 입력 검사만 더함. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법 (배포본, 로그인 계정)
1. 옆 메뉴 → 거래처 → [+ 추가] → 이미 있는 거래처 이름(예: "테스터거래처")을 넣고 [저장] → 거래처명 칸 빨간 테두리·"같은 이름의 거래처가 이미 있습니다.", 창이 닫히지 않음 → [취소].
2. 기사 차량 거래처(기사 관리 → 거래처)에서 차주 거래처와 같은 이름은 등록됨 — 확인 뒤 지움.
3. 이미 있는 거래처를 열어 담당자만 바꿔 [저장] → 저장됨 → 되돌림.

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
