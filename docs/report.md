# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 3-B-1 — `unknown` 정리: 지출 기록(주유·정비·기타) 묶음 `[x]`

보리 요청 2026-10-10(로드맵 밖), 순서 보리 확인 "추천순대로". 근거: `docs/code-cleanup-review.md` F, AGENTS §6(금지)·§5 3번(2026-10-10 추가). 앞 슬라이스 3 `[x]`(react-app `3fbac44`).

### 전체 계획 (127곳, 3번에 나눔)
`unknown`은 서로 값을 주고받는 파일끼리 같이 바꿔야 타입 검사가 맞아서, 폴더가 아니라 **연결된 묶음**으로 나눔.
| 슬라이스 | 묶음 | 곳 |
|---|---|---|
| **3-B-1 (이번)** | 지출 기록: `domain/fuelRecords`·`maintenanceRecords`·`miscExpenseRecords`, `lib/syncExpenseItems`·`syncExpenseRecords` | 53 |
| 3-B-2 | 백업·복원·저장소 읽기: `lib/guestBackup`·`memberBackup`·`memberRestore`·`cloudStorage`·`cars`·`clients`·`drivers`·`invoices`·`dailyInspections`·`syncWorkData`·`dayLogCloudCommit` 등 — 시험에서 값 모양 확인 코드 몇 줄이 필요해 보임 | 약 25 |
| 3-B-3 | 나머지: `domain/` 13개 파일(`practicePresets`·`money` 등), `app/`·`components/` | 약 49 |

### 현재 상태 (코드 확인)
- 5개 파일에 `unknown` 53곳. 서버 표(`fuel_records` 등)의 칸 설명, 서버에 보낼 줄을 만드는 함수의 결과 설명, 서버 줄을 지출 항목으로 바꾸는 함수의 입력 설명.
- 같은 뜻의 허용된 표시가 이미 있음: `JsonValue`·`JsonRecord`(`lib/pendingWorkDataWritesTypes.js:37~40`, "서버·저장소에서 온 JSON 값").

### 목표 상태 (기대 동작)
1. 5개 파일의 `unknown` 0곳. 바꾸는 규칙:
   - 칸 설명 `{unknown}`·`?: unknown` → `JsonValue`(함수 입력은 `JsonValue|undefined`)
   - `Record<string, unknown>` → `Record<string, JsonValue|undefined>`
   - 서버에 보낼 줄 만드는 함수(`build…RecordRow`) 결과 → `Record<string, JsonValue> & { raw: JsonRecord }`, `syncExpenseItems.js`의 `KindSpec.buildRow` 결과 → `Record<string, JsonValue>`, `ServerRow.raw` → `JsonValue`
   - `JsonValue`·`JsonRecord` 설명 줄이 없는 파일엔 1줄씩 추가
2. **바뀌는 건 설명(주석 속 타입)뿐 — 실행되는 코드는 한 글자도 안 바뀜.** 동작·화면·저장 변화 없음.

### 건드릴 파일 (5개)
`src/domain/fuelRecords.js`, `src/domain/maintenanceRecords.js`, `src/domain/miscExpenseRecords.js`, `src/lib/syncExpenseItems.js`, `src/lib/syncExpenseRecords.js`

### 안 건드릴 것
- 3-B-2·3-B-3 묶음 파일. 이 5개 파일을 쓰는 다른 파일 — 시험에서 타입 검사 오류 0건이라 안 바꿔도 됨(아래).
- 테스트 파일.

### 사전 시험 (코드 안 건드리고 임시 복사본에서, 2026-10-10)
- 위 규칙대로 바꾼 복사본: `tsc --noEmit` 오류 **0건**.
- 엄격 검사 목록(`tsconfig.strict-inventory.json`, 플레이북 §8): **450 → 438건으로 줄어듦**. 줄어든 12건은 세 테스트 파일(`fuelRecords`·`maintenanceRecords`·`miscExpenseRecords.test.js`)이 `row.raw`를 "모름"으로 받아 생기던 것. 늘어난 것 없음.
- 5개 파일 남은 `unknown` 0곳.

### 테스트
- 새 테스트 없음(설명만 바뀜). 로컬 `npm test`·`typecheck`·엄격 검사 목록 수(438 이하)·`build`로 확인.
- 플레이북 트리거(`lib/sync*`가 서버 저장 경로) 해당 — 실행 코드 변경 0이라 저장 동작 변화 없음. 근거: `git diff`에서 바뀐 줄이 전부 `/** … */`·`*` 주석 줄인지 확인해 결과에 적음.

### §6 200줄
- 5개 파일 모두 200줄 이하. 설명 줄 1~2줄 늘 수 있음 — 넘으면 보고.

### 실패 시 처리
- 새 저장·대비 장치 없음(§7 해당 없음). 실패 시 수정 착수지시서 작성 후 대기.

### 브라우저 확인 (보리, 휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
- 로그인 계정으로 주유·정비·기타 지출 1건씩 추가 → 껐다 켜도 그대로.
- 그중 1건 고치기·지우기 → 껐다 켜도 반영된 그대로.

### 진행 결과
- 5개 파일 `unknown` 53곳 → 0곳(58줄 추가·53줄 삭제, 추가 5줄은 `JsonValue`·`JsonRecord` 설명 줄).
- 실행 코드 변경 0: `git diff`에서 바뀐 줄은 전부 `/** … */`·`*` 설명 줄이거나, 실행 줄 안의 `/** @type {…} */` 설명 부분만(7줄 — 괄호 안 `Record<string, unknown>`→`Record<string, JsonValue|undefined>`).
- 로컬 `npm test` 전부 통과(단위 832 / 화면 365), `typecheck` 오류 0, 엄격 검사 목록 450→**438**, `build` 성공. 5개 파일 모두 200줄 이하.
- react-app `7fe99a1`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-10).
