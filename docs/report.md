# docs/report.md — 현재 슬라이스 착수지시서

24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 24-E2 운비서 답을 xAI 목소리(Iris)로 `[x]` `[확인: 2026-10-23 보리 — "이건 승인"]`

보리 지시(2026-10-23): "Iris 보이스로 일단 해 보고, 사용자가 없는 지금 내가 테스트하며 비용이 얼마나 나가는지 확인해 보고 싶은데" `[확인: 2026-10-23 보리]`. 24-E(휴대폰 기본 목소리)의 목소리만 xAI 음성(Text-to-speech, 목소리 Iris)으로 바꿈. 언제 읽는지(마이크로 말했을 때만)·[멈추기]는 24-E 그대로.

### 확인할 점 (하나) — xAI 목소리를 못 받으면
- 권함: **휴대폰 기본 목소리로 대신 읽음**(지금 24-E 그대로 — 운전 중 소리가 아예 안 나는 것보다 나음, 새 저장·재시도 없음). 다른 안: 소리 없이 화면 답만.

### 쉬운 요약
- 마이크로 물으면 답을 xAI의 Iris 목소리로 읽어 줍니다. 서버 함수가 답 문장을 xAI에 보내 소리 파일을 받아 오고, 휴대폰이 그 소리를 틉니다.
- 요금: xAI 공식 가격 **글자 100만 개당 $4.20**(베타 — 바뀔 수 있음). 답 한 번이 30자 안팎이라 **1,000번 읽어도 약 $0.13(약 180원)**. 보리가 시험하며 xAI 콘솔 사용량과 함수 기록(읽은 글자 수)으로 실제 비용을 봄.

### 현재 상태 (코드 확인)
- 24-E: `features/home/hooks/useSpeechOutput.ts`(46줄)가 휴대폰 `speechSynthesis`로 `speak(문장)`·`stop()`. `useUnbiseoAssistant.ts`(105줄)가 마이크로 보낸 답이면 `voice.speak(sentence)`.
- 서버 함수는 `unbiseo-parse` 하나(`supabase/functions/unbiseo-parse/`), 앱은 `supabase.functions.invoke`로 부름(`unbiseoParse.ts:180`). 키 `XAI_API_KEY`는 함수 비밀값에 있음.
- xAI 음성 API(공식 문서 2026-10-23): `POST https://api.x.ai/v1/tts`, 칸 `text`(필수)·`language`(`ko`)·`voice_id`(대소문자 무관 — Iris는 보리 화면의 Voice library "Built-in, Female, Multilingual"; 착수 때 `GET /v1/tts/voices`로 id 확인)·`output_format`(mp3 등), 답은 소리 파일 그대로. 베타 한도 분당 600번·동시 10번.

### 목표 상태 (기대 동작)
1. 새 서버 함수 `unbiseo-voice`: 로그인 계정만(운비서와 같은 확인 — "Verify JWT" + 함수 안 `auth.getUser`), 받는 것 `text`(1~300자), xAI 음성에 `voice_id` Iris·`language` `ko`·mp3(작은 크기 — 낮은 비트레이트)로 요청, 받은 소리를 그대로 돌려줌(`audio/mpeg`). 15초 넘거나 xAI 오류면 503. 재시도 없음.
2. 함수 기록(Logs)에는 **읽은 글자 수·걸린 시간·성공 여부만**(문장 내용은 안 남김) — 보리가 비용을 셀 수 있게.
3. 앱: 마이크로 보낸 답이면 먼저 `unbiseo-voice`로 소리를 받아 틀고, 못 받으면(오류·5초 넘음·재생 실패) 확인할 점대로 휴대폰 기본 목소리로. [멈추기]·듣기 시작·새로 보내기면 소리 멈춤(24-E와 같음). 받은 소리는 그 자리에서만 쓰고 저장 안 함.
4. 글로 친 답은 지금처럼 안 읽음. 비회원·운행·지출·질문 흐름 그대로.

### 건드릴 파일
- 새 `react-app/supabase/functions/unbiseo-voice/index.ts`(약 80줄) — 로그인 확인·입력 검사·xAI 음성 부르기·기록. (`unbiseo-parse`의 로그인 확인과 같은 방식 — 두 함수가 파일을 나눠 쓸 수 없어 같은 몇 줄을 따로 둠.)
- 새 `src/features/home/api/unbiseoVoice.ts`(약 40줄) — 함수 부르기(소리 받기, 5초 넘으면 포기).
- `src/features/home/hooks/useSpeechOutput.ts` — 받은 소리 틀기(`Audio`)·멈추기, 못 받으면 휴대폰 목소리(46 → 약 80).
- 테스트: 새 `unbiseoVoice.test.js`(부르는 값·실패), `useSpeechOutput.test.js`(xAI 소리 틀기·실패면 휴대폰 목소리·멈추기), `HomeAssistantPanel.test.js` 맞춤.
- 서버(Supabase 관리 화면): 새 함수 만들기 — 데스크톱 AI가 편집기에 넣고 지문 확인, **배포 버튼은 보리**. 키는 이미 있음.
- 문서(`[x]` 때): sot §4-16 소리 줄(xAI Iris, 실패면 휴대폰 목소리), 로드맵 24-E2, 10-P 자문 목록 "AI 전달"에 "답 문장도 소리 변환을 위해 xAI로 보냄".

### 안 건드릴 것
- `unbiseo-parse` 함수, 답 문장 만드는 곳, `useUnbiseoAssistant.ts`의 언제 읽는지 판단(24-E 그대로 — `voice.speak` 부르는 곳 그대로), 화면 모양.

### 검증
- 테스트 위. 기존 테스트 통과·경고 늘지 않음. 타입·빌드·의존 방향 CI.
- 진짜 서버(보리 배포 뒤): 공개 키만 401, 보리 계정으로 "10월 매출은 3,303,011원입니다." 보내 소리 파일(mp3)이 오는지·크기·걸린 시간, 함수 기록에 글자 수만 있는지. 휴대폰(보리): 마이크로 물으면 Iris 목소리, [멈추기], 비행기 모드 등으로 실패시키면 휴대폰 목소리.
- 비용: 보리가 며칠 써 본 뒤 xAI 콘솔 사용량 + 함수 기록 글자 수 합 × $4.20/100만으로 계산.

### 파일 길이
- 새 함수 약 80, 새 `unbiseoVoice.ts` 약 40, `useSpeechOutput.ts` 46 → 약 80. 200 이하.

### 코드에 넣을 주석(최종 문구)
- 함수 머리: `// 운비서 답 문장을 xAI 목소리(Iris)로 — 로그인한 사람만, 문장 내용은 기록에 안 남기고 글자 수만(로드맵 24-E2).`
- `useSpeechOutput.ts`: `// xAI 목소리를 먼저, 못 받으면 휴대폰 기본 목소리로 읽는다.`

### 실패 시 처리
- 새 저장 장치·재시도 없음(AGENTS §7) — 실패는 휴대폰 목소리로 대신(확인할 점). 비용이 생각보다 크면 24-E(휴대폰 목소리)로 되돌리는 수정 착수지시서.

### 진행 기록
- 2026-10-23 AI: react-app `f05f477`(6개 파일, push 안 함). 확인할 점은 권함대로(못 받으면 휴대폰 기본 목소리).
- 줄 수: 새 함수 `unbiseo-voice/index.ts` 96(약 80 예상), 새 `unbiseoVoice.ts` 18, `useSpeechOutput.ts` 46 → 87. 함수 SHA-256(LF) `fdf25e48…`.
- 교차검증·지시서와 다른 점:
  - xAI 공식 문서(2026-10-23) 확인: `POST /v1/tts`, `text`·`voice_id`(대소문자 무관)·`language`(`ko` 있음)·`output_format { codec, sample_rate, bit_rate }`. 키가 함수 비밀값에만 있어 `GET /v1/tts/voices`로 Iris id를 직접 확인하지 못함 → `voice_id: 'iris'`로 넣음. 틀리면 xAI가 오류를 내고 앱은 휴대폰 목소리로 읽으니, 진짜 서버 시험에서 소리가 오는지로 확인.
  - 소리 크기: mp3·24kHz·32kbps(문서가 허용하는 가장 낮은 비트레이트).
  - **돌려주는 형식은 `audio/mpeg` 대신 `application/octet-stream`** — 앱의 `supabase.functions.invoke`는 `audio/mpeg`를 글자로 읽어 소리가 깨짐(설치된 `@supabase/functions-js` 코드 확인). 앱이 받은 것을 `audio/mpeg`로 다시 감쌈.
  - 함수 기록: `{ chars, ms, ok, bytes }`(소리 크기도 같이 — 비용·속도 볼 때). xAI 실패는 상태 번호·시간 넘김만.
  - 앱 5초 포기는 `invoke`의 `timeout` 칸으로.
  - 소리를 받는 중에 [멈추기]·새로 읽기가 오면 늦게 온 소리는 틀지 않음. 재생 오류와 재생 거절이 둘 다 와도 휴대폰 목소리는 한 번만.
  - 기존 패널 시험 1개(받아쓰기 → 보냄 횟수)는 목소리 부르기도 같은 가짜 서버를 거쳐 세어지므로 운비서 부르기만 세게 맞춤.
- 테스트 892 + 800 통과, 타입·코드 검사·의존 방향(1737개 연결)·빌드 통과. 함수 타입 검사 통과.
- 저장소 밖 가짜 시험(scratchpad `24e2/harness.mjs`) 통과: 공개 키만·로그인 없음 401, 틀린 모양·빈 문장·301자 400(AI 안 부름), 요청 모양(주소·키·`iris`·`ko`·mp3 32kbps·15초 신호), 소리 그대로·형식, xAI 400·401·429·500·503·시간 넘김·연결 실패·빈 소리·키 없음 → 503·재시도 없음, 기록에 문장 없음·글자 수 있음.
- 고치기 전 실패 확인: `useSpeechOutput.test.js` 7개 중 4개(xAI 소리 틀기·늦게 온 소리·재생 막힘·트는 중 사라짐)와 패널 읽어 주기 시험은 수정 전 코드로 실패. 3개(둘 다 없음·못 받으면 휴대폰 목소리·휴대폰 목소리 멈추기)는 24-E 동작을 지키는 안전 확인. `unbiseoVoice.test.js` 3개와 함수는 새 파일이라 되돌릴 대상 없음(정상).
- 남은 것: 데스크톱 AI가 새 함수 `unbiseo-voice` 만들기(편집기에 넣고 지문 확인, "Verify JWT" 켬) → 보리 배포 → 진짜 서버 시험(공개 키만 401, 보리 계정으로 문장 → mp3·크기·시간, 기록에 글자 수만) → 휴대폰 확인.
- 2026-10-23 데스크톱 AI가 새 함수 화면에 이름·코드를 커밋과 같게 넣음(SHA-256 `fdf25e48…`), 보리가 "Deploy function". 설정 "Verify JWT with legacy secret" 켜짐. 시험: 앱 공개 키만 → 401 "로그인이 필요합니다."(함수 코드의 확인). **보리 로그인 표(ES256)로 부르면 401 `UNAUTHORIZED_ASYMMETRIC_JWT` "Invalid JWT"** — 같은 표로 `unbiseo-parse`는 200. 새로 만든 함수는 이 설정이 이름대로 "예전 키(HS256)로 서명된 표만" 받아 지금 로그인 표(ECC)를 문 앞에서 막음(24-A 때 `unbiseo-parse`는 같은 설정에도 통과 — 옛 함수와 새 함수가 다르게 동작). Supabase 화면 권장도 "끄고 함수 코드에서 확인". 함수 코드는 이미 `auth.getUser`로 로그인을 다시 확인하므로, **이 함수의 설정을 끄면 로그인 계정만 그대로**(공개 키만 → 함수가 401). 보리 결정 대기.
- 보리 "unbiseo-voice Verify JWT 설정 꺼도 돼" `[확인: 2026-10-23 보리]` → 데스크톱 AI가 이 함수만 끄고 저장(다시 불러와 꺼짐 확인, `unbiseo-parse`는 그대로). 다시 시험: 공개 키만 → 401, 로그인 표 없음 → 401(둘 다 함수 코드의 "로그인이 필요합니다."). **보리 계정 → 200 소리 파일(`voice_id` `iris` 맞음)**: 22자 → 14,304바이트·3.6초 소리·0.9초에 받음 / 33자 → 15,552바이트·3.9초·0.8초 / 37자 → 25,824바이트·6.5초·0.9초. 함수 기록에 `{"chars":37,"ms":828,"ok":true,"bytes":25824}` 꼴만(문장 없음). CI verify·deploy 초록(run 39935601401). Iris 목소리로 실제 들리는지·휴대폰 목소리 대신 읽기는 보리 휴대폰 확인 몫.
- §5 리뷰(데스크톱 AI): ① 범위 — 6개 파일(새 함수·새 `unbiseoVoice.ts`·`useSpeechOutput.ts` + 테스트 3), 지시서와 같음, `useUnbiseoAssistant.ts`·`unbiseo-parse` 안 바뀜 ② 몰래 증설 없음(받은 소리는 그 자리에서만, 저장·재시도 0 — 실패는 휴대폰 목소리) ③ 타입 꼼수 0 ④ 200줄 — 함수 96·`useSpeechOutput.ts` 87 ⑤ 테스트 — 지운 줄은 패널 시험의 보냄 횟수 세기를 운비서 부르기만 세게 바꾼 것(진행 기록), 새 시험 중 4개+패널 1개 고치기 전 실패 ⑥ 공용 영향 없음 ⑦ 목표 1~4 충족. 지시서와 다른 점: 함수 설정 "Verify JWT"는 끔(보리 확인, 로그인 확인은 함수 코드) — `[x]` 때 sot §4-16에 함수별로 적음. 남은 것: 보리 휴대폰 확인 → 최종 승인.

### 사용자 확인 방법
1. 휴대폰 운비서 마이크 → "이번 달 매출 얼마야" → Iris 목소리로 답.
2. [멈추기]·마이크 다시 누르기 → 멈춤.
3. 며칠 쓴 뒤 xAI 콘솔(console.x.ai) 사용량에서 음성 비용 확인.

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
