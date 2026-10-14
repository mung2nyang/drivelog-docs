# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 66 기사 차량 번호를 고치면 그 번호에 묶인 것도 새 번호로 옮김 `[x]`

> 진행(2026-10-14): 코드 커밋 react-app `1db33e6`(DB 0022 포함). 테스트 1,283개 통과, 타입 검사·빌드 통과, 엄격 검사 경고 457개(늘지 않음). 새 테스트 6개 중 R-4(번호 안 바꾸면 서버 함수 안 부름)만 빼고 고치기 전 코드로 되돌리면 실패함을 확인. DB 0022: SQL Editor에서 실행·사후검증(관리자 권한 아님, 로그인 사용자만 실행, 없는 차량·메인 차량은 거절). 개발 서버 브라우저: 첫 시도는 SQL 계산 순서 오류로 실패했으나 서버·화면 모두 옛 번호 그대로였음(66-1로 고침). 고친 뒤 시험 차량 99가9999 → 99가9998로 바꾸니 서버 차량 번호·전용 거래처·설정 키, 화면 일지·지출·거래처·설정이 모두 새 번호. 새로고침 뒤에도 유지, 주유를 추가해도 그 차량 칸으로 감(메인 칸 0건). 보리 push, CI 초록(verify·deploy), 최종 승인(2026-10-14).

보리 지시(2026-10-14, 65 최종 승인 뒤 "66번 착수지시서"). 근거: 코드 리뷰(2026-10-14) → `docs/roadmap.md` 66번. 방식은 보리 선택(2026-10-14): **"전부 새 번호로 옮김"**(번호 수정 막기·연동 차량만 막기 대신), 서버 쪽은 **DB 함수 하나로 한꺼번에**(보리 승인 2026-10-14 "DB변경 필요하면 진행해"). 저장 경로라 `docs/testing-playbook.md` 확인.

### 쉬운 요약
- 기사 차량 번호를 고치면 지금은 일지만 새 번호로 옮겨지고, 그 차량 지출 표시·전용 거래처(고정노선 포함)·기사 연결·그 차량 일지 설정(일상점검표 켜기 포함)은 옛 번호에 남는다. 그 상태로 지출을 저장하면 지출이 메인 차량으로 옮겨지고, 거래처·설정은 서버에도 옛 번호라 앱을 다시 켜도 안 고쳐진다.
- 번호를 고칠 때 이 네 가지를 같이 새 번호로 바꿔 저장한다.
- 끝나면 번호 오타를 고쳐도 그 차량 기록·거래처·설정이 그대로 따라온다.

### 현재 상태 (코드 확인)
- `lib/vehicleMutations.js:104-111` 번호 변경 감지 후 `planSubLogRename`(`:38-67`)으로 일지(`workLogs`)·대기 저장 키만 옮김.
- 옛 번호에 남는 것(전부 `grep` 확인):
  ① 지출 `vehicleNumber`(`domain/expenses.js:93-113`) — 저장 때 `planExpenseVehicleTargets`(`domain/expenseVehicleRouting.js:56-64`)가 일치하는 차량이 없으면 메인 칸으로 보냄.
  ② 거래처 `scopedToVehicleNumber`(`domain/clients.js:21` 고정노선 찾기, 서버 `clients.raw`에 저장 — `lib/cloudStorage.js:112`, 연동 기사 불러오기가 이 값으로 거래처를 고름 `lib/hydrateEmployedDriver.js:140`).
  ③ 기사 `vehicleNumber`(Store, 서버는 `vehicle_id`로 연결이라 다시 켜면 맞춰짐 `lib/hydrateMerge.js:72-78`).
  ④ 설정 `subCarSettings[번호]`(`domain/carSettingsScope.js:16`, 서버 `profiles.settings` — `lib/profileCloudCommit.js:32`).
- `lib/vehicleMutations.js`는 191줄이라 이 일을 더 넣으면 200줄을 넘음.

### 목표 상태 (기대 동작)
1. 기사 차량 번호를 A → B로 바꿔 저장하면 ① 지출 ② 그 차량 전용 거래처 ③ 기사 연결 ④ 그 차량 일지 설정이 모두 B로 바뀐다(일지는 지금처럼).
2. 로그인 차주: 새 DB 함수 `rename_sub_vehicle` 한 번으로 서버의 **차량 번호·그 번호 전용 거래처·차량 설정 키를 한꺼번에** 바꾼다(셋 다 되거나 하나도 안 됨). 그다음 지금처럼 차량 나머지 칸(톤수·수수료 등)을 저장하고, 둘 다 성공한 뒤에만 화면(Store)을 한 번에 바꾼다. ①③은 서버가 `vehicle_id`로 연결하므로 Store만 바꾼다.
3. 실패하면 화면은 그대로(옛 번호)이고 "저장에 실패했습니다…" 안내. 함수가 성공하고 나머지 칸 저장만 실패했으면 서버는 이미 새 번호로 일관되게 바뀐 상태라, 다시 저장하거나 앱을 다시 켜도 거래처·설정이 옛 번호에 남지 않는다(나머지 칸만 다시 저장하면 됨).
4. 비회원: 기기 저장 한 번(지금 일지 옮기기와 같은 묶음)에 ①~④가 함께 저장되거나 하나도 안 된다.
5. 번호를 안 바꾼 저장, 메인 차량 저장은 동작·서버 호출 횟수가 지금과 같다.
6. 지출 저장 뒤에도 바뀐 번호 차량 지출이 그 차량 칸에 남는다(메인으로 안 옮겨짐).

### 건드릴 파일
- 새 파일 `react-app/src/lib/vehicleRename.js` — `planSubLogRename`을 `vehicleMutations.js`에서 옮겨 오고(내용 그대로), 번호에 묶인 ①~④를 새 번호로 바꾼 값을 계산하는 함수와, 로그인일 때 DB 함수 `rename_sub_vehicle`을 부르는 함수를 둔다(새 저장 장치 없음). 넣을 주석(최종 문구): `// 기사 차량 번호를 바꾸면 그 번호에 묶인 일지·지출·전용 거래처·기사 연결·차량 설정을 새 번호로 함께 옮긴다.`
- DB(AGENTS §9 절차): 함수 `public.rename_sub_vehicle(p_vehicle_id, p_new_number)` 새로 만듦 — 로그인한 차주 본인(`auth.uid()`) 기사 차량만, 옛 번호를 읽어 `vehicles.number`·`legacy_log_id`·`raw.number`, 같은 차주 `clients.raw.scopedToVehicleNumber`, `profiles.settings.subCarSettings` 키를 한 번에 바꿈. 권한은 호출한 사람 기준(RLS 그대로 적용, `security invoker`, `set search_path = public`), `authenticated`만 실행. 순서: ① 읽기 전용 조회(칸·권한 규칙 확인) → ② `create or replace` SQL → ③ 실행(보리 또는 보리 지시로 AI) → ④ 사후검증 조회. 기록 파일 `react-app/supabase/migrations/0022_rename_sub_vehicle.sql`.
- `react-app/src/lib/vehicleMutations.js` — 번호 변경 때 위 함수를 불러 한 번의 `commitBatch`(cars·clients·expenses·drivers·settings + 일지)로 반영.
- 테스트: 새 `react-app/src/lib/vehicleRename.test.js`, 기존 `vehicleMutations.test.js`는 그대로 통과해야 함.
- 공용 값: `requestVehicleSave` 돌려주는 모양 그대로(호출부 `CarListPage.jsx:145`·`onboardingFinish.js:61`).

### 안 건드릴 것
- 서버 지출 행(`fuel_records` 등)의 `raw.vehicleNumber` — 불러오기가 차량 번호로 다시 붙임(`lib/hydrateOwnerDriverExpenses.js:39`), 다음 저장 때 새 번호로 쓰임.
- 서버 `driver_links` — `vehicle_id`로 연결돼 번호와 무관(`hydrateMerge.js:72`).
- 메인 차량 번호 변경 — 메인은 `main` 칸이라 번호에 묶인 것이 없음(`vehicleSupabaseIdForLog` `main` 분기).
- 지출 저장 방식(70번), 차량 삭제(37번).

### 검증
- 자동: `npm test`·`typecheck`·`build`, 엄격 검사 경고 457 이하.
- 새 테스트(가짜 서버):
  - R-1 비회원: A→B 저장 → ①~④ 모두 B, 기기 저장소에도 B.
  - R-2 로그인: A→B → `rename_sub_vehicle` 1회 → 차량 저장 1회, 그 뒤 Store ①~④ B.
  - R-3 로그인 함수 실패 → 차량 저장 0회, Store 전부 옛 번호 그대로, 실패 안내.
  - R-3b 함수 성공·차량 나머지 칸 저장 실패 → Store 옛 번호 그대로·실패 안내, 다시 저장 → 성공하고 B.
  - DB: 실행 뒤 시험 계정에서 함수로 번호 변경 → 차량·전용 거래처·설정 키가 같이 바뀌는지, 남의 차량 id로 부르면 0건 변경(거절)인지 조회로 확인.
  - R-4 번호 그대로 저장 → 거래처·설정 서버 저장 0회(지금과 같음).
  - R-5 이름 바꾼 뒤 지출 저장 → 그 지출이 B 차량 칸(서버 차량)으로 감, 메인 칸 0건.
  - 진실성: 고치기 전 코드로 R-1·R-2·R-5가 실패하는지 확인.
- 브라우저: 개발 서버 시험 계정. 기사 차량 22가2222는 연동 중이라 실제 기사 앱에 영향 → **시험용 미연동 기사 차량을 새로 만들어**(예: 99가9999) 전용 거래처·지출·설정을 넣고 번호를 바꿔 확인.

### 파일 길이
- `lib/vehicleMutations.js` 191줄 → 약 170줄(`planSubLogRename` 이동).
- 새 `lib/vehicleRename.js` 약 110줄.
- 새 `supabase/migrations/0022_rename_sub_vehicle.sql` 약 70줄(SQL 기록 파일).
- 새 테스트 파일 약 200줄(테스트 파일 예외).

### 실패 시 처리
- 새 저장 장치·재시도 큐·임시 보관 없음(AGENTS §7). 실패하면 안내만, 다시 저장하면 마저 저장(목표 3).
- DB 함수는 데이터를 지우지 않는 보존적 변경(`create or replace`, 테이블·칸 변경 없음). 잘못되면 `drop function`으로 되돌릴 수 있음.
- 검증이 실패하면 코드를 멈추고 이 파일에 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법
1. 차주 시험 계정 → 차량 관리 → 기사 차량 하나 추가(예: 99가9999, 일지 방식).
2. 그 차량 일지에 운행 1건·주유 1건, 거래처 관리에서 그 차량 전용 거래처 1곳(고정노선 켬), 그 차량 운행일지 설정에서 일상점검표 켬.
3. 차량 관리 → 그 차량 번호를 99가9998로 수정·저장.
4. 새로고침 → 99가9998 일지에 운행·주유가 있고, 전용 거래처·고정노선 단가가 그대로, 일상점검표 켜짐이면 성공.
5. 주유를 하나 더 추가해도 메인 차량 지출로 안 옮겨지면 성공.

---

### 66-1 수정 착수지시서 (브라우저 검증 실패) `[x]` — 보리 승인 후 수정, 재검증 통과(`1db33e6`에 포함)

**무엇이 왜 실패했나 (2026-10-14 개발 서버 확인)**
- 시험 계정에 시험 기사 차량 99가9999(서버 등록됨)를 만들었다. 거기에 일지 2회·주유 30,000원·전용 거래처 "66번시험거래처"(고정노선)·일상점검표 켜기를 넣은 뒤, 차량 관리에서 번호를 99가9998로 저장했다 → "저장에 실패했습니다".
- 콘솔: `22P02 invalid input syntax for type json — Token "subCarSettings" is invalid`.
- 원인: DB 함수 `rename_sub_vehicle`(0022) 설정 줄 `p.settings -> 'subCarSettings' - v_old`. Postgres는 `-`를 `->`보다 먼저 계산해서, 글자 `'subCarSettings'`를 json으로 읽으려다 실패했다. 앱 테스트는 가짜 서버라 이 SQL을 실행하지 않아 못 잡았다.
- 안전 확인: 함수 전체가 한 번에 되돌려져 서버의 차량 번호·거래처·설정, 그리고 화면(Store)이 **모두 옛 번호 그대로**였다(반만 바뀐 것 없음 — 목표 3의 실패 동작은 실제로 확인됨).
- 함께 본 것: 같은 시각 콘솔에 개발 서버 화면 갱신(HMR) 오류 1줄이 있었다. 이는 코드를 고치던 중 잠깐 생긴 것으로 이번 실패와는 무관하다(지금 화면은 정상 동작).

**수정 계획**
- `react-app/supabase/migrations/0022_rename_sub_vehicle.sql` 한 곳만 고친다: `(p.settings -> 'subCarSettings' - v_old)` → `((p.settings -> 'subCarSettings') - v_old)`(괄호로 계산 순서 고정). 나머지 SQL·앱 코드는 그대로 둔다.
- SQL Editor에서 고친 `create or replace`를 다시 실행한다(보존적 변경). 이어서 사후검증 조회로 권한이 그대로인지 확인한다(관리자 권한 아님·로그인 사용자만 실행).
- 브라우저 재검증: 같은 시험 차량 99가9999 → 99가9998로 번호를 저장하고 아래를 확인한다.
  - 서버: 차량 번호·`legacy_log_id`·`raw.number`, 전용 거래처, 설정 키가 모두 새 번호로 바뀐다.
  - 화면: 일지·지출·거래처·설정이 모두 새 번호로 바뀐다.
  - 새로고침한 뒤에도 그대로이고, 주유를 하나 더 저장해도 그 차량 칸에 들어간다.
- 새 저장 장치는 없다. 앱 테스트는 그대로 둔다(SQL은 브라우저 실검증으로 확인).
