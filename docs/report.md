# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 4-E-1 — 이력형 주석 줄이기: `lib/` 저장·동기화 묶음 `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` C. 앞 슬라이스 4-D-2 `[x]`(react-app `28fd903`). 4번 계획의 마지막 폴더 `lib/`(64개)를 **4-E-1 저장·동기화 37개(이번) / 4-E-2 나머지 27개**로 나눔.

### 쉬운 요약
- 서버 저장·동기화를 맡은 37개 파일의 주석에서 "재감사 7차(FAIL 지적 1번)", "사용자 지시 3번", "슬라이스 C(2026-09-01)" 같은 작업 기록을 뺀다.
- **"이걸 안 지키면 기록이 지워진다·되살아난다" 같은 이유 설명은 남긴다** — 이 폴더 주석의 핵심이라 내용은 그대로, 짧게만.
- 주석만 바뀌어 저장·동기화 동작은 그대로다.

### 현재 상태 (코드 확인)
- 37개 파일에 작업 기록 주석(근거: 4번 공통 검색 패턴). 가장 많은 곳: `outboxFlush.js` 머리 17줄, `durableStorage.js` 머리 14줄, `pendingWorkDataWritesTypes.js`·`hydrateMergeTypes.js`·`directMutations.js`.
- 지금과 다른 설명도 있음: `hydrateMerge.js:5` "호출부인 cloudSync.js"(그 파일 없음, 근거: `ls src/lib/cloudSync.js` 없음), `syncExpenseRecords.js:3` "syncAll이 부르는"(syncQueue.js는 syncAll을 안 씀, 근거: `syncQueue.js:2` 주석·코드), `workData.js:28~35`의 `syncWorkData.js`·`syncDeletedWorkDates.js` 설명(1-B·2-A에서 삭제·축소).

### 목표 상태 (기대 동작)
1. 아래 "부록 — 바뀔 주석 최종 문구"대로 37개 파일 주석이 바뀐다.
2. 버그 방지 이유(지워짐·부활·중복 저장·세션 섞임을 막는 이유)는 문장 수만 줄이고 뜻은 남는다.
3. 실행 코드·타입 설명 변경 0. 저장·동기화 동작 변화 없음.

### 건드릴 파일 (37개, `src/lib/`)
`cloudSession`, `cloudStorage`, `dailyLogRemoval`, `dayLogCloudCommit`, `directMutationActions`, `directMutations`, `dirtyJournal`, `durablePatchSchema`, `durableStorage`, `durableWriteGuard`, `hydrate`, `hydrateEmployedDriver`, `hydrateEmployedDriverCar`, `hydrateMerge`, `hydrateMergeCars`, `hydrateMergeClients`, `hydrateMergeTypes`, `hydrateMergeWork`, `hydrateOwnerDriverExpenses`, `hydrateVehicleDayLogs`, `mutationOutbox`, `outboxDriverMerge`, `outboxErrors`, `outboxFlush`, `outboxReconcile`, `outboxRollback`, `outboxTypes`, `ownerSaveQueue`, `pendingLogOwner`, `pendingRetryPulse`, `pendingWorkDataWrites`, `pendingWorkDataWritesTypes`, `singleFlight`, `syncExpenseItems`, `syncExpenseRecords`, `syncQueue`, `workData` (`.js`)
- 파일 수가 기준보다 많지만 주석만 고쳐 묶음(4-A~4-D와 같은 판단). 공용 스타일·값 변경 없음.

### 안 건드릴 것
- `lib/` 나머지 27개(4-E-2), 테스트 파일, 타입 설명 본문(`@typedef`·`@param`의 타입), `docs/sot.md` 절 번호(`hydrateEmployedDriver.js:102`).

### 검증
- 37개 파일 모두 HEAD 판과 작업 판을 `esbuild --minify-whitespace --minify-syntax`(주석·공백만 지움, 이름 유지)로 비교해 글자 하나까지 같은지 확인.
- `typecheck` 오류 0, 엄격 검사 목록 438 이하, `npm test`·`build` 통과.
- 플레이북 트리거 전부 해당(`lib/cloud*`·`hydrate*`·`*outbox*`·`*commit*`) — 주석만 바뀜(esbuild 비교가 근거).

### 파일 길이
- 37개 모두 줄어듦. 200줄인 `directMutations.js`·`hydrate.js`, 199줄 `dayLogCloudCommit.js`, 197줄 `mutationOutbox.js`·`outboxFlush.js`도 줄어듦(예: `outboxFlush.js` 197 → 약 185).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 실패하면 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법 (휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
1. 로그인 → 일지에 운행 1건·주유 1건 추가 → 앱 껐다 켜기 → 둘 다 그대로 있으면 성공.
2. 그 운행 1건 지우기 → 껐다 켜기 → 지워진 채면 성공(되살아나지 않음).
3. 메뉴 → 기사 관리에서 기사 1명 정보 고쳐 저장 → 껐다 켜도 바뀐 값이면 성공.

### 진행 결과
- 부록 표대로 37개 파일 주석 정리(105줄 추가·284줄 삭제). 표의 바꿀 문구를 모두 찾아 적용(못 찾은 것 0). 남은 작업 기록 낱말 0곳.
- **실행 코드 같음 확인**: 37개 파일 모두 HEAD 판과 비교(주석·공백만 제거, 이름 유지) 글자 하나까지 같음.
- 줄 수 예: `outboxFlush.js` 197→183, `directMutations.js` 200→191, `hydrate.js` 200→198.
- 로컬 `npm test` 전부 통과(단위 832 / 화면 365), `typecheck` 오류 0, 엄격 검사 목록 438 그대로, `build` 성공.
- react-app `0bc69f8`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-11).

---

### 부록 — 바뀔 주석 최종 문구
"앞 낱말만 삭제"는 줄 맨 앞(또는 괄호 속)의 기록 낱말만 빼고 나머지 문장은 그대로라는 뜻.

| 위치 | 바뀔 문구(최종) |
|---|---|
| `cloudSession.js:2~5` | `// 로그인 세션·세대(epoch) 상태와 그 판정 함수. hydrate·outboxFlush·directMutationActions가 getSessionEpoch()로 "시작 때와 같은 세션인지" 재확인한다.` |
| `cloudSession.js:66~71` | ` * 다단계 원격 작업 중 매 await 직후 부른다. 세션이 바뀌었으면 StaleSessionError를 던져 남은 단계를 멈추고, outboxFlush·syncQueue는 이 에러면 op를 그대로 보존한다.` |
| `cloudSession.js:90~101` | ` * 로그아웃. 세대를 올려 이전 계정의 늦게 끝난 hydrate·flush 결과를 버리고, 그 owner의 singleFlight 항목을 지워 재로그인이 옛 요청에 합류하지 않고 새 hydrate를 시작하게 한다.` |
| `cloudStorage.js:3~5` | `// practice 스냅샷 localStorage I/O 원시 함수(hydrate·직접 mutation·outbox가 공유).` |
| `dailyLogRemoval.js:2` | 앞 "로드맵 0-3-A:" 삭제 |
| `dayLogCloudCommit.js:2·6` | "슬라이스 D + Step 9 슬라이스 A:", "(0-3-A)" 삭제 |
| `directMutationActions.js:5` | `// 기사 초대·상태변경·삭제와 차량·거래처 삭제는 mutation outbox /` (다음 줄 그대로) |
| `directMutationActions.js:31·36·69·99` | "슬라이스 A~C", "슬라이스 B:", "슬라이스 C:" 삭제 |
| `directMutations.js:2~12` | `// Supabase를 직접 부르는 실행기 함수. 즉시 시도와 outbox 재시도가 같은 함수를 쓴다. 삭제는 이미 없는 행 삭제도 성공이라 중간부터 다시 돌아도 안전하다.` / `// 모든 함수는 captured를 받아 원격 await마다 assertSessionStillCurrent로 세션을 재확인한다.` |
| `directMutations.js:37·41·64·185` | "슬라이스 C(2026-09-01):", "예전처럼", "슬라이스 C:", "슬라이스 B 보완(2026-09-01):" 삭제 |
| `directMutations.js:95·100·105` | "사용자 지시 8번(4차 재작업에서 오류 처리 보강) —", "4차 재작업 정정:" 삭제, "알려진 한계(마이그레이션 필요, 이번 라운드에서 적용하지 않음):" → "알려진 한계(DB 변경 필요):" |
| `dirtyJournal.js:3~7` | `// owner별·도메인별 dirty journal(localStorage) — 새로고침해도 "아직 서버에 못 보낸 변경"이 남는다. revision은 commit마다 오르는 카운터.` |
| `dirtyJournal.js:62~67` | ` * commitBatch가 도메인 값과 저널을 한 번의 writeAllOrNothing으로 묶도록, 다음 저널 값만 계산해 { key, value }로 돌려준다(여기서는 쓰지 않는다).` |
| `durablePatchSchema.js:2~8` | `// durable 큐 내부 값(dateKey·patch·callDetails)을 EffectivePatch 계약대로 런타임 검증한다 — { "2026-08-31": [] } 같은 값이 통과하면 기존 일지가 지워진다.` / `// JsonValue로 받아 실제로 좁힌 뒤에만 필드를 읽는다. 콜상세 검증은 callDetailSchema.js.` |
| `durablePatchSchema.js:20~24` | ` * fixedCount/palletCount/fixedRouteCounts는 0 이상의 유한한 정수만(DayDraft 계약).` |
| `durableStorage.js:2~15` | `// durable(localStorage) 저수준 읽기·쓰기. "읽기 실패"와 "정상적으로 비어 있음"을 구분해 돌려주는 게 핵심이다 —` / `// 섞으면 빈 객체 위에 다시 써서 다른 날짜 원문을 지운다. 내부 값은 durablePatchSchema.js, dateKey는 domain/dateKey.js로 검증한다.` |
| `durableStorage.js:53·70` | "재감사 7차(FAIL 지적 1번) —", "재감사 7차(FAIL 지적 2번) —" 삭제 |
| `durableWriteGuard.js:2~6` | `// pending의 fallback·unsafe를 owner/date 키로 본다(owner 하나 성공이 전체를 정상으로 만들지 않게).` / `// unsafe는 잘못된 dateKey/patch라 자동 재시도 대상이 아니고, 화면 이동 경고와 다시 들어올 때 덮어 보여 주기용이다.` |
| `hydrate.js:2~4` | `// hydrate 전체 — 조회 전부 성공 판정, dirty journal 재적용, single-flight/세대 보호, outbox tombstone·pending 재적용.` |
| `hydrate.js:128~129` | `    // supabaseId 있는 전 차량(main+기사) daily_logs/transport 병합.` / `    // tombstone은 메인만 적용(서브는 서버 직접 저장이라 tombstone을 쓰지 않는다).` |
| `hydrateEmployedDriver.js:74·102` | "로드맵 5-B-1:" 삭제, "9-B-0(docs/sot.md §0 …):" → "docs/sot.md §0(연동 기사 앱의 개인정보·설정):" |
| `hydrateEmployedDriverCar.js:2` | `// hydrateEmployedDriver.js에서 분리.` |
| `hydrateMerge.js:3~6` | `// hydrateFromSupabase의 순수 병합 함수(조회 → 검증 → 병합 → 커밋 중 병합만). localStorage/store는 쓰지 않는다. 타입 정본은 hydrateMergeTypes.js.` |
| `hydrateMerge.js:65~71` | ` * DRIVER_KEYS 9개 필드만 명시적으로 채운다 — 로컬 객체를 통째로 펼치면 정본 밖 필드가 섞여 다음 initialize에서 drivers 전체가 스키마 실패한다.` / ` * row.id(숫자일 수 있음)는 String으로 바꾼다(isPersistedDriver는 문자열 id 요구).` |
| `hydrateMerge.js:96` | "슬라이스 B 보완(2026-09-01):" 삭제 |
| `hydrateMergeCars.js:12~20` | `// CAR_KEYS에 있는 필드만 정본 타입으로 정규화한다 — raw(JSONB)를 통째로 펼치면 스키마와 다른 값 하나로` / `// 다음 새로고침 때 cars 전체가 사라진다. 검증기는 느슨하게 하지 않고 producer만 맞춘다.` |
| `hydrateMergeCars.js:32~40` | `// insuranceOn/logEnabled/driverLinkEnabled/shareRevenueWithOwner/archived는 false를 심지 않는다 — "없음"과 "false"는 뜻이 다르다` / `// (shareRevenueWithOwner는 없음 = 공유). raw에 boolean이 없으면 키를 생략해 소비 쪽 기본값이 적용되게 한다.` |
| `hydrateMergeCars.js:58`, `hydrateMergeClients.js:30` | "슬라이스 C(2026-09-01):" 삭제 |
| `hydrateMergeClients.js:2~5` | `// CLIENT_KEYS에 있는 필드만 정본 타입으로 정규화한다(이유는 hydrateMergeCars.js 상단과 같다).` |
| `hydrateMergeTypes.js:2~13` | `// hydrateMerge*.js가 실제로 읽고 쓰는 upstream(Supabase 행·로컬 값) 모양(타입 전용 모듈).` / `// raw(JSONB) 자리는 모양을 미리 알 수 없어 JsonRecord를 쓴다.` |
| `hydrateMergeTypes.js:22~28` | `// LocalCar/LocalClient는 도메인 정본 타입 그대로, RawCarBackup/RawClientBackup은 raw(JSONB)라 Partial — 병합 쪽이 필드마다 typeof로 다시 확인한다.` |
| `hydrateMergeWork.js:60~62` | `  // dailyRows가 배열이면(빈 배열 포함) 서버 날짜 맵이 정본이다 — 로컬 dateKey를 깔면 지운 날짜가 hydrate 뒤 부활한다.` / `  // fallback은 dailyRows가 배열이 아닐 때(조회 실패 등)만.` |
| `hydrateOwnerDriverExpenses.js:44·46` | "로드맵 5-B-2:" 삭제, "5-B-2부터 연동 여부로 나누지 않음(호출부 hydrate.js 유지용)" → "연동 여부로 나누지 않음(호출부 모양 유지용)" |
| `hydrateVehicleDayLogs.js:2` | `// hydrate가 차량별 daily_logs를 logId 맵으로 합친다.` |
| `mutationOutbox.js:2~6` | `// durable mutation outbox: 차량·거래처 삭제(tombstone)와 기사 변경(mutation)을 localStorage에 남겨, 원격 실패·새로고침에도` / `// 의도가 남아 자동 재시도되게 한다. 여기는 순수 계산과 읽기만 — 쓰기는 호출부가 writeAllOrNothing으로 도메인 값과 함께 한다.` |
| `mutationOutbox.js:16·36·118·140` | "4차 재작업(사용자 지시 1번) —", "사용자 지시 3번 —", "(재감사 1번)", "(재감사 1번)" 삭제 |
| `outboxDriverMerge.js:2~10` | `// driverLink/upsert 병합: 최초 op의 id와 previousDriverSnapshot은 이어받고 배정 내용만 최신으로 —` / `// 매번 교체하면 확정 실패 때 처음(A)이 아니라 중간 값(B)으로 롤백되고, id가 바뀌면 flush 결과를 못 찾는다.` |
| `outboxErrors.js:2~5` | `// 던지는 시점부터 전용 Error 클래스를 써서, 호출부가 instanceof StaleSessionError처럼 바로 구분한다.` |
| `outboxFlush.js:2~18` | `// mutation outbox 재시도 엔진. owner별 single-flight라 여러 번 겹쳐 불려도 안전하다.` / `// 세션은 flush 시작·op 실행 직전·원격 await 직후마다 재확인하고, 바뀌었으면 조용히 멈춘다.` / `// op마다 OUTBOX_RESULT를 담은 Map을 돌려준다. 확정 실패(재시도해도 같은 결과)는 outbox에서 빼고, 기사 upsert면 낙관적 로컬 값을 되돌린다(outboxRollback.js).` |
| `outboxFlush.js:48·55·138·139` | "슬라이스 E:", "사용자 지시 8번:", "사용자 지시 3번:", "(1번)" 삭제 |
| `outboxReconcile.js:2~6` | `// hydrate 서버 병합 결과에 아직 반영 안 된 outbox 의도를 겹쳐 적용하는 순수 함수 — 서버 삭제가 덜 끝난 레코드는 tombstone으로 부활을 막고,` / `// pending 상태변경은 서버값 대신 로컬 의도를 다시 적용한다.` |
| `outboxRollback.js:2~6` | `// 기사 upsert op 하나의 "로컬 drivers 쓰기 + outbox 제거"를 원자적으로 묶는 두 경로(성공 반영·확정 실패 롤백).` / `// hydrate 때 재적용은 outboxReconcile.js가 맡는다.` |
| `outboxRollback.js:36·56` | "(사용자 지시 5번)", "4차 재작업(사용자 지시 1번) —" 삭제 |
| `outboxTypes.js:2~5` | `// outbox·직접 mutation 파일들이 공유하는 JSDoc 타입(런타임 코드 없음) — 한 곳에 둬야 모양이 어긋나지 않는다.` |
| `outboxTypes.js:23` | "(차주 또는 기사, 로드맵 7-C)" → "(차주 또는 기사)" |
| `ownerSaveQueue.js:2`, `syncQueue.js:2` | "슬라이스 E:" 삭제 |
| `pendingLogOwner.js:3` | `// 메인 일지는 기존 \`${ownerKey}\` 키를 그대로 쓴다.` |
| `pendingRetryPulse.js:2` | "재감사 11차 —" 삭제 |
| `pendingWorkDataWrites.js:22·54·114·144` | "재감사 9차 —", "(재감사 6차)", "(재감사 5차)", "(재감사 6차)", "(재감사 7차)", ", 재감사 8차" 삭제 |
| `pendingWorkDataWritesTypes.js:2~3` | `// pending 큐 타입 정본(타입 전용 모듈).` |
| `pendingWorkDataWritesTypes.js:7` | "(재감사 9차 FAIL 지적 2번 — durable에" → "(durable에" |
| `pendingWorkDataWritesTypes.js:13~22` | ` * durable 큐가 저장하는 "완성된 Effective Patch" — useDayDraft.js commitNow가 draft 전체로 만들어 5개 필드를 항상 채운다.` / ` * 전부 필수다: 하나만 있는 값을 정상으로 통과시키면 saveDayRecord가 나머지를 기본값으로 덮어 기존 일지를 지운다. 횟수는 number만.` |
| `pendingWorkDataWritesTypes.js:32·43·49·54` | "재감사 7차(FAIL 지적 1번) —", "재감사 6차(FAIL 지적 1번) —", "(실측 확인)", "재감사 7차(FAIL 지적 2번) —" 삭제 |
| `singleFlight.js:1~4` | `// restoreSessionOnBoot()/hydrateFromSupabase()를 owner별 single-flight로 — StrictMode가 두 번 불러도 실제 요청은 한 번.` |
| `singleFlight.js:32~38` | ` * key 하나를 강제로 지운다 — 로그아웃 때 그 owner의 진행 중 hydrate를 지워 두면 재로그인이 옛 요청에 합류하지 않고 새로 시작한다.` / ` * 옛 Promise는 끝까지 돌지만 결과는 세대(epoch) 검사가 버린다.` |
| `syncExpenseItems.js:3` | "로드맵 5-B:" 삭제 |
| `syncExpenseRecords.js:3~7` | `// 정비/주유/기타 비용 서버 저장(세 함수가 표·칸 이름만 다르다). 차량은 호출부가 planExpenseVehicleTargets로 나눠 넘긴다.` |
| `workData.js:2~5` | `// 운행 기록(workData) 저장소 I/O + domain/의 day-record·call-details·payments 재수출 배럴.` |
| `workData.js:28~35` | `// workData 삭제와 빈 날 삭제 목록(tombstone)을 같은 commitBatch로 기록한다. 지웠던 날짜에 다시 입력이 들어오면 tombstone을 바로 지운다.` |
| `workData.js:79~82` | `// day-record.js·call-details.js가 같은 타입 이름을 내보내 export *가 충돌하므로 함수 이름을 나열해 재수출한다.` |
