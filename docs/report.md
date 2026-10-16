# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 75 휴무일 콜은 모든 화면에서 제외 + 휴무로 바꿀 때 확인 `[x]`

코드 리뷰(2026-10-14)로 로드맵 75번 등재 `[확인: 2026-10-14 보리]`, 순서 ②(돈·세무 금액). 74번 다음. 플레이북 트리거 해당(`domain/finance*` 계산) — `docs/testing-playbook.md` 열람함. DB 변경 없음.
**보리 결정(2026-10-15): B — 휴무일 콜은 모든 화면에서 제외.** 이 결정으로 F-03(`docs/finance-parity.md:93` "휴무일도 합산", `[확인: 2026-09-21 보리]`)을 바꿉니다. sot에는 휴무 규칙이 없었습니다(`grep "휴무\|isOff" docs/sot.md` 0건). [x] 문서 커밋 때 sot·finance-parity·roadmap에 새 결정을 적습니다.

### 쉬운 요약
- **무엇을:** 휴무로 표시한 날의 콜은 모든 화면(달력 미수 합계·빨간 점·기사 정산 합계 포함)에서 뺍니다. 휴무로 바꿀 때 그날 콜이 있으면 확인 창으로 먼저 묻습니다.
- **왜:** 지금은 휴무일 콜을 달력 미수 합계·빨간 점·기사 정산 합계에서만 세고, 달력 정산·미수금·매출·세금계산서·내역서에서는 뺍니다. 그래서 휴무일 콜의 미수가 달력에만 보이고 미수금 화면에서는 입금 처리할 수 없습니다.
- **달라지는 것:** 모든 화면의 합계가 같아집니다. 실수로 휴무를 눌러 받을 돈이 빠지지 않게 확인 창이 막아 줍니다. 휴무를 끄면 그 콜은 다시 모든 합계에 들어갑니다(콜은 지우지 않음).

### 현재 상태 (코드 확인)
- 휴무일 콜을 **세는** 곳 3곳:
  - 달력 미수 합계: `domain/day-record.js:176-186` `monthCallUnpaidTotal`(`CalendarPage.jsx:48`).
  - 달력 빨간 점: `domain/calendarBadges.js:67-72` `dayHasUnpaid`(`CalendarGrid.jsx:45`).
  - 기사 정산 합계: `domain/financeCore.js:99-124` `getMonthlyDriverTotals`(`driverRevenueShareExpense.js:59·63`, 기사 몫·연동 기간 밖 금액).
- 나머지 계산은 휴무일을 이미 뺍니다(`monthSettlement.js:86`, `financeReceivables.js:58`, `financeOwnerDetail.js:73`, `financeCore.js:164`, `financeTaxInvoiceGroups.js:105`, `reportSummary.js:46`, `reportDetail.js:30·79`, `day-record.js:85·108·114`, `call-details.js:139`, `clients.js:47`).
- 휴무 버튼(`DayLogPage.jsx:157`)은 확인 없이 휴무를 켜고 고정 노선 횟수만 0으로 만듭니다. 콜 상세는 남습니다.
- 앱 공용 확인 창 `confirmDialog`가 있고, 같은 폴더 `callDetailFormHelpers.js:92`가 이미 씁니다.

### 목표 상태 (기대 동작)
1. 휴무일 콜의 미수는 달력 이번 달 미수 합계에 들어가지 않습니다.
2. 휴무일 칸에는 미수 빨간 점이 없습니다.
3. 기사 정산 합계(기사 몫·연동 기간 밖 금액)에 휴무일 콜·고정 노선이 들어가지 않습니다.
4. 휴무로 바꿀 때 그날 콜이 1건 이상이면 확인 창이 뜹니다.
   - 제목 "휴무로 바꾸시겠습니까?", 본문 "이날 콜 N건이 있습니다. 휴무로 바꾸면 이 콜은 합계와 미수에서 빠집니다.", 버튼 "휴무로 바꾸기"/"취소".
   - 취소하면 아무것도 바뀌지 않습니다. 콜이 없으면 지금처럼 바로 바뀝니다.
5. 휴무를 끌 때는 확인 없이 바로 바뀝니다. 콜은 지우지 않으므로 다시 모든 합계에 들어갑니다.
6. 휴무가 아닌 날의 계산은 하나도 바뀌지 않습니다.

### 건드릴 파일
- `react-app/src/domain/day-record.js` — `monthCallUnpaidTotal`에서 휴무일을 건너뜁니다(199줄 → 199줄 이하, 조건 한 줄에 더함).
- `react-app/src/domain/calendarBadges.js` — `dayHasUnpaid`가 휴무일이면 `false`(104줄 → 105줄).
- `react-app/src/domain/financeCore.js` — `getMonthlyDriverTotals`(`:111`)가 휴무일을 건너뜁니다.
  - **§6 예외 요청:** 이 파일은 212줄입니다. 예전처럼(`STATUS.md:450` "줄 수 증가 없는 제자리 수정, §6 응집도 예외, 보리 승인") 기존 조건 줄에 `|| record.isOff`만 더하고 줄 수는 늘리지 않습니다. 이 지시서 승인으로 예외 승인을 함께 받습니다.
- `react-app/src/components/day-log/callDetailFormHelpers.js` — 확인 창 함수 `confirmOffWithCalls(off, callCount)`를 더합니다(98줄 → 약 112줄).
  - 새 주석(최종 문구): `// 콜이 있는 날을 휴무로 바꾸면 그 콜이 합계·미수에서 빠지므로 먼저 묻는다(로드맵 75).`
- `react-app/src/components/day-log/DayLogPage.jsx` — `:157` 휴무 버튼이 위 함수를 거친 뒤 바꾸도록 같은 줄만 바꿉니다(233줄 그대로, 26번 예외 범위 안).
- 테스트:
  - `domain/day-record`·`calendarBadges`·`finance` 쪽 — 휴무일 콜 미수 제외, 빨간 점 없음, 기사 정산 합계 제외. 휴무일 콜을 세던 기존 기대값이 있으면 새 결정대로 바꾸고 줄마다 이유 주석 1줄.
  - `callDetailFormHelpers` — 콜 있음+켜기면 확인, 취소면 `false`, 콜 없음·끄기면 묻지 않고 `true`.

### 안 건드릴 것
- 이미 휴무일을 빼는 계산들(위 목록) — 그대로입니다.
- 휴무를 켤 때 콜을 지우지 않습니다(되돌리면 다시 셈, 원본도 콜 유지).
- 고정 노선 0 처리·달력 칸 표시·"오늘 비어 있음" 알림.

### 검증
- 로컬 `npm test`. 새 테스트는 고치기 전 코드에서 실패하는지 확인합니다. `tsc` 0, CI verify 초록.
- 브라우저: 개발 서버 비회원으로 콜 1건(미수) 있는 날을 휴무로 바꿈 → 확인 창 → 취소면 그대로 → 휴무로 바꾸기 → 달력 미수 합계에서 빠지고 빨간 점이 사라지는지 봅니다. 휴무를 끄면 돌아오는지도 봅니다.

### 파일 길이
- `day-record.js` 199줄 이하, `calendarBadges.js` 약 105줄, `callDetailFormHelpers.js` 약 112줄.
- `financeCore.js` 212줄 그대로(§6 예외 요청), `DayLogPage.jsx` 233줄 그대로(26번 예외 범위).

### 실패 시 처리
- 새 저장 장치 없음. 계산 조건과 확인 창만 더합니다.
- 검증이 실패하면 수정 착수지시서를 써서 다시 승인받습니다.

### 사용자 확인 방법
1. 앱에서 지난달 아무 날에 콜 1건(운송료 10,000원, 미수)을 저장 → 달력에 빨간 점·이번 달 미수 합계에 10,000원 확인.
2. 그날 휴무 버튼 → "휴무로 바꾸시겠습니까? 이날 콜 1건이 있습니다…"가 뜨면 성공 → 취소 → 그대로인지 확인.
3. 다시 휴무 → "휴무로 바꾸기" → 빨간 점이 사라지고 달력 미수 합계에서 10,000원이 빠지면 성공입니다.
4. 휴무를 끄면 빨간 점·미수 합계가 돌아오면 성공입니다. 시험 콜을 지웁니다.

---

## 진행 기록 (75)

- 코드 커밋 react-app `7395b21`(push 안 함).
  - `day-record.js` 199줄, `calendarBadges.js` 104줄, `financeCore.js` 212줄 — 셋 다 기존 조건 줄에만 더해 줄 수 그대로(`financeCore.js` §6 예외 승인 범위).
  - 새 테스트 2개 파일: `domain/offDayCalls.test.js`, `components/day-log/OffToggle.test.js`.
- **지시서와 다른 점 — 확인 창 위치.** 지시서엔 `callDetailFormHelpers.js`에 함수를 두고 `DayLogPage.jsx`에서 부른다고 적었습니다. 그런데 `DayLogPage.jsx`가 그 파일을 불러오지 않아 불러오기 1줄이 늘면 233줄을 넘습니다. 그래서 확인 함수를 휴무 버튼 부품 `components/day-log/OffToggle.jsx`(19→42줄)에 두고, 버튼이 직접 묻게 했습니다.
  - `DayLogPage.jsx`는 같은 줄에 `callCount={draft.callDetails.length}`만 넘깁니다(233줄 그대로).
  - `callDetailFormHelpers.js`는 건드리지 않았습니다.
- 새 테스트 75-1~6. 수정한 4개 파일을 되돌리면 75-1~4는 실패하고 고친 뒤 통과합니다. 75-5(확인하면 바뀜)·75-6(콜 없음·끄기)은 고치기 전에도 결과가 같은 안전 확인입니다.
- 기존 테스트 기대값을 바꾼 것은 없습니다(휴무일 콜을 세는 걸 기대하던 테스트가 없었음). 로컬 `npm test` 928+408 통과, `tsc` 0. 새 `any`/`unknown`/`@ts-ignore` 없음.
- 브라우저(AI, 개발 서버, 비회원, 결제·수금 켬, 10/10 콜 10,000원 미수를 기기에 미리 넣음):
  - 달력에 10일 빨간 점·"미수금 10,000원이 있습니다." 보임.
  - 10일 휴무 → 앱 확인 창 "휴무로 바꾸시겠습니까? / 이날 콜 1건이 있습니다. 휴무로 바꾸면 이 콜은 합계와 미수에서 빠집니다." → 취소 → 휴무 아님 그대로.
  - 다시 휴무 → "휴무로 바꾸기" → 콜은 남고 휴무 저장. 달력에 휴무 배지, 빨간 점·미수 안내 사라짐.
  - 휴무 끔 → 확인 창 없이 바뀜, 빨간 점·"미수금 10,000원" 돌아옴.
  - 콘솔 오류 0, 시험 데이터 지움.
- [x] 문서 커밋 때 할 일: sot에 휴무일 콜 규칙 추가, `finance-parity.md` F-03 행·요약과 `roadmap.md` 이관 후 개선(F-03)을 이 결정으로 고쳐 적기.
- push 뒤 CI verify·deploy 초록(run 38828808756).
- 남은 것: 보리 최종 `[x]`.
