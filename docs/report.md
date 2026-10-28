# docs/report.md — 현재 슬라이스 착수지시서

131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 132-A 차량 없는 로그인 계정 — 일지·지출이 "저장됨"으로 보이고 사라짐 `[x]`

전수점검(2026-10-26) A2, 로드맵 132 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 132를 둘로 나눔: **132-A**(A2, 이번), **132-B**(A3 차주 메인 일지·지출이 기사 차량 칸으로 감 — 다음). A부터 하는 이유: B를 고치면 기사 차량만 있는 차주가 "차량 없음" 상태가 되므로, 그 상태에서 실패를 알리는 A가 먼저 있어야 함. DB 변경 없음.

### 쉬운 요약
- 로그인 계정인데 서버에 차량이 하나도 없으면, 운행일지와 지출이 서버에도 휴대폰에도 저장되지 않습니다. 그런데 화면에는 "저장됨"으로 나오고, 새로고침하면 사라집니다.
- 이런 계정은 실제로 생깁니다. 첫 설정 마지막 단계(차량 등록)에서 [건너뛰기]를 누르면 차량 없이 가입됩니다. 시험 서버에도 이런 계정이 2개 있습니다.
- 고치고 나면 저장되지 않았다는 사실을 알립니다. 화면은 "저장 실패"로 바뀌고, "메인 차량이 없어 저장하지 못했습니다. 차량 관리에서 메인 차량을 등록해 주세요." 알림이 뜹니다.

### 현재 상태 (코드 확인)
- 일지: `shouldCommitDayLogToCloud`는 서버 차량이 없으면 거짓을 돌려줌(`data/sync/mainDayLogRouting.ts:25-28`). 그래서 기기 저장(`commitLocal`, `features/day-log/hooks/dayDraftLifecycle.js:126-128`)으로 가는데, 로그인 세션은 `commitBatch`가 휴대폰에 쓰지 않음(`data/store/app-store.ts:119-124`). 결국 메모리에만 남는데 `markSaved`가 "저장됨"을 띄움(`dayDraftLifecycle.js:92`). 실패 재현 테스트는 `docs/full-code-review-failing-tests.md` A2에 있음.
- 지출(차주): 서버 차량이 없으면 `planExpenseVehicleTargets`가 빈 목록을 돌려줌(`domain/expenseVehicleRouting.ts:31`). 서버 호출 없이 `commitExpenses`로 화면만 바뀌고 성공 처리됨(`data/sync/expensesSave.ts:43-59`).
- 지출(연동 기사): 배정 차량이 없으면 같은 식으로 화면만 바뀜(`expensesSave.ts:32-38`).
- 실제 계정(시험 서버, 읽기 조회 2026-10-28): 프로필 14개 중 차량도 없고 연동 기사도 아닌 계정 2개, 기사 차량만 있는 차주 1개(132-B 대상), 연동 기사 5개.
- 첫 설정: 4단계의 [다음]은 차량번호가 비면 잠기지만, [건너뛰기]는 검사 없이 끝냄(`features/account/ui/OnboardingPage.jsx:37`, `:151`).
- 알림 문구는 화면마다 같은 문구를 쓰는 표가 있음(`shared/utils/failureMessages.js`, 로드맵 56 보리 확정 문구 표). 지금은 ①서버 실패 ②휴대폰 실패 ③로그인 바뀜 세 가지.

### 목표 상태 (기대 동작)
1. 로그인 계정에서 그 일지를 저장할 서버 차량이 없으면, 일지 자동 저장은 "저장 실패" 표시와 함께 알림 ④를 띄움. "저장됨"은 나오지 않음.
2. 1번 실패는 서버 저장 실패와 똑같이 처리함. 실패 날짜를 기록하고, 입력은 화면에 그대로 두고, 나갈 때 확인 창이 뜸(`settleCloud` 실패 갈래 그대로). 휴대폰에는 쓰지 않음(sot §5).
3. 로그인 계정에서 서버 차량이 없으면 지출 [저장]은 아무것도 바꾸지 않고 알림 ④를 돌려줌. 차주와 연동 기사 모두 해당함.
4. 새 알림 ④ 최종 문구: **"메인 차량이 없어 저장하지 못했습니다. 차량 관리에서 메인 차량을 등록해 주세요."** "못했"이 들어 있어 빨간 실패 알림 모양으로 뜸(`isFailureMessage`).
5. 비회원(휴대폰 저장), 서버 차량이 있는 차주, 배정 차량이 있는 연동 기사는 지금과 똑같이 동작함.
6. 기사 차량만 있는 차주는 이번에 바뀌지 않음. 지금처럼 기사 차량 칸으로 저장되며, 132-B에서 고침.

### 건드릴 파일
- `src/data/sync/mainDayLogRouting.ts` — 함수 `hasNoCloudVehicle(ownerKey, logId)`를 추가함. 로그인 상태이고 `vehicleSupabaseIdForLog`가 없음일 때 참. 주석: `/** 로그인 계정인데 이 일지를 저장할 서버 차량이 없음 — 로그인 기록은 휴대폰에 안 남아 그대로 두면 사라진다. */`
- `src/features/day-log/hooks/dayDraftLifecycle.js` — `commitNow`의 기기 갈래 앞에서 `hasNoCloudVehicle`이 참이면 `settleCloud({ ok: false, toast: NO_VEHICLE_SAVE_FAIL }, progress.draftRev, patch)`를 부름. 주석: `// 로그인 계정인데 저장할 서버 차량이 없으면 휴대폰에도 안 남으므로 실패로 알린다.`
- `src/data/sync/expensesSave.ts` — `saveExpenseDraft`에서 입력 검사 뒤 `hasNoCloudVehicle(ownerKey, 'main')`이면 `NO_VEHICLE_SAVE_FAIL`을 돌려줌. 주석: `// 저장할 서버 차량이 없으면 화면에만 남으므로 저장하지 않고 알린다.` 차주에게 이 조건은 `planExpenseVehicleTargets`가 빈 목록인 것과 같은 판정이고, 연동 기사에게는 배정 차량이 없는 것과 같은 판정임(셋 다 "메인, 없으면 아무 서버 차량" 규칙을 씀).
- `src/shared/utils/failureMessages.js` — `/** ④ 로그인 계정에 저장할 서버 차량이 없음 */ export const NO_VEHICLE_SAVE_FAIL = '…'`(목표 4번 문구)를 추가함.
- 새 테스트 `src/features/day-log/hooks/dayDraftLifecycle.noVehicle.test.js` — 전수점검 A2 테스트를 바탕으로, 서버 저장 0번·휴대폰 없음·마지막 상태 "failed"·알림 ④를 확인함.
- 새 테스트 `src/data/sync/expensesSave.noVehicle.test.js` — 차량 없는 로그인 차주에서 `saveExpenseDraft`가 ④를 돌려주고 지출 목록과 서버 호출이 그대로인지 확인함. 차량 있는 차주는 지금처럼 저장되는지도 확인함.
- 공용 값 영향: `failureMessages.js`는 상수 추가만 하고 기존 세 문구는 그대로 둠. `mainDayLogRouting.ts`는 함수 추가만 하고 `vehicleSupabaseIdForLog`·`shouldCommitDayLogToCloud`는 그대로 둠(쓰는 곳 11곳, grep 확인).

### 안 건드릴 것
- A3 "메인 없으면 아무 서버 차량" 규칙(`mainDayLogRouting.ts:14`·`expenseVehicleRouting.ts:30`·`hydrateMerge.ts:76`)은 132-B에서 다룸. 연동 기사 계정은 배정 차량(종류가 기사 차량)을 이 규칙으로 찾고 있어서(`hydrateEmployedDriverCar.ts:10`, `hydrateEmployedDriver.ts:2`), 고칠 때 차주와 기사를 나눠야 함.
- 첫 설정 [건너뛰기]로 차량 없이 가입되는 것은 이번 범위가 아님. 가입 뒤에도 차주가 메인 차량을 지우면 같은 상태가 되므로, 저장할 때 알리는 쪽이 먼저임. 막을지는 보리 결정.
- 미수금 화면의 입금 처리(`ownerFinance.ts:44`)는 그대로 둠. 차량이 없으면 저장된 일지가 없어서 미수 항목도 생기지 않음.
- 지출 삭제(`deleteExpense`)는 그대로 둠. 차량이 없으면 서버에 지울 줄이 없음.
- 백업 불러오기의 지출 저장(`memberRestoreRecords.js:145`)은 133번 범위.
- 일지가 "저장 실패"일 때의 화면 모양과 실패 날짜 알림은 기존 것을 그대로 씀.

### 검증
- 새 테스트 2개 파일은 고치기 전 코드에서 실패하는지 먼저 확인함(revert-check).
- 로컬 `npm test` 전체와 타입 검사를 돌림. 기존 테스트 중 "차량 없는 로그인 계정 = 저장됨"을 기대하는 것이 있으면 이번 목표와 반대이므로, 그 테스트 이름과 고친 내용을 진행 기록에 적고 보고함.
- 비회원·차량 있는 차주·연동 기사 저장은 기존 테스트(`dayDraftLifecycle.test.js`, `expensesSave.draft.test.js`, `expensesSave.order.test.js`, `dayLogCloudCommit.test.js`)가 그대로 통과하는지로 확인함.

### 파일 길이
- `mainDayLogRouting.ts` 28 → 약 34
- `dayDraftLifecycle.js` 188 → 약 191(가져오기 줄은 기존 줄에 이름만 더함)
- `expensesSave.ts` 88 → 약 92
- `failureMessages.js` 30 → 약 33
- 새 테스트 파일은 줄 제한에서 빠짐. 200줄을 넘는 파일 없음.

### 실패 시 처리
- 새 저장 장치, 재시도, 임시 보관을 만들지 않음. 실패는 알림과 "저장 실패" 표시로만 알림(기존 서버 실패 갈래를 다시 씀).
- 테스트나 타입 검사가 실패하면 바로 고치지 않음. 수정 계획을 다시 써서 승인받음.

### 사용자 확인 방법
- 차량 없는 상태는 로그인해 둔 계정에서 만들 수 없음. 메인 차량을 지워야 하는데 그러면 그 차량 기록이 지워짐. 그래서 목표 1~4는 자동 테스트로 확인함.
- 배포본에서는 지금 동작이 그대로인지만 봄(로그인 탭):
  1. 홈 → [운행 기록하기] → 고정 횟수를 하나 올림 → "저장됨" → 새로고침해도 남아 있음 → 원래대로 되돌림.
  2. 일지 창 지출 → 시험 지출 하나 저장 → 목록에 보임 → 새로고침해도 남아 있음 → 지움.

### 진행 기록
- 2026-10-28 보리 "확정, 진행해".
- 새 테스트 2개 파일(`dayDraftLifecycle.noVehicle.test.js` 1개, `expensesSave.noVehicle.test.js` 3개)은 고치기 전 코드에서 3개가 실패함: 지출이 `null`(성공)을 돌려줬고, 일지는 마지막 상태가 `saved`였음. 메인 차량이 있는 차주 1개는 통과. 고친 뒤 4개 모두 통과.
- 코드 4곳은 지시서대로 고쳤고 주석도 최종 문구 그대로임. 타입 검사 통과, `test:unit` 930개 전부 통과.
- **`test:app` 8개 실패. 지시서대로 멈추고 아래 수정 착수지시서를 씀**(코드 커밋 전).

### 수정 착수지시서 1 — `src/app/App.test.js` 화면 테스트 8개 `[x]`

**무엇이 실패했나 (모두 `App.test.js`)**: 이 파일은 로그인 계정(`user-boot-nav`)으로 앱 전체를 띄움. 그중 아래 테스트는 서버 차량 없이 일지와 지출을 저장하고 "저장됨"을 기대함. 이번 목표가 막으려는 바로 그 상태.
- 차량 없이 시작해서 실패한 테스트 4개:
  - `:283` 재감사 4번 store 구독(일지)
  - `:349` Step 6 일지 디바운스
  - `:623` 재감사 5차 FAIL 지적 1번(휴대폰 대기 저장 정리 실패)
  - `:785` 재감사 2차 FAIL 지적 2번(지출)
- 앞 테스트 때문에 따라서 실패한 4개: `:908` 로드맵 35, `:1036` 새 홈 1단계, `:1091` 알림 화면, `:1122` 다크 로그아웃. 앞 일지 테스트들이 실패하며 남긴 "나가기 전 확인" 표시가 뒤 테스트의 화면 이동을 막음.

**확인(임시 사본으로 실험한 뒤 지움, 저장소에 남긴 것 없음)**: 4개 테스트에 같은 파일의 `seedMainCar(ownerKey)`(메인 차량 501을 넣는 기존 도우미, 이미 6곳에서 씀)를 넣으면 `:283`·`:349`·`:785`는 통과함. `:623`만 남는데, 이 테스트는 로그인 화면에서 "휴대폰 대기 저장(durable)에 남은 옛 값 A가 입력 칸 위에 보인다"를 전제로 함. 로그인 계정은 이번 수정 뒤 대기 저장 갈래로 갈 수 없음: 차량이 있으면 서버, 없으면 실패. 그래서 이 전제가 성립하지 않음. 이 테스트가 남긴 대기 저장 1건은 뒤 테스트 3개(`:845`·`:908`·`:975`의 "대기 저장 0건")까지 깨뜨림.

**고칠 것**
1. `:283`·`:349`·`:785` — 첫 화면 그리기 직전에 `seedMainCar(ownerKey)` 한 줄을 넣음(같은 파일 `:426`·`:501`과 같은 방식). 확인 내용(assert)은 그대로 둠.
2. `:623` — 이 화면 테스트를 지움. 같은 내용("정리 쓰기만 실패해도 최신 값 유지, 복구 뒤 다시 해도 옛 값으로 안 돌아감")은 비회원·로그인과 상관없는 대기 저장 단위 테스트 `src/data/sync/pendingWorkDataWrites.test.js:314`가 계속 확인함. 지우는 것은 테스트를 줄이는 일이라 따로 승인받는 것임.
   - 다른 방법: 비회원으로 앱을 띄우는 새 화면 테스트 파일로 옮기기. 이 파일은 로그인 세션이 고정돼 있어서 비회원 부트 준비를 새로 만들어야 하므로, 이번 범위보다 큼.
3. `:908`·`:1036`·`:1091`·`:1122`는 고치지 않음. 위 1~2번 뒤에 통과하는지만 확인함.

**건드릴 파일**: `src/app/App.test.js`만(테스트 파일이라 줄 제한에서 빠짐. 1157줄에서 약 1095줄로 줄어듦). 앱 코드는 위 지시서 범위 그대로임.

**검증**: `npm test` 전체(`test:unit` + `test:app`)와 타입 검사. 그 뒤 코드 커밋을 1번 함.

**실패 시 처리**: 또 실패하면 고치지 않고 이 기록에 덧붙여 다시 승인받음.

**결과 (`[x]` 2026-10-28 보리 "확정, 진행해" 뒤 진행)**
- 1~2번대로 고침. `:623`을 지우면서 그 테스트만 쓰던 불러오기 2개(`registerPendingDayWrite`, `retryPendingDayWrites`)도 뺌.
- 타입 검사 통과. `npm test` 전체 통과: `test:unit` 930개, `test:app` 918개, 실패 0. 따라서 실패하던 4개(`:908`·`:1036`·`:1091`·`:1122`)도 고치지 않고 통과함.
- 코드 커밋 react-app `c25535d`(push 안 함). 바뀐 파일 7개 = 지시서 앱 코드 4개 + 새 테스트 2개 + `App.test.js`.
- 리뷰 점검: 범위 밖 파일 없음. 새 저장 장치·재시도 없음. 금지 타입 표시 없음. 새 주석 3개는 지시서 최종 문구 그대로이고 로드맵 번호·날짜 없음. 앱 코드 줄 수 `dayDraftLifecycle.js` 190, `expensesSave.ts` 91, `mainDayLogRouting.ts` 33, `failureMessages.js` 33으로 200줄 이하. 지운 테스트는 위 2번대로 승인받은 1개뿐이고, 남은 테스트의 확인 내용은 그대로임.
- push 뒤 CI verify·deploy 초록(run 40689382165). 배포 번들에 알림 ④ 문구가 들어간 것을 확인함.
- 배포본 확인(로그인 탭 `seed`, 10월 28일 일지):
  1. 고정 횟수 2회 → "저장 완료" → 새로고침 뒤에도 2 → 0으로 되돌림, 새로고침 뒤 빈 칸.
  2. 주유 1,234원 저장 → "내역을 등록했습니다"·목록에 보임 → 새로고침 뒤에도 남음 → 삭제 → 새로고침 뒤 없음.
- 2026-10-28 보리 최종 승인.

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
