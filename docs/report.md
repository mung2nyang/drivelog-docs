# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 58-A 거의 같은 거래처 화면 두 벌을 하나로 `[x]`

> **진행(2026-10-17)**: 보리 "착수지시서 확정, 작업 진행해"(확인할 점 1·2 권장대로 — 공용 카드로 맞춤, 두 화면만). 코드 커밋 react-app `b3de723` — push 뒤 CI verify·deploy 초록(run 39027402742).
> - 바뀐 파일(지시서 목록과 같음): 새 `clients/useScopedClients.js` 83줄, 새 `clients/ScopedClientsList.jsx` 59줄, `OwnerScopedClientsView.jsx` 157→35, `drivers/LinkedDriverClientsPage.jsx` 177→63(§6 예외 머리 주석 지움), 새 `clients/ScopedClientsPages.test.js` 4개.
> - 순서: 동작 기록 테스트 4개를 먼저 써서 **고치기 전 코드에서 통과** 확인 → 합친 뒤 그대로 통과. 공용 카드(결제주기 줄) 확인 한 줄만 더함 — 고치기 전으로 돌리면 이 테스트만 실패, 나머지 3개는 안전 확인(revert-check).
> - `npm test` 951 + 478 통과, 타입 검사 통과.
> - 개발 서버 비회원 360px 밝은·어두운 화면: 기사 차량 하나 넣고 "박기사 거래처" 화면 → 추가(그 차량 번호로 저장, "거래처를 등록했습니다."), 수정 창에 값 채움, 수정, 삭제 확인 → 삭제. 콘솔 오류 없음. 사진 `scratchpad\v58a\`. 연동 기사 앱 화면은 로그인 필요라 테스트로.
> - **지시서와 다른 점**: 공용 카드에는 **담당자 이름 줄이 없어서**, 연동 기사 앱 거래처 카드에서 "김담당 담당" 표시가 사라짐(지시서엔 "새로 보이는 것"만 적고 빠지는 것을 못 적음). 수정 창 안에는 담당자 칸이 그대로 있음. 보리 "승인"(2026-10-17)으로 그대로 둠.

보리 "진행해"(2026-10-17, 85 `[x]`로 ⑤ 순서 끝난 뒤). 근거: `docs/roadmap.md` 58번(⑥), `docs/code-cleanup-review.md` D(겹침 가장 큼, 먼저).
58번은 세 쌍이라 한 쌍씩 나눔: **58-A 거래처(이번)** → 58-B 매출(`revenue/DriverRevenueView.jsx` ↔ `OwnerRevenueView.jsx`) → 58-C 공유 창(`ReportShareModal.jsx` ↔ `documents/DailyInspectionShareModal.jsx`). 이번엔 A만.

### 쉬운 요약
- 연동 기사 앱의 "거래처" 화면과, 차주가 보는 "기사 거래처" 화면은 코드가 거의 똑같이 두 벌 복사돼 있습니다. 같은 부분을 한 곳으로 모아 한쪽만 고쳐지는 일을 막습니다.
- 누르는 동작(추가·수정·삭제·저장)과 저장되는 값은 그대로입니다.

### 현재 상태 (코드 확인)
- `components/clients/OwnerScopedClientsView.jsx`(157줄) — 연동 기사 계정의 거래처 화면(`app/AppShellRoutes.jsx:81`, `session.linkedOwnerId`일 때).
- `components/drivers/LinkedDriverClientsPage.jsx`(177줄, 머리 주석에 "§6 예외(≤250)") — 차주가 보는 연동·미연동 기사 거래처 화면(`AppShellRoutes.jsx:70,94`).
- 두 파일에서 똑같은 것: 빈 입력값(`emptyDraft`), 수정 열 때 값 채우기(`openEdit` 15줄), `openAdd`, 저장(`requestClientSave`), 삭제(`requestClientDeletion`), 상태 4개, 목록 거르기(`scopedToVehicleNumber === scopeKey`), 추가 단추, 입력 창, 삭제 확인 창.
- 다른 것:
  1. "어느 차량 거래처인가" 찾기 — 기사 앱은 내 첫 차량 번호(`cars[0].number`), 차주 화면은 주소의 기사·차량에서 찾음(`resolveDriverManagementContext`).
  2. 제목·뒤로 가기·"찾을 수 없음" 안내 — 차주 화면만.
  3. 위 안내 문구 "차주와 공유하는 거래처 목록입니다…" — 기사 앱만.
  4. **거래처 카드 모양** — 기사 앱은 카드를 직접 그림(상호·담당자·사업자·연락처), 차주 화면은 공용 `clients/ClientListItem.jsx`(상호·즐겨찾기 별·고정 노선/수수료/파렛트 표·사업자·연락처·결제주기 — 차주 본인 거래처 화면 `ClientListPage.jsx:96`도 이것을 씀).
- 두 화면 테스트 없음(`grep -rln "OwnerScopedClientsView\|LinkedDriverClientsPage" src --include=*.test.js` 결과 없음).

### 목표 상태 (기대 동작)
1. 두 화면의 같은 부분(빈 입력값, 수정 값 채우기, 추가·수정·삭제·저장, 목록 거르기, 추가 단추, 입력 창, 삭제 확인 창)이 한 곳에만 있다.
2. 기사 앱 거래처 화면: 위 안내 문구, 제목 "거래처", 내 차량 거래처만 보이고, 추가하면 내 차량 거래처로 저장 — 지금과 같다.
3. 차주의 기사 거래처 화면: 제목("홍길동 기사 거래처" 등), "찾을 수 없음" 안내, 그 기사 차량 거래처만 보이고 추가하면 그 차량 거래처로 저장 — 지금과 같다.
4. 추가·수정·삭제의 저장 결과와 실패 안내는 지금과 같다(같은 `requestClientSave`·`requestClientDeletion`).
5. 거래처 카드는 두 화면 모두 공용 `ClientListItem` 모양(확인할 점 1 — 기사 앱 화면에 즐겨찾기 별·고정 노선/수수료/파렛트 표·결제주기 줄이 새로 보임).

### 건드릴 파일
- 새 `react-app/src/components/clients/useScopedClients.js` — 한 차량 거래처의 상태·추가·수정·삭제·저장(빈 입력값·수정 값 채우기 포함).
- 새 `react-app/src/components/clients/ScopedClientsList.jsx` — 목록·추가 단추·입력 창·삭제 확인 창(위 안내 문구는 받아서 보여 줌).
- `react-app/src/components/clients/OwnerScopedClientsView.jsx` — 위 둘을 써서 제목·안내만 남김.
- `react-app/src/components/drivers/LinkedDriverClientsPage.jsx` — 위 둘을 써서 기사·차량 찾기·제목·"찾을 수 없음"만 남김. "§6 예외" 머리 주석은 200줄 아래로 내려가 지움.
- 새 테스트 `react-app/src/components/clients/ScopedClientsPages.test.js`.
- 공용 영향: `ClientFormModal`·`ConfirmModal`·`ClientListItem`·`requestClientSave`·`requestClientDeletion`은 안 바꾸고 부르기만 함. CSS 안 바꿈(목록 `id`도 지금 이름 그대로 넘김 — CSS·테스트에서 안 쓰지만 그대로 둠).

### 안 건드릴 것
- 차주 본인 거래처 화면 `ClientListPage.jsx`, 콜 상세 빠른 거래처 추가 `day-log/CallClientQuickAdd.jsx` — 여기에도 같은 "수정 값 채우기"가 있지만 끌어서 순서 바꾸기·즐겨찾기 등 동작이 더 있어 이번 범위 밖(확인할 점 2).
- 기사 앱 안내 문구 내용 — 그대로(`docs/sot.md` 295줄 "연동 기사 거래처는 기사 독립 데이터"와 문구가 맞는지는 이번 범위 밖).
- 58-B·58-C.

### 검증
- **먼저** 새 테스트를 지금 코드에 돌려 통과시킨다(동작 기록): 두 화면 각각 — 제목·안내, 그 차량 거래처만 보임, 추가 → 그 차량으로 저장, 수정 창에 기존 값 채워짐, 삭제 확인 → 삭제, 차주 화면 "찾을 수 없음".
- 합친 뒤 같은 테스트가 그대로 통과(목표 1~4). 카드 모양만 바뀌는 곳(목표 5)은 테스트 기대를 그 한 줄만 바꿈 — 진행 기록에 적음.
- `npm test`·`npm run typecheck` 통과.
- 개발 서버: 미연동 기사 차량(비회원으로 만들 수 있음)의 기사 거래처 화면에서 추가·수정·삭제·사진. 연동 기사 앱 화면은 로그인 필요라 테스트로.

### 파일 길이
- `OwnerScopedClientsView.jsx` 157 → 약 30
- `LinkedDriverClientsPage.jsx` 177 → 약 70
- 새 `useScopedClients.js` 약 85, 새 `ScopedClientsList.jsx` 약 60
- 새 테스트 약 150(테스트 파일 예외)

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(같은 저장 함수를 한 곳에서 부르기만).
- 합친 뒤 동작 기록 테스트가 깨지면 고치지 않고 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤)
1. 차주 계정: 기사 관리 → 기사 → 거래처 → 거래처 추가 → 목록에 보이고, 수정·삭제가 지금처럼 된다.
2. 연동 기사 계정: 마이페이지 → 거래처 → 위 안내 문구가 있고, 추가·수정·삭제가 지금처럼 된다. 카드에 결제주기 줄이 보인다(확인할 점 1).

### 확인할 점
1. **거래처 카드 모양**: 두 화면을 하나로 합치면 카드도 하나여야 합니다. 차주 화면·차주 본인 거래처 화면이 쓰는 공용 카드(즐겨찾기 별, 고정 노선/수수료/파렛트 표, 결제주기 줄)로 맞추는 것을 권합니다. 그러면 **연동 기사 앱 거래처 카드에 이 정보가 새로 보입니다.** 지금 기사 앱 카드(상호·담당자·사업자·연락처만)를 그대로 두길 원하시면 카드는 두 벌로 남기고 나머지만 합칩니다.
2. 차주 본인 거래처 화면과 콜 상세 빠른 추가에도 "수정 값 채우기" 같은 코드가 있습니다. 이번엔 두 화면만 하고, 필요하면 나중에 따로 하겠습니다.
