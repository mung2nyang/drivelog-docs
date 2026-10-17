# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 82-B 입력이 잘못된 칸 표시 — 차량·기사·지출 창 `[x]`

> **진행(2026-10-17)**: 보리 "착수지시서 확정, 작업 진행해"(문구 나누기·`expenses.js` 예외 포함). 아래 "지시서와 다른 점" 세 가지 보리 결정(2026-10-17: 1번은 전체 차량을 넘겨 칸 표시, 2·3번 CSS 승인) 반영 뒤 코드 커밋 react-app `857cb46`. push 뒤 CI verify·deploy 초록(run 39013159737). 보리 최종 승인(2026-10-17).
> 테스트: 새 테스트 20개(도메인 7: `cars.test.js` 2·`drivers.assignment.test.js` 3·`expenses.test.js` 1 + 기존 `validateDriverLinkFields` 3개 기대 문구 변경 / 화면 12: `CarFormModal.test.js` 3·`DriverFormModal.test.js` 5(메인 차량 배정 포함)·`ExpenseFormModal.test.js` 2 + `directMutationActions.test.js` 71-1·71-2 기대 문구 변경). 고친 소스 9개를 되돌리면 새 테스트가 실패, 고친 뒤 통과(기사 창 "다 채우면 저장 1번"만 안전 확인 — 고치기 전에도 통과). `npm test` 944 + 461 통과, `npm run typecheck` 오류 0.
> AI 개발 서버(5174번 비회원 360px): 차량 창 차량번호 비우고 저장 → 빨간 테두리(#c53030)·아래 문구·커서·아래 알림 없음, 글자 넣으면 사라짐. 기사 차량 연락처 "010" → 연락처 칸, 월급제 금액 0 → 금액 상자 빨강·문구·커서. 정비 빈 채 저장 → 항목명 칸. 주유 비용 비움 → 비용 칸, 문구가 입력칸 바로 아래 줄(왼쪽 맞춤). 어두운 화면 #fc8181. 값 넣고 저장 → "내역을 등록했습니다." 그대로. 시험 기록(주유 1건·차량 88가8888)은 지움. 콘솔 오류 없음. **기사 초대 창은 로그인해야 열려 화면 확인 못 함** — 테스트로 확인(날짜 단추 표시·커서 포함).
> 줄 수: `cars.js` 193 / `drivers.js` 183 / `expenses.js` 203(§6 예외 승인) / `CarFormModal.jsx` 189 / `DriverFormModal.jsx` 139 / `DriverConnectionPage.jsx` 189→190 / `ExpenseFormModal.jsx` 198 / `FieldError.jsx` 45 / `TemporalInput.jsx` 160 / `modal-form.css` 173 / `expense-form.css` 117 / `requestDriverInviteSave.js` 140.
> 리뷰 §5: 타입 꼼수 0건(diff grep), 새 저장 장치 없음, 옛 두 문구 코드 0곳(grep).
>
> **지시서와 다른 점 (보리 결정 2026-10-17)**
> 1. **기사 창 "메인 차량 배정"은 칸 표시가 안 됨(목표 4 일부 미충족)** — 기사 창은 부모 화면에서 메인 차량을 뺀 차량 목록만 받음(`DriverConnectionPage.jsx` 35줄 `assignableCars`). 그래서 창 안 검사는 메인 차량을 알 수 없고, 지금처럼 저장 함수가 아래 알림으로 막음. → **보리 결정: 칸 표시로.** `DriverConnectionPage.jsx`가 전체 차량을 창에 넘김(`cars={cars}` 1줄), 창은 그 목록으로 검사(받는 값 1줄). 새 테스트 1개(고치기 전 실패 확인).
> 2. **`components/expense-form.css` 1곳 추가(건드릴 파일 목록 밖)** — 지출 창 비용 칸은 "이름표 | 입력칸" 두 칸 배치라, 문구를 그대로 두면 왼쪽 좁은 이름표 칸(84px)으로 들어감. 문구를 입력칸 아래 칸에 두는 4줄. → **승인.**
> 3. **`.field-error`에 낱말 단위 줄바꿈(`word-break: keep-all`) 1줄 추가** — 차량 창 반쪽 칸에서 "주세" / "요."로 낱말 중간이 끊겨서. 공용 이름이라 82-A 거래처 창 문구에도 같이 적용됨(거래처 창은 칸이 넓어 지금 모양 그대로, 좁아질 때만 낱말 단위로 넘어감). → **승인.**

로드맵 82번(⑤, 화면 모양) 세 번 중 두 번째. 82-A(`6194b51`, `[x]` 2026-10-17)에서 만든 공용 칸 오류 모양(`shared/FieldError.jsx`·`modal-form.css` `.field-error`)을 차량·기사·지출 창에 그대로 붙인다. 모양·동작 규칙은 82-A 보리 결정(직접 만들기·커서까지)과 같아서 새 시안은 없다 — 날짜 칸만 처음 붙인다(아래 "날짜 칸"). 82-C(콜 상세·노선·입금·세금계산서·문자 문구·고정 노선 거래처 + 계기판 정리)는 따로.

### 쉬운 요약
- 지금은 차량 등록·기사 초대·정비/주유/기타 지출 창에서 칸을 비우거나 잘못 넣고 저장하면 아래 검은 알림 한 줄만 뜬다.
- 이제 거래처 창처럼 문제 칸에 **빨간 테두리 + 칸 바로 아래 빨간 문구**가 나오고, 그 칸으로 화면이 옮겨져 커서가 들어간다. 고치기 시작하면 사라진다.
- 저장 동작은 그대로다. 문구는 그대로 두되, 여러 칸을 한 문장으로 묶어 말하던 두 문구만 칸별로 나눈다(보리 결정, 아래 "문구 나누기").

### 현재 상태 (코드 확인)
- 세 창 모두 저장 버튼이 부모 화면 저장 함수를 바로 부름(`CarFormModal.jsx` 158줄·`DriverFormModal.jsx` 95줄·`ExpenseFormModal.jsx` 166줄 `onClick={onSave}`), 칸 표시 없음(`aria-invalid` grep — `clients` 밖 0곳).
- **차량 창** 검사: `domain/cars.js` `upsertCar` 50·59·62줄 — 차량번호 없음 / 기사 연락처 10자리 미만 / 월급제 급여 0. 로그인·기사 차량·"기사 연동" 탭이면 `CarListPage.jsx` 95-97줄이 먼저 `validateDriverLinkFields`(`cars.js` 104-108줄, "기사명과 연락처를 확인해 주세요." — 이름·연락처 중 어느 쪽인지 안 가림).
- **기사 창** 검사: `domain/drivers.js` `upsertDriver` 68-86줄 — 이름 / 전화번호 / 초대 코드 / 메인 차량 배정 / 차량 있는데 시작일 없음 / 종료일이 시작일보다 앞. 로그인이면 `lib/requestDriverInviteSave.js` 77줄이 배정 차량·시작일 없음을 한 번 더 막음("기사 이름, 배정 차량, 시작일을 입력해 주세요."). 창은 `required` 값으로 로그인 여부를 이미 받음(`DriverConnectionPage.jsx` 182줄).
- **지출 창** 검사: `domain/expenses.js` `upsertExpense` 96-101줄 — 날짜 없음 / 주유는 비용 없음 / 정비·기타는 항목명과 비용 둘 다 없음. 창은 정비·주유 화면(`MaintFuelPage.jsx` 122줄)과 일지 지출(`day-log/useExpenseForm.js` 76줄) 두 곳이 같이 씀.
- **날짜 칸**(`shared/TemporalInput.jsx`)은 눌리는 단추에 오류 표시를 받는 길이 없음(받는 값 27줄: type·id·value·onChange·className·min·max·disabled·centered). 쓰는 곳 7개 파일.
- 82-A 커서 옮기기(`ClientFormModal.jsx` `handleSave`)는 창 안에 직접 씀 — 날짜 칸은 숨은 입력이 아니라 단추로 커서를 보내야 해서 공용으로 뺀다.
- sot에 입력 오류 표시 규칙 없음(grep "aria"·"빨간 테두리" 0건).

### 목표 상태 (기대 동작)
1. 세 창에서 저장을 누를 때 빠지거나 틀린 칸이 있으면 저장하지 않고: 그 칸 빨간 테두리 + 칸 아래 빨간 문구(느낌표 + 지금과 같은 문구) + 그 칸으로 화면 이동 + 커서(82-A와 같음).
2. 아래 검은 알림은 띄우지 않는다. 그 칸을 고치기 시작하면 표시가 사라지고, 다시 저장하면 다시 검사. 한 번에 한 칸, 지금 검사 순서대로.
3. **차량 창**: 차량번호 → (기사 차량) 기사 연락처 형식 → 월급제 급여 칸. 로그인·기사 연동 탭이면 기사명 → 연락처를 먼저(지금 `CarListPage` 순서).
4. **기사 창**: 기사 이름 → 전화번호 → 초대 코드(코드 칸) → 배정 차량(메인 차량·로그인인데 비어 있음) → 계약 시작일 → 계약 종료일.
5. **지출 창**: 주유는 비용 칸, 정비·기타는 항목명 칸. 날짜는 창에서 비울 방법이 없어(기본값 오늘, 일지에선 잠김) 표시하지 않고 지금처럼 알림(안전망).
6. 날짜 칸이 문제면 단추에 빨간 테두리, 커서는 단추로(자판 안 뜸, 달력은 안 열림).
7. 다른 기록과 겹치는 검사(같은 차량번호·메인 차량 이미 있음·쓰인 초대 코드·다른 기사에게 배정된 차량)와 저장 뒤 검사(`requestDriverInviteSave`의 등록 안 된 차량번호)는 지금처럼 알림.
8. 화면 읽기: 칸 "잘못됨"(`aria-invalid`), 아래 문구와 연결, 문구가 나오면 읽어 줌. 저장 흐름·저장 함수 안 검사는 그대로.
9. **문구 나누기**(보리 결정 2026-10-17) — 칸 아래와 안전망 알림 모두 새 문구:
   - 차량 창 "기사명과 연락처를 확인해 주세요." → 기사명 비었으면 "기사명을 입력해 주세요.", 연락처가 짧으면 "연락처 형식을 확인해 주세요."(같은 창에서 이미 쓰는 문구)
   - 기사 창 "기사 이름, 배정 차량, 시작일을 입력해 주세요." → 배정 차량 비었으면 "배정 차량을 입력해 주세요.", 시작일 비었으면 "계약 시작일을 선택해 주세요."
   - 그 밖의 문구는 그대로.

### 건드릴 파일 (모두 `react-app/src/`)
- `domain/cars.js` — `checkCarDraft(draft, { linkRequired })` 새로(문구+칸), `upsertCar`·`validateDriverLinkFields`가 같은 검사를 씀(문구 한 곳)
- `domain/drivers.js` — `checkDriverDraft(draft, { editingId, items, cars, required })` 새로, `upsertDriver`가 같은 검사를 씀
- `domain/expenses.js` — `checkExpenseDraft(draft)` 새로, `upsertExpense`가 같은 검사를 씀
- `components/cars/CarFormModal.jsx`·`components/DriverFormModal.jsx`·`components/ExpenseFormModal.jsx` — 저장 누르면 먼저 검사, 칸 표시·문구
- `components/shared/FieldError.jsx` — `focusField(inputId)` 추가(화면 이동·커서, 날짜 칸이면 단추로). 주석: `// 문제 칸으로 화면을 옮기고 커서를 넣는다 — 날짜 칸은 숨은 입력 대신 단추로.`
- `lib/requestDriverInviteSave.js` — 저장 뒤 안전망 검사 문구만 둘로(23줄 상수 → 배정 차량·시작일 각각, 78-80줄 조건 나눔), 동작 그대로
- `components/shared/TemporalInput.jsx` — 단추에 `aria-invalid`·`aria-describedby`를 받아 붙임(안 주면 지금과 같음)
- `modal-form.css` — 빨간 테두리 대상에 `.app-temporal-trigger[aria-invalid='true']` 한 줄
- 기존 테스트 기대 문구 바뀜(문구 나누기 때문, 검사 약화 아님): `domain/cars.test.js` 168·175·182줄(`validateDriverLinkFields` 3곳 → 각 칸 새 문구), `lib/directMutationActions.test.js` 739·747줄(→ "배정 차량을 입력해 주세요."·"계약 시작일을 선택해 주세요.")
- 테스트: `domain/cars.test.js`·`domain/expenses.test.js`·`domain/drivers.assignment.test.js`에 검사 결과, `components/ExpenseFormModal.test.js`에 칸 표시, 새 `components/cars/CarFormModal.test.js`·`components/DriverFormModal.test.js`
- **공용 영향**: `TemporalInput` 쓰는 7개 파일 — 새 값을 안 넘기면 그대로(추가만). `.app-temporal-trigger`는 `aria-invalid`가 붙을 때만 바뀜(`[aria-invalid]` 붙은 곳 0곳 grep). `FieldError.jsx`는 거래처 창이 쓰지만 기존 두 함수 안 바꿈.

### 안 건드릴 것
- 부모 화면(`CarListPage.jsx`·`DriverConnectionPage.jsx`·`MaintFuelPage.jsx`·`useExpenseForm.js`) — 저장 함수 검사는 안전망으로 그대로
- `lib/carInviteFromDraft.js` — 그대로. `lib/requestDriverInviteSave.js`는 문구만(위), 저장·서버 흐름 안 건드림
- `ClientFormModal.jsx` — 커서 옮기기를 공용 `focusField`로 바꾸는 건 82-C(이번엔 82-B만)
- 콜 상세 계기판 `.input-error`(82-C)

### 검증
- 새 테스트: 세 검사 함수가 칸마다 맞는 칸·문구를 돌려줌, 세 창에서 빈 채로 저장 → 문제 칸 `aria-invalid`·아래 문구·저장 함수 안 불림·고치면 사라짐, 날짜 칸 단추 표시. 고치기 전 코드에서 실패 확인.
- 기존 테스트는 위 5곳 기대 문구만 바꾸고 나머지 그대로 통과 — `npm test`. 옛 두 문구가 코드에 0곳인지 grep.
- AI 개발 서버(비회원 360px + 가능하면 로그인): 차량·기사·정비·주유·기타·일지 지출 창, 밝은·어두운 화면.

### 파일 길이
- `domain/cars.js` 177 → 약 195 / `domain/drivers.js` 171 → 약 190
- `domain/expenses.js` 194 → 약 202 — **§6 예외 요청**: 검사와 저장이 같은 문구를 나눠 쓰는 한 묶음이라 한 파일에 둠(82-A `clients.js` 246줄과 같은 이유, 250 안). 주석: `// 저장 전 검사와 저장이 같은 문구를 쓰려고 한 파일에 둔다.`
- `CarFormModal.jsx` 163 → 약 192 / `DriverFormModal.jsx` 100 → 약 135 / `ExpenseFormModal.jsx` 182 → 약 199
- `FieldError.jsx` 36 → 약 50 / `TemporalInput.jsx` 156 → 약 160 / `modal-form.css` 171 → 약 172 / `lib/requestDriverInviteSave.js` 138 → 약 140

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 화면 표시만 바뀌고 저장 함수 검사는 그대로 남음. 검증이 실패하면 코드는 그대로 두고 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법 (보리 휴대폰, 배포 뒤)
1. 차량 관리 → 차량 등록 → 차량번호 비우고 저장 → 차량번호 칸 빨간 테두리·아래 "차량번호를 입력해 주세요."·자판. 글자 넣으면 사라짐.
2. 기사 차량 등록 → 월급제 고르고 금액 비우고 저장 → 금액 칸 빨강·아래 문구.
3. 기사 연동 → 기사 초대 → 이름만 넣고 저장 → 전화번호 칸 빨강. 배정 차량 비우고 저장 → 차량 칸 아래 "배정 차량을 입력해 주세요.". 시작일 비우고 저장 → 시작일 칸 빨강·"계약 시작일을 선택해 주세요."(달력은 안 열림).
3-1. (로그인) 기사 차량 등록 → 기사 연동 탭 → 기사명 비우고 저장 → 기사명 칸 아래 "기사명을 입력해 주세요."
4. 정비·주유 → 주유 추가 → 비용 비우고 저장 → 비용 칸 빨강. 정비 추가 → 아무것도 안 넣고 저장 → 항목명 칸 빨강.
5. 각 창에서 값 다 넣고 저장 → 지금처럼 저장되고 "등록했습니다" 알림.

### 보리 결정 (2026-10-17)
1. 묶인 문구 두 개는 칸별 새 문구로 나눔(목표 9번).

### 확인할 점 (보리)
- `domain/expenses.js` 약 202줄 §6 예외.
