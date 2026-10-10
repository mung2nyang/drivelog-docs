# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 1-A — 수수료 판단 규칙 하나로 합치기 `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` A1.
보리 결정 순서: 1(A1·A2) → 2(안 쓰는 파일·함수) → 3(안 쓰는 CSS) → 4(이력형 주석) → 5(뒤로).
A1(돈)과 A2(저장)는 영역이 달라 **1-A / 1-B 두 슬라이스로 나눔**. 이 지시서는 1-A만.

### 현재 상태 (코드 확인)
- "이 운행에 수수료가 붙나·방식·값" 판단(저장된 `commissionSnapshot` 먼저 → 없으면 거래처 설정)이 두 곳에 똑같이 있음.
  - `domain/financeCore.js:74` `getCallDetailCommissionAmount` — 금액 계산(정산·월 정산·보고서가 씀)
  - `lib/reportDetail.js:45` `detailCommissionLabel` — 보고서 "10%", "7,000원" 표시
- 한쪽 규칙만 바뀌면 보고서의 수수료 금액과 표시가 서로 달라질 수 있음.

### 목표 상태 (기대 동작)
1. `financeCore.js`에 판단만 하는 함수 `resolveCallDetailCommission(detail, settings)` 하나 → `{ enabled, type, value }`.
2. `getCallDetailCommissionAmount`와 `detailCommissionLabel`이 둘 다 이 함수를 불러 씀. 판단 코드는 한 곳에만 있음.
3. **화면·금액 변화 없음.** 정산·보고서 숫자와 "10%"/"7,000원" 표시 그대로.

### 건드릴 파일
- `react-app/src/domain/financeCore.js` — 판단 함수 추가, 금액 함수가 그걸 쓰게
- `react-app/src/lib/reportDetail.js` — `detailCommissionLabel`이 판단 함수를 쓰게
- `react-app/src/lib/report.test.js` — 테스트 1건 추가(아래)

### 안 건드릴 것
- `domain/finance.js`(재수출 모음) — 새 함수는 `reportDetail.js`가 `financeCore.js`에서 직접 불러서 추가 안 함. 근거: `reportDetail.js:4`가 이미 `financeCore.js`를 직접 부름.
- `financeOwnerDetail.js`·`monthSettlement.js` — `getCallDetailCommissionAmount`의 이름·인자·결과가 그대로라 안 바뀜. 근거: `grep getCallDetailCommissionAmount` 호출부 4곳(`financeOwnerDetail.js:103`, `monthSettlement.js:120`, `reportDetail.js:98`, `finance.test.js:155~156`).
- 1-B(운행 기록 서버 변환 두 벌)는 다음 슬라이스.

### 테스트
- 기존: `finance.test.js:155~156`(저장된 값 우선 7000 / 거래처 설정 10000), `report.test.js:118`(합계 17000), `report.test.js:202`(표시 "10%") — 그대로 통과해야 함.
- 추가: `report.test.js` — 저장된 값이 `direct 7000`이고 거래처 설정은 `10%`일 때, 보고서 표시가 **"7,000원"**(저장된 값 기준)인지 확인. 지금은 표시 쪽의 "저장된 값 우선" 경로를 검사하는 테스트가 없음.
- 플레이북 트리거(`domain/finance*`) 해당 — 저장·삭제·동기화 없음, 순수 계산만. 플레이북 §6 되돌려 실패 확인: 새 테스트는 동작 보존 확인용이라, 표시 함수가 저장된 값을 무시하도록 임시로 바꿨을 때 FAIL하는지 확인해 로그 첨부.

### §6 200줄
- `financeCore.js` 현재 218줄 — 이미 §6 예외("~250 한도, `getMonthlyDriverTotals` 응집", 파일 머리 주석). 판단 함수 추가·금액 함수 축소로 **약 218줄 유지**, 250 이하. 머리 주석의 줄 수 숫자만 실제 값으로 고침.
- `reportDetail.js` 현재 약 150줄 → 줄어듦.

### 실패 시 처리
- 새 저장·대비 장치 없음(§7 해당 없음). 테스트·브라우저 확인 실패 시 수정 착수지시서 작성 후 대기.

### 브라우저 확인 (보리)
- 보고서 → 세부 내역서: 수수료 있는 거래처의 수수료 금액·"10%"/"원" 표시가 수정 전과 같은지.
- 매출 화면 월 정산 수수료 금액이 수정 전과 같은지.

### 정정 (착수 후, 2026-10-10)
- 지시서 초안의 "`financeCore.js:30` 빈 설명 한 줄 삭제"는 AI 확인 착오 — 그 줄은 바로 아래 `getDetailPaymentSummary`의 설명이라 정상. **삭제 안 함**(범위에서 뺌).

### 진행 결과
- `financeCore.js` 218→216줄(머리 주석 숫자 고침), `reportDetail.js` 약 150→118줄, `report.test.js` 테스트 1건 추가.
- 로컬 `npm test` 전부 통과(단위 839 / 화면 365, 실패 0), `npm run typecheck` 오류 0.
- 바뀐 줄에 `any`·`unknown`·`@ts-ignore`·`@ts-expect-error` 없음.
- 되돌려 실패 확인: `detailCommissionLabel`이 저장된 값을 무시하도록 임시로 바꾼 뒤 실행 → 종료 코드 1(아래 원문), 원래대로 되돌린 뒤 다시 전부 통과.

```
$ node --test src/lib/report.test.js   (종료 코드: 1)
  ✔ 등록 거래처는 monthFareByClient, 미등록/미지정은 defaultBaseFare (0.2439ms)
  ✔ commissionSnapshot이 있으면 우선, 없으면 현재 거래처 설정 폴백 (0.2067ms)
  ✖ 수수료 표시도 금액과 같은 기준 — 저장된 값(직접 7000)이 거래처 설정(10%)보다 우선 (0.8849ms)
  ✔ isOff 레코드는 완전히 제외된다 (0.2054ms)
✖ buildDetailReport (14.8428ms)
▶ getReportShareCompanyName
ℹ suites 11
ℹ pass 28
ℹ fail 1
ℹ cancelled 0
ℹ skipped 0
ℹ todo 0
ℹ duration_ms 210.3959

✖ failing tests:

test at src\lib\report.test.js:141:3
✖ 수수료 표시도 금액과 같은 기준 — 저장된 값(직접 7000)이 거래처 설정(10%)보다 우선 (0.8849ms)
  AssertionError [ERR_ASSERTION]: Expected values to be strictly equal:
  
  '10%' !== '7,000원'
  
      at TestContext.<anonymous> (file:///C:/Users/znlsl/OneDrive/%EB%AC%B8%EC%84%9C/GitHub/%F0%9F%A7%AAteat/react-app/src/lib/report.test.js:154:12)
      at Test.runInAsyncScope (node:async_hooks:227:14)
      at Test.run (node:internal/test_runner/test:1382:25)
      at Suite.processPendingSubtests (node:internal/test_runner/test:960:18)
      at Test.postRun (node:internal/test_runner/test:1522:19)
      at Test.run (node:internal/test_runner/test:1447:12)
      at async Suite.processPendingSubtests (node:internal/test_runner/test:960:7) {
    generatedMessage: true,
    code: 'ERR_ASSERTION',
    actual: '10%',
    expected: '7,000원',
    operator: 'strictEqual',
    diff: 'simple'
  }
```

- react-app `255da47`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "전과 같아"·최종 `[x]` 승인(2026-10-10).
