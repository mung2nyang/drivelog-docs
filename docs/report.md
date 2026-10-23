# docs/report.md — 현재 슬라이스 착수지시서

24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 24-G-2 운비서 앱 — 거래처 등록 카드와 등록 창 채워 열기 `[x]`

보리 지시(2026-10-23, 24-G-1 `[x]` 뒤): "24-G-2 착수지시서 작성해". 로드맵 24-G 두 번째 조각. 서버는 G-1(react-app `f826fdc`, 배포 끝)에서 `kind: 'client'`를 돌려주지만, 앱이 아직 몰라 "알아듣지 못했습니다"가 뜬다. 이번엔 앱만 바꾸며 함수는 다시 올리지 않는다. `unbiseoParse.ts`(191줄)가 넘치므로 문장 만드는 부분을 나누는 것도 이번에 한다.

### 확인할 점 (하나) — 이미 있는 이름 — **가로 확정** `[확인: 2026-10-23 보리 — "이미 있는 이름은 가(문장만, 버튼 없음)로"]`
- 지금 거래처 저장은 같은 이름을 막지 않는다(`clientMutations.ts`·`clients.ts`에 겹침 검사 없음, sot에도 규칙 없음).
- **권함 가**: 앱 거래처 목록에 같은 이름(앞뒤 공백 뺀 글자 그대로)이 있으면 "한진은 이미 등록된 거래처예요."만 보여 주고 버튼은 없음. 고칠 땐 거래처 화면에서 직접 고침.
- 나: 문장은 같고 [그래도 등록하기]를 둠. 같은 이름 두 개를 만들 수 있음.
- 다: [열기]로 기존 거래처 수정 창을 엶. AI가 들은 값으로 덮을지 말지까지 정해야 해서 커짐.
- 가를 권하는 이유: 같은 이름이 두 개면 일지에서 거래처를 고를 때 헷갈리고, 말로 하다 잘못 덮어쓸 일이 없다.

### 쉬운 요약
- "거래처 등록 한진 김철수 010-1234-5678 익월 말일 수수료 5%"라고 말하면 카드에 "한진을 거래처로 등록할게요. (담당자 김철수, 010-1234-5678, 익월 말일 정산, 수수료 5%)"와 [등록하기]가 나온다.
- [등록하기]를 누르면 거래처 화면으로 가서 등록 창이 그 값으로 채워진 채 열린다. 저장은 사람이 [저장]을 누른다(운행·지출과 같음).

### 현재 상태 (코드 확인)
- `unbiseoParse.ts` `readAnswer`는 call·expense·fixed·question·unknown만 읽는다. 서버의 `client`는 마지막 줄에서 `error`(알아듣지 못함)가 된다.
- 서버가 주는 칸(`supabase/functions/unbiseo-parse/clientDraft.ts`): `name`·`manager`·`phone`(숫자·하이픈)·`paymentTerm`(6가지 또는 빈 값)·`paymentTermValue`(숫자 또는 null)·`commType`(`percent`/`direct`/빈 값)·`commValue`(숫자 또는 null).
- [기록하기]는 `AppShell.jsx:156` 한 줄에서 늘 `/app/day/{날짜}`로 간다(`assistantCallNavState`).
- 거래처 화면 `/app/clients`(`AppShellRoutes.jsx:73-81`)에는 두 가지가 있다.
  - 차주와 연동 안 된 기사: `ClientListPage.jsx`(직접 `ClientFormModal`)
  - 연동 기사: `OwnerScopedClientsView` → `ScopedClientsList` → `useScopedClients.js`
  - 둘 다 등록 창은 `openAdd()`가 빈 초안(`emptyDraft`)으로 연다.
- 등록 창이 열려 있으면 운비서 동그라미(z-index 890)가 창 아래에 깔려 누를 수 없다(`unbiseo-dock.css:8`). 그래서 입력 중인 창을 운비서 값이 덮을 일이 없다.

### 목표 상태 (기대 동작)
1. 서버 답 `kind: 'client'`를 읽는다.
   - 이름이 비었거나 50자를 넘으면 알아듣지 못함.
   - 그 밖의 칸은 모양이 틀리면 빈 칸이다. 서버 G-1과 같은 범위: 담당자 30자, 연락처 숫자 9~13자리, 결제 주기는 앱 목록 안, 값은 N일 후 1~365·익월/익익월 N일 1~31, 수수료는 percent 0~100·direct 원 정수, 종류와 값이 함께 맞을 때만.
2. 카드 문장: "{이름}{을/를} 거래처로 등록할게요."
   - 들은 칸이 있으면 뒤에 " (담당자 김철수, 010-1234-5678, 익월 말일 정산, 수수료 5%)"를 붙인다.
   - 연락처는 거래처 창과 같은 `formatPhoneNumber`로 보여 준다.
   - 결제 주기는 `getPaymentTermLabel`로 보여 준다.
   - 수수료는 percent면 "수수료 5%", direct면 "수수료 10,000원"이다.
   - 빈 칸은 뺀다.
3. 버튼은 [등록하기] 하나. 계약 기간·휴무·콜 상세 버튼은 날짜가 없는 거래처에는 나오지 않는다.
4. 이미 있는 이름(앞뒤 공백 뺀 글자 그대로)이면 문장만 "{이름}{은/는} 이미 등록된 거래처예요.", 버튼 없음(확인할 점 가).
5. [등록하기]를 누르면 떠 있는 카드를 접고 `/app/clients`로 간다.
   - 차주·미연동 기사·연동 기사 모두, 그 화면의 등록 창이 들은 값으로 채워진 채 **새 등록**으로 열린다.
   - 넘기는 값: 거래처명, 담당자, 연락처(하이픈 형식), 결제 주기와 값, 수수료 켜짐·종류·값.
   - 결제 주기를 안 말했으면 지금 기본값(익월 말일)이다.
   - 연동 기사는 지금처럼 그 차량 거래처로 저장된다.
6. 주소 상태는 읽은 뒤 바로 지운다(`replace`). 뒤로가기나 새로 고침으로 창이 다시 열리지 않는다.
   - 이미 거래처 화면에서 운비서로 또 넘기면 새 값으로 다시 연다(이동 키가 바뀜).
7. 저장, 검사, 알림은 지금 등록 창 그대로다(`checkClientDraft`·`requestClientSave`). [취소]면 저장하지 않는다.
8. 운행, 지출, 고정 노선, 질문, 알아듣지 못함의 문장과 버튼은 지금과 글자까지 같다.

### 건드릴 파일
- `src/features/home/api/unbiseoParse.ts` — `UnbiseoClient` 타입, `client` 종류, `readClient`를 넣는다. 문장 함수들은 아래 새 파일로 옮긴다(나눔).
- `src/features/home/api/unbiseoSentence.ts`(새) — 옮긴 `withRo`·`dateLabelOf`·`describeCall`·`describeFixed`·`describeExpense`·`describeResult`에 `describeClient`를 더한다. 내용 변경 없이 옮기고, 다시 내보내기(re-export)는 두지 않는다.
- `src/features/home/hooks/useUnbiseoAssistant.ts` — 답에 `client`를 담고, 이미 있는 이름을 확인한다. `describeResult` 가져오는 곳을 바꾼다.
- `src/features/home/ui/HomeAssistantPanel.tsx` — 거래처면 [등록하기].
- `src/app/routes/assistantCallState.js` — `assistantNavTarget(record, options)`: 거래처면 `{ to: '/app/clients', state: { from: 'home', assistantClient } }`, 아니면 지금 일지 경로.
- `src/app/shell/AppShell.jsx` — 156번 줄 `onOpenCall`이 `assistantNavTarget`를 쓰게 한 줄만 바꾼다.
- `src/features/clients/hooks/useAssistantClient.js`(새) — 주소 상태 `assistantClient`를 칸마다 검사해 등록 초안으로 바꾸고, 지운 뒤 `open(초안)`을 부른다.
- `src/features/clients/ui/ClientListPage.jsx`·`src/features/clients/hooks/useScopedClients.js` — 위 훅을 불러 채운 초안으로 새 등록 창을 연다.
- 테스트
  - `unbiseoParse.test.js`: 문장 함수 가져오는 곳 바꿈, 거래처 읽기
  - 새 `unbiseoSentence.test.js`: 거래처 문장
  - 새 `useAssistantClient.test.js`: 칸 검사·초안
  - 거래처 화면 테스트 1개: 상태를 넘기면 채워진 창, 지운 뒤 다시 그려도 안 열림
- `unbiseoContext.ts`는 주석만 "기록하기·등록하기"로 바꿀 수 있다(타입은 `UnbiseoRecord` 합집합이라 그대로).
- 공용 CSS 변경 없음. [등록하기]는 기존 `home-assistant-action` 클래스를 그대로 쓴다.

### 안 건드릴 것
- 서버 함수(`supabase/functions/*`) — G-1에서 끝남. 이번엔 배포·편집기 작업 없음.
- `ClientFormModal.jsx`·`ClientTradeFields.jsx`·저장 함수 — 초안만 채워 넘기므로 창과 저장은 그대로.
- 일지 쪽(`MainPageRoute.jsx`·`useAssistant*.js`) — 날짜 기록은 이번 범위 밖.
- 미수금 문자(24-H)·날씨(24-I).

### 검증
- `npm run lint`·타입 검사·`npm test` 전부 통과.
- 새 테스트는 고치기 전 코드에서 실패하는지 확인(revert-check).
- 옮긴 문장 함수는 기존 문장 테스트가 그대로 통과해야 한다(목표 8).
- `git diff -M`으로 옮긴 부분에 내용 변경이 없는지 확인.
- 데스크톱 AI: 앱 배포 뒤 브라우저에서 "거래처 등록 한진 김철수 …"를 말해(글로 입력) 카드 문장, [등록하기], 채워진 창을 확인한다. 저장은 하지 않고 [취소]한다.

### 파일 길이
| 파일 | 지금 | 예상 |
|---|---|---|
| `unbiseoParse.ts` | 191 | ~150 |
| `unbiseoSentence.ts`(새) | — | ~80 |
| `useUnbiseoAssistant.ts` | 105 | ~112 |
| `HomeAssistantPanel.tsx` | 80 | ~84 |
| `assistantCallState.js` | 103 | ~115 |
| `AppShell.jsx` | 198 | 198(한 줄 바꿈만, 넘으면 멈추고 보고) |
| `useAssistantClient.js`(새) | — | ~60 |
| `ClientListPage.jsx` | 120 | ~125 |
| `useScopedClients.js` | 85 | ~90 |

- 나누는 설계(§6): `unbiseoParse.ts`는 "서버 부르기와 답 읽기", `unbiseoSentence.ts`는 "결과를 앱 틀 문장으로". `unbiseoSentence.ts` → `unbiseoParse.ts`는 타입만 가져온다(되돌아오는 가져오기 없음).
- 한 줄에 여러 문장 금지.

### 코드에 넣을 주석(최종 문구)
- `unbiseoSentence.ts` 첫 줄: `// 운비서 결과를 앱이 정한 틀의 문장으로 — 빈 칸은 뺀다.`
- `useAssistantClient.js` 첫 줄: `// 운비서가 넘긴 거래처 칸을 검사해 새 등록 창을 채워 연다 — 저장은 사람이.`
- `useAssistantClient.js` 지우기 위: `// 읽은 뒤 주소 상태를 지운다 — 뒤로가기·새로 고침으로 다시 열리지 않게.`
- `useUnbiseoAssistant.ts` 이름 확인 위: `// 이미 있는 이름이면 등록 버튼 없이 알린다.`

### 실패 시 처리
- 새 저장 장치·재시도·임시 보관 없음(AGENTS §7). 주소 상태만 쓰고, 휴대폰에 저장하지 않는다.
- 테스트가 깨지거나 `AppShell.jsx`가 200줄을 넘으면 멈추고 지시서를 고쳐 다시 승인받는다.

### 진행 기록
- 2026-10-23 보리: "이미 있는 이름은 가(문장만, 버튼 없음)로. 24-G-2 착수지시서 확정, 작업 진행해" — 구현은 터미널 AI(시작 전 교차검증).
- 2026-10-23 AI: react-app `4a04844`(18개 파일, push 안 함). 서버·편집기 작업 없음.
- 줄 수: `unbiseoParse.ts` 191 → 175(예상 150보다 큼 — 거래처 읽기 20줄), 새 `unbiseoSentence.ts` 77, `useUnbiseoAssistant.ts` 105 → 110, `HomeAssistantPanel.tsx` 80 → 81, `assistantCallState.js` 103 → 115, `AppShell.jsx` 198 그대로(한 줄 바꿈), 새 `useAssistantClient.js` 73, `ClientListPage.jsx` 120 → 123, `useScopedClients.js` 85 → 88.
- 옮긴 문장 함수(`withRo`·`dateLabelOf`·`describeCall`·`describeFixed`·`describeExpense`·`describeResult`)는 원래 글과 비교해 더한 줄(거래처 문장 함수·`describeResult`의 거래처 한 줄)만 다름 — 기존 문장 시험 그대로 통과.
- 교차검증·지시서와 다른 점:
  - **건드릴 파일 밖 3곳**:
    - `ScopedClientsList.jsx` — `onClick={scoped.openAdd}`가 누름 정보를 "채울 칸"으로 넘기게 되어 `() => scoped.openAdd()`로.
    - `App.clientsCars.test.js` — 거래처 화면을 라우터 없이 그리던 시험 1개가 새 훅(`useLocation`)에서 깨져 `MemoryRouter`로 감쌈(앱에서는 늘 라우터 안).
    - `MainPageRoute.assistantCall.test.js` — 갈 곳(`assistantNavTarget`) 시험 1개 추가.
  - `AppShell.jsx` 한 줄을 한 문장으로 두려고 `assistantNavTarget`가 `navigate`에 넘길 `[갈 곳, { state }]`를 돌려줌 → `navigate(...assistantNavTarget(record, options))`.
  - 거래처인지 가르는 기준은 `paymentTerm` 칸(지출에도 `name` 칸이 있어서).
  - 이미 있는 이름 확인은 앱 거래처 목록 전체(`useOwnerClients` — 차량 거래처 포함)에서 앞뒤 공백 뺀 이름이 같을 때.
  - 등록 창 결제 주기: 값이 필요한 주기(N일 후·익월/익익월 N일)인데 값이 없거나 범위 밖이면 주기도 빼서 창 기본값(익월 말일)을 둠 — 값 빈 칸으로 열면 [저장]이 막히기 때문.
  - 받침 없는 이름·한글 아닌 이름("CJ")은 "를"·"는".
  - 연락처 표시는 거래처 창과 같은 `formatPhoneNumber` — "02-123-4567"은 "021-234-567"로 보임(창 입력과 같은 규칙, 기존 동작).
  - "이미 거래처 화면에서 또 넘기면 새 값으로" — 주소 이동마다 한 번(`location` 바뀜) 읽는 방식이라 그대로 됨, 따로 시험은 없음.
- 테스트 892 + 825 통과, 타입·코드 검사·의존 방향(1753개 연결)·빌드 통과.
- 고치기 전 실패 확인: 새 테스트 16개 중 7개는 수정 전 코드로 실패, 8개(문장·칸 검사)는 새 파일만 시험해서 되돌릴 대상 없음(정상), 1개(지운 뒤 다시 그려도 안 열림)는 안전 확인.
- 데스크톱 AI 리뷰·시험(CI `4a04844` 초록 — verify·deploy, 함수 배포 없음):
  - §5: 200줄 안(`AppShell.jsx` 198 그대로), 한 줄 여러 문장 없음, 새 저장·재시도 없음(주소 상태만), 옮긴 문장 함수 내용 그대로. 건드릴 파일 밖 3곳은 근거 있어 받아들임. 주석 한 곳(`assistantCallState.js` 첫 줄)에 로드맵 번호가 더 붙음 — 기존 줄을 늘린 것이라 넘김.
  - 배포본 홈 카드 "거래처 등록 한진 김철수 과장 010-1234-5678 익월 말일 수수료 5%" → "한진을 거래처로 등록할게요. (담당자 김철수, 010-1234-5678, 익월 말일 정산, 수수료 5%)" + [등록하기] → `/app/clients` 등록 창: 한진·김철수·010-1234-5678·익월 말일 정산·퍼센트 5. 주소 상태 지워짐(`from`만 남음), [취소] → 저장 안 됨, 새로 고침해도 창 안 열림.
  - 거래처 화면에서 떠 있는 운비서 "거래처 등록 테스터거래처" → "테스터거래처는 이미 등록된 거래처예요.", 버튼 없음.
  - 같은 화면에서 또 넘기기 → 새 값으로 다시 열림(카드 접힘).
  - **문제 1개**: "새 거래처 롯데 익월 지정일 결제" → 카드 "롯데를 거래처로 등록할게요. (익월 일 정산)"(날짜 숫자 없음), 등록 창은 기본값 익월 말일 정산 — 문장과 창이 다름. 원인: `describeClient`가 값이 필요한 주기인데 값이 없어도 `getPaymentTermLabel`을 부름(창 쪽 `useAssistantClient`는 이때 주기를 뺌).
- 2026-10-23 보리: "익월 일 정산 문구 지금 고쳐. 지시서에 수정 내용 추가해줘" → 아래 "추가 수정" 절. 구현은 터미널 AI.
- 추가 수정: react-app `75861b5`(2개 파일, `unbiseoSentence.ts` 77 → 79, 주석은 지시서 글자 그대로). CI 초록(verify·deploy).
- 배포본 다시 시험: "새 거래처 롯데 익월 지정일 결제" → "롯데를 거래처로 등록할게요."(주기 빠짐) / "다음 달 10일 결제" → "(익월 10일 정산)" / "건별 30일 후, 수수료 건당 만원" → "(운행 건별 30일 후 정산, 수수료 10,000원)" / 한진 문장 → 전과 같음. 운행·지출·잡담 문장도 전과 같음. 문제 없음.
- 2026-10-23 보리 승인("24-G-2 승인").

### 추가 수정 — "익월 일 정산" 문구 (2026-10-23 보리: "익월 일 정산 문구 지금 고쳐")
- 지금: 값이 필요한 결제 주기(N일 후·익월 N일·익익월 N일)인데 날짜 숫자가 없으면 카드 문장에 "(익월 일 정산)"이 나온다. 등록 창은 이때 주기를 빼 기본값 익월 말일로 열려 서로 다르다(`unbiseoSentence.ts` `describeClient` → `getPaymentTermLabel(term, '')`, `useAssistantClient.js`는 주기를 뺌).
- 목표:
  1. 이런 때 카드 문장에서 결제 주기를 뺀다. 예: "새 거래처 롯데 익월 지정일 결제" → "롯데를 거래처로 등록할게요."
  2. 날짜가 있으면 지금처럼 보인다("익월 10일 정산", "운행 건별 30일 후 정산"). 말일·당일 주기도 지금 그대로.
  3. 등록 창, 이미 있는 이름, 다른 종류 문장은 바뀌지 않는다.
- 건드릴 파일: `src/features/home/api/unbiseoSentence.ts`(`describeClient` 결제 주기 조건만, `needsPaymentTermValue` 가져오기)와 `unbiseoSentence.test.js`(값 없는 지정일·N일 후는 결제 주기를 빼는지, 값 있으면 그대로인지).
- 안 건드릴 것: `unbiseoParse.ts`의 `readClient`(서버가 준 값을 그대로 읽는 곳), `useAssistantClient.js`, 서버 함수.
- 파일 길이: `unbiseoSentence.ts` 77 → ~79.
- 주석(최종 문구, 조건 줄 위): `// 날짜가 필요한 주기인데 날짜가 없으면 뺀다 — 등록 창도 이때 기본값으로 연다.`
- 검증: lint·타입·`npm test`, 새 시험이 고치기 전 코드에서 실패하는지(revert-check). 배포 뒤 데스크톱 AI가 같은 문장으로 카드 문장을 확인한다.
- 커밋: 코드 커밋 1개를 더한다(이 슬라이스 안). [x] 문서 커밋은 그 뒤 승인 때 1개.
- 실패 시: 새 장치 없음. 다르게 고쳐야 하면 멈추고 보고한다.
- 진행 기록(2026-10-23 AI): react-app `75861b5`(2개 파일, push 안 함). `unbiseoSentence.ts` 77 → 79.
  - 조건: 결제 주기가 있고, 날짜가 필요 없는 주기이거나 날짜 값이 있을 때만 문장에 넣음(값 범위는 `readClient`가 이미 걸러 null이면 빠짐 — 창 쪽 `useAssistantClient`와 같은 결과).
  - 테스트 892 + 826 통과, 타입·코드 검사·의존 방향(1753개 연결)·빌드 통과.
  - 고치기 전 실패 확인: 새 시험 1개(날짜 없는 익월·익익월 지정일·N일 후는 빼고, 날짜 있음·말일·당일은 그대로)는 수정 전 코드로 실패, 복구 확인.

### 사용자 확인 방법
1. 폰에서 운비서를 열고 "거래처 등록 한진 김철수 010-1234-5678 익월 말일 수수료 5%"라고 말한다.
2. 카드에 "한진을 거래처로 등록할게요. (담당자 김철수, 010-1234-5678, 익월 말일 정산, 수수료 5%)"와 [등록하기]가 보이면 성공이다.
3. [등록하기]를 누르면 거래처 화면에서 "거래처 등록" 창이 이름·담당자·연락처·결제 주기·수수료가 채워진 채 열린다. [저장]하면 목록에 생기고, [취소]하면 안 생긴다.
4. 이미 있는 거래처 이름으로 말하면 "○○은 이미 등록된 거래처예요."가 보인다.

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
