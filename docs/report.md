# docs/report.md — 현재 슬라이스 착수지시서

117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 126 일지 저장이 겹치면 다른 날짜 내용이 되돌아감 `[x]`

폴더별 전수점검 A4(2026-10-26), 로드맵 126 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 앞 슬라이스 117 `[x]`(react-app `dc46f0c`, 같은 파일).

### 쉬운 요약
- 일지는 저장이 끝나면 "저장을 시작할 때 읽어 둔 그 차량 일지 전체"를 화면 데이터에 통째로 넣습니다. 그 사이 다른 날짜 저장(또는 미수금 입금 처리)이 먼저 끝나면, 그 내용이 화면에서 사라집니다.
- 화면에서 사라진 날짜를 다시 열어 고치면, 다음 자동 저장이 서버까지 옛 값으로 덮을 수 있습니다.
- 저장이 끝날 때 "방금 저장한 날짜만" 지금 화면 데이터 위에 얹도록 바꿉니다. 서버 저장 방식은 그대로입니다.

### 현재 상태 (코드 확인)
- 날짜 하나 저장: `data/sync/dayLogCloudCommit.ts:106` `commitLogWorkData(ownerKey, logId, nextData)` — `nextData`는 저장 시작 때 만든 일지 전체(`features/day-log/hooks/dayDraftLifecycle.js:118-126`).
- 여러 날짜 저장: 같은 파일 `:131` `partialNext = { ...previousData }`에 성공 날짜를 더해 `:152`에서 통째 반영 — 그 사이 끝난 다른 저장이 사라짐.
- 지금 코드로 재현함(2026-10-26 데스크톱 AI, 임시 테스트 파일로 돌린 뒤 지움): 아래 두 테스트 모두 "저장분이 사라짐"으로 실패.
- 부르는 곳: 날짜 하나 `dayDraftLifecycle.js:135`·`ownerFinance.ts:37`, 여러 날짜 `ownerFinance.ts:40`·`memberRestore.js:140` — 모두 "바꾼 날짜만 다른" `nextData`를 넘김(바꾸지 않은 날짜를 일부러 덮어쓰는 곳 없음).
- 기존 테스트 중 Store가 `nextData` 전체와 같은지 보는 것 없음 — `dayLogCloudCommit.test.js`·`assignedVehicleDay.test.js`에 일지 전체 비교 없음(검색), `receivablesCloudCommit.test.js` G-2·G-3·G-4는 날짜별 확인이라 그대로 통과해야 함.

### 목표 상태 (기대 동작)
1. 날짜 하나 저장이 끝나면 그 날짜만 지금 Store 위에 반영한다 — 그 사이 끝난 다른 날짜 저장이 남는다.
2. 여러 날짜 저장이 끝나면 서버에 저장된 날짜만 지금 Store 위에 반영한다 — 그 사이 끝난 다른 저장이 남는다. 실패 날짜는 반영하지 않는다(지금과 같음).
3. 세션이 바뀌었으면 반영하지 않는다(117 그대로). 돌려주는 값·알림 문구는 그대로.
4. 저장한 날짜가 빈 날이 되어 `nextData`에 없으면 Store에서도 그 날짜를 뺀다(지금과 같은 결과).

### 건드릴 파일
- `react-app/src/data/sync/dayLogCloudCommit.ts` — 반영 함수 1개 추가, `:106`·`:152`에서 그 함수 사용, `partialNext`(`:131`·`:139-140`) 삭제. `readOwnerLogWorkData`(`data/store/ownerWorkDataHooks.ts`) 가져오기 1개. 추가할 함수(주석 포함 최종 모양):
  ```ts
  /** 저장한 날짜만 지금 Store 위에 얹는다 — 그 사이 끝난 다른 날짜 저장을 되돌리지 않게. */
  function commitSavedDays(ownerKey: string, logId: string, nextData: Record<string, DayRecordLike>, dateKeys: string[]) {
    const latest = { ...readOwnerLogWorkData(ownerKey, logId) }
    for (const dateKey of dateKeys) {
      if (nextData[dateKey]) latest[dateKey] = nextData[dateKey]
      else delete latest[dateKey]
    }
    commitLogWorkData(ownerKey, logId, latest)
  }
  ```
  - `:106` → `commitSavedDays(ownerKey, logId, nextData, [dateKey])`
  - `:152` → `if (appliedDateKeys.length > 0 && isSessionStillCurrent(captured)) commitSavedDays(ownerKey, logId, nextData, appliedDateKeys)`(117 주석 그대로 둠)
- `react-app/src/data/sync/dayLogCloudCommit.overlap.test.js` — 새 테스트 파일, 2개. 코드는 `docs/full-code-review-failing-tests.md` A4를 이번에 늘린 판(여러 날짜 저장 경우 추가) — 아래 "테스트 코드".
- 공용 스타일·공용 값 변경 없음.

### 안 건드릴 것
- 서버 저장(`writeDayKeyToServer`·`save_day_log`)·117 세션 확인 — 그대로.
- 부르는 곳 4곳 — 넘기는 값·받는 값이 같아 그대로.
- 기기 저장 경로(비회원·서버 차량 없음, `dayDraftLifecycle.js` `commitLocal`) — 저장이 기다림 없이 바로 끝나 겹치지 않음(`saveLogWorkDataWithTombstoneCheck` 동기 호출).

### 테스트 코드 (`src/data/sync/dayLogCloudCommit.overlap.test.js`)
```js
// 일지 저장이 겹쳐도 먼저 끝난 다른 날짜 저장이 화면(Store)에서 되돌아가지 않는다.
import '../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'
import { createFakeSupabase, wait } from '../../testSupport/fakeSupabaseClient.js'

const { fakeSupabase, handlers, resetHandlers, emptyOkHandlers } = createFakeSupabase()
mock.module('../cloud/supabaseClient.ts', { namedExports: { supabase: fakeSupabase } })

const { commitMainDayLogToCloud, commitMainDayLogMapToCloud } = await import('./dayLogCloudCommit.ts')
const { beginSessionEpoch, endCloudSession } = await import('../cloud/cloudSession.ts')
const { setHydration } = await import('../store/app-store.ts')
const { commitCars, commitWorkData } = await import('../store/commitHelpers.ts')
const { readOwnerLogWorkData } = await import('../store/ownerWorkDataHooks.ts')
const { saveDayRecord } = await import('../../domain/day-record.ts')

/** 날짜별로 서버 저장이 끝나는 시간(ms)을 정한 가짜 서버로 로그인 상태를 만든다. @param {string} ownerKey @param {Record<string, number>} delays */
function beginOwner(ownerKey, delays) {
  resetHandlers()
  Object.assign(handlers, emptyOkHandlers())
  handlers.rpc = {
    save_day_log: async (/** @type {{ p_log: { work_date: string } }} */ args) => {
      await wait(delays[args.p_log.work_date] ?? 5)
      return { data: 1, error: null }
    },
  }
  beginSessionEpoch(`${ownerKey}-user`, ownerKey)
  setHydration({ status: 'ready', userId: `${ownerKey}-user`, ownerKey })
  commitCars(ownerKey, [{ id: 'car-main', type: 'main', number: '12가3456', supabaseId: 700 }])
  commitWorkData(ownerKey, {})
}

/** 일지 화면 저장과 같은 순서: 저장 시작 때 그 차량 일지 전체를 읽고 그 날짜만 바꾼다. @param {string} ownerKey @param {string} dateKey @param {number} fixedCount */
function startSave(ownerKey, dateKey, fixedCount) {
  const latest = readOwnerLogWorkData(ownerKey, 'main')
  const next = saveDayRecord(latest, dateKey, { isOff: false, fixedCount, palletCount: 0, callDetails: [], fixedRouteCounts: {} })
  return commitMainDayLogToCloud({ ownerKey, logId: 'main', dateKey, previousData: latest, nextData: next })
}

test('A4: 다른 날짜 저장 두 개가 겹쳐도 둘 다 화면(Store)에 남는다', async () => {
  const ownerKey = 'a4-owner'
  beginOwner(ownerKey, { '2026-10-01': 40, '2026-10-02': 5 })
  const first = startSave(ownerKey, '2026-10-01', 3) // 먼저 시작, 늦게 끝남
  const second = startSave(ownerKey, '2026-10-02', 5) // 나중 시작, 먼저 끝남
  const results = await Promise.all([first, second])
  assert.deepEqual(results.map((r) => r.cloud && 'ok' in r && r.ok), [true, true], '둘 다 서버 저장 성공')
  const work = readOwnerLogWorkData(ownerKey, 'main')
  assert.equal(work['2026-10-01']?.fixedCount, 3, '10월 1일 저장분')
  assert.equal(work['2026-10-02']?.fixedCount, 5, `10월 2일 저장분이 사라짐: ${JSON.stringify(work['2026-10-02'])}`)
  endCloudSession()
})

test('A4: 여러 날짜 저장 중 먼저 끝난 다른 날짜 저장도 화면(Store)에 남는다', async () => {
  const ownerKey = 'a4-owner-map'
  beginOwner(ownerKey, { '2026-10-01': 40, '2026-10-02': 40, '2026-10-03': 5 })
  const latest = readOwnerLogWorkData(ownerKey, 'main')
  const nextMany = {
    ...latest,
    '2026-10-01': { isOff: false, fixedCount: 1, palletCount: 0, callDetails: [], fixedRouteCounts: {} },
    '2026-10-02': { isOff: false, fixedCount: 2, palletCount: 0, callDetails: [], fixedRouteCounts: {} },
  }
  // 미수금 일괄 입금처럼 두 날짜를 차례로 저장하는 사이에 일지 화면이 10월 3일을 저장한다.
  const many = commitMainDayLogMapToCloud({ ownerKey, logId: 'main', dateKeys: ['2026-10-01', '2026-10-02'], previousData: latest, nextData: nextMany })
  const one = startSave(ownerKey, '2026-10-03', 7)
  const [manyResult] = await Promise.all([many, one])
  assert.equal(manyResult.cloud && manyResult.ok, true, '두 날짜 서버 저장 성공')
  const work = readOwnerLogWorkData(ownerKey, 'main')
  assert.equal(work['2026-10-01']?.fixedCount, 1, '10월 1일 저장분')
  assert.equal(work['2026-10-02']?.fixedCount, 2, '10월 2일 저장분')
  assert.equal(work['2026-10-03']?.fixedCount, 7, `10월 3일 저장분이 사라짐: ${JSON.stringify(work['2026-10-03'])}`)
  endCloudSession()
})
```
지금 코드 실행 결과(2026-10-26): 첫 번째 "10월 2일 저장분이 사라짐: undefined", 두 번째 "10월 3일 저장분이 사라짐: undefined"로 둘 다 실패.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(AGENTS §4 트리거 — 저장 경로 `*commit*`), 특히 §5 세션 무효화.
- 새 테스트 2개가 고치기 전 코드에서 실패하는지 먼저 확인(위 결과와 같아야 함), 고친 뒤 통과. `receivablesCloudCommit.test.js`·`dayLogCloudCommit.test.js` 등 기존 테스트 그대로 통과.
- 테스트·타입·빌드·코드 검사는 CI가 봄. 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `dayLogCloudCommit.ts` 167 → 약 174줄(200 이하).
- `dayLogCloudCommit.overlap.test.js` 새 파일 약 70줄(테스트 파일 예외).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음 — 반영할 값을 고르는 방법만 바뀜). 실패하면 수정 착수지시서를 다시 써서 승인받음.

### 사용자 확인 방법
- 저장이 겹치는 순간은 손으로 맞추기 어려워 테스트로 확인. 배포본에서는 동작이 그대로인지만 봄:
1. 로그인 계정에서 아무 날짜 일지에 고정 노선 횟수를 1 올리고 저장 표시 → 다른 날짜로 옮겨 같은 일 → 두 날짜 모두 다시 불러와도 남아 있는지.
2. 두 날짜를 원래 값으로 되돌림(시험한 것 되돌림).

### 진행 기록
- 교차검증: 파일·줄·현재 동작·부르는 곳 4곳·`readOwnerLogWorkData` 위치·테스트 도우미(`wait`, `saveDayRecord`) 모두 일치. 고친 점 없음.
- 고치기 전 실패 확인: 새 테스트 2개가 고치기 전 코드에서 지시서 기록과 같게 실패(종료 코드 1).
  ```
  test at src\data\sync\dayLogCloudCommit.overlap.test.js:40:1
  ✖ A4: 다른 날짜 저장 두 개가 겹쳐도 둘 다 화면(Store)에 남는다 (56.4694ms)
    AssertionError [ERR_ASSERTION]: 10월 2일 저장분이 사라짐: undefined
  test at src\data\sync\dayLogCloudCommit.overlap.test.js:53:1
  ✖ A4: 여러 날짜 저장 중 먼저 끝난 다른 날짜 저장도 화면(Store)에 남는다 (95.663ms)
    AssertionError [ERR_ASSERTION]: 10월 3일 저장분이 사라짐: undefined
  ℹ tests 2  ℹ pass 0  ℹ fail 2  ℹ duration_ms 1073.1029
  ```
- 고친 뒤: 새 파일·`receivablesCloudCommit.test.js`·`dayLogCloudCommit.test.js` 32개 통과, 로컬 `npm test` 전체 통과(916·899개). 줄 수 `dayLogCloudCommit.ts` 175, 새 테스트 72.
- 코드 커밋 react-app `d4275c3`, push 뒤 CI verify·deploy 초록(run 40463703427).
- 배포본 확인(2026-10-27, 데스크톱 AI, 로그인 테스트 계정 메인 차량): 빈 날 10-19에 [2회] → 바로 10-20으로 옮겨 [3회] → 달력을 새로 불러와 10-19 "10만"·10-20 "15만" 둘 다 남음 → 두 날 운행 횟수 0으로 되돌림, 새로 불러와 두 날 다시 빈 칸 확인.
- §5 점검: ① 범위 — 바뀐 파일 `dayLogCloudCommit.ts`·새 `dayLogCloudCommit.overlap.test.js` 2개, 지시서와 같음(새 테스트는 지시서 코드와 글자까지 같음) ② 새 저장 장치·저장 키 없음(새 파일은 테스트뿐) ③ `any`·`unknown`·`@ts-ignore` 없음 ④ `dayLogCloudCommit.ts` 175줄 ⑤ 새 테스트 2개, 기존 테스트 그대로 ⑥ 공용 클래스·상태값 변경 없음, 함수 주석은 지시서 문구 그대로 ⑦ 목표 1·2는 새 테스트, 3·4는 기존 G-2~G-4와 배포본으로 확인.

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
