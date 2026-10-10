# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 2-B — 안 쓰는 함수 지우기 `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` B2. 앞 슬라이스 2-A `[x]`(react-app `183fe39`).

### 현재 상태 (코드 확인)
정의만 있고 어디서도 안 부르는 함수·값 11개. 근거: 각 이름 `grep -rlw <이름> src` → **자기 파일 1개만** 나옴(2026-10-10 재확인).

| 파일 | 지울 것 | 같이 안 쓰게 되는 것(같은 파일 안) |
|---|---|---|
| `domain/money.js` | `calcSupplyVat`, `monthFareSummary` | 없음 |
| `domain/finance.fixtures.js` (테스트 자료) | `OVERLAP_LINKS` | 없음 |
| `lib/cloudStorage.js` | `writeJson` | 없음 |
| `lib/dirtyJournal.js` | `clearDirtyDomain` | 없음 |
| `lib/mainDayLogRouting.js` | `mainCarSupabaseId` | 없음 |
| `lib/ownerFinance.js` | `persistWorkDataByLogId` | 없음(`saveWorkData`는 101줄에서 계속 씀) |
| `lib/outboxCommit.js` | `commitWithOutboxAndFlush`(약 35줄) | 불러오기 5개(`storageKeyFor`·`writeAllOrNothing`·`OUTBOX_RESULT`·`planOutboxAppend`·`flushMutationOutbox`), 타입 설명 2줄, 지운 경로를 설명하던 머리 주석 |
| `store/persistDayRecordLegacy.js` | `coercePersistedFuelItem`, `coercePersistedMaintItem`, `coercePersistedMiscItem` | 보조 함수 3개(`labelOr`·`moneyOrZero`·`stringOrNumberOrZero`) — 이 셋만 썼음. `hasValidMoney`는 남는 검사 함수가 계속 씀 |

### 목표 상태 (기대 동작)
1. 위 표의 "지울 것"과 "같이 안 쓰게 되는 것" 삭제. 남는 코드는 한 글자도 안 바꿈(`outboxCommit.js` 머리 주석만 남는 `commitLocalOnly` 설명 1~2줄로).
2. 지운 뒤 `oxlint`로 "안 쓰는 이름" 경고가 새로 생기지 않았는지 확인.
3. **동작·화면 변화 없음.**

### 건드릴 파일 (8개)
`domain/money.js`, `domain/finance.fixtures.js`, `lib/cloudStorage.js`, `lib/dirtyJournal.js`, `lib/mainDayLogRouting.js`, `lib/ownerFinance.js`, `lib/outboxCommit.js`, `store/persistDayRecordLegacy.js`
- 슬라이스 크기 기준(1~3개 파일)보다 많음. 다만 **전부 지우기만** 하고 파일마다 몇 줄씩이라 한 번에 묶음. 나누길 원하면 "돈 쪽 3개(`money`·`finance.fixtures`·`ownerFinance`) / 저장 쪽 5개"로 나눔.

### 안 건드릴 것
- `store/commitHelpers.js` `commitWorkDataDeletedDates` — 2-A 뒤 운영 코드에선 안 쓰지만 `lib/workData.test.js:36`이 테스트 준비 단계에서 씀. 지우려면 테스트를 고쳐야 해서 이번엔 제외(삭제 목록 정리 때 같이).
- `export`만 불필요하게 붙어 있는 이름 26개(파일 안에서는 씀) — 효과가 작아 안 함.
- 테스트에서만 쓰는 이름 55개 — 테스트를 위해 연 것이라 정상.

### 테스트
- 지우는 함수를 직접 검사하는 테스트 없음(위 grep 근거 — 테스트 파일에도 이름이 안 나옴). 새 테스트 없음.
- 플레이북 트리거 해당(`store/**`, `lib/*commit*`, `lib/cloud*`, `localStorage` 직접 쓰는 `cloudStorage.js`). 저장·삭제·동기화 코드 중 **호출 0건인 것만** 지우므로 저장 동작 변화 없음. 로컬 `npm test`·`typecheck`·`build` 전부 통과로 확인.

### §6 200줄
- 삭제만 있음. 8개 파일 모두 200줄 이하이고 줄어듦.

### 실패 시 처리
- 새 저장·대비 장치 없음(§7 해당 없음). 실패 시 수정 착수지시서 작성 후 대기.

### 브라우저 확인 (보리, 휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
- 로그인 계정: 일지 운행 1건 추가 → 껐다 켜도 그대로.
- 주유·정비 지출 1건 추가 → 껐다 켜도 그대로.
- 매출 화면 이번 달 금액이 전과 같은지.

### 진행 결과
- 8개 파일에서 함수·값 11개 + 같이 안 쓰게 된 보조 함수 3개·불러오기 5개·타입 설명 2줄 삭제. `outboxCommit.js` 머리 주석(지운 경로 설명 15줄)은 남은 `commitLocalOnly` 설명 1줄로. 합계 222줄 삭제·1줄 추가.
- `oxlint`(8개 파일) 새 경고 0, 로컬 `npm test` 전부 통과(단위 832 / 화면 365, 실패 0), `npm run typecheck` 오류 0, `npm run build` 성공.
- react-app `b644be8`(보리 지시로 커밋·push AI가 함), CI verify 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-10).
