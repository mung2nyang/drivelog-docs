# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 61-A 매출제 기사 몫의 기준 금액을 차량마다 정하고, 네 화면이 같은 기준으로 계산 `[x]`

> **진행(2026-10-18)**: 보리 "기본값은 운송료 전체로 진행해"(확인할 점 2·3은 지시서대로 — 선택지 두 가지, 차량마다). 코드 커밋 react-app `e91e9c8` — push 뒤 CI verify·deploy 초록(run 39093421859).
> - 바뀐 파일(지시서 목록과 같음): 새 `domain/driverShareBase.js` 54줄(`resolveDriverShareBase`·`driverShareBaseAmount`·`getMonthlyDriverClientCommission`), `monthSettlement.js` 160→161·`driverRevenueShareExpense.js` 105→107·`financeTaxInvoiceGroups.js` 174→176·`driverSelfRevenue.js` 112→115(기준 금액을 새 함수로), `financeTypes.js`(CarLike `driverShareBase`), `persistDomainRecords.js`(CAR 허용 값 2개), `hydrateMergeCars.js`(raw에서 꺼냄, 모르는 값 버림). `financeCore.js`(212줄)는 안 고침.
> - 새 테스트 `domain/driverShareBase.test.js` 6개: 운송료 100만원·거래처 수수료 10%·기사 몫 30% → 기준 없음/전체 = 네 화면 모두 30만원, 뗀 뒤 = 모두 27만원, 건당은 기준 무관, 모르는 값은 전체, 고정노선 수수료도 셈·계약 기간 밖은 안 셈. 네 화면 비교 3개는 고치기 전 코드에서 실패(달력 27만원 vs 나머지 30만원, 기준 칸 무시) → 고친 뒤 통과(revert-check). + `hydrateMerge.test.js` 1개(raw 왕복), `persistDomainRecords.test.js`(허용 값·틀린 값).
> - `npm test` 1042 + 485 통과, 타입 검사·빌드·린트 통과, 타입 꼼수 0(테스트에 이중 단언 한 줄 썼다가 바로 `Reflect.set`으로 고침, 커밋 전).
> - 개발 서버 비회원: 기사 차량(매출제 30%)·거래처 수수료 10% 콜 100만원 → 달력 "2222 차량 30% - 300,000원"(전엔 270,000원), 매출 "기사" 탭 "기사 급여 -300,000원", 기사 관리 "기사 정산금 300,000원" — 셋 같음. 콘솔 오류 없음.
> - **화면에서 바뀐 것**: 기본값이 "운송료 전체"라, 거래처 수수료가 있는 기사 차량의 **달력 기사 몫이 커짐**(예: 27만 → 30만원). 매출·기사 관리·연동 기사 매출은 그대로.
> - **지시서와 다른 점**: 연동 기사 본인 매출 화면은 차량 정보를 서버 함수 `get_assigned_vehicle_summary`(마이그레이션 0016)에서 받는데, 이 함수가 정해진 칸만 내보내 `driverShareBase`가 기사 쪽에 안 감 → 기사 화면은 늘 기본값("운송료 전체")으로 계산. 지금은 아무도 "뗀 뒤"를 못 고르므로(드롭다운은 61-B) 어긋나는 숫자 없음. 차주가 "뗀 뒤"를 고를 수 있게 되기 전에 서버 함수에 칸 1개 추가가 필요(DB 변경, AGENTS §9 절차) — 보리 결정(2026-10-18): **61-B와 함께**.

보리 확정 순서(2026-10-18) 81 → **61** → 62 → 63 → 76 → 60 → 59. 근거: `docs/roadmap.md` 61번(②, 39번 조사 중 발견).
**보리 결정(2026-10-18, 착수 전 질문)**: 기사 몫을 "운송료 전체" 기준으로 할지 "거래처 수수료 뗀 뒤" 기준으로 할지 **차주가 드롭다운으로 정한다.**
61번은 두 조각: **61-A 계산(이번)** — 차량에 기준 값을 저장·불러오고, 기사 몫을 계산하는 네 곳이 그 값을 따름 / 61-B 화면 — 차량 관리 창에 드롭다운(시안 먼저)·달력 설명 문구.

### 쉬운 요약
- 매출제 기사 몫(차량 관리에 적은 %)을 계산할 때, 지금은 화면마다 기준이 다릅니다: 달력만 "거래처 수수료 뗀 뒤", 매출·기사 관리 정산·연동 기사 본인 매출은 "운송료 전체".
  예) 운송료 100만원·거래처 수수료 10%·기사 몫 30% → 달력 27만원, 나머지 세 화면 30만원.
- 차량마다 "기사 몫 기준"(운송료 전체 / 거래처 수수료 뗀 뒤)을 저장하고, 네 화면이 모두 그 값으로 같게 계산하도록 바꿉니다. 고르는 드롭다운은 61-B에서 붙입니다.

### 현재 상태 (코드 확인)
- 기사 몫 계산은 모두 `calculateDriverVehicleCommission(car, 기준금액, 횟수)`(`domain/financeCore.js:130`, 매출제 %면 기준금액 × %, 건당이면 횟수 × 금액 — 건당은 기준과 무관). 기준금액만 다름:
  1. 달력(기사 차량 월 정산) `domain/monthSettlement.js:140` — `fare + palletFare - commissionTotal`(거래처 수수료 **뗀 뒤**).
  2. 매출 차주 "기사 급여" `domain/driverRevenueShareExpense.js:61` — `getMonthlyDriverTotals(...).grossAmount`(**전체**).
  3. 기사 관리 정산 카드 `domain/financeTaxInvoiceGroups.js:140` — `grossAmount`(**전체**).
  4. 연동 기사 본인 매출 `domain/driverSelfRevenue.js:69` — `grossAmount`(**전체**).
- `getMonthlyDriverTotals`(`financeCore.js:99`)는 계약 기간 안 운송료 합(`grossAmount`)·건수만 줌. 거래처 수수료 합은 없음. `financeCore.js`는 이미 212줄(200줄 넘음).
- 운송비 내역서(`lib/reportSummary.js:87`)는 차량을 안 넘겨 기사 몫이 0 — 영향 없음(기사 몫은 내역서에 안 넣는 원칙, `docs/sot.md` §0).
- 차량에 새 칸을 저장하는 길: 서버 `vehicles` 행의 `raw`(차량 전체 글)에 들어감(`lib/cloudStorage.js:85`), 불러올 때 `lib/hydrateMergeCars.js`가 아는 칸만 꺼냄(예: `withholdingOn` 77~78줄), 기기 저장 검사는 `store/persistDomainRecords.js` CAR — **서버 표(DB) 변경 없이** 됨.
- `docs/sot.md`에 기사 몫 기준 규칙 없음(`grep 매출제`).

### 목표 상태 (기대 동작)
1. 차량에 `driverShareBase` 칸: `'gross'`(운송료 전체) 또는 `'afterClientCommission'`(거래처 수수료 뗀 뒤). 없으면 **기본값**(확인할 점 1).
2. 기사 몫 기준 금액을 내는 공용 함수 1개(새 파일): 그 차량 기준 값에 따라 `grossAmount` 또는 `grossAmount − 같은 운행들의 거래처 수수료`(콜 상세 수수료 + 고정노선 수수료, 39번 규칙 그대로, 계약 기간 안만).
3. 위 네 곳이 모두 이 함수로 기준 금액을 받아 `calculateDriverVehicleCommission`에 넘긴다 → 같은 차량·같은 달이면 네 화면의 기사 몫이 같다.
4. 건당(`direct`) 기사 몫은 기준과 무관하게 지금 그대로.
5. 새 칸은 기기 저장·서버 저장(`raw`)·불러오기·연동 기사 쪽 차량 정보에서 그대로 유지된다. 모르는 값이면 무시하고 기본값.
6. 운송비 내역서는 그대로(기사 몫 없음).

### 건드릴 파일
- 새 `react-app/src/domain/driverShareBase.js` — 기준 금액 함수(거래처 수수료 합을 계약 기간 안에서 셈).
- `domain/monthSettlement.js`·`domain/driverRevenueShareExpense.js`·`domain/financeTaxInvoiceGroups.js`·`domain/driverSelfRevenue.js` — 기준 금액을 새 함수로.
- `domain/financeTypes.js`(CarLike 타입에 칸), `store/persistDomainRecords.js`(CAR 허용 칸), `lib/hydrateMergeCars.js`(불러올 때 칸 꺼냄), `lib/hydrateEmployedDriverCar.js`(연동 기사 쪽 차량에 칸 전달 — 착수 때 이 파일이 칸을 골라 담는지 확인, 고르지 않으면 안 고침).
- 테스트: 새 `domain/driverShareBase.test.js` + 네 곳 기존 테스트 파일에 "같은 기준이면 같은 값" 확인 추가.
- **`financeCore.js`(212줄)는 안 고침** — 거래처 수수료 합은 새 파일에서 셈(200줄 넘는 파일 늘리지 않음).

### 안 건드릴 것
- 화면(JSX)·차량 관리 창 — 61-B.
- `calculateDriverVehicleCommission`·`getMonthlyDriverTotals`(`financeCore.js`) 자체.
- 서버(Supabase) 표·함수 — `raw`로 저장.
- 산재·3.3% 계산(기사 정산액을 받아 계산 — 기사 몫이 바뀌면 그대로 따라감).
- 41-B(해제된 예전 계약 기간), 달력과 매출의 다른 차이(고정노선 단가 출처 등) — 이번 범위 아님.

### 검증
- 새 테스트(고치기 전 실패 → 고친 뒤 통과): 운송료 100만원·거래처 수수료 10%·기사 몫 30%인 기사 차량
  ① 기준 "전체" → 달력·매출·기사 관리·연동 기사 매출 모두 30만원 ② 기준 "뗀 뒤" → 모두 27만원 ③ 고정노선 수수료(39번)도 뗌 ④ 계약 기간 밖 운행은 수수료도 안 셈 ⑤ 건당 기사 몫은 기준과 무관 ⑥ 기준 칸이 없거나 모르는 값이면 기본값.
- 저장 왕복: 기기 저장 검사 통과, 서버 `raw` → 불러오기에서 칸 유지, 모르는 값은 버림.
- 기존 테스트 통과(39·40·41-A·45 정산 테스트 포함). `npm test`·`npm run typecheck`·`npm run build` 통과.
- 개발 서버 비회원: 기사 차량(매출제 30%)·거래처 수수료 10% 콜 넣고 달력·매출 "기사" 탭·기사 관리의 기사 몫이 같은지(기본값 기준).

### 파일 길이
- 새 `driverShareBase.js` 약 50. `monthSettlement.js` 160→약 162, `driverRevenueShareExpense.js` 105→약 106, `financeTaxInvoiceGroups.js` 174→약 175, `driverSelfRevenue.js` 112→약 113, `hydrateMergeCars.js` 104→약 106, `financeTypes.js` 110→111, `persistDomainRecords.js` 106→107. 모두 200줄 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(차량 기존 저장 길에 칸 하나 추가). 검증이 실패하면 고치지 않고 수정 착수지시서로 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤)
- 거래처 수수료가 있는 기사 차량(매출제 %)에서 달력 월 정산의 기사 몫과 매출 "기사" 탭 기사 급여, 기사 관리 정산 카드 금액이 같으면 성공(기본값 기준). 고르는 드롭다운은 61-B 뒤.

### 확인할 점
1. **기본값**(차량에 아직 기준을 고르지 않았을 때): **"운송료 전체"를 권장** — 지금 네 화면 중 세 곳(기사에게 보이는 정산 포함)이 이 기준이라 기사 몫이 바뀌는 화면이 달력 하나뿐. "뗀 뒤"로 하면 매출·기사 관리·연동 기사 매출의 기사 몫이 줄어듭니다.
2. 드롭다운 선택지는 **"운송료 전체" / "거래처 수수료 뗀 뒤" 두 가지**로 이해했습니다. 말씀하신 "기사 몫으로 할지"가 세 번째 선택지라면 어떤 계산인지 알려 주세요.
3. 기준은 **기사 차량마다** 정합니다(기사 몫 %도 차량마다 적으니까). 차주 전체에 하나로 원하시면 말씀해 주세요.
