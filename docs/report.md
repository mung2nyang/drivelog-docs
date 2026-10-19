# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 76 타입 규칙 위반 2곳 정리 — `any`·`Function`을 정확한 타입으로 `[x]`

> **진행(2026-10-19)**: 보리 "착수지시서에있는거 작업해줘". 코드 커밋 react-app `cda7770`.
> - 교차검증(AGENTS §1): 파일·줄(`hydrateVehicleDayLogs.js:26-28`, `syncFlushListeners.js:7-8`, `pendingWriteRetryListeners.js:13-16`, `hydrateMergeTypes.js:6`, `hydrateEmployedDriver.js:56`)·금지 표시 grep 3줄 모두 코드와 같음, 고친 것 없음.
> - `hydrateVehicleDayLogs.js` 56→57: `throwIfAnyHydrateError`를 `Record<string, SupabaseQueryError>`로 바꾸자 타입 검사가 `fetchDaily`·`fetchTransport` 오류 타입(`{ message?: string }|null`)과 안 맞다고 해서, 목표 1대로 그 둘도 `SupabaseQueryError`로 맞춤. 부르는 쪽 `hydrate.js`는 안 바꿔도 타입 검사 통과.
> - `syncFlushListeners.js` 26→29: `// @ts-check` + `EventTargetLike`(`pendingWriteRetryListeners.js`에서 가져옴), `documentTarget`은 `EventTargetLike & { hidden?: boolean }`. 새 타입 오류 없음.
> - `git diff`: 바뀐 줄은 JSDoc 주석과 `// @ts-check`뿐(코드 줄 0). 운영 파일 금지 표시 grep 0건. `npm test` 1044 + 500 통과(테스트 파일 안 고침), 타입 검사·빌드 통과. 새 테스트 없음·revert-check 해당 없음(지시서대로).
> - push 뒤 CI verify·deploy 초록(run 39195815855). §5 리뷰(데스크톱 AI): 바뀐 줄 JSDoc·`@ts-check`뿐(코드 0), 금지 표시 grep 0건, 범위 = 지시서.
> - 보리 최종 승인(2026-10-19) `[x]`. 다음 88번(보리 판단: 60보다 먼저).

보리 확정 순서(2026-10-18) 81 → 61 → 62 → 63 → **76** → 60 → 59. 63번(63-B·63-C 포함) 끝난 뒤 다음. 로드맵 76 `[확인: 2026-10-14 보리 — 로드맵 기록 지시]`(64~76 코드 리뷰 묶음).

### 쉬운 요약
- 코드 두 곳에 규칙(AGENTS §6)에서 금지한 "아무 타입이나 됨" 표시(`any`, `Function`)가 남아 있습니다. 이걸 실제로 들어오는 값에 맞는 정확한 타입으로 바꿉니다.
- 앱 동작은 바뀌지 않습니다. 타입 검사가 실수를 더 잘 잡게 되는 정리입니다.

### 현재 상태 (코드 확인)
- 남은 금지 표시는 이 2곳뿐(운영 파일 grep `\{any\}|<any>|, any>|\bFunction\b` — 테스트 제외 3줄):
  - `src/lib/hydrateVehicleDayLogs.js:28` `@param {(errors: Record<string, any>) => void} params.throwIfAnyHydrateError`. 실제 함수는 `hydrateMerge.js:28` `throwIfAnyHydrateError(labeledErrors)`, 오류 타입은 `hydrateMergeTypes.js:6` `SupabaseQueryError`(`{ message: string, code?: string }|Error|null`). 같은 함수를 받는 `hydrateEmployedDriver.js:56`은 이미 `Record<string, SupabaseQueryError>`로 적혀 있음. 이 파일의 `fetchDaily`·`fetchTransport`(`:26-27`) 오류 타입은 `{ message?: string }|null`로 따로 적혀 있음.
  - `src/app/syncFlushListeners.js:7-8` `windowTarget`·`documentTarget`의 `addEventListener: Function, removeEventListener: Function`. 같은 모양을 `pendingWriteRetryListeners.js:13-16` `EventTargetLike`가 정확한 타입으로 이미 적어 둠. 이 파일은 `// @ts-check`도 없음(§6: 바꾸는 운영 파일은 `@ts-check` 필수).
- 부르는 곳: `hydrate.js:72·135`(`mergeVehicleDayLogsFromServer`에 `throwIfAnyHydrateError` 넘김), `providers.jsx:15` `attachSyncFlushListeners(window, document)`.

### 목표 상태 (기대 동작)
1. `hydrateVehicleDayLogs.js`에 `any`가 없다. `throwIfAnyHydrateError` 매개변수는 `SupabaseQueryError`를 쓴다(`hydrateEmployedDriver.js:56`과 같게). 타입 검사가 `fetchDaily`·`fetchTransport` 오류 타입과 안 맞다고 하면 그 둘도 `SupabaseQueryError`로 맞춘다 — 부르는 쪽(`hydrate.js`)은 바꾸지 않는 것이 목표, 바꿔야 하면 멈추고 보고.
2. `syncFlushListeners.js`에 `Function`이 없고 `// @ts-check`가 있다. 이벤트 등록 타입은 `pendingWriteRetryListeners.js`의 `EventTargetLike`와 같은 모양(가져다 쓰거나 같은 typedef), `documentTarget`엔 `hidden?: boolean` 그대로.
3. 운영 파일 grep으로 금지 표시(`any`/`Function`) 0건.
4. 앱 동작·테스트 기대값 변화 없음.

### 건드릴 파일
- `src/lib/hydrateVehicleDayLogs.js` — JSDoc 타입 줄만(목표 1). 코드 줄은 안 바뀜.
- `src/app/syncFlushListeners.js` — `// @ts-check` 1줄 + JSDoc 타입 줄만(목표 2). 새 타입 오류가 나오면 이 파일 안에서만 고침.
- `docs/roadmap.md` 76번 줄 `[x]`는 완료 때 데스크톱 AI가 함.
- 공용 값 변경 없음(타입 표시만) — 영향 목록 해당 없음.

### 안 건드릴 것
- `// @ts-check` 없는 다른 운영 파일(로드맵 76 줄: `docs/code-cleanup-review.md` F에 적힌 13개) — 이번 범위 밖, 이 파일 1개만.
- `hydrate.js`·`hydrateMerge.js`·`hydrateEmployedDriver.js`·`providers.jsx`·`pendingWriteRetryListeners.js` 코드.
- 테스트 파일(`syncFlushListeners.test.js` 등) — 기대값 그대로, 안 고쳐도 통과해야 함.

### 검증
- `npm test`·타입 검사·빌드 통과. 기존 `syncFlushListeners.test.js`·hydrate 관련 테스트 그대로 통과.
- 동작이 안 바뀌었다는 증명: 바뀐 줄이 주석(JSDoc)과 `// @ts-check`뿐인지 `git diff`로 확인(코드 줄 0).
- 새 테스트는 만들지 않음 — 동작 변화가 없어 실패시킬 대상이 없음(revert-check 해당 없음). 대신 금지 표시 grep 0건을 보고.

### 파일 길이
- `hydrateVehicleDayLogs.js` 56 → 약 57. `syncFlushListeners.js` 26 → 약 30. 둘 다 200 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(타입 표시만).
- `@ts-check`를 켜서 타입 오류가 이 파일 밖까지 번지거나, 부르는 쪽 코드를 바꿔야 하면 고치지 않고 원인·수정 계획을 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법
- 화면 변화 없음. 배포 뒤 평소처럼 로그인해 달력·일지가 열리고(서버에서 차량별 일지 불러오기), 인터넷을 껐다 켜도 오류 안내가 없으면 됨.

### 확인할 점
- 없음.
