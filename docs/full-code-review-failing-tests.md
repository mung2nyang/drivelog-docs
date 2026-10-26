# 데이터 저장·유실 묶음 — 실패하는 테스트 (react-app `3505ff6`, 운영 코드 수정 없음·커밋 안 함)

실행: `node --experimental-test-module-mocks --test-timeout=60000 --test <파일>` — 13개 모두 실패(= 지금 코드에서 재현됨).

## A1

- 테스트 파일: `src/data/hydrate/retryHydrateLinkCheck.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 A1: 연동 기사가 "다시 불러오기"를 할 때 연동 확인이 실패하면 기사 화면이 빈 데이터로 덮이고 "준비 완료"가 된다.
// 앱 켤 때(buildBootSession)는 checkLinkedDriverLink로 "확인 실패"와 "연동 없음"을 나누는데, retryHydrate는 나누지 않는 fetchLinkedDriverLink를 쓴다.
import '../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'
import { createFakeSupabase } from '../../testSupport/fakeSupabaseClient.js'

const { fakeSupabase, handlers, resetHandlers, emptyOkHandlers } = createFakeSupabase()
mock.module('../cloud/supabaseClient.ts', { namedExports: { supabase: fakeSupabase } })

const { hydrateFromSupabase, retryHydrate } = await import('./hydrate.ts')
const { endCloudSession } = await import('../cloud/cloudSession.ts')
const { getState } = await import('../store/app-store.ts')
const { commitCars } = await import('../store/commitHelpers.ts')

test('A1: 다시 불러오기 중 연동 확인이 실패하면 연동 기사 화면을 빈 차주 칸으로 덮고 ready로 두지 않는다', async () => {
  resetHandlers()
  Object.assign(handlers, emptyOkHandlers())
  const ownerKey = 'a1-owner'
  const userId = 'a1-driver'

  // 1) 연동 기사로 처음 불러오기 — 배정 차량 조회가 실패해 failed(세션은 이 기사·차주 칸으로 잡힘).
  handlers.rpc = { get_assigned_vehicle_summary: () => ({ data: null, error: { message: 'first attempt fails' } }) }
  await assert.rejects(() => hydrateFromSupabase(userId, ownerKey, { employedDriver: true }))
  assert.equal(getState().hydration.status, 'failed')

  // 화면에는 이미 배정 차량이 보이던 상태라고 둔다.
  commitCars(ownerKey, [{ id: 'car-a1', type: 'sub', number: '12가3456', supabaseId: 'veh-a1' }])

  // 2) 다시 불러오기: 서버는 정상인데, 연동 확인(driver_id + status=linked) 조회만 실패한다.
  handlers.rpc = {
    get_assigned_vehicle_summary: () => ({ data: [{ id: 'veh-a1', number: '12가3456', type: 'sub' }], error: null }),
    get_linked_owner_business_info: () => ({ data: null, error: null }),
  }
  handlers.driver_links = {
    select: (/** @type {Record<string, string|number>} */ filters) => (
      filters.driver_id === userId && filters.status === 'linked'
        ? { data: null, error: { message: 'link check timeout' } }
        : { data: [], error: null }
    ),
  }

  try {
    await retryHydrate()
  } catch {
    // 실패로 끝나는 것은 괜찮다(기대 동작 쪽).
  }

  const status = getState().hydration.status
  const cars = getState().cars[ownerKey] || []
  assert.ok(
    status !== 'ready' || cars.length > 0,
    `연동 확인이 실패했는데 status=${status}, 차량 ${cars.length}대 — 차주 칸을 빈 값으로 덮고 준비 완료가 됨`,
  )
  endCloudSession()
})
```

### 실행 결과

```
not ok 1 - A1: 다시 불러오기 중 연동 확인이 실패하면 연동 기사 화면을 빈 차주 칸으로 덮고 ready로 두지 않는다
  failureType: 'testCodeFailure'
  error: '연동 확인이 실패했는데 status=ready, 차량 0대 — 차주 칸을 빈 값으로 덮고 준비 완료가 됨'
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: true
  actual: false
  operator: '=='
```

## M1

- 테스트 파일: `src/features/cars/api/carInviteEndDate.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 M1: 기사가 배정된 차량을 고쳐 저장하면 그 기사의 계약 종료일이 지워진다.
// saveInviteAfterVehicle이 배정 정보를 다시 저장할 때 endDate를 늘 ''로 넘기고, upsertDriver가 그대로 덮어쓴다.
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'

/** @type {Array<{ items: Array<{ id: string, endDate?: string }>, editingId: string|null }>} */
const saved = []
mock.module('../../../data/sync/requestDriverInviteSave.ts', {
  namedExports: {
    requestDriverInviteSave: async (/** @type {{ items: Array<{ id: string, endDate?: string }>, editingId: string|null }} */ args) => {
      saved.push(args)
      return { items: args.items, blocked: null, toast: '기사 배정 정보를 수정했습니다.' }
    },
  },
})

const { saveInviteAfterVehicle } = await import('./carInviteFromDraft.js')

test('M1: 차량 창 저장(기사 연동 탭)이 기존 기사의 계약 종료일을 지우지 않는다', async () => {
  const driver = {
    id: 'drv-1', name: '김기사', phone: '010-1111-2222', inviteCode: 'A2B3C4D5E6',
    vehicleNumber: '12가3456', startDate: '2026-11-01', endDate: '2026-12-31', status: 'linked',
  }
  const car = { id: 'car-1', type: 'sub', number: '12가3456', supabaseId: 'veh-1' }

  // CarListPage.openEdit이 만드는 초안 그대로(톤수만 고쳤다고 가정 — 배정 칸은 안 건드림).
  await saveInviteAfterVehicle({
    cloud: true,
    ownerKey: 'owner-m1',
    userId: 'user-m1',
    drivers: /** @type {any} */ ([driver]),
    cars: /** @type {any} */ ([car]),
    saved: /** @type {any} */ ({ ...car, tonnage: '5톤' }),
    inviteDraft: {
      type: 'sub', number: '12가3456', driverName: '김기사', driverPhone: '010-1111-2222',
      inviteCode: driver.inviteCode, inviteStartDate: driver.startDate, inviteDriverId: driver.id,
    },
  })

  assert.equal(saved.length, 1, '배정 정보 저장 1번')
  const next = saved[0].items.find((item) => item.id === 'drv-1')
  assert.equal(next?.endDate, '2026-12-31', `계약 종료일이 "${next?.endDate}"로 바뀜`)
})
```

### 실행 결과

```
not ok 1 - M1: 차량 창 저장(기사 연동 탭)이 기존 기사의 계약 종료일을 지우지 않는다
  failureType: 'testCodeFailure'
  error: |-
    계약 종료일이 ""로 바뀜
    
    '' !== '2026-12-31'
    
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: '2026-12-31'
  actual: ''
  operator: 'strictEqual'
```

## A4

- 테스트 파일: `src/data/sync/dayLogCloudCommit.overlap.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 A4: 일지 저장이 둘 겹치면 먼저 끝난 저장 내용이 화면에서 되돌아간다.
// commitMainDayLogToCloud는 성공 뒤 저장 시작 때 읽어 둔 그 차량 일지 전체(nextData)를 commitLogWorkData로 통째로 넣는다.
import '../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'
import { createFakeSupabase, wait } from '../../testSupport/fakeSupabaseClient.js'

const { fakeSupabase, handlers, resetHandlers, emptyOkHandlers } = createFakeSupabase()
mock.module('../cloud/supabaseClient.ts', { namedExports: { supabase: fakeSupabase } })

const { commitMainDayLogToCloud } = await import('./dayLogCloudCommit.ts')
const { beginSessionEpoch, endCloudSession } = await import('../cloud/cloudSession.ts')
const { setHydration } = await import('../store/app-store.ts')
const { commitCars, commitWorkData } = await import('../store/commitHelpers.ts')
const { readOwnerLogWorkData } = await import('../store/ownerWorkDataHooks.ts')
const { saveDayRecord } = await import('../../domain/day-record.ts')

test('A4: 다른 날짜 저장 두 개가 겹쳐도 둘 다 화면(Store)에 남는다', async () => {
  const ownerKey = 'a4-owner'
  resetHandlers()
  Object.assign(handlers, emptyOkHandlers())
  // 10월 1일 저장은 늦게(40ms), 10월 2일 저장은 빨리(5ms) 끝나는 서버.
  handlers.rpc = {
    save_day_log: async (/** @type {{ p_log: { work_date: string } }} */ args) => {
      await wait(args.p_log.work_date === '2026-10-01' ? 40 : 5)
      return { data: 1, error: null }
    },
  }
  beginSessionEpoch('a4-user', ownerKey)
  setHydration({ status: 'ready', userId: 'a4-user', ownerKey })
  commitCars(ownerKey, [{ id: 'car-main', type: 'main', number: '12가3456', supabaseId: 700 }])
  commitWorkData(ownerKey, {})

  // useDayDraft.commitNow와 같은 순서: 저장 시작 때 그 차량 일지 전체를 읽고 그 날짜만 바꾼 next를 만든다.
  const startSave = (/** @type {string} */ dateKey, /** @type {number} */ fixedCount) => {
    const latest = readOwnerLogWorkData(ownerKey, 'main')
    const next = saveDayRecord(latest, dateKey, { isOff: false, fixedCount, palletCount: 0, callDetails: [], fixedRouteCounts: {} })
    return commitMainDayLogToCloud({ ownerKey, logId: 'main', dateKey, previousData: latest, nextData: next })
  }

  const first = startSave('2026-10-01', 3) // 먼저 시작, 늦게 끝남
  const second = startSave('2026-10-02', 5) // 나중 시작, 먼저 끝남
  const results = await Promise.all([first, second])
  assert.deepEqual(results.map((r) => r.cloud && 'ok' in r && r.ok), [true, true], '둘 다 서버 저장 성공')

  const work = readOwnerLogWorkData(ownerKey, 'main')
  assert.equal(work['2026-10-01']?.fixedCount, 3, '10월 1일 저장분')
  assert.equal(work['2026-10-02']?.fixedCount, 5, `10월 2일 저장분이 사라짐: ${JSON.stringify(work['2026-10-02'])}`)
  endCloudSession()
})
```

### 실행 결과

```
not ok 1 - A4: 다른 날짜 저장 두 개가 겹쳐도 둘 다 화면(Store)에 남는다
  failureType: 'testCodeFailure'
  error: |-
    10월 2일 저장분이 사라짐: undefined
    
    undefined !== 5
    
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: 5
  operator: 'strictEqual'
```

## A5

- 테스트 파일: `src/data/sync/invoicesSave.partialFail.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 A5: 세금계산서 저장이 중간에 실패하면 다음 저장 때 이미 들어간 계산서가 서버에 한 번 더 생긴다.
// syncTaxInvoices는 새 서버 번호를 지역 변수 next에만 붙이다가 중간 실패면 next 전체를 버리고 던진다(Store에도 안 들어감).
import '../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'
import { createFakeSupabase } from '../../testSupport/fakeSupabaseClient.js'

const { fakeSupabase, handlers, resetHandlers, emptyOkHandlers } = createFakeSupabase()
mock.module('../cloud/supabaseClient.ts', { namedExports: { supabase: fakeSupabase } })

const { saveInvoices } = await import('./invoicesSave.ts')
const { beginSessionEpoch, endCloudSession } = await import('../cloud/cloudSession.ts')
const { setHydration, getState } = await import('../store/app-store.ts')
const { commitCars } = await import('../store/commitHelpers.ts')

test('A5: 두 번째 계산서에서 저장이 실패한 뒤 다시 저장해도 첫 계산서는 서버에 한 번만 들어간다', async () => {
  const ownerKey = 'a5-owner'
  resetHandlers()
  Object.assign(handlers, emptyOkHandlers())
  beginSessionEpoch('a5-user', ownerKey)
  setHydration({ status: 'ready', userId: 'a5-user', ownerKey })
  commitCars(ownerKey, [{ id: 'car-main', type: 'main', number: '12가3456', supabaseId: 700 }])

  /** @type {Array<string>} */
  const insertedLocalIds = []
  let failSecond = true
  let nextServerId = 9000
  handlers.tax_invoices = {
    insert: (/** @type {{ raw: { id: string } }} */ row) => {
      if (row.raw.id === 'inv-2' && failSecond) return { data: null, error: { message: 'network down' } }
      insertedLocalIds.push(row.raw.id)
      nextServerId += 1
      return { data: { id: nextServerId }, error: null }
    },
  }

  const items = [
    { id: 'inv-1', flow: 'sales', monthKey: '2026-10', clientName: '한진', supplyAmount: 100000, taxAmount: 10000, totalAmount: 110000, status: 'draft' },
    { id: 'inv-2', flow: 'sales', monthKey: '2026-10', clientName: '대한', supplyAmount: 200000, taxAmount: 20000, totalAmount: 220000, status: 'draft' },
  ]

  await assert.rejects(() => saveInvoices(ownerKey, /** @type {any} */ (items)), (/** @type {{ message?: string }} */ error) => error?.message === 'network down')
  // 화면은 Store 값(실패라 그대로)으로 다시 저장한다 — TaxInvoiceTab.persist와 같은 입력.
  const again = getState().invoices[ownerKey]?.length ? getState().invoices[ownerKey] : items
  failSecond = false
  await saveInvoices(ownerKey, /** @type {any} */ (again))

  const inv1Count = insertedLocalIds.filter((id) => id === 'inv-1').length
  assert.equal(inv1Count, 1, `inv-1이 서버에 ${inv1Count}번 들어감(중복 계산서)`)
  endCloudSession()
})
```

### 실행 결과

```
not ok 1 - A5: 두 번째 계산서에서 저장이 실패한 뒤 다시 저장해도 첫 계산서는 서버에 한 번만 들어간다
  failureType: 'testCodeFailure'
  error: |-
    inv-1이 서버에 2번 들어감(중복 계산서)
    
    2 !== 1
    
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: 1
  actual: 2
  operator: 'strictEqual'
```

## A6

- 테스트 파일: `src/data/sync/invoicesSave.renamedCar.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 A6: 기사 차량 번호를 바꾸면(그 차량 계산서가 서버에 있을 때) 그 뒤 모든 세금계산서 저장이 계속 실패한다.
// 계산서는 raw.carNumber(옛 번호)로 차량을 찾는데 vehicleRename은 계산서 차량 번호를 옮기지 않는다 → 2.5초 기다린 뒤 던짐.
import '../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'
import { createFakeSupabase } from '../../testSupport/fakeSupabaseClient.js'

const { fakeSupabase, handlers, resetHandlers, emptyOkHandlers } = createFakeSupabase()
mock.module('../cloud/supabaseClient.ts', { namedExports: { supabase: fakeSupabase } })

const { saveInvoices } = await import('./invoicesSave.ts')
const { beginSessionEpoch, endCloudSession } = await import('../cloud/cloudSession.ts')
const { setHydration } = await import('../store/app-store.ts')
const { commitCars } = await import('../store/commitHelpers.ts')

test('A6: 기사 차량 번호를 바꾼 뒤에도 메인 차량 새 계산서는 저장된다', async () => {
  const ownerKey = 'a6-owner'
  resetHandlers()
  Object.assign(handlers, emptyOkHandlers())
  handlers.tax_invoices = { insert: () => ({ data: { id: 9101 }, error: null }), update: () => ({ data: null, error: null }) }
  beginSessionEpoch('a6-user', ownerKey)
  setHydration({ status: 'ready', userId: 'a6-user', ownerKey })
  // 기사 차량 "34나5678" → "56다7890"으로 번호를 바꾼 뒤(차량 id·서버 번호는 같음).
  commitCars(ownerKey, [
    { id: 'car-main', type: 'main', number: '12가3456', supabaseId: 700 },
    { id: 'car-sub', type: 'sub', number: '56다7890', supabaseId: 801 },
  ])

  const items = [
    // 번호 바꾸기 전에 서버에 저장된 그 차량 계산서 — 차량 번호가 옛 번호 그대로 남아 있다.
    { id: 'inv-old', supabaseId: 5001, flow: 'sales', monthKey: '2026-09', clientName: '한진', carNumber: '34나5678', supplyAmount: 1, taxAmount: 0, totalAmount: 1, status: 'issued' },
    // 지금 새로 저장하려는 메인 차량 계산서.
    { id: 'inv-new', flow: 'sales', monthKey: '2026-10', clientName: '대한', carNumber: '12가3456', supplyAmount: 100000, taxAmount: 10000, totalAmount: 110000, status: 'draft' },
  ]

  await assert.doesNotReject(() => saveInvoices(ownerKey, /** @type {any} */ (items)))
  endCloudSession()
})
```

### 실행 결과

```
not ok 1 - A6: 기사 차량 번호를 바꾼 뒤에도 메인 차량 새 계산서는 저장된다
  failureType: 'testCodeFailure'
  error: |-
    Got unwanted rejection.
    Actual message: "방금 등록한 차량이 아직 저장 중입니다. 잠시 후 다시 시도해 주세요."
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  actual:
  error: '방금 등록한 차량이 아직 저장 중입니다. 잠시 후 다시 시도해 주세요.'
```

## K1

- 테스트 파일: `src/features/settings/ui/RoutePresetEditor.staleOverwrite.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 K1: 노선을 빨리 이어서 지우면 앞에서 지운 노선이 되살아난다.
// ×가 "지금 화면의 설정 전체"(removeFixedRoutePreset 결과)를 넘기는데, 로그인이면 서버 저장이 끝나야 화면 값이 바뀌어
// 두 번째 ×는 옛 설정 전체로 첫 번째 삭제를 덮는다(settingsSave.ts saveSettingsNow가 { ...current, ...changes }).
import { register } from 'node:module'
import { pathToFileURL } from 'node:url'

register(pathToFileURL('./src/testSupport/jsxLoaderHook.mjs').href, import.meta.url)

import '../../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'
import { createFakeSupabase, wait } from '../../../testSupport/fakeSupabaseClient.js'

const { fakeSupabase, handlers, resetHandlers, emptyOkHandlers } = createFakeSupabase()
mock.module('../../../data/cloud/supabaseClient.ts', { namedExports: { supabase: fakeSupabase } })

Reflect.set(globalThis, 'IS_REACT_ACT_ENVIRONMENT', true)

const React = await import('react')
const { createRoot } = await import('react-dom/client')
const { act } = React
const { default: RoutePresetEditor } = await import('./RoutePresetEditor.jsx')
const { normalizeSettings } = await import('../../../domain/practiceSettings.ts')
const { savePracticeSettings } = await import('../../../data/sync/settingsSave.ts')
const { beginSessionEpoch, endCloudSession } = await import('../../../data/cloud/cloudSession.ts')
const { setHydration } = await import('../../../data/store/app-store.ts')
const { commitSettings } = await import('../../../data/store/commitHelpers.ts')
const { readOwnerSettings } = await import('../../../data/store/ownerDataHooks.ts')

test('K1: 노선 A를 지우고 바로 B를 지우면 둘 다 지워진 채로 남는다', async () => {
  const ownerKey = 'k1-owner'
  resetHandlers()
  Object.assign(handlers, emptyOkHandlers())
  // 서버 저장(프로필 행 upsert)이 20ms 걸린다 — 그동안 화면 설정은 옛 값.
  handlers.profiles = { ...handlers.profiles, upsert: async () => { await wait(20); return { data: null, error: null } } }
  beginSessionEpoch('k1-owner', ownerKey)
  setHydration({ status: 'ready', userId: 'k1-owner', ownerKey })
  const settings = normalizeSettings({
    fixedOn: true, fixedRouteOn: true,
    fixedRoutePresets: [{ id: 'route-a', loadLoc: '부산', unloadLoc: '대구' }, { id: 'route-b', loadLoc: '평택', unloadLoc: '용인' }],
  })
  commitSettings(ownerKey, settings)

  /** @type {Array<Promise<unknown>>} */
  const saves = []
  const container = document.createElement('div')
  document.body.appendChild(container)
  const root = createRoot(container)
  // AppSettingsPage.patch와 같은 저장 — 설정 prop은 서버 저장이 끝나기 전이라 그대로(옛 값).
  await act(async () => {
    root.render(React.createElement(RoutePresetEditor, {
      scope: 'main', settings, onPatch: (/** @type {object} */ patch) => { saves.push(savePracticeSettings(ownerKey, /** @type {any} */ (patch))) },
    }))
  })
  try {
    const deleteA = /** @type {HTMLButtonElement} */ (container.querySelector('button[aria-label="부산 → 대구 노선 삭제"]'))
    const deleteB = /** @type {HTMLButtonElement} */ (container.querySelector('button[aria-label="평택 → 용인 노선 삭제"]'))
    await act(async () => { deleteA.click() })
    await act(async () => { deleteB.click() })
    await Promise.all(saves)

    const left = (readOwnerSettings(ownerKey).fixedRoutePresets || []).map((route) => `${route.loadLoc} → ${route.unloadLoc}`)
    assert.deepEqual(left, [], `지운 노선이 되살아남: ${JSON.stringify(left)}`)
  } finally {
    await act(async () => { root.unmount() })
    container.remove()
    endCloudSession()
  }
})
```

### 실행 결과

```
not ok 1 - K1: 노선 A를 지우고 바로 B를 지우면 둘 다 지워진 채로 남는다
  failureType: 'testCodeFailure'
  error: |-
    지운 노선이 되살아남: ["부산 → 대구"]
    + actual - expected
    
    + [
    +   '부산 → 대구'
    + ]
    - []
    
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected:
  actual:
    0: '부산 → 대구'
  operator: 'deepStrictEqual'
```

## I2

- 테스트 파일: `src/features/account/ui/OnboardingPage.doubleFinish.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 I2: 첫 설정 [완료하기]를 빨리 두 번 누르면 두 번 저장된다(버튼이 안 잠김).
// App.jsx의 onFinish는 applyOnboardingWizard(설정 저장 + 메인 차량 등록)를 기다리는 동안 다시 불릴 수 있다.
import { register } from 'node:module'
import { pathToFileURL } from 'node:url'

register(pathToFileURL('./src/testSupport/jsxLoaderHook.mjs').href, import.meta.url)

import '../../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { test } from 'node:test'

Reflect.set(globalThis, 'IS_REACT_ACT_ENVIRONMENT', true)

const React = await import('react')
const { createRoot } = await import('react-dom/client')
const { act } = React
const { default: OnboardingPage } = await import('./OnboardingPage.jsx')

/** @param {string} label */
function buttonByText(label) {
  const button = [...document.querySelectorAll('button')].find((item) => item.textContent?.trim() === label)
  assert.ok(button, `${label} 버튼`)
  return /** @type {HTMLButtonElement} */ (button)
}

/** @param {HTMLInputElement} input @param {string} value */
async function typeInto(input, value) {
  const setter = Object.getOwnPropertyDescriptor(Object.getPrototypeOf(input), 'value')?.set
  await act(async () => { setter?.call(input, value); input.dispatchEvent(new window.Event('input', { bubbles: true })) })
}

test('I2: [완료하기]를 빨리 두 번 눌러도 저장(onFinish)은 한 번만', async () => {
  let calls = 0
  /** @type {() => void} */
  let finishSave = () => {}
  // 서버 저장을 기다리는 동안(App.jsx: await applyOnboardingWizard → goHome) 화면은 그대로 남는다.
  const onFinish = () => { calls += 1; return new Promise((resolve) => { finishSave = () => resolve(undefined) }) }

  const container = document.createElement('div')
  document.body.appendChild(container)
  const root = createRoot(container)
  await act(async () => { root.render(React.createElement(OnboardingPage, { onFinish })) })
  try {
    await act(async () => { buttonByText('둘 다 섞어서 해요').click() })
    await act(async () => { buttonByText('다음').click() })
    await act(async () => { buttonByText('네, 아직 입금되지 않은 운송료도 관리할게요').click() })
    await act(async () => { buttonByText('다음').click() })
    await act(async () => { buttonByText('다음').click() })
    await typeInto(/** @type {HTMLInputElement} */ (container.querySelector('#onboardingCarNumber')), '12가3456')

    const done = buttonByText('완료하기')
    await act(async () => { done.click() })
    await act(async () => { done.click() })
    assert.equal(calls, 1, `저장이 ${calls}번 불림 — 같은 메인 차량 등록 요청이 ${calls}번`)
  } finally {
    finishSave()
    await act(async () => { root.unmount() })
    container.remove()
  }
})
```

### 실행 결과

```
not ok 1 - I2: [완료하기]를 빨리 두 번 눌러도 저장(onFinish)은 한 번만
  failureType: 'testCodeFailure'
  error: |-
    저장이 2번 불림 — 같은 메인 차량 등록 요청이 2번
    
    2 !== 1
    
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: 1
  actual: 2
  operator: 'strictEqual'
```

## M3

- 테스트 파일: `src/features/cars/ui/CarListPage.doubleSave.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 M3: 차량 창 [저장]을 빨리 두 번 누르면 두 번 저장된다(저장 중 잠금 없음).
// 지출 창은 로드맵 111에서 잠금으로 고쳤는데(sot §6), 차량 창(useCarListActions.save·CarFormModal [저장])에는 없다.
import { register } from 'node:module'
import { pathToFileURL } from 'node:url'

register(pathToFileURL('./src/testSupport/jsxLoaderHook.mjs').href, import.meta.url)

import '../../../testSupport/stubSupabaseClient.js'
import '../../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'

Reflect.set(globalThis, 'IS_REACT_ACT_ENVIRONMENT', true)

let vehicleSaves = 0
/** @type {Array<() => void>} */
const pending = []
mock.module('../../../data/sync/vehicleMutations.ts', {
  namedExports: {
    // 서버 저장이 끝나기 전(응답 대기 중)에 두 번째 누름이 들어오게 한다.
    requestVehicleSave: (/** @type {{ draft: { number: string } }} */ { draft }) => {
      vehicleSaves += 1
      return new Promise((resolve) => {
        pending.push(() => resolve({ failed: false, toast: '차량을 등록했습니다.', saved: { id: `car-${vehicleSaves}`, number: draft.number }, renamedFrom: null }))
      })
    },
  },
})
mock.module('../api/carInviteFromDraft.js', {
  namedExports: { todayIsoDate: () => '2026-10-26', saveInviteAfterVehicle: async () => null },
})

const React = await import('react')
const { withSession } = await import('../../../testSupport/withSession.js')
const { withToast } = await import('../../../testSupport/withToast.js')
const { createRoot } = await import('react-dom/client')
const { act } = React
const { MemoryRouter } = await import('react-router-dom')
const { default: CarListPage } = await import('./CarListPage.jsx')
const { commitCars } = await import('../../../data/store/commitHelpers.ts')
const { beginSessionEpoch, endCloudSession } = await import('../../../data/cloud/cloudSession.ts')

test('M3: 새 차량 창 [저장]을 빨리 두 번 눌러도 차량 저장 요청은 한 번만', async () => {
  const ownerKey = 'm3-owner'
  beginSessionEpoch(ownerKey, ownerKey)
  commitCars(ownerKey, [])
  const container = document.createElement('div')
  document.body.appendChild(container)
  const root = createRoot(container)
  await act(async () => {
    root.render(withToast(React.createElement(MemoryRouter, { initialEntries: ['/app/cars'] },
      withSession(React.createElement(CarListPage, {}), { ownerKey, session: { userId: ownerKey, name: '차주', phone: '' } })), () => {}))
  })
  /** @param {string} text */
  const button = (text) => /** @type {HTMLButtonElement|undefined} */ ([...container.querySelectorAll('button')].find((el) => el.textContent?.trim() === text))
  try {
    await act(async () => { button('+ 추가')?.click() })
    const number = /** @type {HTMLInputElement} */ (container.querySelector('#newCarNumber'))
    const setter = Object.getOwnPropertyDescriptor(Object.getPrototypeOf(number), 'value')?.set
    await act(async () => { setter?.call(number, '12가3456'); number.dispatchEvent(new window.Event('input', { bubbles: true })) })

    const save = button('저장')
    assert.ok(save)
    await act(async () => { save.click() })
    await act(async () => { save.click() })
    assert.equal(vehicleSaves, 1, `차량 저장 요청이 ${vehicleSaves}번 — 같은 차량이 ${vehicleSaves}대 등록될 수 있음`)
  } finally {
    pending.forEach((resolve) => resolve())
    await act(async () => { root.unmount() })
    container.remove()
    endCloudSession()
  }
})
```

### 실행 결과

```
not ok 1 - M3: 새 차량 창 [저장]을 빨리 두 번 눌러도 차량 저장 요청은 한 번만
  failureType: 'testCodeFailure'
  error: |-
    차량 저장 요청이 2번 — 같은 차량이 2대 등록될 수 있음
    
    2 !== 1
    
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: 1
  actual: 2
  operator: 'strictEqual'
```

## R2

- 테스트 파일: `src/features/clients/ui/ClientListPage.doubleSave.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 R2: 거래처 창 [저장]을 빨리 두 번 누르면 같은 거래처가 두 개 생긴다(저장 중 잠금 없음, 같은 이름 검사도 없음).
import { register } from 'node:module'
import { pathToFileURL } from 'node:url'

register(pathToFileURL('./src/testSupport/jsxLoaderHook.mjs').href, import.meta.url)

import '../../../testSupport/stubSupabaseClient.js'
import '../../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'

Reflect.set(globalThis, 'IS_REACT_ACT_ENVIRONMENT', true)

let clientSaves = 0
/** @type {Array<() => void>} */
const pending = []
mock.module('../../../data/sync/clientMutations.ts', {
  namedExports: {
    // 서버 저장 응답을 기다리는 동안 두 번째 누름이 들어오게 한다.
    requestClientSave: (/** @type {{ clients: Array<object> }} */ { clients }) => {
      clientSaves += 1
      return new Promise((resolve) => { pending.push(() => resolve({ clients, toast: '거래처를 등록했습니다.', failed: false, saved: null })) })
    },
    requestClientReorder: async () => ({ toast: null, failed: false }),
  },
})

const React = await import('react')
const { withSession } = await import('../../../testSupport/withSession.js')
const { withToast } = await import('../../../testSupport/withToast.js')
const { createRoot } = await import('react-dom/client')
const { act } = React
const { MemoryRouter } = await import('react-router-dom')
const { default: ClientListPage } = await import('./ClientListPage.jsx')
const { commitClients } = await import('../../../data/store/commitHelpers.ts')
const { beginSessionEpoch, endCloudSession } = await import('../../../data/cloud/cloudSession.ts')

test('R2: 새 거래처 창 [저장]을 빨리 두 번 눌러도 거래처 저장 요청은 한 번만', async () => {
  const ownerKey = 'r2-owner'
  beginSessionEpoch(ownerKey, ownerKey)
  commitClients(ownerKey, [])
  const container = document.createElement('div')
  document.body.appendChild(container)
  const root = createRoot(container)
  await act(async () => {
    root.render(withToast(React.createElement(MemoryRouter, { initialEntries: ['/app/clients'] },
      withSession(React.createElement(ClientListPage, {}), { ownerKey, session: { userId: ownerKey, name: '차주', phone: '' } })), () => {}))
  })
  /** @param {string} text */
  const button = (text) => /** @type {HTMLButtonElement|undefined} */ ([...document.querySelectorAll('button')].find((el) => el.textContent?.trim() === text))
  try {
    await act(async () => { button('+ 추가')?.click() })
    const name = /** @type {HTMLInputElement} */ (document.querySelector('#clientCompanyName'))
    const setter = Object.getOwnPropertyDescriptor(Object.getPrototypeOf(name), 'value')?.set
    await act(async () => { setter?.call(name, '한진'); name.dispatchEvent(new window.Event('input', { bubbles: true })) })

    const save = button('저장')
    assert.ok(save)
    await act(async () => { save.click() })
    await act(async () => { save.click() })
    assert.equal(clientSaves, 1, `거래처 저장 요청이 ${clientSaves}번 — "한진"이 ${clientSaves}개 생길 수 있음`)
  } finally {
    pending.forEach((resolve) => resolve())
    await act(async () => { root.unmount() })
    container.remove()
    endCloudSession()
  }
})
```

### 실행 결과

```
not ok 1 - R2: 새 거래처 창 [저장]을 빨리 두 번 눌러도 거래처 저장 요청은 한 번만
  failureType: 'testCodeFailure'
  error: |-
    거래처 저장 요청이 2번 — "한진"이 2개 생길 수 있음
    
    2 !== 1
    
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: 1
  actual: 2
  operator: 'strictEqual'
```

## A2

- 테스트 파일: `src/features/day-log/hooks/dayDraftLifecycle.noVehicle.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 A2: 서버에 차량이 하나도 없는 로그인 계정은 일지가 서버에도 휴대폰에도 저장되지 않는데 화면엔 "저장됨".
// shouldCommitDayLogToCloud가 서버 차량이 없으면 false → 기기 경로(commitLocal)인데, 로그인 계정은 commitBatch가 기기에 안 쓴다(메모리만).
import '../../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'
import { createFakeSupabase } from '../../../testSupport/fakeSupabaseClient.js'

const { fakeSupabase, handlers, resetHandlers, emptyOkHandlers, countOf } = createFakeSupabase()
mock.module('../../../data/cloud/supabaseClient.ts', { namedExports: { supabase: fakeSupabase } })

const { createDayDraftSaver } = await import('./dayDraftLifecycle.js')
const { beginSessionEpoch, endCloudSession } = await import('../../../data/cloud/cloudSession.ts')
const { setHydration } = await import('../../../data/store/app-store.ts')
const { commitCars, commitWorkData } = await import('../../../data/store/commitHelpers.ts')
const { readJsonKey } = await import('../../../data/store/persist.ts')

test('A2: 차량 없는 로그인 계정에서 일지가 "저장됨"이면 서버나 휴대폰 어딘가에는 남아 있다', async () => {
  const ownerKey = 'a2-owner'
  resetHandlers()
  Object.assign(handlers, emptyOkHandlers())
  handlers.rpc = { save_day_log: () => ({ data: 1, error: null }) }
  beginSessionEpoch('a2-owner', ownerKey)
  setHydration({ status: 'ready', userId: 'a2-owner', ownerKey })
  commitCars(ownerKey, []) // 온보딩에서 차량번호를 비운 계정
  commitWorkData(ownerKey, {})

  /** @type {Array<string>} */
  const statuses = []
  const saver = createDayDraftSaver({
    ownerKey, logId: 'main', dateKey: '2026-10-26',
    draft: { isOff: false, fixedCount: 2, palletCount: 0, callDetails: [], fixedRouteCounts: {} },
    setAutoSaveStatus: (/** @type {string} */ status) => { statuses.push(status) },
  })
  saver.commitNow()
  await new Promise((resolve) => setTimeout(resolve, 20))

  const serverWrites = countOf('rpc', 'save_day_log')
  const onDevice = readJsonKey('workData', ownerKey, {})['2026-10-26']
  assert.equal(statuses.at(-1), 'saved', '화면 표시는 "저장됨"')
  assert.ok(
    serverWrites > 0 || onDevice,
    `"저장됨"인데 서버 저장 ${serverWrites}번, 휴대폰 저장 ${onDevice ? '있음' : '없음'} — 새로고침하면 사라짐`,
  )
  endCloudSession()
})
```

### 실행 결과

```
not ok 1 - A2: 차량 없는 로그인 계정에서 일지가 "저장됨"이면 서버나 휴대폰 어딘가에는 남아 있다
  failureType: 'testCodeFailure'
  error: '"저장됨"인데 서버 저장 0번, 휴대폰 저장 없음 — 새로고침하면 사라짐'
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: true
  operator: '=='
```

## A3

- 테스트 파일: `src/data/sync/mainDayLogRouting.subOnly.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 A3: 메인 차량 없이 기사 차량만 있는 차주는 메인 일지가 기사 차량 칸에 저장된다.
// vehicleSupabaseIdForLog(ownerKey, 'main')이 "메인을 못 찾으면 아무 서버 차량" 규칙이라 기사 차량 번호를 돌려준다.
import '../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'
import { createFakeSupabase } from '../../testSupport/fakeSupabaseClient.js'

const { fakeSupabase, handlers, resetHandlers, emptyOkHandlers } = createFakeSupabase()
mock.module('../cloud/supabaseClient.ts', { namedExports: { supabase: fakeSupabase } })

const { commitMainDayLogToCloud } = await import('./dayLogCloudCommit.ts')
const { beginSessionEpoch, endCloudSession } = await import('../cloud/cloudSession.ts')
const { setHydration } = await import('../store/app-store.ts')
const { commitCars } = await import('../store/commitHelpers.ts')

test('A3: 메인 차량이 없는 차주의 메인 일지는 기사 차량(vehicle 801)으로 저장되지 않는다', async () => {
  const ownerKey = 'a3-owner'
  resetHandlers()
  Object.assign(handlers, emptyOkHandlers())
  /** @type {Array<string|number>} */
  const savedVehicles = []
  handlers.rpc = {
    save_day_log: (/** @type {{ p_log: { vehicle_id: string|number } }} */ args) => {
      savedVehicles.push(args.p_log.vehicle_id)
      return { data: 1, error: null }
    },
  }
  beginSessionEpoch('a3-owner', ownerKey)
  setHydration({ status: 'ready', userId: 'a3-owner', ownerKey })
  // 차주 계정인데 메인 차량은 없고 기사 차량만 있다.
  commitCars(ownerKey, [{ id: 'car-sub', type: 'sub', number: '56다7890', supabaseId: 801, driverName: '김기사' }])

  await commitMainDayLogToCloud({
    ownerKey, logId: 'main', dateKey: '2026-10-26',
    previousData: {}, nextData: { '2026-10-26': { isOff: false, fixedCount: 1, callDetails: [] } },
  })

  assert.ok(!savedVehicles.includes(801), `메인 일지가 기사 차량(801) 칸에 저장됨: ${JSON.stringify(savedVehicles)}`)
  endCloudSession()
})
```

### 실행 결과

```
not ok 1 - A3: 메인 차량이 없는 차주의 메인 일지는 기사 차량(vehicle 801)으로 저장되지 않는다
  failureType: 'testCodeFailure'
  error: '메인 일지가 기사 차량(801) 칸에 저장됨: [801]'
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: true
  actual: false
  operator: '=='
```

## I1

- 테스트 파일: `src/features/account/api/memberRestoreFixedRoute.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 I1: 데이터 불러오기에서 기사 차량 고정 노선 거래처 연결이 풀린다.
// 고정 노선 거래처는 차량 범위마다 하나(sot §4-4e, domain/clients.ts upsertClient)인데,
// newClientsAgainst의 linkedTaken은 계정 전체에 하나라도 있으면 새로 들어오는 거래처의 연결을 끈다.
import assert from 'node:assert/strict'
import { mock, test } from 'node:test'

const cars = [
  { id: 'c-main', type: 'main', number: '12가3456', supabaseId: 'v-main' },
  { id: 'c-sub', type: 'sub', number: '34나5678', supabaseId: 'v-sub' }, // 미연동 기사 차량
]
mock.module('../../../data/store/ownerDataHooks.ts', {
  namedExports: {
    readOwnerCars: () => cars,
    readOwnerDrivers: () => [],
    // 지금 계정: 메인(범위 없음) 고정 노선 거래처 A
    readOwnerClients: () => [{ id: 'cl-a', companyName: '한빛물류', fixedRouteLinked: true, fixedUnitPrice: '250000' }],
    readOwnerExpenses: () => [],
    readOwnerInvoices: () => [],
  },
})
mock.module('../../../data/cloud/cloudSession.ts', { namedExports: { getCloudUserId: () => 'o-1' } })
mock.module('../../../data/sync/clientCloudSave.ts', { namedExports: { saveClientsToCloud: async () => ({ failed: false }) } })
mock.module('../../../data/sync/expensesSave.ts', { namedExports: { saveExpenses: async () => {} } })
mock.module('../../../data/sync/invoicesSave.ts', { namedExports: { saveInvoices: async () => {} } })

const { pickMemberRecords } = await import('./memberRestoreRecords.js')

test('I1: 파일의 기사 차량 전용 고정 노선 거래처 B는 메인에 A가 있어도 연결된 채로 들어온다', () => {
  const file = {
    clients: [{
      id: 'cl-b', companyName: '금오물류', scopedToVehicleNumber: '34나5678',
      fixedRouteLinked: true, fixedUnitPrice: '100000',
    }],
  }
  const picks = pickMemberRecords('o-1', file)
  assert.ok(picks, '파일 모양은 올바름')
  const b = picks.clients.find((client) => client.id === 'cl-b')
  assert.ok(b, 'B는 불러올 대상')
  assert.equal(b.fixedRouteLinked, true, '기사 차량(34나5678) 범위에는 고정 노선 거래처가 없는데 B의 "고정 노선 연동"이 꺼짐')
})
```

### 실행 결과

```
not ok 1 - I1: 파일의 기사 차량 전용 고정 노선 거래처 B는 메인에 A가 있어도 연결된 채로 들어온다
  failureType: 'testCodeFailure'
  error: |-
    기사 차량(34나5678) 범위에는 고정 노선 거래처가 없는데 B의 "고정 노선 연동"이 꺼짐
    
    false !== true
    
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: true
  actual: false
  operator: 'strictEqual'
```

## B5

- 테스트 파일: `src/data/sync/pendingWorkDataWrites.newCallDetail.test.js`
- 재현: 됨

### 테스트 코드

```js
// 전수점검 B5: 새 콜 상세가 "저장 대기" 검사에 걸린다.
// buildCallDetail이 새 콜에 linkedLoadIndex: undefined 칸을 만들고, 검사기(v.exactOptional)는 값 없는 칸을 거부한다.
// 그래서 일지 휴대폰 저장이 실패했을 때 저장 대기로 보관하는 단계(registerPendingDayWrite)가 거절된다.
import '../../testSupport/stubSupabaseClient.js'
import '../../testSupport/setupDom.js'
import assert from 'node:assert/strict'
import { test } from 'node:test'

const { buildCallDetail } = await import('../../domain/call-details.ts')
const { registerPendingDayWrite } = await import('./pendingWorkDataWrites.ts')

test('B5: 방금 만든 콜 상세가 들어 있는 하루 기록도 저장 대기에 등록된다', () => {
  const built = buildCallDetail({ loadLoc: '평택', unloadLoc: '용인', fare: '150000', client: '' }, null, '2026-10-26', [])
  assert.ok('item' in built && built.item, '콜 상세 만들기 성공')
  assert.ok('linkedLoadIndex' in built.item, '새 콜에 linkedLoadIndex 칸이 있음')

  // dayDraftLifecycle.commitNow와 같은 patch(structuredClone은 값 없는 칸도 그대로 둔다).
  const patch = structuredClone({ isOff: false, fixedCount: 0, palletCount: 0, callDetails: [built.item], fixedRouteCounts: {} })
  const registered = registerPendingDayWrite('b5-owner', '2026-10-26', /** @type {any} */ (patch))
  assert.equal(registered, true, '저장 대기 등록이 거절됨 — 휴대폰 저장 실패 뒤 새로고침하면 이 콜을 잃을 수 있음')
})
```

### 실행 결과

```
not ok 1 - B5: 방금 만든 콜 상세가 들어 있는 하루 기록도 저장 대기에 등록된다
  failureType: 'testCodeFailure'
  error: |-
    저장 대기 등록이 거절됨 — 휴대폰 저장 실패 뒤 새로고침하면 이 콜을 잃을 수 있음
    
    false !== true
    
  code: 'ERR_ASSERTION'
  name: 'AssertionError'
  expected: true
  actual: false
  operator: 'strictEqual'
```

