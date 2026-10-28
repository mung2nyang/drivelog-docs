# docs/report.md — 현재 슬라이스 착수지시서

129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 129-B 기사 차량 번호를 바꾸면 그 뒤 세금계산서 저장이 계속 실패 `[x]`

폴더별 전수점검(2026-10-26) A6, 로드맵 129 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 앞 조각 129-A `[x]`(react-app `f25d3e0`). **DB 변경 있음(번호 변경 함수 고침 — 0031)**, 앱 코드도.

### 쉬운 요약
- 세금계산서에는 차량 번호가 여러 곳에 적혀 있습니다(계산서 고유 번호 끝, 차량 번호 칸, 차량 이름표 등). 기사 차량 번호를 바꿀 때 운행일지·지출·전용 거래처·기사·차량 설정은 새 번호로 옮기는데, 계산서만 안 옮깁니다(앱도 서버도).
- 그래서 ① 그 차량 계산서가 하나라도 저장돼 있으면, 그 뒤 모든 계산서 저장이 "차량이 아직 저장 중" 오류로 실패합니다(2.5초 기다린 뒤). 새로고침해도 서버에 옛 번호가 그대로라 안 풀립니다. ② 그 차량의 지난 발급 완료 계산서가 새 번호 차량의 서류 목록에서 사라지고, 그 달이 다시 "작성 전"으로 보입니다(코드로 확인, 리뷰에는 없던 것).
- 번호를 바꿀 때 계산서도 새 번호로 옮깁니다. 서버는 번호 변경 함수 안에서(한 번에 다 되거나 하나도 안 됨), 앱은 지금 다른 것들을 옮기는 자리에서 같이.

### 현재 상태 (코드·서버 확인, 2026-10-28)
- 계산서에 번호가 들어가는 칸(기사 차량 계산서): 고유 번호 `id` = `sales|<달>|<거래처>__<번호>`, `partyKey` = `<거래처>__<번호>`(`domain/financeTaxInvoiceGroups.ts:25-28`·`domain/financeTaxInvoiceEntries.ts:48-50`), `carNumber`·`vehicleNumbers`·`vehicleLabel`(`<사업자명>, <번호>` 또는 `<번호>`)·`supplierKey`(`car:<번호>:…`)(`domain/cars.ts:160-164`), `logId`(`financeTaxInvoiceEntries.ts:79`). 메인 차량 계산서는 `__main`·번호 없음.
- 저장 때 차량 찾기: `domain/taxInvoices.ts:41-50` `resolveTaxInvoiceVehicleId`가 `carNumber`로 차량을 찾고, 못 찾으면 `data/sync/syncTaxInvoicesTable.ts:19-26`이 0.5초×5번 다시 본 뒤 `TAX_INVOICE_VEHICLE_RETRY_ERROR`. 저장은 목록 전체를 차례로 보내서 한 장이 막히면 전부 실패.
- 목록 맞추기: 저장된 계산서는 `id`로 찾고(`financeTaxInvoiceEntries.ts:68-69`), 발급 완료분은 `partyKey`가 `__<지금 번호>`로 끝나는 것만(`:102-103`) → 옛 번호 계산서는 안 보임.
- 번호 변경: 서버 `rename_sub_vehicle`(0022, 0029 `rename_and_save_sub_vehicle`가 안에서 부름)은 차량 번호·전용 거래처·차량 설정만, `tax_invoices` 안 건드림. 앱 `data/sync/vehicleRename.ts:54-80` `planRenamedPlateEntries`도 지출·거래처·기사·설정만. 부르는 곳 `data/sync/vehicleMutations.ts:63`(비회원·로그인 둘 다).
- 서버 표: `tax_invoices.vehicle_id` → `vehicles(id)` `ON DELETE SET NULL`(번호가 바뀌어도 그대로인 차량 id). 불러올 때는 서버 `raw`가 이김(`taxInvoices.ts:73-84`). 테스터 계정 계산서 0줄(2026-10-28).
- 실패 테스트 기록 `docs/full-code-review-failing-tests.md` A6(재현됨).

### 목표 상태 (기대 동작)
1. 기사 차량 번호를 바꾸면 그 차량 계산서의 `id`·`partyKey`·`carNumber`·`vehicleNumbers`·`logId`·`vehicleLabel`·`supplierKey`에서 옛 번호가 새 번호로 바뀐다 — 서버(`vehicle_id`가 그 차량인 줄)와 앱 화면 모두.
2. 서버는 번호 변경 함수 안에서 같이 — 차량 번호·거래처·설정·계산서가 다 바뀌거나 하나도 안 바뀐다.
3. 번호를 바꾼 뒤 계산서 저장이 된다(실패 테스트 기록 A6과 같은 순서).
4. 그 차량의 지난 발급 완료 계산서가 새 번호 차량 서류 목록에 그대로 보인다.
5. 메인 차량 계산서·다른 기사 차량 계산서는 안 바뀐다. 번호를 안 바꾸는 저장은 지금과 같다.

### 배포 순서 (플레이북 §9)
1. 착수 전 읽기 진단(위 "서버" 줄 — `rename_sub_vehicle` 본문·`tax_invoices` 칸 그대로인지).
2. SQL Editor에서 `0031` 실행 → 사후검증(파일 끝 주석) → 되돌림 시험: 트랜잭션 안에서 테스터 기사 차량(99가9998)에 계산서 1줄을 넣고 번호 변경 → 그 줄 7칸이 바뀌고 메인 차량 계산서는 그대로인지 본 뒤 `rollback`.
3. 그다음 앱 push → CI 초록 → 배포본 확인. 순서가 바뀌어도 망가지지는 않음(옛 앱 + 새 DB: 새로고침하면 풀림, 새 앱 + 옛 DB: 다음 계산서 저장 때 앱이 새 번호로 덮음) — 그래도 §9 순서대로.
4. ①~③ 중 하나라도 기대와 다르면 push하지 않고, 0022를 다시 실행해 함수를 되돌린 뒤 지시서를 고쳐 다시 승인받는다.

### 건드릴 파일
- 새 `react-app/supabase/migrations/0031_rename_sub_vehicle_tax_invoices.sql` — `create or replace function public.rename_sub_vehicle`(이름·인자·돌려주는 값·권한 0022와 같음)에 전용 거래처 다음으로 아래 한 덩이를 더함. 0029는 그대로(안에서 이 함수를 부름).
  ```sql
  update public.tax_invoices t
  set raw = t.raw || jsonb_strip_nulls(jsonb_build_object(
        'id', case when right(t.raw ->> 'id', length(v_old) + 2) = '__' || v_old
                   then left(t.raw ->> 'id', length(t.raw ->> 'id') - length(v_old)) || v_new end,
        'partyKey', case when right(t.raw ->> 'partyKey', length(v_old) + 2) = '__' || v_old
                         then left(t.raw ->> 'partyKey', length(t.raw ->> 'partyKey') - length(v_old)) || v_new end,
        'carNumber', case when t.raw ->> 'carNumber' = v_old then v_new end,
        'logId', case when t.raw ->> 'logId' = v_old then v_new end,
        'vehicleLabel', case when t.raw ->> 'vehicleLabel' = v_old then v_new
                             when right(t.raw ->> 'vehicleLabel', length(v_old) + 2) = ', ' || v_old
                             then left(t.raw ->> 'vehicleLabel', length(t.raw ->> 'vehicleLabel') - length(v_old)) || v_new end,
        'supplierKey', case when left(t.raw ->> 'supplierKey', length(v_old) + 5) = 'car:' || v_old || ':'
                            then 'car:' || v_new || substr(t.raw ->> 'supplierKey', length(v_old) + 5) end,
        'vehicleNumbers', case when jsonb_typeof(t.raw -> 'vehicleNumbers') = 'array' and (t.raw -> 'vehicleNumbers') ? v_old
                               then (select jsonb_agg(case when e = v_old then v_new else e end)
                                     from jsonb_array_elements_text(t.raw -> 'vehicleNumbers') e) end
      ))
  where t.user_id = auth.uid() and t.vehicle_id = p_vehicle_id;
  ```
  머리 주석 최종 문구: `-- 0031: 기사 차량 번호를 바꿀 때 그 차량 세금계산서(raw)의 번호도 같이 옮긴다 — 번호·거래처·설정·계산서가 한 번에(0022와 같은 함수, 본문만 더함).`
- `react-app/src/domain/taxInvoices.ts` — 위 SQL과 같은 규칙의 순수 함수 `renameTaxInvoicePlate(item, oldNumber, newNumber)`(맞는 칸이 없으면 같은 객체 그대로). 주석 최종 문구: `/** 기사 차량 번호가 바뀌면 계산서 안의 옛 번호를 새 번호로(서버 rename_sub_vehicle과 같은 규칙). */`
- `react-app/src/data/sync/vehicleRename.ts` — `planRenamedPlateEntries`에 계산서 한 묶음 추가(바뀐 계산서가 있을 때만 `invoices` 항목). 함수 주석의 목록에 "계산서"를 더함: `/** 옛 번호를 가리키는 지출·전용 거래처·기사 연결·차량 설정·계산서를 새 번호로 바꾼 commitBatch 항목(바뀐 도메인만). */`
- `react-app/src/domain/taxInvoices.test.js` — `renameTaxInvoicePlate`: 기사 차량 계산서 7칸 바뀜(사업자명 있는 이름표 포함), 메인·다른 차량 계산서 그대로.
- 새 `react-app/src/data/sync/invoicesSave.renamedCar.test.js` — 실패 테스트 기록 A6 순서(로그인, `requestVehicleSave`로 번호 변경 → 계산서 저장 통과) + 번호 변경 뒤 Store 계산서가 새 번호·발급 완료 계산서가 새 번호 목록에 보임(`listTaxInvoiceEntries`).
- 공용 값: `planRenamedPlateEntries`는 `vehicleMutations.ts:63` 한 곳에서만 씀(`grep`), 공용 스타일 변경 없음.

### 안 건드릴 것
- 차량을 지운 직후 계산서 저장 실패(리뷰 A6 끝 문장, 새로고침하면 풀림) — 이번 범위 밖.
- 0029 `rename_and_save_sub_vehicle` — 그대로(바뀐 0022 함수를 안에서 부름).
- 계산서 저장(`syncTaxInvoicesTable.ts`) — 129-A 그대로. 차량을 못 찾을 때 기다리는 동작도 그대로.
- 새 번호와 같은 번호를 쓰던 지운 차량의 계산서가 남아 있어 겹치는 경우 — 번호 변경이 지금 차량끼리 같은 번호를 막고(0022), 지운 차량 계산서는 차량 연결이 비어(`SET NULL`) 서버에서 안 바뀜. 겹침 정리는 범위 밖.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(§5 세션·§9 배포 순서).
- 새 테스트가 고치기 전 코드에서 실패하는지 먼저 확인(A6 기록처럼 계산서 저장 실패), 고친 뒤 통과. 기존 `vehicleRename.test.js` R-1~R-5·`taxInvoices.test.js`·129-A `invoicesSave.retry.test.js` 그대로 통과. 로컬 `npm test` 전체 통과.
- DB는 위 "배포 순서" 2의 되돌림 시험(앱 테스트는 가짜 서버라 SQL 규칙을 못 봄 — TS 함수와 SQL이 같은 규칙인지는 되돌림 시험 결과와 TS 테스트를 같은 입력으로 대조).
- 테스트·타입·빌드·코드 검사는 CI가 봄. 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `taxInvoices.ts` 88 → 약 110줄, `vehicleRename.ts` 92 → 약 97줄, 새 `0031` 약 90줄(0022 본문 전체 + 계산서 덩이), `taxInvoices.test.js` 101 → 약 130줄, 새 `invoicesSave.renamedCar.test.js` 약 80줄.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음 — 이미 있는 번호 옮기기에 계산서를 더함). 실패하면 0022를 다시 실행해 되돌리고 수정 착수지시서를 다시 써서 승인받음.

### 사용자 확인 방법
1. 배포본 로그인 테스터 계정 → 서류 발급에서 기사 차량(99가9998) 계산서를 하나 저장(그 차량 운행분이 없으면 이 단계는 되돌림 시험으로 대신).
2. 차량 관리 → 99가9998 번호를 바꿔 저장 → 서류 발급에서 새 번호 차량 계산서가 그대로 보이고, 계산서 저장이 되면 성공.
3. 확인 뒤 번호를 원래대로 되돌리고, 확인용 계산서는 서버에서 지움(129-A와 같은 방식, 보리 확인 뒤).

### 고를 것
- **가(권함) 번호를 바꾸면 계산서도 새 번호로 옮김** — 위 내용. `[확인: 2026-10-28 보리 — "가안"]` DB 변경 있음, 다른 것들을 옮기는 방식(로드맵 66·115)과 같음, 지난 발급 완료 계산서도 새 번호 목록에 남음.
- 나. 옮기지 않고 저장만 풀기 — 차량을 못 찾는 옛 계산서는 서버의 차량 연결을 그대로 두고 고침(앱 1파일, DB 변경 없음). 저장 실패는 풀리지만 위 ②(지난 계산서가 새 번호 목록에서 사라지고 그 달이 "작성 전"으로)는 그대로 남음.

### 누가 하나
- 데스크톱 AI가 SQL 실행·사후검증·되돌림 시험·앱 코드·코드 커밋까지, push는 보리 `[확인: 2026-10-28 보리 — "가안, 너가 진행"]`.

### 진행 기록
- 착수 전 진단(2026-10-28): 서버 `rename_sub_vehicle` 본문이 0022 파일과 같음, `tax_invoices` 안 건드림(`position('tax_invoices' in prosrc)` false), security invoker·`search_path=public`. `tax_invoices` 칸 11개(`vehicle_id uuid`, `raw jsonb` 등) 그대로. 0029 `rename_and_save_sub_vehicle` 그대로.
- SQL 실행: `0031` 본문(begin~commit) 실행 → 사후검증 1줄: `security_definer=false`, `proconfig={search_path=public}`, `anon_exec=false`, `auth_exec=true`, `touches_invoices=true`, 돌려주는 값 `TABLE(old_number text, new_number text, clients_updated integer)` 그대로.
- 되돌림 시험(트랜잭션 안, 테스터 권한 `role authenticated`·`jwt sub` = 테스터, 끝에 오류를 내서 전부 되돌림): 계산서 3줄 넣음 — 99가9998 발급 완료(이름표 `시험상사, 99가9998`, 공급자 `car:99가9998:…`), 99가9998 작성 전(이름표 번호만, 공급자 `owner:111`), 메인 발급 완료 → `rename_sub_vehicle(99가9998 → 99가0001)` → 결과: 차량 99가0001, 거래처 1줄 옮김, 99가9998 계산서 2줄은 `id`·`partyKey`·`carNumber`·`logId`·`vehicleLabel`·`vehicleNumbers` 새 번호(첫 줄은 `supplierKey`도 `car:99가0001:…`, 둘째 줄 `owner:111` 그대로), 메인 계산서 그대로. 되돌림 확인: 차량 99가9998, 계산서 0줄, 전용 거래처 1줄·설정 키 99가9998 그대로. TS `renameTaxInvoicePlate` 테스트(같은 입력)와 결과 일치.
- 코드: react-app `516703f`(파일 5개 = 지시서 "건드릴 파일" 그대로, 주석은 지시서 최종 문구 그대로). 줄 수 `taxInvoices.ts` 110, `vehicleRename.ts` 96, `0031` 93, `taxInvoices.test.js` 133, 새 `invoicesSave.renamedCar.test.js` 76. `vehicleRename.ts` 머리 주석 1~2줄(옮기는 목록·0029 설명)은 지시서 범위 밖이라 안 바꿈 — 계산서가 빠진 채 남음.
- 고치기 전 실패 확인: `vehicleRename.ts`·`taxInvoices.ts`만 마지막 커밋으로 되돌려 새 테스트 실행 → 3개 모두 실패(`taxInvoices.test.js`는 새 함수가 없어 불러오기 실패), 복구(`cmp` 같음) 뒤 통과.
  ```
  ✖ 번호를 앱에서 바꾼 뒤에도 세금계산서 저장이 된다 (2540ms)
    AssertionError [ERR_ASSERTION]: Got unwanted rejection.
    Actual message: "방금 등록한 차량이 아직 저장 중입니다. 잠시 후 다시 시도해 주세요."
  ✖ 번호를 바꾸면 그 차량 계산서가 새 번호로, 발급 완료분이 새 번호 목록에 보이고 메인 계산서는 그대로
      actual: 'sales|2026-09|한진__34나5678',
      expected: 'sales|2026-09|한진__56다7890',
  SyntaxError: The requested module './taxInvoices.ts' does not provide an export named 'renameTaxInvoicePlate'
  ℹ tests 3  ℹ pass 0  ℹ fail 3  (exit=1)
  ```
- 로컬: `test:unit` 927 통과·`test:app` 901 통과(실패 0), `typecheck`·`lint` 통과.
- push(보리) 뒤 CI **verify 실패**(run 40628601213, deploy 건너뜀 — 배포 안 됨): `test:app` 901개 중 1개 — `features/expenses/ui/expenseSaveOnce.test.js:149` "저장 실패면 알림 + 창 그대로, 잠금이 풀려 다시 누르면 저장된다"가 `:157` `typeName`에서 지출 입력칸(`#expenseName`)을 못 찾음(`assert.ok(el)`). 이번에 바꾼 계산서·번호 변경 경로와 다른 지출 화면 테스트.
- 흔들림 확인(로컬, 이 파일만 반복): 지금 코드 `516703f` 43번 중 1번 실패, 바로 전 커밋 `f25d3e0`(임시 작업 폴더, 확인 뒤 지움) 37번 중 0번. 최근 CI 40번에 이 테스트 실패 없음. 드물게 흔들리는 테스트로 보이지만 이번 변경과 무관하다고 단정할 근거는 부족(이번 변경은 이 화면 코드를 안 건드리지만 앱 전체 불러오기에 파일 하나가 더 붙음).
- 원인(데스크톱 AI 조사): 같은 파일 두 번째 테스트(일지 지출 창)가 저장 뒤 입력 칸 붙잡기 기록을 `history.back()`으로 빼는데 jsdom은 타이머 두 번 뒤에 처리 → 느리면 세 번째 테스트가 `/app/expenses`를 그린 뒤 주소가 `/app/day`로 돌아가 입력 창이 사라짐. `history.back`을 5~8ms 늦추면 CI와 같은 `:157`에서 실패로 재현. 앱 코드 문제 아님, 516703f와 무관.
- 수정: react-app `0c92e9a`(테스트 파일만 — 닫기 전에 붙잡기 기록이 빠질 때까지 기다림, 보리 지시로 지시서 없이). 늦추기 0~50ms 모두 통과, 비슷한 테스트 3개는 같은 날짜 주소 안에서만 움직여 영향 없음.
- push(보리) 뒤 CI verify·deploy 초록(run 40630712843) — 516703f도 같이 배포됨.
- 배포본 확인(로그인 테스터 계정, 2026-10-28): 99가9998은 새 운행을 넣지 않고 기존 10-14 일지(고정 노선 2회 × 30,000원)의 계산서(66번시험거래처, 60,000원)를 저장(사업자등록번호는 보리가 입력) → 서버 1줄 `66번시험거래처__99가9998` → 번호를 99가0001로 → 서버 같은 줄 `id`·`partyKey`·`vehicleLabel`·`vehicleNumbers` 새 번호, 새 번호 서류 발급에 저장한 계산서 그대로(사업자등록번호 남음) → 그 계산서 다시 저장 "세금계산서 작성 내용을 저장했습니다."(서버 같은 줄 고쳐짐, 줄 수 1) → 번호 99가9998로 되돌림(계산서도 옛 번호로 돌아옴). 메인 계산서는 서버 줄이 생기지 않게 저장 안 함, 발급 완료 상태는 시험 안 함(되돌림 시험으로 대신).
- 정리: 시험 계산서 줄 SQL로 지움(그 계정 계산서 0줄), 계산서 저장 때 거래처에 같이 들어간 사업자등록번호는 거래처 수정에서 비움 — 서류 발급 화면이 시험 전과 같음(작성 전 1, "사업자등록번호 미입력").
- §5 리뷰(516703f): ① 범위 — 파일 5개 = "건드릴 파일", 0029·`syncTaxInvoicesTable.ts` 안 바뀜(`git show --stat`) ② 증설 없음 — 새 파일은 지시서의 0031·새 테스트뿐, 새 저장 키·대비 장치 없음 ③ 타입 꼼수 없음(`any`·`unknown`·`@ts-*`·`!` 추가 0) ④ 200줄 — 최대 `taxInvoices.ts` 110 ⑤ 테스트 — 지운 테스트 줄 0, 새 테스트 5개는 고치기 전 실패 확인됨 ⑥ `planRenamedPlateEntries`는 `vehicleMutations.ts:63` 한 곳(비회원·로그인 공통), 공용 스타일 없음 ⑦ 기대 동작 1~5 — 1·3은 배포본, 2·5는 되돌림 시험·단위 테스트, 4(발급 완료분)는 단위 테스트·되돌림 시험으로만(배포본은 작성 전 계산서).
- 참고: 배포본의 실제 계산서는 `carNumber` 없음·`logId` `fleet`였음(지시서 "현재 상태"는 번호가 든다고 적음). 저장 때 차량 찾기는 `carNumber`가 없으면 `vehicleNumbers[0]`(`taxInvoices.ts:43`)이라 이번 옮기기로 맞음 — 결함 아님.
- 같이 들어간 다른 커밋: `0c92e9a`(테스트 파일만, CI 흔들림 수정 — 보리 지시로 지시서 없이).
- 보리 최종 승인 `[x]` (2026-10-28).

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
