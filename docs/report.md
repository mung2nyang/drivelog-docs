# docs/report.md — 현재 슬라이스 착수지시서

124-B-1: drivelog-docs `5c354fd`. 124-A: drivelog-docs `022a710`. 121-B: drivelog-docs `0aa4357`. 긴 주석 줄이기: drivelog-docs `59ddc9d`. 121-A: drivelog-docs `1590ef7`. 120-D: drivelog-docs `2ce5170`. 120-C: drivelog-docs `ae914c9`. 120-B: drivelog-docs `503a3d3`. 120-A: drivelog-docs `81f927a`. 119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 124-B-2 운비서 서버 함수 요청 흐름 시험 `[x]`

로드맵 124의 마지막 조각(`docs/roadmap.md` 124-B-2). 124-B-1에서 받기 코드를 `handler.ts`로 떼어 시험이 서버를 켜지 않고 `handleRequest`를 부를 수 있게 됐다. 보리가 착수지시서 쓰기를 확인했다(2026-11-02 "응").

### 쉬운 요약
- 운비서 서버에 요청이 들어와서 답이 나갈 때까지의 순서를 시험으로 확인한다. 순서는 로그인 확인, 입력 확인, AI·기상청 부르기, 답 만들기다.
- 진짜 AI·기상청·Supabase는 부르지 않는다. 시험 안에서 가짜가 대신 답한다. 그래서 돈이 들지 않고 인터넷도 쓰지 않는다.
- 끝나면 "로그인 안 한 사람 막기", "AI가 고장 나면 잠시 뒤 다시 시도 안내", "말한 문장을 기록에 안 남기기"가 바뀌면 CI가 빨갛게 된다. 서버 함수 코드는 그대로라서 배포는 없다.

### 현재 상태 (코드 확인)
- 세 `handler.ts`의 `handleRequest` 순서는 같다(`unbiseo-*/handler.ts` 끝 함수). OPTIONS면 'ok'를 돌려준다. POST가 아니면 405, 로그인이 안 되면 401, JSON이 틀리거나 입력이 틀리면 400이다. 그다음에 바깥을 부른다.
- 로그인 확인 `signedInUserId`가 읽는 것: `Authorization` 머리, `SUPABASE_URL`, `SUPABASE_ANON_KEY`(없으면 `SUPABASE_PUBLISHABLE_KEYS`). 이 값으로 `createClient(...).auth.getUser(token)`을 부르고, 이것이 `${SUPABASE_URL}/auth/v1/user`를 GET으로 부른다(`@supabase/auth-js` `GoTrueClient.js` 2679행).
- 라이브러리는 fetch를 부르는 순간에 전역 `fetch`를 찾는다(`auth-js/lib/helpers.js` 86~91행 `resolveFetch`). 그래서 시험에서 `globalThis.fetch`를 바꾸면 로그인 확인도 가짜로 간다.
- parse: `askAi`(`grok.ts` 136~176행)가 `XAI_API_KEY`로 `https://api.x.ai/v1/chat/completions`를 부른다. 키가 없거나 요청이 실패하거나 응답 상태가 나쁘면 503이다. 답을 못 읽거나 finish_reason이 'length'면 502다.
- voice: `askVoice`가 `https://api.x.ai/v1/tts`를 부른다. 키가 없거나, 오류가 나거나, 빈 소리면 503이다. 성공하면 200 `application/octet-stream`이다.
- weather: 지난 날짜면 `past`, 모레보다 뒤면 `tooFar`, 지역을 못 찾으면 `noPlace`다(모두 200). 그 밖에는 `kmaDay`(`kma.ts` 75~101행)가 `KMA_API_KEY`로 기상청을 부른다. 실패하면 503이다.
- 지금 시험 67개는 입력 읽기와 지역 찾기만 본다. `handleRequest`를 부르는 시험은 없다(`grep -n handleRequest supabase/functions/tests/` 결과 0줄).
- CI는 `deno test --no-config supabase/functions/tests/`이고 권한을 주지 않는다(`ci.yml` 75행). `Deno.env`를 읽으면 권한 오류가 난다.

### 목표 상태 (기대 동작)
1. 공통(세 함수 각각):
   - OPTIONS는 'ok'와 CORS 머리를 돌려준다.
   - GET은 405다.
   - 로그인 머리가 없으면 401이고, 바깥은 하나도 부르지 않는다.
   - 가짜 Supabase가 토큰을 거절하면 401이고, AI·기상청은 부르지 않는다.
   - `SUPABASE_URL`이 없으면 401이다.
   - JSON이 깨졌거나 모양이 틀리면 400이고, AI·기상청은 부르지 않는다.
2. parse:
   - 가짜 AI가 운행 답을 주면 200과 운행 칸이 나온다.
   - AI 요청은 chat completions 주소로 가고, 시험 키를 Bearer로 싣고, 몸에 말한 문장이 들어간다.
   - finish_reason이 'length'이거나 JSON이 아닌 답이면 502다.
   - AI가 500을 주거나, fetch가 실패하거나, 키가 없으면 503이다. 키가 없으면 AI를 부르지 않는다.
3. voice:
   - 가짜 소리 바이트를 받으면 200, octet-stream, 같은 바이트를 돌려준다.
   - AI가 500을 주거나, 빈 소리이거나, 키가 없으면 503이다.
4. weather:
   - 어제 날짜는 `past`다.
   - 3일 뒤는 `tooFar`다.
   - '없는동네'는 `noPlace`다. 이 세 경우는 기상청을 부르지 않는다.
   - 내일·'평택'은 기상청에 nx=62, ny=114, 시험 키로 요청하고, 200 `weather` 요약이 나온다.
   - 기상청 resultCode가 '00'이 아니거나, 500이거나, 키가 없으면 503이다.
5. 기록 지키기: 위 흐름에서 `console.log`·`console.error`로 나간 글에 말한 문장, 지역 이름, 시험 키가 들어 있지 않다.
6. 가짜가 모르는 주소가 불리면 시험이 실패한다(부른 주소 목록을 시험마다 그대로 대조한다). 인터넷 권한(`--allow-net`)은 주지 않는다.
7. 기존 시험 67개는 그대로 통과한다. `deno check`도 그대로 통과한다.
8. 서버 함수 코드(`unbiseo-*/` 안)는 한 글자도 안 바뀐다. 배포는 없고 지문도 그대로다.

### 건드릴 파일
- `react-app/supabase/functions/tests/fakes.ts`(새로 만듦) — 시험 도우미. `.test.ts`가 아니라서 deno test가 따로 돌리지 않는다.
  - 환경 값을 넣고 지운다.
  - `globalThis.fetch`를 주소별 가짜로 바꾸고, 부른 주소를 적어 둔다.
  - console 출력을 모은다.
  - 시험이 끝나면 모두 되돌린다.
  - 머리 주석: `// 서버 함수 시험용 가짜 바깥: 환경 값, 주소별 fetch 답, console 모으기. 시험마다 되돌린다.`
- `react-app/supabase/functions/tests/unbiseo-flow.test.ts`(새로 만듦) — 목표 1~6. 공통 시험은 세 `handleRequest`를 차례로 돌린다.
  - 머리 주석: `// 운비서 서버 요청 흐름 시험: 로그인 확인 → 입력 → AI·기상청 → 답. 바깥은 fakes.ts 가짜, CI에서 deno test로 돈다.`
- `react-app/.github/workflows/ci.yml` — 75행을 `deno test --no-config --allow-env supabase/functions/tests/`로 바꾼다. 71행 위에 주석 1줄을 더한다.
  - 주석: `# 시험은 환경 값만 허용한다. 바깥 호출은 가짜라 인터넷 권한은 주지 않는다.`

### 안 건드릴 것
- `supabase/functions/unbiseo-*/` 안의 모든 파일. 이번은 시험만이다(목표 8). 배포·지문 대조도 하지 않는다.
- 기존 시험 세 파일(`unbiseo-parse.test.ts`·`unbiseo-weather.test.ts`·`unbiseo-voice.test.ts`).
- 앱 코드(`src/`), `deno check` 단계, 다른 CI 일(db-test·deploy).
- 저장소 밖 시험 장치(로컬 Node 임시 연결)는 scratchpad에만 두고 저장소에 넣지 않는다.

### 검증
- 로컬: scratchpad의 Node 임시 연결(`denoshim/run.mjs`)에 `Deno.env.set·delete`를 더한다(저장소 밖). 이것으로 새 시험과 기존 67개를 돌린다. `npm run lint`도 돌린다(oxlint는 저장소 전체를 본다).
- 작업 가지 `fn-ci-c`에서 CI를 돌린다(push 전 보리 확인). `deno check`와 `deno test`가 초록이어야 하고, 시험 수가 67보다 늘어야 한다.
- 깨는 실험: 작업 가지에만 임시 커밋을 올렸다가 되돌린다.
  - parse의 로그인 확인 줄을 지운다. 목표 1의 401 시험이 빨개져야 한다.
  - voice 기록에 문장을 넣는다. 목표 5 시험이 빨개져야 한다.
  - CI가 빨개지는 것을 확인한 뒤 되돌리고 다시 초록인지 본다.
- main: 코드 커밋 1개 → 보리 push → CI 초록 → §5 리뷰.

### 파일 길이
- `tests/fakes.ts`: 0 → 약 60줄.
- `tests/unbiseo-flow.test.ts`: 0 → 약 210줄. 시험 파일이라 §6 200줄 예외다(AGENTS.md 127행). 넘더라도 나누지 않는다.
- `.github/workflows/ci.yml`: 133 → 134줄.

### 실패 시 처리
- 새 재시도·대비 장치는 만들지 않는다. 서버 함수 코드도 고치지 않는다.
- 미리 정해 둘 예외가 하나 있다. CI에서 Deno 시험 누수 검사(sanitizer)가 실패할 수 있다. 원인이 `AbortSignal.timeout` 타이머나 supabase 라이브러리 안쪽이면, 흐름 시험에만 `sanitizeOps: false`·`sanitizeResources: false`를 단다. 이 경우 진행 기록에 적는다.
- 그 밖의 실패(시험이 기대와 다른 응답을 받는 경우 등)는 바로 고치지 않는다. 원인과 수정 계획을 이 파일에 수정 지시서로 써서 다시 확정받는다.

### 사용자 확인 방법
- GitHub 저장소 Actions에서 main 커밋의 CI를 연다. verify 안 "서버 함수 시험 (deno test)" 단계가 초록이고, 끝줄 통과 수가 67보다 크면 성공이다.
- 화면(앱)은 바뀌는 것이 없다.

### 확인할 점
- 날씨 시험은 실제 지금 시각으로 "어제·내일·3일 뒤"를 계산한다. 한국 자정 바로 그 순간에 CI가 돌면 드물게 날짜가 어긋날 수 있다. 시각을 가짜로 바꾸면 서버 함수 코드를 고쳐야 해서 그대로 둔다.

### 진행 기록
- 2026-11-02 보리 확정("응"). 작업 가지 `fn-ci-c` 커밋 `03fed0e`. 건드린 파일은 지시서와 같은 3개다(`fakes.ts` 66줄, `unbiseo-flow.test.ts` 195줄, `ci.yml` 134줄).
- 로컬(Node 임시 연결): 시험 97개(기존 67개 + 새 30개) 모두 통과했고 `npm run lint`도 통과했다.
- 로컬 깨는 실험을 했다. parse의 로그인 확인 줄을 지우고 voice 기록에 문장을 넣었다. 그러자 8개가 실패했다(parse 공통 401·400과 흐름, 기록 시험). 실험 뒤 원래대로 되돌렸고, `git status`에서 handler 변경이 없음을 확인했다.
- `fn-ci-c` `03fed0e` CI 41394212730 초록이다. Deno 시험 97 passed / 0 failed가 나왔다. 누수 검사도 통과해서 sanitizer 예외는 쓰지 않았다.
- 깨는 실험 커밋 `ae00bec`(되돌릴 것): parse 로그인 확인 줄을 지우고 voice 기록에 문장을 넣었다.
- 깨는 실험 CI 41394641311 빨강이다(89 passed / 8 failed, 로컬 실험과 같은 8개). 되돌리기 커밋 `8f07f9c`(`03fed0e`와 내용이 같다)를 했다.
- 되돌린 `8f07f9c` CI 41395037328 초록이다(97 passed). main 코드 커밋 `2d0d20d`(`03fed0e`를 그대로 옮겼다). 보리 push를 기다린다.
- main `2d0d20d` push 뒤 CI 41396088399 초록이다(verify·db-test·deploy 성공, 97 passed). §5 점검: 건드린 파일 3개가 지시서와 같고, 서버 함수 코드는 바뀌지 않았다. 타입 꼼수가 없고, 주석 2개가 지시서 문구와 같다.
- 2026-11-02 보리 최종 승인("승인").
