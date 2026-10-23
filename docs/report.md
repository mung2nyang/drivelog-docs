# docs/report.md — 현재 슬라이스 착수지시서

24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 24-G-1 운비서 서버 — 거래처 등록 말 알아듣기 `[x]`

보리 지시(2026-10-23, 24-D3 `[x]` 뒤): "작성해". 로드맵 24-G(거래처 등록 — 등록 창을 채워 열기, 저장은 사람). 24-C·F처럼 **둘로 나눔(AI 판단)**: **이번 G-1** = 서버가 "거래처 등록 …" 말을 새 종류로 칸 나눔(앱은 아직 — 지금은 이런 말이 운행 등으로 잘못 감), **다음 G-2** = 앱 카드 [등록하기] → 거래처 관리 화면의 등록 창을 채워 열기. 앱 쪽 `unbiseoParse.ts`가 191줄이라 G-2에서 나누기가 같이 붙어 크기를 나눔.

### 확인할 점 (하나) — 말로 받을 칸
- 권함: **거래처명(필수)·담당자 이름·연락처·결제 주기(+날짜/일수)·수수료(%) 또는 원)**. 말로 하기 쉬운 것만. 사업자등록번호·주소·이메일·업태·종목·고정 노선 단가는 빼고 등록 창에서 직접(번호·주소는 받아쓰기가 자주 틀림).
- 결제 주기는 앱 목록 6가지 안에서만(`domain/clientPaymentTerms.ts` — 당일/수시, N일 후, 익월 N일, 익월 말일, 익익월 N일, 익익월 말일). 예: "익월 말일 결제", "다음 달 10일", "건별 30일 후".

### 쉬운 요약
- "거래처 등록, 한진, 김철수 과장, 010-1234-5678, 익월 말일, 수수료 5%"를 서버가 **거래처 등록**으로 알아듣고 칸으로 나눠 돌려줍니다. 이번엔 서버만, 앱 연결은 다음 G-2.

### 현재 상태 (코드 확인)
- 서버 `unbiseo-parse`: 종류 `call·expense·question·fixed·unknown`(sot §4-16). "거래처 등록 …" 말은 지금 지명 두 개면 운행, 아니면 관계없음 등으로 감. `grok.ts` 149줄, `index.ts` 178줄(200 가까움), `question.ts`·`fixedRoute.ts`처럼 칸 읽기는 따로 파일.
- 거래처 등록 창 칸(`features/clients/ui/ClientFormModal.jsx` 127줄): 거래처명(필수 — `checkClientDraft`)·담당자 이름·대표자·연락처·사업자등록번호·업태·종목·주소·이메일·결제 주기(`paymentTerm` + `paymentTermValue`)·수수료(`commEnabled`·`commType` percent/fixed·`commValue`)·고정 노선(단가·파렛트). 빈 값 `emptyDraft`(`ClientListPage.jsx:20`, 결제 주기 기본 익월 말일).
- **배포 주의**: 이 함수를 다시 배포하면 Supabase가 "Verify JWT"를 다시 켜 운비서 전체가 401이 될 수 있음(24-D3 때 겪음, sot §4-16) — 배포 뒤 꺼짐 확인을 검증에 넣음.

### 목표 상태 (기대 동작)
1. 새 종류 `client`: `{ kind: 'client', client: { name, manager, phone, paymentTerm, paymentTermValue, commType, commValue } }` — "거래처 등록·추가·새 거래처" 같은 말이 있을 때만(운행처럼 지명이 있어도).
2. 칸 규칙: `name` 50자(없으면 이 종류가 아님 → 관계없음), `manager` 30자, `phone`은 숫자·하이픈만 남겨 9~13자리 아니면 빈 칸, `paymentTerm`은 6가지 중 하나 아니면 빈 칸(앱 기본값), `paymentTermValue`는 1~31(N일 후는 1~365) 정수 아니면 빈 칸, `commType` percent/fixed·`commValue` 0 이상 숫자(%는 100 이하) 아니면 둘 다 빈 칸. 말 안 한 칸은 비움.
3. 이미 있는 거래처 이름이면 그대로 돌려줌(앱이 G-2에서 "이미 있는 거래처예요" 처리).
4. 운행·지출·질문·고정·관계없음 그대로. 기록·로그인 확인·실패 문구 그대로.

### 건드릴 파일
- `react-app/supabase/functions/unbiseo-parse/grok.ts` — 지시문(거래처 등록 고르는 법·결제 주기 목록·예시 2개)·칸 모양(`clientName`·`clientManager`·`clientPhone`·`paymentTerm`(열거)·`paymentTermValue`·`commType`(열거)·`commValue`, 필수·null 허용)(149 → 약 170).
- 새 `react-app/supabase/functions/unbiseo-parse/clientDraft.ts`(약 45줄) — 칸 읽기·규칙.
- `react-app/supabase/functions/unbiseo-parse/index.ts` — `client` 돌려주기(178 → 약 183).
- 서버: 데스크톱 AI가 편집기에(새 파일 추가)·지문 확인, 보리 배포, **배포 뒤 Verify JWT 꺼짐 확인**(켜져 있으면 보리 확인 받아 끔).
- 문서(`[x]` 때): sot §4-16 돌려주는 것, 로드맵 24-G 나눔.

### 안 건드릴 것
- 앱 `src/`(G-2), 다른 종류의 칸·지시문, 모델, DB.

### 검증
- 저장소 밖 가짜 시험(터미널 AI): 칸 규칙(전화·결제 주기·수수료 범위·이름 없음 → 관계없음), 다른 종류 그대로.
- 진짜 서버(보리 배포 뒤): Verify JWT 꺼짐 확인 → "거래처 등록 한진 김철수 010-1234-5678 익월 말일 수수료 5%" / "새 거래처 롯데 다음 달 10일 결제" / "거래처 추가 씨제이 건별 30일 후, 수수료 건당 만원" / "거래처 등록"(이름 없음) / "평택 용인 30만 한진"(운행 그대로) + 운행·지출·질문·고정 다시.

### 파일 길이
- `grok.ts` 약 170, 새 `clientDraft.ts` 약 45, `index.ts` 약 183. 200 이하.

### 코드에 넣을 주석(최종 문구)
- `clientDraft.ts`: `// 거래처 등록 칸 읽기 — 이름은 꼭, 연락처·결제 주기·수수료는 앱 목록·범위 안일 때만(로드맵 24-G-1).`

### 실패 시 처리
- 새 저장 장치·재시도 없음(AGENTS §7). 다른 종류가 깨지면 이전 판(`fe3ec4b` 함수)으로 되돌려 올림.

### 진행 기록
- 2026-10-23 AI: react-app `f826fdc`(3개 파일, push 안 함). 확인할 점은 권함대로.
- 줄 수: `grok.ts` 149 → 160, `index.ts` 178 → 185, 새 `clientDraft.ts` 51. SHA-256(LF): `grok.ts` `9cda03d4…`, `index.ts` `5aba485d…`, `clientDraft.ts` `1c59e92a…`(`question.ts`·`fixedRoute.ts` 그대로).
- 교차검증·지시서와 다른 점:
  - **수수료 종류 이름은 `percent`/`direct`**(지시서의 `fixed` 아님) — 앱 거래처 칸(`ClientListPage.jsx:66` `commType: 'direct'`)과 같게 해 G-2에서 그대로 넘김.
  - **AI 답 길이 상한 300 → 500** — 칸이 16개 → 25개(고정 2·거래처 7 추가)라 다 차면 300을 넘을 수 있고, 넘으면 "알아듣지 못함"이 됨. 다른 종류도 같은 상한이라 같이 보호.
  - 결제 주기 값(`paymentTermValue`)은 값이 필요한 주기만(N일 후 1~365, 익월·익익월 N일 1~31) — 당일·말일 주기에 값이 오면 빈 칸.
  - 연락처는 숫자·하이픈만 남긴 뒤(괄호·띄어쓰기 지움) 숫자 9~13자리일 때만.
  - 수수료 `percent`는 소수 허용(3.3%), `direct`는 정수 원만. 종류·값 중 하나라도 틀리면 둘 다 빈 칸.
  - 이름이 없거나 51자 넘으면 관계없음(`unknown`).
  - 지금 앱은 `client` 답을 모름 → 배포 뒤 G-2 전까지 거래처 등록 말에는 "알아듣지 못했습니다"(지금 "운행 등으로 잘못 감"보다 안전).
- 저장소 밖 가짜 시험(scratchpad `24g1/harness.mjs`, 24-A2부터 전부 + 거래처) 통과, 함수 타입 검사 통과. 고치기 전 코드로 돌리면 실패, 복구 확인.
- 데스크톱 AI: 편집기에 새 `clientDraft.ts` 추가·3개 파일 지문 일치 → 보리 배포 → 배포본 지문 5개 파일 모두 커밋과 같음, **Verify JWT 꺼짐 그대로**. CI `f826fdc` 초록(verify·deploy).
- 문장 시험(배포본):
  - 공개 키만 → 401 "로그인이 필요합니다."
  - "거래처 등록 한진 김철수 과장 010-1234-5678 익월 말일 수수료 5%" → client: 한진·김철수·01012345678·익월 말일·percent 5.
  - "새 거래처 롯데 다음 달 10일 결제" → client: 롯데·익월 10일.
  - "거래처 추가 씨제이 건별 30일 후, 수수료 건당 만원" → client: 씨제이·30일 후·direct 10000.
  - "거래처 등록"(이름 없음) → unknown.
  - 운행·지출·질문(달·하루)·고정 노선·잡담 → 전과 같게 맞음.
- 2026-10-23 보리 승인("24-G-1 승인").
- §5 리뷰: 지시서 범위 3개 파일만, 200줄 안(160·185·51), 한 줄 여러 문장 없음, 새 저장·재시도 없음, 문장·답 내용 기록 안 함. 지시서와 다른 점 2개(`percent`/`direct` 이름, 상한 500)는 근거 있어 받아들임. 문제 없음.

### 사용자 확인 방법
- 앱 화면 변화 없음. 보리 할 일: 편집기 "Deploy updates". 결과는 문장 시험 표.

### 다음 24-G-2 미리 보기(이번 범위 아님)
- 앱: 카드 "한진을 거래처로 등록할게요. (담당자 김철수, 010-…, 익월 말일, 수수료 5%)" + [등록하기] → 거래처 관리 화면(차주 `ClientListPage`·연동 기사 `useScopedClients`)의 등록 창이 채워진 채 열림, 저장은 사람. 이미 있는 이름이면 "이미 있는 거래처예요" + [열기]? (착수 때). `unbiseoParse.ts`(191줄) 나누기 함께.

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
