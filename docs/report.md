# docs/report.md — 현재 슬라이스 착수지시서

124-A: drivelog-docs `022a710`. 121-B: drivelog-docs `0aa4357`. 긴 주석 줄이기: drivelog-docs `59ddc9d`. 121-A: drivelog-docs `1590ef7`. 120-D: drivelog-docs `2ce5170`. 120-C: drivelog-docs `ae914c9`. 120-B: drivelog-docs `503a3d3`. 120-A: drivelog-docs `81f927a`. 119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 124-B-1. 운비서 받기 코드를 떼어 시험하기 `[x]`

- 근거: 로드맵 124-B `[확인: 2026-11-02 보리 — "124 비 먼저해"]`. 124-A(react-app `6ae5757`)로 CI가 Deno 시험을 돌린다. 이번엔 그때 뺀 받기 파일을 시험할 수 있게 한다.
- 124-B는 둘로 나눈다. 이번엔 B-1만 한다. 확인할 점 1은 권장대로 나누고, 지시서를 확정했다. AI가 직접 작업한다 `[확인: 2026-11-02 보리 — "응"]`.
  - B-1: 떼어 내기 + 입력 읽기·지역 찾기 시험. 서버 배포가 붙는다.
  - B-2: 요청 흐름(로그인 확인 → 입력 → AI·기상청 → 답) 시험. 바깥 호출은 가짜로 바꾼다. 시험 파일과 CI만 바뀌고 배포는 없다.

### 쉬운 요약
- 운비서 서버 세 개의 받기 파일은 맨 끝 줄이 서버를 켠다. 그래서 시험이 이 파일을 불러오면 서버까지 같이 켜져 시험할 수 없었다.
- 받기 코드를 새 파일 `handler.ts`로 그대로 옮긴다. `index.ts`에는 "서버 켜기" 한 줄만 남긴다. 그러면 시험이 `handler.ts`만 불러 쓸 수 있다.
- 동작은 하나도 안 바뀐다. 다만 관리 화면 편집기에 세 함수를 다시 올려 배포해야 한다.

### 현재 상태 (코드 확인)
- 세 `index.ts`가 마지막 줄에서 `Deno.serve(handleRequest)`로 서버를 켠다(`unbiseo-parse/index.ts:91`, `unbiseo-voice/index.ts:96`, `unbiseo-weather/index.ts:143`).
- 시험할 수 없던 함수(모두 `index.ts` 안):
  - `unbiseo-parse`: `readInput`. 글 1~300자(앞뒤 공백 뗌), 오늘 날짜 모양, 거래처 목록 200개·이름 50자까지, 노선 목록 모양.
  - `unbiseo-voice`: `readText`. 글 1~300자.
  - `unbiseo-weather`: `readInput`(날짜 + 지역 이름 30자 또는 격자 번호, 둘 다는 안 됨, 격자 1~149·1~253). `gridOf`(정식·줄인 이름, 시·도+구, 한 시의 일반구는 첫 구, 다른 곳 둘 이상은 못 찾음).
- 격자 표는 272줄이다. 같은 이름 예: "중구"는 5개 시·도에 있고, "수원"은 일반구 4개(`kmaGrid.json` 확인).
- 배포 절차는 `docs/unbiseo.md` 6절이다(편집기에 파일 넣기 → 지문 대조 → 보리 [Deploy updates] → 배포본 지문 → Verify JWT 꺼짐 → 문장 시험).

### 목표 상태 (기대 동작)
1. 세 함수마다 `index.ts`의 내용을 마지막 줄만 빼고 그대로 새 `handler.ts`로 옮긴다(`git mv`로 기록 이어 감). 고치는 글자는 없다. 옛 머리 주석도 그대로 옮긴다.
2. 새 `index.ts`는 아래 세 줄이다(최종 문구).
   ```ts
   // 서버 켜기만 — 받기·로그인·답은 handler.ts(시험이 서버를 켜지 않고 불러 쓰게).
   import { handleRequest } from './handler.ts'
   Deno.serve(handleRequest)
   ```
3. `ci.yml`의 타입 검사 대상은 그대로 세 `index.ts`다. `handler.ts`는 거기서 불러오므로 같이 검사된다. 시험은 같은 `deno test`가 돈다.
4. 시험을 더한다.
   - 말 읽기 `readInput`: 글 앞뒤 공백 뗌, 빈 글·301자 거절, 없는 날짜 거절, 거래처 목록이 배열 아님·201개·51자 이름 거절, 노선 목록 모양 틀림 거절, 노선 칸 없음은 빈 목록.
   - 목소리 `readText`: 앞뒤 공백 뗌, 빈 글·301자·글자 아님 거절.
   - 날씨 `readInput`: 지역만·격자만은 됨. 둘 다·둘 다 없음·격자 0이나 150·지역 31자·없는 날짜는 거절.
   - 날씨 `gridOf`: "평택"(줄인 이름), "서울 강남구"(띄어 써도), "수원"(일반구 → 첫 구), "서울"(시·도만), "부산 중구"는 찾는다. "중구"(5곳)와 표에 없는 이름은 못 찾는다(`null`).
5. 배포본이 지금과 똑같이 동작한다. 아래 "검증"의 문장 시험으로 확인한다.

### 건드릴 파일 (react-app)
- `supabase/functions/unbiseo-parse/index.ts` → `handler.ts`로 옮김, 새 `index.ts` 3줄.
- `supabase/functions/unbiseo-voice/index.ts` → `handler.ts`로 옮김, 새 `index.ts` 3줄.
- `supabase/functions/unbiseo-weather/index.ts` → `handler.ts`로 옮김, 새 `index.ts` 3줄.
- `supabase/functions/tests/unbiseo-parse.test.ts`: `readInput` 시험을 더한다.
- `supabase/functions/tests/unbiseo-weather.test.ts`: `readInput`·`gridOf` 시험을 더한다.
- `supabase/functions/tests/unbiseo-voice.test.ts`(새 파일): `readText` 시험. 머리 주석은 `// 운비서 목소리 서버 시험: 앱이 보낸 문장 읽기. CI에서 deno test로 돈다.`
- 공용 영향: 앱(`src`)은 함수를 이름으로만 부르므로 그대로다(`supabase.functions.invoke('unbiseo-parse'·'unbiseo-voice'·'unbiseo-weather')` 3곳, `src/features/home/api/` — 2026-11-02 grep).
- 문서(AI, `[x]` 때): `docs/unbiseo.md` 2절 표의 파일 칸(`index.ts`(받기·로그인) → `index.ts`(서버 켜기)·`handler.ts`(받기·로그인)), 7절 시험 줄.

### 안 건드릴 것
- 옮기는 코드 안의 글자 하나도(옛 주석의 로드맵 번호 포함, 정리는 로드맵 149).
- 요청 흐름 시험, 가짜 바깥 호출, `--allow-env` 같은 시험 권한: B-2.
- `ci.yml`(이번엔 바꿀 것 없음), 앱 코드, DB, 비밀값, Verify JWT 설정.

### 검증
- 옮긴 뒤 `git diff -M` 으로 세 `handler.ts`가 옛 `index.ts`에서 마지막 줄 하나만 빠졌는지 본다.
- 로컬: Node 임시 연결로 새 시험을 먼저 돌린다. `handler.ts`가 `npm:@supabase/supabase-js`를 불러오므로, 임시 연결에서 그 이름을 react-app에 설치된 판으로 잇는다(저장소 밖 임시 폴더).
- 작업 가지에 push해 CI(두 Deno 단계)를 돌린다. push 전에 보리 확인을 받는다.
- 깨는 실험: `gridOf`의 "한 시의 일반구는 첫 구" 줄을 잠깐 지우면 "수원" 확인이 빨간색인지 본다. 그 뒤 되돌린다.
- main push(보리) → CI 초록 → 배포(`docs/unbiseo.md` 6절): AI가 편집기에 `handler.ts`를 더하고 `index.ts`를 바꾼다. 세 함수 모두 지문을 대조한다. 보리가 [Deploy updates]를 누른다. 배포본 지문과 Verify JWT 꺼짐을 다시 본다.
- 배포본 문장 시험(AI, 로그인된 탭): 말 읽기 "어제 수원 화성 15만" → 운행, 목소리 짧은 문장 → 소리 파일, 날씨 "오늘 평택 날씨 어때" → 날씨. 공개 키만으로는 세 함수 모두 401.

### 파일 길이
- `handler.ts`: 89·94·141줄(옛 `index.ts`보다 2줄 짧음). 새 `index.ts`: 3줄씩.
- 시험 파일은 200줄 예외다. 말 읽기 219 → 약 260줄, 날씨 63 → 약 110줄, 목소리 새로 약 30줄.

### 실패 시 처리
- 옮긴 뒤 타입 검사나 기존 시험이 깨지면 멈추고, 원인과 수정안을 적어 다시 승인받는다.
- 배포 뒤 문장 시험이 하나라도 다르면 곧바로 편집기에 옛 `index.ts`(react-app `6ae5757`)를 다시 넣어 되돌리자고 보리에게 말하고, 원인을 적어 다시 승인받는다.
- 새 저장 장치·재시도·대비 장치는 만들지 않는다.

### 사용자 확인 방법
- CI: `verify`의 "서버 함수 타입 검사"·"서버 함수 시험"이 초록이고, 시험 끝 줄의 통과 수가 47보다 늘었으면 성공이다.
- 배포: 관리 화면 Edge Functions에서 세 함수 모두 Code에 `handler.ts`가 보이고 [Deploy updates]를 누른 뒤 "방금"으로 바뀌면 된다.
- 휴대폰: 운비서에 "어제 수원 화성 15만"이라고 말해 운행 카드가 뜨고, 마이크로 물었을 때 소리로 읽어 주고, "오늘 평택 날씨 어때"에 날씨 답이 오면 성공이다.

### 확인할 점
1. 124-B를 B-1(떼어 내기 + 입력 시험, 배포 있음)과 B-2(요청 흐름 시험, 배포 없음)로 나눠도 될까(권장)? 한 번에 하면 파일이 10개 가까이 되고, 배포와 가짜 바깥 호출 문제가 한 슬라이스에 섞인다.

### 진행 기록
- 작업 가지 `fn-ci-b`, 커밋 `efeec0e`(push 전).
- 바뀐 파일은 "건드릴 파일" 9개(함수 6개·시험 3개)와 같다.
- 옮기기 확인: 옛 `index.ts`(HEAD)와 새 `handler.ts`를 비교했다. 세 함수 모두 마지막 빈 줄과 `Deno.serve(handleRequest)` 두 줄만 빠졌다. `index.ts`가 그대로 남아 git은 이름 바꿈으로 보지 않는다(기록은 이 비교로 대신함).
- 시험 20개를 더해 모두 67개다(말 읽기 받기 7, 날씨 받기 5·지역 찾기 5, 목소리 받기 3). 로컬 Node 임시 연결(`npm:` 이름을 react-app 설치본으로 이음)로 모두 통과했다.
- 로컬 깨는 실험(복사본): 네 개 모두 해당 확인이 실패했다.
  - 일반구 첫 구 규칙 지움 → "수원" 1개 실패.
  - 지역·격자 같이 막기 지움 → 1개 실패.
  - 글 300자를 400자로 → 말 읽기·목소리 2개 실패.
  - 거래처 200개 제한 풂 → 1개 실패.
- 코드 검사(oxlint) 경고 0. `ci.yml`은 바꾸지 않았다.
- 파일 길이: `handler.ts` 89·94·141줄, `index.ts` 3줄씩. 시험 파일 262·116·20줄(200줄 예외).
- `fn-ci-b` push는 보리가 했다.
- CI 실행(fn-ci-b):
  - 41390205619(`efeec0e`): 초록. 시험 67개 통과(말 읽기 43·목소리 3·날씨 21).
  - 41390436120(`64351c6`, 깨는 실험): 일반구 첫 구 규칙을 지웠더니 빨강이었다. "지역 찾기: 한 시의 일반구들은 첫 구"가 실패했다(받음 null, 기대 60·121). "66 passed | 1 failed"였고 `db-test`는 초록이었다.
  - 41390705631(`2040f0a`, 되돌림): 초록. `efeec0e`와 내용 차이 0줄.
- main 코드 커밋: `efeec0e`(main 바로 위라 앞으로 옮기기만 함). main push는 보리가 했다.
- push 뒤 CI(run 41391243727): verify·db-test·deploy 모두 초록이다.
- 편집기 배포(AI, 함수 하나씩 — 편집기 내용이 탭을 떠나면 사라져서):
  - 바꾸기 전 편집기 지문이 모두 옛 판 `6ae5757`과 같았다(parse 8개, voice 1개, weather 3개).
  - 새 파일 내용은 커밋 `efeec0e`의 raw 주소에서 받았고, 받은 지문이 커밋과 같았다.
  - parse에서 [Add File]이 두 번 눌려 빈 `file10.ts`가 생겼고, 배포 전에 지웠다. 이름 바꾸기는 한 번에 안 돼서 이름 칸을 두 번 눌러 바꿨다(6절에 적힌 증상).
  - 보리가 세 함수 차례로 [Deploy updates]를 눌렀다.
- 배포본 확인(2026-11-02):
  - 지문: parse 9개, voice 2개(`handler.ts` `6939977f`, `index.ts` `ac66534a`), weather 4개(`handler.ts` `da12c1b3`, `index.ts` `ac66534a`, `kma.ts`·`kmaGrid.json` 그대로) 모두 `efeec0e`와 같다. parse `handler.ts`는 `04f49dac`다.
  - Verify JWT with legacy secret: 세 함수 모두 꺼짐.
  - 로그인 없이 부르기: 세 함수 모두 401 "로그인이 필요합니다."(새 구조로 켜짐).
- 배포본 문장 시험(AI, getdrivelog.com 로그인된 탭, 오늘 2026-11-02):
  - parse "어제 수원 화성 15만" → 200, 운행, 2026-11-01, 수원 → 화성, 150000.
  - voice "시험 문장입니다." → 200, 소리 파일 6,624바이트.
  - weather 오늘·평택 → 200, 날씨(맑음, 9~16도, 강수 확률 30).
  - weather 지역·격자 같이 → 400 "요청 모양이 맞지 않습니다."
  - 공개 키만 → 세 함수 모두 401.
- §5 리뷰 점검 결과는 다음과 같다.
  - 바뀐 파일은 "건드릴 파일" 9개와 같다(`git show --stat efeec0e`).
  - 새 파일은 `handler.ts` 3개(옮긴 것)와 목소리 시험 1개뿐이다. 새 저장 장치·설정 파일은 없다.
  - 타입 꼼수 없음(새 줄에서 `any`·`unknown` 타입·`@ts-`·`as` 단언 0곳).
  - 새 주석 두 개(`index.ts` 세 곳 같은 문구, 목소리 시험 머리)는 지시서 최종 문구와 같다. 옮긴 코드의 옛 주석은 그대로다(로드맵 149).
  - 목표 1~5를 모두 채웠다.
- 최종 승인을 받았다 `[확인: 2026-11-02 보리 — "승인"]`. 휴대폰 확인 결과는 따로 듣지 않았다. `docs/unbiseo.md` 2절 파일 표와 7절 시험 줄을 고쳤다.
