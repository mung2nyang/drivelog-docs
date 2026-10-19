# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 88-A 아무도 안 넣는 outbox(보낼 편지함) 걷어내기 — 연결 끊기·파일 지우기 `[x]`

> **진행(2026-10-19)**: 보리 "착수지시서 작성한거 작업진행해줘, 확인할 점 2개는 권하는대로"(1 옛 기록 그냥 지움, 2 88-B는 59번 때). 코드 커밋 react-app `88247d7`(41개 파일, +90 / −1857줄).
> - 교차검증(AGENTS §1)으로 고친 것: ① 주석 정리 목록에 `hydrateMerge.js:92`(`reconcileDrivers` 언급)·`outboxTypes.js:2·9` 빠져 있어 함께 정리. ② 지시서의 "옛 키 지우는 자리 1곳" → hydrate 성공 갈래가 2개(연동 기사·차주, 옛 편지함 보내기도 2곳이었음)라 같은 함수 `dropLegacyOutbox` 하나를 두 갈래에서 부름. ③ 테스트 언급 수는 grep 방식 차이로 다르나(예: `directMutationActions.test` 실제 20줄) 파일 목록은 같음.
> - 목표 1 grep(`scheduleCloudSync|flushCloudSync|flushMutationOutbox|hasPendingOps|reconcile(Cars|Clients|Drivers)|isTombstoned|getPendingMutation|SyncFlushBridge|attachSyncFlushListeners|syncQueue|mutationOutbox|outboxFlush|outboxReconcile|outboxRollback|outboxDriverMerge|syncFlushListeners`, `src` 전체·테스트 포함) **0건**.
> - 운영 코드: 7개 파일 삭제. `hydrate.js` 199→199(§5 리뷰 정정: 진행 기록의 181은 틀림 — 지운 줄만큼 `dropLegacyOutbox` 6줄이 늘어 그대로, 200줄 이내)(편지함 보내기 2곳·`reconcile*` 3곳 삭제, `dropLegacyOutbox` 추가 — 주석 지시서 문구 그대로), `hydrateEmployedDriver.js`(`reconcileClients` 삭제), `syncVehiclesClients.js` 135→95(§5 리뷰 정정: 91 아님)(`isTombstoned`·`syncOne*`·`syncVehicles`·`syncClients` 삭제, `findExistingVehicle`의 안 쓰게 된 `ownerKey` 인자도 뺌), `app-store.js`(`scheduleCloudSync` 1줄+가져오기, `syncToCloud` 옵션은 `buildBatchWrites`가 계속 써서 그대로), `providers.jsx`(`SyncFlushBridge`), `App.jsx` 2줄, `useAppSession.js` 2줄. 주석만: `cloudSession.js`·`directMutations.js`·`outboxErrors.js`·`hydrateMerge.js`·`outboxTypes.js`·`testSupport` 2개.
> - **지운 테스트(통째로 편지함만 보던 것)**: 파일 5개 — `mutationOutbox.test.js`, `outboxFlush.test.js`, `syncQueue.test.js`, `syncFlushListeners.test.js`, `vehicleTombstoneReregister.test.js`(2개 테스트, 편지함 tombstone + 지운 `syncVehicles`만 봄). 개별 테스트 3개 — `hydrate.test.js` "활성 tombstone이 있는 차량은 서버 응답에 있어도 hydrate 결과에서 제외된다", "대기 중인 기사 상태변경은 서버 값이 아니라 로컬 pending 값으로 유지된다", `App.unsafeQuotaFailSyncSpy.test.js` "재감사 15차 — 양성 대조: 정상 일지 커밋은 scheduleCloudSync를 1회 부른다".
> - **뺀 단언(같은 테스트의 다른 단언은 그대로)**: `scheduleCloudSync` 호출 횟수 — `App.legacyCallInit` 2, `App.unsafeQuotaFailSyncSpy` 2, `pendingWorkDataWritesSyncSpy` 5, `vehicleMutations.syncSpy` 7. `hasPendingOps(...) false` — `directMutationActions.test` 20. 편지함 저장 키 불변 — `App.clientsCars` 8줄(스냅샷 `oA/oB` 포함), `owner-state.persistRoundtrip`·`owner-state.schemaFail`의 `outbox`·`pendingOps`·`tombstones`·`schedule` 칸과 그 비교, `schemaFail` 편지함 시드·"tombstone 있음" 전제 1줄. `employedDriverClients.test`는 `reconcileClients` 대신 병합 결과를 그대로 단언(기대값 같음). 편지함 비우기 대기(`await flushCloudSync()`) 줄과 `mock.module('../lib/syncQueue.js')` 등록 삭제. 테스트 이름·메시지의 `scheduleCloudSync`·outbox 문구만 고침.
> - 새 테스트 1개(`hydrate.test.js` "옛 편지함 키가 있어도 기사·차량은 서버 병합 결과 그대로, 키는 지워진다" — 키 삭제, 서버 차량 그대로, 기사 1명·서버 값 `pending`, 서버로 안 보냄). 고치기 전 코드(HEAD를 임시 폴더에 풀어 이 테스트만 바꿔 넣음 — 지운 파일이 있어 revert-check 스크립트 대신)에서 실패, 고친 뒤 통과.
> - `npm test` 1010 + 497 통과(지운 테스트만큼 줄어듦), 타입 검사·빌드 통과. 중간에 `schemaFail`의 "tombstone 있음" 전제 1줄을 놓쳐 1개 실패 → 같은 규칙(편지함 전제)으로 그 줄만 빼고 통과.
> - 개발 서버(비회원): 거래처 2개 추가·일지 1건 저장 → 새로고침 뒤 그대로, 편지함 키 없음, 콘솔 오류 없음. **로그인 계정 확인(옛 키 손으로 넣고 새로고침 → 키 사라짐)은 AI가 로그인할 수 없어 못 함** — 새 테스트로만 확인, 배포 뒤 보리 확인 필요.
> - push 뒤 CI verify·deploy 초록(run 39198835073).
> - §5 리뷰(데스크톱 AI): ①범위 — 지시서 목록 + 교차검증에서 더한 주석 2곳(`hydrateMerge.js`·`outboxTypes.js`)뿐 ②새 파일·저장 키 0(옛 키 지우기만) ③타입 꼼수 0 ④바뀐 운영 파일 모두 200줄 이내(`hydrate.js` 199) ⑤테스트 — 빠진 단언은 `hasPendingOps`·`scheduleCloudSync` 등 편지함만 보던 것, 같은 테스트의 다른 단언 그대로, 새 테스트 1개 ⑥공용 상태값 — 해당 없음 ⑦목표 1 grep 0건 확인.
>   **작은 남은 것 1개**: `store/app-store.js:145` `const memoryOnly = allEntriesCloudMemoryOnly(...)`가 유일하게 쓰던 줄(`scheduleCloudSync`)이 지워져 이제 아무도 안 씀 → 이 변수·가져오기와 `store/batchWrites.js:34` `allEntriesCloudMemoryOnly`(운영·테스트 모두 다른 사용처 0)가 죽은 코드로 남음. 동작 영향 없음. → 보리 결정: 89번에 합침(로드맵 89에 적음), 88-A 수정 커밋 없음.
> - 배포본 로그인 확인(데스크톱 AI, 내장 브라우저, 보리 로그인 2026-10-19): ① 차량 3·거래처 1·연동 기사 1 그대로 ② 기사 이름 고쳐 저장 → 새로고침 뒤 그대로(차량 관리 기사 이름도 같이), 원래 이름으로 되돌림 ③ 오늘 일지 운행 2회 저장 → 새로고침 뒤 그대로(휴대폰 저장소엔 일지 없음 = 서버에서 읽음), 비워서 되돌림. 콘솔 오류는 주소 직접 열기 404(GitHub Pages 원래 동작)뿐.
>   **못 한 것**: 옛 편지함 키를 손으로 넣고 새로고침 — 휴대폰 저장소 쓰기가 데스크톱 권한 확인에서 막힘. 새 테스트(`hydrate.test.js`)로만 확인됨.
>   참고: 로그인 계정에도 `reactPracticeDirtyJournal:<id>` 키가 실제로 쌓여 있음(89번 근거).
> - 보리 최종 승인(2026-10-19). 옛 키 직접 확인은 새 테스트로 갈음.

보리 판단(2026-10-19): 60보다 88 먼저(① 데이터 위험). 로드맵 88 `(①)` — 백로그 "outbox 9개 파일 사실상 죽은 코드"를 번호로 올린 항목.
**이번엔 88-A만.** 88-B(남겨 둘 타입·오류·`commitLocalOnly`의 이름·자리 옮기기, 60여 파일 가져오기 줄 수정)는 따로 — 아래 "확인할 점" 2. 89번(`dirtyJournal.js`)은 88 뒤.

### 쉬운 요약
- 예전엔 서버 저장이 실패하면 "보낼 편지함"에 넣어 두고 나중에 다시 보냈습니다. 지금은 저장마다 서버에 바로 쓰고, 편지함에 넣는 곳은 하나도 없습니다.
- 그런데도 저장할 때마다 빈 편지함을 비우러 가고, 앱을 켤 때마다 편지함 기준으로 목록을 되돌리는 검사가 돕니다. 옛 편지함이 남은 휴대폰에선 그 되돌리기가 기사 목록을 빈 목록으로 덮을 수 있습니다.
- 편지함과 그 검사를 통째로 지웁니다. 화면·저장 동작은 그대로입니다.

### 현재 상태 (코드 확인)
- 넣는 곳 0: `planOutboxAppend`·`buildMutationOp`(`mutationOutbox.js:94·144`)을 부르는 운영 코드 없음(grep — `mutationOutbox.js` 안만). 마지막 넣기 함수 `commitWithOutboxAndFlush`는 `b644be8`(2026-10-10)에 "호출 0건"으로 이미 삭제.
- 그래도 도는 것:
  - 저장마다: `store/app-store.js:167` `scheduleCloudSync()` → `syncQueue.js:15-17` 600ms 뒤 `flushMutationOutbox`.
  - 백그라운드·온라인 복귀·페이지 닫힘: `providers.jsx:14-15` `SyncFlushBridge`(`App.jsx:62`) → `syncFlushListeners.js` → `flushCloudSync`. 로그아웃: `useAppSession.js:121` `flushCloudSync()`.
  - 앱 켤 때(hydrate): `hydrate.js:81·175` 편지함 있으면 보내기, `hydrate.js:122-125` `reconcileCars`·`reconcileClients`·`reconcileDrivers`, `hydrateEmployedDriver.js:168` `reconcileClients`. 편지함이 비면 이 셋은 받은 목록을 그대로 돌려줌(`outboxReconcile.js` — tombstone·pending 없으면 필터·덮기 없음).
  - 차량 저장: `syncVehiclesClients.js:24` `isTombstoned` 검사(편지함 비면 늘 false).
- 위험: `outboxRollback.js:13-15` `readDrivers`가 기사 목록을 못 읽으면 빈 목록에서 시작해 저장(`:22-28`) — 옛 편지함 기록이 남은 기기에서만 돎(`outboxFlush.js`가 부름).
- 옛 편지함 저장 키: `reactPracticeMutationOutbox:<ownerKey>`(`mutationOutbox.js:10·28-30`, 휴대폰 저장소).
- `syncVehiclesClients.js`의 `syncOneVehicle`·`syncVehicles`·`syncOneClient`·`syncClients`는 `outboxFlush.js:14`만 부름("outboxFlush 레거시용" `:3`). 살아 있는 건 `upsertVehicleFromList`·`upsertClientFromList`(`vehicleMutations.js`·`requestDriverInviteSave.js`·`clientCloudSave.js`).

### 목표 상태 (기대 동작)
1. 운영 코드에서 편지함 관련 부르기 0: `scheduleCloudSync`·`flushCloudSync`·`flushMutationOutbox`·`hasPendingOps`·`reconcile*`·`isTombstoned`·`getPendingMutation`·`SyncFlushBridge`·`attachSyncFlushListeners` grep 0건(테스트 포함).
2. 지우는 파일: `lib/mutationOutbox.js`, `lib/outboxFlush.js`, `lib/outboxReconcile.js`, `lib/outboxRollback.js`, `lib/outboxDriverMerge.js`, `lib/syncQueue.js`, `app/syncFlushListeners.js` + 그 파일만 검사하던 테스트(`mutationOutbox.test.js`, `outboxFlush.test.js`, `syncQueue.test.js`, `syncFlushListeners.test.js`).
3. 앱 켤 때 옛 편지함 저장 키(`reactPracticeMutationOutbox:<ownerKey>`)가 있으면 보내지 않고 지운다(확인할 점 1 — AI 권함 "그냥 지움"). 지우는 자리는 hydrate 성공 직후 1곳.
4. 남기는 것(88-B 전까지 이름·자리 그대로): `outboxTypes.js`, `outboxErrors.js`, `outboxCommit.js` — 다른 운영 코드가 씀(가져오기 각 60여·11·4 파일).
5. 저장·불러오기·로그아웃 동작, 화면은 그대로. 기사 목록이 편지함 때문에 비는 길이 없어짐.

### 건드릴 파일
- 지움(목표 2): 운영 7개 + 테스트 4개.
- 고침:
  - `store/app-store.js` — `scheduleCloudSync` 가져오기·부르기 삭제. `syncToCloud` 옵션은 남은 쓰는 곳 확인 후 그대로 두거나 이 파일 안에서만 정리(밖에 넘기는 곳 바꾸지 않음).
  - `app/providers.jsx` — `SyncFlushBridge` 삭제. `app/App.jsx` — 가져오기·`<SyncFlushBridge />` 1줄 삭제.
  - `app/useAppSession.js` — 로그아웃의 `flushCloudSync()` 줄 삭제.
  - `lib/hydrate.js` — 편지함 보내기 2곳·`reconcile*` 3곳 삭제(서버 병합 결과 그대로 사용), 옛 키 지우기 1곳(목표 3).
  - `lib/hydrateEmployedDriver.js` — `reconcileClients` 삭제.
  - `lib/syncVehiclesClients.js` — `isTombstoned` 검사·`syncOne*`·`syncVehicles`·`syncClients` 삭제, 머리 주석 정리.
  - 주석만: `cloudSession.js:2·23·64·95`, `directMutations.js:2·80-86`, `outboxErrors.js:13`, `testSupport/fakeSupabaseClient.js:1`, `testSupport/stubSupabaseClient.js:2` — 없어진 파일 이름만 빼기.
- 테스트 고침(언급 수): `App.clientsCars` 8, `App.guestDurable` 12, `App.legacyCallInit` 8, `App.test` 7, `App.unsafeQuotaFailSyncSpy` 14, `CalendarPage.test` 1, `directMutationActions.test` 1, `employedDriverClients.test` 1, `hydrate.test` 1, `pendingWorkDataWritesSyncSpy.test` 21, `vehicleMutations.syncSpy.test` 24, `vehicleTombstoneReregister.test` 3, `app-store.test` 3, `owner-state.persistRoundtrip.test` 9, `owner-state.schemaFail.test` 6, `persistDomainSchema.notifOff.test` 2.
  - 규칙: 지운 함수를 가짜로 바꿔 끼우던 줄(spy·mock)과 "편지함으로 안 갔다"만 보던 단언만 뺀다. 같은 테스트의 다른 단언(저장 결과·서버 호출·화면)은 그대로. 테스트 하나가 통째로 편지함만 보던 것이면 지우고 이름을 진행 기록에 적는다.
  - `vehicleTombstoneReregister.test`가 편지함 tombstone만 보던 것이면 지움 — 서버 삭제 뒤 재등록 동작 자체를 보는 단언이 있으면 남김.
- 코드 주석 최종 문구(`hydrate.js` 옛 키 지우는 줄 위): `// 옛 보낼 편지함 기록은 보내지 않고 지운다 — 저장은 서버 직접 1회라 더 쓰지 않음(로드맵 88).`

### 안 건드릴 것
- `outboxTypes.js`·`outboxErrors.js`·`outboxCommit.js`(`commitLocalOnly`) 이름·자리 — 88-B.
- `lib/dirtyJournal.js`(89번), `pendingWorkDataWrites`·`pendingWriteRetryListeners.js`(일지 저장 실패 재시도 — 편지함과 별개, `providers.jsx` `PendingWriteRetryBridge` 그대로).
- 서버(Supabase) 테이블·함수 — 바꿀 것 없음.
- 직접 저장 경로(`directMutationActions.js`·`directMutations.js` 코드, `vehicleMutations.js`·`clientCloudSave.js`·`requestDriverInviteSave.js`).

### 검증
- `npm test`·타입 검사·빌드 통과. 목표 1 grep 0건을 진행 기록에 붙임.
- 테스트에서 뺀 단언·지운 테스트 목록을 진행 기록에 적음(§5 테스트 진실성 리뷰용).
- 새 테스트 1개: 옛 편지함 키가 있는 상태로 hydrate → 키가 지워지고, 기사 목록은 서버 병합 결과 그대로(빈 목록으로 안 바뀜). revert-check로 고치기 전 코드에서 실패 확인.
- 개발 서버(로그인 계정 또는 비회원): 차량·거래처·기사 추가·수정·삭제, 일지 저장, 로그아웃·다시 로그인 후 목록 그대로, 콘솔 오류 없음. 휴대폰 저장소에 옛 키를 손으로 넣고 새로고침 → 키 사라짐.

### 파일 길이
- 줄어들기만 함: `hydrate.js` 199 → 약 190, `hydrateEmployedDriver.js` 181 → 약 180, `syncVehiclesClients.js` 135 → 약 85, `app-store.js` 177 → 약 175, `providers.jsx` 46 → 약 34, `App.jsx` 164 → 약 162, `useAppSession.js` 146 → 145. 운영 코드 약 600줄 삭제.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(지우기만, 옛 키 지우기 1줄 추가).
- 테스트를 고치다 편지함 말고 다른 동작이 걸려 있는 단언이 나오면 빼지 말고 멈추고 보고. 타입·빌드 실패가 목록 밖 파일로 번지면 고치지 않고 수정 계획을 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, 설치 앱·로그인 계정)
1. 앱을 켜면 차량·거래처·기사 목록이 전과 같다.
2. 기사 하나 수정·저장 → 앱 껐다 켜도 그대로.
3. 일지 한 건 입력 → 다른 기기(또는 웹)에서 보임.

### 확인할 점
1. 옛 편지함 기록이 남은 휴대폰 처리 — "그냥 지움"을 권합니다(서버가 정본, 마지막 넣기 함수는 10월 10일 이전부터 이미 안 쓰임). 다른 길 "한 번 보내 보고 지움"은 지울 보내기 코드를 한 번 더 살려 둬야 해서 권하지 않습니다.
2. 88-B(남겨 둘 타입·오류·`commitLocalOnly`를 "outbox" 아닌 이름·자리로 옮기기, 60여 파일 가져오기 줄만 바뀜)를 할지 — 동작 변화 없는 이름 정리라 59번(폴더 구조 옮기기) 때 같이 하는 걸 권합니다.
