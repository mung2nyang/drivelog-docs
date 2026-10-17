# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 82-C 입력이 잘못된 칸 표시 — 콜 상세·노선·입금·세금계산서·문자 문구·고정 노선 거래처 `[x]`

> **진행(2026-10-17)**: 보리 "착수지시서 확정, 작업 진행해"(확인할 점 1~4 지시서대로). 코드 커밋 react-app `c3abc58` — push 뒤 CI verify·deploy 초록(run 39022214769).
> 테스트: 새 테스트 13개(도메인·lib 4: `workData.test.js` 2·`practiceSettings.test.js` 1·`taxInvoiceActions.test.js` 1 / 화면 9: `CallDetailForm.test.js` 2·`TaxInvoiceDraftModal.test.js` 2·`useReceivablesActions.test.js` 1·새 `RoutePresetEditor.test.js` 2·`MessageSettingsPage.test.js` 1·`FixedRouteClientModal.test.js` 1) + 기존 `practiceSettings.test.js` 노선 문구 기대 1곳 변경. 고친 소스 17개를 되돌리면 새 테스트 13개 모두 실패(새 검사 함수를 부르는 도메인 테스트 파일 2개는 파일째 실패), 기존 테스트는 통과. `npm test` 948 + 470 통과, `npm run typecheck` 오류 0.
> AI 개발 서버(5174번 비회원 360px, 헤드리스 크롬 — 이 세션은 크롬 확장 도구가 꺼져 있어 직접 띄움): 노선 빈 추가 → 상차지 칸, 상차지만 → 하차지 칸·"하차지를 입력해 주세요.". 거래처 창 빈 저장 → 거래처명 칸·커서(공용 `focusField`). 고정 노선 거래처 빈 저장 → 단추 빨강·문구·커서, 고르면 사라짐. 콜 상세 빈 저장 → 상차지 칸·문구, 계기판 1000/900 → 마감 칸·"마감 계기판이 출발 계기판보다 작습니다."(반쪽 칸에서 낱말 단위 두 줄), 1100으로 고치면 사라지고 "운행거리 100km", 값 넣고 저장 → 저장됨. 부분 입금 빈 확인 → 입금액 칸·"입금액을 올바르게…", 400,000 → "남은 금액보다 큰…", 100,000 → "부분 입금을 등록했습니다.". 세금계산서 사업자등록번호 빈 저장 → 그 칸·문구, 채우고 저장 → "세금계산서 작성 내용을 저장했습니다.". 문자 문구 운행 완료 비우고 저장 → 그 칸만 빨강·"문구를 입력해 주세요.". 어두운 화면 #fc8181(고정 노선 거래처·세금계산서·문자 문구). 콘솔 오류 없음. 작성일자 빈 칸은 화면에서 비우기 어려워 테스트로 확인(단추 표시·커서). 새 프로필이라 실제 데이터 안 건드림.
> 줄 수: `CallDetailForm.jsx` 200→196 / `CallOdometerFields.jsx` 39(새) / `call-detail-form.css` 289→285(§6 예외 승인) / `call-details.js` 150 / `practicePresets.js` 152 / `payments.js` 167 / `taxInvoiceActions.js` 111 / `RoutePresetEditor.jsx` 68 / `useReceivablesActions.js` 160 / `ReceivableItemCard.jsx` 133 / `ReceivablesDetailPage.jsx` 106 / `TaxInvoiceDraftModal.jsx` 103 / `MessageSettingsPage.jsx` 119 / `FixedRouteClientModal.jsx` 121 / `AppDropdown.jsx` 136 / `app-dropdown.css` 138 / `ClientFormModal.jsx` 127 / `modal-form.css` 174.
> 리뷰 §5: 타입 꼼수 0건(diff grep — 새 테스트의 `unknown`도 `object`로 바꿈), 새 저장 장치 없음, 옛 문구 "상차지와 하차지를 모두"·"모든 문자 문구를"·`input-error` 코드 0곳(grep).
>
> **지시서와 다른 점 (보리 확인 — 2026-10-17 승인)**
> 1. **드롭다운 빨간 테두리는 `modal-form.css`가 아니라 `components/shared/app-dropdown.css`에 5줄** — 상자형 드롭다운의 커서 표시(파랑, `.app-dropdown-boxed .app-dropdown-trigger:focus-visible`)가 더 강한 규칙이라, `modal-form.css`에 두면 커서가 들어간 순간 파란 테두리로 덮임(82-A 규칙 "커서가 있어도 빨강"과 어긋남). 같은 파일 커서 규칙 바로 뒤에 둠. 상자형 드롭다운에만 해당, `aria-invalid`가 붙을 때만 바뀜(넘기는 곳 고정 노선 거래처 1곳).
> 2. **고정 노선 거래처·노선 등록 문구는 줄 전체 아래(왼쪽 맞춤)** — 두 곳 모두 "이름표|칸" 또는 "칸→칸 추가" 한 줄 배치라, 칸 바로 아래에 넣으려면 CSS(`app-settings.css`)를 더 고쳐야 함. 지시서 파일 목록 안에서 끝내려고 줄 아래에 둠. 바꾸길 원하면 수정 착수지시서로.
> 3. **`components/receivables/useReceivablesActions.js`에 `partialInputId` 내보내기 1개** — 부분 입금 칸 id를 카드와 함께 쓰려고(카드마다 다른 id).

로드맵 82번(⑤, 화면 모양) 세 번 중 마지막. 82-A(`6194b51`)·82-B(`857cb46`, 둘 다 `[x]` 2026-10-17)에서 만든 공용 칸 오류 모양(`shared/FieldError.jsx` `invalidProps`·`FieldError`·`focusField`, `modal-form.css` `.field-error`)을 남은 입력 화면 6곳에 붙이고, 콜 상세 계기판의 옛 빨간 테두리를 같은 모양으로 바꾼다. 82-B에서 미뤄 둔 거래처 창 커서 옮기기도 공용 `focusField`로 바꾼다. 모양·동작 규칙은 82-A·B 보리 결정과 같아서 새 시안은 없다.

### 쉬운 요약
- 지금은 콜 상세·노선 등록·부분 입금·세금계산서 작성·문자 문구·고정 노선 거래처에서 칸을 비우고 저장하면 아래 검은 알림만 뜬다. 콜 상세 마감 계기판은 출발보다 작으면 테두리만 빨개지고 이유 문구가 없다.
- 이제 차량·기사·지출 창처럼 문제 칸에 **빨간 테두리 + 칸 바로 아래 빨간 문구 + 커서**가 나온다. 고치기 시작하면 사라진다.
- 이번으로 82번이 끝난다. 저장 동작은 그대로다.

### 현재 상태 (코드 확인)
- **콜 상세**: 저장 버튼이 바로 `onSave`(`CallDetailForm.jsx` 197줄) → `DayLogPage.jsx` 116-117줄 `upsertCallDetail` 오류면 알림. 검사는 `domain/call-details.js` 41-43줄 "운임 또는 상·하차지 중 하나를 입력해 주세요." 한 가지.
- **계기판**: 마감 < 출발이면 마감 칸에 `.input-error`만(`CallDetailForm.jsx` 52·170줄, `call-detail-form.css` 286-288줄, 쓰는 곳 이 1곳뿐 — grep). 문구 없음, 저장은 그대로 되고 운행거리만 빈칸(`computeDistanceKm` 100줄).
- **노선 등록**(앱 설정): `RoutePresetEditor.jsx` 22-25줄 → `domain/practicePresets.js` 77줄 "상차지와 하차지를 모두 입력해 주세요." 알림. 두 칸에 `id` 없음. 10개 넘으면 추가 버튼이 이미 꺼짐(51줄).
- **부분 입금**(미수금 상세): `ReceivableItemCard.jsx` 113-119줄 칸(`id` 없음) → `useReceivablesActions.js` 117-120줄 → `domain/payments.js` 75-77줄 "입금액을 올바르게 입력해 주세요." / "남은 금액보다 큰 금액은 입력할 수 없습니다." 알림.
- **세금계산서 작성**: `TaxInvoiceDraftModal.jsx` 77줄 저장 → `TaxInvoiceTab.jsx` 96줄 → `lib/taxInvoiceActions.js` 36-43줄 "사업자등록번호를 입력해 주세요." / "작성일자를 입력해 주세요." 알림. 작성일자는 날짜 칸(`TemporalInput`, 82-B에서 빨간 테두리 받는 길 생김).
- **문자 문구**: `MessageSettingsPage.jsx` 32-35줄 네 칸 중 하나라도 비면 "모든 문자 문구를 입력해 주세요." 알림. 칸은 `textarea.message-settings-textarea`(`.input-box` 아님, `id` 없음).
- **고정 노선 거래처**: `FixedRouteClientModal.jsx` 56-60줄 거래처 안 고르면 "거래처를 선택해 주세요." 알림. 칸은 공용 `AppDropdown`(단추, 받는 값에 오류 표시 없음 — 17줄).
- **거래처 창** 커서 옮기기는 창 안에 직접(`ClientFormModal.jsx` 50-52줄), 공용 `focusField`와 같은 일.
- sot에 입력 오류 표시 규칙 없음(grep "계기판"·"입금액"·"빨간 테두리" 0건).

### 목표 상태 (기대 동작)
1. 아래 화면에서 저장(추가·확인)을 누를 때 문제 칸이 있으면 저장하지 않고: 그 칸 빨간 테두리 + 칸 아래 빨간 문구 + 그 칸으로 화면 이동 + 커서. 아래 알림은 안 띄움. 고치기 시작하면 사라지고, 다시 누르면 다시 검사. 한 번에 한 칸.
2. **콜 상세**: 운임·상차지·하차지가 모두 비었으면 **상차지 칸**(맨 위 칸)에 지금 문구 그대로. (셋 중 하나만 있으면 되는 조건이라 칸별로 못 나눔.)
3. **계기판**: 마감 < 출발이면 마감 칸에 공용 빨간 테두리 + 칸 아래 "마감 계기판이 출발 계기판보다 작습니다."(새 문구, 확인할 점 1). 지금처럼 입력하는 동안 바로 보이고, 저장은 막지 않음(지금과 같음). 커서는 옮기지 않음. 옛 `.input-error` 없앰.
4. **노선 등록**: 빈 칸 순서대로 상차지 → 하차지. 문구 나누기 "상차지를 입력해 주세요." / "하차지를 입력해 주세요."(82-B 문구 나누기와 같은 방식, 확인할 점 2).
5. **부분 입금**: 입금액 칸에 지금 두 문구 그대로(0·빈칸 / 남은 금액 초과).
6. **세금계산서 작성**: 사업자등록번호 → 작성일자(날짜 단추에 테두리·커서, 달력 안 열림). 문구 그대로. 사업자 정보 없음 같은 저장 뒤 검사(`taxInvoiceActions.js` 45줄 이후)는 지금처럼 알림.
7. **문자 문구**: 위에서부터 첫 빈 칸에 "문구를 입력해 주세요."(칸 제목이 바로 위에 있어서 짧게, 확인할 점 2).
8. **고정 노선 거래처**: 거래처 고르는 단추에 테두리·문구(그대로)·커서. 고르면 사라짐.
9. **거래처 창**: 커서 옮기기만 공용 `focusField`로(보이는 동작 같음).
10. 화면 읽기: 칸 "잘못됨"(`aria-invalid`), 아래 문구와 연결. 저장 함수 안 검사는 안전망으로 그대로.

### 건드릴 파일 (모두 `react-app/src/`)
- `domain/call-details.js` — `checkCallDetailDraft(draft)` 새로(문구+칸), `buildCallDetail`이 같은 검사를 씀
- `components/day-log/CallDetailForm.jsx` — 저장 전 검사·상차지 칸 표시. 계기판 두 칸은 새 `day-log/CallOdometerFields.jsx`로 옮김(아래 "파일 길이")
- `components/day-log/CallOdometerFields.jsx` **새 파일** — 출발·마감 계기판 칸, 운행거리, 마감 칸 오류 표시
- `components/day-log/call-detail-form.css` — `.input-box.input-error` 3줄 삭제만
- `domain/practicePresets.js` — `checkRoutePreset(load, unload)` 새로, `addFixedRoutePreset`이 같은 검사(문구 둘로)
- `components/RoutePresetEditor.jsx` — 두 칸 `id`·표시
- `domain/payments.js` — `checkPartialAmount(amount, remaining)` 새로, `addPartialPayment`가 같은 검사
- `components/receivables/useReceivablesActions.js`·`ReceivableItemCard.jsx`·`ReceivablesDetailPage.jsx` — 확인 전 검사, 칸 `id`(카드마다 다름)·표시, 값 전달 1줄
- `lib/taxInvoiceActions.js` — `checkTaxInvoiceDraft(item)` 새로, `saveTaxInvoiceDraft`가 같은 검사
- `components/TaxInvoiceDraftModal.jsx` — 저장 전 검사·두 칸 표시
- `components/MessageSettingsPage.jsx` — 네 칸 `id`·표시
- `components/FixedRouteClientModal.jsx` — 단추 표시
- `components/shared/AppDropdown.jsx` — 단추에 `id`·`aria-invalid`·`aria-describedby`를 받아 붙임(안 주면 지금과 같음)
- `components/clients/ClientFormModal.jsx` — 50-52줄 → `focusField(inputId)`
- `modal-form.css` — 빨간 테두리 대상에 `.app-dropdown-trigger[aria-invalid='true']`·`.message-settings-textarea[aria-invalid='true']` 두 줄
- 테스트: `domain/call-details`·`practicePresets`·`payments` 검사 결과, `lib/taxInvoiceActions` 검사, 화면 테스트(콜 상세·노선·부분 입금·세금계산서·문자 문구·고정 노선 거래처 — 빈 채 저장 → `aria-invalid`·문구·저장 안 불림·고치면 사라짐, 계기판 문구)
- 기존 테스트 기대 문구 바뀜: `domain/practiceSettings.test.js` 57줄 "상차지와 하차지를 모두…" 1곳(문구 나누기 때문, 검사 약화 아님 — 옛 문구 테스트 grep 결과 이곳뿐)
- **공용 영향**: `AppDropdown` 쓰는 6개 파일(`CalendarDateSelect`·`ClientFormModal`·`CustomerCenterPage`·`FixedRouteClientModal`·`ReportDetailView`·`OwnerRevenueView` — grep) — 새 값을 안 넘기면 그대로(추가만). `.app-dropdown-trigger`·`.message-settings-textarea`는 `aria-invalid`가 붙을 때만 바뀜. `.input-error` 쓰는 곳 1곳뿐(grep)이라 지우면 끝.

### 안 건드릴 것
- `DayLogPage.jsx`(233줄)·`TaxInvoiceTab.jsx`·`lib/practiceSettings.js` — 저장 흐름·안전망 알림 그대로
- 다른 기록과 겹치는 검사·저장 뒤 검사(노선 10개 넘음은 버튼이 이미 꺼짐, 세금계산서 사업자 정보 없음) — 지금처럼 알림
- 고객센터 문의·개인정보 등 82번 목록 밖 화면

### 검증
- 새 테스트: 검사 함수 4개가 칸·문구를 돌려줌, 각 화면 빈 채 저장 → 문제 칸 표시·저장 안 불림·고치면 사라짐. 고친 소스를 되돌리면 실패하는지 확인.
- 기존 테스트는 노선 문구 기대만 바꾸고 그대로 통과 — `npm test`, `npm run typecheck`. 옛 문구 "상차지와 하차지를 모두"·"모든 문자 문구를"·`input-error`가 코드에 0곳인지 grep.
- AI 개발 서버(비회원 360px, 밝은·어두운 화면): 콜 상세(빈 저장·계기판 거꾸로), 앱 설정 노선, 미수금 부분 입금, 세금계산서 작성, 문자 문구, 고정 노선 거래처, 거래처 창.

### 파일 길이
- `CallDetailForm.jsx` **200** → 계기판 칸(160-176줄, 약 17줄)을 `CallOdometerFields.jsx`(새, 약 40줄)로 옮기고 검사 추가 → 약 195. 같은 폴더에 `CallInsuranceFeeField`처럼 한 칸 묶음을 따로 둔 선례 있음.
- `call-detail-form.css` **289** → 286 — **§6 예외 요청**: 3줄 지우기만이라 나누지 않음.
- `domain/call-details.js` 141 → 약 150 / `practicePresets.js` 139 → 약 148 / `payments.js` 155 → 약 163 / `taxInvoiceActions.js` 103 → 약 113
- `RoutePresetEditor.jsx` 55 → 약 70 / `useReceivablesActions.js` 145 → 약 158 / `ReceivableItemCard.jsx` 127 → 약 134 / `ReceivablesDetailPage.jsx` 105 → 약 107
- `TaxInvoiceDraftModal.jsx` 81 → 약 100 / `MessageSettingsPage.jsx` 108 → 약 125 / `FixedRouteClientModal.jsx` 113 → 약 122 / `AppDropdown.jsx` 130 → 약 136 / `ClientFormModal.jsx` 129 → 약 127 / `modal-form.css` 173 → 175

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 화면 표시만 바뀌고 저장 함수 검사는 그대로 남음. 검증이 실패하면 코드는 그대로 두고 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법 (보리 휴대폰, 배포 뒤)
1. 일지 → 콜 추가 → 아무것도 안 넣고 저장 → 상차지 칸 빨강·아래 문구·자판.
2. 같은 창 출발 계기판 1000, 마감 900 → 마감 칸 빨강·"마감 계기판이 출발 계기판보다 작습니다.". 마감 1100으로 고치면 사라지고 "운행거리 100km".
3. 앱 설정 → 자주 다니는 노선 → 상차지만 넣고 추가 → 하차지 칸 빨강·"하차지를 입력해 주세요.".
4. 미수금 → 상세 → 부분 입금 → 비우고 확인 → 입금액 칸 빨강. 남은 금액보다 큰 금액 → 같은 칸에 "남은 금액보다 큰…".
5. 서류 → 세금계산서 → 작성 → 사업자등록번호 지우고 저장 → 그 칸 빨강.
6. 문자 문구 설정 → 입금 요청 문자 지우고 저장 → 그 칸 빨강·"문구를 입력해 주세요.".
7. 고정 노선 거래처 창 → 거래처 안 고르고 저장 → 거래처 단추 빨강·"거래처를 선택해 주세요.".
8. 각 화면 값 다 넣고 저장 → 지금처럼 저장·알림.

### 확인할 점 (보리)
1. **계기판**: 새 문구 "마감 계기판이 출발 계기판보다 작습니다."를 칸 아래에 띄우고, 저장은 지금처럼 막지 않음 — 이대로 괜찮은지(저장도 막을지).
2. **문구 나누기**: 노선 "상차지를 입력해 주세요." / "하차지를 입력해 주세요.", 문자 문구 "문구를 입력해 주세요." — 이대로 괜찮은지.
3. `CallOdometerFields.jsx` 새 파일(200줄 맞추기)과 `call-detail-form.css` 3줄 삭제 §6 예외.
4. 크기: 소스 17개 파일(82-B는 12개). 82번을 세 번으로 나눈 계획대로 한 번에 끝냄 — 더 나누지 않음(AGENTS §3 "3조각 이상 금지").
