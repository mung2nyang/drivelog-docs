# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 93-A 아무 데서도 안 쓰는 함수 지우기 — 테스트도 없는 것 `[x]`

> **진행(2026-10-19)**: 보리 "착수지시서 작업진행해". 코드 커밋 react-app `2adbf85`(5개 파일, +3 / −189).
> - 교차검증(AGENTS §1): 이름 11개(`mergeRemoteIdByLocalId`·`findOverlappingDriverLinkOnSupabase`·`upsertDriverLinkOnSupabase`·`findExistingDriverLinkInsert`·`rangesOverlap`·`loadClients`·`DRIVER_SHARE_BASES`·`syncIdMerge`·`KEYS`·`readJson`·`keyFor`) 단어 일치 grep — 정의 줄과 지시서에 적은 안쪽 사용(`:127`·`:68`·`:7`)뿐, 줄 번호 모두 같음. 고친 것 없음.
> - 목표 1 grep(위 이름, `src` 전체) **0건**, `cloudStorage.js`의 `KEYS|readJson|keyFor` 0건.
> - 바뀐 파일: `syncIdMerge.js` 삭제, `directMutations.js` 177→79(함수 3개, `getCloudUserId`·`rangesOverlap` 가져오기), `cloudStorage.js` 114→67(값·함수 4개, 머리 주석 지시서 문구 그대로 — `JsonValue` 표기는 `parseEntityNumber`가 계속 써서 남김), `clients.js` 21→13(`loadClients`·`readJsonKey` 가져오기·`JsonValue` 표기, 머리 주석 지시서 문구), `driverShareBase.js` 54→52(상수 1줄+빈 줄).
> - `git diff --stat`: 테스트 파일 0개. `npm test` 1004 + 497 그대로 통과, 타입 검사·빌드 통과. 새 테스트 없음·revert-check 해당 없음(지시서대로).
> - 개발 서버(비회원): 기사 차량 수정 창 "기사 몫 기준 — 운송료 전체" 보임, 거래처 추가 됨, 콘솔 오류 없음.
> - 그대로 둔 것(범위 밖, 기록만): `directMutations.js:2` 머리 주석의 "즉시 시도와 outbox 재시도가 같은 함수를 쓴다" — outbox는 88-A에서 없어졌으나 지시서 주석 정리 목록에 없음. 88-B 또는 다음 정리 때.
> - push 뒤 CI verify·deploy 초록(run 39204693911).
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서 5개 파일 ②새 파일·저장 키 0(파일 1개 삭제는 지시서대로) ③타입 꼼수 0 ④200줄 이내 ⑤테스트 파일 0개 바뀜 ⑥공용 값 — 해당 없음 ⑦목표 1 grep 0건. 더한 줄 3개는 머리 주석 2줄(지시서 문구)·가져오기 1줄(`getCloudUserId` 뺀 것)뿐. 남은 타입 표기(`SessionCapture`·`JsonValue`) 모두 아직 쓰임.
> - 배포본 로그인 확인(2026-10-19, 내장 브라우저): 바뀐 묶음 파일(`directMutationActions` 새 이름)로 열림, 차량 3·거래처 1·연동 기사 1 그대로, 기사 "수정" 창 열고 [취소] → 그대로, 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-19).

보리 "승인"(2026-10-19, 89-B `[x]`). 순서는 로드맵 "88~94" 줄 — 89 다음 93. 88-A·89에서 편지함을 걷어내며 새로 죽은 것도 포함.
93번 목록을 지금 코드로 다시 뽑으니(운영 파일 밖 사용 0 + 자기 파일 안에서도 안 씀) 두 종류였음:
**93-A(이번)** 테스트도 안 부르는 것 — 그냥 지우면 됨. **93-B(다음)** 테스트만 부르는 것 — 함수마다 "테스트가 다른 진짜 동작을 대신 지키고 있나" 판단이 필요해 따로(아래 "확인할 점").

### 쉬운 요약
- 앱 어디서도 부르지 않고 테스트도 없는 함수·값이 8개 있습니다(지우면 같이 쓸모없어지는 2개 더). 대부분 지난번 "보낼 편지함"을 지울 때 함께 쓸모가 없어진 것입니다.
- 지웁니다. 파일 하나(`syncIdMerge.js`)는 통째로 비어서 파일째 지웁니다.
- 화면·저장 동작은 그대로이고, 테스트도 하나도 안 바뀝니다.

### 현재 상태 (코드 확인)
근거: 운영 파일 전체 grep(이름 단어 일치) — 정의한 파일 밖 사용 0, 정의한 파일 안 사용 0(정의 줄뿐), `*.test.js` 사용 0.
- `lib/syncIdMerge.js`(31줄) `mergeRemoteIdByLocalId` — 가져오는 파일 0(운영·테스트 모두, `syncIdMerge` grep 0). 88-A에서 `syncVehiclesClients.js`의 `syncOne*`가 지워지며 남음.
- `lib/directMutations.js` `findOverlappingDriverLinkOnSupabase`(`:57`), `upsertDriverLinkOnSupabase`(`:111`) — 옛 `outboxFlush.js`만 불렀음. `findExistingDriverLinkInsert`(`:92`)는 `upsertDriverLinkOnSupabase` 안(`:127`)에서만 씀 → 같이 죽음. 그러면 `rangesOverlap` 가져오기(`:7`)·`getCloudUserId` 가져오기(`:6`, 쓰는 곳 `upsert…` 안 1곳뿐)도 안 씀.
- `lib/cloudStorage.js` `KEYS`(`:4`)·`readJson`(`:20`)·`keyFor`(`:34`) — 옛 `outboxRollback.js`만 씀. `rangesOverlap`(`:56`)은 위 `findOverlapping…`만 씀. 남는 것 `parseEntityNumber`·`buildVehicleRow`·`buildClientRow`는 계속 쓰임(`syncVehiclesClients.js`·`dayLogCloudCommit.js`·`syncWorkData.js`).
- `lib/clients.js` `loadClients`(`:8`) — 거래처는 Store 구독으로 읽음(`useOwnerClients`). `saveClients`·재수출은 계속 쓰임.
- `domain/driverShareBase.js` `DRIVER_SHARE_BASES`(`:16`) — 상수 목록, 아무도 안 읽음(`resolveDriverShareBase`는 글자 두 개를 직접 씀).

### 목표 상태 (기대 동작)
1. 위 8개(+ 같이 죽는 `findExistingDriverLinkInsert`·`rangesOverlap`) 정의가 없고, `src` 전체 grep(이름 단어 일치) 0건: `mergeRemoteIdByLocalId|findOverlappingDriverLinkOnSupabase|upsertDriverLinkOnSupabase|findExistingDriverLinkInsert|rangesOverlap|loadClients|DRIVER_SHARE_BASES|syncIdMerge`, 그리고 `cloudStorage.js`에서 `KEYS|readJson|keyFor` 0건.
2. 그 함수만 쓰던 가져오기 줄·타입 표기도 같이 지움(타입 검사가 "안 쓰는 이름"을 안 잡으므로 손으로 확인 — `clients.js`의 `readJsonKey` 가져오기·`JsonValue` 표기, `directMutations.js`의 `rangesOverlap`·`getCloudUserId`).
3. 테스트 파일은 하나도 안 바뀜(사용 0). 테스트 수·검사 수 그대로.
4. 머리 주석에서 없어진 역할만 뺌 — `cloudStorage.js:3` "(hydrate·직접 mutation·outbox가 공유)" → 실제 남은 것에 맞게, `clients.js:3` "localStorage I/O" → "저장".

### 건드릴 파일
- 지움: `lib/syncIdMerge.js`.
- 고침: `lib/directMutations.js`(함수 3개·가져오기 2개), `lib/cloudStorage.js`(값·함수 4개, 머리 주석), `lib/clients.js`(함수 1개·가져오기·타입 표기, 머리 주석), `domain/driverShareBase.js`(상수 1줄).
- 머리 주석 최종 문구: `cloudStorage.js` → `// 서버 저장 행 만들기와 번호 읽기 도우미(차량·거래처·일지 서버 저장이 공유).`, `clients.js` → `// 거래처 저장 + domain/clients.js 재수출 배럴.`

### 안 건드릴 것
- 93-B 대상(테스트만 부르는 것): `buildReportSmsUrl`·`resolveDriverVehicleNumber`·`group{Fuel,Maint,Misc}ExpensesByDate`·`requestClientFixedUnitPrice`·`loadInvoices`·`loadPracticeSettings`·`loadWorkData`·`commitWorkDataDeletedDates`.
- 테스트용 손잡이(일부러 테스트를 위해 둔 것): `reset*ForTests` 3개, `store/persist.js` `writeJsonKey`(테스트 8개가 저장소 준비에 씀), `pendingDayWriteCount`(테스트 7개가 관찰에 씀).
- 일지 저장 실패 장치 `durableWriteGuard.js`의 `hasUnsafeRegistration`·`hasAnyUnsafeRegistration` — 91번.
- 자기 파일 안에서는 쓰는데 밖으로 내보내기만 하는 이름(약 70개, 예: `app/usePullToRefresh.js` `pullDistance`) — 대부분 테스트가 가져다 검사하는 것이라 정리 대상 아님.

### 검증
- `npm test`·타입 검사·빌드 통과(테스트 수 그대로 — 1004 + 497).
- 목표 1 grep 0건을 진행 기록에.
- `git diff --stat`에 테스트 파일 0개인지 진행 기록에.
- 새 테스트 없음(동작 변화 없음 — revert-check 해당 없음).
- 개발 서버(비회원): 거래처 추가, 기사 차량 화면 열기(기사 몫 기준 드롭다운 보임) → 콘솔 오류 없음.

### 파일 길이
- 줄어들기만 함: `lib/directMutations.js` 177 → 약 110, `lib/cloudStorage.js` 114 → 약 70, `lib/clients.js` 21 → 약 14, `domain/driverShareBase.js` 54 → 53. 운영 코드 약 150줄 삭제(파일 1개 포함).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(지우기만).
- 지우고 나서 타입 검사·테스트가 하나라도 실패하면 어딘가에서 아직 쓴다는 뜻 — 되살리지 말고 멈추고 어디서 쓰는지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 앱 켜기 → 차량·거래처·기사 목록 그대로.
2. 기사 연동 관리 → 기사 "수정" 창 열고 닫기(저장 안 함) → 오류 없음.

### 확인할 점
1. 93-B(테스트만 부르는 함수 10개)는 93-A 바로 다음에 따로 지시서를 씁니다. 함수마다 "그 테스트가 화면이 실제로 쓰는 다른 코드를 대신 지키고 있는지" 보고, 그렇다면 테스트를 화면이 쓰는 함수 쪽으로 옮기고, 아니면 함수와 테스트를 같이 지우는 방식을 권합니다.
