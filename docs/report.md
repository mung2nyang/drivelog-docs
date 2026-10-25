# docs/report.md — 현재 슬라이스 착수지시서

UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## UI 위계화 B — 단계 밖 글자 크기를 공용 단계로 `[x]`

> react-app `1933adc`, 2026-10-25 보리 "승인". 375px 차량 입력 창·달력·설정·일일운행 전후 비교에서 바뀐 건 연월 버튼 17→17.28px·입력 창 제목 16.8→17.28px뿐, 넘침·줄 수 변화 0. 로그인 화면에서 점검표 창(제목 17.28px·묶음 제목 14.72px)·삭제 확인 창(제목 17.28px) 넘침 0. `.confirm-modal-text`(문장 있는 확인 창)는 띄우지 못해 값만 확인.

보리 지시(2026-10-25, A `[x]` 뒤): "작성해". 로드맵 번호 없음(로드맵에 안 올림). 위계화 순서 A 굵기(`023edeb`) → **B 크기(이번)** → C 제목 3단계 → D 간격·모서리 → E 버튼.
- sot에 글자 크기 규칙 없음. 지킬 원칙은 "가장 작은 글씨 12px(`--fs-floor`)"(STATUS 완료 목록, 2026-10-02 가이드라인 슬라이스 A `eb183aa`).

### 쉬운 요약
- 글자 크기를 공용 7단계 대신 숫자로 직접 쓴 곳이 38곳 있다. 그중 25곳(+코드 안 2곳)을 가장 가까운 단계로 바꾼다.
- 14곳은 지금과 크기가 똑같고, 13곳은 0.5px 안팎으로 아주 조금 바뀐다(눈으로는 거의 구분 안 됨).
- 인쇄 서류·로고·큰 제목·기호(×, ›)처럼 일부러 크기를 정한 10곳은 그대로 둔다. 12px보다 작은 3곳은 확인할 점으로 묻는다.

### 현재 상태 (코드 확인)
- 공용 단계: `--fs-floor` 0.75rem(12px) / `--fs-2` 0.78rem(12.48px) / `--fs-3` 0.85rem(13.6px) / `--fs-4` 0.92rem(14.72px) / `--fs-5` 1rem / `--fs-6` 1.08rem(17.28px) / `--fs-7` 1.18rem(18.88px)(`shared/styles/variables.css` 5~11줄).
- 숫자로 쓴 크기: CSS 38곳(`grep -rnE "font-size:\s*[0-9.]+(rem|px|em)" --include=*.css src`) + `clamp` 2곳 + 코드 안 `HydrationRetryBanner.jsx` 13px 2곳(16·27줄).
- 12px보다 작은 곳 3곳: 달력 정비 배지 0.68rem(좁은 화면 380px 이하만, `calendar-grid.css` 191줄 `.maint-badge`), 서류 탭 0.7rem(380px 이하만, `documents.css` 173줄 `.doc-scope-tab`), 알림 숫자 배지 11px(`app-top-bar.css` 52줄 `.notification-count-badge`). 셋 다 12px 작업(10-02) 뒤에 생겼고, 앞의 둘은 좁은 화면에서 넘치지 않게 일부러 줄인 것이다(react-app `1c20f77` 2026-10-12 "작은 화면: 달력 배지 축소, 서류 종류 탭 한 줄"). 알림 배지는 새 홈(`99a317d` 2026-10-10)에서 생김.

### 목표 상태 (기대 동작)
1. **크기 그대로(14곳)**: 0.85rem 13곳 → `var(--fs-3)` — `calendar.css` 98, `client-management.css` 138, `call-detail-form.css` 72, `daily-inspection.css` 22·144·157·178·214·246, `car-business-info.css` 15, `expense-form.css` 21, `modal-form.css` 28·126. 12px 1곳 → `var(--fs-floor)` — `call-detail-payment.css` 37. (아래 2번 13곳과 합쳐 바꾸는 곳 27곳 = CSS 25 + 코드 2)
2. **0.5px 안팎 바뀜(13곳)** — 가장 가까운 단계로:
   - 0.95rem(15.2px) → `--fs-4`(14.72px, -0.48): `boot-retry.css` 17(첫 화면 오류 안내 문장), `calendar.css` 92(연동 차량 배너 제목), `daily-inspection.css` 131(점검표 묶음 제목)·231(점검표 비고 이름), `modal-base.css` 21(확인 창 문장)
   - 1.15rem → `--fs-7`(+0.48): `boot-retry.css` 16(첫 화면 오류 제목)
   - 1.1rem → `--fs-6`(-0.32): `daily-inspection.css` 76(점검표 창 제목)
   - 0.9rem → `--fs-4`(+0.32): `report.css` 132(운송비 내역서 들여 쓴 줄 이름)
   - 1.05rem → `--fs-6`(+0.48): `modal-base.css` 19(`.modal-title`, 입력 창 제목 — 차량·거래처·지출·콜 상세 등 창 11곳이 같이 씀)
   - 17px → `--fs-6`(+0.28): `app-dropdown.css` 17(`.app-dropdown-trigger`, 연월 고르기 버튼 — `AppDropdown.jsx` 하나가 씀, 입력칸이 아니라 아이폰 확대와 무관)
   - 13px → `--fs-2`(-0.52): `app-settings.css` 116(고정 노선 프리셋 줄 버튼), `HydrationRetryBanner.jsx` 16·27(불러오기 다시 시도 띠 문장·버튼, `fontSize: 'var(--fs-2)'`)
3. **그대로 두는 10곳(예외)**: `legal-form.css` 21·26·51·60·87(인쇄 서류, 종이 양식에 맞춘 px) / 로고·큰 제목 `account-flow.css` 153(2.2rem)·`auth-form.css` 53(clamp)·93(1.78rem)·`onboarding.css` 25(clamp)·`app-top-bar.css` 22(1.45rem 앱 이름) / 기호 `message-template.css` 32(1.7rem ×)·`home.css` 122(1.2rem ›). 줄 수로는 12줄(clamp 2곳 포함).
4. 끝나면 숫자로 쓴 크기는 위 3번 예외 12줄 + 12px보다 작은 3곳(그대로 둠)뿐이다.
5. 새 주석은 없다.

### 건드릴 파일
- CSS 13개(값만): `app/shell/boot-retry.css`, `features/calendar/ui/calendar.css`, `features/clients/ui/client-management.css`, `features/day-log/ui/call-detail-form.css`, `features/day-log/ui/call-detail-payment.css`, `features/day-log/ui/daily-inspection.css`, `features/documents/ui/report.css`, `features/drivers/ui/car-business-info.css`, `features/expenses/ui/expense-form.css`, `features/settings/ui/app-settings.css`, `shared/styles/modal-base.css`, `shared/styles/modal-form.css`, `shared/ui/app-dropdown.css`
- 코드 1개: `app/shell/HydrationRetryBanner.jsx` 16·27줄 값만
- 영향 목록(여러 곳이 같이 쓰는 클래스): `.modal-title` — `DataDownloadModal`·`InviteRedeemModal`·`LinkStatusChip`·`CarFormModal`·`ClientFormModal`·`CallDetailForm`·`ReportDetailView`·`TaxInvoiceDraftModal`·`DriverFormModal`·`ExpenseFormModal`·`FixedRouteClientModal`·`ConfirmModal`. `.confirm-modal-text` — `DataDownloadModal`·`LinkStatusChip`·`ConfirmModal`. `.app-dropdown-trigger` — `AppDropdown` 하나. 배치·너비·높이 속성은 안 바꾼다.

### 안 건드릴 것
- 위 3번 예외 10곳(12줄), 12px보다 작은 3곳(`calendar-grid.css` 191, `documents.css` 173, `app-top-bar.css` 52), `index.html` 첫 로딩 화면 22px(앱 CSS보다 먼저 그려짐).
- 공용 단계 값 자체(`--fs-*`)와 새 단계 추가 — 안 함.
- 제목 단계 정하기 — C에서.

### 검증
- 테스트·타입·빌드는 CI가 한다. 화면 모양만 바뀌는 일이라 새 테스트는 없다.
- 바꾼 줄을 바꾸기 전 줄과 1:1로 맞대어 위 1·2번 대응표대로만 바뀌었는지 기계로 확인. 남은 숫자 크기가 4번 목록과 같은지 검색으로 확인.
- 개발 서버 375px 폭에서 바뀌는 화면(확인 창, 입력 창 제목, 일상점검표 창, 달력 연동 차량 배너, 운송비 내역서, 고정 노선 설정, 연월 고르기)의 글자 넘침·줄바꿈을 바꾸기 전과 후로 비교.

### 파일 길이
- 값만 같은 줄 안에서 바뀌므로 모든 파일 줄 수가 그대로다.
- 이미 200줄 넘는 3개(`call-detail-form.css` 285, `report.css` 257, `daily-inspection.css` 254)는 A 때 정한 대로 값만 바꾸고 나누지 않는다 `[확인: 2026-10-25 보리 — A "가"]`. `app-settings.css` 197줄도 줄이 안 늘어 그대로.

### 실패 시 처리
- 새 보호 장치·대비 장치를 만들지 않는다. 375px에서 넘침·이상한 줄바꿈이 새로 생기면 고치지 말고 멈춘 뒤, 원인과 수정 계획을 다시 써서 승인받는다.

### 사용자 확인 방법
1. 배포본에서 차량 관리 → [추가]를 눌러 입력 창 제목을 본다 → 전과 거의 같은 크기면 성공.
2. 일일운행 → 일상점검표 창을 연다 → 제목·묶음 제목이 깨지거나 넘치지 않으면 성공.
3. 아무 지우기 버튼을 눌러 확인 창 문장이 잘 보이는지 본다(지우지 말고 [취소]).

### 확인할 점
1. 12px보다 작은 3곳(좁은 화면 달력 정비 배지 10.9px, 좁은 화면 서류 탭 11.2px, 알림 숫자 배지 11px) — 가(그대로 둠) `[확인: 2026-10-25 보리 — "가"]`
   - 가: 그대로 둔다(예외). 앞의 둘은 좁은 화면 넘침을 막으려고 일부러 줄였고(`1c20f77`), 알림 배지는 작은 동그라미 안 숫자라 키우면 동그라미도 커져야 할 수 있다.
   - 나: 12px(`--fs-floor`)로 키운다. 375px·320px에서 넘침을 확인하고, 넘치면 멈추고 다시 묻는다.

---

## 쉬운 요약
- 지금 글자 굵기는 화면마다 숫자를 직접 써서 9가지가 섞여 있다. 이걸 `variables.css`의 공용 굵기 7개로 묶는다.
- 이번엔 **어느 기기에서도 보이는 굵기가 그대로**다. 묶어 두면 나중에 "제목은 더 굵게, 굵기 단계 줄이기"가 공용 값 한 줄 고치기로 끝난다.
- 미수금 상세 창의 합계 금액 숫자가 정의 안 된 크기를 써서 의도보다 작게 보인다. 가장 큰 글자 단계로 고친다.

### 현재 상태 (코드 확인)
- 글자 크기는 공용 단계 `--fs-floor`~`--fs-7`이 있다(`shared/styles/variables.css` 5~11줄). 굵기 공용 값은 없다(같은 파일, `--fw` 0건).
- 굵기 숫자 직접 지정: CSS 54개 파일 220곳. 값별로 400(9)·500(7)·600(15)·650(10)·700(72)·750(28)·800(53)·850(22)·900(4)(`grep -rhoE "font-weight:\s*[^;]+"` 집계). `bold`·`normal` 같은 글자 값은 0건.
- 코드 안 직접 지정 1곳: `features/revenue/ui/OwnerMonthlyCards.jsx` 70줄 `fontWeight: 850`.
- 앱 글꼴은 휴대폰 기본 글꼴이다(`shared/styles/app-base.css` 6줄). 650·850은 브라우저가 700·900과 똑같이 그린다(이 PC 크롬에서 같은 문장 글자 폭 650=700, 850=900). **750은 기기마다 다르다** — 이 PC에선 700과 같고(750≠800), 굵기가 촘촘한 글꼴(아이폰)에선 규칙상 800 쪽이다. 그래서 750은 따로 둔다 `[확인: 2026-10-25 보리 — "다로"]`.
- 미수금 합계: `features/receivables/ui/receivables.css` 214줄 `font-size: var(--fs-8)`. `--fs-8`은 어디에도 없다(`grep -rn "fs-8"` 이 1곳뿐). 없는 값이라 이 줄은 무시되고 바깥 글자 크기를 받는다.

### 목표 상태 (기대 동작)
1. `variables.css` `:root`에 굵기 공용 값 7개를 더한다: `--fw-regular: 400` / `--fw-medium: 500` / `--fw-semibold: 600` / `--fw-bold: 700` / `--fw-strong: 750` / `--fw-heavy: 800` / `--fw-black: 900`. 다크모드는 굵기가 같아 따로 안 적는다.
2. 굵기 숫자를 공용 값으로 바꾼다. 400→regular, 500→medium, 600→semibold, 650·700→bold, 750→strong, 800→heavy, 850·900→black. 750을 700·800으로 합칠지는 C(제목 3단계)에서 화면 보고 정한다.
3. 끝나면 `src/` CSS에 `font-weight: 숫자`가 0건이다. 로고 글꼴(Jua) 3곳의 400도 regular로 바꾼다.
4. `OwnerMonthlyCards.jsx` 70줄은 `fontWeight: 'var(--fw-black)'`.
5. `receivables.css` 214줄은 `font-size: var(--fs-7)`(새 단계를 만들지 않음). 미수금 상세 창 합계 숫자가 1.18rem으로 보인다.
6. 새 주석은 없다. `variables.css` 첫 줄 설명("디자인 토큰 단일 출처")이 이미 범위를 말한다.

### 건드릴 파일
- `src/shared/styles/variables.css` — 굵기 공용 값 7줄 추가
- `src/` 아래 굵기 숫자가 있는 CSS 54개 — 값만 바꿈(목록: `grep -rlE "font-weight:\s*[0-9]" --include=*.css src`)
- `src/features/revenue/ui/OwnerMonthlyCards.jsx` — 70줄 값만
- `src/features/receivables/ui/receivables.css` — 위 54개에 포함, 214줄 크기도 바꿈
- 영향 목록: 클래스 이름·자리 잡는 속성(배치·너비·높이 등)은 하나도 안 바꾼다. 굵기 값만 같은 줄에서 바뀌므로 영향은 각 줄의 그 글자뿐이다. 보이는 변화는 미수금 합계 크기 1곳.

### 안 건드릴 것
- 글자 크기의 단계 밖 값 약 40곳 — B에서. `legal-form.css`(인쇄 서류 px)와 로고 큰 제목(`clamp`)은 B에서도 예외 후보.
- 제목 단계 — C에서. 간격·모서리·버튼 — D·E에서.
- 굵기 단계를 줄이는 일(예: 900을 800으로) — 이번엔 안 함. 보이는 굵기가 바뀌므로 화면 보고 따로 정한다.
- `index.html` 50줄 첫 로딩 화면의 400 — 앱 CSS보다 먼저 그려져 공용 값을 못 쓴다.

### 검증
- 테스트·타입·빌드는 CI가 한다.
- `grep -rnE "font-weight:\s*[0-9]" --include=*.css src` 0건, `grep -rn "fs-8" src` 0건.
- 바꾸기 전 CSS의 굵기 줄과 바꾼 뒤 줄을 1:1로 맞대어, 위 2번 대응표대로만 바뀌었는지 기계로 확인.
- 개발 서버에서 650=700, 850=900 글자 폭이 같은지 다시 재고, 홈·미수금 상세 화면을 바꾸기 전과 후 사진으로 비교. 미수금 상세 합계만 크기가 바뀜.
- 화면 모양만 바뀌는 일이라 새 테스트는 만들지 않는다. 그래서 고치기 전 실패 확인도 없다.

### 파일 길이
- 굵기 값은 같은 줄 안에서 바뀌므로 모든 파일 줄 수가 그대로다. `variables.css`만 57 → 64줄.
- **이미 200줄 넘는 파일 5개를 값만 바꾼다**: `receivables.css` 419, `call-detail-form.css` 285, `report.css` 257, `daily-inspection.css` 254, `call-detail-card.css` 213. AGENTS §6은 "200줄 초과 파일을 수정하면 분리설계 먼저"인데, 이번은 줄이 늘지 않는 값 바꾸기라 분리 없이 한다 `[확인: 2026-10-25 보리 — "가로 진행해"]`. 나누기는 A에 섞지 않는다. 그 파일에 줄이 늘어나는 작업이 생길 때 그 파일 하나만 분리설계한다.

### 실패 시 처리
- 새 보호 장치·대비 장치를 만들지 않는다. 보이는 굵기가 바뀐 화면이 있으면 고치지 말고 멈춘 뒤, 원인과 수정 계획을 다시 써서 승인받는다.

### 사용자 확인 방법
1. 배포본에서 홈·일일운행·설정 화면을 열고 글자 굵기가 전과 같아 보이는지 본다(달라 보이면 성공 아님).
2. 미수금 → 거래처 하나를 눌러 상세 창을 연다 → 위 합계 금액 숫자가 전보다 조금 커졌으면 성공.

### 확인할 점
1. 200줄 넘는 CSS 5개는 분리 없이 값만 바꾼다 — 가 `[확인: 2026-10-25 보리 — "일단 그럼 가로 진행해"]`.

---

## 로드맵 112 일상점검표 지우기 — 다 비우고 저장하면 지움 `[x]`

보리 지시(2026-10-25): "점검표를 지우는 기능도 추가해야해" → "112번 점검표 지우기 먼저 하자. 착수지시서 작성해"(34번보다 먼저).
- 계기: 24-J-2 배포본 시험으로 저장한 10월 25일 점검표(테스트 계정)를 지울 길이 없었다.
- 처음 지시서는 보기 창에 [삭제] 버튼을 두는 안이었다. 보리 정정으로 바꿨다: "걍 수정에서 양호나 불량을 눌러야지 저장가능햇다면 비어도 저장가능하게 바꿔". 따로 버튼을 두지 않고, 수정에서 다 비우고 [저장]하면 지우는 방식이다.
- 저장·삭제 경로라 `docs/testing-playbook.md`를 열람했다(AGENTS §4). 점검표는 휴대폰에 보관하지 않고 바로 서버로 가므로, 해당하는 것은 §3의 서버 실패(12·13)와 §5의 세션 확인뿐이다.

### 확인할 점 (둘) — **1·2 모두 가로 확정** `[확인: 2026-10-25 보리 — "확인할 점 1·2 둘 다 가로"]`
1. **일부만 고른 점검표**(11항목 중 몇 개만 고르고 [저장])
   - **권함 가**: 지금처럼 "모든 항목을 선택해 주세요."로 막는다. **전부 비운 경우만** 새로 허용한다.
     - 서류 한 달 표·법정 서식은 고르지 않은 칸을 "미"(미운행 표시)로 적는다(`domain/dailyInspectionMonth.ts` `inspectionMark`). 그래서 일부만 저장하면 운행한 날인데 "미"가 섞인다.
   - 나: 일부만 골라도 저장한다. 고르지 않은 칸은 서식에 "미"로 나온다.
2. **전부 비우고 [저장]했을 때**
   - **권함 가**: 앱 공용 확인 창으로 한 번 묻고 지운다.
     - 제목 "점검표를 지울까요?"
     - 본문 "모든 항목이 비어 있어 {M월 D일} 점검표가 지워집니다. 되돌릴 수 없습니다."
     - 버튼 [지우기](빨간색)·[취소]. [취소]면 수정 화면에 그대로 남는다.
     - 지우면 창이 닫히고, 알림 "일상점검표를 지웠습니다."가 뜨고, 안내 줄이 "작성이 필요합니다 [+ 입력]"으로 바뀐다.
   - 나: 묻지 않고 바로 지운다(알림만).

### 쉬운 요약
- 점검표 수정 화면에서 양호·불량을 모두 해제하고(또는 [모두 양호]를 한 번 더 눌러 전부 해제하고) [저장]하면 그날 점검표가 지워진다. 그날은 다시 "작성 필요"가 된다.
- 버튼은 새로 만들지 않는다. 서버는 이미 지우기를 허락하므로 데이터베이스도 바꾸지 않는다.

### 현재 상태 (코드 확인)
- 저장 조건은 11항목 모두 선택이다(`domain/dailyInspectionItems.ts` `isInspectionComplete`). 하나라도 비면 `DailyInspectionModal.jsx` `save`가 "모든 항목을 선택해 주세요."를 띄우고 멈춘다.
- 수정 화면에서 같은 버튼을 다시 누르면 선택이 풀리고(`toggleItem`), [모두 양호]를 전부 양호일 때 다시 누르면 전부 풀린다(`toggleAllGood`). 그래서 다 비우는 길은 이미 있다.
- 앱에는 지우는 함수가 없다. `data/sync/dailyInspections.ts`에는 읽기와 저장(`saveDailyInspection`, 차량+날짜로 덮어씀)만 있다.
- 서버는 이미 지울 수 있다. DB `0014` "일상점검 삭제" 정책은 쓰기·수정과 같은 조건이다: 그 차량·날짜에 쓸 수 있는 연동 기사, 또는 내 차량이면서 그날 연동 기사가 없을 때.
  - 이 정책이 막으면 서버는 **오류 없이 0줄을 지운다**(Supabase 동작). 그래서 지운 줄 수를 받아 확인해야 한다.
- 안내 줄 `DailyInspectionNotice.jsx`는 그날 1장을 화면 상태로 들고 있고, 저장하면 그 상태를 바로 바꾼다(`save` → `setResult`).
- 다른 곳은 열 때마다 서버에서 다시 읽는다. 그래서 따로 고칠 것이 없다.
  - 홈 할 일 "오늘 일상점검 안 함": `home/hooks/useTodayInspectionMissing.js`가 홈을 열 때 읽는다.
  - 서류 한 달 표: `fetchMonthDailyInspections`가 화면을 열 때 읽는다.
  - 운비서 점검표 카드(24-J-2): 말할 때마다 읽는다.
- 공용 확인 창은 `confirmDialog`(`shared/ui/confirmDialog.jsx`, `danger` 지원)다.

### 목표 상태 (기대 동작)
1. 수정 화면(저장된 점검표를 연필로 연 경우)에서 11항목이 **모두 비어 있으면** [저장]이 지우기로 동작한다(확인할 점 2).
2. 일부만 고른 경우는 확인할 점 1대로 한다(가: 지금처럼 막음).
3. **처음 입력**(저장된 점검표가 없는 날)에서 다 비우고 [저장]하면 지울 것이 없다. 지금처럼 "모든 항목을 선택해 주세요."를 띄운다.
4. 지우기는 그 차량·날짜 점검표를 서버에서 지운다. 지우는 동안 [저장]이 잠긴다(두 번 눌림 막기).
   - 성공(1줄 지움) → 창이 닫히고 안내 줄이 "작성 필요 [+ 입력]"이 된다. 알림을 띄운다(확인할 점 2).
   - 서버 오류 또는 0줄(권한 없음·이미 없음) → 실패 알림을 띄우고 수정 화면에 남는다. 실패 알림은 저장 실패와 같은 것을 쓴다(오류 문구, 없으면 `SERVER_SAVE_FAIL`). 0줄일 때 문구는 "일상점검표를 지우지 못했습니다."다.
5. 저장과 같은 막기를 한다. 로그인이 아니면 지우지 않고, 쓰기 막힘 사유(`blockedReasonForCloudWrite`)가 있으면 그 문구를 띄운다. 기다리는 동안 계정이 바뀌면 결과를 붙이지 않는다(`captureSession`·`assertSessionStillCurrent`).
6. 지운 날에 다시 [+ 입력]으로 새로 저장할 수 있다. 운비서 "오늘 점검표 모두 양호"도 다시 [저장하기]를 보인다.
7. 조치 기록 칸만 쓰고 항목을 다 비운 경우도 "모두 비어 있음"으로 본다(항목 기준).
8. 휴대폰 보관·재시도·지운 흔적(tombstone)은 만들지 않는다.

### 건드릴 파일
- `src/data/sync/dailyInspections.ts`: `deleteDailyInspection({ vehicleId, workDate })`를 추가한다. 저장과 같은 막기를 하고, 지운 줄 수를 받아 0줄이면 실패로 한다.
- `src/domain/dailyInspectionItems.ts`: `isInspectionEmpty(items)`(11항목 모두 비었는지) 한 개.
- `src/features/day-log/ui/DailyInspectionModal.jsx`: `save`에서 "수정 중 + 모두 비어 있음"이면 `onDelete`로 넘긴다. 지우는 중 잠금.
- `src/features/day-log/ui/DailyInspectionNotice.jsx`: 확인 창을 띄우고 지우기를 부르고, 성공하면 상태를 "없음"으로 바꾸고 알림을 띄운다.
- 시험: `dailyInspections.test.js`(지우기 성공·0줄·오류·막힘), `DailyInspectionNotice.test.js`(다 비우고 저장 → 확인·취소·성공·실패·잠금, 처음 입력에서 비우고 저장은 지금처럼 막힘), 필요하면 `dailyInspectionItems` 시험.

### 안 건드릴 것
- DB(정책 `0014`가 이미 있음 — `react-app/supabase/migrations/0014_daily_inspections.sql` 79~83줄).
- 버튼·CSS(새 버튼 없음).
- 홈 할 일·서류 한 달 표·운비서 점검표 카드(열 때 다시 읽음 — 현재 상태 근거).
- 공용 확인 창(그대로 씀).

### 검증
- 로컬 `npm test`·타입·검사기(CI가 다시 함). 새 시험은 고치기 전 코드에서 실패하는지 확인한다.
- 실패 주입(플레이북 §3 12·13): 서버가 오류를 던질 때, `{ data: null, error }`일 때, 0줄일 때 모두 수정 화면이 남고 안내 줄 상태가 그대로이며 성공 알림이 없는지 본다.
- 계정 바뀜(플레이북 §5): 지우는 중 계정이 바뀌면 화면 상태를 바꾸지 않는지 본다.
- 배포본: 데스크톱 AI가 테스트 계정의 10월 25일 점검표(24-J-2 시험분)를 연필 → [모두 양호] 다시 눌러 전부 해제 → [저장] → 확인 → 안내 줄 "작성 필요" → 서류 한 달 표 25일이 O가 아님 → 운비서 "오늘 점검표 모두 양호"가 다시 [저장하기]를 보임(이때는 저장하지 않음).

### 파일 길이
| 파일 | 지금 | 예상 |
|---|---|---|
| `data/sync/dailyInspections.ts` | 95 | ~115 |
| `domain/dailyInspectionItems.ts` | 62 | ~66 |
| `day-log/ui/DailyInspectionModal.jsx` | 136 | ~145 |
| `day-log/ui/DailyInspectionNotice.jsx` | 110 | ~130 |

- 200줄 감시 목록 파일은 없다.

### 코드에 넣을 주석(최종 문구)
- `dailyInspections.ts` 지우기 함수 위: `/** 그 차량·날짜 점검표 지우기. 권한이 막으면 서버가 오류 없이 0줄을 지우므로 0줄도 실패로 본다. */`
- `DailyInspectionModal.jsx` `save` 안: `// 저장된 점검표를 다 비우고 저장하면 지운다(처음 입력은 지금처럼 막음).`
- `DailyInspectionNotice.jsx` 지우기 처리 위: `// 지우기는 확인 창을 거쳐 서버에서 지운 뒤 "작성 필요"로 돌린다.`

### 실패 시 처리
- 새 저장·재시도·임시 보관·지운 흔적은 만들지 않는다(AGENTS §7). 실패는 알림만 띄운다.
- 시험이나 배포본이 기대와 다르면 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법
- 휴대폰에서 점검표가 있는 날의 일지를 연다 → 안내 줄 [보기] → 연필 → [모두 양호]를 눌러 전부 해제(또는 하나씩 해제) → [저장] → 확인 창 [지우기].
- 창이 닫히고 "일상점검표를 지웠습니다." 알림이 뜬다. 안내 줄이 "일상점검표 작성이 필요합니다. [+ 입력]"으로 바뀐다.
- [+ 입력]으로 다시 저장할 수 있다.

### 진행 기록
- 2026-10-25 보리 정정: 보기 창 [삭제] 버튼 안 대신 "수정에서 비어도 저장 가능하게" → 다 비우고 저장하면 지우는 방식으로 지시서를 고침.
- 2026-10-25 보리: "확인할 점 1·2 둘 다 가로." — 일부만 고른 것은 지금처럼 막고 전부 비운 경우만 허용, 지우기 전 확인 창("점검표를 지울까요?", [지우기]·[취소]), 지우면 알림 "일상점검표를 지웠습니다.". 구현은 터미널 AI(시작 전 교차검증).
- 2026-10-25 보리: "착수지시서 확정 작업진행해" — 터미널 AI.
  - 교차검증: 지시서의 코드 사실(`isInspectionComplete`·`toggleItem`·`toggleAllGood`, 지우는 함수 없음, `0014` 79~83줄 "일상점검 삭제" 정책, 안내 줄 `save` → `setResult`, `confirmDialog` `danger`)이 모두 맞아 고친 것 없음. 조회 정책(62줄)이 삭제 정책보다 넓어 지운 줄을 돌려받을 수 있음도 확인.
  - 구현: react-app `ad2902d`(push 안 함).
    - `dailyInspections.ts`(109줄): `deleteDailyInspection`. 차량·거래처 지우기와 같은 방식(`.delete()…select('id')`, `directMutations.ts`)으로 지운 줄을 받아, 오류·0줄·줄 정보 없음이면 던진다. 0줄 문구 `DELETE_FAIL`.
    - `dailyInspectionItems.ts`(67줄): `isInspectionEmpty`.
    - `DailyInspectionModal.jsx`(142줄): 연필로 연 수정에서 다 비었으면 `onDelete`, 아니면 지금처럼. `DailyInspectionNotice.jsx`(129줄): 확인 창 → 지우기 → 상태 "없음"·알림, 실패면 오류 문구 또는 `SERVER_SAVE_FAIL`.
  - 지시서와 다른 점: 저장 중 잠금을 상태에서 ref로 바꿈(모달 +3줄). 상태만으로는 같은 순간 두 번 눌림이 두 번 지워져서(시험으로 확인). 기존 저장도 같이 막힌다.
  - 검증: lint·typecheck·depcheck(1802)·npm test(896·873) 통과. 실패 주입: 서버 오류(던짐·`{ error }`)·0줄·줄 정보 없음 모두 실패, 수정 화면·안내 줄 그대로, 성공 알림 없음. 로그인 안 함·서버 값 받는 중이면 서버를 부르지 않음. 지우는 사이 계정이 바뀌면 던짐.
  - 고치기 전 실패 확인: 새 시험 8개(서버 3·화면 4·항목 1) 모두 고치기 전 코드에서 실패(항목 1개는 함수가 없어 파일을 못 불러와 실패), 고친 뒤 통과.
  - 남은 일: 보리 push → CI → 데스크톱 AI 배포본 시험(10월 25일 시험분 지우기) → 보리 휴대폰 확인.
- 2026-10-25 보리 push 뒤 CI verify·deploy 초록(run 40159988220). §5 리뷰: 범위 = 지시서 파일 그대로, 새 저장·재시도 없음, 타입 꼼수 없음, 주석 지시서 문구(+ `isInspectionEmpty` 설명 한 줄), 200줄 안(최대 `DailyInspectionModal.jsx` 142).
- **배포본 시험 실패(데스크톱 AI)**: 10월 25일 점검표 보기 → 연필 → [모두 양호] 다시 눌러 11항목 해제 → [저장] → 확인 창이 **점검표 창 뒤에 가려져 보이지 않음**. 문구("점검표를 지울까요? / 모든 항목이 비어 있어 10월 25일 점검표가 지워집니다. 되돌릴 수 없습니다.", [취소]·[지우기])는 맞으나 [지우기] 자리를 누르면 점검표 창 글자(`di-sign`)가 눌린다. Esc로 취소 → 지워지지 않음, 수정 취소 → 안내 줄 "작성 완료" 그대로.
  - 원인: 두 창 바깥막 `.modal-overlay`가 둘 다 `z-index: 1200`(`shared/styles/modal-base.css` 10줄)이고 같은 층이다. 확인 창 자리 `<ConfirmDialogHost />`는 앱 틀에서 화면 내용 **앞**(`app/shell/AppShell.jsx` 160줄)에 있고, 점검표 창은 일지 화면 안(`.modal-work-details`)이라 문서 순서상 뒤 → 점검표 창이 위에 그려진다.
  - 지금까지는 확인 창을 다른 창 위에서 띄운 곳이 없어 드러나지 않았다: `confirmDialog`를 부르는 곳은 `OffToggle.jsx`·`inlinePanelActions.js`·`callDetailFormHelpers.js`(콜 상세 입력 칸, 창 아님)·이번 `DailyInspectionNotice.jsx`. 창 껍데기(`ModalShell`)를 쓰는 일지 쪽 파일은 `DailyInspectionModal.jsx`·`MessageTemplateSheet.jsx`뿐이고, 문구 시트에서는 확인 창을 띄우지 않는다.

### 수정 착수지시서 — 확인 창을 다른 창 위로 `[x]`

- **확인할 점 (하나)** — 고치는 방법
  - **권함 가**: `app/shell/AppShell.jsx`에서 `<ConfirmDialogHost />`를 화면 내용 **뒤**(컨테이너 맨 끝)로 옮긴다. 같은 층에서는 나중 것이 위에 그려지므로 확인 창이 늘 위에 온다. 한 줄 자리만 바뀌고 줄 수는 그대로다(198줄, 200줄 감시 목록 파일이라 줄을 늘리지 않음). CSS는 바꾸지 않는다.
  - 나: 확인 창 바깥막에만 새 클래스로 `z-index: 1250`을 준다(`ConfirmModal.jsx`·`modal-base.css`). 공용 클래스 `.modal-overlay`는 그대로다. 단, 문구 시트(`message-template.css` 1300) 같은 더 높은 층 위에서는 다시 가려질 수 있다.
- **건드릴 파일**(가): `src/app/shell/AppShell.jsx`(한 줄 옮김). 시험: `DailyInspectionNotice.test.js` 또는 앱 틀 시험에 "확인 창이 점검표 창보다 문서 순서상 뒤에 있음" 확인 하나.
- **안 건드릴 것**: `.modal-overlay` 공용 CSS(영향 범위가 앱 전체 창), 확인 창 부품, 112번 지우기 코드(동작은 맞음 — 문구·취소·안 지워짐 확인).
- **영향 범위**(가): 확인 창을 부르는 네 곳(`OffToggle.jsx`·`inlinePanelActions.js`·`callDetailFormHelpers.js`·`DailyInspectionNotice.jsx`) 모두 확인 창이 맨 위로 온다 — 지금도 다른 창이 없을 때는 같은 모습이다. 앱 틀의 다른 띠(`OfflineBanner`·`HydrationRetryBanner`)는 자리 그대로다.
- **검증**: 로컬 시험·CI. 배포본에서 다시 10월 25일 점검표를 다 비우고 [저장] → 확인 창이 위에 보이고 [지우기]가 눌림 → 지워짐 → 안내 줄 "작성 필요". 휴무 바꾸기 확인 창(`OffToggle`)도 그대로 뜨는지 본다.
- **실패 시 처리**: 새 장치 없음(AGENTS §7). 다른 화면 확인 창이 이상하면 다시 보고.
- **추가 수정 커밋 하나**로 올린다(AGENTS §3, 이력 되감기 없음).
- 2026-10-25 보리: "가로확정 터미널ai말고 직접고쳐" → 데스크톱 AI 구현: react-app `f337971`(push 안 함). `AppShell.jsx`에서 `<ConfirmDialogHost />`를 컨테이너 맨 끝(`SideMenu` 뒤)으로 옮김, 198줄 그대로. 새 시험 `app/shell/confirmLayer.test.js` 1개(앱 틀을 그려 확인 창이 `.page-transition`보다 문서 순서상 뒤인지) — 고치기 전 앱 틀에서 실패("확인 창이 화면 내용 뒤"), 고친 뒤 통과. lint·typecheck·`npm test`(896·874) 통과. 남은 일: 보리 push → CI → 배포본에서 10월 25일 점검표 다시 지우기.
- 보리 push 뒤 `f337971` CI verify·deploy 초록(run 40160744252).
- 배포본 다시 시험(데스크톱 AI, 테스트 계정, 2026-10-25): 10월 25일 보기 → 연필 → [모두 양호] 다시 눌러 11항목 해제 → [저장] → 확인 창 "점검표를 지울까요? / 모든 항목이 비어 있어 10월 25일 점검표가 지워집니다. 되돌릴 수 없습니다."가 **점검표 창 위에** 보임([지우기]가 그 자리 맨 위 요소) → [지우기] → 창 닫힘, 알림 "일상점검표를 지웠습니다." 1개, 안내 줄 "일상점검표 작성이 필요합니다. [+ 입력]" → 서류 발급 일상점검표 탭 25일 칸 "미" → 운비서 "오늘 점검표 모두 양호" → 다시 "… 저장할게요." + [저장하기](저장 안 함). 24-J-2 시험분은 이것으로 지워짐. 휴무 바꾸기 확인 창은 배포본에서 따로 누르지 않음(일지 데이터를 바꿀 수 있어서) — 확인 창 자리는 앱 전체에 하나라 같은 자리에서 뜬다.

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
