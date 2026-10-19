# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-B 앱 안 데이터 보관(`store/`)과 서버 연결 파일을 `src/data/`로 `[x]`

보리 승인(2026-10-19, 59-A `[x]`). 59번 나누는 방식(아래층부터 9조각, 로드맵 59 `[확인: 2026-10-19 보리]`)의 두 번째 조각. 59-A와 같은 방식(옮기기·경로만, 동작 0, 상대 경로, `git mv`). 경로 줄만 바뀌는 200줄 넘는 파일은 §6 예외(보리 결정 2026-10-19).
플레이북 트리거(`src/store/**`·`supabaseClient`·`lib/cloud*`) 해당 — 저장 경로의 **동작**은 안 바꾸고 파일 위치만 바꿈.

> **진행(2026-10-19)**: 보리 "착수지시서 작업해줘". 코드 커밋 react-app `705afdb`(217개 파일: 옮김 34 + 경로만 고침 183).
> - 교차검증(AGENTS §1) 고친 것: `lib/cloudSession.test.js`가 지시서 목록에 없었음 → 59-A처럼 짝 테스트도 같이 `data/cloud/`로(옮긴 파일 33 → 34). 부르는 파일 수는 지시서 143·57·79와 달리 grep으로 147·62·81(주석·테스트 포함 세는 방식 차이), 대본이 모두 처리. `domain/workDataTombstones.js:3` 주석 속 경로 `store/ownerDataHooks.js`도 `data/store/…`로(동작 아님).
> - 손으로 고친 줄 3개: `lib/failureMessages.test.js:22` 읽는 경로, `package.json` `test:unit` 범위, 위 주석 1줄. 나머지 경로는 59-A와 같은 대본(옮긴 위치 기준 다시 계산, `import`·`import()`·`mock.module`·JSDoc `import(...)`).
> - 목표 3: 옛 경로 grep 0건(걸린 2줄은 `data/cloud/` → `../store/app-store.js`로 새 위치가 맞음). 테스트 지원 파일 주석은 파일 이름만 적혀 있어 그대로 둠.
> - 빌드 비교: 옮기기 전·후 `npm run build` 결과 48개 파일(JS 33 포함, 이름 해시 뺌) **내용이 모두 글자 그대로 같음** — 묶음 안 동작 변화 0.
> - `git diff -M`: 34개 모두 이름 바뀜으로 잡힘(2개 100%, 32개는 안의 경로 줄만), 바뀐 줄 561개 중 경로 아닌 줄 0(위 3줄도 경로).
> - `npm test` 1004 + 499 통과(같은 수), 타입 검사·빌드 통과.
> - 개발 서버(비회원): 차량·거래처 추가, 일지 한 건 저장 → 새로고침 뒤 셋 다 그대로, 콘솔 오류 없음.
> - 메모(59-H): `data/cloud/supabaseClient.js`가 `app/routerBasename.js`를 부름(아래층 → 앱 틀), 이번엔 경로만.
> - 지시서 오류(데스크톱 AI): 짝 테스트 `lib/cloudSession.test.js`를 옮길 목록에서 빠뜨림 — 교차검증이 채움(33 → 34).
> - push 뒤 CI verify·deploy 초록(run 39250757845).
> - §5 리뷰(데스크톱 AI): ①범위 = 옮김 34(모두 이름 바뀜) + 경로만 183 ②새 파일 0, 새 저장 키 0 ③타입 꼼수 0 ④줄 수 그대로(§6 예외 결정대로) ⑤테스트 수 그대로, 테스트 바뀐 줄은 경로뿐(가짜 서버 연결 `testSupport/stubSupabaseClient.js:65`도 새 경로) ⑥공용 값 — 해당 없음 ⑦목표 1~5 대조. 경로 아닌 바뀐 줄은 `package.json` `test:unit` 1줄·주석 1줄(+ `guestBackup.js`의 빈 줄이 같은 덩어리 안에서 빠졌다 다시 들어간 것 — 실제 변화 없음). `src/` 맨 위는 `main.jsx`뿐, `src/store/` 없음.
> - 배포본 로그인 확인(2026-10-19, 내장 브라우저): **묶음 파일 이름이 59-A 배포와 같음**(`index-BH1dhE45.js`, 디자인 `index-WIA-vpHP.css` — 빌드 결과가 글자까지 같다는 교차검증 결과와 맞음). 로그인 유지, 차량 3·거래처 1·연동 기사 1 그대로, 오늘 일지 2회 저장 → 새로고침 뒤 그대로 → 비워서 되돌림, 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-19).

### 쉬운 요약
- 앱 안 데이터 보관 장치(`store/` 폴더)와 서버 연결 파일 3개(`supabaseClient.js`·`cloudSession.js`·`cloudStorage.js`)를 새 `data/` 폴더 아래로 모읍니다.
- 파일 위치와 부르는 경로만 바뀌고, 저장·불러오기·로그인 동작은 그대로입니다.

### 현재 상태 (코드 확인)
- `src/store/` 30개(운영 17 + 테스트 13) — 부르는 파일 143개(grep `store/`, 같은 폴더 안 제외).
- `src/supabaseClient.js` — 부르는 파일 57개. 안에서 `./app/routerBasename.js`를 부름(`:3` — 아래층이 앱 틀을 부르는 곳, 이번엔 경로만).
- `src/lib/cloudSession.js` — 부르는 파일 79개. 안에서 `../store/app-store.js`·`./singleFlight.js`·`./outboxErrors.js`·`./failureMessages.js`를 부름(`:5-8`).
- `src/lib/cloudStorage.js` — 부르는 파일 5개.
- 위 넷을 부르는 파일을 합치면 183개(겹침 뺌). 테스트 가짜 모듈: `testSupport/stubSupabaseClient.js:65` `mock.module('../supabaseClient.js', …)`, 각 테스트의 `mock.module('…/store/…')`·`import('…/supabaseClient.js')` 등 경로 문자열.
- 경로로 읽는 테스트: `lib/failureMessages.test.js:22` `new URL('./cloudSession.js', import.meta.url)`.
- 테스트 실행 범위: `package.json` `test:unit`에 `src/store/**`(`:13`).

### 목표 상태 (기대 동작)
1. `src/data/store/` ← `src/store/` 30개 전부(테스트 포함, 이름 그대로). `src/data/cloud/` ← `supabaseClient.js`·`cloudSession.js`·`cloudStorage.js`(이름 그대로).
2. `src/store/` 폴더 없어짐. `src/` 맨 위 파일은 `main.jsx`뿐.
3. 옮긴 파일을 부르는 모든 경로(`import`·`import()`·`mock.module`·JSDoc `import(...)`·경로로 읽는 테스트)가 새 위치를 가리킴. 옛 경로 grep 0건: `/store/`(단 `data/store/` 제외), `src/supabaseClient`·`'(../)*supabaseClient.js'` 꼴(단 `data/cloud/` 제외), `lib/cloudSession`·`lib/cloudStorage`·`'./cloudSession.js'`(lib 안).
4. `package.json` `test:unit`의 `src/store/**/*.test.js` → `src/data/**/*.test.js`.
5. 테스트 수 그대로(1004 + 499), 전부 통과. 동작 줄 변화 0.

### 건드릴 파일
- 옮김(`git mv`): 33개(위 1번).
- 경로만 고침: 부르는 파일 약 183개 + 옮긴 파일 안의 상대 경로 + `testSupport/stubSupabaseClient.js`·`fakeSupabaseClient.js`(주석 속 경로 포함) + `lib/failureMessages.test.js:22` + `package.json` 1줄.
- 동작 코드·문구는 한 글자도 안 바꿈. 59-A처럼 경로 다시 계산은 대본으로 하고, 손으로 고친 줄은 진행 기록에 모두 적음.

### 안 건드릴 것
- `supabaseClient.js`가 `app/routerBasename.js`를 부르는 것(아래층 → 앱 틀) — 이번엔 경로만 고치고 그대로. 의존 방향 정리는 59-H(앱 틀 정리) 때 — 진행 기록에 메모.
- `cloudSession.js`가 부르는 `lib/singleFlight`·`outboxErrors`·`failureMessages` — 59-C(`outboxErrors` 이름은 88-B와 같이)·59-H에서 옮김, 이번엔 경로만.
- `lib/`의 나머지(불러오기·저장 창구) — 59-C. 화면 폴더 — 59-D~G.
- 저장 키·저장 순서·로그인 흐름 등 동작 일체.

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 3 grep 0건을 진행 기록에.
- `git diff -M --stat`: 33개가 이름 바뀜(100% 또는 안의 경로 줄만)으로 잡히는지, 경로가 아닌 바뀐 줄 목록(있으면 전부)을 진행 기록에.
- 빌드 결과 비교: 옮기기 전·후 `npm run build`의 JS 결과에서 모듈 경로 문자열 말고 달라진 게 없는지(가능한 만큼) — 최소한 디자인 결과 같음 + 묶음 파일 수 같음을 진행 기록에.
- 개발 서버(비회원): 차량·거래처 추가, 일지 저장 → 새로고침 뒤 그대로, 콘솔 오류 없음. 로그인 흐름은 배포본에서 AI가 확인(아래).

### 파일 길이
- 옮기기만 해서 줄 수 그대로(경로 줄 글자만). 200줄 넘는 파일은 경로 줄만 바뀜 — §6 예외(보리 결정).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(파일 위치·경로만).
- 테스트가 하나라도 실패하면(특히 가짜 모듈 경로를 못 찾아 진짜 서버 연결을 부르는 경우) 멈추고 어느 경로인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 앱 켜기 → 로그인 유지, 차량·거래처·기사 목록 그대로.
2. 오늘 일지 한 건 저장 → 새로고침 뒤 그대로 → 되돌림.
3. 콘솔 오류 없음.
