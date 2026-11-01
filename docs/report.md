# docs/report.md — 현재 슬라이스 착수지시서

119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 120-A. 등록 안 한 호출은 실패로(엄격 모드) — 기본값 바꾸기 `[x]`

- 근거: 로드맵 120(③, 테스트 점검 2026-10-26). 119(`119ed38`)의 다음이다.
- 2026-11-01 보리가 "응"으로 지시서를 요청했다.
- 120은 세 조각으로 나눈다. **이번엔 A만** 한다.
  - A: 기본값을 엄격으로 바꾸고, 지금 깨지는 파일에만 "예전처럼" 표시를 단다.
  - B: `data/` 파일 10개에서 그 표시를 떼고, 필요한 응답을 등록한다.
  - C: `app/`·`features/` 파일 3개에서 같은 일을 한다.

### 쉬운 요약
- 지금 가짜 서버는 테스트가 미리 정해 두지 않은 호출이 와도 "성공"으로 답한다. 그래서 앱이 엉뚱한 표를 지우거나 고쳐도 테스트가 모를 수 있다.
- 이번에는 미리 정해 두지 않은 호출이 오면 "등록 안 한 호출: 표.동작" 오류로 테스트를 멈추게 바꾼다.
- 다만 지금 그런 호출에 기대는 파일 13개는 "예전처럼" 표시를 달아 그대로 둔다. 나머지 36개 파일과 앞으로 새로 쓰는 테스트는 바로 엄격해진다.

### 현재 상태 (코드 확인)
- 등록 안 하면 성공으로 답한다: `fakeSupabaseClient.js:110-117` 표 호출 5가지, `:128-129` 서버 함수.
  - 읽기는 빈 목록, 나머지는 빈 성공을 돌려준다.
- 엄격 실험(임시 사본에서만, 원래 폴더는 안 바꿈)
  - 방법: 기본 성공 6곳을 오류로 바꾸고, 가짜 서버를 쓰는 테스트 파일 50개를 하나씩 돌렸다.
  - 결과: 36개 파일은 모두 통과했다. 14개 파일에서 테스트 55개가 실패했다.
- 실패한 14개 파일과 원인 호출

| 묶음 | 파일 (실패/전체) | 등록 안 한 호출 |
|---|---|---|
| 가짜 서버 자체 | `data/cloud/fakeSupabaseClient.test.js` 3/5 | 기록 시험용 호출 |
| B | `data/cloud/fetchAllRows.saves.test.js` 2/3 | `maintenance_records.select` |
| B | `data/hydrate/hydrate.test.js` 6/21 | 일지·지출 3종·콜 상세 읽기, `rpc.settle_expired_driver_unlinks` |
| B | `data/hydrate/employedDriverClients.test.js` 2/6 | `rpc.get_linked_owner_business_info` |
| B | `data/hydrate/hydrateRetry.test.js` 1/2 | `rpc.get_assigned_vehicle_summary` 등 |
| B | `data/sync/cloudMemorySave.test.js` 11/16 | 지출 읽기, `profiles.upsert`, `tax_invoices.select` |
| B | `data/sync/directMutationActions.test.js` 4/43 | `vehicles.delete`, `driver_links`·`tax_invoices`·`transport_details.update` |
| B | `data/sync/expensesSave.noVehicle.test.js` 1/3, `expensesSave.order.test.js` 1/2 | 일지·정비 읽기 |
| B | `data/sync/invoicesSave.renamedCar.test.js` 2/2 | `rpc.rename_and_save_sub_vehicle` |
| B | `data/sync/vehicleRename.test.js` 2/6 | `vehicles.update`, `fuel_records.select` |
| C | `app/App.clientsCars.test.js` 8/15 | 시작 때 `rpc.is_admin`·`settle_expired_driver_unlinks`, 문의·지출 읽기 등 |
| C | `app/App.test.js` 9/16 | 위와 같음, `profiles.upsert` |
| C | `features/expenses/ui/expenseSaveOnce.test.js` 3/3 | `fuel_records.select` |

- B는 10개 파일이다(`expensesSave` 2개 포함). 가짜 서버 자체 시험은 A에서 고친다.
- `createFakeSupabase`에 인자를 넘기는 곳은 지금 없다(`grep "createFakeSupabase("`, 모두 빈 괄호).

### 목표 상태 (기대 동작)
1. `createFakeSupabase()`가 기본으로 엄격하다. 등록 안 한 표·동작·서버 함수를 부르면 그 호출을 기다리는 곳에서 `등록 안 한 호출: 표.동작` 오류가 난다. 서버 함수는 `rpc.함수이름`으로 나온다.
2. `createFakeSupabase({ allowUnregistered: true })`는 지금과 똑같이 동작한다. 읽기는 빈 목록, 나머지는 빈 성공이다.
3. 위 표의 B·C 파일 13개는 `createFakeSupabase({ allowUnregistered: true })`로 한 줄만 바뀐다. 그 밖의 줄은 바꾸지 않는다.
4. 나머지 36개 파일은 하나도 바꾸지 않고 엄격한 기본값으로 통과한다.
5. 등록하면 지금처럼 동작한다. 등록하는 방법은 `handlers`와 `emptyOkHandlers`다. 호출 기록(`calls`·`callsOf`·`countOf`)은 오류가 나도 남는다.
6. 가짜 서버 자체 시험 처리
   - 기록을 보는 시험 2개는 쓰는 표에 응답을 등록한다.
   - "`resetHandlers` 뒤" 시험은 비운 뒤 오류가 나는지 보도록 바꾼다.
   - 시험 2개를 새로 넣는다. ① 엄격 기본값의 오류 문구(표 호출과 서버 함수) ② `allowUnregistered`면 예전처럼 성공.

### 건드릴 파일
- `react-app/src/testSupport/fakeSupabaseClient.js`
  - `createFakeSupabase({ allowUnregistered = false } = {})`로 바꾼다.
  - 기본 성공 6곳이 `unregistered(표, 동작, 예전 답)`을 거치게 한다. 이 함수는 허용이면 예전 답을 주고, 아니면 오류를 던진다.
  - 넣을 주석 최종 문구:
    - `// 등록 안 한 호출은 기다리는 곳에서 오류(allowUnregistered면 예전처럼 빈 성공).`
    - 서버 함수 설명 3줄(`:121-123`, "없으면 no-op"이 이제 틀림)은 `// handlers.rpc[함수이름]이 있으면 그 답, 없으면 위와 같이 처리.` 한 줄로 바꾼다.
- `react-app/src/data/cloud/fakeSupabaseClient.test.js` — 목표 6.
- 위 표의 B·C 파일 13개 — `createFakeSupabase()` 한 곳만 `createFakeSupabase({ allowUnregistered: true })`로 바꾼다.
- 공용 영향: 가짜 서버를 쓰는 테스트 파일 50개 전부가 영향 범위다. 위 실험으로 파일마다 결과를 확인했다.

### 안 건드릴 것
- 앱 코드(운영 파일)는 바꾸지 않는다.
- `stubSupabaseClient.js`(서버를 시험하지 않는 테스트용)는 바꾸지 않는다.
- B·C 파일의 응답 등록은 120-B·120-C에서 한다. 이번에는 표시 한 줄만 단다.
- `chainable`·기록(`calls`)·`filters`·`countOf`는 그대로 둔다(119 그대로).

### 검증
- 자동 검사: `npm test`, `npm run typecheck`, `npm run lint`, `npm run depcheck`, `npm run build`.
- 고치기 전 실패 확인: `fakeSupabaseClient.js`만 되돌리면 새 시험 2개가 실패해야 한다. 엄격 오류와 `allowUnregistered` 인자가 없기 때문이다.
- 엄격이 실제로 켜졌는지 확인: 엄격 파일 하나(예: `dailyInspections.test.js`)에서 등록 한 줄을 잠깐 지우고 돌린다. "등록 안 한 호출"로 실패하는지 보고, 바로 되돌린 뒤 `git diff --quiet`로 그대로인지 확인한다.
- 화면 변화 없음. CI 초록만 본다.

### 파일 길이
- `fakeSupabaseClient.js`: 192 → 약 197(200 이하). 서버 함수 설명 3줄을 1줄로 줄여 자리를 만든다.
- 테스트 파일 14개는 제한 밖이다. 13개는 한 줄 바꿈이라 줄 수도 그대로다.

### 실패 시 처리
- 표의 14개 말고 다른 파일이 깨지면 멈추고, 원인을 적은 수정 착수지시서로 다시 승인받는다.
- 새 장치(호출 목록을 따로 모아 검사하는 단계 등)는 만들지 않는다.

### 사용자 확인 방법
- 화면이 바뀌는 일이 아니다. CI 초록과 "등록 한 줄을 지우면 테스트가 잡아냄" 실험 결과로 확인한다.

### 확인할 점
1. 로드맵 120에는 "파일마다 엄격을 켜 가며 옮기고, 다 옮긴 뒤 기본값을 엄격으로"라고 적혀 있다. 이 지시서는 순서를 거꾸로 했다. 먼저 기본값을 엄격으로 바꾸고, 깨지는 13개 파일에만 "예전처럼" 표시를 단다.
   - 이유: 36개 파일이 이미 엄격으로 통과한다. 거꾸로 하면 36개 파일을 손대지 않고 바로 보호할 수 있다. 원래 순서대로 하면 36개 파일마다 한 줄씩 고쳐야 한다.
   - 이대로 가도 될까?
2. A에서 바뀌는 파일이 15개다. 그중 13개는 한 줄씩만 바뀐다. 묶음 크기가 괜찮을까?

### 진행 기록
- 2026-11-01 보리 "착수지시서 확정"(확인할 점 1·2 이대로).
- 보리 지시("너가해")로 데스크톱 AI가 앱 코드를 고쳤다. 코드 커밋 react-app `3e59665`(파일 15개: 가짜 서버 1, 가짜 서버 자체 시험 1, 한 줄 표시 13). 앱 코드 변경 없음.
- 전체 검사 통과: 테스트 964·942개(새 시험 2개 포함) 모두 통과, 타입·lint·의존 검사·빌드 통과. `fakeSupabaseClient.js` 196줄.
- 고치기 전 실패 확인: `fakeSupabaseClient.js`만 되돌리면 "엄격 기본값" 시험과 "resetHandlers 뒤 오류" 시험 2개가 실패한다.
  - 지시서와 다른 점: 지시서는 "새 시험 2개가 실패"라고 적었다. 그런데 새 시험 중 `allowUnregistered` 시험은 예전 동작이 원래 그대로라 이전 코드에서도 통과한다(안전 확인용).
- 엄격 동작 실험: `dailyInspections.test.js` 지우기 시험의 등록 줄(81줄)을 잠깐 지우니 "Error: 등록 안 한 호출: daily_inspections.delete"로 실패했다. 되돌린 뒤 `git diff --quiet`로 그대로인지 확인했다.
- 실험에서 본 한계 `[확인: 2026-11-01 보리 — "응고쳐", B·C에서 고침, 로드맵 120에 적음]`: 아무 오류나 기다리는 시험(`assert.rejects`에 문구 없음)은 등록이 빠져도 이 오류로 통과할 수 있다. 예: `dailyInspections.test.js` "읽기 실패는 던짐".
- 푸시: 보리 예외 지시("예외로 푸시까지")로 데스크톱 AI가 react-app `3e59665` push.
- 푸시 뒤 CI verify·deploy 초록(run 41284044892).
- §5 리뷰(데스크톱 AI, 3e59665)
  1. 범위: 바뀐 파일 15개가 "건드릴 파일"과 같다. 13개는 `createFakeSupabase()` 한 줄씩만 바뀌었다. 앱 코드·`stubSupabaseClient.js`는 안 바뀌었다.
  2. 몰래 증설 없음: 새 파일 없음, 새 저장 키·저장 단계 없음. 호출 목록을 따로 모아 검사하는 장치도 안 만들었다.
  3. 타입 꼼수 없음: 더한 줄에 `any`·`unknown`·`@ts-ignore`·`@ts-expect-error`·로드맵 번호 없음(grep).
  4. 200줄: `fakeSupabaseClient.js` 196줄.
  5. 테스트 진실성: 가짜 서버 자체 시험에서 지운 줄은 머리 주석·꺼내 쓰는 이름 2줄·시험 이름 1줄뿐이다. 빠진 확인은 없고, "비운 뒤 빈 목록" 확인은 "비운 뒤 오류" 확인으로 바뀌었다.
  6. 공용 영향: 가짜 서버를 쓰는 파일 50개 중 엄격 기본값 37개(자체 시험 포함), `allowUnregistered` 13개. 지시서 실험 표와 같고, CI 전체 통과로 확인했다.
  7. 요구사항: 목표 1~6 모두 됐다.
- 보리 최종 승인(2026-11-01).
