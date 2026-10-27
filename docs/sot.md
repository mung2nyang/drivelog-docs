# 단일진실원 (SoT)

> 이전 파일명: `handoff-2026-08-30.md`. 대상: `react-app` (구현) + `drivelog-docs` (원칙·계획).
> 갱신일: 2026-09-11. "Step 8 미착수" 등 오래된 오기 3곳 삭제(보리 승인, 실제로는 Step 0~11 로드맵 전체 완료 — 상세는 `docs/report.md`, 지난 스냅샷은 git 이력).
> 푸시는 별도 지시 전 하지 않는다. 신규 durable/fallback/unsafe/tombstone/큐 금지. (예외 하나: 로그인 일지 저장 실패 날짜 목록 — §4-18, 2026-10-23 보리 허락)

이 문서는 **도메인 읽기=Store** 와 화면이 **같은 계산 함수**를 쓰는지 한곳에 둔다.
다음 도메인·리팩토링 착수 전에 `AGENTS.md` §0-1을 읽고 4대 기준 보고 + 명시적 승인을 받는다.

---

## 0. 도메인 개념 — 차량·기사 관계 (보리 설명, 2026-09-09)

일지·매출·정산 관련 코드를 볼 때마다 헷갈리지 않도록, 차량과 기사의 관계를 먼저
정의해 둔다. **아래는 전부 실제 사업 구조를 반영한 것 — 코드 구현 편의로 지어낸
구분이 아니다.**

- **메인 차량**: 차주 본인 소유 차량. 차주 본인이 직접 운행하고 직접 일지를 쓴다.
- **서브차량(기사차량)**: 차주가 차량을 한 대 더 사서 기사를 고용해 그 차를 운행시킨다.
  기사 급여/수수료 정산 방식은 매출제 또는 월급제 중 하나.
  - **연동 서브차량**: 그 기사가 본인 계정으로 로그인해서 자기 운행일지를 직접 쓴다.
    차주는 "기사 관리" 화면에서 정산 요약(운송료·수수료·산재보험·최종 정산액)만
    조회한다. **거래처는 차주·기사 둘 다 등록·수정·삭제 가능**(하나의 레코드를
    공유, 저장은 차주 계정 기준). 2026-09-05 승인·완료.
    ⚠️ **2026-09-17 재검토 필요**: 원본은 연동 기사가 **차주와 완전히 독립된
    자기 계정 데이터**를 가졌다(거래처 포함, 고정노선도 독립적으로 1곳 가능).
    이 "레코드 공유" 결정이 그 독립성을 없애면서 고정노선 기능 손실 등 부작용을
    냈다 — 상세·후속 조치는 §4-4c, `docs/roadmap.md` "연동기사 연동해제 시
    운행일지 데이터 처리" 항목 참고.
  - **미연동 서브차량**: 기사 계정이 연동돼 있지 않다(로그인 계정이 없거나, 있어도
    안 씀). **차주가 그 기사를 대신해서 운행일지를 직접 입력**한다. 이유: 기사가
    종이에 손으로 쓰는 걸 선호하거나 앱 일지 작성 자체를 거부하는 경우가 있는데,
    그래도 차주는 현금흐름(매출·정산) 파악을 위해 그 차량의 운행 데이터를 앱에
    직접 넣어야 하기 때문.

**핵심**: "연동 여부"는 **누가 데이터를 입력하느냐**의 차이일 뿐이다. 그 차량에
**실제로 기사가 있고, 그 기사에게 정산(매출제/월급제)해야 한다는 사실은 연동·
미연동 둘 다 동일**하다 — 미연동 서브차량이라고 차주 본인이 모는 차가 되는 게
아니다. 그러므로 정산 요약·거래처 세금계산서 같은 "기사에게 지급"을 전제로 한
화면·계산은 미연동 서브차량에도 그대로 적용돼야 한다(데이터 입력 주체만 다를 뿐).

이 구분을 헷갈리면 안 되는 이유: react-app 재감사 중 "미연동 서브차량은 정산 개념이
없다"고 잘못 짐작했다가 보리가 바로 정정한 사례가 있음 — 코드 작성 전에 반드시
이 정의를 다시 확인할 것.

**데이터 분리**: 연동 기사·미연동 서브차량 각각의 진입점(`/app/drivers/:linkId`,
`/app/logs/:번호/manage`)은 거래처·운행일지·비용(정비/주유/기타)까지 전부 그
기사/차량 범위로 완전히 분리돼 있다. 다른 기사/차량, 차주 본인 데이터와 섞이지
않는다. (2026-09-09~10 "미연동 서브차량 데이터 분리 4단계" 완료로 확정 — 그 전엔
실제로 기사일지↔메인일지 데이터가 섞이는 버그가 있었음.)

**업무 흐름과 문서의 성격 (보리 설명, 2026-09-21):** 아래 순서로 돈과 서류가 흐른다.
1. 매일 운행일지에 콜상세·고정노선을 입력한다.
2. 차주가 **운송내역서**를 회사에 제출한다 — 기사가 이번 달에 벌어온 운송 내역을 근거로 차주가 회사에 "지급해 달라"고
   요청하고 확인·대조를 받는 **영수증**이다. 따라서 **기사 급여·기사차량 수수료 항목은 운송내역서에 넣지 않는다**
   (그 돈은 차주가 기사에게 지급하는 몫이라 회사에 보이는 서류에 들어가면 안 된다). 원본이 서브차량 내역서에서만
   기사차량 수수료를 차감한 것(`script.js:5018-5031`)은 이 원칙에 어긋나 채택하지 않았다.
3. 차주가 **세금계산서**를 나라(세무)에 제출한다(§4-5b — 앱은 작성 지원·상태 관리, 실제 발급은 홈택스).
4. **매출** 화면은 차주·기사가 **각자 본인 돈의 흐름**을 보는 화면이다(기사 급여·수수료·지출 포함).
5. 회사가 차주에게 운송료를 지급한다(미수금 관리).
6. 차주가 기사차량 수수료(월급/매출 몫)를 기사에게 지급한다.

**용어 변경 완료(2026-10-02, 2026-09-17 확정 방향을 이관 완료 후 적용):** 화면에 "할당기간"으로
표시되던 문구를 **"계약기간"으로 바꿨다.** "할당기간"이라는 표현이 모호하고
헷갈린다는 게 이유 — 실제로는 차주·기사 간 (근로)계약서상 계약 기간을 뜻한다. 바꾼 화면 문구:
기사 연동 화면의 "계약기간" 칸·위쪽 설명, 기사 초대·수정 창의 "계약 시작일/계약 종료일",
날짜 오류 안내("계약 종료일은 시작일 이후로 선택해 주세요."). 계산·저장 로직은 그대로.
**아직 "할당"으로 남은 말:** "할당 차량"·"할당 중" 상태 뱃지(이번 변경 범위 밖), 화면에 안 보이는
코드 주석·테스트 설명. **새 화면·기능에서도 "할당기간" 대신 "계약기간"을 쓸 것.**

**계약기간(옛 "할당기간") 개념은 연동 기사 전용이다.** 미연동 서브차량은 이 개념
자체가 없다(연동 계약 레코드가 없으므로). 오너 매출화면의 "기사" 드롭다운은
현재 차량 번호 기준이라, 한 차량에 기사가 월 중간 교체되면 기간 구분 없이
합산된다(드롭다운 재설계 계획은 `docs/roadmap.md` 참고).

**연동 기사 앱의 개인정보·설정 (보리 결정, 2026-10-01)** `[확인: 2026-10-01 보리]` — 로드맵 9-B-0에서 구현 예정:
- **개인정보 01 사업자 정보·03 정산 계좌 = 차주(사업주)가 입력한 값, 기사는 보기만(수정 불가).** 사업주 정보이고 세금계산서 작성에 쓰는 사업주의 입금 계좌다.
  기사 계좌는 이 앱이 다룰 일이 아니다(은행 앱이 아니고, 기사·차주 간 세금계산서는 세무사 일). 기사가 개인정보를 저장해도 사업자·계좌 칸은 기사 계정에 쓰지 않는다.
- **개인정보 02 대표자·연락처 = 기사 본인 것**(기사가 수정).
- **기사 앱 설정 = 기사 자기 것**(온보딩에서 정한 것과 그 뒤 바꾼 것). 연동해도 차주 설정을 빌려오지 않는다. 차주가 원하는 기록 항목은 배포 후 차주·기사 메신저로 요청.
- 연동 해제 후엔 일반 계정이므로 자기 사업자 정보·계좌를 직접 입력한다.
- 지금 코드(2026-10-01)는 사업자명만 차주 값이 보이고 나머지 칸은 비어 있으며, 설정은 차주 것을 빌려오고 저장 때 기사 계정에 차주 상호·설정이 덮어써진다 — 9-B-0에서 고침.
  시험 기사 계정에 이미 들어간 차주 상호는 시험 데이터라 그대로 둠.

---

## 1. 한 줄 상태

- **처음 보고된 장애(저장 막힘 / fuel 스키마 / 비용 증식 / 콜상세 증식): 닫힘.**
- **권장 도메인 SoT 완료:** 거래처, 비용, 차량, 설정, 계산서/일지 맵, 프로필, 기사.
- **정산 수수료 SoT (2026-08-31):** 홈 월간 정산 카드의 운임 수수료 = 매출 `getOwnerMonthlyFinanceDetail`.income.commission.total.
- **Step 7 (거래처/차량 슬라이스): 사용자 최종 승인 `[x]`.**
- **Fail-Fast 방향 (보리 승인, 2026-08-31):** 로그인 SoT = Supabase. 저장 실패는 큐가 아니라 지정 토스트 후 중단. 슬라이스 A(기사 초대 RPC) 구현·커밋. B~E는 미착수.

---

## 2. 오늘 작업 내용 (2026-08-30)

### 2-1. 원칙

- `AGENTS.md`에 **§0-1** 반영 (보리 명시적 승인).
  - 신규 복구 레이어 금지
  - 실패는 먼저 묻고 최소 코드
  - 리팩토링 착수 전 4대 기준 보고 + 승인

### 2-2. 거래처 단일 진실원 (승인 범위, 완료)

목표: 거래처 배열 **읽기 = Store**, **쓰기 = `requestClient*`만**.

| 파일 | 한 일 |
|---|---|
| `react-app/src/lib/clientMutations.js` | `requestClientSave` / `Reorder`에 더해 `requestClientFixedUnitPrice`, `requestClientTaxInfo` 추가. 세션 미준비·persist 실패 시 Store 유지 + 토스트. 신규 큐 없음. |
| `react-app/src/components/calendar/CalendarPage.jsx` | 단가 편집 → `saveClients` 제거, `requestClientFixedUnitPrice`만. `showToast` 연결. |
| `react-app/src/components/TaxInvoicePage.jsx` (+ Draft/Entry/Toolbar 분할) | `loadClients` 스냅샷 제거, `useOwnerClients`. 세무 저장은 `taxInvoiceActions` → `requestClientTaxInfo`. |
| `react-app/src/lib/ownerFinance.js` | `buildFinanceSettings.clients` = `readOwnerClients` (`loadClients` 제거). |
| `react-app/src/lib/taxInvoiceActions.js` | 계산서 초안 저장 시 거래처 세무를 `requestClientTaxInfo`로만. |

프로덕션 UI에서 `saveClients` 직접 호출은 없음. `lib/clients.js`의 `loadClients`/`saveClients`는 배럴로 남음 (테스트·레거시 경로).

### 2-3. 로그인 후 저장이 막히던 문제 (hydrate 스위치)

증상: `클라우드 동기화가 아직 준비되지 않았습니다.`

- `src/lib/hydrate.js` — `finishHydration(epoch)`: 성공/스테일 본인 슬롯 → `ready`, 에러/`finally`가 아직 hydrating이면 `failed`. 로그아웃 epoch `0`.
- `src/store/app-store.js` hydration에 `epoch`.
- `src/lib/hydrate.test.js` 보강.

### 2-4. `hydrate 스키마 위반: fuel`

서버 `fuel_records.raw`가 비용 객체 전체(kind, fuelType, payment 등)인데 persist fuel 스키마는 type/cost 등만 허용 → hydrate 전체가 실패해 `ready`가 안 됨.

- `src/store/persistDayRecordLegacy.js` — fuel/maint/misc coerce, extra 키 흡수.
- 관련 hydrate/persist 테스트 갱신 (extra 키는 throw가 아니라 흡수).

### 2-5. 매출 / 일일운행 탭 — 정비·주유·기타 무한 증식

원인: hydrate가 같은 비용 행을 `workData[date].fuelItems|maintItems|miscItems`와 `expenses[]` **양쪽**에 심음. 일지 저장이 `...prev`로 임베드를 남기고, 재hydrate/탭 이동 때 복제.

한 일 (신규 큐 없음):

- `src/lib/hydrateMergeWork.js` — 일지에 fuel/maint/misc **붙이지 않음**. daily_logs.raw에 있어도 삭제.
- `src/domain/day-record.js` `saveDayRecord` — 저장 시 임베드 키 제거.
- `src/domain/expenses.js` `dedupeExpensesById` — 같은 id 선두 1건.
- `mergeExpenseKind` / `loadExpenses` / `saveExpenses`에서 사용.
- **금지:** `readOwnerExpenses` / `useSyncExternalStore` getSnapshot 안에서 매번 새 배열 dedupe (렌더 루프).

### 2-6. 달력 날짜 클릭 — 운행 일지(콜상세) 무한 증식

원인: `daily_logs.raw.callDetails`와 `transport_details`가 겹치거나, 같은 id가 반복 insert. `syncWorkData`가 transport **delete 실패를 무시하고 insert**. 숫자 id는 persist 스키마에서 스킵·재부여될 수 있음.

한 일:

- `src/domain/callDetailIds.js` (신규, 순수 함수) — 숫자 id → 문자열, id 기준 중복 제거.
- `hydrateMergeWork.js` — daily raw의 `callDetails` 버리고 transport만. 병합 후 dedupe.
- `saveDayRecord` / `backfillCallDetailIds` — 저장·백필도 같은 규칙.
- `src/lib/syncWorkData.js` — `transport_details.delete` 에러면 throw (insert 안 함).
- 테스트: `hydrateMerge.test.js`, `workData.test.js`, `App.legacyCallInit.test.js`.

### 2-7. 오늘 검증

- 관련 단위 테스트(hydrate / hydrateMerge / workData / legacyCallInit 등) 통과.
- `tsc --noEmit` 통과.
- 보리: 브라우저 로그인 + F12, 육안 이상 없음.

에이전트가 브라우저로 본 시점에는 hydrate 배너가 떠 로컬 빈 일지만 확인했다. **실데이터 확인은 보리의 로그인 검증이 정본.**

---

## 3. 처음 보고 대비 — 남은 양

| 항목 | 남은가 |
|---|---|
| 거래처 저장 토스트로 막힘 | 없음 |
| fuel 스키마로 hydrate 실패 | 없음 |
| 탭마다 비용 배열 증식 | 없음 (로직 수정 + 사용자 육안) |
| 달력 클릭 시 콜상세 증식 | 없음 (로직 수정 + 사용자 육안) |
| 거래처 SoT (승인분) | 없음 |
| 비용 expenses SoT (승인분, 2026-08-31) | 없음 (코드·단위 테스트). 로컬 브라우저 확인은 보리 `npm run dev` |
| 차량 cars SoT (승인분, 2026-08-31) | 없음 (코드·단위 테스트). 로컬 브라우저 확인은 보리 `npm run dev` |
| 설정 settings SoT (승인분, 2026-08-31) | 없음 (코드·단위 테스트). 로컬 브라우저 확인은 보리 `npm run dev` |
| 계산서/일지 맵 SoT (승인분, 2026-08-31) | 없음 (코드·단위 테스트). 로컬 브라우저 확인은 보리 `npm run dev` |
| **다른 도메인 SoT** | **없음 — 프로필·기사까지 완료** |
| 기존 일지 durable/fallback/unsafe/tombstone | 걷어내지 않음 (§0-1: 기존 계약 유지, 신규만 금지) |

---

## 4. 2026-08-31 — 비용 expenses 단일 진실원 (승인 범위, 완료)

보리: 「단일진실원 다음거 진행하자」 → 핸드오프 권장 순서의 **비용**만 착수. 거래처와 같이 **읽기 = Store**, **쓰기는 기존 `saveExpenses` → `commitExpenses`**. `requestExpense*` / 신규 큐 **없음**.

| 기준 | 당시 | 한 일 |
|---|---|---|
| 1 subscribe vs load | `useExpenseForm`(일지)만 Store. 매출·정비/주유/기타·리포트는 `loadExpenses` 마운트 스냅샷. | 해당 화면 `useOwnerExpenses`. |
| 2 값의 자리 | Store + persist. UI 스냅샷이 한 박자 늦음. | 화면은 Store. |
| 3 request vs save | `saveExpenses` → `commitExpenses`. | 창구 유지. 새 mutation API 없음. |
| 4 충돌 승자 | 일지에서 넣은 비용이 매출 탭에 안 보이려면 스냅샷 탓. | 구독이면 리마운트 없이 반영. |

| 파일 | 한 일 |
|---|---|
| `react-app/src/components/revenue/OwnerRevenueView.jsx` | `useMemo(() => loadExpenses)` 제거, `useOwnerExpenses`. |
| `react-app/src/components/MaintFuelPage.jsx` | 목록 `useOwnerExpenses`. 저장·삭제는 `readOwnerExpenses` 직후 `saveExpenses`. persist try/catch 토스트, 실패 시 Store 유지. 로컬 `setItems` 없음. |
| `react-app/src/lib/report.js` | `buildMonthReport(..., expenses = readOwnerExpenses(ownerKey))`. 운행/설정/프로필/차량은 아직 `loadWorkData` / `loadPracticeSettings` / `loadProfile` / `loadCars` (이 도메인 밖). |
| `react-app/src/components/ReportPage.jsx` | `useOwnerExpenses` 후 `buildMonthReport`에 전달 (메모가 리마운트 없이 갱신). |
| `react-app/src/components/revenue/OwnerRevenueView.expensesSoT.test.js` | 마운트 후 `commitExpenses` 주유 12345 → 텍스트 `12,345`. `package.json` `test` 스크립트에 포함. |

**금지 유지:** `readOwnerExpenses` / `useSyncExternalStore` getSnapshot 안에서 `dedupe`로 매번 새 배열 만들지 말 것 (렌더 루프).

프로덕션 UI에서 `loadExpenses(` 호출 없음. 정의는 `lib/expenses.js` 배럴 + 주석·테스트 문구만.

검증: 위 컴포넌트 테스트 통과, `tsc --noEmit` 통과. **푸시하지 않음.** 화면은 보리가 `npm run dev`로 확인.

일지 `useExpenseForm`은 이미 Store였음. 회귀시키지 말 것.

---

## 4-2. 2026-08-31 — 차량 cars 단일 진실원 (승인 범위, 완료)

보리: 「차량 시작하자」. **읽기 = Store** (`useOwnerCars` / `readOwnerCars`). 쓰기는 기존 `requestVehicleSave` / `commitCars`. 신규 `requestCar*`·큐 **없음**. `CarListPage` / `MainPageRoute`는 이미 구독.

| 기준 | 당시 | 한 일 |
|---|---|---|
| 1 subscribe vs load | 목록은 Store. 기사 연결은 `useState(() => loadCars)`. `ownerFinance`/`report.js`는 `loadCars`. | 기사 연결·손익 조립·내역서 읽기 Store. |
| 2 값의 자리 | Store + persist. UI 스냅샷이 한 박자 늦음. | 화면은 Store. |
| 3 request vs save | `vehicleMutations` / `requestVehicleSave`. UI `saveCars` 없음. | 창구 유지. |
| 4 충돌 승자 | 차량 추가 후 기사 할당 목록·손익 차량 분해가 안 바뀌면 스냅샷 탓. | 구독이면 리마운트 없이 반영. |

| 파일 | 한 일 |
|---|---|
| `react-app/src/components/DriverConnectionPage.jsx` | `loadCars` 스냅샷 제거, `useOwnerCars`. |
| `react-app/src/lib/ownerFinance.js` | `buildFinanceSettings.cars` = `readOwnerCars`. |
| `react-app/src/lib/report.js` | `buildMonthReport`에 `cars = readOwnerCars` 인자. |
| `react-app/src/components/ReportPage.jsx` | `useOwnerCars` 후 `buildMonthReport`에 전달. |
| `OwnerRevenueView` / `DriverRevenueView` / `ReceivablesPage` / `TaxInvoicePage` | `useOwnerCars`로 `buildFinanceSettings` 메모 무효화 (거래처 `void clients`와 동일). |
| `DriverConnectionPage.carsSoT.test.js` | 초대 모달 연 뒤 `commitCars` 서브 `88나8800` → datalist option. `package.json` `test`에 포함. |

프로덕션 UI에서 `loadCars(` 호출 없음. 정의는 `lib/cars.js` 배럴 + 주석·테스트 문구만.

검증: 위 컴포넌트 테스트 + `tsc`. **푸시하지 않음.** 화면은 보리가 `npm run dev`로 확인.

---

## 4-3. 2026-08-31 — 설정 settings 단일 진실원 (승인 범위, 완료)

보리: 「설정시작하자」. **읽기 = Store** (`useOwnerSettings` / `readOwnerSettings`). 쓰기는 기존 `savePracticeSettings` → `commitSettings`. 신규 `requestSettings*`·큐 **없음**. 달력/메인은 이미 구독.

| 기준 | 당시 | 한 일 |
|---|---|---|
| 1 subscribe vs load | 달력/메인 Store. `AppSettingsPage` 마운트 `loadPracticeSettings`. `App.jsx` 테마 load. `ownerFinance`/`report.js` load. | 설정 화면·테마·손익 조립·내역서 Store. |
| 2 값의 자리 | 설정 페이지 local state가 persist와 어긋날 수 있음. | 화면은 Store. |
| 3 request vs save | `savePracticeSettings` 배럴. | 창구 유지. 패치 병합은 `readOwnerSettings`(persist `load*` 아님). |
| 4 충돌 승자 | 설정 바꾼 뒤 다른 탭 단가/테마가 안 바뀌면 스냅샷 탓. | 구독이면 리마운트 없이 반영. |

| 파일 | 한 일 |
|---|---|
| `react-app/src/store/ownerDataHooks.js` | `readOwnerSettings` 추가. **getSnapshot 안에서 normalize 금지 유지.** |
| `react-app/src/lib/practiceSettings.js` | `savePracticeSettings` 병합 소스를 `readOwnerSettings`. |
| `react-app/src/components/AppSettingsPage.jsx` | `useOwnerSettings`. 로컬 `setSettings` 없음. |
| `react-app/src/app/App.jsx` | 테마는 `useOwnerSettings().theme`. |
| `ownerFinance.js` / `report.js` / `ReportPage` | `readOwnerSettings` / 구독값 전달. |
| 매출·기사손익·미수·계산서 | `useOwnerSettings`로 `buildFinanceSettings` 메모 무효화. |
| `AppSettingsPage.settingsSoT.test.js` | `commitSettings` inputMode fare → 금액 버튼 `active-work`. |

프로덕션 UI에서 `loadPracticeSettings(` 호출 없음. 정의는 `lib/practiceSettings.js` 배럴 + 주석만.

검증: 위 테스트 + `tsc`. **푸시하지 않음.**

---

## 4-4. 2026-08-31 — 차량 id 중복 React key 경고

보리: 새로고침 후 `Encountered two children with the same key, car_1788141346245_c60pq4`. 원인: `mergeCarsFromRows`가 서버 행(raw.id)과 `supabaseId` 없는 로컬을 이어 붙여 같은 id가 두 번 들어감. 신규 큐 없음.

- `domain/cars.js` `dedupeCarsById` (선두 유지). **getSnapshot 안에서 쓰지 않음.**
- `hydrateMergeCars.js` 병합 결과 및 서버 행 없는 로컬 중복에도 적용.
- `loadCars` / `commitCars` / `initializeOwnerFromPersist` / `replaceOwnerState`에서 적용.
- `CarListPage` / `DriverFormModal` key: `id || number || index`.

---

## 4-4b. 2026-08-31 — 메인 달력 1회 단가 = 거래처 단가

보리: 메인 카드 하단 1회 단가는 임시(설정 `unitPrice`)였고 거래처 단가를 봐야 한다. 이어서 **정산 카드의 1회 단가 행·입력은 삭제**. 합산만 고정노선 `fixedUnitPrice`. 단가 편집은 거래처 화면.

## 4-4c. 2026-09-17 — 고정노선 "1곳 제한"은 계정별 독립 적용이 원칙 (정정, 버그 확인됨)

**정정 이력**: 이 절은 최초 작성 시(2026-09-17 오전) "차주 전체 거래처 목록 중
한 곳만 연결 가능(버그 아님)"이라고 적었으나, **범위를 잘못 적은 오류였다.**
같은 날 저녁 보리가 원본(vanilla) 실물 대조로 정정.

**원본(vanilla) 확인 결과(직접 배포 사이트 로그인 테스트 + 코드 대조)**:

| | 저장 방식 | 고정노선 |
|---|---|---|
| 차주 본인 거래처 | 차주 계정 데이터 | 있음, **그 계정 안에서** 1곳 |
| **연동 기사** 거래처 | **기사 자신의 독립 계정 데이터**(차주와 완전 별개 저장공간) | 있음, **그 기사 계정 안에서** 1곳 — 차주와 무관 |
| 미연동 기사 거래처 | 차주 계정 안에 `scopedToVehicleNumber` 태그로 저장(로그인이 없어 차주 데이터에 얹음) | **그 운행일지 안에서 1곳** `[확인: 2026-09-19 보리]` |

즉 "1곳 제한" 규칙 자체(하루 일지의 고정노선 운행횟수 칸이 숫자 하나뿐이라 두
거래처를 동시에 못 씀)는 원본에도 있고 지금도 맞다. **그런데 그 "1곳"은 원래
계정마다(차주 1곳, 각 연동 기사 계정마다 별도로 1곳) 독립적으로 적용되는
것이었다** — 원본은 애초에 계정별로 저장공간이 나뉘어 있어서 저절로 분리됐던 것.

**react-app에서 확인된 버그**: Step 9 슬라이스 C(2026-09-05, §0 참고)가 연동
기사 거래처를 "차주와 하나의 레코드 공유"로 만들면서, 원래 독립 계정이었던
연동 기사 데이터를 미연동과 같은 "차주 계정 안 태그" 방식으로 합쳐버렸다.
그 결과 `domain/clients.js:124-126`의 "1곳 제한"이 **차주+연동 기사를 통틀어
1곳**으로 잘못 작동한다(연동 기사가 자기 거래처 고정노선을 켜면 차주 메인
것이 꺼짐, 또는 그 반대). `LinkedDriverClientsPage.jsx`가 `hideFixedRoute`로
고정노선 토글 자체를 숨겨놔서 지금은 이 사고가 실제로 발생하진 않지만, 그 대신
**연동 기사가 원본처럼 고정노선을 아예 못 씀**(기능 손실).

**미연동도 고정노선을 사용할 수 있어야 한다** `[확인: 2026-09-19 보리]` — 위 표대로
"그 운행일지 안에서 1곳". 원본이 미연동에서 토글을 숨긴 것은 차주 것과 저장공간이
겹치기 때문이었다는 구현 사정(`driver-link.js:932-935` 주석)일 뿐, react-app이 이관하면서
그대로 숨겨야 한다는 설계 결정이 아니다.

**후속 조치**: `docs/roadmap.md`의 "연동기사 연동해제 시 운행일지 데이터 처리"
항목과 같은 뿌리(연동 기사=차주와 데이터 공유 구조 자체를 다시 보는 문제)이므로,
그 항목 작업 시 같이 풀 것 — 연동 기사 거래처(+고정노선 1곳)를 차주와 독립된
스코프로 분리하는 설계 포함.

## 4-4d. 2026-09-17 — 미수금은 콜상세만 다룸 (고정노선 제외, 의도된 설계)

미수금 관리는 **콜상세(건별 운송) 전용**이다. 이유: 콜상세는 거래처·플랫폼이 여러 곳일 수
있고 입금 여부를 예측할 수 없어 미수 추적이 필요하지만, 고정노선은 거래처와 차주가 1:1
계약하고 결제주기(월급날 개념)를 거래처 등록 시 함께 정하므로 입금 시점이 예측 가능해
기획 당시 미수 추적 대상에서 제외했다(보리, 기획 단계 결정).

**버그 아님.** 고정노선도 실제론 며칠 늦어지는 경우가 있을 수 있음(일반 월급도 그런 경우
있듯) — 이걸 미수 추적 대상에 넣을지는 **지금 결정하지 않고 추후 논의로 보류**(2026-09-17).

---

## 4-4e. 2026-09-21 — 고정노선 스코프 규칙 (호출부 전수 조사 + 수정 묶음 1·2 반영)

근거: `docs/scope-audit.md`(react-app 프로덕션 142줄을 실행 맥락별로 분류), 구현 `10cba09`(연동기사 정산)·`8c2bf61`·`c8939c2`
(미연동·차주 집계)·`1413e8e`(연동기사 본인 달력·매출)·`1c5c7b1`(운송비 내역서). 용어는 **"연동기사 본인"**으로 쓴다(옛 "직원기사"
정산 방식과 혼동 금지 — 코드의 계정 유형 이름은 `employed_driver`).

1. **읽는 쪽이 넘기는 스코프 키는 "그 화면이 다루는 거래처 소유 차량번호"다.** 차주 메인 = 스코프 없음, 차주가 서브차량을 볼 때(연동·미연동·게스트 포함) =
   그 차량번호(라우트 `logId`), 연동기사 본인 = 배정 차량번호(`clientScopeKey`, `MainPageRoute.jsx`). 구현: `domain/clients.js`
   `getFixedRouteClient(settings, scopeKey)`·`resolveFixedUnitPrice`.
2. **일지 키(`logId`)와 거래처 스코프 키는 별개다.** 연동기사 본인 세션은 일지를 `main`으로 쓰지만 거래처는 배정 차량 스코프다 — 그래서
   `main`을 스코프로 재사용하면 안 된다. 달력은 `clientScopeKey`를 받고(`CalendarPage`), 기사 본인 매출은 배정 차량번호를 메인 소스의 스코프 키로
   넘기며(`getOwnerMonthlyFinanceDetail`의 선택 인자), 내역서는 대상 차량이 서브차량일 때 그 번호를 쓴다(`buildMonthReport`의 `mainCar`).
3. **스코프 고정노선을 먼저 찾고, 없을 때만 차주 것(스코프 없음)으로 물러난다**(`clients.js:23-31`). 다만 연동기사 세션에는 차주의 스코프 없는 거래처가
   내려오지 않으므로(`hydrateEmployedDriver.js:152-163`) 물러날 대상이 없다 — **고정노선은 차주·기사 둘 다 설정할 수 있고, 같은 스코프 레코드를 공유한다**
   (차주가 "기사 관리 → 거래처"에서 저장하든 기사가 본인 거래처 화면에서 저장하든 결과가 같다, 보리 확인 2026-09-21).
4. **하위 계산 함수가 `unitPrice`·`fixedRouteClient`를 옵션으로 받으면 어떤 스코프를 쓸지는 그 값을 만드는 상위 호출부의 책임이다**
   (`monthSettlement.js`, `reportSummary.js`). 스코프 없이 부르는 새 호출부를 만들지 않는다.
5. **거래처 저장은 draft를 그대로 통과시키고 스코프를 보존한다**(`requestClientSave`→`upsertClient`, hydrate·persist가 `scopedToVehicleNumber`·`fixedRouteLinked`·
   두 단가를 보존). 단가 수정은 스코프 문자열로 다시 찾지 않고 화면이 고른 `clientId` 한 건을 고친다(`updateClientFixedUnitPrice` — 현재 프로덕션 호출자 없음,
   쓰게 될 때 호출 화면 증거를 다시 확인).
6. **게스트도 미연동 서브차량을 만들 수 있다**(기사 초대는 없음, §8) — 그 차량의 거래처·일지·달력·내역서에는 차량번호 스코프가 필요하다.
7. **운송내역서는 회사 제출용이라 기사차량 수수료·기사 급여를 넣지 않는다**(§0 "업무 흐름"). 스코프와 무관하게 수수료 계산은 이 원칙을 따른다.

미해결(범위 밖): "1곳 규칙"(같은 스코프에서 켜면 다른 거래처 자동 해제, `clients.js:137-142`)은 추후 삭제 예정이며 그때 이 규칙 3·5를 다시 본다 — `docs/roadmap.md`.

---

## 4-5. 2026-08-31 — 계산서 invoices + 일지 맵 workData (승인 범위, 완료)

보리: 「계산서/일지 맵시작하자」. **읽기 = Store**. 쓰기는 기존 `saveInvoices` / `persistWorkDataByLogId` → `saveWorkData`. 신규 `request*`·큐 **없음**. 달력 일지는 이미 `useOwnerWorkData`.

| 기준 | 당시 | 한 일 |
|---|---|---|
| 1 subscribe vs load | 계산서 `loadInvoices` 마운트. 손익·미수·계산서 일지 맵 `loadWorkDataByLogId`(persist). | `useOwnerInvoices` / `useOwnerWorkDataByLogId`. |
| 2 값의 자리 | 목록·맵 스냅샷이 일지 저장보다 한 박자 늦음. 모달 draft는 로컬 유지. | 목록·맵은 Store. |
| 3 request vs save | `saveInvoices`, `saveWorkData`. | 창구 유지. 계산서 쓰기는 `readOwnerInvoices` 직전 재읽기. |
| 4 충돌 승자 | 일지 저장 직후 열린 계산서/매출이 안 바뀌면 스냅샷 탓. | 구독이면 리마운트 없이 반영. |

| 파일 | 한 일 |
|---|---|
| `ownerDataHooks.js` | `readOwnerInvoices` / `useOwnerInvoices`. `useOwnerWorkDataByLogId`는 `main` 구독 후 `{ main }` 메모 (getSnapshot에서 새 객체 만들지 않음). |
| `ownerFinance.js` | `loadWorkDataByLogId` = `readOwnerWorkDataByLogId`. |
| `TaxInvoicePage.jsx` | 목록 Store. 일지 맵 구독. persist는 `saveInvoices`만. |
| `ReceivablesPage` / `OwnerRevenueView` / `DriverRevenueView` | `useOwnerWorkDataByLogId`. 미수는 persist 후 Store 갱신(로컬 setState 제거). |
| `report.js` / `ReportPage` | `readOwnerWorkData` / `useOwnerWorkData`. |
| `TaxInvoicePage.workInvoiceSoT.test.js` | 일지 횟수 4회, 발급 계산서 거래처명. |

맵 모양은 기존과 같이 **main만**. 서브 일지 persist 창구 확대는 이 범위 밖.

## 4-5b. 2026-09-17 — 세금계산서 화면 = 작성 지원 + 상태 관리 (실제 발급은 홈택스)

앱의 계산서 화면은 운행 기록에서 금액을 모아 계산서 작성 정보를 만들고, 발급 완료 여부 같은
처리 상태를 관리하며, Excel 파일을 내보내는 기능이다. **실제 세금계산서 발급(국세청 신고)은
앱이 하지 않고, 화면에서도 홈택스에서 직접 발급하도록 안내한다.**

관련 코드: `src/components/TaxInvoiceDraftModal.jsx`

## 4-5c. 2026-09-17 — 기사 계산서 기준 "공제 전/후": 실제 계산은 항상 공제후 (의도된 정리 중, 헷갈리기 쉬움)

**[2026-10-02 갱신] 기사 매입 계산서 자체를 삭제**(react-app `f10e794` — 세금계산서 [기사 매입]·[수수료 발행] 탭·계산·엑셀 뒤바꿈 삭제, 거래처 매출 계산서만).
지운 "계산서 처리 방식"(회사 정산·기사 직접 정산)에 기대던 갈래였다. 그래서 아래 "공제 전/후"·`gross` 분기도 함께 없어졌고,
`driverInvoiceBasis`는 **저장 검사에서 옛 저장분 읽기용으로 받아 주기만** 한다(빼면 옛 값 1건에 설정 전체가 거부됨). 아래는 그 전 경위.

**[2026-09-21 갱신] "공제 전/후" 선택 화면은 삭제 완료**(react-app `3bc860a` — `BillingSettingsPage`·
라우트 삭제). 아래는 삭제 전 경위이며, 지금 기사 매입 계산서 기준은 화면 선택 없이 **공제후 고정**이다.
스키마의 `driverInvoiceBasis`(예전에 `gross`를 저장한 계정의 hydrate를 깨지 않기 위해)와 도달할 수 없는
`gross` 분기(`financeTaxInvoiceGroups.js`·`TaxInvoicePage.jsx`)는 일부러 남겼다.

설정 화면엔 기사 계산서 기준을 "공제 전/후" 중에서 고르는 토글이 있었지만, 공통 계산 로직에
실제로 넘어가는 값은 **공제후로 고정**돼 있었다. 화면 선택이 계산에 반영 안 되는 것처럼
보였지만 **버그가 아니라 의도된 정리 과정**이었다.

이유: "공제 전/후" 개념은 원본(바닐라) 앱에 있던 것인데, 실제 현직 화물기사들은 "공제전"을
쓰지 않고 전부 "공제후"만 쓴다(보리 확인). 그래서 이관 과정에서 계산 로직을 먼저 공제후로
고정해 정리했고, **이관 완료 후 "공제 전" 선택지 자체를 화면에서 없앴다**(2026-09-21 완료). "원본에 있는 옵션 = 지금도 실제로 쓰는 개념"이 아닌
사례 중 하나이니 주의.

---

## 4-6. 2026-08-31 — 프로필 profile + 기사 drivers 단일 진실원 (승인 범위, 완료)

보리: 「프로필·기사 진행하자」. **읽기 = Store** (`useOwnerProfile` / `readOwnerProfile`, `useOwnerDrivers` / `readOwnerDrivers`). 쓰기는 기존 `saveProfile` / `saveDrivers` / `requestDriver*`. 신규 큐 **없음**.

| 기준 | 당시 | 한 일 |
|---|---|---|
| 1 subscribe vs load | 개인정보/마이페이지 `loadProfile`. 기사 연결 `loadDrivers` 마운트. 손익·내역서·알림 load. | 화면·조립 Store. |
| 2 값의 자리 | 스냅샷이 persist와 어긋날 수 있음. | 화면은 Store. 개인정보 로컬 setState 없음. |
| 3 request vs save | `saveProfile` / `saveDrivers` / `requestDriver*`. | 창구 유지. |
| 4 충돌 승자 | 개인정보 저장 후 마이페이지·내역서 상호가 안 바뀌면 스냅샷 탓. | 구독이면 리마운트 없이 반영. |

| 파일 | 한 일 |
|---|---|
| `ownerProfileDriversHooks.js` | raw만 getSnapshot. 빈 필드 병합은 훅 useMemo / `readOwnerProfile`(getSnapshot 금지). |
| `PersonalInfoPage` / `MyPage` | `useOwnerProfile`. |
| `DriverConnectionPage` | `useOwnerDrivers`. 저장 후 로컬 `setDrivers` 없음. |
| `ownerFinance` / `report` / `notifications` | `readOwnerProfile` / `readOwnerDrivers`. |
| 매출·기사손익·미수·계산서 | 프로필·기사 구독으로 `buildFinanceSettings` 메모 무효화. |
| `AppShell` | 기사 구독으로 초대 대기 알림 재계산. |
| `PersonalInfoPage.profileSoT.test.js` / `DriverConnectionPage.driversSoT.test.js` | `commitProfile` 상호·이름, `commitDrivers` 목록. |

프로덕션 UI에서 `loadProfile(` / `loadDrivers(` 호출 없음. 정의는 배럴 + 주석.

검증: 위 테스트 + `tsc`. **푸시하지 않음.** 화면은 보리가 `npm run dev`로 확인.

---

## 4-7. 2026-08-31 — 홈 월간 정산 카드 수수료 = 매출 SoT (승인, Step 7)

보리: 일지·매출에는 거래처 수수료가 보이는데 메인 정산 카드에는 안 보인다. **홈 전용 수수료 식을 새로 짜지 말고** 매출과 같은 함수를 쓴다.

| 기준 | 당시 | 한 일 |
|---|---|---|
| 1 subscribe vs load | 달력은 Store. 수수료는 카드가 안 읽음. | `buildFinanceSettings` + `useOwner*` (매출 `OwnerRevenueView`와 같음). `load*` 없음. |
| 2 값의 자리 | 매출: `getCallDetailCommissionAmount`(스냅샷 우선, 없으면 거래처). 홈: `monthWorkFareSummary`만. | 홈 `commissionTotal` = `getOwnerMonthlyFinanceDetail(..., 'owner', ...).income.commission.total` 한곳. |
| 3 request vs save | 표시만. | 쓰기 창구 없음. |
| 4 충돌 승자 | 일지 저장 후 홈이 안 바뀌면 스냅샷 탓. | 구독 + 동일 함수. |

| 파일 | 한 일 |
|---|---|
| `CalendarPage.jsx` | `monthKeyOf` + `getOwnerMonthlyFinanceDetail` owner 스코프. `monthWorkFareSummary`로 수수료를 다시 계산하지 않음. |
| `CalendarMonthSummary.jsx` | 「운임 수수료」행(`-금액`, 0이면 숨김). 합계 = `fareSummary.total - commission`. 공급가액은 차감 전. |
| `CalendarPage.test.js` | 홈 금액 = 매출 `income.commission.total`. 수수료 0이면 행 없음. |

차량 `calculateDriverVehicleCommission`은 이 행에 쓰지 않음(매출 「운임 수수료」는 거래처).

**추가(2026-10-13, 로드맵 39)** `[확인: 2026-10-13 보리]`: **고정노선 운행에도 그 거래처에 등록된 수수료를 뗀다.** 계산은 `domain/fixedRouteCommission.js` `getFixedRouteCommissionAmount` 하나 — 비율이면 그날 고정노선 금액에서 내림, 정액이면 그날 횟수만큼. 달력·홈(`monthSettlement.js`)과 매출(`financeOwnerDetail.js`, 연동 기사 본인 매출 포함)이 같이 쓴다. 파렛트에는 수수료를 안 뗀다. (react-app `50716d2`)

## 4-8. 2026-08-31 — hydrate producer가 persist 스키마만 남김

`mergeCarsFromRows` / `mergeClientsFromRows` / `mergeDriversFromRows`의 `...raw`/`...local` 제거. CAR/CLIENT/DRIVER_KEYS만. 불리언 5개(`insuranceOn` 등)는 raw에 진짜 boolean일 때만 키를 넣음(`shareRevenueWithOwner` 없음 = 공유). `hydrateMergeClients.js` 분리. 신규 큐 없음.

## 4-9. 2026-08-31 — 일지 인라인 시트 취소/저장 in-flow

`position: sticky; bottom`이 스크롤 포트에 조기 부착되어 버튼이 위쪽에 떴다. sticky/`fixed` 제거, 폼 맨 아래 문서 흐름. `scrollIntoView` / `InlineExpandHost` 없음.

**2026-10-08 변경(로드맵 20-A, 보리 결정)**: 칸이 화면 밖에서 열려 눌렀는지 모르는 문제 → `InlineSheet.jsx`만 펼쳐짐이 끝난 뒤·고르는 항목(`scrollKey`)이 바뀔 때 `scrollIntoView({ block: 'start' })` 허용. 버튼 문서 흐름·`useLayoutEffect` 금지·CSS 스크롤 없음은 그대로. `InlineExpandHost` 없음도 그대로.

## 4-10. 2026-08-31 — 테스트·지원 strict-inventory 중간점검

Step 8 전. `error TS\d+:` 테스트·지원 **384 → 314**(캡 355 이하). 전체 911→840, 프로덕션 527→526. `finance.fixtures.js` 타입, `normalizeSettings` `@returns`, `App.test.js` 기존 헬퍼 재사용. 프로덕션 런타임 분기 변경 없음(`CalendarPage` `!!paymentOn`은 optional boolean 정합).

## 4-11. 2026-09-11 — `InlineSheet` 0fr↔1fr 트릭의 구조적 한계: "이미 열린 채 내용만 교체"는 CSS만으론 애니메이션 불가

`day-log.css`의 `.inline-sheet` 슬라이드는 `grid-template-rows: 0fr ↔ 1fr`
**값 자체가 바뀔 때만** 전환(transition)이 걸린다(CSS 트랜지션은 값 변화가
있어야 발동, 렌더된 픽셀 크기 변화만으론 발동 안 함). 그래서 시트가
**계속 열려 있는 채로**(`open` prop이 true로 유지, 값은 내내 `1fr`) 안의
콘텐츠만 바뀌는 경우 — 예: 정비/주유/기타의 "종류 선택 패널 → 실제 입력
폼" 전환(`useExpenseForm.js` `openAdd`가 `kindPick`/`modalOpen`을 한
렌더에서 같이 바꿈) — 는 **애니메이션이 원천적으로 안 걸린다**(실측:
219px→511px가 25ms 안에 즉시 점프).

**해결하려면 유일한 방법**: 그 전환을 "잠깐 닫혔다가(`open`→false, 실제
`1fr→0fr` 전환 발동) 새 내용으로 다시 열리는(`open`→true)" 것으로
바꾸는 것뿐 — 이미 검증된 열기/닫기 사이클을 재사용하는 대신, 그 사이의
닫기+열기 시간(~0.4~0.5초)이 그대로 사용자 대기시간으로 추가된다.
**즉시 전환(지연 없음)과 슬라이드 둘 다는 이 기술로는 동시에 못 가진다
— 어느 한쪽을 반드시 포기해야 하는 구조적 트레이드오프다(버그 아님).**
이후 테스터가 "종류 선택 후 폼 뜨는 게 느리다"고 지적하면, 이 트레이드오프를
받아들이기로 한 결정인지(닫기+재열기 방식 적용됨) 아니면 즉시 전환 쪽을
택해 애니메이션을 포기하기로 한 결정인지 `docs/report.md`/`STATUS.md`
완료 기록에서 확인할 것 — 무작정 "버그 아니다"로 답하지 말고 어느
쪽을 택했는지부터 확인.

상세 조사·실측 근거: git 이력(2026-09-11 아코디언 슬라이드 커밋들, react-app
`67762ff`~`6c5ba8d`/`3ae7db9`).

---

## 4-12. 2026-09-27 — 산재보험료·3.3% 원천징수 규칙 (현직자 확정, 4-2 완료)

기사차량의 산재보험료·사업소득세(3.3%)는 **기사 정산액 기준 월 단위**로 계산한다(콜 상세 건별 입력이 아니다).
정본 함수는 `domain/driverIncomeDeductions.js`(순수 계산, react-app) — 계산식을 바꿀 일이 생기면 이 파일과 이 절만 같이 고친다.

- **기준 금액 = 기사 정산액**: 매출제는 그달 기사 몫(수수료 계산 결과), 월급제는 월급. 차량 전체 운송료가 아니다.
- **매출제 % 기사 몫의 기준 금액(로드맵 61, 2026-10-18 보리 결정)**: 차주가 기사 차량 창 "기사 몫 기준"에서 고른다 — "운송료 전체"(기본) / "거래처 수수료 뗀 뒤". 차량 `raw.driverShareBase`(`gross`/`afterClientCommission`, 없거나 모르는 값은 `gross`). 달력·매출 기사 급여·기사 관리·연동 기사 본인 매출이 모두 `domain/driverShareBase.js` 한 곳으로 같은 금액을 쓰고, 연동 기사에게는 `get_assigned_vehicle_summary`의 `driver_share_base` 칸(마이그레이션 `0024`)으로 전달.
- **월보수액** = 정산액 − 정산액 × 필요경비율.
- **필요경비율**(근로복지공단 고시, 기본값 2종 — 차주가 품목·차종 선택으로 채우거나 직접 수정 가능):
  **30.5%** = 컨테이너·시멘트(BCT 등)·철강재·위험물질(탱크로리)·자동차 수송(카캐리어)·곡물·사료·일반 화물차(카고·윙바디·탑차 등).
  **43.1%** = 살수차·카고크레인·렉카차(구난형 특수자동차).
- **산재보험료 총액** = 월보수액 × 산재보험료율(기본 1.8% — 고용노동부 고시, 매년 1월 1일 개정. 0이면 산재 적용 제외).
- **부담 비율**: 3.3% 사업소득자(월급제·매출제 모두) = 차주 50%·기사 50%(산재 적용 토글이 켜졌을 때만 기사 몫이 발생, 꺼지면 차주 100%).
  4대보험 근로자(드묾) = 차주 100%, 기사 공제 없음.
- **3.3% 원천징수** = 정산액 × 3.3%(**산재 공제 전** 금액 기준), 원천징수 토글이 켜졌을 때만.
- **기사 실수령** = 정산액 − 산재 기사 몫 − 3.3%. **차주 지출** = 기사 정산액 + 산재 차주 몫(기사 몫 산재·3.3%는 기사에게서 떼서 대신 내는 돈이라 차주 지출 아님).
- **차량 필드 4개**(기존 `insuranceOn`과 함께 `vehicles.raw`에 저장, 새 DB 칼럼 없음): `driverIncomeType`('employee'|'business', 두 토글 기본값만 결정 — 계산식 자체는 안 봄),
  `withholdingOn`(3.3% 토글), `expenseRate`·`insuranceRate`(문자열, 빈 값/이상값은 기본값 30.5·1.8로 되돌림). `insuranceOn`은 이제 "산재 기사 몫(50%) 차감 여부" 뜻.
  새 차량 폼 기본값은 사업소득자+두 토글 ON, 기존 차량은 미설정값이면 원천징수 꺼짐·사업소득자로 하위호환.
- **기사 본인 화면도 동일**: 서버 함수 `get_assigned_vehicle_summary`(마이그레이션 `0007`)가 위 4개 필드를 돌려주고, `driverSelfRevenue.js`가 차주 화면과 같은 함수를 쓴다.
- **콜 상세 "산재보험료"(건별 입력, A)는 이 규칙과 무관** — 차주·기사 사이 정산액에는 영향 없음. **1인 차주 본인 기록용**이다.
  - **추가(2026-10-13, 로드맵 40)** `[확인: 2026-10-13 보리]`: 이 칸의 금액은 플랫폼이 실제로 떼고 주는 돈으로 보고 **모든 화면 합계에서 뺀다**(하루 화면·달력·홈·요약·세부 운송비 내역서·매출·연동 기사 본인 매출, react-app `c07ff46`·`dfc2890`). 하루 합은 `domain/call-details.js` `callInsuranceTotal`. 기사차량 월 단위 산재(위 규칙)와는 별개.
  (예전엔 회사정산 기사차량의 매입 세금계산서 공급가액에도 쓰였으나, 2026-10-02 기사 매입 계산서 삭제로 그 쓰임은 없어짐 — §4-5c.)
  기사연동 계정(고용된 기사 본인 로그인) 화면에서는 이 입력칸을 숨긴다(자기 명의로 플랫폼 일을 받는 게 아니라서 의미가 없음).

**남은 것(미확인, 이번 범위 아님)**: "결제 및 수금 입력" 스위치가 꺼진 1인 차주는 여전히 이 칸을 못 본다(`docs/roadmap.md`에 등재).

구현: react-app `4c3375e`(슬라이스 1, 차주 쪽)·`c7de4f3`(슬라이스 2, 기사 본인+DB `0007`)·`e8cdc45`(슬라이스 3, 콜 상세 칸 숨김). DB `0006`(산재 적용 여부 전달)·`0007`(나머지 3개).

---

## 4-13. 2026-10-15 — 휴무일 콜은 모든 계산에서 제외 (보리 결정, 로드맵 75)

- 휴무로 표시한 날에 남아 있는 콜 상세는 **모든 화면의 합계·미수에서 뺀다** — 달력 정산·달력 미수 합계·빨간 점·미수금·홈·연체 알림·매출·기사 정산 합계·세금계산서·운송비 내역서 전부.
- 휴무로 바꿔도 콜은 지우지 않는다. 휴무를 끄면 다시 모든 합계에 들어간다. 고정 노선 횟수·파렛트는 휴무면 0.
- 콜이 있는 날을 휴무로 바꿀 때는 앱 확인 창으로 먼저 묻는다(`OffToggle.jsx` `confirmOffWithCalls`). 콜이 없거나 휴무를 끌 때는 묻지 않는다.
- 이전 결정 F-03(`docs/finance-parity.md`, 2026-09-21 "휴무일도 합산")을 바꾼 것이다. 원본 앱은 달력만 합산하고 미수금·매출·내역서는 뺐다(화면마다 달랐음) — 이 결정으로 원본과 다르게 전부 뺀다.
- react-app `7395b21`.

## 4-14. 2026-10-21 — 부가세 끝전은 버림 (보리 결정, 로드맵 102 `[x]` react-app `db5ea29`)

- 부가세 = 공급가 x 10%, **원 미만 버림**. 면세 운행은 0(그대로).
- 정본 함수 `domain/money.js` `vatOf` — 부가세는 모두 이 함수로(계산식을 바꿀 일이 생기면 이 함수와 이 절만 같이 고친다).
  쓰는 곳(6개 파일): `domain/monthSettlement.js`(달력), `domain/financeOwnerDetail.js`(매출), `domain/day-record.js`(하루 화면),
  `domain/financeTaxInvoiceGroups.js`(세금계산서), `features/documents/api/reportDetail.js`(운송비 내역서), `features/day-log/ui/CallDetailForm.jsx`(입력 창 미리보기).
- 버리는 단위: **운행 건마다**(고정 노선·파렛트는 날마다) 버린 뒤 더한다 `[확인: 2026-10-22 보리 — "건마다 버림으로"]`. 그래서 화면끼리 같은 값.
- 이미 저장된 세금계산서의 서버 세액(`tax_amount`)은 고치지 않았다 — 화면은 열 때마다 다시 계산한 값을 보이고, 다시 저장하면 새 값으로 바뀐다.
- 근거: 다른 운송·정산 앱(화물노트, 날라 TMS)은 버림. 비교는 `docs/external-calc-comparison.md` 3절 1번.

## 4-15. 2026-10-22 — 고객센터 문의 권한·관리자 (보리 결정, 로드맵 23-A `[x]` react-app `24afd74`·DB `0025`)

- 사용자(로그인 계정): 본인 문의 **넣기·읽기만**. 넣을 수 있는 칸은 `user_id·type·title·content·status` 5개, 답변 칸·고치기·지우기 불가. 비회원은 문의 못 함(§8-2 "비회원 문의").
- 관리자: 서버 명단 `admin_users`에 있는 계정만(지금 보리 계정 1개, 명단 넣기는 SQL Editor에서만) `[확인: 2026-10-22 보리 — "응 내계정으로 붙혀"]`. 앱 화면에서 숨기는 건 실수 방지일 뿐, 막는 건 서버.
- 관리자는 표를 직접 못 만지고 서버 함수로만: `is_admin()`·`admin_list_support_inquiries()`(모든 문의 + 문의한 사람 이름만)·`admin_answer_support_inquiry()`(답변·`status = answered`·`answered_at`만 바꿈).
- 관리자 화면: 마이페이지 아래 목록·옆 메뉴 "설정" 칸의 "고객센터" 다음 **"관리자"**(관리자 계정에만 보임) → `/app/admin/inquiries`, 관리자가 아니면 홈으로(로드맵 23-B `[x]` react-app `59c97c1`·`02feb28`) `[확인: 2026-10-22 보리]`. 답변은 문의마다 "답변하기"/"답변 수정".
- 문의 알림(앱 안, 로드맵 23-C `[x]` react-app `1b8981c`) `[확인: 2026-10-22 보리 — 권하는 대로]`: 로그인 계정은 앱을 켤 때·화면을 옮길 때 서버에 물어 알림 맨 뒤에 붙임(받은 값은 화면 메모리만, 서버 오류면 문의 알림만 빠짐). 사용자 — 답변 온 문의마다 "문의에 답변이 도착했습니다", id에 답변 시각이 들어가 [닫기] 뒤 답변이 수정되면 다시 뜸, 누르면 나의 문의 탭. 관리자 — "답변 대기 문의 N건" 한 줄, id에 가장 최근 대기 문의가 들어가 [닫기] 뒤 새 문의면 다시 뜸, 0건이면 없음. 알림 받기 스위치 `inquiryAnswer`(로그인 계정)·`adminInquiry`(관리자만). 앱이 꺼져 있을 때 휴대폰 푸시는 없음(로드맵 11번).
- 답변 문구 버튼(로드맵 23-D `[x]` react-app `ba46d25`) `[확인: 2026-10-22 보리]`: 관리자 답변 칸 위 인사·맺음·건의 감사·오류 사과 4개, 누르면 글 끝에 붙음(글이 있으면 줄바꿈), 2000자를 넘으면 안 넣음. 문구는 앱 코드(`features/support/ui/AnswerTemplateButtons.tsx`) — 바꾸려면 코드 수정.

## 4-16. 2026-10-22 — AI 비서(운비서) 서버 함수 (로드맵 24-A `[x]` react-app `bcad738`·`c378941`·`f96df4f`)

> 구조·파일 지도·새 종류 붙이는 순서·서버 함수 배포 절차·시험 방법은 `docs/unbiseo.md`(유지보수 안내). 이 절은 정한 동작의 정본.

- Supabase Edge Function `unbiseo-parse`(관리 화면 편집기로 올림, 저장소 `supabase/functions/unbiseo-parse/`와 같게 유지). 로그인 계정만 — 함수 안에서 `auth.getUser`로 확인("Verify JWT"는 끔 — 아래 xAI 목소리 줄). 비회원·공개 키만이면 401.
- 받는 것: `text`(1~300자)·`today`(YYYY-MM-DD)·`clientNames`(최대 200개, 각 50자). 돌려주는 것: `kind` = call(운행 — `date`·`loadLoc`·`unloadLoc`·`fare`(원 정수 또는 없음)·`client`)·expense(지출 — `date`·`type`(maint/fuel/misc, 모르면 maint)·`cost`(원 정수 또는 없음)·`name`·`category`(종류에 맞는 앱 목록 안에서만)·`fuelType`(주유일 때만)·`payment`(카드/현금일 때만), 로드맵 24-C-1 `[x]` react-app `79ec90a`)·question(질문 — 하루 기간 `day`(YYYY-MM-DD, 날짜 표에서, 앞날이면 오늘 — 로드맵 24-D3 `[x]` react-app `fe3ec4b`), `type`(revenue 매출 합계·netProfit 순이익·expense 지출 합계·unpaid 미수금·trips 운행 횟수·other 그 밖)·`period`(month YYYY-MM·year YYYY·all·unsupported(달보다 짧은 기간), 말 없으면 이번 달·미수금은 all, 앞날 달은 가장 가까운 지난 그 달·앞날 연은 올해)·`client`·`expenseType`(지출 질문일 때만), 로드맵 24-D-1 `[x]` react-app `7ac84aa` `[확인: 2026-10-23 보리 — 질문 목록·달/연만 권함대로]`. AI는 숫자를 안 다룸 — 계산은 앱)·fixed(고정 노선 — `date`·`count`(1~20, 말 안 하거나 밖이면 1)·`route`(앱이 보낸 등록 노선 이름 `routeNames`와 같을 때만, 띄어쓰기 무시), **말에 "고정"이 있을 때만** — 등록 노선과 같아도 "고정"이 없으면 call, 로드맵 24-F-1 `[x]` react-app `bdf7ea3` `[확인: 2026-10-23 보리]`)·unknown. 말 안 한 칸은 비움, 앞날 날짜는 오늘. 받는 것에 선택 칸 `routeNames`(최대 30개, 각 60자, 없으면 빈 목록).
- AI는 **xAI Grok `grok-4.20-0309-non-reasoning`(유료)** `[확인: 2026-10-23 보리 — 로드맵 24-A2, Gemini 무료 구간 503이 잦아 바꿈]`, 키는 함수 비밀값 `XAI_API_KEY`(보리만 다룸). AI 부르는 코드는 `grok.ts` 한 파일(표준 웹 요청, 답 모양 지정) — 회사를 바꿀 땐 그 파일과 비밀값만. 칸 모양은 모든 칸 필수·null 허용(말 안 한 칸은 비움), 앞날 날짜는 함수가 오늘로 바꿈(`index.ts`). (이전: Gemini `gemini-3.1-flash-lite` 무료 구간, 2026-10-22~23)
- **날짜는 AI가 계산하지 않음** — 함수가 오늘·어제·그제·3~6일 전·이번 주·지난주·"지난 ○요일" 표를 만들어 주고 AI는 고르기만. 거래처는 목록 이름으로 맞춤(줄임말·발음 포함), 없으면 들은 그대로.
- 저장·금액 계산 안 함(저장은 사람이 입력 창에서 [저장]). 함수 기록에 말한 문장·AI 답 내용을 남기지 않음(끝난 이유·쓴 양·걸린 시간만).
- 앱(로드맵 24-B-1 `[x]`): 홈 "Ai 운비서 / 운비서에게 무엇이든 물어보세요" 줄 `[확인: 2026-10-22 보리 — 이름·문구]`. 오른쪽 마이크(크롬 받아쓰기, 지원 안 하면 없음)를 한 번 누르면 카드가 열리며 듣고, 말이 끝나면 바로 보냄. 줄 나머지를 누르면 글로 쓰는 칸. 카드에 "내가 한 말: …"과 앱이 정한 틀의 문장(운행 "10월 21일 수원 → 화성, 150,000원으로 기록할게요." `[확인: 2026-10-22 보리]`, 지출·질문은 곧 지원). 로그인 계정만.
- 입력 창 채워 열기(로드맵 24-B-2 `[x]` react-app `d132321`) `[확인: 2026-10-22 보리 — 문구 "기록하기", "설정을 켤까요?"]`: 운행 결과 아래 [기록하기] → 그 날짜 일지(`/app/day/{날짜}`)로 가서 콜 상세 **새** 입력 창이 상차지·하차지·운송료·거래처로 채워진 채 열림, 저장은 사람이 [저장](지금 저장 그대로). 넘기는 값은 주소 상태에 담고 일지가 칸마다 모양 확인 뒤 바로 지움(새로고침·앞뒤로 가기로 다시 안 열림), 닫거나 저장하면 버림. "콜 상세"가 꺼진 계정은 "운행 세부 입력 기능이 꺼져 있어요. 켤까요?" + [켜기](앱 설정과 같은 값).
- 떠 있는 운비서(로드맵 24-B-3-B `[x]` react-app `5d922bf`·`2026b26`·`aef951d`) `[확인: 2026-10-23 보리 — 모양 "가"]`: 로그인 계정 + 홈이 아닌 앱 화면에서 아래 메뉴 위 오른쪽에 캐릭터 동그라미(52px)만. 누르면 위로 카드가 열리며 바로 듣기(받아쓰기 없으면 글 칸), 카드 머리 꺾쇠로 다시 작게(듣는 중이면 멈춤). 홈 카드와 같은 대화(앱 틀 한 곳, 휴대폰에 저장 안 함 — 다시 켜면 작게), [기록하기]면 작게 하고 일지로, 이미 그 날짜 일지면 새 입력 창(입력 중이면 확인 창 먼저). 떠 있는 동안 화면 아래 여백·알림 문구 +60px. 동그라미는 끌어서 옮기고 놓으면 가까운 왼쪽·오른쪽 가장자리에 붙음(높이는 아래 메뉴 위 ~ 화면 위 12px), 위치는 이 휴대폰에 저장(계정 데이터 아님), 카드는 위치와 상관없이 아래 메뉴 위에 펼침, 여백 +60px는 그대로(로드맵 108 `[x]` react-app `0de05e4`) `[확인: 2026-10-23 보리 — 방식 "나로", "권함대로"]`. + 버튼(관리 화면 + 추가·+ 초대, 48px)이 있는 화면에선 저장 위치와 상관없이 왼쪽(저장 값은 그대로, 그 화면에서 끌어 놓으면 높이·좌우는 놓은 자리로 저장)(react-app `35052fa`) `[확인: 2026-10-26 보리 — "+버튼이잇을때 운비서를 왼쪽 배치하자 그리고 +버튼 너무 커 좀만 줄여"]`. 앱 설정 "화면 표시" 다크 모드 아래 "운비서 버튼" 스위치로 끔(계정 설정 `unbiseoDockOn`, 기본 켜짐, 로그인 계정 메인 설정에만) — 끄면 이 동그라미만 안 그림(펼친 카드는 접고 듣기 멈춤), 홈 카드는 그대로, 다시 켜면 저장된 자리(react-app `14b89ff`·`e51831e`) `[확인: 2026-10-26 보리 — "1.2 가 3 켜짐", 이름 "운비서 버튼"]`.
- 지출 입력 창 채워 열기(로드맵 24-C-2 `[x]` react-app `b736571`) `[확인: 2026-10-23 보리]`: 지출 결과 문장 "{오늘|M월 D일} {항목}, {금액}원, {결제}로 기록할게요."(항목은 주유면 주유 종류, 정비·기타면 항목명 → 분류 → 종류 이름, 빈 칸은 뺌) + [기록하기] → 그 날짜 일지의 그 종류 지출 창이 금액·항목명·분류·주유 종류·결제로 채워진 채 열림(빈 칸은 빈 창 기본값), 저장은 지출 창 [저장] 그대로. 기간 밖·휴무 처리는 운행과 같음(휴무 문구 "지출을 기록할까요?"), 콜 상세 켜짐 확인은 운행만.
- 질문 답하기(로드맵 24-D-2 `[x]` react-app `daed064`·`a40a95d`) `[확인: 2026-10-23 보리]`: 숫자는 앱이 질문 받은 그 순간 값으로 화면과 같은 함수를 불러 냄(새 계산 없음, AI에 숫자 안 감), 문장은 앱 틀, 버튼 없음. 매출·순이익·지출·운행 횟수 = 그 계정이 매출 화면을 열면 처음 보이는 숫자(차주 "차주" 탭 `getOwnerMonthlyFinanceDetail(…, 'owner', …)` — 메인 차량, 연동 기사 `getDriverSelfMonthlyDetail` — 이름도 "운송 수입(정산 전)"·"정산액"), 연은 12달 합. 미수금 = 미수금 화면(`getReceivableItems` — 메인 + 기사 차량, 기사 차량분이 섞이면 "기사 차량 포함"), 거래처·달로 거름. 거래처 매출 = 서류 발급 [세금계산서] 탭 메인 서류의 그 거래처(`getTaxInvoiceSourceGroups(…, 'main')` 공급가액·부가세 포함 합계). 순이익·지출·운행 횟수에 거래처를 붙이면 "거래처별로는 매출과 미수금만", 여러 날(이번 주·지난주)은 "달이나 연으로", 목록 밖은 "아직 답할 수 없는 질문". **하루**(24-D3 `[확인: 2026-10-23 보리 — 권함대로]`): 운송료 = 일지 "운송료 합계"·달력 칸(`dayFareTotal` — 수수료 전, 메인 차량, 휴무 0, 문장 "{날짜} 운송료는 ○원입니다."), 운행 횟수 = `dayTripCount`, 지출 = 그날 메인 지출 합, 미수금 = 그 날짜 운행분, 순이익·거래처 하루 매출 → "달이나 연으로".
- 소리로 읽어 주기(로드맵 24-E `[x]` react-app `0ee92ea`) `[확인: 2026-10-23 보리]`: 마이크로 말해 보낸 답만 휴대폰 기본 읽어 주기(`speechSynthesis`, 한국어, 무료 — 문장을 밖으로 안 보냄)로 한 번 읽음, 글로 친 답은 안 읽음. 읽는 동안 [멈추기], 듣기 시작·새로 보내기·계정 바뀜이면 멈춤. 끄기 스위치·설정 저장 없음, 지원 안 하는 브라우저면 조용히 안 읽음.
- 고정 노선 기록(로드맵 24-F-2 `[x]` react-app `1bb79b0`) `[확인: 2026-10-23 보리 — [기록하기]는 "가"]`: 앱이 메인 일지 설정이 고정 노선·노선 칩 켜짐이면 등록 노선을 "상차지 → 하차지"(60자 넘는 것 뺌)로 `routeNames`에 보냄. 문장 "{날짜} {노선|고정 노선} {n}회로 기록할게요.", 버튼 자리는 기간 밖 → 고정 노선 꺼짐 [켜기](`fixedOn`) → 휴무 → [기록하기]. [기록하기] → 그 날짜 일지에서 한 번: 노선이 그날 칩과 같으면 칩 누름과 같은 `runRoute(id, n)`, 아니면 운행 횟수 +n(`patchDraft`) — 일지 자동 저장 그대로, 알림 "{노선|고정 노선} n회를 더했습니다.", [−]로 되돌림. 휴무면 "휴무 취소"로 왔을 때만 끄고 더함, 고정 노선 칸이 꺼진 일지면 안 더함.
- 거래처 등록 말 알아듣기(로드맵 24-G-1 `[x]` react-app `f826fdc`) `[확인: 2026-10-23 보리]`: 서버가 "거래처 등록·추가·새 거래처 …" 말을 `kind` = client로 — `name`(필수, 50자, 없으면 unknown)·`manager`(30자)·`phone`(숫자·하이픈만 남겨 숫자 9~13자리일 때만)·`paymentTerm`(앱 결제 주기 6가지)·`paymentTermValue`(N일 후 1~365, 익월·익익월 N일 1~31, 다른 주기면 빈 칸)·`commType`(percent 소수 허용 100 이하 / direct 원 정수, 종류·값 하나라도 틀리면 둘 다 빈 칸). 저장 안 함. AI 답 길이 상한 500.
- 거래처 등록 카드(로드맵 24-G-2 `[x]` react-app `4a04844`·`75861b5`) `[확인: 2026-10-23 보리 — 이미 있는 이름은 "가"]`: 문장 "{이름}{을/를} 거래처로 등록할게요. (담당자 …, 연락처(거래처 창 하이픈 형식), 결제 주기(거래처 창 이름), 수수료 n% / n원)", 들은 칸만 괄호 안에. 날짜가 필요한 주기인데 날짜가 없으면 문장·창 모두 주기를 뺌(창은 기본값 익월 말일). 버튼은 [등록하기] 하나(기간·휴무·설정 버튼 없음) → 떠 있는 카드 접고 `/app/clients`(차주·미연동 기사 `ClientListPage`, 연동 기사 차량 거래처 `useScopedClients`)의 **새** 등록 창을 채워 열기, 저장은 사람이 [저장](지금 검사·저장 그대로). 주소 상태는 읽고 바로 지움(뒤로가기·새로 고침으로 안 열림), 같은 화면에서 또 넘기면 새 값으로. 앱 거래처 목록 전체에 앞뒤 공백 뺀 같은 이름이 있으면 "{이름}{은/는} 이미 등록된 거래처예요."만, 버튼 없음.
- 미수금 문자 말 알아듣기(로드맵 24-H-1 `[x]` react-app `9edd988`·`3800371`) `[확인: 2026-10-23 보리 — "문자"가 있을 때만 "가", 서버 코드에서 막기]`: 서버가 `kind` = sms, `sms: { client, month, all, template }` — `client`는 운행 거래처와 같은 규칙(목록 이름 맞춤, 말 안 했으면 빈 값), `month`는 달 표의 YYYY-MM(앞으로 올 달·틀린 모양이면 빈 값), `all`은 "전체·전부·남은 거 다"(달이 있으면 거짓), `template`은 1~3("1번"·"첫 번째"·문구 이름 미수금 안내 1·입금 요청 2·운행 완료 3, 그 밖 null). **말에 "문자"가 없으면 AI가 문자라 해도 서버 코드가 unknown으로**("한진 미수금 얼마 남았어"는 미수금 질문 그대로). 저장·보내기 안 함.
- 미수금 문자 되묻기(로드맵 24-H-2 `[x]` react-app `4e0747c`) `[확인: 2026-10-23 보리 — 확인할 점 1·2·3 모두 "가"]`: 첫 문장 "{거래처}에 미수금 문자를 보낼게요."(거래처 없으면 "미수금 문자를 보낼게요.", 이 문장만 소리로). 숫자는 답을 받은 순간 미수금 화면과 같은 `getReceivableItems`·`groupByClientMonth`(남은 금액). 빠진 것만 차례로 버튼으로 물음: 거래처(남은 미수금 있는 거래처 "한진(3건, ○원)", 금액 큰 순서 — 없으면 "남은 미수금이 없어요.") → 어떤 건(달별 "7월분 전체(n건, ○원)" 오래된 달부터, 달이 둘 이상이면 "남은 전체", [직접 고르기] 체크 + [다음]; 말한 달·전체는 바로, 그 달에 없으면 알리고 물음) → 문구([미수금 안내]·[입금 요청]·[운행 완료], 이름은 `MESSAGE_TEMPLATE_TITLES` 하나로 일지 문자 창과 같이). 문구 값: `{거래처}` 이름, `{운송료}` 고른 건 남은 미수금 합계, `{운행구간}` 달 "7월분 3건"·전체 "전체 5건"·직접 1건 "10월 3일 평택 → 용인"·여러 건 "… 외 n건"(문구는 이 휴대폰의 바꾼 문구, 없으면 기본). 마지막 "{거래처}({연락처})에 이 문구로 보낼게요." + 문구 + [문자 열기](`buildSmsHref` — 문자 앱이 채워진 채 열림, [보내기]는 사람). 연락처는 앱 거래처 목록 같은 이름의 연락처, 없으면 "{거래처}에 등록된 연락처가 없어요. 거래처 화면에서 넣어 주세요."만. 고른 값은 화면 안에서만(저장 안 함), 새 답이면 처음부터. 거래처 없이 기록한 운행은 미수금 화면처럼 "미지정 거래처"로 나옴.
- 되묻기 버튼 말로 고르기(로드맵 24-H-3 `[x]` react-app `0d3403a`·`4074ddd`) `[확인: 2026-10-24 보리 — 확인할 점 1·2 "가", 문구 버튼 가로]`: 미수금 문자 카드가 버튼으로 묻는 중(거래처·어떤 건·문구 단계)이면 말·글을 서버로 보내기 전에 지금 보이는 버튼과 맞춰 봄 — 띄어쓰기·문장부호 빼고 말에 들어 있으면 맞음, 가장 길게 맞은 버튼이 딱 하나일 때만 누름(서버 안 부름, 글 칸 비움), 아니면 지금처럼 서버로(새 대화). 맞출 말: 거래처 이름 / 달 "7월분"·"7월"(다른 해는 "2025년 7월") / "전체·전부·남은 거 다"(남은 전체 버튼 없으면 하나뿐인 달) / "직접·고를게" / 문구 "1번·일번·첫 번째·미수금 안내"=1, "2번·두 번째·입금 요청"=2, "3번·세 번째·운행 완료"=3. 숫자 앞이 숫자면 안 맞음("11월"의 "1월"). 체크 목록·마지막 단계는 말로 안 고름, **[문자 열기]는 손으로만**. 마이크로 골랐을 때만 다음 질문을 소리로("어떤 문구로 보낼까요? 미수금 안내, 입금 요청, 운행 완료.", 마지막 "{거래처}에 이 문구로 보낼게요. 문자 열기를 눌러 주세요."). 문구 버튼 3개는 한 줄 가로(좁으면 줄바꿈), 다른 되묻기 버튼은 세로. 마이크는 매번 사람이 누름.
- 날씨 서버(로드맵 24-I-1 `[x]` react-app `48789b1`) `[확인: 2026-10-24 보리 — 확인할 점 1·2·3 모두 "가"]`: `unbiseo-parse`가 날씨 말을 `kind` = weather, `weather: { date, place }`로 — `date`는 날짜 표(날씨에만 내일·모레·글피 줄, 운행·지출의 앞날은 그대로 오늘)에서, 말 없으면 오늘, `place`는 들은 지역 그대로(30자, 말 안 했으면 빈 값 → 앱이 현재 위치). 새 함수 `unbiseo-weather`(로그인 계정만, Verify JWT 끔): 받는 것 `date` + `place` 또는 `nx`·`ny`(기상청 5km 격자 번호) 중 하나. **정확한 좌표는 서버로 보내지 않음** — 휴대폰이 격자 번호로 바꿔 그 번호만(로드맵의 "좌표는 휴대폰 → 날씨 서비스"를 바꿈, 기상청은 키 때문에 우리 함수를 거쳐야 해서). 지역은 함수 안 기상청 격자 표(시·도 16·시·군·구 256)에서 정식·줄인 이름으로 찾고, 다른 곳 둘 이상이면 못 찾음(noPlace). 오늘·내일·모레(한국 시각)만 — 지난 날은 past, 그 뒤는 tooFar. 기상청 단기예보(키 비밀값 `KMA_API_KEY`, 02시 발표, 02:15 전이면 전날 23시)에서 하늘(맑음·구름많음·흐림)·비눈·최저·최고(반올림)·가장 높은 강수 확률. 실패하면 503 "잠시 뒤 다시"(재시도 없음). 함수 기록은 끝난 이유·걸린 시간만(지역·격자·날씨 내용 없음). 저장 안 함. 상업 이용·위치 관련 절차는 앱이 무료인 동안 따지지 않음, 유료화 때 다시 봄 `[확인: 2026-10-24 보리]`.
- 날씨 카드(로드맵 24-I-2 `[x]` react-app `fd5489c`·`6d28860`) `[확인: 2026-10-24 보리 — 확인할 점 1·2·3 모두 "가"]`: 날짜가 오늘보다 앞이면 "지난 날씨는 알려 드리지 않아요.", 모레보다 뒤면 "모레까지만 알려 드릴 수 있어요."(위치도 서버도 안 씀). 지역을 말했으면 그 이름으로, 안 했으면 바로 휴대폰 위치(정확도 낮음, 10초, 10분 안 위치 다시 씀) → 기상청 격자 변환식으로 nx·ny만 보냄, 좌표 저장 안 함. 기다리는 동안 "날씨를 알아보는 중…"·입력 잠김. 문장 "{오늘|내일|모레} {지역|지금 있는 곳} 날씨는 {하늘}(비·눈이면 "흐리고 비"처럼), {최저}~{최고}도, 비 올 확률 {n}%예요."(없는 칸은 뺌), 못 찾은 지역 "{지역} 지역을 찾지 못했어요. 시, 군, 구 이름을 말해 주세요.", 위치 거절·시간 넘김·지원 안 함 모두 "위치를 허용하거나 지역을 말해 주세요.", 401 "로그인이 필요합니다.", 그 밖 실패 "잠시 뒤 다시 시도해 주세요.". 마이크로 물었으면 마지막 문장만 소리로. 버튼 없음.
- 점검표 말 알아듣기(로드맵 24-J-1 `[x]` react-app `c992cb7`) `[확인: 2026-10-24 보리 — 확인할 점 1~5 모두 "가"]`: `unbiseo-parse`가 일상점검표를 모두·다·전부 양호(이상 없음)로 처리하라는 말을 `kind` = inspection, `inspection: { date }`로 — `date`는 기존 날짜 칸(운행과 같은 규칙: 말 없으면 오늘, 앞날은 오늘). 일부 항목·불량을 말하면 unknown(AI 설명). **말에 "점검"이 없으면 AI가 inspection이라 해도 서버 코드가 unknown**("문자" 막기와 같은 방식). 저장 안 함.
- 점검표 카드(로드맵 24-J-2 `[x]` react-app `1b080ee`) `[확인: 2026-10-24 보리 — 확인할 점 1~5 모두 "가"]`: 묻는 그 순간 값으로 차례로 보고 처음 걸리는 것 하나만 — ① 메인 일지 `dailyInspectionOn` 꺼짐 "앱 설정에서 일상점검표 사용을 켜면 쓸 수 있어요." ② 연동 기사 계약 기간 밖 → 운행 카드 기간 안내 두 줄을 한 문장으로 ③ 휴무 "휴무 날은 일상점검표를 쓰지 않아요." ④ 메인 차량이 서버에 없음 "일상점검표를 저장할 차량이 없어요." ⑤ 그날 1장 읽기("점검표를 확인하는 중…", 입력 잠김) — 있으면 "{오늘|M월 D일} 일상점검표는 이미 저장되어 있어요."(덮어쓰지 않음), 읽기 실패 "잠시 뒤 다시 시도해 주세요.", 없으면 "… 모두 양호로 저장할게요." + [저장하기]. 모두 버튼 없음(⑤ 없음일 때만 버튼). [저장하기] = 일지 점검표 창과 같은 저장(`saveDailyInspection`, 11항목 양호, 조치 기록 빈 칸, 점검자 = 메인 차량 기사 이름 또는 내 이름, 메인 차량만), 저장 중 잠금, 성공 "… 모두 양호로 저장했어요."(버튼 사라짐, 소리로 안 읽음), 실패는 일지 창과 같은 알림·버튼 남음. 계정이 바뀌거나 다른 말을 보냈으면 결과 안 붙임. 홈 카드·떠 있는 운비서 같음.
- 점검표 지우기(로드맵 112 `[x]` react-app `ad2902d`·`f337971`) `[확인: 2026-10-25 보리 — 버튼 대신 "수정에서 비어도 저장 가능하게", 확인할 점 1·2 "가"]`: 따로 [삭제] 버튼 없음. **저장된 점검표를 연필로 연 수정에서 11항목이 모두 비었을 때만** [저장] = 지우기 — 확인 창 "점검표를 지울까요? / 모든 항목을 비우면 {M월 D일} 점검표가 삭제됩니다. 삭제한 점검표는 복구할 수 없습니다."([취소]·빨간 [지우기], 조치 기록 칸만 써도 항목 기준으로 빈 것). 지우면 창 닫힘·알림 "일상점검표를 지웠습니다."·안내 줄 "작성 필요 [+ 입력]". 일부만 고른 것·처음 입력에서 다 비운 것은 지금처럼 "모든 항목을 선택해 주세요.". 서버 `daily_inspections` 그 차량·날짜 줄을 지우고 지운 줄을 돌려받음(DB `0014` 삭제 정책 그대로) — 오류·0줄(권한 없음·이미 없음)은 실패 알림(0줄 "일상점검표를 지우지 못했습니다."), 창·상태 그대로. 저장과 같은 막기(로그인·쓰기 막힘 사유·세션 확인), 지우는 중 잠금. 휴대폰 보관·재시도·지운 흔적 없음. 홈 할 일·서류 한 달 표·운비서 카드는 열 때 다시 읽어 그대로 맞음. **앱 공용 확인 창 자리(`ConfirmDialogHost`)는 앱 틀 맨 끝** — 화면 안의 창(같은 `z-index` 1200) 위에 그려지게(`f337971`).
- 휴무 말 알아듣기(로드맵 24-K-1 `[x]` react-app `6a52421`) `[확인: 2026-10-25 보리 — 확인할 점 1~4 모두 "가"]`: `unbiseo-parse`가 휴무로 바꾸기·휴무 취소 말을 `kind` = dayOff, `dayOff: { date, off }`로 — `date`는 기존 날짜 칸, **앞날도 그대로**(날짜 모양만 검사, 틀리거나 없으면 오늘; 운행·지출의 "앞날은 오늘"과 다름), `off`는 휴무로 바꾸면 true·취소면 false(칸 `dayOffCancel`). **말에 "휴무"나 "쉬"가 없으면 서버 코드가 unknown.** 저장 안 함. 서버 AI 답 읽기는 `answer.ts`(요청 받기·로그인은 `index.ts`)로 나눔.
- 휴무 카드(로드맵 24-K-2 `[x]` react-app `7dec724`) `[확인: 2026-10-25 보리 — 확인할 점 1~4 모두 "가"]`: 묻는 그 순간 값으로 — ① 연동 기사 계약 기간 밖 → 운행 카드와 같은 기간 안내 ② 휴무로인데 이미 휴무 "{날짜}은 이미 휴무예요." ③ 취소인데 휴무 아님 "{날짜}은 휴무가 아니에요." ④ 그 밖 "{날짜}을 휴무로 할게요." + [휴무로 하기] / "{날짜} 휴무를 취소할게요." + [휴무 취소하기](①~③은 버튼 없음, 날짜 말은 "오늘" 또는 "M월 D일"). 버튼 → [기록하기]처럼 카드 접고 그 날짜 일지로(주소 상태 `assistantDayOff: { off }`) → 일지가 한 번만 적용: 휴무로는 콜이 있으면 휴무 단추와 같은 확인 창(`confirmOffWithCalls`, [취소]면 그대로) 뒤 `isOff: true, fixedCount: 0`, 취소는 `isOff: false`(고정 횟수 그대로), 바뀌면 알림 "{M월 D일}을 휴무로 바꿨습니다." / "{M월 D일} 휴무를 취소했습니다.", 이미 그 상태면 그대로. 저장은 일지 평소 저장(새 저장 길 없음). 확인 창을 기다리는 사이 일지가 닫히면 바꾸지 않음. 앱 칸 읽기는 `unbiseoFields.ts`로 나눔.
- xAI 목소리(로드맵 24-E2 `[x]` react-app `f05f477`) `[확인: 2026-10-23 보리]`: 읽어 주기(24-E)의 목소리를 먼저 Supabase 함수 `unbiseo-voice`(xAI 음성 `POST /v1/tts`, `voice_id` `ara`(2026-10-26 보리 지시로 `iris`에서 바꿈), `ko`, mp3 24kHz 32kbps, 받는 것 `text` 1~300자)로 받아 틀고, 5초 안에 못 받거나 실패하면 휴대폰 기본 목소리. 받은 소리는 저장 안 함. 함수 기록은 글자 수·시간·성공·크기만(비용 = 글자 수 합 × $4.20/100만, 베타 가격). **두 함수(`unbiseo-voice`·`unbiseo-parse`) 모두 "Verify JWT with legacy secret" 끔** `[확인: 2026-10-23 보리]` — 켜면 지금 로그인 표(ES256)를 문 앞에서 거절(새로 만들거나 다시 배포하면 켜진 채 막힘 — 24-D3 배포 뒤 `unbiseo-parse`도 막혀 운비서 전체가 401). 로그인 확인은 함수 코드 `auth.getUser`(공개 키만이면 401). **함수를 만들거나 다시 배포할 때마다 이 설정이 꺼져 있는지 확인.**
- 버튼 자리 순서(로드맵 24-B-3-A `[x]` react-app `d7e750e`) `[확인: 2026-10-23 보리]`: ① 연동 기사이고 그 날짜가 계약 기간 밖 → 일지와 같은 기간 안내(`domain/drivers.ts` `assignmentPeriodNotice`), 버튼 없음 ② 콜 상세 꺼짐 → [켜기] ③ 그날 휴무 → "휴무를 취소하고 운행을 기록할까요?" + [휴무 취소하고 기록](누르는 순간 휴무 끄기 — 휴무 단추와 같은 저장, 닫아도 꺼진 채) ④ [기록하기].
- 무료 구간은 불안정(503·1~12초) — 앱은 "잠시 뒤 다시 시도해 주세요"를 보임, 재시도 장치 없음. 너무 안 되면 AI 회사 바꾸기 검토, 하루 사용 횟수 제한은 출시 직전(로드맵 11번).

## 4-17. 2026-10-23 — 하루 금액에 파렛트 포함 (보리 결정, 로드맵 109-A `[x]` react-app `f6488ba`)

- 하루 금액(달력 칸·홈 오늘 카드) = 고정 노선 횟수 × 단가 + 파렛트 장수 × 파렛트 단가 + 세부 운송료 `[확인: 2026-10-23 보리 — "파렛트 입력하면 당연히 금액을 포함해야지"]`. 수수료·부가세는 하루 금액에 안 넣음(그대로).
- 파렛트는 달 정산 합계와 같은 조건(고정 노선 켜짐 + 그 거래처 파렛트 켜짐)일 때만. 정본 함수 `domain/calendarBadges.ts` `dayFareTotal`, 파렛트 단가는 `data/store/useMonthSettlement.ts`가 조건을 보고 넘김(꺼졌으면 0).
- 횟수 입력 계정 달력 칸은 "N회" 그대로 — 파렛트는 운행 횟수가 아니라 횟수에 안 셈.
- 세금계산서(서류 발급 세금계산서 탭·운비서 거래처 매출 답)도 고정 노선 거래처 계산서에 파렛트 금액 포함 — 달력·매출과 같은 조건(그 차량 고정 노선 켜짐 + 거래처 파렛트 켜짐), 부가세는 고정 노선·파렛트 날마다 따로 버림, 건수는 안 셈(로드맵 113-A `[x]` react-app `bdd5565`) `[확인: 2026-10-26 보리 — "넣어야지 당연히"]`. 파렛트 금액에는 거래처 수수료를 안 뗀다 `[확인: 2026-10-26 보리 — "파렛트는 수수료를 안때"]`.
- 운송비 내역서의 파렛트(날짜 칸·월 합계)도 그 일지의 고정 노선 스위치 — 메인 일지(연동 기사 본인 포함)는 `fixedOn`, 차주가 보는 기사 차량은 `subFixedOn`, 그 차 달력과 같음(로드맵 113-B `[x]` react-app `cfa3231`) `[확인: 2026-10-26 보리 — "고쳐야지"]`.
- 기사 관리 "거래처 세금계산서" 카드에도 고정 노선(파렛트 포함)을 그 차량 고정 노선 거래처 카드에 넣는다 — 서류 발급 세금계산서와 같은 규칙(건수는 고정 횟수만, 부가세 고정·파렛트 날마다 따로, 고정 노선 거래처가 없으면 빠짐), 카드 줄은 "MM/DD 고정 노선 N회(+ 파렛트)"·파렛트만이면 "MM/DD 파렛트"(로드맵 113-C `[x]` react-app `a7dfe85`) `[확인: 2026-10-26 보리 — "고정노선도 넣어야지"]`.
- 운송비 내역서 월간 정산에도 "파렛트 회수 청구액" 줄 — 달력과 같은 자리(거래처 줄 다음·산재보험료 앞)·이름, 0원이면 없음, 계·부가세는 원래 파렛트 포함(로드맵 113-D `[x]` react-app `8abf4db`) `[확인: 2026-10-26 보리 — "응 그것도 고쳐"]`.
- 운행일지 하루 화면(로드맵 109-B `[x]` react-app `34e5f56`·`455e77d`) `[확인: 2026-10-23 보리 — 시안 "다", 문구 "운송료 합계", 저장 표시 아이콘]`: 제목 바로 아래 "운송료 합계 금액"(같은 계산, 입력 중 값 기준), 0원·휴무면 글만 빼고 자리는 남김(아래 칸 안 밀림). 자동 저장 표시는 제목 오른쪽 작은 아이콘(저장 중 도는 원·완료 체크 원·실패 빨간 느낌표 원, 화면 읽기용 글 있음). 일지 저장 실패 알림 문구는 앞에 "M월 D일 운행일지 "(서버·휴대폰 저장 실패만). "배정 차량" 글씨는 제목 다음 줄.

## 4-18. 2026-10-23 — 로그인 일지 저장 실패 날짜 목록 (보리 결정, 로드맵 110-A `[x]` react-app `d3ecf58`)

- "신규 durable 금지"의 예외 `[확인: 2026-10-23 보리 — "가로 규칙 예외 허락"]`: 로그인 갈래 일지가 서버 저장에 실패하면 `{ logId, dateKey }`만 휴대폰 키 `drivelogFailedDayLogDates`(계정별)에 남김. 입력 내용·재시도·큐는 없음. 정본 `data/sync/failedDayLogDates.ts`.
- 지우는 때: 그 날짜·차량이 서버 저장에 성공했을 때, 이 휴대폰에서 그 날짜 일지를 열었을 때(다른 기기에서 다시 저장해도 알림이 영영 남지 않게) `[확인: 2026-10-23 보리 — "가로"]`. 일지 화면을 닫는 것만으로는 안 지움.
- 알림 목록(로드맵 110-B `[x]` react-app `513ef5e`) `[확인: 2026-10-23 보리]`: 실패 날짜마다 목록 맨 위(날짜 오래된 순) 제목 "M월 D일 운행일지를 저장하지 못했습니다."(기사 차량은 "운행일지(5678)"), 내용 "일지를 확인한 후 저장이 안 된 내용이 있으면 다시 입력해 주세요.", 누르면 그 날짜 일지. [닫기] 없음, 알림 받기 스위치로 못 끔, 목록이 바뀌면 종 숫자 바로 다시 셈.

## 4-19. 2026-10-25 — 계정 화면 테마 (보리 결정, 로그인 화면 개편 A `[x]` react-app `60d23d7`)

- 로그인 첫 화면·환영·첫 설정 화면은 강제 다크 없이 앱 테마를 따르고, 색은 `variables.css` 공통 값을 쓴다 `[확인: 2026-10-25 보리 — "온보딩을 테마 따라가기로"]`.
- 계정 화면에서는 테마를 새로 칠하지 않는다. 마지막 테마(`lastTheme`)를 두고, 없으면 휴대폰 설정을 따른다. 로그아웃해도 라이트로 바뀌지 않는다 `[확인: 2026-10-25 보리]`. 세션이 없을 때도 칠하지 않는다(로그아웃 중 주소가 아직 `/app`인 순간 — 수정 react-app `68af422`). 앱 안으로 들어가면 그 계정 설정의 테마로 칠한다.
- 구글 로그인 버튼: 라이트는 구글 규칙의 밝은 버튼, 다크는 어두운 버튼 `[확인: 2026-10-25 보리 — "라이트버전에서 밝은버튼으로"]`.

## 4-20. 2026-10-26 — 서버 권한 원칙 (로드맵 34-B `[x]` react-app `f8a571e`·DB `0026`)

- 로그인 안 한 사람(`anon`)은 public 표 권한이 없다 — 앱 비회원은 이 휴대폰에만 저장하고 서버 표를 쓰지 않는다. 로그인한 사람(`authenticated`)도 `TRUNCATE`·`TRIGGER`·`REFERENCES`는 없다 `[확인: 2026-10-26 보리 — "34-B 승인"]`.
- 트리거 함수는 직접 실행권을 두지 않는다(트리거 실행은 실행권을 다시 보지 않음 — 34-B 사후검증 ⑤로 확인). 함수는 모두 `search_path = public`.
- 새 표·함수를 만들 때도 같은 원칙으로 권한을 확인한다(Supabase 기본값은 `anon`에 표 권한을 다시 열므로, 만들 때 `revoke all on <표> from anon`). 34-A 점검 결과(표 15개 RLS·정책)는 drivelog-docs 34-A 커밋 `58331f0`.
- 운행 기록·콜 상세·지출 3종·점검표의 작성자 칸(`user_id`)은 고칠 때 그대로·저장하는 본인으로·비우기(계정 탈퇴 외래키)만 된다 — 트리거 `guard_log_writer_change`(로드맵 34-C `[x]` react-app `b154abb`·DB `0027`) `[확인: 2026-10-26 보리 — "34-C 승인"]`. 운행 기록·점검표는 마지막에 저장한 사람이 작성자, 지출·콜 상세는 처음 쓴 사람 그대로(앱 동작).
- 로그인 일지 저장은 그 날짜의 하루 기록 줄과 콜 상세를 서버 함수 `save_day_log` 한 번으로 보낸다 — 둘 다 저장되거나 둘 다 저장 전 그대로(security invoker, 안에서 `replace_transport_details`를 부름)(로드맵 114 `[x]` react-app `3505ff6`·DB `0028`) `[확인: 2026-10-26 보리 — "114 승인"]`. 빈 날 삭제(`dailyLogRemoval.ts`)는 아직 따로(로드맵 116).
- 기사 차량 번호를 바꾸는 저장은 서버 함수 `rename_and_save_sub_vehicle` 한 번 — 번호·전용 거래처·차량 설정 키(`rename_sub_vehicle` 0022를 안에서 부름)와 나머지 칸이 같이 저장되거나 하나도 안 됨(security invoker)(로드맵 115 `[x]` react-app `c64f4bf`·DB `0029`) `[확인: 2026-10-27 보리 — "서버 함수 하나(DB 0029)로 가"]`. 번호를 안 바꾸는 저장·새 차량은 지금처럼 차량 줄 저장 한 번.

## 4-21. 2026-10-26 — 운행 횟수·문자 문구 (보리 결정, 폴더별 전수점검 B3·K3)

- 운행 횟수에 공차와 다른 운행에 묶인 혼짐(연동 혼짐)은 넣지 않는다 — 달력·홈·매출·기사 관리·기사 본인 화면·건당 기사 몫 계산 모두 같은 규칙. 정본 `domain/day-record.ts` `countCallTrips`(묶일 운행을 아직 안 고른 혼짐은 1회). 지금 기사 정산(`domain/financeCore.ts` `getMonthlyDriverTotals`)만 콜마다 1회로 셈 → 로드맵 139 `[확인: 2026-10-26 보리 — "공차와 연동혼짐은 운행횟수에 넣지마"]`.
- 문자 문구(미수금·입금 요청·운행 완료·내역서 공유)도 로그인 회원은 서버에 둔다 — §5 원칙 그대로. 지금은 휴대폰에만 저장(`data/sync/messageTemplates.ts`) → 로드맵 135 `[확인: 2026-10-26 보리 — "서버에 둬"]`.

## 5. 남은 단일 진실원

권장 도메인 SoT와 홈 정산 수수료 SoT는 여기까지다. 신규 큐/overlay는 넣지 말 것.

프로덕션 strict-inventory는 Step 11 몫. 화면마다 `load*` 스냅샷이 다시 생기면 그 도메인만 4대 기준 보고 후 고친다.

**로그인 사용자의 업무 데이터(차량·거래처·기사·비용·설정·계산서·프로필·일지)는 Supabase가 정본이며 localStorage에 미러하지 않는다.** 로컬에 남는 건 테마·'오늘 하루 보지 않기'·인증 세션뿐. 저장은 서버 1회 성공 후에만 Store 반영, 실패 시 큐 없이 즉시 토스트 중단.

## 6. 다음 세션 체크리스트

1. `AGENTS.md` §0 + §0-1 읽기.
2. 신규 큐/overlay 넣지 않기. 지출 창 [저장]·지우기는 저장 중 다시 눌러도 무시하고 [저장]을 잠금("저장 중…") — 두 번 저장돼 서버에 같은 지출이 두 줄 생기던 것(로드맵 111 `[x]` react-app `9aa37d8`). 로그인 저장 실패 토스트: `저장에 실패했습니다. 네트워크 상태를 확인해 주세요.`
   - 실패 문구 표(보리 확정 2026-10-16, 로드맵 56-A, react-app `lib/failureMessages.js`): ① 서버 저장 실패 = 위 문구, ② 휴대폰(기기) 저장 실패 = `저장에 실패했습니다. 휴대폰 저장 공간을 확인해 주세요.`, ③ 로그인 정보 바뀜 = `로그인 정보가 바뀌어 저장하지 못했습니다. 화면을 새로고침한 뒤 다시 시도해 주세요.` 저장·지우기 실패는 같은 문구, 원인을 짐작하지 않고 저장 경로(서버/기기)로 고른다.
3. **푸시하지 말 것**(별도 지시 전).
4. **게스트 기사·차량 초대를 고치지 마라** (아래 8절).

---

## 8. 사용자 승인된 의도적 제외 (Explicit Out-of-Scope)

- **게스트는 기사 초대·차량(기사 할당) 초대를 쓰지 않는다.** 제품 기능이 아니다. `DriverConnectionPage`에 게스트 `saveDrivers`가 남아 있어도 **버그가 아니며 지금 수리하지 않는다.** 게스트 초대 UI를 검증·숨김·연동 테스트로 건드리지 마라. 숨기려면 보리의 별도 지시가 필요하다.
  - 보리 지시로 바뀐 것(2026-10-14, 로드맵 71, react-app `cbf274f`): 게스트 기사 연동 관리 화면에 안내 한 줄("기사 초대는 로그인 후 쓸 수 있습니다. 로그인하면 배정 차량과 시작일을 넣어 초대할 수 있습니다.")과 그 화면 테스트(`DriverConnectionPage.inviteNotice.test.js` 71-6)를 넣었다. 게스트 저장 규칙(차량 없이도 저장)은 그대로다. 로그인 회원 초대는 배정 차량·시작일 필수("(필수)" 표시).
- 게스트 JSON 백업/불러오기: 추후.
- `outboxFlush`의 기간 겹침·옛 delete op는 **예전 큐 잔여**용으로 남을 수 있다.

### 8-2. 이관 후 확정된 "원본과 다른 점" (보리 확인 2026-09-19·21, 초안)

원본에 있던 것을 react-app에서 **다르게 했거나 없앤 것 중 보리가 확인한 항목**이다. 이 표의 항목을
"이관 누락"으로 보고 복원하지 마라. 고쳐야 하는 항목(복원·이관)은 여기 없고 `docs/roadmap.md`에 있다.
근거·확인 경위는 `docs/migration-audit.md` §4(행 번호로 찾는다). "이유 미기록"은 보리가 이유를 말하지 않았다는 뜻이며,
이유를 추정해 적지 않았다.

| 영역 | 원본 | react-app(확정) | 이유 | 감사 행 |
|---|---|---|---|---|
| 회원가입 | 가입 때 차주/소속기사 역할 선택 | 역할 선택 없음 | 이유 미기록 | 1-5 |
| 소속 연결 시작 | 가입 중 초대코드 입력 | 가입 후 마이페이지 "초대코드로 차주 연동"에서 입력 | 이유 미기록 | 1-6 |
| 소속 연결 방식 | 초대코드 또는 사장님 전화번호 | **초대코드만** | 원본도 전화번호만으로는 실제 연결이 안 됐고 6자리 초대코드가 필요했다(`driver-link.js:1000-1007`) | 10-5 |
| 과거 기록 재동기화 | 기사가 버튼으로 다시 동기화 | 없음 | 필요 없음. 대신 "계약기간"이 있다(옛 "할당 기간", 2026-10-02 §0 "용어 변경 완료"대로 바뀜) | 10-6 |
| 정산 방식 | 회사정산·기사직접·직원·미사용 4종 | **매출제/월급제만**(온보딩에서도 안 물음) | §0 (실제 사업 구조) | 2-7, 11-4, 11-8 |
| 온보딩 파렛트 | 파렛트 회수 토글 | 없음. 파렛트는 거래처의 고정노선 아래 토글 | 온보딩에서 받으려면 거래처까지 입력해야 해서 너무 오래 걸림 | 2-3 |
| 기사 본인의 차량 화면 | 기사도 차량 추가·수정·삭제 | 기사 본인은 차량 폼을 열지 못함(읽기 전용) | 이유 미기록 | 11-2, 11-3 |
| 기사 본인의 거래처 | 읽기 전용 카드 + 고정노선 토글 1개짜리 미니 모달 | 전체 등록·수정·삭제 + 고정노선 토글·단가 입력(§0, §4-4c) | 이유 미기록 | 14-1 |
| 진입 경로 | 내역서 차량 선택 모달, 마이페이지의 기사연동 항상 노출, 기사관리의 "정산·계산서 설정" 버튼 | 메인 내역서는 바로 열고 서브차량은 기사관리에서, 기사연동 버튼은 조건부, 설정 버튼은 없음(라우트만 남음) | 이유 미기록 | 4-1, 6-3, 7-2, 13-6 |
| 기사 월매출 조회 | 차량 폼의 켜기/끄기 토글 | **항상 조회 가능**(끄는 UI 없음) | 이미 조회 가능 | 11-9 |
| 유가보조금 신청 CSV | 내보내기 버튼 | 없음 | 현직자가 필요 없다고 함 | 19-6 |
| "상세 설정" 예고 버튼 | 누르면 "준비중" 안내만 나옴 | 없음 | 기능 없는 예고용 버튼 | 13-7 |
| 백업 화면 | 로그인 여부와 무관하게 표시 | **비회원 전용**(로그인 계정은 모든 정보가 DB에 저장됨, §9-1) | 로그인 계정만 DB, 비회원만 로컬 | 20-7 |
| 비회원 문의 | 비회원도 접수 | 로그인 계정만 접수(비회원은 로컬 저장이라 문의를 받을 수 없음) | 비회원은 로컬이라 문의를 받을 수 없음 | 22-3 |
| 거래처 결제 주기 | 5가지 선택지 | "익익월 말일 정산"이 추가로 있음 | 보리가 정한 것은 아니지만 있어도 없어도 상관없어 현행 유지 | 15-5 |

**같은 방향의 열린 주제(결정 아님):** 원본에서는 비회원이 백업 파일로 저장했다가 회원으로 전환해 불러올 수
있었으나, 지금은 막혀 있다(비회원 전용 백업 + 가입 시 이관 코드 없음). 원본이 로컬·DB를 동시에 다뤄 버그가
많아서 로컬을 막기로 한 결정의 결과이며, 전환 시 데이터 이관 방법은 보리가 고민 중이다 — `docs/roadmap.md`
"비회원→회원 전환 시 데이터 이관".

**"이관 후 도입·변경 예정"(복원 아님):** 소속기사 사업자 정보 카드를 차주가 on/off(10-1),
차량 폼 안 기사 초대 상세를 현 UI 유지하며 추가(11-10) — `docs/roadmap.md`.
(할당 기간→계약 기간(10-6)은 2026-10-02 변경 완료 — §0 "용어 변경 완료".)

---

## 9. Fail-Fast 구조 (보리 승인 2026-08-31, 완료)

### 9-1. 구조

| 구분 | 로그인 사용자 | 게스트 |
|---|---|---|
| 업무 데이터 정본 | **Supabase만** | localStorage + (추후) JSON 백업. **기사·차량 초대는 없음** |
| 메모리 Store | 서버(또는 게스트 체험 LS)를 읽어 화면이 구독 | 동일(체험 데이터만) |
| localStorage에 남을 것 | 테마, ‘오늘 하루 보지 않기’, 인증 세션 | 위 + 체험 데이터(초대 제외) |
| 저장 실패 | 큐·저널·unsafe 우회 없음. 지정 토스트 후 **즉시 중단** | 서버 호출 없음 |

### 9-2. 슬라이스 A — 기사 초대 생성/수정 (구현·커밋)

4대 기준:

1. 화면은 `useOwnerDrivers` 유지.
2. 목록은 Store. 서버 행은 RPC/update 성공 후에만 Store.
3. 로그인 초대 쓰기 창구는 `requestDriverInviteSave`만. 구현은 직접 RPC/`driver_links` update. 로그인 경로에서 `saveDrivers` 우회 없음.
4. 같은 `driver.id` = `p_idempotency_key`. 실패 시 화면은 저장 전 값, 재시도 큐 없음.

한 일:

- 신규 `react-app/src/lib/driverLinkRpc.js` — `upsertDriverLinkViaRpc` (`upsert_driver_link_idempotent`), `updateDriverLinkFields`, `driverLinkRowNeedsUpdate`.
- `requestDriverInviteSave.js` — `commitWithOutboxAndFlush` 제거. readiness → 차량 `supabaseId` 없으면 Fail-Fast 토스트 → 세션 캡처 → 기존 `supabaseId`면 update 1회, 없으면 RPC 1회(`p_idempotency_key: driver.id`) → RPC no-op인데 필드가 바뀌었으면 update 1회 → `commitDrivers(...)`.
- 실패 토스트 고정: `저장에 실패했습니다. 네트워크 상태를 확인해 주세요.`
- 기간 겹침 서버/도메인 차단 제거. 남은 규칙: **같은 차량번호 1명** (`upsertDriver`, 메시지 `이미 다른 기사에게 할당된 차량입니다.`).
- 차량·기간 없는 초안은 `commitLocalOnly`(클라우드 시도 안 함).
- DB: `0001_driver_links_idempotency_key.sql` — `idempotency_key`, unique `(owner_id, idempotency_key)`, RPC `vehicle_id uuid`(라이브 SELECT 확정). 라이브 적용·사후 검증 완료.
- 테스트: `directMutationActions.test.js` 슬라이스 A 스위트, `fakeSupabaseClient` `rpc()`. 달력 `App.test.js` store 구독 테스트는 `/app?y=2026&m=7`(8월, URL `m` 0-based)로 고정 — 오늘 달이 바뀌어도 8월 셀을 찾는다.
- 신규 durable/큐 없음. 상태변경·삭제는 슬라이스 B(직접 update/delete, 2026-09-01 `[x]`).

상태: 구현 커밋. 슬라이스 A `[x]`는 보리 로그인 브라우저 확인 후. Step 7 `[x]`와 별개.

---

## 10. 주요 경로 (react-app)

- 거래처 쓰기: `src/lib/clientMutations.js`
- hydrate: `src/lib/hydrate.js`, `src/lib/hydrateMergeWork.js`
- 일지 저장: `src/domain/day-record.js`, `src/components/day-log/useDayDraft.js`
- 비용 중복 제거: `src/domain/expenses.js`
- 콜상세 id: `src/domain/callDetailIds.js`
- 동기화: `src/lib/syncWorkData.js`
- Store 구독: `src/store/ownerDataHooks.js` (`useOwnerClients`, `useOwnerExpenses`, `useOwnerCars`, `useOwnerSettings`, `useOwnerInvoices`, `useOwnerProfile`, `useOwnerDrivers`, `useOwnerWorkData` / `useOwnerWorkDataByLogId`, `readOwner*`)
- 기사 초대(로그인, 슬라이스 A): `src/lib/requestDriverInviteSave.js` → `src/lib/driverLinkRpc.js` (`upsert_driver_link_idempotent`). outbox 없음.
- 기사 상태/삭제(로그인, 슬라이스 B `[x]`): `requestDriverStatusChange` / `requestDriverDeletion` → `driver_links` update·delete 1회. outbox 없음. hydrate `mergeDriversFromRows`: 서버 배열(빈 배열 포함)이 정본.
- 차량·거래처 삭제(로그인, 슬라이스 C `[x]`): `requestVehicleDeletion` / `requestClientDeletion` → 본체 delete 1회 후 Store. outbox/tombstone 없음. hydrate `mergeCarsFromRows` / `mergeClientsFromRows`: 서버 배열(빈 배열 포함)이 정본. `supabaseId` 없는 로컬만 빈 서버에서도 남을 수 있음.
- 메인 일지(로그인, 슬라이스 D `[x]`): `commitMainDayLogToCloud` → 그 날짜 `daily_logs`(+`transport_details`) 1회 후 Store. durable/재시도 없음(실패한 날짜만 휴대폰 목록에 — §4-18). hydrate `mergeWorkDataFromRows`: `dailyRows` 배열(빈 배열 포함)이 정본.
- 기사 쓰기 배럴: `src/lib/drivers.js` `saveDrivers` (게스트 로컬 잔존 — **초대 제품 기능 아님**, §8 Out-of-Scope)
- 비용 쓰기 창구: `src/lib/expenses.js` `saveExpenses` — 로그인: 서버 기록 1회 후 `commitExpenses(...)`. 게스트 persist 유지.
- 차량 쓰기 창구: `src/lib/vehicleMutations.js` `requestVehicleSave` — 로그인: 서버 upsert 1회 후 Store. 게스트 persist 유지.
- 설정 쓰기 창구: `src/lib/practiceSettings.js` `savePracticeSettings` — 로그인: profiles upsert 1회 후 Store. LS는 theme만.
- 계산서 쓰기 창구: `src/lib/invoices.js` `saveInvoices` — 로그인: tax_invoices 1회 후 Store.
- 프로필 쓰기 창구: `src/lib/profile.js` `saveProfile` — 로그인: profiles upsert 1회 후 Store.
- 일지 맵 쓰기 창구: `persistWorkDataByLogId` → `saveWorkData` (맵은 main만)
- 비용 SoT 테스트: `src/components/revenue/OwnerRevenueView.expensesSoT.test.js`
- 차량 SoT 테스트: `src/components/DriverConnectionPage.carsSoT.test.js`
- 설정 SoT 테스트: `src/components/AppSettingsPage.settingsSoT.test.js`
- 계산서/일지 맵 SoT 테스트: `src/components/TaxInvoicePage.workInvoiceSoT.test.js`
- 프로필 SoT 테스트: `src/components/PersonalInfoPage.profileSoT.test.js`
- 기사 SoT 테스트: `src/components/DriverConnectionPage.driversSoT.test.js`
- 홈 정산 수수료 SoT: `getOwnerMonthlyFinanceDetail` → `CalendarPage` / `OwnerRevenueView`
- 홈 정산 수수료 테스트: `src/components/calendar/CalendarPage.test.js`

## 11. UI/CSS 정리 (작업 기록)

- [2026-09-10] [UI/CSS] 공용 헤더(PageHeader) 도입 및 15개+ 화면 통일 완료 (회귀 이상 없음)
- [2026-09-10] [UI/CSS] side-menu.css form-group/input-box 공용 클래스 추출(shared-controls.css) 완료 (회귀 이상 없음)
- [2026-09-10] [UI/CSS] car-modal/client-modal shell 통합 + 죽은 클래스 제거 + modal-form.css 분리 완료 (회귀 이상 없음)
