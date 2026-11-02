# docs/report.md — 현재 슬라이스 착수지시서

121-B: drivelog-docs `0aa4357`. 긴 주석 줄이기: drivelog-docs `59ddc9d`. 121-A: drivelog-docs `1590ef7`. 120-D: drivelog-docs `2ce5170`. 120-C: drivelog-docs `ae914c9`. 120-B: drivelog-docs `503a3d3`. 120-A: drivelog-docs `81f927a`. 119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 124-A. 서버 함수(운비서) 타입 검사·시험을 CI로 `[x]`

- 근거: 로드맵 124(③ 배포 안전, "124는 121 다음"). Deno는 CI에서만 들여 쓴다 `[확인: 2026-11-02 보리 — "응"]`.
- 확정했다. AI가 직접 작업한다 `[확인: 2026-11-02 보리 — "확정", "너가 작업해"]`.
- 이번엔 A만: CI 단계 + 서버 시동 줄이 없는 파일들의 시험. 받기 파일(`index.ts`) 시험은 아래 "안 건드릴 것" 참고.

### 쉬운 요약
- 운비서 서버 코드는 앱과 달리 지금 CI가 타입 검사도 시험도 하지 않는다. 고친 사람이 저장소 밖에서 따로 돌려 왔다.
- 이번에 CI가 매 push마다 서버 코드 타입을 검사하고, "AI 답을 읽는 규칙"과 "날씨 묶는 규칙" 시험을 돌리게 한다.
- 하고 나면 이 규칙을 잘못 고쳤을 때 배포 전에 CI가 빨간색이 된다. 앱 화면·서버 동작은 바뀌지 않는다.

### 현재 상태 (코드 확인)
- 서버 함수 3개, 파일 12개(`.ts` 11개·`.json` 1개), 1,164줄(`wc -l supabase/functions/*/*`). 시험 파일은 없다(`find supabase/functions -name "*test*"` 0개).
- 타입 검사는 `src`만 본다(`tsconfig.json` `"include": ["src", "env.d.ts"]`). 코드 검사(oxlint)는 저장소 전체라 이미 받는다.
- 저장소 밖 시험은 이 컴퓨터에 남아 있지 않다(임시 폴더에서 서버 함수를 불러 쓰는 파일 검색 0개). `docs/unbiseo.md` 7절에 "터미널 AI가 저장소 밖에서 돌린다"고만 적혀 있다.
- 세 `index.ts`는 맨 끝에서 `Deno.serve(handleRequest)`로 서버를 띄운다(`unbiseo-parse/index.ts:91` 등). 그래서 시험이 `index.ts`를 불러오면 서버가 같이 뜬다.
- 시험할 규칙이 있는 파일(서버 시동 줄 없음):
  - `unbiseo-parse/answer.ts` `readAiAnswer`: 종류별 읽기, 앞날은 오늘로, 운임은 0 이상 정수만, 장소 100자, 거래처는 목록 이름과 같을 때만 그 이름, "문자"·"점검"·"휴무/쉬"가 없는 말은 `unknown`, 이름 없는 거래처 등록은 `unknown`, 모양이 틀린 답은 `null`.
  - `unbiseo-parse/grok.ts` `dateTable`·`monthTable`: 날짜 표·달 표.
  - `unbiseo-weather/kma.ts` `kstDate`·`baseOf`·`summarizeDay`: 한국 날짜, 02:15 기준 발표 고르기, 하늘·비눈·최저·최고·강수 확률 묶기.
- 정한 동작의 정본은 `docs/sot.md` §4-16이다. 시험의 기대값은 지금 코드에서 가져오되, sot와 다르면 멈춘다.

### 목표 상태 (기대 동작)
1. CI `verify`에 Deno를 받는 단계와 두 단계가 더해진다. 하나가 실패해도 나머지는 도는 기존 방식(`if: ${{ !cancelled() }}`)을 따른다.
   - 서버 함수 타입 검사: `deno check`로 세 `index.ts`(불러오는 파일 전부 포함)를 검사한다.
   - 서버 함수 시험: `deno test supabase/functions/tests/`.
   - 판 번호는 고정한다. Deno `v2.9.7`(2026-09-17 출시), `denoland/setup-deno@v2.0.5`(2026-07-03 출시). 둘 다 2주 넘게 지난 판이다(2026-11-02 GitHub 출시 목록 확인).
2. `answer.ts` 시험이 위 "현재 상태"의 규칙을 종류마다 하나 이상 확인한다. 10종류(`call`·`expense`·`fixed`·`client`·`question`·`sms`·`weather`·`inspection`·`dayOff`·`unknown`)와 모양이 틀린 답(JSON 아님·배열·모르는 종류) 모두 들어간다.
   - 날짜: 내일 날짜·없는 날짜(`2026-02-30`)는 오늘로, 지난 날짜는 그대로. `dayOff`만 앞날 그대로.
   - 지출: 모르는 지출 종류는 정비로, 목록 밖 분류·결제는 빈 값, 주유가 아니면 주유 종류는 빈 값.
3. `dateTable`·`monthTable` 시험: 오늘이 월요일·일요일일 때 이번 주(월~일)·지난 ○요일, 3월 1일의 어제(윤년·평년), 1월의 지난달(작년 12월).
4. `kma.ts` 시험: 한국 시각으로 날짜가 넘어가는 때(UTC 15:00), 02:14는 전날 23시·02:15는 그날 02시, 기온 줄 없으면 `null`, -999 같은 값은 버림, 하늘 코드는 가장 많은 것(같으면 더 흐린 쪽), 최저·최고가 없으면 시간별 기온에서, 강수 확률은 가장 높은 값.
5. 시험 확인 하나하나에 쉬운 한국어 이름을 붙인다. 예: `'운행: 내일 날짜는 오늘로'`. CI 기록에 이름이 줄마다 남는다.
6. 깨는 실험 두 개가 빨간색이 되고, 되돌리면 초록이다.
   - 시험: `answer.ts`의 앞날을 오늘로 바꾸는 줄을 잠깐 지우면 시험 단계가 빨간색이고 깨진 확인 이름이 보인다.
   - 타입: 서버 함수 한 곳에 타입 틀린 줄을 잠깐 넣으면 타입 검사 단계가 빨간색이다.

### 건드릴 파일 (react-app)
- `.github/workflows/ci.yml`: `verify`에 Deno 받기 + 두 단계를 코드 검사 단계 다음에 더한다. 단계 이름은 "서버 함수 타입 검사 (deno check)", "서버 함수 시험 (deno test)".
- `supabase/functions/tests/unbiseo-parse.test.ts`(새 파일): 목표 2·3.
- `supabase/functions/tests/unbiseo-weather.test.ts`(새 파일): 목표 4.
- 시험은 Deno에 들어 있는 `node:assert/strict`와 `Deno.test`만 쓴다. 바깥 시험 도구를 새로 들이지 않는다. 파일 이름 `*.test.ts`는 oxlint 설정의 시험 파일 규칙(`.oxlintrc.json` overrides)에 맞춘 것이다.
- `deno.json`·잠금 파일은 만들지 않는다. 필요해지면 멈추고 묻는다.
- 새 파일 머리 주석 최종 문구:
  - `// 운비서 말 읽기 서버 시험: AI 답 읽기 규칙과 날짜·달 표. CI에서 deno test로 돈다.`
  - `// 운비서 날씨 서버 시험: 한국 날짜, 발표 시각 고르기, 그 날 예보 묶기. CI에서 deno test로 돈다.`
- 공용 영향: CI `verify` 작업 하나뿐이다. `tests` 폴더는 함수 폴더 밖이라 관리 화면 편집기 배포(`docs/unbiseo.md` 6절)에 들어가지 않는다.
- 문서(데스크톱 AI, `[x]` 때): `docs/unbiseo.md` 7절 첫 줄을 "서버 함수 시험은 `supabase/functions/tests/`에 있고 CI가 돌린다"로 고친다.

### 안 건드릴 것
- 서버 함수 코드 전부(시험만 더함). 그래서 관리 화면 편집기 반영·배포는 없다.
- 세 `index.ts`의 받기·로그인·지역 찾기(`readInput`·`gridOf`·`handleRequest`) 시험: 불러오면 서버가 뜨는 맨 끝 줄을 바꿔야 해서 서버 코드 변경과 배포가 붙는다. 이번 밖이다.
- `grok.ts`의 AI 부르기(`askAi`), `kma.ts`의 기상청 부르기(`kmaDay`), `unbiseo-voice`: 바깥 회사를 부르는 부분이라 이번 밖이다.
- `tsconfig.json`, `package.json`, `src` 앱 코드, `db-test` 작업, 자동 배포.

### 검증
- 저장소 밖 시험 문장은 찾지 못했으므로 목표 목록대로 새로 쓴다.
- 로컬 확인: Deno가 이 컴퓨터에 없어서, Node(24, 타입 표기를 지우고 바로 돌림)에 `Deno.test`를 Node 시험으로 잇는 임시 파일(저장소 밖 임시 폴더)로 시험을 먼저 돌려 본다. 실제 판정은 CI의 Deno다.
- 작업 가지(예: `fn-ci`)에 push해 CI를 돌린다(121 때와 같은 방식, push 전에 보리 확인). 확인할 것:
  - 두 새 단계가 초록이고, 시험 기록에 확인 이름이 줄마다 찍히는지.
  - 목표 6 깨는 실험 두 개가 빨간색인지. 그 뒤 되돌려 초록인지.
- 기존 다섯 단계와 `db-test`가 그대로 초록인지.
- main에는 코드 커밋 하나로 합친다. main push는 그때 다시 묻는다.

### 파일 길이
- `ci.yml`: 114줄 → 약 128줄.
- 시험 파일 두 개: 200줄 예외(AGENTS §6). 각각 약 150줄·약 80줄로 본다.

### 실패 시 처리
- 지금 코드가 `deno check`에 걸리면 고치지 않고 멈춘다. 걸린 곳과 수정안을 적어 다시 승인받는다(서버 코드를 고치면 배포가 붙기 때문).
- 시험 기대값이 sot §4-16과 다르면 멈추고 묻는다. 시험을 통과시키려고 확인을 빼거나 약하게 쓰지 않는다.
- CI에서 Deno를 못 받으면 멈추고 보고한다. 새 저장 장치·재시도·대비 장치는 만들지 않는다.

### 사용자 확인 방법
- GitHub CI의 `verify` 기록을 연다. "서버 함수 타입 검사", "서버 함수 시험" 두 단계가 초록이면 성공이다.
- "서버 함수 시험"을 펼치면 "운행: 내일 날짜는 오늘로 ... ok" 같은 줄이 이어지고 끝에 "ok | N passed | 0 failed"가 보인다.

### 진행 기록
- 작업 방식: 터미널 AI에 넘기지 않고 AI가 직접 작업한다 `[확인: 2026-11-02 보리 — "너가 작업해"]`.
- 작업 가지 `fn-ci`, 커밋 `6ae5757`(push 전).
- 바뀐 파일은 "건드릴 파일" 3개와 같다. 서버 함수 코드는 그대로다.
- 시험 47개: 말 읽기 36개, 날씨 11개. 로컬 Node 임시 연결로 모두 통과했다.
- 로컬 깨는 실험(복사본에서, 저장소는 그대로): 다섯 개 모두 해당 확인이 실패했다.
  - 앞날을 오늘로 바꾸는 규칙 지움 → 4개 실패(운행·지출·고정·점검표 앞날).
  - "문자" 막기 지움 → 1개 실패.
  - 하늘 코드 같으면 흐린 쪽 → 뒤집음 → 1개 실패.
  - 02:15 경계를 02:16으로 → 1개 실패.
  - 주 시작 요일 바꿈 → 날짜 표 2개 실패.
- 지시서와 다른 점: 두 Deno 단계에 `--no-config`와 `DENO_NO_PACKAGE_JSON=1`을 붙였다. Deno가 react-app의 `package.json`·`tsconfig.json`(앱용 설정)을 찾아 쓰지 않게 하려는 것이다. 새 설정 파일은 만들지 않았다.
- `ci.yml`: 114 → 133줄. 시험 파일: 219줄·63줄(200줄 예외).
- 코드 검사(oxlint) 로컬 경고 0.
- `fn-ci` push는 보리 확인을 받고 했다 `[확인: 2026-11-02 보리 — "푸시해"]`.
- CI 실행(fn-ci):
  - 41387257878(`6ae5757`): 초록. 두 Deno 단계 성공, 확인 이름 줄 47개, "ok | 47 passed | 0 failed". `db-test`도 초록.
  - 41387503576(`8222dd6`, 깨는 실험): 빨강. 앞날을 오늘로 바꾸는 규칙을 지웠고, `unbiseo-voice/index.ts`에 타입 틀린 줄을 넣었다. 타입 검사는 TS2322로 실패했고, 시험은 "43 passed | 4 failed"였다. 깨진 확인 이름 4개(운행·지출 "내일 날짜는 오늘로", 고정, 점검표)가 찍혔다. 기존 다섯 단계와 `db-test`는 초록이었다.
  - 41387856866(`b880727`, 되돌림): 초록. 47개 통과. `6ae5757`과 내용 차이 0줄.
- main 코드 커밋: `6ae5757`(fn-ci 첫 커밋 그대로, main 바로 위라 앞으로 옮기기만 함). main push는 보리가 했다.
- push 뒤 CI(run 41388564767): verify·db-test·deploy 모두 초록이다.
- §5 리뷰 점검 결과는 다음과 같다.
  - 바뀐 파일은 "건드릴 파일" 3개와 같다(`git show --stat 6ae5757`). 서버 함수 코드는 그대로다.
  - 새 파일은 시험 2개뿐이고 새 저장 장치·설정 파일은 없다.
  - 타입 꼼수 없음(`any`·`@ts-`·`as` 단언 0곳, "unknown"은 종류 이름 문자열 5곳뿐).
  - 머리 주석은 지시서 최종 문구와 같다. 로드맵 번호·날짜가 없다.
  - 목표 1~6을 모두 채웠다. 지시서와 다른 점은 위 Deno 옵션 두 개뿐이다.
- 최종 승인을 받았다 `[확인: 2026-11-02 보리 — "승인"]`. `docs/unbiseo.md` 7절 첫 줄을 고쳤다.
