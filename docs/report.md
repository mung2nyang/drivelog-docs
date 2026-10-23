# docs/report.md — 현재 슬라이스 착수지시서

110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 24-E 운비서 답을 소리로 읽어 주기 `[x]` `[확인: 2026-10-23 보리 — "승인"]`

보리 지시(2026-10-23): "응 좋아 작성해" — 24-E~I 순서 권함대로 `[확인: 2026-10-23 보리]`, 음성은 "1번으로 시도"(휴대폰 기본 목소리). 24-E~I 첫 조각, 앱만(서버·저장 없음).

### 확인할 점 (하나) — 언제 읽나
- 권함: **마이크로 말했을 때만** 답을 읽음(운전 중 = 마이크, 글로 쳤으면 화면을 보고 있으니 안 읽음). 읽는 동안 카드에 **[멈추기]**. 끄기 스위치는 이번엔 안 둠 — 글로 치면 안 읽으니 끌 일이 적고, 스위치를 두면 설정 저장 칸이 하나 늘어남(써 보고 필요하면 그때). 다른 안: 늘 읽기 + 앱 설정에 끄기 스위치.

### 쉬운 요약
- 마이크로 "이번 달 매출 얼마야"라고 하면 카드에 답이 나오면서 휴대폰이 "10월 매출은 3,303,011원입니다."를 소리로 읽어 줍니다. 운행·지출도 "…으로 기록할게요."를 읽고, [기록하기]는 지금처럼 눌러야 합니다.
- 휴대폰 크롬에 들어 있는 읽어 주기라 무료, 인터넷으로 문장을 보내지 않습니다.

### 현재 상태 (코드 확인)
- 답 문장은 `features/home/hooks/useUnbiseoAssistant.ts`(89줄) `send`가 `setAnswer({ sentence })`로 만듦. 말로 왔는지 글로 왔는지 구분 없음 — 받아쓰기 끝나면 `useSpeechInput` → `send(heard)`(`:44-47`), 글은 `HomeAssistantPanel.tsx` 폼 → `send(text)`.
- 읽어 주기 코드 없음(`src` grep `speechSynthesis` 0). 받아쓰기는 `useSpeechInput.ts`(80줄, `webkitSpeechRecognition`, 지원 안 하면 마이크 없음).
- 대화 상태는 앱 틀 한 곳(`unbiseoContext.ts`) — 홈 카드·떠 있는 버튼이 같이 씀(24-B-3-B).

### 목표 상태 (기대 동작)
1. 마이크로 받아쓴 말로 보낸 답이면, 답 문장이 나올 때 휴대폰이 그 문장을 한국어(`ko-KR`)로 한 번 읽음. "알아듣는 중…"은 안 읽음. 실패 문구("잠시 뒤 다시 시도해 주세요." 등)도 읽음.
2. 글로 쳐서 보낸 답은 읽지 않음.
3. 읽는 동안 답 아래 **[멈추기]** — 누르면 바로 멈춤. 마이크를 다시 누르면(듣기 시작) 읽던 것을 먼저 멈춤 — 휴대폰 소리를 마이크가 받아쓰지 않게. 새 답이 오면 앞 것은 멈추고 새 것을 읽음. 계정이 바뀌거나 앱 틀이 사라지면 멈춤.
4. 읽어 주기를 지원하지 않는 브라우저면 조용히 안 읽음(카드는 그대로, [멈추기]도 없음).
5. 문장·버튼·저장 흐름은 그대로.

### 건드릴 파일
- 새 `src/features/home/hooks/useSpeechOutput.ts`(약 45줄) — 읽어 주기 지원 여부·`speak(문장)`·`stop()`·읽는 중 상태(`window.speechSynthesis`·`SpeechSynthesisUtterance`, 없으면 아무것도 안 함).
- `src/features/home/hooks/useUnbiseoAssistant.ts` — 받아쓰기로 보낸 것인지 기억해 답이 나오면 읽기, 듣기 시작·새 답·계정 바뀜에 멈추기, `speaking`·`stopSpeaking` 내줌(89 → 약 100).
- `src/features/home/ui/HomeAssistantPanel.tsx` — 읽는 중이면 답 아래 [멈추기](기존 `home-assistant-action` 모양 — CSS 안 늘림)(78 → 약 83).
- 테스트: 새 `useSpeechOutput.test.js`(가짜 `speechSynthesis` — 읽기·멈추기·지원 안 함), `HomeAssistantPanel.test.js`(가짜 받아쓰기로 말함 → 답을 읽음, 글로 보냄 → 안 읽음, [멈추기], 듣기 시작하면 멈춤).
- 문서(`[x]` 때): sot §4-16 한 줄, 로드맵 24-E.

### 안 건드릴 것
- 서버 함수, 답 문장 만드는 곳(`unbiseoParse.ts`·`unbiseoQuestion.ts`), 떠 있는 버튼·홈 줄 모양(`UnbiseoDock.tsx`·`HomeAssistantBar.jsx`), CSS, 설정 저장(스위치 없음).

### 검증
- 위 테스트. 기존 테스트 전부 통과·경고 늘지 않음. 타입·빌드·의존 방향은 CI.
- 휴대폰(보리): 마이크로 "이번 달 매출 얼마야" → 소리로 답, [멈추기] 누르면 멈춤, 글로 치면 조용함. (데스크톱 AI의 브라우저 창은 마이크가 막혀 있어 소리 확인은 보리 몫.)

### 파일 길이
- 새 `useSpeechOutput.ts` 약 45, `useUnbiseoAssistant.ts` 89 → 약 100, `HomeAssistantPanel.tsx` 78 → 약 83. 200 이하(감시 목록 파일 안 건드림).

### 코드에 넣을 주석(최종 문구)
- `useSpeechOutput.ts`: `// 휴대폰 기본 읽어 주기(한국어) — 무료, 문장을 밖으로 보내지 않는다. 지원 안 하면 조용히 안 읽는다.`
- `useUnbiseoAssistant.ts`(읽기 부분): `// 마이크로 말했을 때만 답을 소리로 — 듣기를 시작하면 읽던 것을 먼저 멈춘다(로드맵 24-E).`

### 실패 시 처리
- 새 저장 장치·재시도 없음(AGENTS §7). 기대와 다르면 코드는 그대로 두고 이 파일에 수정 착수지시서.

### 진행 기록
- 2026-10-23 AI: react-app `0ee92ea`(push 안 함). 확인할 점은 권함대로(마이크로 말했을 때만, 스위치 없음).
- 새 `useSpeechOutput.ts` 46줄, `useUnbiseoAssistant.ts` 89 → 100, `HomeAssistantPanel.tsx` 78 → 79.
- 교차검증·지시서와 다른 점:
  - 마이크 단추는 패널이 아니라 `HomeAssistantBar.jsx`·`UnbiseoDock.tsx`에 있음 — 두 파일은 안 건드리고, 훅이 내주는 `speech.start`를 "읽던 것 멈춘 뒤 듣기"로 감싸 둘 다 같이 바뀜.
  - 새로 보내는 순간("알아듣는 중…")에도 읽던 것을 멈춤 — 글로 새로 보내도 앞 답 소리가 이어지지 않게.
  - [멈추기]는 답 문장 바로 아래, [기록하기] 위.
  - 계정이 바뀔 때 멈추기는 테스트 없음(같은 효과 안에서 `currentOwner`와 함께 멈춤).
- 테스트 892 + 781 통과, 타입·코드 검사·의존 방향(1732개 연결)·빌드 통과.
- 고치기 전 실패 확인: 새 테스트 5개 중 패널 테스트 1개는 수정 전 코드로 실패. `useSpeechOutput.test.js` 4개는 새 파일만 시험해서 되돌릴 대상이 없어 통과(정상).
- 소리는 휴대폰 확인 필요(아래 1~4).
- 보리 push 뒤 CI verify·deploy 초록(run 39924283358). §5 리뷰(데스크톱 AI): ① 범위 — 5개 파일(운영 3·테스트 2), `HomeAssistantBar.jsx`·`UnbiseoDock.tsx`·CSS·서버 안 바뀜(마이크는 훅의 `speech.start`를 감싸 두 곳이 같이 바뀜 — 진행 기록) ② 몰래 증설 없음(설정 저장·휴대폰 저장 0, 읽기는 화면 메모리) ③ 타입 꼼수 0 ④ 200줄 — 최대 `useUnbiseoAssistant.ts` 100 ⑤ 테스트 — 지운 시험 줄 없음, 새 5개 ⑥ 공용 영향 — `speech.start`를 감싼 것뿐(멈춘 뒤 같은 듣기) ⑦ 목표 1~5 충족(계정 바뀔 때 멈추기는 시험 없음 — 진행 기록). 남은 것: 보리 휴대폰 소리 확인(1~4) → 최종 승인.

### 사용자 확인 방법
1. 휴대폰 홈 또는 다른 화면의 운비서 → 마이크 → "이번 달 매출 얼마야" → 답이 화면에 나오며 소리로 읽힘.
2. 읽는 중 [멈추기] → 바로 멈춤.
3. 글로 "이번 달 매출 얼마야" → 답만 나오고 소리 없음.
4. 읽는 중에 마이크를 다시 누르면 읽기가 멈추고 듣기 시작.

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
