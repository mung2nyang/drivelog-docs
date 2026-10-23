# docs/report.md — 현재 슬라이스 착수지시서

109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 110-A 로그인 일지 저장 실패 날짜를 휴대폰에 기록 `[x]`

로드맵 110 `[확인: 2026-10-23 보리 — "다음 작업을 알림 목록에 저장하는 걸로", 정한 것 ①~④, "가로 규칙 예외 허락", "110번 착수지시서 써"]`. 둘로 나눔(AI 판단, 파일 약 7개): **110-A** 실패한 날짜를 휴대폰에 남기고 저장되면 지우기(이번, 화면 변화 없음) → **110-B** 알림 목록에 그 날짜 알림 보이기·누르면 그 날짜 일지·닫기와 끄기 없음. 이번엔 110-A만.

### 쉬운 요약
- 로그인 계정의 일지가 서버 저장에 실패하면, 그 날짜를 휴대폰에 적어 둔다. 앱을 껐다 켜도 남는다.
- 그 날짜 일지가 나중에 저장에 성공하면 적어 둔 것을 지운다.
- 입력 내용은 적지 않는다(날짜만). 다음 조각(110-B)이 이 기록으로 알림을 보인다.

### 규칙 예외 (보리 허락)
- `docs/sot.md` 맨 위 "신규 durable/fallback/unsafe/tombstone/큐 금지", 691줄 "메인 일지(로그인) durable/재시도 없음"의 예외 — **실패한 날짜 목록 하나만** 휴대폰에 둔다. 입력 내용·재시도·큐는 여전히 없음 `[확인: 2026-10-23 보리 — "가로 규칙 예외 허락"]`. `[x]` 때 sot에 예외로 적는다.

### 현재 상태 (코드 확인)
- 로그인 일지 서버 저장: `features/day-log/hooks/dayDraftLifecycle.js` `commitNow` → `commitMainDayLogToCloud` → `settleCloud`(91~104줄). 실패면 알림 문구 + `markUnsafeRegistrationFailure(pendingOwner, dateKey, patch)`(`data/sync/durableWriteGuard.ts:20`) — **앱 메모리 Map**에만 날짜·내용을 둠. 앱을 끄면 사라짐.
- 성공이면 `markSaved`(68~73줄)가 `clearUnsafeRegistrationFailure`·`clearPendingDayWrite`를 부름. 일지 화면을 닫을 때 `close()`(170~174줄)는 메모리 표시를 먼저 내리고 한 번 더 저장 시도.
- 로그인 갈래 여부: `data/sync/mainDayLogRouting.ts:25` `shouldCommitDayLogToCloud(ownerKey, logId)` — 로그인 + 그 차량이 서버에 있으면 서버 저장(메인·기사 차량 일지 모두 해당될 수 있음).
- 비회원·서버에 없는 차량 일지는 이미 휴대폰 대기 목록(`data/sync/pendingWorkDataWrites.ts`)에 남고 자동 재시도 — 이번 범위 밖(그대로).
- 날짜 검사기: `domain/dateKey.ts:13` `isValidCalendarDateKey`. 휴대폰 저장 선례: `app/guestSessionPersist.js`(키 하나, `try/catch`).

### 목표 상태 (기대 동작)
1. 로그인 갈래 일지 저장이 서버에서 실패하면 `{ logId, dateKey }`를 그 계정(ownerKey)의 실패 목록에 더한다(같은 날짜·차량은 한 번만).
2. 그 날짜·차량 일지가 나중에 서버 저장에 성공하면(`markSaved`) 목록에서 지운다. 일지 화면을 닫을 때 메모리 표시를 내리는 것(`close()`)으로는 지우지 않는다.
2-1. 이 휴대폰에서 그 날짜·차량 일지 화면을 열면 목록에서 지운다 — 다른 기기에서 다시 저장한 경우에도 알림이 영영 남지 않게. 열어 둔 화면에서 다시 실패하면 1번대로 또 더한다 `[확인: 2026-10-23 보리 — "가로"]`.
3. 목록은 휴대폰에 남아 앱을 껐다 켜도 그대로다. 계정마다 따로다.
4. 휴대폰 저장소를 못 쓰거나 값이 깨졌으면(배열 아님, 날짜 모양 아님, logId 문자열 아님) 그 항목·목록을 무시하고 앱은 그대로 동작한다. 읽기·쓰기 실패로 일지 저장 흐름이 멈추지 않는다.
5. 바뀔 때 알려 주는 구독 함수가 있다(110-B 알림 목록이 다시 세는 용도). 이번엔 부르는 곳 없음.
6. 화면에 보이는 변화는 없다. 비회원·서버 밖 차량 일지 동작은 그대로.

### 건드릴 파일
- `react-app/src/data/sync/failedDayLogDates.ts` (새 파일, 약 60줄) — `listFailedDayLogDates(ownerKey)`, `addFailedDayLogDate(ownerKey, logId, dateKey)`, `removeFailedDayLogDate(ownerKey, logId, dateKey)`, `subscribeFailedDayLogDates(listener)`. 휴대폰 키 하나(`drivelogFailedDayLogDates`, 값 `{ [ownerKey]: Array<{ logId, dateKey }> }`), 읽을 때 중첩 필드까지 검사(AGENTS §6 외부 경계). 맨 위 주석(최종 문구):
  `// 로그인 일지 서버 저장에 실패한 날짜 목록 — 앱을 껐다 켜도 알림이 남게 휴대폰에 날짜만 둔다(입력 내용·재시도 없음).`
- `react-app/src/features/day-log/hooks/dayDraftLifecycle.js` — `settleCloud` 실패 갈래에서 `addFailedDayLogDate`, `markSaved`에서 `removeFailedDayLogDate`(로그인 갈래일 때), 일지 화면을 열 때(저장 도우미를 만들 때 한 번) `removeFailedDayLogDate`. 182 → 약 188줄.
- 테스트(200줄 예외): 새 `data/sync/failedDayLogDates.test.js` — 더하기·중복 없음·지우기·계정별·깨진 값 무시·저장소 예외에도 throw 없음·구독 호출. `features/day-log/hooks/useDayDraft.cloudSerial.test.js`에 — 서버 실패 → 목록에 날짜, 다음 저장 성공 → 목록에서 지움, 화면 닫기만으로는 안 지움, 그 날짜 일지를 열면 지움(다른 날짜는 그대로).

### 안 건드릴 것
- `durableWriteGuard.ts` 메모리 표시·`pendingWorkDataWrites.ts` 휴대폰 대기 목록·자동 재시도 — 그대로.
- 알림 목록·알림 화면·알림 받기 설정 — 110-B.
- 실패한 입력 내용 보관 — 범위 밖(로드맵 110 "이번 밖").
- 서버 저장 방식·`dayLogCloudCommit.ts`.

### 검증
- CI 초록(테스트·타입·빌드·코드 검사·의존 방향).
- 새 테스트는 고치기 전 코드에서 실패하는지 확인(revert-check).
- 화면 변화가 없어 브라우저 확인은 개발 화면에서 서버 실패를 흉내 낼 수 있을 때만(안 되면 테스트로 대신, 110-B에서 화면으로 확인).

### 파일 길이
- `failedDayLogDates.ts` 새로 약 60줄, `dayDraftLifecycle.js` 182 → 약 188줄(200줄 가까움 — 넘으면 멈추고 보고). 테스트는 예외.

### 실패 시 처리
- 이 목록 말고 새 저장·재시도·큐 없음(허락받은 예외는 날짜 목록 하나뿐).
- 일지 저장 흐름이 이 목록 때문에 멈추거나 느려지면 멈추고 보고.

### 정한 것 (보리 확인)
- 다른 기기에서 그 날짜를 다시 저장한 경우: 이 휴대폰에서 그 날짜 일지를 열면 기록을 지움(목표 2-1) `[확인: 2026-10-23 보리 — "가로"]`.

### 진행 기록
- 교차검증: 줄 번호·흐름(`settleCloud`·`markSaved`·`close()`·`shouldCommitDayLogToCloud`)은 코드와 맞음. 지시서와 다르게 둔 곳 2가지:
  - 실패 기록은 `settleCloud` 실패 갈래 맨 앞(`if (!progress.mounted) return`보다 앞)에 둠 — 화면을 닫을 때 한 번 더 저장하다 실패한 경우(화면은 이미 닫힘)도 남아야 해서.
  - 성공 때 지우기는 `markSaved` 안이 아니라 `settleCloud` 성공 갈래(`markSaved` 부르기 바로 전, 같은 판 번호 조건)에 둠 — `markSaved`는 휴대폰 저장 갈래(`commitLocal`)도 불러서, "로그인 갈래일 때만"을 조건 없이 맞추려고.
  - 다른 계정으로 바뀌어 실패한 경우(`SESSION_CHANGED`)도 서버 저장 실패라 같이 기록함.
- `failedDayLogDates.ts`(65줄): 키 `drivelogFailedDayLogDates`, 값 `{ [ownerKey]: [{ logId, dateKey }] }`. 읽을 때 `JsonValue`로 받아 항목마다 검사(logId 빈 문자열 아님, `isValidCalendarDateKey`) — `unknown` 안 씀. 바뀐 경우에만 구독자 부름.
- `dayDraftLifecycle.js` 182 → 188줄: import 1, 저장 도우미를 만들 때(= 그 날짜 일지를 열 때, `useDayDraft`의 `useState` 초기값) 지우기, 실패 더하기, 성공 지우기.
- 검사: 테스트 892 + 773(새 8개), 타입·코드 검사·의존 방향(1724 → 1727)·빌드 통과.
- 고치기 전 실패 확인: `useDayDraft.cloudSerial.test.js` F-1·F-2·F-3 3개는 수정 전 코드에서 실패. `failedDayLogDates.test.js` 5개는 새 파일만 시험해 되돌릴 것이 없어 통과(정상).
- 브라우저: 화면 변화 없음, 개발 화면에서 서버 실패 흉내는 안 함 — 테스트로 대신, 110-B에서 화면 확인.
- 코드 커밋: react-app `d3ecf58`(4개 파일, push 안 함). 남은 것: 보리 push → CI 초록 → `[x]` 때 sot 예외 기록·STATUS·로드맵 110-A → 110-B 지시서.
- push 뒤 CI verify·deploy 초록(run 39911536977, react-app `d3ecf58`). 코드 대조(AI): 실패 갈래에서만 기록, 서버 저장 성공·일지 열기에서 지움, 휴대폰 값은 중첩까지 검사·실패해도 throw 없음, `dayDraftLifecycle.js` 188줄. 화면 변화 없어 브라우저 확인 없음(110-B에서 알림으로 확인).
- 2026-10-23 보리 승인("110-A 승인, [x]로 확정"). `[x]`.

### 사용자 확인 방법
- 화면 변화 없음. 110-B가 끝나면 알림 목록에서 함께 확인.

---

## 로드맵 94 `.ts` 전환 — 남은 조각 (94-A·B·C `[x]`, D~G는 출시 전 순서 뒤)

지난 지시서·진행 기록: 94-A drivelog-docs `3ee010c`, 94-B `90ef647`, 94-C `0c0eafe`(`git show <해시> -- docs/report.md`).

| 조각 | 범위 | 운영 파일 | 비고 |
|---|---|---|---|
| ~~94-A~~ `[x]` | 설정 + 시범 2개 | 2 | react-app `a009f2b` |
| ~~94-B~~ `[x]` | `domain/` | 58 | react-app `855fb6a` |
| ~~94-C~~ `[x]` | `data/` | 81 | react-app `079aee8` |
| 94-D | `shared/` + `app/` + `main.jsx` + `testSupport/` | 69 | |
| 94-E | `features/` 일지·달력·지출 | 39 | |
| 94-F | `features/` 서류·미수금·매출 | 38 | |
| 94-G | `features/` 나머지 8개 | 64 | 마무리: `allowJs` 끄기, `// @ts-check` 규칙 정리 |

- 순서 `[확인: 2026-10-22 보리 — "그럼 반만, 응"]`: D~G는 출시 전 순서(23·24 → 34 → 79) 뒤.
- 방식(B·C와 같음): AI가 손으로, 밖 파일은 경로 끝만(기계 비교), `mock.module` 지정 포함. 증명: 타입 설명 파일(`.d.ts`, 저장소 밖에서 뽑음) 비교 + 빌드 결과. 옛 JSDoc 형 변환 주석이 빠지면 압축 변수 이름이 바뀌어 해시가 달라질 수 있음(94-C) — 그때는 저장소 밖에서 옛·새를 압축 없이 빌드해 같은지로 증명(보리 승인 방식).
- 의존 검사 연결 수는 JSDoc `import('…')`이 `import type`이 되며 타입 전용 연결만큼 늘어남 — 늘어난 것이 그것뿐인지 확인.
- 94-D: `// @ts-check` 없는 4개(`app/guestSessionPersist.js`·`app/routes/ComingSoonRoute.jsx`·`app/routes/lazyPages.js`·`app/routes/sessionGate.js`)는 처음 타입 검사를 받음. `.jsx`는 `.tsx`로 — 화면 테스트 로더가 이미 읽음(94-A).
