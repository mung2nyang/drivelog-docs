# docs/report.md — 현재 슬라이스 착수지시서

24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 24-F-2 운비서 → 고정 노선 [기록하기]면 일지에서 바로 더하기 `[x]` `[확인: 2026-10-23 보리 — "24-F-2 승인"]`

보리 지시(2026-10-23, 24-F-1 `[x]` 뒤 — "승인", 이어서 F-2). 서버는 고정 노선을 `{ kind: 'fixed', fixed: { date, count, route } }`로 줌(sot §4-16, 앱이 보낸 `routeNames`와 같을 때만 노선). [기록하기]는 **가** — 그 날짜 일지로 가서 일지의 노선 칩·횟수와 같은 동작으로 바로 더함 `[확인: 2026-10-23 보리 — "가로"]`.

### 쉬운 요약
- "평택 용인 고정 2번 뛰었어" → 카드 "오늘 평택 → 용인 2회로 기록할게요." + [기록하기]. 누르면 오늘 일지로 가서 평택 → 용인 칩이 2회 올라가고(고정 노선 운행 횟수도 같이) "평택 → 용인 2회를 더했습니다." 알림. 잘못이면 칩 옆 [−]로 되돌림.
- "오늘 고정 3번"처럼 노선을 안 말하면 고정 노선 운행 횟수만 3 올림.

### 현재 상태 (코드 확인)
- 앱 `features/home/api/unbiseoParse.ts`(156줄) `readAnswer`는 `fixed`를 모름 → 지금 "알아듣지 못했습니다". 보낼 때 `routeNames`를 안 보냄(`useUnbiseoAssistant.ts:65` — `text`·`today`·`clientNames`).
- 일지 고정 노선: 칸은 `settings.fixedOn`일 때만 보임, 노선 칩은 `settings.fixedRouteOn`일 때만(`DayLogPage.jsx` `routePresets = settings.fixedRouteOn ? fixedRoutePresets : []`), 칩 누름 `runRoute(노선 id, +1)`(`useCallDetailActions.js:32-36` — `applyFixedRouteRun`(`domain/day-record.ts:35`)로 노선 횟수와 `fixedCount`를 같이), 횟수 칸은 `patchDraft({ fixedCount })`. 저장은 일지 자동 저장.
- 운비서 이동 길: `assistantCallState.js`(88줄)가 주소 상태를 만들고 읽음(운행 `assistantCall`·지출 `assistantExpense`), `MainPageRoute.jsx`(112줄)가 읽고 지움, `DayLogPage.jsx`(181줄)가 훅으로 받음(`useAssistantPrefill`·`useAssistantExpense` — 이동 키로 같은 날짜 다시, 휴무 끄기).

### 목표 상태 (기대 동작)
1. **보내기**: `fixedOn`이고 `fixedRouteOn`이면 등록 노선을 "상차지 → 하차지" 이름(30개까지)으로 `routeNames`에 실어 보냄. 아니면 안 보냄(빈 목록 — 서버 그대로).
2. **문장**: "{오늘|M월 D일} {노선 이름|고정 노선} {n}회로 기록할게요."
3. **버튼 자리**(운행과 같은 순서): 기간 밖 안내 → **고정 노선 꺼짐**(`fixedOn` 아님) "고정 노선이 꺼져 있어요. 켤까요?" + [켜기](앱 설정과 같은 값 `fixedOn: true`) → 휴무면 "휴무를 취소하고 고정 노선을 기록할까요?" + [휴무 취소하고 기록] → [기록하기].
4. **이동·더하기**: [기록하기] → `/app/day/{날짜}`, 주소 상태 `assistantFixed: { count, route, cancelOff }`(일지가 모양 확인 — 횟수 1~20 정수, 노선 100자 — 뒤 바로 지움). 주소 상태가 지워진 뒤 한 번: `cancelOff`면 휴무 끄기(지금과 같음) → 노선이 그날 설정의 노선 칩 이름과 같으면 `runRoute(그 id, 횟수)`, 아니면 `patchDraft({ fixedCount: 지금 + 횟수 })` → 알림 "{노선} {n}회를 더했습니다." / "고정 노선 {n}회를 더했습니다.". 저장은 일지 자동 저장 그대로(새 저장 길 없음).
5. **같은 날짜**: 이미 그 날짜 일지에서 또 [기록하기]면 이동 키로 알아보고 한 번 더 더함(입력 칸이 열려 있어도 고정 노선은 입력 칸과 상관없어 확인 창 없음). 다시 그려도 두 번 더하지 않음.
6. 고정 노선 칸이 꺼진 일지(`fixedOn` 아님)로 왔으면 더하지 않음(카드에서 이미 [켜기]로 막음 — 안전망). 운행·지출·질문 흐름 그대로.

### 건드릴 파일
- `src/features/home/api/unbiseoParse.ts` — `fixed` 읽기(모양 확인)·문장, 보낼 때 `routeNames`(156 → 약 175).
- `src/features/home/hooks/useUnbiseoAssistant.ts` — 등록 노선 이름 만들어 보내기, `answer.fixed`, `fixedOn`·[켜기](100 → 약 112).
- `src/features/home/hooks/unbiseoContext.ts` — 이동 함수 타입에 고정(35 → 약 36).
- `src/features/home/ui/HomeAssistantPanel.tsx` — 고정 버튼 자리(79 → 약 92).
- `src/app/routes/assistantCallState.js` — `assistantFixed` 만들기·읽기(88 → 약 108).
- `src/app/routes/MainPageRoute.jsx` — `assistantFixed` 읽어 넘기고 지울 때 같이(112 → 약 115).
- 새 `src/features/day-log/hooks/useAssistantFixed.js`(약 45줄) — 받아서 한 번 더하기·휴무 끄기·같은 날짜 다시·알림(운행·지출 훅은 안 건드림).
- `src/features/day-log/ui/DayLogPage.jsx` — 새 훅 부르기(181 → 약 185).
- 테스트: `unbiseoParse.test.js`(고정 읽기·문장·routeNames 보냄), `HomeAssistantPanel.test.js`(버튼 순서 — 꺼짐 [켜기]·휴무·[기록하기]), 일지 시험(`MainPageRoute.assistantCall.test.js`에 고정 — 노선 맞음 → 칩·횟수 같이·저장 기록, 노선 없음 → 횟수만, 휴무 끄기, 다시 그려도 한 번, 이상한 값 걸러냄), `UnbiseoDock.test.js`에 같은 날짜 고정 1개.
- 문서(`[x]` 때): sot §4-16 앱 줄, 로드맵 24-F 끝.
- 파일 수 8개(새 1, 테스트 따로)로 큼 — 문장·버튼·이동·더하기가 한 흐름(24-C-2와 같은 크기·같은 길).

### 안 건드릴 것
- 서버(24-F-1 끝), 일지 저장 길(`useDayDraft`·`dayLogCloudCommit` — 칩·횟수와 같은 `patchDraft`/`runRoute`), 노선 칩·횟수 칸 화면(`FixedRouteChips.jsx`·`FixedCountSection.jsx`), 운행·지출 훅, 앱 설정 화면, CSS.

### 검증
- 위 테스트(실제 누르기·이동). 기존 테스트 전부 통과·경고 늘지 않음. 타입·빌드·의존 방향은 CI.
- 플레이북: 일지 기록을 바꿈(저장 길은 기존 그대로) — §2(호출 경로) 확인: 더하기는 칩 누름과 같은 `runRoute`/`patchDraft` 한 번, 저장된 기록에 노선·횟수가 맞게 남음.
- 브라우저(보리 계정 — 노선 칩이 있는 계정): "평택 용인 고정 2번"(등록 노선 이름으로) → [기록하기] → 그날 칩 +2·횟수 +2·알림 → [−]로 되돌림 확인(저장 기록 원래대로).

### 파일 길이
- 위 "건드릴 파일" 예상 — 모두 200 이하. `DayLogPage.jsx` 약 185(200 가까움 — 넘으면 훅 부르기를 기존 훅 쪽으로 묶음). 감시 목록 파일 안 건드림.

### 코드에 넣을 주석(최종 문구)
- `useAssistantFixed.js`: `// 운비서가 넘긴 고정 노선을 그 날짜 일지에 한 번 더한다 — 노선 칩·횟수 칸과 같은 동작(로드맵 24-F-2).`

### 실패 시 처리
- 새 저장 장치·재시도 없음(AGENTS §7). 기대와 다르면 코드는 그대로 두고 이 파일에 수정 착수지시서.

### 진행 기록
- 2026-10-23 AI: react-app `1bb79b0`(13개 파일, push 안 함).
- 줄 수: `unbiseoParse.ts` 156 → 191(예상 175보다 큼, 200 이하), `useUnbiseoAssistant.ts` 100 → 105, `HomeAssistantPanel.tsx` 79 → 80, `unbiseoContext.ts` 35(설명 주석만), `assistantCallState.js` 88 → 103, `MainPageRoute.jsx` 112 → 114, `DayLogPage.jsx` 181 → 184, 새 `useAssistantFixed.js` 38.
- 교차검증·지시서와 다른 점:
  - 노선 이름은 일지 칩 글자와 같게 `{상차지} → {하차지}`. 서버 상한(60자)을 넘는 이름은 빼고 보냄 — 넘기면 서버가 400을 돌려주기 때문.
  - 노선 이름을 보내는 기준은 운비서의 기존 "콜 상세" 판단과 같은 메인 일지 설정(`resolveLogSettings(settings, 'main')`).
  - [켜기]는 콜 상세와 한 함수로 묶음(`enableCallDetail` → `enableSetting(patch)`), 앱 설정 화면과 같은 `savePracticeSettings`.
  - 노선이 오늘 보이는 칩과 맞지 않으면(칩 꺼짐 등) 횟수만 올리고 알림도 "고정 노선 n회를 더했습니다.".
  - 휴무인데 `cancelOff`가 없으면 더하지 않음(칩이 휴무에 눌리지 않는 것과 같게, 지시서에 없던 안전망).
  - 같은 날짜에서 다시 넘기면 이동 키로 알아보고 한 번 더 — 확인 창 없음(지시서대로).
  - 110-B `notifications.saveFailed.test.js` 1개가 저녁(18시 뒤)에는 "오늘 일지" 알림이 먼저 끼어 실패 — 순서 대신 포함만 보게 고쳐 이 커밋에 같이 넣음.
- 테스트 892 + 794 통과, 타입·코드 검사·의존 방향(1735개 연결)·빌드 통과.
- 고치기 전 실패 확인: 새 테스트 13개 중 11개는 수정 전 코드로 실패, 2개(고정 노선 칸이 꺼진 일지면 안 더함, 노선 칩 끈 계정은 이름 안 보냄)는 안전 확인.
- 플레이북 §2: 더하기는 칩 누름과 같은 `runRoute` 또는 횟수 칸과 같은 `patchDraft` 한 번, 저장된 기록의 노선·횟수를 시험에서 확인.
- 보리 push 뒤 CI verify·deploy 초록(run 39933836995). 데스크톱 AI 배포본 확인(보리 계정, 매출 화면 운비서에 글로 — [기록하기]는 실제 기록을 바꾸므로 안 누름): "오늘 고정 2번" → "오늘 고정 노선 2회로 기록할게요." + [기록하기], "어제 고정 노선 한 번" → "10월 22일 고정 노선 1회로 기록할게요." + [기록하기]. 노선 이름 맞춤·일지 더하기·[−]는 보리 휴대폰 확인 몫.
- §5 리뷰(데스크톱 AI): ① 범위 — 13개 파일(운영 8·테스트 5), 지시서 "건드릴 파일" + 지시서 밖 1개 `notifications.saveFailed.test.js`(110-B 시험이 18시 뒤 "오늘 일지" 알림이 먼저 끼어 실패 — 순서 대신 포함만 보게, 진행 기록에 이유, 운영 코드 아님) ② 몰래 증설 없음(저장 길은 칩·횟수와 같은 `runRoute`/`patchDraft`, 휴대폰 저장 0) ③ 타입 꼼수 0 ④ 200줄 — 최대 `unbiseoParse.ts` 191(지시서 약 175보다 큼, 200 이하 — 다음 조각에서 넘으면 나누기), `DayLogPage.jsx` 184 ⑤ 테스트 — 지운 줄은 새 칸(`readAssistantFixed`·`routeNames`)에 맞춘 것과 위 알림 시험 순서 한 줄, 새 13개 중 11개 고치기 전 실패 ⑥ 공용 영향 — [켜기]를 콜 상세와 한 함수(`enableSetting`)로 묶음(콜 상세 [켜기] 시험 그대로 통과) ⑦ 목표 1~6 충족(지시서와 다른 점 — 진행 기록: 휴무인데 cancelOff 없으면 안 더함 등). 남은 것: 보리 휴대폰 확인 → 최종 승인.

### 사용자 확인 방법
1. 운비서에 "{등록 노선 지명 두 개} 고정 2번 뛰었어" → "오늘 {노선} 2회로 기록할게요." + [기록하기].
2. 누르면 오늘 일지로 가서 그 노선 칩 2회·운행 횟수 +2·알림 "{노선} 2회를 더했습니다.". 칩 [−] 두 번이면 원래대로.
3. "오늘 고정 3번" → 운행 횟수 +3(칩은 그대로).
4. 고정 노선을 끈 계정이면 "고정 노선이 꺼져 있어요. 켤까요?" + [켜기].

## 로드맵 24-E2 운비서 답을 xAI 목소리(Iris)로 `[ ]`

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
