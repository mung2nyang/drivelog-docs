# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 58-B 거의 같은 매출 화면 두 벌의 같은 부분을 하나로 `[x]`

> **진행(2026-10-17)**: 보리 "착수지시서 확정, 작업 진행해". 코드 커밋 react-app `2f231b1` — push 뒤 CI verify·deploy 초록(run 39029083064).
> - 바뀐 파일(지시서 목록과 같음): 새 `revenue/revenuePeriod.js` 45줄(`useRevenuePeriod`·`useRevenueFinanceSettings`·`buildYearlyRows`), 새 `revenue/RevenuePeriodParts.jsx` 46줄(`RevenuePeriodBar`·`RevenueYearList`), `OwnerRevenueView.jsx` 173→128, `DriverRevenueView.jsx` 88→35, 새 `RevenueViews.period.test.js` 2개.
> - 동작 기록 테스트 2개(두 화면: 달·해 이동, 년 매출 12줄 값·빨강·합계, 범위 탭 있음/없음)를 고치기 전 코드에서 먼저 통과시킨 뒤 합침 → 합친 뒤 그대로 통과. 고치기 전으로 되돌려도 통과(동작을 안 바꾸는 정리라 맞음, revert-check). 기존 차주 매출 테스트 2개 통과.
> - `npm test` 951 + 480 통과, 타입 검사 통과.
> - 개발 서버 비회원(차주) 360px 밝은·어두운 화면: 콜 하나(300,000원) 넣고 월 매출 "당월 순이익 300,000원", 다음 달 → 11월, 년 매출 10월 300,000원·나머지 0원·"2026년 순이익 합계 300,000원", 다음 해 → 2027년, 범위 탭 그대로. 콘솔 오류 없음. 사진 `scratchpad\v58b\`. 기사 화면은 로그인 필요라 테스트로.
> - 지시서와 다른 점: 없음. 참고: `RevenueNav.jsx` 머리 주석 "OwnerRevenueView·DriverRevenueView가 같이 쓴다"는 이제 `RevenuePeriodParts.jsx`만 부르는데 "안 건드릴 것" 파일이라 그대로 둠.

보리 "진행해"(2026-10-17, 58-A `[x]` 뒤). 근거: `docs/roadmap.md` 58번(⑥), `docs/code-cleanup-review.md` D("8줄 묶음 12개 겹침"). 58-A 거래처 `[x]`(`b3de723`) → **58-B 매출(이번)** → 58-C 공유 창.

### 쉬운 요약
- 차주 매출 화면과 연동 기사 매출 화면에 똑같이 복사된 부분(달·해 이동, 년/월 매출 탭, 12달 순이익 목록, 계산 설정 준비)을 한 곳으로 모읍니다.
- 화면 모양·숫자·누르는 동작은 그대로입니다. 차주 화면에만 있는 "전체 손익/차주/기사" 탭과 기사 고르기는 차주 화면에 그대로 남깁니다.

### 현재 상태 (코드 확인)
- `components/revenue/OwnerRevenueView.jsx`(173줄) — 차주 매출. `components/revenue/DriverRevenueView.jsx`(88줄) — 연동 기사 매출. `RevenuePage.jsx:23-24`가 계정에 따라 하나를 고름.
- 두 파일에서 똑같은 것:
  1. 보는 달·탭 상태(`tab`·`viewDate`·`year`·`month`·`yearly`)와 이동(`shift` — 년 매출이면 1년, 월 매출이면 1달).
  2. 계산 설정 준비(`buildFinanceSettings`를 차량·설정·개인정보·기사 바뀔 때 다시 — `void` 4줄 포함 9줄).
  3. 12달 순이익 줄 만들기(`for m 0..11` → `{ month, netProfit }`)와 합계.
  4. 위 카드의 날짜 이동(`DateNav`) + "년 매출/월 매출" 두 단추.
  5. 년 매출 목록(12줄 + "OOOO년 순이익 합계", 손해면 빨강 `negative`).
- 다른 것: 한 달 계산 함수(차주 `getOwnerMonthlyFinanceDetail` + 범위·기사 거르기 / 기사 `getDriverSelfMonthlyDetail`), 차주만 범위 탭·기사 고르기, 월 카드에 넘기는 값.
- 테스트: 차주 화면 2개(`OwnerRevenueView.expensesSoT.test.js`, `OwnerRevenueView.vehicleScope.test.js`), 기사 화면·년 매출 목록·이동은 테스트 없음.

### 목표 상태 (기대 동작)
1. 위 같은 것 1~5가 한 곳에만 있다.
2. 두 화면 모두: 월 매출에서 "다음 달"/"이전 달"은 1달, 년 매출에서 "다음 해"/"이전 해"는 1년 이동 — 지금과 같다.
3. 두 화면 모두: 년 매출은 12줄(1월~12월) + "OOOO년 순이익 합계", 손해 달·합계는 빨강 — 지금과 같다.
4. 차주 화면 범위 탭·기사 고르기·월 카드 숫자, 기사 화면 월 카드(본인 정산 기준) — 지금과 같다.
5. 화면 모양(CSS 이름·순서) 그대로.

### 건드릴 파일
- 새 `react-app/src/components/revenue/revenuePeriod.js` — 보는 달·탭 상태와 이동(`useRevenuePeriod`), 계산 설정 준비(`useRevenueFinanceSettings`), 12달 순이익 줄(`buildYearlyRows`).
- 새 `react-app/src/components/revenue/RevenuePeriodParts.jsx` — 위 카드 날짜 이동 + 년/월 단추(`RevenuePeriodBar`, 차주 범위 탭은 안에 끼워 넣음), 년 매출 목록(`RevenueYearList`).
- `react-app/src/components/revenue/OwnerRevenueView.jsx` — 위를 써서 범위 탭·기사 고르기·차주 계산만 남김.
- `react-app/src/components/revenue/DriverRevenueView.jsx` — 위를 써서 기사 계산만 남김.
- 새 테스트 `react-app/src/components/revenue/RevenueViews.period.test.js`.
- 공용 영향: `DateNav`(`RevenueNav.jsx`)·`OwnerMonthlyCards`·계산 함수는 안 바꾸고 부르기만. CSS 안 바꿈.

### 안 건드릴 것
- 계산 함수(`lib/finance.js`, `domain/driverSelfRevenue.js`, `driverRevenueScope.js`) — 숫자가 바뀌면 안 됨.
- `RevenuePage.jsx`, `RevenueNav.jsx`, `OwnerMonthlyCards.jsx`, CSS.
- 58-C.

### 검증
- **먼저** 새 테스트를 지금 코드에 돌려 통과(동작 기록): 두 화면 각각 — 다음 달 → 11월, 년 매출 → 12줄·"2026년 순이익 합계"·다음 해 → 2027년, 일지 하나 넣은 달 순이익 값이 년 목록에 보임. 합친 뒤 그대로 통과(목표 1~4).
- 기존 차주 화면 테스트 2개 그대로 통과(목표 4).
- `npm test`·`npm run typecheck` 통과.
- 개발 서버 비회원(차주 화면) 360px: 월/년 전환, 달·해 이동, 범위 탭 — 바뀌기 전후 같은지(숫자·사진). 기사 화면은 로그인 필요라 테스트로.

### 파일 길이
- `OwnerRevenueView.jsx` 173 → 약 120
- `DriverRevenueView.jsx` 88 → 약 35
- 새 `revenuePeriod.js` 약 50, 새 `RevenuePeriodParts.jsx` 약 55
- 새 테스트 약 110(테스트 파일 예외)

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(보여 주는 화면만).
- 합친 뒤 동작 기록 테스트가 깨지면 고치지 않고 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤)
1. 매출 → 월 매출에서 "<"·">"로 달 이동, 숫자가 전과 같다.
2. 년 매출 → 1월~12월 목록과 "2026년 순이익 합계", "<"·">"로 해 이동.
3. 차주 계정: 전체 손익/차주/기사 탭, 기사 고르기 그대로.
4. 연동 기사 계정: 매출 화면이 전과 같다.

### 확인할 점
- 없음(화면·숫자를 바꾸지 않는 정리 작업).
