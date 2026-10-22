# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 94 `.ts` 전환 — 나누는 계획 (94-A·B `[x]`, 다음 94-C)

지난 지시서·진행 기록: 94-A drivelog-docs `3ee010c`, 94-B `90ef647`(`git show <해시> -- docs/report.md`).

### 나누는 계획 (59번처럼 폴더 단위, 아래층부터)
| 조각 | 범위 | 운영 파일 | 비고 |
|---|---|---|---|
| ~~94-A~~ `[x]` | 설정 + 시범 2개 | 2 | react-app `a009f2b` |
| ~~94-B~~ `[x]` | `domain/` | 58 | react-app `855fb6a` |
| **94-C** | `data/` | 81 (6,365줄) | 저장·동기화 — 플레이북 트리거 |
| 94-D | `shared/` + `app/` + `main.jsx` + `testSupport/` | 69 | 출시 전 순서 뒤 |
| 94-E | `features/` 일지·달력·지출 | 39 (3,088줄) | 출시 전 순서 뒤 |
| 94-F | `features/` 서류·미수금·매출 | 38 (3,490줄) | 출시 전 순서 뒤 |
| 94-G | `features/` 나머지 8개 | 64 (5,767줄) | 출시 전 순서 뒤. 마무리: `allowJs` 끄기, `// @ts-check` 규칙 정리 |

- **반만 먼저** `[확인: 2026-10-22 보리 — "그럼 반만, 응"]`: 94-C를 지금, 94-D~G는 출시 전 순서(23·24 → 34 → 79) 뒤.
- 바꾸는 방법은 94-B와 같이 AI가 손으로 `[확인: 2026-10-22 보리 — "가로해"]`(94-B 결정, 94-C에서 다르게 하려면 지시서에서 다시 물음).
- 모든 조각의 공통 증명: 빌드 결과 파일이 이름 해시까지 옛 커밋과 같음 + 테스트 수 그대로 전부 통과 + 타입 검사·의존 검사 통과 + 타입 설명 파일(`.d.ts`, 저장소 밖에서 뽑음) 비교 + 의존 검사의 옛 연결은 확장자 말고 그대로(JSDoc `import('…')`이 `import type`이 되면 타입 전용 연결이 새로 셈에 들어옴 — 늘어난 것이 그것뿐인지 확인). 다르면 멈추고 보고.
- 테스트 파일은 `.js` 그대로, 가져오는 경로의 확장자만 바뀜. 밖 파일은 경로 끝을 되돌리면 옛 파일과 같은지 기계로 비교.
- JS의 `@typedef`는 저절로 공개되지만 TS `type`은 `export`가 있어야 공개됨 — 밖에서 쓰던 이어받기 이름은 `export type { … }`로 남김(94-B에서 3개).
- `// @ts-check` 없는 운영 파일 중 `data/`에 2개(`data/cloud/singleFlight.js`·`data/store/useHydrationLock.js`), 94-D에 4개(`app/guestSessionPersist.js`·`app/routes/ComingSoonRoute.jsx`·`app/routes/lazyPages.js`·`app/routes/sessionGate.js`) — `.ts`로 바뀌면 처음 타입 검사를 받아 오류가 나올 수 있음.

---

## 94-C `data/`를 `.ts`로 `[x]` `[확인: 2026-10-22 보리 — "승인, 로드맵에 등록해"]` (CI 테스트 1개 빠짐은 로드맵 103번)

> **진행(2026-10-22, 터미널 AI)** `[~]` 81개 바꿈·검사 끝, **빌드 결과 해시가 달라 멈춤(아래 수정 착수지시서)**, 커밋 안 함.
> - 교차검증: 지시서 사실(81개 6,365줄, 테스트 52개, `@ts-check` 없는 2개, 클래스 2개, `as const` 목록 11개, `.d.ts` 81개 2,780줄, 밖 167개·테스트 52개)이 코드와 맞음. 다른 점 1개: "괄호 형 변환 133곳"은 실제 91곳(여러 줄 타입 포함, 파일마다 셈).
> - 한 일: 81개를 손으로 `.ts`로, 긴 주석 18곳 결론 1~2줄로, 경로 724곳(219개 파일 — 밖 167 + `data/` 테스트 52) + `domain/` 주석 속 파일 이름 15곳을 저장소 밖 스크립트로(옛 `.js`가 없고 `.ts`가 있는 `src/data` 파일만). 밖에서 쓰던 이어받기 이름 `cloudSession`의 `AppSession`·`SessionCapture`를 `export type`으로 남김(타입 검사가 잡음).
> - 결과: 타입 검사 오류 0, `npm test` unit 878·app 663 전부 통과(수 그대로, `mock.module` 경로 모두 실제 파일), strict-inventory 445 → 445, lint 50 그대로, depcheck 위반 0. grep 옛 경로 0, `data/`에 `@ts-check`·`@typedef` 0, 금지 표기 0(주석 낱말만). 새 `as` 91곳 = 옛 JSDoc 괄호 형 변환 91곳(파일마다 같음). 바꾼 파일 모두 전보다 짧거나 같음. 클래스 2개 필드 선언 안 함.
> - 의존 검사: 옛 연결 1,445개 전부 그대로, 새 연결 155개 전부 타입 전용, 모듈 422개 그대로.
> - 타입 설명 비교: 진짜 타입 차이 0. 표기 차이와 밖에서 안 쓰던 이어받기 이름 144개 빠짐(`ProfileLike` 같은 다른 이름 별칭 포함)뿐.
> - 코드 커밋 react-app `079aee8`(2026-10-22, 보리 "권함으로" 뒤, 315개 파일, push 안 함). 남은 것: 보리 push → CI → §5 리뷰 → 최종 승인.
> - (데스크톱 AI) push 뒤 CI verify·deploy 초록(run 39736913821). 의존 검사 422 모듈·1,619 연결(94-B 1,448 + 타입 전용 171 — 터미널 AI 셈 155와 다름, 이 PC 확인 안 함·위반 0). 지시서의 "괄호 형 변환 133곳"은 데스크톱 AI가 테스트 파일까지 센 잘못 — 실제 91곳이 맞음.
> - **CI 테스트 수 877·663**(이 PC 878·663): 빠진 것은 `domain/finance.test.js`의 맨 마지막 테스트 "산재보험료가 없으면 0이고 합계는 그대로" — CI 기록에 그 묶음의 1·2번만 찍히고 3번·묶음 끝 줄 없이 다음 파일로 넘어감(실패로도 안 셈). 이 파일은 이번에 안 바뀜, 이 PC에서 878 통과. 지난 CI에도 같은 일 있음(`de91e4d` 876, 앞뒤 커밋 878) → 이번 변경 때문이 아니라 원래 있던 문제(추정: 테스트 명령의 `--test-force-exit`가 파일 마지막 결과를 내보내기 전에 끝냄 — 확인 안 함).
> - 빌드 결과 다시 확인(데스크톱 AI, 저장소 밖에 `855fb6a`·`079aee8`를 풀어 빌드): 두 빌드 이름이 각 CI와 똑같이 재현됨. 압축 안 한 빌드 JS 34개 모두 주석·공백·해시 참조를 빼면 같음, 압축 빌드는 2개(`index`·`directMutationActions`)만 변수 이름이 다르고 이름을 지우면 34개 모두 같음 → 터미널 AI 증거와 같음.
> - §5 리뷰: ①범위 — 315개 = `data/` 81(이름 바꿈 66 + 지움·새로 15쌍) + 경로만 바뀐 219(밖 167·`data/` 테스트 52). 경로 끝을 같은 글자로 바꿔 비교하면 남는 차이는 주석 속 파일 이름뿐(`domain/` 10개·`boot.js`·`DriverConnectionPage.jsx`) ②증설 — 새 저장 장치·저장 키·의존성 없음 ③타입 꼼수 — 추가된 줄에 금지 표기 0, `as`는 파일마다 옛 괄호 형 변환 수와 같음(`persistDomainEnums` `as const` 10) ④200줄 — 늘어난 파일 0, 200줄 넘는 파일 0 ⑤테스트 — 경로만, `mock.module` 지정 66곳 모두 실제 파일 ⑥공용 — 공개 타입은 `.d.ts` 비교 + `export type` 재공개 ⑦기대 동작 — 6번 "빌드 결과 해시까지 같음"은 보리 "권함으로"로 위 증거로 대신함, "테스트 수 그대로"는 이 PC 878·CI 877(위 원인).

### 수정 착수지시서 — 빌드 결과 해시가 다름 `[확인: 2026-10-22 보리 — "권함으로"]` 가(증거로 받아들이고 커밋)
- **무엇이 다른가**: 빌드 결과 JS 34개 중 2개(`index`, `directMutationActions`)의 압축된 변수 이름이 달라짐(예: `e_` → `e2`, 가져온 이름 `v`·`y` 자리 바뀜). 나머지는 그 두 조각의 이름(해시)을 가리키는 글자만 바뀜.
- **실행 코드가 같다는 증거**(저장소 밖 같은 깊이 폴더에 옛·새 코드를 풀어 빌드): ① 압축을 끈 빌드는 34개 JS 모두 주석·공백·괄호를 빼면 글자까지 같음. ② 압축된 빌드도 이름(식별자)을 모두 지우면 34개 모양이 전부 같음. 차이는 옛 JSDoc 형 변환 주석이 압축 전 코드에 남아 있던 것뿐(추정 원인: 압축기가 이름을 고를 때 주석 글자도 세는 것 — 확인 안 함).
- **권함: 가. 위 증거로 받아들이고 커밋** — 앱이 실제로 돌리는 코드는 같고, 94-D~G에서도 JSDoc 형 변환이 있는 폴더마다 같은 일이 날 것이라 그때마다 같은 방법(압축 끈 빌드 비교 + 이름 지운 모양 비교)으로 증명.
- 나. 해시까지 같게 만들 방법을 더 찾음(원인 확인부터 — 시간 듦, 얻는 것 없음).
- 건드릴 파일·검증: 가면 없음(이대로 커밋), 나면 정해지지 않음.

보리 지시 "작성해"(2026-10-22, 94-B `[x]` 뒤). 나누는 계획의 세 번째 조각, 94-B와 같은 방식(손으로). **플레이북 트리거 전부 해당**(`src/store/**`→`data/store/**`, `supabaseClient`, `localStorage` 직접 14개 파일) — `docs/testing-playbook.md` 확인함: 실행 코드가 안 바뀌는 작업이라 §1~§7(저장 실패·세대·되돌려 실패 확인)은 새로 할 것 없음(빌드 결과가 같다는 것으로 증명), §8은 아래 검증에 반영.

### 쉬운 요약
- 저장·불러오기·서버 연결 폴더(`data/`)의 파일 81개를 `.ts`로 바꾸고, 타입 설명을 주석이 아닌 코드 표기로 옮깁니다. 4줄 넘는 설명 주석 18곳은 결론 1~2줄로 줄입니다.
- 이 폴더를 가리키는 다른 파일 약 220개는 경로 끝(`.js` → `.ts`)만 바뀝니다. 테스트가 서버 연결을 가짜로 바꿔 끼우는 자리(70곳)도 같이 바뀝니다.
- 앱 동작은 그대로입니다. 저장·동기화 코드라 더 조심해야 하지만, 빌드 결과가 옛 커밋과 글자까지 같으면 실행되는 코드가 하나도 안 바뀐 것이므로 그것으로 증명합니다.

### 현재 상태 (코드 확인, react-app `855fb6a`)
- `data/` 운영 파일 81개 모두 `.js`(6,365줄, `.jsx` 없음): `cloud/` 7·`hydrate/` 10·`store/` 20·`sync/` 44. 테스트 52개. 주석 줄 1,954개 중 타입 표기 줄 939개, `@typedef` 204개, 괄호 형 변환 133곳(그중 `/** @type {const} */` 11곳), `@template` 9곳(grep).
- `// @ts-check` 없는 2개(`cloud/singleFlight.js`·`store/useHydrationLock.js`) — 저장소 밖 임시 설정으로 타입 검사를 켜 보니 오류 0(2026-10-22).
- 클래스 2개(`sync/saveErrors.js` `StaleSessionError`·`PermanentFailureError`) — 필드 선언 없이 생성자에서 `this.name`만 씀.
- `store/persistDomainEnums.js`의 `/** @type {const} */ ([...])` 목록 11개(옛 저장분 읽기용 값 포함 — 지우지 않음).
- `localStorage`를 직접 읽거나 쓰는 파일 14개, Valibot 검사 규칙 파일 3개(`store/persistDomainSchema.js`·`store/callDetailSchema.js`·`sync/durablePatchSchema.js`).
- `data/` 밖에서 `data/<폴더>/<이름>.js`를 적은 파일 167개(운영 95·테스트 72, 454줄 — `domain/` 주석 15줄 포함). `data/` 안 테스트 52개 모두 `data/` 경로를 씀. `mock.module(…)` 지정 70줄(대부분 `supabaseClient.js`, 그 밖 `dailyInspections`·`ownerDataHooks`·`cloudSession`·`invoicesSave`·`expensesSave`·`clientCloudSave`).
- 4줄 넘는 `//` 설명 주석 18곳(시작 줄): `hydrate/hydrateEmployedDriver.js:1·6`, `hydrate/hydrateMerge.js:88`, `hydrate/hydrateMergeCars.js:44·71·91`, `hydrate/hydrateMergeClients.js:57`, `hydrate/hydrateMergeTypes.js:17`, `sync/dayLogCloudCommit.js:1`, `sync/directMutationActions.js:5`, `sync/directMutations.js:15`, `sync/durableStorage.js:41`, `sync/ownerSaveQueue.js:1`, `sync/pendingRetryPulse.js:1`, `sync/pendingWorkDataWrites.js:1·121`, `sync/requestDriverInviteSave.js:1`, `sync/vehicleDeletion.js:1`.
- 타입 설명 비교 시험: 저장소 밖 임시 설정으로 `data/`의 `.d.ts` 81개(2,780줄)를 오류 없이 뽑음(2026-10-22, 파일 변경 없음).

### `AGENTS.md` §8 질문 (리팩토링 전)
- 1~4(구독/스냅샷, 보이는 값이 어디 것인지, 쓰기 창구, hydrate·디바운스 경합): **전부 그대로** — 함수·호출 순서·저장 키를 안 바꾸고 표기만 바꿈, 빌드 결과가 같다는 것으로 증명. 5(DB 권한): DB 안 건드림.

### 목표 상태 (기대 동작)
1. `data/` 운영 파일 81개가 `.ts`(TS 표기, `// @ts-check` 줄 없음). `@typedef` → `export type`, `@param`·`@returns` → 표기, `@template` → `<T>`, 선언 위 `/** @type {X} */` → `const x: X`, 괄호 형 변환 → `y as X`, `/** @type {const} */ ([...])` → `[...] as const`, 타입만 가져오면 `import type`. `AGENTS.md` §6 금지 표기와 `any`·`unknown`·`@ts-ignore` 없음.
2. **새 형 변환 없음** — `as`는 옛 JSDoc 형 변환이 있던 자리에만(특히 `localStorage`·서버 값을 받는 곳에 새로 단언하지 않음, `AGENTS.md` §6 외부 경계 규칙). 클래스 2개는 필드 선언을 새로 쓰지 않음(쓰면 빌드 결과가 바뀜).
3. 18곳의 긴 설명 주석은 결론 1~2줄로 — "왜 이렇게 해야 하나(안 하면 무엇이 깨지나)"는 남김. 함수 위 한 줄 설명은 그대로.
4. `src/` 안의 `data/<폴더>/<이름>.js` 경로 → `.ts`(실행 가져오기, JSDoc `import('…')`, `mock.module(…)` 지정, `domain/` 주석 속 파일 이름 포함). 실제 `src/data/` 파일을 가리키는 것만(예: `'html2pdf.js'`·`'./memberRestoreRecords.js'`는 안 바뀜). `data/` 밖 파일은 이 바꿈 말고 다른 줄이 안 바뀜.
5. 타입 설명 파일이 옛 것과 같음 — 주석·경로 확장자를 지우고 비교, 남는 차이는 하나씩 보고(표기 차이 / 밖에서 안 쓰던 이어받기 이름 빠짐은 94-B와 같이 허용, 밖에서 쓰던 것은 `export type { … }`로 남김). 타입이 넓어지거나 좁아진 것은 멈추고 보고.
6. 빌드 결과 파일 이름 해시까지 옛 커밋과 같음. 테스트 수 그대로 전부 통과, 타입 검사 통과, strict-inventory 오류 수 445 이하, lint 경고 50 이하, 의존 검사 위반 0이고 옛 연결은 확장자 말고 그대로(늘어난 것은 `import type`이 된 타입 전용 연결뿐).

### 건드릴 파일
- `src/data/**/*.js` 81개 → `.ts`
- `src/data/**/*.test.js` 52개 — 경로 확장자만(가짜 모듈 지정 포함)
- `data/` 밖 167개(운영 95·테스트 72, `testSupport/` 3개·`domain/` 주석 포함) — 경로 확장자만(목록은 grep `data/(cloud|hydrate|store|sync)/[A-Za-z.-]+\.js`)
- §3 "1~3개 파일" 예외: 94 나누는 계획 승인으로 폴더 단위(94-B와 같음).

### 안 건드릴 것
- `data/` 밖 파일의 경로 말고 다른 줄, 테스트 파일 확장자, 설정 파일, 함수 이름·인자·호출 순서, 저장 키·저장 형식·옛 저장분 읽기용 값(`persistDomainEnums`), DB, `features/` 이후 폴더(94-D~G).

### 검증
- 바꾸기 전 저장소 밖에 저장: 빌드 결과 파일 해시, 의존 검사 json, strict-inventory 오류 수, lint 경고 수, `data/` 타입 설명 파일(94-B와 같은 임시 설정, `data/` 운영 파일만).
- 바꾼 뒤 비교(목표 5·6). `npm test`·`npm run typecheck`·`npm run typecheck:strict-inventory`·`npm run build`·`npm run depcheck`·`npm run lint`.
- 가짜 모듈: 모든 `mock.module('…')` 지정이 실제 있는 파일을 가리키는지 확인(옛 `.js` 이름이 남으면 가짜가 안 끼워져 진짜 서버 연결 코드가 돌 수 있음). 테스트 수가 같고 전부 통과.
- `data/` 밖 파일과 `data/` 테스트 diff가 경로 확장자 바꿈뿐인지 기계로 확인(바뀐 경로를 `.js`로 되돌리면 옛 파일과 같음).
- 형 변환: 새 `as` 수 = 옛 괄호 형 변환 수 133(`as const` 11 포함), 자리별로 대조.
- grep: `data/(cloud|hydrate|store|sync)/[A-Za-z.-]+\.js` 0, `data/`에 `@ts-check`·`@typedef` 0.
- 플레이북 §8: 검사 대상에서 파일을 빼서 오류 0을 만들지 않음, Valibot 검사 규칙과 타입이 같은 모양 그대로.

### 파일 길이
- 지금 190줄 넘는 파일: `hydrate/hydrate.js` 199, `sync/dayLogCloudCommit.js` 197 — 타입 표기 줄이 빠져 줄어들 것. 200줄 넘는 파일 없음.
- 규칙: 바꾼 파일은 바꾸기 전보다 길어지지 않음. 길어지거나 200줄을 넘으면 멈추고 보고.

### 코드에 넣을 주석(최종 문구)
- 새 주석 없음. 18곳 줄인 문구는 그 자리의 결론 문장을 그대로 남김(새로 쓰지 않음).

### 실패 시 처리
- 새 저장 장치·층 없음. 진짜 타입 차이·빌드 결과 차이·가짜 모듈이 안 끼워지는 테스트가 나오면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
- 화면에서 볼 것은 없음(빌드 결과가 같으면 앱이 글자 하나까지 같음). CI 초록 + 빌드 결과 파일 이름이 직전 커밋과 같은지는 AI가 CI 기록으로 확인해 보고.
