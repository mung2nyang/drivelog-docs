# docs/report.md — 현재 슬라이스 착수지시서

120-D: drivelog-docs `2ce5170`. 120-C: drivelog-docs `ae914c9`. 120-B: drivelog-docs `503a3d3`. 120-A: drivelog-docs `81f927a`. 119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 121-A. 실제 DB 시험을 CI에서 자동으로 — 시작점 파일과 CI 작업 `[x]`

- 근거: 로드맵 121(③). 2026-11-02 보리가 도구는 A(Supabase 로컬 DB 도구)로 정했다 `[확인: 2026-11-02 보리 — "a로 진행"]`.
- 운영 DB 표 구조는 AI가 Supabase 연결로 읽어서 뽑는다 `[확인: 2026-11-02 보리 — "수파베이스 로그인해줄게 너가해"]`. Supabase 연결은 이미 붙어 있었다.
- 2026-11-02 확정했다. 확인할 점 1~3은 모두 "응"이다(200줄 예외, `db-ci` 가지 push 허용, 실패하면 배포 멈춤) `[확인: 2026-11-02 보리 — "세가지 응 ,착수지시서 확정 너가 진행해"]`.
- 이번엔 A만 한다. 지금 시험 2개를 그대로 CI에서 돌리는 데까지다. 시험을 DB 시험 도구(pgTAP) 형식으로 바꾸는 일은 121-B 후보로 둔다.

### 쉬운 요약
- 지금 서버 권한을 확인하는 DB 시험 2개는 사람이 Supabase 화면에서 직접 돌린다. 그래서 CI는 서버 권한이 깨져도 모른다.
- CI 안에서 Supabase를 잠깐 띄우고, 운영과 같은 표 구조를 만든 뒤 시험 2개를 자동으로 돌린다. 하나라도 실패하면 CI가 빨간색이 되고 배포도 멈춘다.
- 운영 DB의 데이터는 건드리지 않는다. 구조만 읽어 오고, 사용자 기록은 읽지도 옮기지도 않는다.

### 현재 상태 (코드 확인)
- DB 시험: `supabase/tests/driver_link_security.sql`(218줄), `driver_invite_security.sql`(129줄)이 있다.
  - 둘 다 임시 사용자·차량을 만들어 쓰고 마지막에 되돌린다(`rollback`). 운영 데이터에 기대지 않는다. 빈 DB에서도 돈다.
  - 로그인 사용자 흉내(`auth.users`, `authenticated` 역할, `auth.uid()`)를 쓴다. 그래서 Supabase 환경이 필요하다.
- DB 변경 기록 `supabase/migrations/0001~0031`에는 기본 표를 처음 만드는 내용이 없다. 예를 들어 0001은 이미 있는 `driver_links`에 칸을 더한다. 그래서 이 파일들만으로는 빈 DB에 운영 구조를 만들 수 없다.
- 운영 DB(프로젝트 `wphlnkfymvpnklgbuxrk`, Postgres 17)를 읽기 전용으로 확인했다(2026-11-02).
  - 표 15개, 보안 규칙 45개, 서버 함수 30개, 트리거 10개, 색인 20개, 번호 생성기 2개가 있다.
  - 쓰는 확장은 `pgcrypto`·`uuid-ossp`·`pg_stat_statements`·`supabase_vault`다.
  - Supabase의 변경 기록표는 비어 있다(`list_migrations` 0건). 지금까지 DB 변경은 SQL Editor로 직접 넣었다.
- 이 PC에는 Docker가 없다. 그래서 로컬 Supabase는 GitHub CI에서만 띄울 수 있다.
- CI(`.github/workflows/ci.yml`)에는 `verify`(테스트·타입·빌드 등)와 `deploy` 작업이 있다. `deploy`는 `verify`만 기다린다.

### 목표 상태 (기대 동작)
1. 운영 DB 구조를 담은 시작점 파일이 생긴다.
   - 담는 것: `public` 아래 표·칸·기본값·제약·색인·번호 생성기·보안 규칙(RLS 켜짐 여부와 규칙)·서버 함수·트리거·권한, 그리고 필요한 확장.
   - 담지 않는 것: 사용자 데이터. `auth`·`storage` 같은 Supabase 자체 영역도 담지 않는다. 로컬 Supabase가 스스로 만든다.
   - 권한은 운영과 똑같이 만든다. 로컬 Supabase가 새 표에 자동으로 주는 권한을 먼저 걷고, 운영 목록대로 다시 준다.
2. CI에 `db-test` 작업이 새로 생긴다. 하는 일은 이 순서다.
   1. Supabase 로컬 도구를 받는다. 버전은 고정한다.
   2. 쓰지 않는 부품(화면 도구 등)을 빼고 로컬 Supabase를 띄운다.
   3. 시작점 파일을 넣는다. 이어서 시작점 뒤에 생긴 DB 변경 파일(`0032` 이후)이 있으면 번호 순서대로 넣는다.
   4. `supabase/tests/*.sql`을 하나씩 돌린다. 오류가 하나라도 나면 실패다.
3. `deploy`는 `verify`와 `db-test`가 둘 다 초록일 때만 돈다.
4. 시작점 파일이 운영과 같은지 "구조 지문"으로 확인한다.
   - 같은 확인 SQL을 운영(Supabase 연결)과 CI의 로컬 DB에서 각각 돌린다. 이 SQL은 표·칸·제약·색인·규칙·함수·트리거·권한의 정의를 모아 종류별 지문(md5)을 만든다.
   - 종류별 지문이 모두 같아야 한다. 이 확인은 121-A를 만드는 동안 한 번 하는 것이다. CI가 매번 하지는 않는다.
5. 시험 2개는 고치지 않고 그대로 통과한다.
6. 일부러 깨는 실험을 한다. 예를 들어 시작점에서 보안 규칙 하나를 빼고 돌린다. 그러면 `db-test`가 빨간색이 되는지 본다(되돌린 뒤 다시 초록).

### 건드릴 파일 (react-app)
- 새 파일
  - `supabase/config.toml`: 로컬 Supabase 설정이다. Postgres 17로 맞추고, 시작할 때 `migrations` 폴더를 자동으로 넣지 않게 한다. 옛 0001~0031은 시작점에 이미 들어 있어 다시 넣으면 안 되기 때문이다.
  - `supabase/baseline/` 아래 시작점 파일들: 운영 구조다. 0031까지 반영한 상태이고, 파일 머리에 뽑은 날짜를 적는다.
  - `scripts/ci-db-test.sh`(이름은 착수 때 확정): 위 2-3, 2-4 순서를 담은 짧은 실행 파일이다.
- 고치는 파일
  - `.github/workflows/ci.yml`: `db-test` 작업을 더하고, `deploy`가 그것도 기다리게 한다.
- 공용 영향: CI는 모든 push에 돈다. `db-test`가 실패하면 앱 배포도 멈춘다. CI 시간은 몇 분 늘어날 것으로 보는데, 실제 시간은 첫 실행에서 잰다.

### 안 건드릴 것
- 운영 DB: 읽기 전용 조회만 한다. 구조 변경과 데이터 읽기는 하지 않는다.
- `supabase/migrations/0001~0031`은 그대로 둔다. 문서 여러 곳이 이 경로를 가리키고 있어서 옮기지 않는다.
- `supabase/tests/` 시험 2개, 앱 코드, `npm test`.
- DB 변경 절차 문서(`testing-playbook.md` §9)는 이번엔 고치지 않는다. 고칠 필요가 있으면 완료([x]) 때 따로 묻는다.

### 검증
- CI의 `verify`·`db-test`가 초록이다. `deploy`는 main에서 둘 다 초록일 때만 돈다.
- 목표 4: 운영과 로컬의 구조 지문을 비교한다. 결과는 진행 기록에 남긴다.
- 목표 6: 깨는 실험을 하고 빨간색을 확인한 뒤 되돌린다.
- 기존 `npm test`·typecheck·lint·depcheck·build는 그대로 통과한다(앱 코드 변경 없음).

### 파일 길이
- `config.toml`, 실행 파일, `ci.yml` 고친 부분은 모두 200줄 안이다.
- 시작점 파일은 200줄을 넘는다. 함수 30개만으로도 천 줄이 넘을 것으로 본다(옛 변경 파일 31개 합이 2,532줄이다).
  - 사람이 손으로 고치는 코드가 아니라 운영에서 뽑은 기록이다. 옛 변경 파일들도 200줄 검사에 넣지 않아 왔다.
  - 그래도 종류별로 나눠 둔다: `01_extensions_tables.sql`, `02_functions.sql`, `03_triggers_policies.sql`, `04_grants.sql`. 각 파일은 200줄을 넘을 수 있다. 이 부분은 확인할 점 1이다.

### 실패 시 처리
- 지문이 다르거나, 시험이 로컬에서 운영과 다르게 실패하면 멈춘다. 원인을 적은 수정 착수지시서로 다시 승인받는다.
- 시험을 고쳐서 통과시키지 않는다. CI에서 `db-test`를 "실패해도 넘어감"으로 두지 않는다.
- 운영 DB에는 아무것도 쓰지 않는다.

### 사용자 확인 방법
- GitHub의 CI 화면에서 `db-test` 작업이 초록인지 본다. 깨는 실험 때 빨간색이었던 기록도 진행 기록에 남긴다.

### 확인할 점
1. 시작점 파일은 200줄을 넘는다. 운영에서 뽑은 기록이라 옛 DB 변경 파일처럼 200줄 예외로 두어도 될까? 종류별 4개 파일로 나누는 것까지는 한다.
2. 이 PC에 Docker가 없어 CI에서만 시험해 볼 수 있다. 처음 맞추는 동안 몇 번 돌려 봐야 한다. 그래서 작업용 가지(`db-ci`)를 만들어 AI가 push해도 될까? 이 가지는 배포가 돌지 않는다. 다 맞으면 main에 합치는 push는 그때 다시 묻는다.
3. `db-test`가 실패하면 배포도 멈추게 할까(권장)? 아니면 실패해도 배포는 하고 빨간색 표시만 할까?

### 진행 기록
- 시작점 파일 4개를 만들었다(`01` 293줄, `02` 822줄, `03` 190줄, `04` 108줄). 운영에서 읽기 전용 조회로만 뽑았다.
  - 함수 12개 본문에 섞여 있던 CR 줄바꿈(179개)은 LF로 바꿨다. 저장소가 `* text=auto`라 커밋 때 어차피 LF가 된다. 모두 줄 끝이었고 여러 줄 문자열 안에는 없었다. 지문 SQL도 함수 비교 때 CR을 빼고 비교한다.
- `config.toml`·`scripts/ci-db-test.sh`·`ci.yml`(`db-test` 작업, `deploy`는 둘 다 기다림)을 썼다. Supabase CLI는 2.122.0으로 고정했다(릴리스 파일을 직접 받음).
- `db-ci` 가지 1차 실행(run 41373601106, `203a2ac`)
  - 로컬 Supabase가 떴고 시작점 4개가 오류 없이 들어갔다. `verify`는 초록이다.
  - 목표 4 구조 지문: 운영과 로컬이 9종류 모두 같다.

    | 종류 | 개수 | 지문(운영 = 로컬) |
    |---|---|---|
    | columns | 173 | `fdf4daa5cd8270d6b86fb7d2dc292b9d` |
    | constraints | 55 | `ca46b8ba690a45dd1c9ca9d363fb453f` |
    | functions | 30 | `a40d3b92c4e9a5fc49407c8957df6202` |
    | grants | 252 | `d5a0505e04c247a1bad8f4a206c6a678` |
    | indexes | 20 | `ec4ce121e95d9fd1b0e7f53987a1af95` |
    | policies | 45 | `afb77eebceb7e0ab1fa42dc8f4bdc6cd` |
    | relations | 37 | `43cfa2c849ea6ab9a36c10b3a726f6d1` |
    | sequences | 2 | `a482d3bf794f71a60740738962660db0` |
    | triggers | 10 | `2b87a57806d116f799973594a8b86a97` |

  - `driver_invite_security.sql`은 통과했다.
  - `driver_link_security.sql`은 실패했다. 82행의 정상 초대 넣기에서 "초대코드는 [코드 생성]으로 만든 10자리여야 합니다." 오류가 났다.
- 실패 원인
  - 이 시험(10-S-1, `e003f0f`)은 초대코드를 `gen_random_uuid()::text`로 만든다(49·50행).
  - 같은 날 나중에 들어간 0019(10-S-2, `3fe7975`)가 초대코드를 10자리 정해진 글자로만 받게 바꿨다(`guard_driver_link_invite_code`).
  - 그래서 이 시험은 0019 이후로 운영에서 돌려도 같은 자리에서 실패한다. 지문이 같으니 로컬과 운영의 차이가 아니다. 시험이 그동안 손으로 다시 돌려지지 않아 묵은 것이다.
- 목표 5("시험 2개를 고치지 않고 그대로 통과")를 지킬 수 없어 여기서 멈췄다. 아래 수정 지시서를 승인받아야 한다.

## 121-A 수정 착수지시서 — 묵은 시험의 초대코드 값만 바꾸기 `[x]`

- 승인했다 `[확인: 2026-11-02 보리 — "승인"]`.

### 쉬운 요약
- 기사 연동 보안 시험이 옛날 형식 초대코드를 써서, 지금 서버 규칙에 막혀 시작도 못 한다.
- 시험 안의 초대코드 두 개만 지금 규칙에 맞는 10자리로 바꾼다. 검사하는 내용(무엇을 막고 무엇을 허용하는지)은 그대로 둔다.

### 건드릴 파일
- `supabase/tests/driver_link_security.sql` 49·50행만 고친다.
  - 지금: `code_a text := gen_random_uuid()::text;`, `code_again text := gen_random_uuid()::text;`
  - 바꿈: `code_a text := 'M2N3P4Q5R6';`, `code_again text := 'M7N8P9Q2R3';`
  - 두 값은 서버 규칙 `^[23456789ABCDEFGHJKMNPQRSTUVWXYZ]{10}$`(숫자 2~9와 I·L·O를 뺀 대문자, 10자리)에 맞다. 다른 시험의 `A2B3C4D5E6`와도 겹치지 않는다.
- 검사 문장(`expect_denied`, `raise exception` 조건)은 하나도 바꾸지 않는다. 주석도 더하지 않는다.

### 검증
- `db-ci` 가지에서 `db-test`가 초록이 되는지 본다.
- 이 두 줄 뒤에서 또 다른 묵은 곳이 나오면 다시 멈추고 보고한다. 검사를 약하게 바꿔서 넘기지 않는다.

### 파일 길이
- 218줄 그대로다.

### 실패 시 처리
- 원래 지시서와 같다. 운영 DB에는 아무것도 쓰지 않는다.

### 수정 뒤 진행 기록
- `driver_link_security.sql` 49·50행만 바꿨다(`5339ded`). `db-ci` 실행 41374127863에서 `db-test`·`verify` 초록이다.
  - 두 시험 모두 끝에 PASS 안내가 찍혔다. `db-test`는 50초 걸렸다(`verify`는 2분 14초라 CI 전체 시간은 늘지 않았다).
- 목표 6 깨는 실험: 시작점 03에서 보안 규칙 "차주만 초대 생성"을 뺐다(`6b84ea8`). 실행 41374363966에서 `db-test`가 빨간색이 됐다.
  - 오류: 정상 초대 넣기가 "new row violates row-level security policy"로 막혔다.
  - 되돌린 뒤(`867a692`) 실행 41374634613에서 다시 초록이다.
- 임시 지문 단계와 파일을 뺀 최종 모양(`1404554`)도 실행 41374899034에서 초록이다.
- main에 코드 커밋 하나로 합쳤다: react-app `4dbb811`. 내용은 `1404554`와 같다.
- main push 승인 `[확인: 2026-11-02 보리 — "응"]`. push 뒤 CI 실행 41375339891에서 `verify`·`db-test`·`deploy` 모두 초록이다.
- `db-ci` 가지 지우기도 승인받았다. AI 쪽 권한 확인에서 막혀 아직 남아 있다.

### §5 점검
- 범위: 바뀐 파일 8개가 "건드릴 파일"과 같다. 시험 파일은 수정 지시서 승인 범위(49·50행)만 바뀌었다. 앱 코드·`migrations`·`npm test`는 그대로다.
- 새 장치: 새 저장소·재시도 장치 없음. 새 파일은 지시서의 설정·시작점·실행 파일뿐이다.
- 주석: 새 주석 5줄에 로드맵 번호·날짜·결정자 없음. 시작점 파일 머리의 뽑은 날짜는 지시서가 정한 것이다.
- 파일 길이: `ci-db-test.sh` 29줄, `config.toml` 33줄, `ci.yml` 114줄. 시작점 파일 4개는 승인받은 200줄 예외다.
- 시험 진실성: 검사 문장은 하나도 바꾸지 않았다. 깨는 실험에서 빨간색이 되는 것을 확인했다.
- 목표 대조: 1 시작점 파일, 2 `db-test` 작업, 3 `deploy`가 둘 다 기다림, 4 지문 9종류 일치, 6 깨는 실험을 모두 확인했다. 5는 묵은 초대코드 값 두 개를 승인받고 바꾼 뒤 통과했다.
- 최종 승인 `[확인: 2026-11-02 보리 — "승인"]`.
