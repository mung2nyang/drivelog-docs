# docs/report.md — 현재 슬라이스 착수지시서

145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 146 날짜·시간 칸을 비울 수 없음 `[x]`

전수점검 C1(`docs/full-code-review.md` 52·210·343줄). 보리 "응"(2026-10-30) — 145 다음 순서. DB 변경 없음. 공용 부품이라 쓰는 곳 전부를 아래 영향 목록에 적음.

### 쉬운 요약
- 날짜·시간 칸(눌러서 고르는 칸)은 한 번 값을 고르면 다시 비울 수 없습니다.
- 기사 계약 종료일은 "종료일이 없으면 계속 배정"인데 한 번 고르면 되돌릴 수 없고, 출발·도착 시간은 "시"만 잘못 눌러도 값이 들어가 지울 길이 없습니다.
- 비워도 되는 칸에만 "지우기"를 붙입니다. 칸을 눌러 여는 고르기 창 맨 아래에 [지우기] 단추를 두고, 누르면 칸이 비고 창이 닫힙니다(값이 있을 때만 보임).

### 현재 상태 (코드 확인)
- 공용 부품 `shared/ui/TemporalInput.jsx`(160줄) — 고르면 값을 넣는 동작(`commit`)만 있고 빈 값으로 되돌리는 단추·동작 없음. 숨은 `input`은 읽기 전용.
- 쓰는 곳 8칸(전체 검색 `TemporalInput`):
  - 비워도 되는 칸: 기사 계약 종료일 `features/drivers/ui/DriverFormModal.jsx:136`(안내 "종료일이 없으면 계속 배정됩니다", 검사 `domain/drivers.ts:68`은 있을 때만 확인), 출발·도착 시간 `features/day-log/ui/CallDetailForm.jsx:164`·`168`(빈 값 저장 가능), 입금 예정일 `features/day-log/ui/CallDetailPaymentFields.jsx:29`.
  - 꼭 있어야 하는 칸(이번에 안 붙임): 계약 시작일 `DriverFormModal.jsx:131`(필수 표시·검사 `drivers.ts:67`), 지출 날짜 `features/expenses/ui/ExpenseFormModal.jsx:62`, 세금계산서 작성일 `features/documents/ui/TaxInvoiceDraftModal.jsx:84`.
- 입금 예정일은 비워 저장해도, 저장한 콜을 다시 열면 거래처 결제 주기로 다시 채워짐(`features/day-log/ui/callDetailFormHelpers.js:42`). 비운 날짜는 미수 연체 계산에서 빠짐(`domain/financeReceivables.ts:87`).

### 목표 상태 (기대 동작)
1. 계약 종료일·출발 시간·도착 시간 칸에 값이 있을 때, 칸을 눌러 연 고르기 창 맨 아래에 [지우기]가 보임. 누르면 칸이 "날짜 선택"/"시간 선택"으로 비고 창이 닫힘.
2. 값이 없을 때는 [지우기]가 안 보임.
3. 계약 시작일·지출 날짜·세금계산서 작성일에는 [지우기]가 없음(지금과 같음).
4. 비운 값은 각 창의 [저장]으로 지금처럼 저장됨(종료일 비우면 "계속 배정", 시간 비우면 빈 시간).
5. 날짜·시간 고르기 동작 자체는 그대로.

### 건드릴 파일
- `src/shared/ui/TemporalInput.jsx` — 새 속성 `clearable`(기본 꺼짐). 켜져 있고 값이 있으면 고르기 창 아래 [지우기] 단추: 빈 값으로 `onChange` → 창 닫기 → 칸에 초점. 속성 설명 한 줄 `@param {boolean} [props.clearable] 값이 있을 때 고르기 창에 [지우기](비워도 되는 칸만)`.
- `src/shared/ui/temporal-input.css` — [지우기] 단추 모양(창 아래 오른쪽 작은 글씨 단추, 다른 클래스는 안 바꿈).
- `src/features/drivers/ui/DriverFormModal.jsx` — 종료일 칸에 `clearable`.
- `src/features/day-log/ui/CallDetailForm.jsx` — 출발·도착 시간 칸에 `clearable`.
- 테스트 `src/shared/ui/TemporalInput.test.js` — `clearable` + 값 있음 → 창에 [지우기], 누르면 빈 값 `onChange`·창 닫힘. 값 없음·`clearable` 없음 → [지우기] 없음(안전 확인).
- 공용 영향 목록: 새 속성은 기본 꺼짐이라 위 8칸 중 `clearable`을 붙인 칸만 바뀜. 새 CSS 클래스(`app-temporal-clear`)는 새 단추에만 씀 — 기존 `.app-temporal-*` 클래스는 안 바꿈.

### 안 건드릴 것
- 고르기 창 열 칸(`TemporalColumns.jsx`), 날짜·시간 문자열 도구(`temporalValue.js`).
- 필수 칸 3개, 입금 예정일 칸(`CallDetailPaymentFields.jsx`, 보리 결정으로 안 붙임), 각 창의 저장·검사(`domain/drivers.ts` 등), 입금 예정일 자동 채우기(`callDetailFormHelpers.js`).
- "시"만 눌러도 값이 들어가는 동작 — 지우기로 되돌릴 수 있게만 하고 고르는 방식은 그대로.

### 검증
- `npm test`, 타입 검사, 코드 검사, 의존 검사, 빌드(CI).
- 새 테스트가 고치기 전 코드에서 실패하는지 확인(revert-check), 기존 `TemporalInput.test.js`·기사·콜 상세·지출·세금계산서 창 테스트 그대로 통과.
- 로컬 비회원 화면: 기사 등록 창 종료일 고름 → 창 열어 [지우기] → 빈 칸 / 콜 상세(운행 시간 켬) 출발 시간 "시"만 누름 → [지우기] → 빈 칸 / 지출 날짜 창엔 [지우기] 없음. 시험 자료 지움.
- [x] 때 sot에 "비워도 되는 날짜·시간 칸은 고르기 창 [지우기]" 한 줄.

### 파일 길이
- `TemporalInput.jsx` 160 → 약 170, `temporal-input.css` 122 → 약 135, `DriverFormModal.jsx`·`CallDetailForm.jsx`는 줄 수 그대로(속성만 추가). 모두 200 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 빈 값을 지금 있는 `onChange`로 넘기는 것뿐. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 확인할 점 (보리 결정) — 보리 "권함으로"(2026-10-30): 1 붙이지 않음, 2 가)
1. 입금 예정일에도 [지우기]를 붙일까요? 붙이면 비워 저장은 되지만, 그 콜을 다시 열면 거래처 결제 주기로 다시 채워지고 비운 콜은 미수 연체 알림에서 빠집니다. AI 권함: 붙이지 않음(다시 열면 채워져 지운 효과가 오래가지 않음).
2. [지우기] 자리: 가) 고르기 창 맨 아래 [지우기] 단추(권함 — 실수로 지울 일이 적고, "시"를 잘못 눌러 창이 열려 있을 때 바로 누를 수 있음) / 나) 칸 오른쪽 X(글자 칸 지우기와 같은 모양, 창을 안 열어도 됨).

### 사용자 확인 방법
1. 배포본 기사 관리 → 기사 수정 창 → 계약 종료일 칸을 눌러 날짜를 고른 뒤 다시 누름 → 창 아래 [지우기] → 칸이 "날짜 선택"으로 비면 성공([취소]로 닫으면 저장 안 됨).
2. 운행일지 세부 입력(운행 시간 켠 차량) → 출발 시간 "시"만 누름 → [지우기] → 칸이 "시간 선택"으로 비면 성공(창은 [취소]로 닫기).
3. 지출 추가 창 날짜 칸에는 [지우기]가 없으면 정상.

### 진행 기록
- 코드 커밋 react-app `faf12b1`. 전체 테스트(단위 956·화면 936), 타입 검사, 코드 검사, 의존 검사, 빌드 통과. 기존 TemporalInput 테스트 4개 그대로 통과.
- 고치기 전 실패 확인(revert-check): "값이 있으면 [지우기], 누르면 빈 값·닫힘" 고치기 전 실패 → 고친 뒤 통과. "값 없음·지우기 칸 아님이면 [지우기] 없음"은 고치기 전에도 통과 = 안전 확인.
- 로컬 비회원 화면: 콜 상세(운행 시간 켬) 출발 시간 창 — 값 없을 땐 [지우기] 없음 → "21시" 누름 → "21:00"과 창 아래 [지우기] → 누름 → "시간 선택"으로 비고 창 닫힘. 일지 지출 창 날짜는 잠긴 칸이라 그대로. 기사 초대 창은 비회원에 없어 계약 종료일은 배포본에서 확인. 시험 설정 원래대로, 저장한 자료 없음.
- push 뒤 CI verify·deploy 초록(run 41057126762). 배포본 seed 계정 기사 [+ 초대] 창(저장 안 하고 [취소]): 계약 종료일 값 없을 땐 [지우기] 없음 → 30일 고름 → 다시 열면 창 아래 [지우기] → 누름 → "날짜 선택"으로 비고 창 닫힘. 계약 시작일은 값이 있어도 [지우기] 없음. 기사 목록 그대로(초대된 기사 없음).
- §5 리뷰: 바뀐 파일 5개 = 건드릴 파일(공용 부품·CSS·테스트·기사 창·콜 상세 창), 새 CSS 클래스 `app-temporal-clear`는 새 단추에서만 씀(검색), 기존 클래스 안 바꿈, 새 주석은 속성 설명 1줄(지시서 문구 그대로), 새 저장 장치·타입 꼼수 없음, 기존 테스트 줄 삭제 없음, 모두 200줄 이하.
