# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 89-A 아무도 안 읽는 변경 기록장(dirty journal) 지우기 + 88-A가 남긴 `memoryOnly` `[x]`

> **진행(2026-10-19)**: 보리 "착수지시서에 잇는거 작업진행해줘, 확인할 점은 권하는대로"(89-B는 89-A 바로 다음 따로). 코드 커밋 react-app `553c458`.
> - 교차검증(AGENTS §1): 파일·사용처 모두 코드와 같음. 고친 것: `batchWrites.js` 기록장 갈래는 `:56-69`가 아니라 `:57-70`(1줄 차이).
> - 목표 1 grep(`dirtyJournal|DirtyJournal|planDirtyWrite|clearDirty|hasDirty|markDirty|getDirtyDomains|allEntriesCloudMemoryOnly|memoryOnly`, `src` 전체): 운영 코드는 `hydrate.js` 옛 키 문자열 1곳만. 테스트엔 옛 키 문자열이 목표 4·새 테스트용으로 2곳(`hydrate.test` 기록장 키 준비, `app-store.test` 새 테스트) — 함수·변수 이름은 0건.
> - 운영 코드: `lib/dirtyJournal.js` 삭제. `batchWrites.js` 72→49(기록장 갈래·`allEntriesCloudMemoryOnly`·`syncToCloud` 인자 삭제, 머리 주석), `app-store.js` 175→174(`memoryOnly`·`syncToCloud` 꺼내기·넘기기 삭제, 옵션 타입은 89-B까지 그대로), `hydrate.js` 199→199(`clearDirty` 2줄 삭제, `dropLegacyOutbox` → `dropLegacyKeys` 두 키 — 주석 지시서 문구 그대로), `atomicPersist.js` 주석 1줄.
> - **지운 테스트**: `dirtyJournal.test.js` 파일째, `app-store.test` "commitBatch — dirty journal 쓰기 실패는 도메인/journal/state/notify/Supabase 전부 그대로 둔다"(1개 테스트, 기록장 쓰기 실패만 봄 — 두 키 원자성은 `atomicPersist.test`가 계속 봄. 이 파일에서만 쓰던 `withFailingSetItem` 도우미도 같이 지움).
> - **뺀 단언·준비 줄(같은 테스트의 다른 단언은 그대로)**: `hasDirty` 단언 — `App.guestDurable` 5, `cloudMemorySave` 4, `pendingWorkDataWrites` 3, `vehicleMutations` 4. `hasDirty` 전 값 준비 — `App.guestDurable` 2, `pendingWorkDataWrites` 3, `vehicleMutations` 2. 기록장 키 원문 비교 — `vehicleMutations` 준비 5·단언 5, `App.clientsCars` 6줄(스냅샷 `jA/jB` 포함), `owner-state.persistRoundtrip` 칸 1·단언 3, `owner-state.schemaFail` 칸 2·단언 2·`markDirty` 준비 2·"기록장 있음" 전제 1. `hydrate.test` 재시도 테스트의 `markDirty`·`getDirtyDomains` 2줄(나머지 단언 그대로, describe 이름 "dirtyJournal 통합 …" → "hydrate 재시도 — …"). `App.guestDurable`의 `settleGuestDirty` 도우미(기록장 지우기만 함)·부르는 7곳·`clearDirty('guest')` 1줄. 테스트 이름·메시지의 dirty 문구만 고침.
> - `App.test:689·915` `waitUntil(() => !hasDirty(...))`: 테스트 끝의 기다림이고 그 뒤 단언이 없어 **삭제**(지킬 관찰 대상 없음 — 로그인 저장은 기록장을 안 써서 늘 바로 통과하던 줄).
> - `atomicPersist.test` "임의의 key도 도메인 키와 함께 섞어 쓸 수 있다": 키만 `reactPracticeAtomicTest:<owner>`로 바꿈(원자 쓰기 검사 그대로).
> - 새 테스트 `app-store.test` "비회원 저장 뒤 기록장 키가 생기지 않는다" + 88-A 테스트에 기록장 키 준비·"지워진다" 1줄. 고치기 전 코드(HEAD를 임시 폴더에 풀어 두 테스트 파일만 바꿔 넣음 — 지운 파일이 있어 revert-check 스크립트 대신)에서 **둘 다 실패**. 지시서와 다른 점: 88-A 테스트에 더한 단언은 "고치기 전에도 통과(회귀 방지용)"라고 적었지만 실패함 — 옛 `clearDirty`는 키를 지우지 않고 빈 값으로 남겼기 때문. 고친 뒤 둘 다 통과.
> - `npm test` 1004 + 497 통과(지운 테스트만큼 줄어듦), 타입 검사·빌드 통과.
> - 문서: `docs/testing-playbook.md` "dirty journal" 다섯 곳 정리(필수 실패 지점 6번을 빼고 7~17번을 6~16번으로 당김 — 번호를 가리키는 곳 grep 0건). 문서 커밋 때 함께.
> - 개발 서버(비회원): 거래처 2개 추가·일지 1건 저장 → 새로고침 뒤 그대로, `reactPracticeDirtyJournal:*` 키 0개, 콘솔 오류 없음(첫 실행은 서버 준비 전이라 빈 결과, 다시 돌려 확인).
> - push 뒤 CI verify·deploy 초록(run 39200859907).
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서(교차검증 줄 번호 정정 1건) ②새 파일 0, 새 저장 키 0(옛 키 지우기만) ③타입 꼼수 0 ④바뀐 운영 파일 200줄 이내(`hydrate.js` 199) ⑤테스트 — 빠진 단언은 기록장만 보던 것, 지운 테스트 1개는 기록장 쓰기 실패만 보던 것, 새 테스트 1개 + 단언 1줄(둘 다 고치기 전 실패) ⑥공용 상태값 — `syncToCloud`는 옵션 타입만 남김(89-B) ⑦목표 1 grep 함수·변수 이름 0건.
>   지시서 오류 1건(데스크톱 AI): 88-A 테스트에 더한 단언을 "고치기 전에도 통과"라고 썼으나 옛 `clearDirty`가 키를 빈 값으로 남겨 실패했음 — 진행 기록대로가 맞음.
> - 배포본 로그인 확인(2026-10-19, 내장 브라우저): 새 묶음 파일로 열림, 있던 `reactPracticeDirtyJournal:<id>` 키 **사라짐**(남은 키는 테마·로그인 2개), 차량 3·거래처 1·연동 기사 1 그대로, 오늘 일지 2회 저장 → 새로고침 뒤 그대로·기록장 키 안 생김 → 비워서 되돌림. 알림 닫기는 되돌릴 화면이 없어 하지 않음(비회원 저장 새 테스트로 갈음). 콘솔 오류는 주소 직접 열기 404뿐.
> - 보리 최종 승인(2026-10-19).

보리 "승인 다음 착수지시서 작성해줘"(2026-10-19, 88-A `[x]` 직후). 순서는 로드맵 "88~94" 줄 — 88 다음 89. `memoryOnly`는 88-A 리뷰에서 나와 89에 합침 `[확인: 2026-10-19 보리]`.
**이번엔 89-A만.** 89-B(이제 아무 일도 안 하게 되는 `syncToCloud`/`sync` 옵션을 넘기는 곳 17개 파일 48곳에서 빼기)는 따로 — 아래 "확인할 점".
플레이북 트리거(`store/**`) 해당 — `docs/testing-playbook.md` 읽음, §1 "dirty journal" 항목은 이번에 문서에서 뺌(아래 건드릴 파일).

### 쉬운 요약
- 저장할 때마다 휴대폰 저장소에 "아직 서버에 안 보낸 것 있음" 기록을 남기는데, 이 기록을 읽는 곳이 하나도 없습니다(앱 켤 때 지우기만 함).
- 그 기록장과 기록하는 줄을 지웁니다. 88-A 때 쓸모없어진 계산 한 줄도 같이 지웁니다.
- 화면·저장 동작은 그대로이고, 저장할 때 휴대폰 저장소 쓰기가 한 번 줄어듭니다.

### 현재 상태 (코드 확인)
- 쓰기: `store/batchWrites.js:56-69` — `syncToCloud`이고 로그인 메모리 전용이 아니면 `planDirtyWrite`(`lib/dirtyJournal.js:63`)로 `reactPracticeDirtyJournal:<ownerKey>` 키를 도메인 값과 함께 저장. 비회원은 모든 저장, 로그인은 `dismissedNotifications` 등 메모리 전용 아닌 도메인에서 쌓임(배포본 로그인 계정에 키 실제로 있음, 2026-10-19 확인).
- 읽기: 운영 코드 0곳 — `hasDirty`·`getDirtyDomains`·`markDirty`는 테스트만 부름(grep, `src` 운영 파일 중 `dirtyJournal.js` 밖 사용은 `planDirtyWrite`(batchWrites)·`clearDirty`(hydrate)뿐).
- 지우기: `lib/hydrate.js:72·165` `clearDirty(ownerKey)`.
- `store/app-store.js:145` `const memoryOnly = allEntriesCloudMemoryOnly(...)` — 쓰던 줄(`scheduleCloudSync`)이 88-A에서 지워져 안 씀. `allEntriesCloudMemoryOnly`(`store/batchWrites.js:34`)는 이 줄 말고 사용처 0(운영·테스트 grep).
- `syncToCloud`가 `buildBatchWrites`에서 하는 일은 기록장 쓰기뿐(`batchWrites.js:56`) → 89-A 뒤엔 아무 일도 안 함(89-B).

### 목표 상태 (기대 동작)
1. `lib/dirtyJournal.js`·`lib/dirtyJournal.test.js` 삭제. 운영·테스트 전체 grep `dirtyJournal|DirtyJournal|planDirtyWrite|clearDirty|hasDirty|markDirty|getDirtyDomains|allEntriesCloudMemoryOnly|memoryOnly` 0건(단 4번의 옛 키 문자열 1곳 제외).
2. `buildBatchWrites`는 도메인 값 쓰기만 계산 — `syncToCloud` 인자와 기록장 갈래 삭제. `commitBatch`는 `syncToCloud`를 더 꺼내지 않음(옵션 타입은 89-B 전까지 그대로 — 넘기는 곳이 깨지지 않게).
3. 저장 결과(Store·도메인 키·notify 횟수·서버 호출)는 지금과 같음.
4. 앱 켤 때(hydrate 성공) 옛 기록장 키 `reactPracticeDirtyJournal:<ownerKey>`도 옛 편지함 키와 함께 지움 — 88-A의 `dropLegacyOutbox`를 `dropLegacyKeys`로 바꿔 두 키를 지움, `clearDirty` 두 줄 대신.

### 건드릴 파일
- 지움: `lib/dirtyJournal.js`(97줄), `lib/dirtyJournal.test.js`.
- 고침(운영):
  - `store/batchWrites.js` — `planDirtyWrite` 가져오기·`:56-69` 갈래·`allEntriesCloudMemoryOnly`(`:30-36`) 삭제, `syncToCloud` 인자 삭제, 머리 주석 2줄에서 "dirty journal"·"dirty" 빼기.
  - `store/app-store.js` — `allEntriesCloudMemoryOnly` 가져오기, `:145` `memoryOnly`, `buildBatchWrites`에 넘기는 `syncToCloud`, 구조분해의 `syncToCloud` 삭제. `:143` 주석 "localStorage·dirty에" → "localStorage에".
  - `lib/hydrate.js` — `clearDirty` 가져오기·두 줄 삭제, `dropLegacyOutbox` → `dropLegacyKeys`(두 키). 머리 주석 "dirty journal 재적용, " 빼기.
  - 주석만: `store/atomicPersist.js:3` "도메인 값과 dirty journal을" → "여러 키를".
- 고침(테스트, 언급 수): `App.guestDurable` 10, `vehicleMutations.test` 18, `cloudMemorySave.test` 5, `pendingWorkDataWrites.test` 7, `App.clientsCars` 6, `owner-state.schemaFail` 5, `hydrate.test` 4, `App.test` 3, `app-store.test` 1(+ 그 `describe`), `atomicPersist.test` 1, `owner-state.persistRoundtrip` 1. `batchWrites`를 직접 부르는 테스트가 있으면 `syncToCloud` 인자만 뺌.
  - 규칙(88-A와 같음): 기록장만 보던 단언·준비 줄(`hasDirty`·`markDirty`·`journal` 스냅샷)만 뺀다. 같은 테스트의 다른 단언은 그대로. 통째로 기록장만 보던 테스트는 지우고 이름을 진행 기록에.
  - `atomicPersist.test` "임의의 key(예: dirty journal 키)도…"는 원자 쓰기 검사라 남김 — 키 이름만 다른 임의 키(예: `reactPracticeAtomicTest:<owner>`)로.
  - `app-store.test` "commitBatch — dirty journal 쓰기 실패는…" `describe`는 기록장 쓰기 실패만 보므로 지움(두 키 원자성은 `atomicPersist.test`가 계속 봄).
  - `App.test:689·915` `waitUntil(() => !hasDirty(...))`가 기다림 지점이면 같은 시점을 보는 다른 관찰값(예: `hydration.status`)으로, 필요 없으면 삭제 — 판단을 진행 기록에.
- 문서: `docs/testing-playbook.md:11·15·31·45·63`에서 "dirty journal" 항목 빼기(규칙 취지는 그대로).
- 코드 주석 최종 문구(`hydrate.js` `dropLegacyKeys` 위): `// 옛 보낼 편지함·변경 기록장 키는 보내지 않고 지운다 — 더 쓰지 않음(로드맵 88·89).`

### 안 건드릴 것
- `syncToCloud`/`sync` 옵션을 넘기는 곳(`store/commitHelpers.js`·`store/owner-state.js`·`lib/*` 13개) — 89-B.
- `isCloudMemoryOnly`·`CLOUD_MEMORY_ONLY_DOMAINS`(`batchWrites.js`) — 도메인 값 쓰기 판단에 계속 씀(`:47`, `owner-state.js:39`).
- 비회원 기기의 옛 키 `reactPracticeDirtyJournal:guest` — 비회원은 hydrate가 없어 남음. 작은 키 하나라 그대로(지우는 새 코드 안 만듦).
- 일지 저장 실패 대비 장치(`pendingWorkDataWrites`·`durableWriteGuard`) — 91번.
- `outboxTypes.js`·`outboxErrors.js`·`outboxCommit.js` — 88-B(59번 때).

### 검증
- `npm test`·타입 검사·빌드 통과. 목표 1 grep 0건을 진행 기록에.
- 뺀 단언·지운 테스트 목록을 진행 기록에(§5 테스트 진실성용).
- 새 테스트 1개(`store/app-store.test.js`): "비회원 저장 뒤 기록장 키가 생기지 않는다" — `commitCars('guest', …)` 뒤 `reactPracticeDirtyJournal:guest` 없음. 고치기 전 코드에서 실패, 고친 뒤 통과(revert-check).
- 목표 4(옛 키 지우기)는 88-A 테스트 "옛 편지함 키가 있어도…"에 기록장 키 준비·"지워진다" 단언 1줄씩 더함(고치기 전 코드도 `clearDirty`로 지우므로 이 단언은 회귀 방지용).
- 개발 서버(비회원): 차량·거래처 추가, 일지 저장 → 새로고침 뒤 그대로, `reactPracticeDirtyJournal:*` 키 안 생김, 콘솔 오류 없음.

### 파일 길이
- 줄어들기만 함: `store/batchWrites.js` 72 → 약 50, `store/app-store.js` 175 → 약 173, `lib/hydrate.js` 199 → 약 197, `store/atomicPersist.js` 그대로. 운영 코드 약 120줄 삭제.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(지우기만, 옛 키 지우기는 88-A 함수 확장).
- 테스트를 고치다 기록장 말고 다른 동작이 걸린 단언이 나오면 빼지 말고 멈추고 보고. 타입·빌드 실패가 목록 밖 파일로 번지면 고치지 않고 수정 계획을 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤)
1. 로그인 계정으로 앱 켜기 → 차량·거래처·기사 목록 그대로.
2. 알림 하나 닫기·일지 한 건 저장 → 새로고침 뒤 그대로.
3. (AI가 배포본에서) 휴대폰 저장소에 `reactPracticeDirtyJournal` 키가 없어진 것 확인.

### 확인할 점
1. 89-B(`syncToCloud`/`sync` 옵션 빼기, 17개 파일 48곳 — 동작 변화 없는 기계적 정리)를 언제 할지 — **89-A 바로 다음 따로**를 권합니다(한 번에 하면 바뀌는 파일이 30개를 넘어 확인이 어려움). 다른 안: 93번(테스트만 살리는 함수 정리) 때 같이.
