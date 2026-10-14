# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 43-B — 나머지 금액·숫자 칸 10곳도 소수점 반올림 `[x]`

> **진행(2026-10-13):** 착수 승인. 10곳 교체 + `CurrencyInput` `clearable` 옵션. 줄 수: `CurrencyInput.jsx` 41, `CarFormModal.jsx` 164, `ClientTradeFields.jsx` 61, `ExpenseFormModal.jsx` 180, `FixedRouteClientModal.jsx` 112, `ReceivableItemCard.jsx` 127, `CallDetailForm.jsx` 200. 안 쓰게 된 불러오기 정리.
> 테스트 새 2개, `npm test` 864+375 통과(**기존 기대값 변경 없음**), `typecheck` 통과.
> 개발 서버 확인: 탭이 여전히 실제 "테스트" 계정 로그인 상태 → 지시서대로 저장하는 시험은 안 함. 정비 추가 창 비용 칸에 "12345.6" → 칸 "12,345.6", 나가면 "12,346" 확인 후 **취소**(저장 안 함).
> 코드 커밋 react-app `de839c5`. 보리 push → CI 초록(verify·deploy) → §5 리뷰 → 보리 최종 승인(2026-10-14).

43-A(`16a982b`)에 이어서. 보리 결정(2026-10-13): 소수점은 반올림해도 된다. 근거: `docs/roadmap.md` 43번.

### 쉬운 요약
- **무엇을:** 43-A에서 만든 금액 칸 부품(`CurrencyInput`)을 나머지 금액·숫자 칸 10곳에도 쓴다. 어디서든 "12345.6"을 치면 12,346이 된다(지금은 123,456 — 10배).
- **방식:** 칸마다 한 줄씩 부품으로 바꾸는 같은 작업의 반복이라 한 슬라이스로 한다(파일 6개, 기준 1~3개보다 많지만 파일마다 1~3줄 교체).
- **달라지는 점:** 소수점을 안 쓰면 모든 칸이 지금과 같다. 칸 모양(글자 크기·"원"/"km" 꼬리표·X 버튼 유무)도 그대로.

### 현재 상태 (코드 확인)
모두 글자를 칠 때마다 `formatCurrencyInput`·`parseCurrencyValue`로 숫자만 남겨 점이 사라지는 칸이다.

| # | 칸 | 위치 | 지금 칸 종류 | 폼에 넣는 값 |
|---|---|---|---|---|
| 1 | 차량 월급 | `cars/CarFormModal.jsx:119-125` | 일반 칸 | 숫자만 글자(`replace(/\D/g, '')`) |
| 2 | 거래처 정액 수수료(금액 선택 때만) | `clients/ClientTradeFields.jsx:43-51` | 일반 칸 | 쉼표 글자 |
| 3~5 | 정비·주유·기타 주행거리·비용·유가보조금 | `ExpenseFormModal.jsx:56-63·118-121·135-138` | 일반 칸 | **숫자**(`parseCurrencyValue`) |
| 6~7 | 고정노선 1회 단가·파렛트 단가 | `FixedRouteClientModal.jsx:80·93` | 일반 칸 | 쉼표 글자 |
| 8 | 미수금 부분 입금액 | `receivables/ReceivableItemCard.jsx:113-119` | **X 버튼 칸**(`ClearableInput`) | 입력 글자 그대로 넘김 |
| 9~10 | 콜 상세 출발·마감 계기판 | `day-log/CallDetailForm.jsx:163·170` | 일반 칸 | 쉼표 글자 |

- 43-A의 `CurrencyInput`은 일반 칸만 그린다. 8번은 X 버튼 칸이라 그대로 바꾸면 X 버튼이 없어진다.
- `CallDetailForm.jsx`는 200줄 — 이미 `CurrencyInput`을 불러오고 있어 줄 수가 늘지 않게 바꿀 수 있다.

### 목표 상태 (기대 동작)
1. 10곳 모두 "12345.6" → 칸엔 "12,345.6", 저장·계산 값은 12,346(칸에서 나가면 "12,346"). 계기판·주행거리(km)도 같은 규칙(반올림).
2. 폼에 넣는 값의 모양은 칸마다 지금과 같다 — 1번은 숫자만 글자, 3~5번은 숫자, 나머지는 쉼표 글자.
3. 8번은 X 버튼이 그대로 있다 → `CurrencyInput`에 `clearable` 옵션을 더해, 켜면 기존 `ClearableInput`으로 그린다(모양 그대로).
4. 2번은 수수료 종류가 "금액"일 때만 부품, "%"일 때는 지금 칸 그대로(소수 % 허용).
5. 소수점을 안 쓰면 10곳 모두 지금과 같다.

### 건드릴 파일
- `react-app/src/components/shared/CurrencyInput.jsx` — `clearable` 옵션(약 +4줄).
- `cars/CarFormModal.jsx`(164), `clients/ClientTradeFields.jsx`(59), `ExpenseFormModal.jsx`(182), `FixedRouteClientModal.jsx`(111), `receivables/ReceivableItemCard.jsx`(127), `day-log/CallDetailForm.jsx`(200) — 칸 교체, 안 쓰게 된 `formatCurrencyInput` 불러오기 정리.
- 테스트: `CurrencyInput.test.js`에 `clearable`(X 버튼 있음·누르면 비움) 1개, `ExpenseFormModal.test.js`에 "12345.6 → 비용 12346" 1개.
- **테스트 영향 미리 찾음(이번엔 칸 id·글자로 넓게 검색):** 이 칸들을 쓰는 테스트 — `ExpenseFormModal.test.js`, `FixedRouteClientLink.test.js`, `modalOutsideClick.test.js`, `App.clientsCars.test.js`, `App.guestDurable.test.js`, `App.test.js`, `hydrate.test.js`, `owner-state.persistRoundtrip.test.js`, `fuelRecords`·`maintenanceRecords`·`miscExpenseRecords.test.js`. 모두 정수 금액을 넣으므로 결과가 같아야 한다. **기대값이 바뀌어야 하는 테스트가 하나라도 나오면 멈추고 수정 착수지시서**(전체 테스트로 한 번에 확인 후).
- 공용 CSS 변경 없음.

### 안 건드릴 것
- `parseCurrencyValue`·`formatCurrencyInput` 자체(61곳) — 그대로.
- 퍼센트 칸(수수료 %·산재요율 등, `formatPercentInput`) — 이미 소수를 받는다.
- 43-C(큰 금액 경고) — 따로.
- 화면 표시용 `formatCurrencyInput`(목록 글자 등, `CarListItem.jsx`·`FixedRouteClientLink.jsx`) — 입력 칸이 아니라 그대로.
- 플레이북: 입력 칸 표시만, 폼에 넣는 값 모양은 그대로(목표 2) → 참고.

### 검증
- `npm test` 전체(위 테스트 목록 포함).
- 개발 서버: **확인 전 로그인 상태부터 본다**(43-A 사고). 비회원이 아니면 시험 입력하지 않고 보리에게 알림. 비회원이면 10곳에 "12345.6" 넣어 12,346 확인.

### 파일 길이
- `CurrencyInput.jsx` 36 → 약 40, `CallDetailForm.jsx` 200 → 200 이하 유지(불러오기 줄 정리), 나머지 파일은 같거나 1~3줄 차이
- 테스트 파일은 예외

### 실패 시 처리
- 새 저장 장치·재시도는 해당 없음(입력 칸만). 테스트·확인 결과가 목표와 다르면 멈추고 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법 (배포본)
1. 정비 추가 → 비용 "12345.6" → 저장 → 12,346원.
2. 앱 설정 → 고정노선 거래처 단가 "275000.4" → 275,000원.
3. 미수금 → 부분 입금 "1000.5" → 1,001원(X 버튼 그대로 있음).
4. 시험 입력은 지운다.
