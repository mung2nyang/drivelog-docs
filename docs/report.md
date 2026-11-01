# docs/report.md — 현재 슬라이스 착수지시서

147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 119. 가짜 서버가 받은 호출을 전부 기록 + 중요한 테스트에 조건 확인 추가 `[x]`

- 근거: 로드맵 119(③, 테스트 점검 2026-10-26).
- 2026-11-01 보리가 "응"으로 조사·지시서를 요청했다.
- 148~147 순서를 마친 뒤 첫 번째이고, 120(엄격 모드)의 바탕이 된다.

### 쉬운 요약
- 테스트는 진짜 서버 대신 "가짜 서버"(`testSupport/fakeSupabaseClient.js`)를 쓴다.
- 지금 가짜 서버는 앱이 보낸 조건 중 몇 가지(`eq`·`gte`·`lte`·`range`)만 기억하고 나머지는 버린다.
  - 버리는 조건: "이 차량들만", "id 순서로", "지운 줄 돌려줘" 같은 것.
- 그래서 앱 코드에서 그런 조건을 실수로 지워도 테스트가 그대로 통과한다.
- 이번에는 가짜 서버가 받은 호출을 순서대로 전부 적어 두게 만든다. 그리고 중요한 저장·지우기·읽기 테스트 몇 곳에 "정말 이 조건으로 불렀는지" 확인을 더한다.
- 앱 코드는 바꾸지 않는다. 테스트 도구와 테스트만 바꾼다.

### 현재 상태 (코드 확인)
- 가짜 서버가 기억하는 것
  - `fakeSupabaseClient.js:16-44` `chainable`은 `eq`·`gte`·`lte`·`range`만 `filters`에 담는다.
  - `select('칸')`·`in`·`neq`·`order`·`limit`·`single`·`maybeSingle`은 받기만 하고 버린다.
  - `:51-56`은 표·동작마다 몇 번 불렸는지(`countOf`)만 센다. 넘긴 값과 순서는 남기지 않는다.
- 실제로 놓치는 예
  - `data/cloud/fetchAllRows.saves.test.js:63` I-1(점검표 일괄 읽기)은 `data/sync/dailyInspections.ts:96-97`의 `.in('vehicle_id', …)`·`.order('id')`를 지워도 통과한다(로드맵 119~121 설명).
  - 지우기 경로 4곳은 `.select('id')`로 "실제로 지운 줄"을 받아 0줄이면 실패로 본다: `directMutations.ts:12·31·55` 차량·거래처·기사 연결, `dailyInspections.ts:61` 점검표.
    - 그런데 테스트는 지운 횟수만 보고 어떤 id를 지웠는지는 보지 않는다(`directMutationActions.test.js:118·355·481`).
    - 점검표 지우기 테스트(`dailyInspections.test.js:75`)는 조건은 보지만 `.select('id')`는 보지 않는다.
- 가짜 서버를 쓰는 테스트 파일은 49개다(`grep -l createFakeSupabase`). `chainable`을 직접 쓰는 다른 파일은 없다.

### 목표 상태 (기대 동작)
1. `createFakeSupabase()`가 `calls` 목록과 `callsOf(표, 동작)`를 함께 돌려준다. 호출마다 다음을 순서대로 남긴다.
   - `{ table, method, args, ops }`
   - `args`: 넘긴 값. `select`는 칸 목록, `upsert`·`insert`·`update`는 줄, `upsert`는 옵션까지.
   - `ops`: 뒤에 이어 붙인 조건 전부, 붙인 순서대로. 예: `['eq','id',901]`, `['in','vehicle_id',['v1','v2']]`, `['order','id']`, `['select','id']`, `['maybeSingle']`.
   - 서버 함수는 `{ table: 'rpc', method: 함수이름, args: [넘긴 값], ops: [] }`로 남긴다.
   - `resetHandlers()`가 기록도 비운다.
2. 지금 동작(`handlers`·`filters`·`countOf`·기본 성공 값)은 그대로다. 기존 테스트는 하나도 고치지 않아도 통과한다.
3. 조건 확인을 더하는 테스트(이번 범위)
   - I-1 점검표 일괄 읽기: 모든 쪽 읽기가 `in('vehicle_id', ['v1','v2'])`와 `order('id')`를 붙였는지.
   - 차량 지우기 성공: `vehicles.delete`가 `eq('id', 901)`, `select('id')` 순서로 불렸는지.
   - 거래처 지우기 성공: `clients.delete`가 `eq('id', 701)`, `select('id')`인지.
   - 기사 연결 지우기 성공: `driver_links.delete`가 `eq('id', 610)`, `select('id')`인지.
   - 기사 연결 상태 바꾸기 성공: `driver_links.update`가 `{ status: 'linked', … }` 줄과 `eq('id', 602)`로 불렸는지.
   - 점검표 지우기: 조건 뒤에 `select('id')`를 붙였는지.
4. 효과 확인: 아래 "검증"의 조건 지우기 실험에서 I-1이 실패한다.

### 건드릴 파일
- `react-app/src/testSupport/fakeSupabaseClient.js`
  - `chainable(getResult, ops)`에 두 번째 인자(기록 목록)를 더하고, 모든 조건을 `ops`에 넣는다(기존 `filters`도 그대로 채움).
  - `from`·`rpc`가 `calls`에 남기게 한다. `callsOf`를 더하고, 반환 형식 설명을 고친다.
- 테스트
  - `react-app/src/testSupport/fakeSupabaseClient.test.js` — **새 테스트 파일**이다. 기록 순서·내용, 서버 함수 기록, `resetHandlers`가 기록을 비우는지, 기존 `filters`·`countOf`가 그대로인지 본다.
  - `react-app/src/data/cloud/fetchAllRows.saves.test.js` — I-1에 확인을 더한다.
  - `react-app/src/data/sync/directMutationActions.test.js` — 위 성공 테스트 4개에 확인을 더한다.
  - `react-app/src/data/sync/dailyInspections.test.js` — 지우기 테스트에 확인을 더한다.

### 안 건드릴 것
- 앱 코드(`src` 아래 운영 파일)는 하나도 바꾸지 않는다.
- `stubSupabaseClient.js`(서버를 시험하지 않는 테스트용, 85개 파일)는 그대로 둔다.
- 등록 안 한 호출을 실패로 만드는 엄격 모드는 120번 일이다. 이번에는 기본 성공 동작을 그대로 둔다.
- 나머지 44개 가짜 서버 테스트 파일에 확인을 더하는 일은 이번 범위 밖이다. 필요하면 120 때 파일별로 옮기며 한다.

### 검증
- 자동 검사: `npm test`, `npm run typecheck`, `npm run lint`, `npm run depcheck`, `npm run build`.
- 고치기 전 실패 확인: `fakeSupabaseClient.js`를 되돌리면(`calls`·`callsOf` 없음) 새·바뀐 테스트가 실패해야 한다.
- 조건 지우기 실험(이번 일의 목적 확인)
  - `dailyInspections.ts:96-97`의 `.in(…)`·`.order('id')`를 잠깐 지우고 I-1을 돌린다. 실패해야 한다.
  - 바로 원래대로 되돌리고 `git diff --quiet src/data/sync/dailyInspections.ts`로 그대로인지 확인한다. 이 파일은 커밋에 들어가지 않는다.
- 화면에 보이는 것이 없어 로컬·배포본 화면 확인은 없다. CI 초록만 본다.

### 파일 길이
- `fakeSupabaseClient.js`: 164 → 약 190(200 이하).
- 테스트 파일은 제한 밖.

### 실패 시 처리
- 기존 테스트가 하나라도 깨지면 고치지 않고 멈춘다. 원인을 적은 수정 착수지시서로 다시 승인받는다.
- 새 장치는 만들지 않는다.

### 사용자 확인 방법
- 화면이 바뀌는 일이 아니다. CI 초록과 위 실험 결과(조건을 지우면 테스트가 잡아냄)를 보고로 확인한다.

### 진행 기록
- 코드 커밋 react-app `119ed38`(파일 5개: 가짜 서버 1, 새 테스트 1, 기존 테스트 3). 앱 코드 변경 없음.
- 지시서와 다른 점: 새 테스트 파일을 `src/testSupport/`가 아니라 `src/data/cloud/fakeSupabaseClient.test.js`에 뒀다. `npm test`가 `src/testSupport/`를 돌리지 않아서다(테스트 실행 설정은 안 바꿈).
- 전체 검사 통과: 테스트 962·942개 모두 통과, 타입·lint·의존 검사·빌드 통과. `fakeSupabaseClient.js` 192줄.
- 고치기 전 실패 확인: 새·바뀐 테스트 10개 중 9개는 이전 가짜 서버로 돌리면 실패. 나머지 1개("기존 동작 그대로")는 안전 확인이라 원래부터 통과가 맞다.
- 조건 지우기 실험: `.in('vehicle_id', …)`를 빼면 I-1이 "부탁한 차량만 읽어야 한다"로 실패, `.order('id')`를 빼면 "쪽마다 같은 순서로 읽어야 …"로 실패. 실험 뒤 `dailyInspections.ts` 변경 없음 확인.
- 푸시 뒤 CI verify·deploy 초록(run 41280972200).
- 커밋 확인(데스크톱 AI): 바뀐 파일·확인 항목이 지시서 목표 3과 같음, 앱 코드 변경 없음, `chainable` 직접 쓰는 다른 파일 없음.
- §5 리뷰(데스크톱 AI, 119ed38)
  1. 범위: 바뀐 파일 5개가 "건드릴 파일"과 같다. 새 테스트 위치만 다르고 이유는 위에 적었다. 앱 코드·`stubSupabaseClient.js`는 안 바뀌었다.
  2. 몰래 증설 없음: 새 파일은 지시서에 있던 테스트 1개뿐이다. 새 저장 키·저장 단계는 없다.
  3. 타입 꼼수 없음: 더한 줄에 `any`·`unknown`·`@ts-ignore`·`@ts-expect-error` 없음(grep).
  4. 200줄: `fakeSupabaseClient.js` 192줄. 테스트 파일은 제한 밖.
  5. 테스트 진실성: 테스트에서 지운 줄은 꺼내 쓰는 이름 목록 3줄뿐이고 확인은 하나도 빼지 않았다. 고치기 전 실패·조건 지우기 실험은 위에 적었다.
  6. 공용 영향: 가짜 서버를 쓰는 테스트 파일은 51개다. `handlers`·`filters`·`countOf`·`callCounts`(`App.clientsCars.test.js`가 직접 읽음)는 그대로이고, CI 전체 통과로 확인했다.
  7. 요구사항: 목표 1~4 모두 됐다.
- 보리 최종 승인(2026-11-01).
