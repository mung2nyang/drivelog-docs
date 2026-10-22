# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 94 `.ts` 전환 — 나누는 계획 (94-A `[x]` react-app `a009f2b`, 다음 94-B)

지난 지시서·진행 기록은 drivelog-docs `3ee010c`(`git show 3ee010c -- docs/report.md`).

### 나누는 계획 (59번처럼 폴더 단위, 아래층부터)
| 조각 | 범위 | 운영 파일 | 비고 |
|---|---|---|---|
| ~~94-A~~ `[x]` | 설정 + 시범 2개 | 2 | react-app `a009f2b` |
| ~~94-B~~ `[x]` | `domain/` | 58 | react-app `855fb6a` |
| 94-C | `data/` | 81 (6,365줄) | 저장·동기화 — 플레이북 트리거 |
| 94-D | `shared/` + `app/` + `main.jsx` + `testSupport/` | 69 | 출시 전 순서 뒤 |
| 94-E | `features/` 일지·달력·지출 | 39 (3,088줄) | 출시 전 순서 뒤 |
| 94-F | `features/` 서류·미수금·매출 | 38 (3,490줄) | 출시 전 순서 뒤 |
| 94-G | `features/` 나머지 8개 | 64 (5,767줄) | 출시 전 순서 뒤. 마무리: `allowJs` 끄기, `// @ts-check` 규칙 정리 |

- **반만 먼저** `[확인: 2026-10-22 보리 — "그럼 반만, 응"]`: 94-B·C를 지금, 94-D~G는 출시 전 순서(23·24 → 34 → 79) 뒤.
- 모든 조각의 공통 증명: 빌드 결과 파일이 이름 해시까지 옛 커밋과 같음 + 테스트 수 그대로 전부 통과 + 타입 검사·의존 검사 통과 + 의존 검사 모듈·연결 수가 바꾸기 전과 같음. 다르면 멈추고 보고.
- 테스트 파일은 `.js` 그대로, 가져오는 경로의 확장자만 바뀜.
- 94-B 지시서에서 물을 것: "JSDoc 타입을 TS 표기로 자동 변환하는 도구"를 쓸지(외부 도구).
- 94-B에서 확인할 것: CI(리눅스)에서 swc가 `.ts`를 실제로 읽는지 — `domain/` 파일이 가져오는 연결 수가 바꾸기 전과 같으면 확인됨.
- `// @ts-check` 없는 운영 파일 6개(`app/guestSessionPersist.js`·`app/routes/ComingSoonRoute.jsx`·`app/routes/lazyPages.js`·`app/routes/sessionGate.js`·`data/cloud/singleFlight.js`·`data/store/useHydrationLock.js`)는 `.ts`로 바뀌는 조각(94-C·D)에서 처음 검사받아 타입 오류가 나올 수 있음.

---

## 94-B `domain/`을 `.ts`로 `[x]` `[확인: 2026-10-22 보리 — "94-B [x] 승인. 마무리해."]`

> **진행(2026-10-22, 터미널 AI)** `[~]` 58개 바꿈·검사 끝, **진짜 타입 차이 1곳이 나와 멈춤(아래 수정 착수지시서)**, 커밋 안 함.
> - 교차검증: 지시서 사실(운영 파일 58개 5,447줄, 테스트 36개, 긴 주석 11곳, `.d.ts` 59개 3,250줄)이 코드와 맞음. "사용자 확인 방법"의 빌드 결과 파일 "49개"는 실제 62개(94-A 기록과 같음).
> - 한 일: 58개를 손으로 `.ts`로(`export type`·표기·`<T>`·`as`·`import type`), 긴 주석 11곳 결론 1~2줄로, 경로 바꾸기는 저장소 밖 스크립트로 685곳(257개 파일) — 따옴표 안 상대 경로가 실제 `src/domain/<이름>.ts`를 가리킬 때만.
> - 결과: 타입 검사 오류 0, `npm test` unit 878·app 663 전부 통과(수 그대로), **빌드 결과 파일 62개 해시까지 옛 커밋과 같음**, strict-inventory 445 → 445, lint 50 그대로, depcheck 위반 0. grep 옛 경로 0, `@ts-check`·`@typedef` 0, 금지 표기(`enum`·`namespace`·`!`·`as unknown as`·`any`·`unknown`·`@ts-ignore`) 0. 바꾼 파일 모두 전보다 짧거나 같음(`clients` 232 → 184, `financeCore` 212 → 187 — 200줄 넘는 파일 없어짐).
> - 의존 검사: 옛 모듈·연결 1,784개는 확장자 말고 전부 그대로(→ swc가 `.ts`를 읽음). 새 연결 83개는 전부 `domain/` 안의 타입 전용(JSDoc `import('…')`이 `import type`이 되어 도구가 이제 셈) — 목표 5 "연결 수 같음"과 다름, 이 이유로만 다름.
> - 타입 설명 비교: 표기 차이(줄바꿈, `import()` ↔ `import type`) 말고 ① 아무도 안 쓰던 이어받기 이름(`/** @typedef {import(…).X} X */`가 JS에선 저절로 공개되던 것) 빠짐 — 밖에서 쓰던 3개(`calendarBadges`·`day-record`의 `DayRecordLike`, `clients`의 `ClientLike`)는 `export type { … }`로 남김, 타입 검사로 확인. ② **진짜 차이 1곳**: 아래.
> - 코드 커밋 react-app `855fb6a`(2026-10-22, 보리 "권함으로" 뒤, 330개 파일 — 이름 바꿈 58·경로만 257·새 타입 표기, push 안 함). 남은 것: 보리 push → CI(리눅스 swc가 `.ts` 읽는지 — 의존 검사 위반 0·연결 수로 확인) → §5 리뷰 → 최종 승인.
> - 남은 `@param` 16줄은 타입 없이 설명만(예: `@param month 0부터`) — 지시서 grep "`@param` 0"과 다름. 권함: 그대로(TS 설명 표기).
> - (데스크톱 AI) push 뒤 CI verify·deploy 초록(run 39731225292, Node 22.23.3). 테스트 878·663 그대로, 빌드 결과 파일 49개 이름(CI 기록에 나오는 해시 붙은 파일 — 62개는 해시 없는 파일 포함한 전체)이 94-A CI와 전부 같음.
> - 의존 검사: CI·이 PC 모두 422 모듈·1,448 연결(94-A 1,362). 늘어난 86개는 전부 `domain/` 안의 타입 전용 연결(위 "83개"는 잘못 센 것 — 이 PC json으로 다시 셈). 리눅스에서도 같은 수 → swc가 `.ts`를 읽음 확인.
> - §5 리뷰: ①범위 — 330개 = `domain/` 운영 58(새 `.ts`, git이 이름 바꿈으로 잡은 43 + 지움·새로 15쌍) + `domain/` 테스트 35 + 밖 222. 밖 222개·`domain/` 테스트 35개는 `domain/` 경로 확장자를 되돌리면 옛 파일과 글자까지 같음(기계 비교). ②증설 — 새 저장 장치·의존성 없음 ③타입 꼼수 — 추가된 줄에 `any`·`unknown`·`@ts-ignore`·`!`·`enum`·`namespace` 없음, `as` 형 변환 31곳은 모두 옛 JSDoc 형 변환 자리(표본 9곳 옛 코드와 대조) ④200줄 — 늘어난 파일 0, 200줄 넘는 파일 0(`domain/` 5,447 → 4,431줄) ⑤테스트 — 경로만, 수 그대로 ⑥공용 — 공개 타입은 `.d.ts` 비교 + 재공개 3개(`export type { … }`) ⑦기대 동작 1~5 충족(4의 `CustomSchema<any>` → `<unknown>`과 설명용 `@param`은 보리 "권함으로"로 그대로). 4줄 넘는 `//` 주석 0(3줄짜리 5곳은 원래 있던 것, 11곳 범위 밖).

### 수정 착수지시서 — `schemaParts.ts` 검사 조각 입력 타입 `[확인: 2026-10-22 보리 — "권함으로"]` 가(그대로), 남은 설명용 `@param`도 그대로
- **무엇이 다른가**: `by`·`plain`이 만드는 Valibot 검사 조각의 입력 타입이 `CustomSchema<any>` → `CustomSchema<unknown>`. `v.custom(…)`에 타입을 안 적었을 때 JS는 `any`로, TS는 `unknown`으로 채운다. 실행 코드·빌드 결과·테스트는 같음.
- **고칠 수 있나**: `v.custom<JsonValue>(…)`로 적어 보면 `plain`의 `v.pipe`에서 타입 오류 1개(시험 후 원래대로 되돌림). `any`로 맞추려면 `any`를 직접 써야 해서 §6 금지.
- **권함: 가. 그대로 둠** — 더 엄격해진 쪽(좁아짐)이고, 글자로 쓴 `unknown`이 아니라 라이브러리 기본값. 기록만 남김.
- 나. 다른 방법을 찾아 `any`와 같은 결과로 맞춤(지금은 방법 없음).
- 건드릴 파일·검증: 가면 없음(이대로 커밋), 나면 `schemaParts.ts` 한 곳 + 타입 검사·테스트·빌드 비교 다시.

보리 지시 "지시서 작성해"(2026-10-22, 94-A `[x]` 뒤). 위 나누는 계획의 두 번째 조각. 플레이북 트리거(`domain/finance*`·`domain/receivables*`) — `docs/testing-playbook.md` §8 확인함(아래 검증에 반영). 저장·동기화 코드는 안 건드림(`domain/`은 계산만, 가져오는 것은 `domain/` 안과 `valibot`뿐 — grep).

### 결정 — 바꾸는 방법 `[확인: 2026-10-22 보리 — "가로해"]`
- **AI가 파일마다 손으로 바꿈.** 자동 변환 도구는 안 씀(TypeScript 5 내장 변환을 저장소 밖에서 돌리는 안은 형 변환 32곳·`@template` 13곳·`export type`·`import type`을 어차피 손봐야 해서 뺌). 타입이 몰래 바뀌는 위험은 아래 "타입 설명 파일 비교"로 잡음.

### 쉬운 요약
- 계산 규칙 폴더(`domain/`)의 파일 58개를 `.ts`로 바꾸고, 타입 설명을 주석이 아닌 코드 표기로 옮깁니다. 긴 설명 주석 11곳은 결론 1~2줄로 줄입니다.
- 이 폴더를 가져다 쓰는 파일 약 260개는 가져오는 경로의 끝(`.js` → `.ts`)만 바뀝니다.
- 앱 동작은 그대로입니다. 빌드 결과가 옛 커밋과 같고, 타입 설명도 옛 것과 같은 것으로 증명합니다.

### 현재 상태 (코드 확인, react-app `a009f2b`)
- `domain/` 운영 파일 59개 중 58개가 `.js`(5,447줄), `smsHref.ts`만 `.ts`. 테스트 36개. 주석 줄 1,925개 중 타입 표기 줄 1,133개, `@typedef` 166개, 형 변환 표기 32개, `@template` 13개, 클래스·JSX·`enum` 없음(grep).
- `finance.fixtures.js`는 테스트만 가져다 씀(테스트 11개 + `data/`·`features/` 테스트, 운영 파일 0 — grep).
- `domain/` 밖에서 `domain/….js` 경로를 적은 곳: 운영 파일 164개·테스트 60개, 609줄(실행 가져오기 + JSDoc `import('…')`). `mock.module`로 `domain/`을 가짜로 바꾸는 테스트 없음(grep).
- JS 파일의 `@typedef`는 저절로 밖에 공개되지만, TS의 `type`은 `export`를 붙여야 공개됨 — 다른 파일이 `import('…/domain/x.js').Foo`로 가져다 씀.
- 4줄 넘게 이어진 `//` 설명 주석 11곳: `call-details.js:68`, `clients.js:15`, `day-record.js:63`, `driverSelfRevenue.js:4`, `finance.fixtures.js:122·154`, `financeOwnerDetail.js:4·24`, `financeOwnerExpenseSweep.js:4`, `financeReceivables.js:4`, `financeTaxInvoiceGroups.js:4`(줄 번호는 시작 줄).
- 타입 설명 파일 비교 시험: 저장소 밖 임시 설정으로 `domain/`의 `.d.ts` 59개(3,250줄)를 오류 없이 뽑음(2026-10-22, 파일 변경 없음).

### 목표 상태 (기대 동작)
1. `domain/` 운영 파일 58개가 `.ts`(TS 표기, `// @ts-check` 줄 없음). `@typedef` → `export type`, `@param`·`@returns` → 표기, `@template` → `<T>`, 형 변환 `/** @type {X} */ (y)` → `y as X`, 타입만 가져오면 `import type`. `AGENTS.md` §6 금지 표기(`enum`·`namespace`·`!`·`as unknown as`)와 `any`·`unknown`·`@ts-ignore` 없음.
2. 위 11곳의 긴 설명 주석은 결론 1~2줄로. 함수 위 한 줄 설명은 그대로, 타입 표기만 있던 주석 줄은 표기로 옮기며 지움.
3. `domain/` 안팎의 가져오는 경로 `domain/….js` → `.ts`(JSDoc `import('…')` 포함). `domain/` 밖 파일은 이 바꿈 말고 다른 줄이 안 바뀜.
4. 타입 설명 파일(`.d.ts`)이 옛 것과 같음 — 주석과 경로 확장자를 지우고 비교, 남는 차이는 하나씩 "표기만 다름"인지 보고(예: `Array<X>` ↔ `X[]`). 타입이 넓어지거나 좁아진 것은 0.
5. 빌드 결과 파일 이름 해시까지 옛 커밋과 같음. 테스트 수 그대로 전부 통과, 타입 검사 통과, strict-inventory 오류 수 445 이하, lint 경고 50 이하, 의존 검사 위반 0이고 모듈·연결 수는 확장자 말고 바꾸기 전과 같음(→ CI 리눅스에서 swc가 `.ts`를 읽는다는 것도 확인됨).

### 건드릴 파일
- `src/domain/*.js` 58개 → `.ts` (목록: `domain/` 운영 파일 전부, `smsHref.ts` 빼고)
- `src/domain/*.test.js` 36개 — 가져오는 경로 확장자만
- `domain/` 밖 운영 파일 164개·테스트 60개 — 가져오는 경로 확장자만(목록은 grep `domain/[A-Za-z.-]+\.js`)
- §3 "1~3개 파일" 예외: 94 나누는 계획 승인으로 이 조각은 폴더 단위(경로만 바뀌는 파일이 대부분 — 59번 폴더 옮기기와 같은 방식).

### 안 건드릴 것
- `domain/` 밖 파일의 경로 말고 다른 줄, 테스트 파일 확장자, 설정 파일(`tsconfig*`·`.dependency-cruiser.cjs`·`package.json` — 94-A에서 끝), `data/` 이후 폴더(94-C~G), 함수 이름·인자 순서·동작.

### 검증
- 바꾸기 전 저장소 밖에 저장: 빌드 결과 파일 해시, 의존 검사 json, strict-inventory 오류 수, lint 경고 수, `domain/` 타입 설명 파일(저장소 밖 임시 설정 — `extends` 프로젝트 `tsconfig.json`, `checkJs`·`declaration`·`emitDeclarationOnly`, `types: []`, `domain/` 운영 파일만).
- 바꾼 뒤 같은 것을 다시 뽑아 비교(목표 4·5). `npm test`·`npm run typecheck`·`npm run typecheck:strict-inventory`·`npm run build`·`npm run depcheck`·`npm run lint`.
- `domain/` 밖 파일 diff가 경로 확장자 바꿈뿐인지 기계로 확인(바뀐 줄에서 `domain/…\.ts` → `.js`로 되돌리면 옛 줄과 같음).
- grep: `domain/[A-Za-z.-]+\.js` 0, `domain/`에 `@ts-check`·`@typedef`·`@param` 0.
- 플레이북 §8: 검사 대상에서 파일을 빼서 오류 0을 만들지 않음, `schemaParts.ts`의 검사 규칙과 타입이 같은 모양 그대로.

### 파일 길이
- 200줄 넘는 파일 2개(이미 §6 예외 주석 있음): `clients.js` 232, `financeCore.js` 212 — 타입 표기 줄이 빠져 줄어들 것. 190줄 넘는 `day-record.js` 199, `cars.js` 193도 줄어들 것.
- 규칙: 바꾼 파일은 바꾸기 전보다 길어지지 않음. 길어지거나 새로 200줄을 넘는 파일이 나오면 멈추고 보고.

### 코드에 넣을 주석(최종 문구)
- 새 주석 없음. 11곳 줄인 문구는 그 자리의 결론 문장을 그대로 남김(새로 쓰지 않음). 예외 주석(`clients`·`financeCore`의 "§6 예외")은 그대로.

### 실패 시 처리
- 새 저장 장치·층 없음. 타입 설명 비교에서 진짜 타입 차이가 나오거나 빌드 결과가 다르면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
- 화면에서 볼 것은 없음(빌드 결과가 같으면 앱이 글자 하나까지 같음). CI 초록 + 빌드 결과 파일 49개 이름이 직전 커밋과 같은지는 AI가 CI 기록으로 확인해 보고.
