# react-app 군더더기 점검 — 안 쓰는 코드·중복·주석 (2026-10-10)

보리 요청(2026-10-10): "react-app 전체 새로 점검".
**조사만 함. 코드 수정 없음.** 기준 시점: react-app `main` `fb6b7b8`.
대상: `react-app/src` 운영 파일(js·jsx 326개, css 58개, 약 2만 9천 줄). 테스트 파일은 참고만 함.
방법: 프로젝트 검사기(`oxlint`) + 직접 만든 검사(안 쓰는 이름·파일, 중복 묶음, 주석 비율, CSS 클래스 사용 여부) → 걸린 항목을 하나씩 열어 확인.

순서는 AGENTS §1 급한 순서(① 저장·유실 → ② 돈 → … → ⑥ 개선)를 따름.
아래 항목은 전부 **AI가 찾은 것이며, 보리 확인 전에는 실행 지시가 아님**.

---

## A. 같은 규칙이 두 군데 — 한쪽만 고치면 어긋남 (① 저장 · ② 돈)

| # | 위치 | 내용 | 위험 |
|---|---|---|---|
| A1 | `domain/financeCore.js:74` `getCallDetailCommissionAmount` ↔ `lib/reportDetail.js:45` `detailCommissionLabel` | "이 운행에 수수료가 붙나·얼마인가" 판단(저장된 기록 → 없으면 거래처 설정)을 **똑같이 두 번** 씀 | 수수료 규칙이 바뀌면 계산과 보고서 표시가 따로 놀 수 있음 (돈) |
| A2 | `lib/dayLogCloudCommit.js:55~68` ↔ `lib/syncWorkData.js:40~53` | 운행 상세를 서버 칸(`transport_details`)으로 바꾸는 코드가 **두 벌** | 새 칸을 한쪽에만 추가하면 다른 경로로 저장할 때 그 값이 빠짐 (저장) |

→ 고치는 방법: 한 곳에만 두고 다른 쪽은 불러 쓰기. 동작은 그대로.
→ 둘 다 플레이북 트리거(`lib/finance*`·저장 경로) — 착수 전 `docs/testing-playbook.md` 필수.

## B. 아무도 안 쓰는 코드 — 지워도 동작이 안 바뀜

### B1. 파일

| 파일 | 상태 |
|---|---|
| `lib/syncDeletedWorkDates.js` (+ 테스트) | 옛 "삭제 목록 모아 두었다 나중에 서버에서 지우기" 방식. `fbe91d5`(서버 성공 후에만 반영으로 전환)부터 **어디서도 호출 안 함**. 지금 삭제는 `lib/dailyLogRemoval.js`가 바로 처리. 파일 머리 주석은 아직 "`syncQueue.js`가 부른다"고 적혀 있어 틀림 |
| `components/CarFormModal.jsx`, `components/ClientFormModal.jsx` | 한 줄짜리 재수출 껍데기. 모두 `cars/`·`clients/` 안의 진짜 파일을 직접 불러서 **이 둘은 아무도 안 씀** |
| `main-calendar.css` | 빈 파일. 테스트(`day-log/inlineSheetCss.test.js`) 하나가 이 경로를 읽어서만 남아 있음 — 지우려면 테스트도 같이 손봐야 함 |

B1의 `syncDeletedWorkDates`와 관련된 "삭제 목록"(`workDataTombstones`)은 `hydrate.js` 등에서 아직 읽음 → **그쪽 정리는 별도 조사 필요**(비회원 경로 영향 확인 전엔 건드리지 않음).

### B2. 함수·값 (정의만 있고 어디서도 안 부름)

- `domain/money.js` `calcSupplyVat`, `monthFareSummary`
- `lib/cloudStorage.js` `writeJson`
- `lib/dirtyJournal.js` `clearDirtyDomain`
- `lib/mainDayLogRouting.js` `mainCarSupabaseId`
- `lib/outboxCommit.js` `commitWithOutboxAndFlush` (약 35줄, 저장 경로)
- `lib/ownerFinance.js` `persistWorkDataByLogId`
- `store/persistDayRecordLegacy.js` `coercePersistedFuelItem`, `coercePersistedMaintItem`, `coercePersistedMiscItem`
- `domain/finance.fixtures.js` `OVERLAP_LINKS` (테스트용 자료)

참고: 그 밖에 26개는 "파일 안에서만 쓰는데 `export`가 붙어 있음", 55개는 "테스트에서만 씀"(테스트를 위해 연 것이라 정상인 경우가 많음). 정리 효과가 작아 목록만 두고 우선순위 없음.

### B3. CSS 클래스 (코드 어디에도 이름이 없음)

| 파일 | 클래스 |
|---|---|
| `account-flow.css` | `auth-question-text`, `auth-secondary-btn`, `auth-role-tabs`, `auth-role-tab`, `auth-role-tab-icon`, `auth-role-subtitle`, `auth-link-text`, `auth-invite-row`, `auth-invite-text`, `auth-code-box`, `onboarding-sub-toggle-card`, `onboarding-sub-copy`, `practice-home` |
| `revenue/revenue.css` | `revenue-summary-card`, `revenue-summary-label`, `revenue-summary-amount`, `revenue-summary-sub`, `revenue-vehicle-list`, `revenue-vehicle-row`, `revenue-vehicle-meta`, `revenue-driver-select-chevron` |
| `calendar/calendar.css` | `main-practice-note`, `main-practice-back` |
| `clients/client-management.css` | `client-inline-field` |
| `day-log/call-detail-form.css` | `work-log-call-modal` |
| `customer-center.css`, `notice.css` | `support-card-label` |

→ 대부분 로그인 개편·새 홈 이전 화면의 흔적. `account-flow.css`(573줄)·`revenue.css`(272줄)는 200줄 초과 파일이라 줄이는 효과도 있음.

### B4. 옛 경로 유지용 재수출 껍데기 (쓰이는 중)

`lib/money.js`, `lib/finance.js`, `lib/calendar.js`, `lib/formatPhone.js`, `lib/receivables.js` — 실제 코드는 `domain/`에 있고, 이 파일들은 "옛 경로로 부르는 화면이 안 깨지게" 둔 것(파일 주석에 "나중에 지울 수 있다"고 적혀 있음). 운영 코드 51곳이 이 경로로 부름. `components/CarManagementPage.jsx`·`ClientManagementPage.jsx`도 같은 껍데기(이 둘은 `lazyPages.js`가 씀).
→ 지우려면 51곳의 불러오는 경로를 바꿔야 함. 동작은 안 바뀌지만 건드리는 파일이 많음.

## C. 주석 — 코드 설명이 아니라 작업 이력 (⑥)

- 운영 파일 160개(326개 중)에 **작업 이력형 주석 약 340줄**: "Step 3 라우터 셸…", "재감사 4번에서 만든…", "Step 9 슬라이스 B…", "FAIL 지적 1번" 등.
- 예: `app/MainPageRoute.jsx:2~15` — 14줄이 전부 언제 무엇을 바꿨는지의 이력. 지금 코드를 읽는 데 필요한 건 1~2줄.
- 이력은 이미 git 커밋에 있으므로 코드 주석에서는 "지금 왜 이렇게 하나" 1~2줄만 남기면 됨(메모 규칙 "서술형 긴 주석 금지"와 같은 취지).
- 이력형 주석이 많은 파일: `store/owner-state.js`(9줄), `day-log/useDayDraft.js`·`app/MainPageRoute.jsx`(각 8줄), `lib/pendingWorkDataWritesTypes.js`·`lib/directMutations.js`·`domain/clients.js`·`domain/calendarBadges.js`(각 7줄) 등.
- **정상인 것(정리 대상 아님)**: `financeTypes.js`·`clientTypes.js`처럼 주석 비율이 90%인 파일은 타입 선언(`@typedef`)이라 주석 형태가 맞음.

## D. 거의 같은 화면 두 벌 (⑥)

| 위치 | 겹침 |
|---|---|
| `clients/OwnerScopedClientsView.jsx`(157줄) ↔ `drivers/LinkedDriverClientsPage.jsx`(177줄) | 거래처 목록·저장·삭제 처리가 거의 같음(8줄 묶음 44개 겹침). 다른 건 "어느 차량 거래처인가" 찾는 부분뿐 |
| `revenue/DriverRevenueView.jsx` ↔ `revenue/OwnerRevenueView.jsx` | 8줄 묶음 12개 겹침 |
| `ReportShareModal.jsx` ↔ `documents/DailyInspectionShareModal.jsx` | 공유 창 처리 5묶음 겹침 |

→ 공용 부분을 하나로 뽑으면 한쪽만 고쳐지는 문제를 막음. 다만 화면 동작 확인(브라우저)이 필요한 작업이라 출시 전엔 급하지 않음.

## E. 건드리지 않길 권하는 것

- **과한 방어 코드처럼 보이는 것**: `Array.isArray(x) ? x : []` 61곳, `?? ''` 49곳, `|| {}` 54곳. 대부분 함수 설명(타입)이 "비어 있을 수 있음"으로 되어 있어 지금은 서로 맞음. 돈 계산·저장 줄에 많이 있어 걷어내는 득보다 계산 오류 위험이 큼 → **정리 안 함 권장**.
- `console.log`·`TODO` 남은 것: 사실상 없음(TODO 1개, `hydrateEmployedDriver.js`).

## F. 점검하다 함께 보인 규칙 관련 사항 (군더더기는 아님)

- 200줄 넘는 파일 22개(코드 13·CSS 9). 가장 큰 것 `account-flow.css` 573줄, `receivables.css` 425줄. B3를 지우면 일부 줄어듦.
- `// @ts-check` 없는 운영 파일 13개(`app/ComingSoonRoute.jsx`, `app/guestSessionPersist.js`, `app/lazyPages.js`, `app/sessionGate.js`, `app/syncFlushListeners.js`, `app/useHydrationLock.js`, `app/workLogNavigation.js`, `lib/singleFlight.js`, 그리고 B4 껍데기 5개).
- 타입 설명에 `unknown`이 45개 파일 133곳. **AGENTS §6 위반 — 보리 승인 이력 없음(보리 확인 2026-10-10).**
  - 뜻: 값의 종류(숫자·글자 등)를 "모름"으로 적어 두는 표시. 동작에는 영향 없음 — 검사기가 그 값의 실수를 덜 잡아 줌.
  - 들어온 때: 2026-08-27~10-09에 걸쳐 조금씩(최근 것도 있음). 과거 재감사에서 몇 군데 지운 기록(`owner-state.js:25`, `day-record.js:27`)이 있어 위반으로 다뤄 온 것 맞음.
  - 새는 이유: §5 리뷰 체크리스트 3번이 `any`·`@ts-ignore`·`as unknown as`만 보고 그냥 `unknown`은 안 봄.
  - AI 판단: 이미 프로젝트에 대체 표시(`JsonValue` — "서버·저장소에서 온 확인 전 값", `lib/pendingWorkDataWritesTypes.js`)가 있어 그걸로 바꾸거나 정확한 종류로 적으면 됨. 동작 변화 없는 표시만 바꾸는 작업이라 **4번(주석 정리) 앞에 3-B로 끼워 폴더별로** 처리. 다시 새지 않게 §5 3번에 `unknown` 추가는 AGENTS.md라 보리만 고칠 수 있음.
- `oxlint` 경고(테스트 제외) 31개 — 전부 React 화면 갱신 규칙 관련(예: `AppShell.jsx:96` 불필요한 의존값). 군더더기가 아니라 동작 쪽 문제라 따로 볼 일.

---

## 정리 순서 (보리 결정 2026-10-10: "그 순대로")

1. **A1·A2 중복 규칙 하나로 합치기** — A1 = 1-A `[x]`(`255da47`), A2 = 1-B `[x]`(`22951d4`, 한 벌이 죽은 코드라 삭제로 끝남). — 돈·저장이라 먼저. 파일 4개, 동작 그대로.
2. **B1·B2 안 쓰는 파일·함수 지우기** — B1 = 2-A `[x]`(`183fe39`), B2 = 2-B `[x]`(`b644be8`). — 파일 약 12개. 테스트 통과만 보면 됨.
3. **B3 안 쓰는 CSS 지우기** — `[x]`(`3fbac44`).
   - 3-B(보리 확인 2026-10-10 "추천순대로"): F의 `unknown` 표시를 `JsonValue`·정확한 종류로 바꾸기 — 연결 묶음별 3번. 3-B-1 `[x]`(`7fe99a1`, 53곳), 3-B-2 `[x]`(`dab2c5c`, `lib/` 15개), 3-B-3 `[x]`(`1e55fb8`, 나머지 35곳) — **완료, 운영 코드 타입 표시 `unknown` 0곳**.
4. **C 이력형 주석 줄이기** — 폴더별 4-A~4-E. 4-A `store/` `[x]`(`6a0412d`), 4-B `app/` `[x]`(`1b8a08c`), 4-C `domain/` `[x]`(`cbc34e8`), 4-D-1 `components/day-log·calendar` `[x]`(`6f01c5b`), 4-D-2 `components/` 나머지 `[x]`(`28fd903`), 4-E-1 `lib/` 저장·동기화 `[x]`(`0bc69f8`), 4-E-2 `lib/` 나머지 `[x]`(`ce6e118`). 놓친 곳은 4-F `[x]`(`fca9751`) — **4번 전체 완료**(운영 코드 작업 기록형 주석 0곳). — 파일이 160개라 폴더별로 나눠서(예: `store/`·`lib/`·`app/`·`components/`).
5. B4 껍데기 정리, D 화면 중복 — 출시 뒤로 미뤄도 됨.
