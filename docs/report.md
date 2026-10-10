# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 4-C — 이력형 주석 줄이기: `domain/` `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` C, 메모 규칙 "코드 주석은 결론 1~2줄"(2026-09-11). 앞 슬라이스 4-B `[x]`(react-app `1b8a08c`).
4번 계획: 4-A `store/` `[x]` → 4-B `app/` `[x]` → **4-C `domain/` (이번)** → 4-D `components/` → 4-E `lib/`.

### 정리 기준 (4-A·4-B와 같음)
- **지움**: 작업 이력 — Step·재감사·슬라이스 X·로드맵 번호·migration 문서 번호·커밋 번호, "예전엔 ~였다" 경위.
- **남김·줄임**: 지금 코드가 **왜** 이렇게 생겼는지 — 1~2줄로. 지금 사실과 다른 설명은 고치거나 지움.
- **안 건드림**: 타입 설명, 상태 조각 뜻의 "슬라이스", `// @ts-check`, 그리고 **이 폴더에서 특히**:
  - **돈·세금 계산식 설명**(수수료·부가세·원천징수·산재보험·고정노선 단가×횟수 등) — 이력 낱말만 빼고 설명 내용은 그대로.
  - **§6 200줄 예외 이유 주석**(예: `financeCore.js:4` "§6 예외(216줄, ~250 한도 내) …") — AGENTS §6이 요구하는 표시라 남김(날짜·경위만 줄임).
  - `docs/sot.md`(도메인 규칙 정본) 절 번호를 가리키는 주석 — 규칙 근거라 남김.
- **실행 코드 변경 0.**

### 현재 상태 (코드 확인)
`domain/` 35개 파일에 이력형 주석: `calendar`, `calendarBadges`(7), `calendarViewDate`, `call-details`, `callDetail`, `carSettingsScope`, `cars`, `clientPaymentTerms`, `clientTypes`, `clients`(7), `dailyInspectionItems`, `dailyInspectionMonth`, `dateKey`, `day-record`, `dayRecordTypes`, `driverSelfRevenue`, `drivers`, `expenseTypes`, `expenseVehicleRouting`, `expenses`, `finance.fixtures`, `finance`, `financeCore`, `financeOwnerDetail`, `financeOwnerExpenseSweep`, `financeReceivables`, `financeTaxInvoiceEntries`, `financeTypes`, `invoices`, `money`, `monthSettlement`, `payments`, `practicePresets`, `practiceSettings`, `workDataTombstones` (`.js`).

### 목표 상태 (기대 동작)
1. 위 기준대로 35개 파일 주석 정리.
2. **실행 코드·타입 설명·계산식 설명 내용 변경 0.** 동작·금액 변화 없음.

### 건드릴 파일 (35개) — 위 목록 (`src/domain/`)
- 파일 수가 기준보다 많지만 주석만 고쳐 폴더 하나로 묶음(4-A·4-B와 같은 판단).

### 안 건드릴 것
- `domain/`의 다른 파일, 테스트 파일(`finance.fixtures.js`는 테스트 자료지만 운영 폴더라 포함), 다른 폴더(4-D·4-E).

### 검증 (실행 코드가 안 바뀐 증명)
- 35개 파일 모두 HEAD 판과 작업 판을 `esbuild --minify-whitespace --minify-syntax`(주석·공백만 지움, 이름 유지)로 바꿔 글자 하나까지 같음을 확인.
- `typecheck` 오류 0·엄격 검사 목록 438 그대로, `npm test`·`build` 통과.
- 플레이북 트리거(`domain/finance*`·`domain/receivables*`) 해당 — 주석만 바뀜(esbuild 비교가 근거).

### §6 200줄
- 35개 파일 모두 줄어듦(200줄 넘는 `financeCore.js` 216줄·`clients.js`·`day-record.js`·`expenses.js`도 줄어듦).

### 실패 시 처리
- 새 저장·대비 장치 없음(§7 해당 없음). 실패 시 수정 착수지시서 작성 후 대기.

### 브라우저 확인 (보리, 휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
- 주석만 바뀌어 화면·금액 변화 없음. 가볍게: 달력 월 합계·매출 화면 이번 달 금액이 전과 같은지.

### 진행 결과
- 35개 파일 주석 정리: 187줄 삭제·66줄 추가. 남은 이력 낱말 0곳(`finance.fixtures.js:24` `보리운수`는 테스트 자료 글자라 그대로).
- 계산식 설명 내용은 그대로 두고 이력 낱말·날짜·"원본 vanilla도 같은 한계" 같은 경위만 뺌. §6 예외 주석은 남기되(`clients.js`·`financeCore.js`·`monthSettlement.js`) 경위·옛 줄 수만 뺌.
- **지시서와 다른 점 1곳**: `day-record.js`의 "206줄, §6 예외" 줄은 이번 정리로 파일이 199줄이 되어 예외가 필요 없어져 지움.
- **실행 코드 같음 확인**: 35개 파일 모두 HEAD 판과 작업 판 비교(주석·공백만 제거, 이름 유지) 글자 하나까지 같음.
- 로컬 `npm test` 전부 통과(단위 832 / 화면 365), `typecheck` 오류 0, 엄격 검사 목록 438 그대로, `build` 성공.
- react-app `cbc34e8`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-10).
