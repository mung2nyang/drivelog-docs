# 운비서(AI 비서) 유지보수 안내

AI가 운비서를 고치거나 새 기능을 붙일 때 먼저 읽는 지도다. 로드맵 24번(24-A~K)과 112번의 결과를 한곳에 모았다.
- **정한 동작(문장·조건·보리 결정)의 정본은 `docs/sot.md` §4-16**이다. 이 파일은 구조·파일·절차만 적는다. 둘이 다르면 sot를 따르고 이 파일을 고친다.
- 슬라이스 기록은 `docs/roadmap.md` 24번과 각 커밋(`git log --grep 24-`)에 있다.

## 1. 한 줄 구조

말·글 → 앱(`useUnbiseoAssistant`) → 서버 `unbiseo-parse`(xAI Grok이 말을 칸으로 나눔) → 앱이 칸을 다시 검사 → **앱이 정한 틀의 문장** + 버튼 → 사람이 눌러야 저장.

지키는 원칙(바꾸려면 보리 확인):
- **AI는 계산하지 않는다.** 숫자는 앱이 매출·미수금 화면과 같은 함수로 낸다. AI 회사로 금액이 가지 않는다.
- **AI는 저장하지 않는다.** 저장은 사람이 입력 창 [저장]이나 카드 버튼을 눌러야 된다.
- **날짜는 AI가 계산하지 않는다.** 서버가 날짜 표·달 표를 만들어 주고, AI는 고르기만 한다.
- 로그인 계정만 쓴다. 함수 기록에 말한 문장·답 내용·위치를 남기지 않는다.
- 새 저장소·재시도 큐·임시 보관을 만들지 않는다(AGENTS §7). 실패는 문장으로만 알린다.

## 2. 서버 함수 (Supabase Edge Function, 저장소 `react-app/supabase/functions/`)

| 함수 | 하는 일 | 비밀값 | 파일 |
|---|---|---|---|
| `unbiseo-parse` | 말 → 종류와 칸 | `XAI_API_KEY` | `index.ts`(받기·로그인), `answer.ts`(AI 답을 종류별로 읽기·말 막기), `grok.ts`(AI 지시문·날짜 표·답 모양), 종류별 칸 읽기 `question.ts`·`clientDraft.ts`·`fixedRoute.ts`·`smsDraft.ts`·`weatherDraft.ts` |
| `unbiseo-voice` | 답 문장 → xAI 목소리(Iris) mp3 | `XAI_API_KEY` | `index.ts` |
| `unbiseo-weather` | 날짜 + 지역 이름 또는 격자 번호 → 기상청 단기예보 요약 | `KMA_API_KEY`(공공데이터포털 일반 인증키 Decoding) | `index.ts`(로그인·입력·지역 찾기), `kma.ts`(기상청 부르기·묶기), `kmaGrid.json`(시·도·시·군·구 → nx·ny) |

- **세 함수 모두 "Verify JWT with legacy secret" 꺼짐.** 로그인 확인은 함수 코드(`auth.getUser`)가 한다. 켜면 지금 로그인 표(ES256)를 문 앞에서 거절해 운비서 전체가 401이 된다. **새로 만들면 켜진 채 만들어지므로 바로 끈다.**
- 함수끼리 파일을 나눠 쓸 수 없어 로그인 확인 코드는 함수마다 따로 있다.
- AI 회사를 바꿀 때는 `grok.ts`와 비밀값만 바꾼다.

## 3. 종류(kind)와 흐름

서버 `unbiseo-parse`가 돌려주는 종류와 앱의 처리:

| kind | 서버 막기 | 앱 처리 | 버튼 |
|---|---|---|---|
| `call` 운행 | 앞날은 오늘 | 일지 콜 상세 입력 창 채워 열기 | [기록하기] / 콜 상세 꺼짐 [켜기] / 휴무 [휴무 취소하고 기록] / 계약 기간 밖 안내 |
| `expense` 지출 | 앞날은 오늘 | 지출 입력 창 채워 열기 | [기록하기] |
| `fixed` 고정 노선 | 말에 "고정" 있을 때만 | 일지에서 노선 칩처럼 바로 더함 | [기록하기] / 고정 꺼짐 [켜기] |
| `client` 거래처 등록 | 이름 필수 | 거래처 화면 새 등록 창 채워 열기 | [등록하기] / 이미 있는 이름은 문장만 |
| `question` 질문 | — | 매출·미수금 화면과 같은 계산으로 답 문장 | 없음 |
| `sms` 미수금 문자 | 말에 "문자" 있을 때만 | 빠진 것만 버튼으로 되물음(말로도 고름) → 문자 앱 채워 열기 | 단계별 고르기, [문자 열기] |
| `weather` 날씨 | — | 오늘~모레만, 지역 없으면 휴대폰 위치 → 격자 번호만 `unbiseo-weather`로 | 없음 |
| `inspection` 점검표 모두 양호 | 말에 "점검" 있을 때만 | 설정·기간·휴무·차량·이미 저장 확인 뒤 저장 | [저장하기] |
| `dayOff` 휴무·휴무 취소 | 말에 "휴무"나 "쉬" 있을 때만, 앞날 그대로 | 이미 그 상태·기간 밖 확인 → 그 날짜 일지로 가서 휴무 단추와 같이 바꿈(콜 있으면 확인 창) | [휴무로 하기] / [휴무 취소하기] |
| `unknown` | — | "알아듣지 못했어요" | 없음 |

- 마이크로 물었을 때만 답 문장을 소리로 읽는다(`useSpeechOutput`: xAI 목소리 먼저, 5초 안에 못 받으면 휴대폰 목소리).
- 계정이 바뀌면 대화를 비우고, 기다리던 답은 버린다.
- 홈 카드와 떠 있는 운비서는 같은 대화·같은 카드(`HomeAssistantPanel`)를 쓴다.

## 4. 앱 파일 지도 (`react-app/src/`)

| 파일 | 맡은 일 |
|---|---|
| `features/home/hooks/useUnbiseoAssistant.ts` | 보내기·기다림·답 상태의 중심. 종류별 기다림(날씨·점검표)과 저장 함수 |
| `features/home/hooks/unbiseoContext.ts` | 대화를 앱 전체에 하나로 |
| `features/home/hooks/useSpeechInput.ts` / `useSpeechOutput.ts` | 크롬 받아쓰기 / 소리 읽기 |
| `features/home/api/unbiseoParse.ts` | `unbiseo-parse` 부르기, 답 종류 가르기(`readAnswer`), 실패 문구 |
| `features/home/api/unbiseoFields.ts` | 종류별 칸을 앱 목록·범위로 다시 검사(지출·고정·거래처·날씨·휴무) |
| `features/home/api/unbiseoSentence.ts` | 운행·지출·고정·거래처 문장 틀, `describeResult` |
| `features/home/api/unbiseoQuestion.ts` / `unbiseoNumbers.ts` | 질문 문장 / 화면과 같은 함수로 숫자 |
| `features/home/api/unbiseoSms.ts` · `unbiseoChoice.ts` | 미수금 문자 고를 거리 / 되묻기 버튼을 말로 고르기 |
| `features/home/api/unbiseoWeather.ts` · `kmaGrid.ts` | 날씨 부르기·문장 / 위도·경도 → 기상청 격자 |
| `features/home/api/unbiseoInspection.ts` | 점검표 확인·모두 양호 저장 |
| `features/home/api/unbiseoVoice.ts` | `unbiseo-voice` 부르기 |
| `features/home/ui/HomeAssistantBar.jsx` · `HomeAssistantPanel.tsx` | 홈 줄 / 카드(글 칸·문장·버튼) |
| `features/home/ui/UnbiseoDock.tsx` · `UnbiseoSmsSteps.tsx` | 떠 있는 캐릭터 버튼 / 문자 되묻기 단계 |
| `features/home/hooks/useDockPosition.ts` | 떠 있는 버튼 위치(이 휴대폰에만 저장) |
| `app/routes/assistantCallState.js` · `MainPageRoute.jsx` | [기록하기]·휴무 버튼으로 넘긴 값을 일지·지출 창에서 칸마다 검사해 채움 |
| `features/day-log/hooks/useAssistantFixed.js` · `useAssistantDayOff.js` 등 | 일지가 넘겨받은 값을 한 번만 적용(고정 노선 더하기, 휴무 바꾸기) |
| `features/clients/hooks/useAssistantClient.js` | [등록하기]로 넘긴 거래처 값 받기 |
| `app/shell/AppShell.jsx` | 운비서 Provider, 떠 있는 버튼, **공용 확인 창 자리는 맨 끝**(다른 창 위에 그려지게) |

## 5. 새 종류를 붙이는 순서

24-F~K가 모두 이 순서였다. **서버(J-1)와 앱(J-2) 두 슬라이스로 나눈다.**

1. 서버 `grok.ts`: 종류 목록(`RESPONSE_SCHEMA.kind.enum`)과 종류 설명 줄, 설명·예 1~2줄, 새 칸(모든 칸 필수·null 허용). 날짜는 기존 `date` 칸이나 날짜 표를 쓴다.
2. 서버 `answer.ts`: `Parsed`에 종류, `readAiAnswer`에 분기. 말에 특정 단어가 있어야 하는 종류는 서버 코드로 막는다("문자"·"고정"·"점검").
3. 앱 `unbiseoParse.ts`: `UnbiseoResult`에 종류, `readAnswer`에 갈래. 칸 검사는 `unbiseoFields.ts`.
4. 앱 문장(`unbiseoSentence.ts` 또는 새 `unbiseo○○.ts`), 훅(`useUnbiseoAssistant.ts`), 버튼(`HomeAssistantPanel.tsx`).
5. 서버 배포 전까지 앱은 그 종류에 "알아듣지 못했습니다"를 보인다(앱 `readAnswer`에 갈래가 없어서). 서버 먼저, 앱 다음.

**줄 수**(24-K에서 나눈 뒤): 서버 `index.ts` 91·`answer.ts` 111·`grok.ts` 179, 앱 `unbiseoParse.ts` 119·`unbiseoFields.ts` 92·`useUnbiseoAssistant.ts` 174, 일지 `DayLogPage.jsx` 187. `grok.ts`·`useUnbiseoAssistant.ts`·`DayLogPage.jsx`는 몇 종류 더 붙이면 200줄에 닿으니 그때 지시서에 분리 설계를 먼저 넣는다(AGENTS §6).

## 6. 서버 함수 배포 절차 (데스크톱 AI)

1. 터미널 AI 커밋 → 보리 push → CI 초록(앱은 이때 배포됨, 서버 함수는 아님).
2. 편집기 반영: Supabase 관리 화면 → Edge Functions → 함수 → Code.
   - 바꾸기 전에 편집기 내용 지문이 이전 판 커밋과 같은지 본다.
   - 파일 내용은 `https://raw.githubusercontent.com/mung2nyang/react-app/<커밋>/supabase/functions/<경로>`에서 받아 편집기에 넣는다(저장소가 공개라 커밋 번호로 정확히 받음).
   - 새 파일은 [Add File] 뒤 이름 칸을 두 번 눌러 바꾼다(한 번에 안 바뀔 때가 있어 확인).
   - 넣은 뒤 모든 파일 지문(줄바꿈 LF 기준 SHA-256 앞 8자)을 커밋과 맞춘다.
   - **편집기 내용은 탭을 떠나거나 작업이 꺼지면 사라진다.** 배포 전까지 그 탭을 둔다.
3. 보리가 [Deploy updates](새 함수는 [Deploy function])를 누른다.
4. **배포본 지문을 다시 본다** — 버튼을 눌러도 반영되지 않은 적이 있다(24-I-1, 화면에 "14 hours ago"가 그대로였음).
5. Settings에서 Verify JWT 꺼짐을 본다(새 함수면 끈다).
6. 문장 시험(7절).

## 7. 시험

- **서버 함수**는 저장소 시험(`npm test`)에 들어 있지 않다. 터미널 AI가 저장소 밖에서 가짜 시험(24-A2부터 쌓은 문장 전부 + 새 문장)과 타입 검사를 돌린다.
- **배포본 문장 시험**: 앱 안 브라우저에서 getdrivelog.com에 로그인한 상태로, 저장된 로그인 표(localStorage `…auth-token`의 access_token)와 앱 번들의 공개 키(`sb_publishable_…`)로 함수를 직접 부른다. 공개 키만이면 401이어야 한다.
  - 401이 나오면 `/auth/v1/user`로 로그인이 살아 있는지 먼저 본다. `session_not_found`면 다른 곳 로그아웃 등으로 서버 로그인이 끝난 것이니 보리에게 다시 로그인을 부탁한다.
- **앱**: 운비서 글 칸에 문장을 넣어 카드 문장·버튼을 본다. 위치 허용·소리 읽기는 앱 안 브라우저로 확인할 수 없어 보리 휴대폰 확인이 필요하다.
- 실제로 저장되는 시험(점검표 등)은 날짜를 보리가 정한다. 시험분은 지울 길이 있는지 먼저 본다(점검표는 112번으로 지울 수 있음).

## 8. 남은 것·나중 일

- 하루 사용 횟수 제한: 지금 없음. 11번 앱 포장(출시 직전) 때.
- 처리방침: 말·답 문장을 xAI로 보냄, 받아쓰기는 구글, 날씨는 동네 격자 번호만 우리 서버 경유 — 10-P ⓑ 자문 목록에 있음.
- 기상청 상업 이용·위치 관련 절차: 무료인 동안 따지지 않음, 유료화 때 다시 봄.
- xAI 목소리는 베타 가격(글자 100만 개당 $4.20), 함수 기록의 글자 수로 비용을 셈.
- 이름 "운비서"는 임시.
