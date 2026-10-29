# docs/report.md — 현재 슬라이스 착수지시서

132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 132-B 메인 차량 없는 차주 — 메인 일지·지출이 기사 차량 칸으로 감 `[x]`

전수점검(2026-10-26) A3, 로드맵 132 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 132-A(`c25535d`, 차량 없는 로그인 계정은 저장 실패 알림 ④)의 다음 조각. 이번엔 B만 함. DB 변경 없음.

### 쉬운 요약
- 메인 차량을 지우고 기사 차량만 남은 차주가 메인 일지나 지출을 쓰면, 그 기록이 기사 차량 칸에 저장됩니다. 그 기사의 일지·정산에 차주 기록이 섞입니다.
- 고치고 나면 차주는 메인 차량에만 저장합니다. 메인 차량이 없으면 132-A에서 만든 안내("메인 차량이 없어 저장하지 못했습니다. 차량 관리에서 메인 차량을 등록해 주세요.")가 뜹니다.
- 연동 기사 본인 앱은 지금처럼 배정받은 차량에 저장합니다.

### 현재 상태 (코드 확인)
- 메인을 찾는 규칙이 세 곳에 따로 있음. 모두 "종류가 메인인 서버 차량, 없으면 아무 서버 차량" 규칙임:
  - 일지·점검표: `data/sync/mainDayLogRouting.ts:14` `vehicleSupabaseIdForLog`
  - 지출 저장: `domain/expenseVehicleRouting.ts:30` `planExpenseVehicleTargets`
  - 로그인 때 불러오기: `data/hydrate/hydrateMerge.ts:76` `findMainCar`
- "아무 서버 차량"으로 넘어가는 규칙이 필요한 건 연동 기사 앱뿐임. 기사의 배정 차량은 종류가 기사 차량으로 들어오고(`hydrateEmployedDriverCar.ts:10`), 기사 일지는 메인 칸에 씀(`hydrateEmployedDriver.ts:2`). 기사 세션은 로그인 id와 차주 id(ownerKey)가 다름(`appSession.ts:77`). 지출 저장은 이미 이 차이로 기사를 가름(`expensesSave.ts:32`).
- 세 곳 중 기사 앱도 거치는 곳은 `vehicleSupabaseIdForLog`뿐임:
  - `planExpenseVehicleTargets`는 차주 갈래에서만 부름. 기사 갈래는 `expensesSave.ts:32-40`에서 먼저 끝남. 검색 결과 다른 호출부 없음.
  - `findMainCar` 결과는 차주 불러오기에서만 씀(`hydrate.ts:130`, 메인 지출 불러오기). 기사 불러오기는 받지 않음(`hydrateEmployedDriver.ts:126`).
- 기사 차량만 남는 경로: 차량 관리에서 메인 차량을 지울 때 막는 장치가 없음(`data/sync/vehicleDeletion.ts`, 종류 검사 없음). 차량 종류는 등록할 때 정해지고 고칠 수 없음(`CarListPage.jsx:49`, `CarFormModal.jsx:185`). 첫 설정을 건너뛰고 나중에 등록한 첫 차량은 메인이 됨.
- 시험 서버(읽기 조회 2026-10-29): 메인 없이 기사 차량만 있는 차주 1명. 기사 차량 1대에 연동 기사 1명이고 일지는 0개라서, 이미 섞인 기록은 없음.
- 재현 테스트: `docs/full-code-review-failing-tests.md` A3(`mainDayLogRouting.subOnly.test.js`). 차주 기사 차량 801로 `save_day_log`가 나감.

### 목표 상태 (기대 동작)
1. 차주 세션에서 메인 일지를 저장할 서버 차량은 종류가 메인인 차량뿐임. 메인이 없으면 기사 차량으로 넘어가지 않고, 132-A 갈래대로 "저장 실패" 표시와 알림 ④가 뜸.
2. 차주 세션에서 지출 [저장]도 메인 차량이 없으면 아무것도 바꾸지 않고 알림 ④가 뜸. 기사 차량 표시가 붙은 지출은 메인이 있을 때만 저장되고, 지금처럼 그 기사 차량 칸으로 감.
3. 차주가 로그인하면 메인 지출은 메인 차량에서만 불러옴. 기사 차량 지출은 지금처럼 차량번호 표시를 붙여 따로 불러옴(`hydrateOwnerDriverExpenses.ts`).
4. 메인이 없는 차주에게 메인 일지 점검표 카드, 홈 "일상점검표 미작성", 서류 점검표 탭, 운비서 점검표 기록은 기사 차량을 쓰지 않음. 서버 차량이 없을 때와 같이 숨거나, 운비서가 "일상점검표를 저장할 차량이 없어요."라고 답함(`DailyInspectionNotice.jsx:36`, `useTodayInspectionMissing.js:13`, `DocumentIssuePage.jsx:45`, `unbiseoInspection.ts:33`).
5. 연동 기사 세션은 지금과 똑같음. 메인 일지·점검표는 배정 차량에 저장되고, 지출은 기사 갈래를 그대로 탐.
6. 메인 차량이 있는 차주와 비회원도 지금과 똑같음.

### 건드릴 파일
- `src/data/sync/mainDayLogRouting.ts` — `vehicleSupabaseIdForLog`의 메인 찾기에서, "아무 서버 차량"으로 넘어가는 것을 기사 세션(로그인 id가 있고 ownerKey와 다를 때)으로 좁힘. 주석: `// 연동 기사의 배정 차량은 종류가 기사 차량이라 메인이 없으면 그 차량을 쓴다. 차주는 메인 차량만.`
- `src/domain/expenseVehicleRouting.ts` — `planExpenseVehicleTargets`의 `mainCar`를 종류가 메인인 서버 차량만으로 바꿈.
- `src/data/hydrate/hydrateMerge.ts` — `findMainCar`를 종류가 메인인 서버 차량만으로 바꿈.
- 새 테스트 `src/data/sync/mainDayLogRouting.subOnly.test.js` — 차주(기사 차량 801만 있음): 메인 일지가 801로 나가지 않고 `vehicleSupabaseIdForLog`는 없음. 기사 세션(로그인 id ≠ 차주 id, 배정 기사 차량 801): 801을 돌려줌.
- 기존 테스트 2개는 이번 목표와 반대인 동작을 확인하고 있어서 바꿈(약하게 만드는 게 아니라 기대값을 뒤집음):
  - `src/data/hydrate/hydrateMerge.test.js:333` "main이 없으면 supabaseId가 있는 아무 차량이나 돌려준다" → "main이 없으면 기사 차량이 있어도 null"
  - `src/domain/expenseVehicleRouting.test.js:86` "기사 본인(차량이 배정 서브 1대뿐): 표시 없는 항목은 그 차량 칸 하나로 간다" → "메인 없이 기사 차량만 있는 차주: 대상 없음". 기사 본인 지출은 이 함수를 거치지 않음(위 현재 상태).
- 같은 규칙을 쓰는 곳(영향 목록, 코드는 안 바꿈): `vehicleSupabaseIdForLog` 호출부는 `dayLogCloudCommit.ts:104,132`, `mainDayLogRouting.ts:27,32`(→ `dayDraftLifecycle.js`, `expensesSave.ts:72`), `DailyInspectionNotice.jsx:35`, `DailyInspectionMonthSheet.jsx:32`, `DocumentIssuePage.jsx:31`, `unbiseoInspection.ts:32`, `useTodayInspectionMissing.js:12`. 모두 "없음"을 이미 처리함. `findMainCar` 호출부는 `hydrateVehicleDayLogs.ts:45` 하나.

### 안 건드릴 것
- 메인 차량 지우기를 막거나 확인 문구를 바꾸는 일은 하지 않음. 지금 정한 규칙이 없어서 따로 정할 일임(sot에 메인 차량 삭제 규칙 없음).
- 첫 설정 [건너뛰기]를 차량 단계에서 막을지는 132-A에서 남긴 질문 그대로 둠.
- 차량 표시가 없는 기사 지출, 연동 기사 앱의 저장 경로(`expensesSave.ts:32-40`, `hydrateEmployedDriver.ts`)는 바꾸지 않음.
- `DailyInspectionMonthSheet.jsx:33`의 화면 표시용 차량 고르기(`cars[0]` 대체)는 저장과 상관없어서 그대로 둠.
- 서버 데이터는 고치지 않음. 시험 서버에 섞인 기록이 없음(위 조회).

### 검증
- 새 테스트를 고치기 전 코드에서 먼저 돌려 차주 쪽이 실패하는지 확인함(기사 쪽은 통과해야 함). 바꾼 기존 테스트 2개도 고치기 전 코드에서 실패하는지 확인함.
- 로컬 `npm test` 전체와 타입 검사를 돌림. 다른 테스트가 실패하면 고치지 않고 수정 착수지시서를 씀.
- 배포본: 로그인 탭(메인 차량 있음)에서 일지 저장, 지출 저장·삭제, 일지 점검표 카드가 지금처럼 되는지 확인함.

### 파일 길이
- `mainDayLogRouting.ts` 33 → 약 37줄
- `expenseVehicleRouting.ts` 63 → 63줄
- `hydrateMerge.ts` 99 → 99줄
- 새 테스트 약 60줄. 바꾼 테스트 2개 파일은 줄 수가 거의 같음(테스트는 줄 제한 밖).

### 실패 시 처리
- 새 저장 장치, 재시도, 임시 보관을 만들지 않음. 메인이 없을 때는 132-A의 알림 ④ 갈래를 그대로 씀.
- 테스트나 타입 검사가 실패하면 바로 고치지 않음. 수정 계획을 다시 써서 승인받음.

### 사용자 확인 방법
- 메인 차량 없는 상태는 로그인해 둔 계정에서 만들 수 없음. 메인 차량을 지워야 하는데 그러면 그 차량 기록이 지워짐. 그래서 목표 1~5는 자동 테스트로 확인함.
- 배포본에서는 지금 동작이 그대로인지만 봄(로그인 탭):
  1. 홈 → [운행 기록하기] → 고정 횟수를 하나 올림 → "저장됨" → 새로고침해도 남아 있음 → 원래대로 되돌림.
  2. 일지 창 지출 → 시험 지출 하나 저장 → 새로고침해도 남아 있음 → 지움.
  3. 일지 창에 "일상점검표를 작성해 주세요." 카드가 지금처럼 보임.

### 진행 기록
- 2026-10-29 보리 "응 진행해".
- 고치기 전 코드에서 새 테스트(`mainDayLogRouting.subOnly.test.js`)와 바꾼 기존 테스트 2개를 돌림. 차주 쪽 3개는 실패함: 801로 저장됨, `findMainCar`가 7을 돌려줌, 지출 대상이 801. 연동 기사 쪽 1개는 통과함.
- 코드 3곳을 지시서대로 고쳤고 주석도 최종 문구 그대로임. 고친 뒤 위 4개 모두 통과. 타입 검사 통과, `test:unit` 932개 전부 통과.
- **`test:app` 1개 실패. 지시서대로 멈추고 아래 수정 착수지시서를 씀**(코드 커밋 전).

### 수정 착수지시서 1 — `DocumentIssuePage.test.js` 연동 기사 앱 테스트 1개 `[x]`

**무엇이 실패했나**: `src/features/documents/ui/DocumentIssuePage.test.js:135` "연동 기사 앱처럼 메인 종류 차량이 없으면 첫 차량(배정 차량) 번호를 머리 칸에". 기사 차량 1대만 넣고 서류 화면의 점검표 머리 칸에 그 번호가 보이는지 봄. 그런데 이 테스트는 로그인 상태를 만들지 않음(`beginSessionEpoch` 없음). 이번 수정으로 "메인이 없으면 배정 차량"은 연동 기사 세션(로그인 id ≠ 차주 id)에만 남아서, 로그인 없는 이 테스트에서는 차량이 없음으로 나오고 점검표 탭이 숨음(`DocumentIssuePage.jsx:45`). 실제 연동 기사 앱은 늘 로그인 상태라 동작은 맞고, 테스트 준비가 실제와 다른 것임.

**확인(임시 사본으로 실험한 뒤 지움, 저장소에 남긴 것 없음)**: 이 테스트에만 연동 기사 로그인(`beginSessionEpoch('doc-issue-driver', OWNER)`)을 넣고 끝에 `endCloudSession()`을 부르니 파일 11개 테스트가 모두 통과함.

**고칠 것**
1. `DocumentIssuePage.test.js` — `cloudSession.ts`의 `beginSessionEpoch`, `endCloudSession`을 불러옴. `:135` 테스트에서 차량을 넣은 뒤 `beginSessionEpoch('doc-issue-driver', OWNER)`, `finally`에서 `endCloudSession()`. 확인 내용(assert)은 그대로 둠.
2. 앱 코드는 위 지시서 범위 그대로이고 더 바꾸지 않음.

**건드릴 파일**: `src/features/documents/ui/DocumentIssuePage.test.js`만(테스트 파일이라 줄 제한에서 빠짐, 3줄 정도 늘어남).

**검증**: `npm test` 전체와 타입 검사. 그 뒤 코드 커밋을 1번 함.

**실패 시 처리**: 또 실패하면 고치지 않고 이 기록에 덧붙여 다시 승인받음.

**결과 (2026-10-29 보리 "확정,진행해" 뒤 진행)**
- 1번대로 고침(불러오기 1줄, 로그인 시작 1줄, 로그인 끝 1줄).
- 타입 검사 통과. `npm test` 전체 통과: `test:unit` 932개, `test:app` 918개, 실패 0.
- 코드 커밋 react-app `0d405d4`(push 안 함). 바뀐 파일 7개 = 앱 코드 3개 + 새 테스트 1개 + 기대값 바꾼 테스트 2개 + `DocumentIssuePage.test.js`.
- 리뷰 점검: 범위 밖 파일 없음. 새 저장 장치·재시도 없음. 금지 타입 표시 없음. 새 주석 1개는 지시서 최종 문구 그대로이고 로드맵 번호·날짜 없음. 앱 코드 줄 수 `mainDayLogRouting.ts` 36, `expenseVehicleRouting.ts` 63, `hydrateMerge.ts` 99로 200줄 이하. 지운 테스트 없음. 기대값을 바꾼 테스트는 지시서에서 승인받은 2개뿐임.
- push는 2026-10-29 보리 "예외로 푸시까지" 지시로 AI가 함(`c25535d..0d405d4`). CI verify·deploy 초록(run 40825196622).
- 배포본 확인(로그인 탭 `seed`, 10월 29일 일지):
  1. 고정 횟수 2회 → "저장 완료" → 새로고침 뒤에도 2 → 0으로 되돌림, 새로고침 뒤 빈 칸.
  2. 주유 1,234원 저장 → "내역을 등록했습니다"·목록에 보임 → 새로고침 뒤에도 남음 → 삭제 → 새로고침 뒤 없음.
  3. 일지 창 "일상점검표를 작성해 주세요." 카드, 홈 "오늘 일상점검표 미작성" 모두 지금처럼 보임.
- 2026-10-29 보리 최종 승인.

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
