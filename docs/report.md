# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 61-B 차량 관리 창에 "기사 몫 기준" 드롭다운 + 연동 기사에게도 기준 전달(서버 함수) `[x]`

> **진행(2026-10-18)**: 보리 "작업진행해"(확인할 점 1 §6 예외·2 시안 그대로). 코드는 끝났고 **커밋은 서버 확인 SQL(DB 절차 1번) 결과로 0024가 맞는지 본 뒤 한 번에**(코드 커밋 1번 규칙).
> - 바뀐 파일(지시서 목록과 같음): 새 `cars/CarShareBaseField.jsx`(드롭다운·설명·예시, 가운뎃점 없이), `CarFormModal.jsx` 189→192(매출제 + %일 때만), `CarListPage.jsx` 230→231(§6 예외 승인: 새 창 기본값·수정 열 때 기준 값 — 같은 줄에 붙여 1줄만 늘어남), `carDriverFields.js` 70→73(저장 칸), `hydrateEmployedDriverCar.js`(서버 `driver_share_base` → 차량, 모르는 값 기본), `monthSettlement.js`(결과에 `subCarCommBase`), `CalendarMonthSummary.jsx`(ⓘ 두 번째 문장을 기준별로 — 첫 문장 "이 차량의 수수료(30%)입니다."는 그대로), 새 `supabase/migrations/0024_assigned_vehicle_share_base.sql`(0016 + 맨 끝 `driver_share_base`).
> - 진행 중 고친 것: ⓘ 첫 문장의 "수수료"까지 "기사 몫"으로 바꿨다가 기존 달력 테스트 2개 실패 → 지시서 범위(계산 기준 문장만)로 되돌림.
> - 새 테스트 7개(차량 창 보임·숨김·저장·다시 열기·월급제 전환 뒤 유지, 저장 칸·모르는 값, 서버 칸 담기·옛 서버·모르는 값, 달력 ⓘ 기준별) — 고치기 전 코드로 돌리면 모두 실패(revert-check, 5개 파일 복구 일치). `npm test` 1044 + 488 통과, 타입 검사·빌드 통과.
> - 개발 서버 비회원 360px 밝은·어두운 화면: 기사 차량 수정 창에 "기사 몫 기준 — 운송료 전체", "거래처 수수료 뗀 뒤"로 바꿔 저장 → 차량에 저장·다시 열면 그 값, 달력 "2222 차량 30% - 270,000원"·ⓘ "거래처 수수료를 뺀 운송료에서 계산해 이만큼 뺍니다.", 매출 "기사 급여 -270,000원", 기사 관리 "기사 정산금 270,000원". 콘솔 오류 없음. 사진 `scratchpad\v61b\`.
> - DB 절차 1 끝(2026-10-18, 보리 요청으로 AI가 Supabase SQL 편집기에서 읽기 전용 조회): 함수 1개, 인자 없음, 반환 14칸·순서·본문이 0016과 같음, `security definer`·`search_path=public`, 실행 권한 `authenticated`·`service_role`·`postgres`만(`anon` 없음) → **0024 그대로 확정**.
> - 코드 커밋 `b15ea59`(파일 12개, 0024 포함).
> - DB 절차 2 끝(2026-10-18): 0024 원본 파일 그대로 SQL 편집기에 넣고 보리가 실행 → "Success".
> - DB 절차 3 끝(2026-10-18, AI 읽기 전용 조회): 함수 1개, 반환 15칸·맨 끝 `driver_share_base text`, 본문 0024와 같음, `security definer`·`search_path=public`, 실행 권한 `authenticated`·`postgres`·`service_role`(`anon` 없음) — 기대와 일치.
> - push 뒤 CI verify·deploy 초록(run 39106377872).
> - 남은 것: 연동 기사 실제 확인 → 최종 승인.

61-A `[x]`(`e91e9c8`) 다음. **보리 결정(2026-10-18)**: 서버 함수에 기준 칸 추가는 61-B와 같이. 61-A에서 기준 값 저장·네 화면 같은 계산은 끝났고, 이번엔 **고르는 칸**과 **연동 기사 쪽 전달**.

### 쉬운 요약
- 기사 차량 등록·수정 창의 "정산(매출제 %)" 아래에 **"기사 몫 기준"** 드롭다운을 붙입니다: "운송료 전체"(기본) / "거래처 수수료 뗀 뒤".
- 연동 기사 앱도 차주가 고른 기준으로 계산하도록, 기사에게 차량 정보를 보내 주는 서버 함수에 이 칸 하나를 더합니다(보리가 SQL 붙여 넣기).
- 달력 정산의 기사 몫 설명(ⓘ)도 고른 기준에 맞는 문장으로 바꿉니다.

### 시안 (개발 서버 비회원 360px, 기사 차량 등록 창에 임시로 모양만 넣어 찍음 — 코드 안 바꿈)
- "정산 — 매출제 [ ]%" 바로 아래: 제목 "기사 몫 기준", 설명 "기사 몫(%)을 어느 금액에서 계산할지 고릅니다.", 상자형 드롭다운(앱 공용 `AppDropdown`), 아래 예시 한 줄 "예) 운송료 100만원, 거래처 수수료 10만원, 기사 몫 30% → 운송료 전체 30만원 / 거래처 수수료 뗀 뒤 27만원".
- **매출제 + 비율(%)일 때만** 보임(월급제·건당이면 기준이 계산에 안 쓰여 숨김).
- 사진: `C:\Users\znlsl\AppData\Local\Temp\claude\C--dev-teat\7bbd9c7d-3733-4708-b41b-39841eaab03d\scratchpad\m61\` — `form-light.png`, `form-dark.png`, `open-light.png`, `open-dark.png`(펼친 모양).
  - 시안 사진의 예시 줄엔 가운뎃점이 찍혀 있는데, 실제로는 87번 규칙대로 쉼표로 씀.

### 현재 상태 (코드 확인)
- 정산 칸: `components/cars/CarFormModal.jsx:132~165`(매출제/월급제 단추 + % 칸). 이 파일 189줄.
- 창 값 ↔ 차량: 새 창 기본값 `components/cars/CarListPage.jsx:23~28`(`emptyDraft`), 수정 열 때 `CarListPage.jsx:65~86`(`openEdit`) — **이 파일 230줄(200 넘음)**, 저장 때 `domain/carDriverFields.js` `driverFieldsFromDraft`(기사 관련 칸 정리) → `domain/cars.js` `upsertCar`(기존 칸은 `...` 로 유지 — 61-A에서 확인).
- 연동 기사 쪽 차량: `lib/hydrateEmployedDriverCar.js` `carFromAssignedSummary`가 서버 함수 `get_assigned_vehicle_summary`(마이그레이션 `supabase/migrations/0016_drop_settlement_mode.sql`, 14칸) 결과에서 정해진 칸만 담음 → 기준 칸 없음 → 늘 기본값.
- 달력 기사 몫 설명: `components/calendar/CalendarMonthSummary.jsx:24~28` `subCarCommInfo` — "거래처 수수료를 뺀 운송료에서 이만큼 다시 뺍니다."(61-A 뒤 기본값과 맞지 않음). 이 파일 169줄.

### 목표 상태 (기대 동작)
1. 기사 차량 창에서 매출제 + %일 때 "기사 몫 기준" 드롭다운이 보이고, 고른 값이 저장된다. 수정 창을 다시 열면 고른 값이 보인다. 안 고른 차량은 "운송료 전체".
2. 월급제·건당이면 드롭다운을 숨기고, 저장된 기준 값은 지우지 않는다(다시 매출제로 바꾸면 그대로).
3. 메인 차량에는 없다.
4. 서버 함수 `get_assigned_vehicle_summary`가 `driver_share_base`(차량 `raw`의 `driverShareBase`) 한 칸을 더 내보내고, 연동 기사 앱이 그 값을 차량에 담아 본인 매출을 같은 기준으로 계산한다. 모르는 값이면 기본값.
5. 서버 함수와 앱 배포 순서가 어느 쪽이 먼저여도 깨지지 않는다(앱이 먼저면 칸이 없어 기본값, 서버가 먼저면 옛 앱은 새 칸을 무시).
6. 달력 기사 몫 설명이 기준에 맞게: 전체면 "운송료 전체에서 계산합니다", 뗀 뒤면 "거래처 수수료를 뺀 운송료에서 계산합니다"(최종 문구는 구현 때 짧게, 가운뎃점 없이).

### 건드릴 파일
- 새 `react-app/src/components/cars/CarShareBaseField.jsx`(약 35줄) — 드롭다운 + 설명·예시(`CarFormModal.jsx`가 200줄 넘지 않게 따로).
- `react-app/src/components/cars/CarFormModal.jsx` — 위 부품을 매출제 % 칸 아래에(+2줄, 189→191), 창 값 타입에 `driverShareBase`.
- `react-app/src/components/cars/CarListPage.jsx` — `emptyDraft`·`openEdit`에 기준 값 1줄씩(230→232) — **§6 예외 요청(확인할 점 1)**.
- `react-app/src/domain/carDriverFields.js` — 저장할 칸에 `driverShareBase`(기사 차량만).
- `react-app/src/lib/hydrateEmployedDriverCar.js` — 서버 결과의 `driver_share_base`를 차량에(모르는 값 버림).
- `react-app/src/domain/monthSettlement.js` — 정산 결과에 기준 값 한 칸(설명 문구용, +1줄).
- `react-app/src/components/calendar/CalendarMonthSummary.jsx` — 설명 문구를 기준에 맞게.
- 새 `react-app/supabase/migrations/0024_assigned_vehicle_share_base.sql` — 서버 함수에 칸 1개(0016과 같은 권한·나머지 14칸·순서 그대로 + 맨 끝 1칸).
- 테스트: `CarFormModal.test.js`(보임/숨김·저장·다시 열기), `hydrateEmployedDriver.test.js`(칸 담기·모르는 값), `CalendarMonthSummary`/`monthSettlement` 설명 문구, `carDriverFields` 저장 칸.

### DB 변경 절차 (AGENTS §9)
1. AI가 **읽기 전용 확인 SQL**을 드림 → 보리가 Supabase SQL 편집기에 붙여 넣고 결과를 알려 줌: 지금 `get_assigned_vehicle_summary`의 반환 칸·정의·권한(추측 금지, 0016과 같은지 확인).
2. 결과를 보고 AI가 `0024` 완성본(되풀이해도 안전한 `drop … if exists`·`create or replace`, `begin … commit`, `security definer`·`set search_path = public`, 권한 다시 주기)을 드림 → 보리가 붙여 넣어 실행.
3. AI가 **확인 SQL + 기대 결과**를 드림(칸 15개, 맨 끝 `driver_share_base`, `authenticated`만 실행 가능) → 보리가 실행해 맞는지 확인.
- 코드 커밋은 1~3과 상관없이 해도 안전(목표 5). 연동 기사 실제 확인은 3번 뒤.

### 안 건드릴 것
- 61-A의 계산 함수·네 화면 계산.
- 서버 `vehicles` 표(칸은 이미 `raw` 안에 저장됨), 다른 서버 함수.
- 차량 목록 카드 표시(기준은 창에서만).

### 검증
- 새/고친 테스트: ① 매출제 %면 드롭다운 보임, 월급제·건당·메인 차량이면 안 보임 ② 고르고 저장 → 차량에 `driverShareBase` ③ 수정 창 다시 열면 고른 값 ④ 월급제로 바꿨다 저장해도 기준 값 유지 ⑤ 서버 결과 `driver_share_base` → 연동 기사 차량에 담김, 모르는 값·없음 → 기본값 ⑥ 달력 설명 문구가 기준별로 다름. 고치기 전 실패 확인(revert-check).
- `npm test`·`npm run typecheck`·`npm run build` 통과, 가운뎃점 검사(87번) 통과.
- 개발 서버 비회원 360px 밝은·어두운 화면: 기사 차량 창에서 고르기·저장·다시 열기, "뗀 뒤"로 바꾸면 달력·매출·기사 관리 기사 몫이 함께 27만원으로(61-A 테스트 예와 같은 자료), 설명 문구 확인·사진.
- 서버: 위 DB 절차 3번 확인 결과.

### 파일 길이
- `CarFormModal.jsx` 189→약 191, `CarListPage.jsx` 230→232(§6 예외 요청), `carDriverFields.js` 70→약 72, `hydrateEmployedDriverCar.js` 37→약 40, `monthSettlement.js` 161→162, `CalendarMonthSummary.jsx` 169→약 172, 새 `CarShareBaseField.jsx` 약 35, 새 SQL 약 50.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 서버 함수 실행이 실패하면 0024는 `begin … commit` 한 묶음이라 그대로 되돌아감 — 결과를 보고 수정 SQL을 다시 드림. 테스트·검증 실패 시 고치지 않고 수정 착수지시서.

### 사용자 확인 방법 (배포 뒤)
1. 차주 계정: 차량 관리 → 기사 차량 수정 → 매출제 % 아래 "기사 몫 기준"이 보이고 "거래처 수수료 뗀 뒤"로 바꿔 저장 → 다시 열면 그대로.
2. 거래처 수수료 있는 그 차량의 달력·매출 "기사" 탭·기사 관리 기사 몫이 함께 줄어든다.
3. (서버 SQL 실행 뒤) 그 차량의 연동 기사 계정 매출 화면 정산액도 같은 금액.

### 확인할 점
1. `CarListPage.jsx`(230줄, 200 넘음)에 기준 값 2줄을 더하는 §6 예외를 승인해 주세요(새 창 기본값·수정 열 때 — 나누려면 차량 화면 처리 흐름을 훅으로 옮기는 로드맵 60번과 겹쳐서, 60번에서 함께 줄이는 쪽 권장).
2. 시안 모양·위치(매출제 % 바로 아래, 매출제 + %일 때만)가 괜찮은지 봐 주세요.
