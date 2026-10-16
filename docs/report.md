# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 48 카드 안 수정·삭제 버튼 크게, 거래처 카드 간격 넓게 `[x]`

> **진행(2026-10-16)**: 보리 "시안 괜찮아. 착수지시서 확정, 작업 진행해". 코드 커밋 react-app `ba0018f`(push 전).
> 새 테스트 3개 고치기 전 실패 확인 → 고친 뒤 `npm test` 1,345개 통과. CSS 값 4줄씩만 바뀜, 줄 수 그대로.
> AI 개발 서버 비회원 320px 측정(시험 거래처·차량·주유 넣고 확인 뒤 지움): 거래처 카드 44px·사이 8px·긴 이름과 안 겹침(버튼 아래 153 < 둘째 줄 161), 차량 카드 44px, 하루 화면 주유 카드 44px, 차량 유지비 기록 44px. 운행 카드는 시안 때 같은 규칙으로 확인.
> 리뷰 §5: 건드릴 파일 3개+새 테스트 1개만, 공용 영향 목록 그대로, 새 저장·타입 꼼수 없음. 보리 push 뒤 CI verify·deploy 초록(run 38843064707). 보리 최종 `[x]` 승인(2026-10-16).

로드맵 48번(⑤, 외부 검수 UI 점검). 47번 `[x]`(`f1a28aa`) 다음. 48~54 규칙대로 **시안 먼저** — 아래 "시안"을 보리가 확인한 뒤 코드 수정.

### 쉬운 요약
- 거래처·차량·유지비·운행·지출 카드의 연필(수정)·휴지통(삭제) 버튼을 손가락 크기(44px)로 키우고, 거래처 카드의 두 버튼 사이를 3px → 8px로 띄운다.
- 수정하려다 삭제를 누르는 일을 줄인다. 색·모양·글자는 그대로, 버튼 칸만 커진다.

### 현재 상태 (코드·개발 서버 측정, 2026-10-16)
- 공용 버튼 `.action-icon-btn`(`management-common.css:59`). 크기는 놓인 곳마다 따로:
  - 거래처·차량 카드·유지비 기록: **34px**(`management-common.css:99-103`), 거래처 카드 두 버튼 사이 **3px**(`.car-action-btns` `:51-57`) — 개발 서버 375px에서 수정 267~301, 삭제 304~338 측정.
  - 하루 화면 운행 카드·지출 카드: **36px**(`day-log-shell.css:111-117`), 운행 카드 사이 4px(`call-detail-card.css:197-200`), 지출 카드 0px(`day-log-expenses.css:20-23`).
  - 운행 카드 전화·문자: **40px**(`call-detail-card.css:91-103`).
- 거래처 카드는 버튼이 오른쪽 위에 떠 있고(`client-management.css:120`), 이름 줄이 80px 비켜 줌(`:117-118`). 버튼만 키우면 320px 화면에서 긴 거래처 이름이 연필 밑으로 들어감(시안 3 확인).
- 화면 읽기 이름: 버튼마다 `title="수정"`/`"삭제"`가 있어(`CardActionButtons.jsx:15·18`, `CallDetailCard.jsx:43-44`, `ExpenseGroups.jsx:39-40`) 화면 읽기 기능은 이것을 이름으로 읽음 — 로드맵 문구 "이름 없음"은 정확하지 않음. 이번엔 안 바꿈.

### 시안 (개발 서버 비회원, 임시 스타일로 찍음 — 코드 안 바꿈, 시험 거래처·운행은 찍은 뒤 지움)
- 1·2: 거래처 카드 375px 지금 / 바꾼 뒤 — 버튼 34→44px, 사이 3→8px
- 3·4: 거래처 카드 320px 긴 이름 지금 / 바꾼 뒤 — 이름 줄이 100px 비켜 줘서 연필과 안 겹침, 둘째 줄과 버튼도 안 겹침(버튼 아래 153 < 둘째 줄 161)
- 5·6: 하루 화면 운행 카드 320px 지금 / 바꾼 뒤 — 36→44px, 제목이 두 줄로 자연스럽게 감김(시안은 버튼 사이 8px로 찍었지만, 실제로는 아래 "안 건드릴 것"대로 4px 그대로라 버튼 사이가 시안보다 4px 좁음)

### 목표 상태 (기대 동작)
1. 거래처·차량 카드, 유지비 기록, 기사 거래처 목록의 수정·삭제 버튼이 **44×44px**.
2. 하루 화면 운행 카드·지출 카드의 수정·삭제 버튼이 **44×44px**.
3. 거래처 카드(`.car-action-btns`) 두 버튼 사이가 **8px**.
4. 거래처 카드의 이름·칩 줄이 버튼 자리만큼(**100px**) 비켜 주고, 칩 줄 없을 때 이름 줄 높이가 **44px** — 320px 긴 이름에서도 글자와 버튼이 안 겹침.
5. 버튼 색·아이콘 크기·누름 색·하는 일은 그대로.

### 건드릴 파일
- `react-app/src/management-common.css` — `:51-57` gap 3→8px, `:99-103` 34→44px
- `react-app/src/components/day-log/day-log-shell.css` — `:111-117` 36→44px(padding 8px 그대로라 아이콘 17px 그대로)
- `react-app/src/components/clients/client-management.css` — `:117-119` padding-right 80→100px, min-height 30→44px
- 테스트: 새 `src/components/shared/CardActionButtons.test.js`(`npm test`가 `src/components/**`만 돌려서 이 자리) — 세 CSS 파일에서 위 값을 읽어 확인(기존 `ReportDetailView.test.js`의 CSS 읽기 방식)
- **공용 영향 목록**(AGENTS §5-6, `grep action-icon-btn`·`car-action-btns`):
  - `.management-list-card .action-icon-btn` → 차량 카드(`CarListItem.jsx:33·49`), 거래처 카드(`ClientListItem.jsx:22·54`), 기사 거래처 목록(`OwnerScopedClientsView.jsx:112·124`)
  - `.management-record-actions .action-icon-btn` → 유지비 기록(`MaintFuelPage.jsx:205`)
  - `.car-action-btns` gap → 위 세 곳(차량·거래처·기사 거래처)
  - `.call-detail-actions`/`.maint-fuel-actions .action-icon-btn` → 운행 카드(`CallDetailCard.jsx:43`), 지출 카드(`ExpenseGroups.jsx:38`)
  - 안 걸리는 곳: 기사 관리 톱니 버튼(`LinkedDriverManagementPage.jsx:154`, `linked-driver-profile-card` 안이라 위 규칙 밖), 기본 `.action-icon-btn`(`management-common.css:59`)은 그대로.

### 안 건드릴 것
- 운행 카드 전화·문자(40px)와 운행 카드 버튼 사이 4px — `call-detail-card.css`가 이미 213줄이라(§6) 이번엔 안 건드림. 지출 카드 버튼 사이(0px, 44px 칸끼리 붙어도 아이콘 사이는 27px)도 그대로.
- `title` 이름, 버튼 아이콘·색, 다른 화면 위쪽 버튼(14-B에서 이미 키움).
- 49~54번.

### 검증
- 로컬 `npm test` — 새 테스트는 고치기 전 실패 확인.
- 브라우저: AI가 개발 서버 비회원 320px·375px에서 거래처·차량·운행·지출 카드 측정(44px, 겹침 없음), 시험 데이터는 확인 뒤 지움.

### 파일 길이
- `management-common.css` 184 → 184 / `day-log-shell.css` 158 → 158 / `client-management.css` 147 → 147 (값만 바뀜)
- 새 테스트 파일은 테스트라 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(모양만 바꿈). 실패하면 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법 (보리 휴대폰)
1. 마이페이지 → 거래처 → 카드 오른쪽 위 연필·휴지통이 조금 커지고 사이가 벌어졌는지, 이름 긴 거래처도 글자와 안 겹치는지.
2. 차량 관리·차량 유지비 카드 연필·휴지통이 눌러지기 쉬운지.
3. 달력 → 날짜 → 운행 카드·주유 카드 연필·휴지통.

### 보리 확인 필요
- 시안 6장 괜찮은지.
- 운행 카드 전화·문자(40px)·버튼 사이 4px는 이번에 그대로 둠 — 같이 고치려면 `call-detail-card.css`(213줄) 분리 설계가 먼저 필요.
