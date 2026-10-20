# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-C 불러오기·공용 저장 장치를 `src/data/`로 + 88-B 이름 정리 `[x]`

보리 승인(2026-10-19, 59-B `[x]`). 59번 세 번째 조각(로드맵 59 `[확인: 2026-10-19 보리]`). 59-A·B와 같은 방식(옮기기·경로만, 상대 경로, `git mv`, 경로 줄만 바뀌는 200줄 넘는 파일은 §6 예외). 88-B `[확인: 2026-10-19 보리 — 59번 때]`를 여기서 같이.
플레이북 트리거(`lib/hydrate*`·`lib/*commit*`·`lib/*mutation*`) 해당 — 저장 경로의 **동작**은 안 바꾸고 위치·파일 이름만.

> **진행(2026-10-19)**: 보리 "착수지시서 확정,작업진행해". 코드 커밋 react-app `5cf6595`(188개 파일: 옮김 47(이름 바뀜 3 포함) + 경로·주석 고침 141).
> - 교차검증(AGENTS §1): 33개·짝 테스트 14개 모두 지시서대로 있음. 고친 것: `lib/` 운영 파일은 87이 아니라 85개 → 옮긴 뒤 남는 것 54가 아니라 52개(확인함). 지시서에 없던 옛 이름 5곳을 목표 3(0건)에 맞춰 고침 — 로그 머리표 `console.error('[outboxCommit] …')` → `[localCommit]`(`data/sync/localCommit.js:21`, 개발자 콘솔에만 보이는 글자)와 그걸 확인하는 기존 테스트 기대값 1줄(`lib/directMutationActions.test.js:35`), 주석 속 옛 경로 3줄(`lib/directMutationActions.js:10`, `components/day-log/useDayDraft.js:187`, `domain/taxInvoices.js:5`).
> - 경로는 59-A·B와 같은 대본. 머리 주석 4곳은 지시서 최종 문구 그대로. `package.json`은 이미 `src/data/**` 포함이라 안 바뀜.
> - 목표 3: 옛 경로 grep·`outboxCommit|outboxErrors|outboxTypes` grep 모두 0건.
> - 빌드 비교: 옮기기 전(`HEAD`를 따로 풀어 빌드)·후 48개 파일, 묶음 파일 이름의 해시를 지우고 비교 → 다른 곳은 `index` 묶음 안 로그 머리표 `[outboxCommit]`→`[localCommit]` 한 군데뿐. 그 머리표 때문에 해시가 바뀌어 다른 묶음들이 부르는 파일 이름도 따라 바뀜(내용은 같음).
> - `git diff -M`: 47개 모두 이름 바뀜으로 잡힘. 경로 아닌 바뀐 줄 9개 — 머리 주석 4, 주석 속 경로 3, 로그 머리표 1, 테스트 기대값 1.
> - `npm test` 1004 + 499 통과(같은 수), 타입 검사·빌드 통과.
> - 개발 서버(비회원): 차량·거래처 추가, 일지 한 건 저장 → 새로고침 뒤 셋 다 그대로, 콘솔 오류 없음.
> - 지시서 오류(데스크톱 AI): `lib/` 운영 파일 수를 87로 적음(실제 85), 개발자 콘솔 로그 머리표 `[outboxCommit]`와 주석 속 옛 경로 3줄을 목록에서 빠뜨림 — 교차검증이 채움.
> - push 뒤 CI verify·deploy 초록(run 39253667833).
> - §5 리뷰(데스크톱 AI): ①범위 = 옮김 47(이름 바뀜 3: `localCommit` R053·`saveErrors` R082·`syncTypes` R095 — 머리 주석·로그 머리표만 바뀌어 비율이 낮음) + 경로·주석 141 ②새 파일 0, 새 저장 키 0 ③타입 꼼수 0 ④줄 수 그대로(§6 예외) ⑤테스트 수 그대로, 바뀐 테스트 기대값 1줄은 로그 머리표 글자 ⑥공용 값 — 해당 없음 ⑦목표 1~5 대조(`lib/` 남은 운영 52개). 경로 아닌 바뀐 줄 9개 = 머리 주석 4·주석 속 경로 3·로그 머리표 1·테스트 기대값 1.
>   남은 것(기록만, 범위 밖): 옮긴 파일 안쪽 설명 주석에 "outbox 없이/쓰지 않는다" 같은 옛 표현이 남음(`data/sync/driverLinkRpc.js:2·35`·`localCommit.js:11`·`syncTypes.js:53`, `lib/directMutationActions.js:5·7·32·37`) — 동작 아님, 94번(주석 정리) 또는 해당 기능을 옮길 때.
> - 배포본 로그인 확인(2026-10-19, 내장 브라우저): 새 묶음 파일(`index-C7TV2Mkv.js` — 로그 머리표 한 글자 차이로 이름 바뀜, 교차검증 빌드 비교와 맞음), 로그인 유지, 차량 3·거래처 1·연동 기사 1 그대로(불러오기), 오늘 일지 2회 저장 → 새로고침 뒤 그대로 → 비워서 되돌림(저장), 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-20).

### 쉬운 요약
- `lib/` 87개 중 "앱 켤 때 불러오기"와 "여러 기능이 같이 쓰는 저장 장치" 33개를 `data/hydrate`·`data/sync`·`data/cloud`로 옮깁니다.
- 지금은 없는 "보낼 편지함(outbox)" 이름이 붙어 있는 공용 파일 3개는 옮기면서 실제 하는 일에 맞는 이름으로 바꿉니다(88-B).
- 한 기능만 쓰는 저장 함수(예: 거래처 저장)는 이번에 안 옮기고, 그 기능 폴더를 옮길 때(59-D~G) 그 기능의 `api/`로 갑니다.
- 화면·저장·불러오기 동작은 그대로입니다.

### 현재 상태 (코드 확인 — 파일마다 쓰는 곳을 grep으로 셈)
- 불러오기 10개: `hydrate`·`hydrateEmployedDriver`·`hydrateEmployedDriverCar`·`hydrateMerge`·`hydrateMergeCars`·`hydrateMergeClients`·`hydrateMergeTypes`·`hydrateMergeWork`·`hydrateOwnerDriverExpenses`·`hydrateVehicleDayLogs` — 서로와 앱 시작(`app/`)·기사 화면이 씀.
- 일지 저장 실패 대비 장치 10개(91번 대상, 위치만): `pendingWorkDataWrites`·`pendingWorkDataWritesState`·`pendingWorkDataWritesTypes`·`pendingRetryPulse`·`pendingLogOwner`·`durableStorage`·`durablePatchSchema`·`durableWriteGuard`·`logPendingLifecycle`·`dayDraftActivity`.
- 여러 기능이 같이 쓰는 서버 저장 도우미 7개: `dayLogCloudCommit`(일지·회원 복원·미수금)·`syncWorkData`·`dailyLogRemoval`·`mainDayLogRouting`(앱·일지·서류·홈)·`syncVehiclesClients`(차량·거래처·기사 초대)·`directMutations`·`driverLinkRpc`(앱 시작·불러오기·기사).
- 저장 공용 3개(88-B): `outboxCommit.js`(`commitLocalOnly` — 기기 저장만 하는 커밋, 4곳)·`outboxErrors.js`(세션 바뀜·영구 실패 오류 종류, 9곳)·`outboxTypes.js`(직접 저장 파일들이 같이 쓰는 타입 — `AppSession`·`DriverRecord`·`SessionCapture` 등, 20곳 넘음).
- 그 밖 공용 3개: `ownerSaveQueue`(같은 owner 저장 한 줄 세우기)·`singleFlight`(같은 일 동시 한 번 — `cloudSession`·`hydrate`가 씀)·`fetchAllRows`(서버에서 1,000줄 넘게 끝까지 받기).
- 위 33개를 부르는 파일 176개. 짝 테스트 14개: `hydrate`·`hydrateEmployedDriver`·`hydrateEmployedDriverCar`·`hydrateMerge`·`hydrateVehicleDayLogs`·`pendingWorkDataWrites`·`pendingWorkDataWritesSyncSpy`·`durableWriteGuard`·`dayDraftActivity`·`dayLogCloudCommit`·`dailyLogRemoval`·`fetchAllRows`·`fetchAllRows.saves`·`singleFlight`(각 `.test.js`).

### 목표 상태 (기대 동작)
1. 옮길 곳(이름 그대로, 짝 테스트 함께):
   - `src/data/hydrate/` ← 불러오기 10개.
   - `src/data/sync/` ← 실패 대비 장치 10개 + 서버 저장 도우미 7개 + `ownerSaveQueue` + 88-B 3개(새 이름, 아래 2).
   - `src/data/cloud/` ← `singleFlight`·`fetchAllRows`(서버 연결 쪽 공용).
2. 88-B 새 파일 이름(안의 내보내는 이름 `commitLocalOnly`·`StaleSessionError`·`PermanentFailureError` 등은 그대로): `outboxCommit.js` → `data/sync/localCommit.js`, `outboxErrors.js` → `data/sync/saveErrors.js`, `outboxTypes.js` → `data/sync/syncTypes.js`. 세 파일의 머리 주석에서 "outbox" 표현을 빼고 하는 일로(아래 최종 문구). `directMutations.js:2` 머리 주석의 "즉시 시도와 outbox 재시도가 같은 함수를 쓴다"도 정리(93-A 때 남긴 것).
3. 옛 경로·이름 grep 0건: `lib/(hydrate|pending|durable|logPendingLifecycle|dayDraftActivity|ownerSaveQueue|outbox|dayLogCloudCommit|syncWorkData|dailyLogRemoval|mainDayLogRouting|syncVehiclesClients|directMutations\.|driverLinkRpc|singleFlight|fetchAllRows)`, 그리고 `outboxCommit|outboxErrors|outboxTypes` 0건(문서 아닌 `src` 전체).
4. 테스트 수 그대로, 전부 통과. 빌드 결과는 경로·파일 이름 말고 같음(59-B처럼 내용 비교).
5. `lib/`에는 한 기능 전용 저장 함수·내보내기·재수출 껍데기 등 54개 남음(59-D~H에서 옮김).

### 건드릴 파일
- 옮김(`git mv`): 33개 + 짝 테스트 14개 = 47개(그중 3개는 이름도 바뀜).
- 경로만 고침: 부르는 파일 약 176개 + 옮긴 파일 안의 상대 경로 + 테스트의 `mock.module`·`import()` 경로. `package.json`은 이미 `src/data/**` 포함이라 안 바뀜(확인만).
- 주석만: 88-B 세 파일 머리 주석, `directMutations.js:2`.
- 머리 주석 최종 문구: `localCommit.js` → `// 기기 저장만 하는 커밋 — 저장 실패해도 예외를 던지지 않고 결과(문구·실패 여부)로 돌려준다.`, `saveErrors.js` → `// 저장 중 세션이 바뀜·영구 실패 같은 오류 종류 — 부르는 쪽이 종류로 나눠 안내한다.`, `syncTypes.js` → `// 서버 직접 저장 파일들이 같이 쓰는 JSDoc 타입(런타임 코드 없음).`, `directMutations.js` → `// Supabase를 직접 부르는 실행기 함수. 삭제는 이미 없는 행 삭제도 성공이라 중간부터 다시 돌아도 안전하다.`(이미 있는 뒷문장 유지, outbox 문장만 뺌).

### 안 건드릴 것
- 한 기능 전용 저장 함수(`clientCloudSave`·`clientMutations`·`vehicleMutations`·`vehicleDeletion`·`vehicleRename`·`requestDriverInviteSave`·`carInviteFromDraft`·`directMutationActions`·`driverUnlink`·`profileCloudCommit`·`syncExpenseItems`·`syncTaxInvoicesTable`·`accountWithdrawal`·`supportInquiryMutations` 등) — 59-D~G에서 기능의 `api/`로.
- `failureMessages.js`(화면·저장 어디서나 쓰는 안내 문구), 재수출 껍데기, 내보내기·엑셀 — 59-H.
- 여러 파일을 함께 검사하는 테스트(`cloudMemorySave`·`assignedVehicleDay`·`receivablesCloudCommit`·`employedDriverClients`·`vehicleMutations.syncSpy` 등) — 위치 그대로, 경로만.
- 실패 대비 장치의 내용(91번) — 위치만.

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 3 grep 0건을 진행 기록에.
- `git diff -M --stat`: 47개가 이름 바뀜으로 잡히는지, 경로 아닌 바뀐 줄은 주석 4곳뿐인지 진행 기록에.
- 빌드 결과 내용 비교(이름 해시 뺌) — 같음을 진행 기록에(파일 이름 바뀐 3개는 묶음 안 이름 차이 정도만 허용, 있으면 적음).
- 개발 서버(비회원): 차량·거래처 추가, 일지 저장 → 새로고침 뒤 그대로, 콘솔 오류 없음.

### 파일 길이
- 옮기기만 해서 줄 수 그대로(주석 4곳은 한 줄 안에서 바뀜). 200줄 넘는 파일은 경로 줄만 — §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(위치·이름만).
- 테스트가 하나라도 실패하면(특히 가짜 모듈 경로를 못 찾는 경우) 멈추고 어느 경로인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 앱 켜기 → 로그인 유지, 차량·거래처·기사 목록 그대로(불러오기 확인).
2. 오늘 일지 한 건 저장 → 새로고침 뒤 그대로 → 되돌림(저장 확인).
3. 콘솔 오류 없음.
