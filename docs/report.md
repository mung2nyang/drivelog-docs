# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 94 `.ts` 전환 — 나누는 계획 + 94-A 설정과 시범 2개 `[ ]`

보리 지시 "지시서 작성해"(2026-10-22, 91 `[x]` 뒤) → **결정 `[확인: 2026-10-22 보리 — "나. .ts 전환으로 가. 나누는 계획 다시 써줘."]`**: 로드맵 보류 2번(`.ts`/`.tsx` 실전환, 2026-09-05부터 미룸)을 94번으로 실행. "긴 설명 주석 줄이기"는 각 조각에서 그 폴더를 바꿀 때 같이(따로 슬라이스 안 만듦).

### 나누는 계획 (59번처럼 폴더 단위, 아래층부터)
| 조각 | 범위 | 운영 파일 | 비고 |
|---|---|---|---|
| **94-A** | 설정 + 시범 2개(아래 지시서) | 2 | 실행·테스트·빌드·의존 검사가 `.ts`/`.tsx`를 읽는지 증명 |
| 94-B | `domain/` | 59 (5,455줄) | 맨 아래층 — 타입 정본이 먼저 `.ts`가 돼야 위층이 가져다 씀 |
| 94-C | `data/` | 81 (6,365줄) | 저장·동기화 — 플레이북 트리거 |
| 94-D | `shared/` + `app/` + `main.jsx` + `testSupport/` | 70 (3,656줄) | |
| 94-E | `features/` 일지·달력·지출 | 39 (3,088줄) | |
| 94-F | `features/` 서류·미수금·매출 | 38 (3,490줄) | |
| 94-G | `features/` 나머지 8개 | 64 (5,767줄) | 마무리: `allowJs` 끄기, `// @ts-check` 규칙 정리 |

- **반만 먼저** `[확인: 2026-10-22 보리 — "그럼 반만, 응"]`: 94-A·B·C를 지금, 94-D~G는 출시 전 순서(23·24 → 34 → 79) 뒤(`docs/roadmap.md` 94번). 섞인 동안도 `allowJs`로 앱·검사는 그대로 돎.
- 운영 파일 수는 폴더 전체 기준(시범 2개는 94-A에서 먼저 바꾸고 B·D에서 빠짐, `main.jsx`는 D).
- 조각이 7개인 이유: 운영 파일 350개를 한 번에 바꾸면 검토가 불가능해서, 59번 폴더 옮기기 때처럼 층·폴더 경계로 나눔(§3 "3조각 이상" 예외 — 이번 1회 보고로 끝, 더 쪼개지 않음).
- 모든 조각의 공통 증명: **빌드 결과 파일이 이름 해시까지 옛 커밋과 같음**(타입 표기는 빌드 때 지워지므로 실행 코드가 같으면 같음) + 테스트 수 그대로 전부 통과 + 타입 검사·의존 검사 통과. 다르면 멈추고 보고.
- **테스트 파일(233개)은 `.js` 그대로 둠** — 테스트는 타입 검사 대상이 아니고(`tsconfig.json` `checkJs: false`), 바꾸면 조각마다 검토할 파일이 두 배가 됨. 가져오는 경로의 확장자만 바뀜.
- 94-B부터 "JSDoc 타입을 TS 표기로 자동 변환하는 도구"를 쓸지는 94-B 지시서에서 따로 물음(외부 도구라 먼저 물어보기 규칙).
- `// @ts-check` 없는 운영 파일 6개(`app/guestSessionPersist.js`·`app/routes/ComingSoonRoute.jsx`·`app/routes/lazyPages.js`·`app/routes/sessionGate.js`·`data/cloud/singleFlight.js`·`data/store/useHydrationLock.js`)는 지금 타입 검사를 전혀 안 받음 — `.ts`로 바뀌는 조각(94-C·D)에서 처음 검사받아 타입 오류가 나올 수 있음. 그 조각 지시서에서 먼저 확인.

### 결정 (2026-10-22 지시서 확인 뒤) `[확인: 2026-10-22 보리 — "권함으로."]`
- 의존 검사 도구(dependency-cruiser 18.5)는 `typescript` 7을 지원하지 않아(`>=2 <7`, `npx depcruise --info`에서 `.ts`·`.tsx` 못 읽음) 바꾼 파일 안의 가져오기가 조용히 검사에서 빠짐 → **`@swc/core`를 개발용 의존성으로 추가**하고 설정에 `parser: 'swc'` 한 줄(swc는 이 옵션이 있어야 쓰임 — `node_modules/dependency-cruiser/src/extract/swc/extract.mjs:7`).
- 계산·데이터 테스트(`test:unit`)는 로더 없이 **Node 자체 타입 지우기**로 `.ts`를 읽음(Node 22.18부터 기본 켜짐 — 이 PC 24, CI `'22'`는 늘 최신 22). 로더는 화면 테스트(`.jsx`·`.tsx`, Node가 못 읽음)에만.

### `AGENTS.md` §6 문구 — 반영함 `[확인: 2026-10-22 보리 — "바꿔놔"]`
데스크톱 AI가 워킹카피에 반영(커밋 안 함 — 94-A `[x]` 문서 커밋에 같이). 터미널 AI는 다시 고치지 않음.
- 지금: "모든 변경·생성 프로덕션 파일 `// @ts-check`."
- 바꿀 문구: "새로 만들거나 `.ts`/`.tsx`로 옮긴 운영 파일은 TS 표기로 쓴다. 아직 `.js`/`.jsx`인 운영 파일은 `// @ts-check`(전환 중, 로드맵 94). `enum`·`namespace`·`!`(null 아님 단언)·`as unknown as`는 쓰지 않는다. 타입만 가져올 때는 `import type`."
- 이유: `enum`·`namespace`는 테스트 실행기(Node)가 못 지우는 표기, `!`는 `@ts-ignore`처럼 검사를 건너뜀.

---

## 94-A 설정 + 시범 2개 `[x]` `[확인: 2026-10-22 보리 — "94-A [x] 승인. 마무리해."]`

> **진행(2026-10-22, 터미널 AI)** `[~]` 코드 수정·로컬 검사 끝, 브라우저 확인 대기, 커밋 안 함.
> - 교차검증: 지시서 사실(설정 파일 내용, 로더가 `.jsx`만 읽음, depcruise `.ts`·`.tsx` 못 읽음, 시범 2개와 가져오는 곳 6곳)이 코드와 맞음. 고친 것 없음.
> - 바꾸기 전 저장(저장소 밖): 빌드 결과 파일 62개 해시, 의존 검사 json, strict-inventory 오류 445건, lint 경고 50.
> - 한 일: 지시서 "건드릴 파일" 그대로. `@swc/core` 1.16.18 개발용 추가(npm이 설치 스크립트 허용 안내를 띄웠지만 `npx depcruise --info`에서 swc 인식 확인).
> - 결과: 타입 검사 통과(새 옵션 3개로 기존 파일 새 오류 0), strict-inventory 445 → 445, `npm test` unit 878·app 663 전부 통과(수 그대로, `smsHref.test.js`는 로더 없이 `.ts`를 읽어 통과, `SwitchRow`는 `AppSettingsPage`·`FixedRouteClientLink` 화면 테스트 4개에서 그려짐), **빌드 결과 파일 62개 해시까지 옛 커밋과 같음**, depcheck 위반 0, lint 50 그대로, grep 옛 경로 0.
> - 의존 관계 비교: 모듈·연결 1,784개가 시범 2개 확장자 말고 전부 같음(swc로 바꿔도 기존 `.js`·`.jsx` 읽기 결과 같음). `smsHref.ts`는 가져다 쓰는 곳 3개, `SwitchRow.tsx`는 2개로 검사에 들어 있음(두 파일 다 가져오는 것 없음).
> - 새 파일 규칙(§6): `import type` 쓸 곳 없음, `enum`·`namespace`·`!`·`as unknown as` 없음.
> - 코드 커밋 react-app `a009f2b`(2026-10-22, 보리 "커밋해", 14개 파일, push 안 함 — push는 보리).
> - 남은 것: 보리 push → CI(Node 22에서 `.ts` 읽기 확인) → §5 리뷰 → 최종 승인.
> - (데스크톱 AI) push 뒤 CI verify·deploy 초록(run 39722112299, Node 22.23.3). 테스트 878·663, 의존 검사 422 모듈·1,362 연결 — 직전 `73e2a52` CI와 같음, `.ts` 읽기 경고·오류 없음.
> - §5 리뷰: ①범위 — 14개 파일이 "건드릴 파일"과 같음 ②증설 — 새 저장 장치 없음, 새 의존성은 승인된 `@swc/core`뿐(잠금 파일 +281줄은 기기별 swc 파일, 리눅스용 포함) ③타입 꼼수 — 추가된 줄에 `any`·`unknown`·`@ts-ignore`·`!`·`enum` 없음 ④200줄 — 전부 30줄 이하 ⑤테스트 — `smsHref.test.js` 가져오기 경로만, 테스트 수 그대로 ⑥공용 — `SwitchRow` 속성 5개(`disabled`만 선택)가 옛 JSDoc과 같음 ⑦기대 동작 1~6 충족.
> - (데스크톱 AI) 배포본 확인(https://getdrivelog.com/, 비회원): 앱 설정 `운행일지 세부 입력`·`고정 노선 사용`·`상하차지 사용`·`운행 횟수 버튼 사용` 스위치를 눌러 켜짐·꺼짐과 아래 칸 열림·닫힘 정상, 이름을 눌러도 바뀜, 새로고침 뒤 유지, 원래대로 되돌림. 문자 보내기는 이 창에서 문자 앱을 못 띄워, 배포된 파일로 대신 확인: CI 빌드 결과 파일 49개 이름(해시 포함)이 직전 `73e2a52` CI와 전부 같고, 배포본이 그 `index-CfWQBu-K.js`를 받음(그 안의 문자 주소 코드 그대로) — 실행 코드가 옛 것과 같음.
> - 남는 한계: 시범 2개는 다른 파일을 가져오지 않아, CI(리눅스)에서 swc가 실제로 읽었는지는 개수로 구분 안 됨(이 PC에서는 확인). 94-B에서 `domain/` 파일이 가져오는 연결 수로 확인됨.

### 쉬운 요약
- 앱을 실행·테스트·빌드하는 도구들이 `.ts`(계산 코드)와 `.tsx`(화면 코드) 파일을 읽을 수 있게 설정을 고치고, 작은 파일 2개만 실제로 바꿔 전부 제대로 도는지 확인합니다.
- 앱 동작은 그대로입니다. 빌드 결과가 옛 커밋과 같은 것으로 증명합니다.

### 현재 상태 (코드 확인, react-app `73e2a52`)
- 운영 파일 350개 모두 `.js`/`.jsx`, `.ts`는 `env.d.ts` 하나(타입 설명 전용). 타입 검사 `tsc --noEmit`(`typescript` 7), `tsconfig.json` `allowJs: true`·`checkJs: false`·`strict: true`, `noEmit: true` — `// @ts-check` 붙은 341개만 검사(나머지 9개 중 6개는 운영 파일, 3개는 `testSupport/`). 새 옵션 3개를 붙여 지금 코드로 타입 검사 시험 → 오류 0(2026-10-22, 파일 변경 없이 명령줄 옵션으로).
- 테스트: `node --test`(CI는 Node 22, 이 PC는 24). 계산·데이터 테스트(`domain/`·`data/`)는 로더 없이 실행, 화면 테스트는 테스트 파일마다 `register(...)`로 `testSupport/jsxLoaderHook.mjs`를 붙여 `.jsx`만 esbuild로 바꿔 읽음(`:15` `if (!url.endsWith('.jsx'))`).
- 가져오기 경로는 확장자를 붙여 씀: 실행 가져오기 1,337곳, 타입 `import('…')` 1,315곳(grep).
- 의존 검사 `.dependency-cruiser.cjs`는 허용 예외 목록에 `.jsx` 파일 이름을 적어 둠(`:6~13`), 테스트 제외는 `\.test\.js$`(`:70`). 지금은 `.ts`·`.tsx`를 못 읽음(`npx depcruise --info` — `typescript` 지원 `<7.0.0`, 설치된 건 7.0.2, swc 없음).
- 코드 검사 `oxlint`는 `.ts`·`.tsx`를 원래 읽음(`.oxlintrc.json` 확장자 제한 없음).
- 시범 대상: `domain/smsHref.js`(8줄, 쓰는 곳 3개 + 테스트 1개), `shared/ui/SwitchRow.jsx`(27줄, 쓰는 곳 2개 — 앱 설정·고정 노선 설정).

### 목표 상태 (기대 동작)
1. `tsconfig.json`·`tsconfig.strict-inventory.json`에 `allowImportingTsExtensions: true`(경로에 `.ts`를 쓸 수 있게)·`verbatimModuleSyntax: true`(타입만 가져오면 `import type`을 강제 — Node가 실행할 때 타입 가져오기를 지울 수 있게)·`erasableSyntaxOnly: true`(`enum` 등 지울 수 없는 표기 금지)를 더함. 이 때문에 기존 `.js` 파일에서 새로 타입 오류가 나면 멈추고 보고.
2. `testSupport/jsxLoaderHook.mjs`가 `.jsx`에 더해 `.ts`·`.tsx`도 esbuild로 바꿔 읽음(화면 테스트 안에서는 한 경로로). 계산·데이터 테스트는 로더 없이 Node 자체 타입 지우기로 `.ts`를 읽음(위 결정) — `smsHref.test.js`가 `smsHref.ts`를 읽고 통과하는지 확인. 못 읽으면 멈추고 보고.
3. `domain/smsHref.js` → `smsHref.ts`(TS 표기), `shared/ui/SwitchRow.jsx` → `SwitchRow.tsx`(props 타입을 TS 표기로). 각각을 가져오는 경로의 확장자만 바꿈.
4. `@swc/core`(1.x, 개발용) 추가 + `.dependency-cruiser.cjs` `options`에 `parser: 'swc'`. `npx depcruise --info`에서 `.ts`·`.tsx` 읽음으로 바뀌고, 검사 결과에 `smsHref.ts`·`SwitchRow.tsx`가 실제로 들어 있으며(그 파일이 가져오는 것까지), 위반 0. swc로 바꾸면 기존 `.js`·`.jsx`도 swc로 읽으므로, 기존 파일의 의존 관계가 바꾸기 전과 같아야 함. 테스트 제외 규칙은 그대로(`\.test\.js$`).
5. 빌드 결과 파일이 이름 해시까지 옛 커밋과 같음. 테스트 수 그대로 전부 통과, 타입 검사 통과, lint 경고 수 그대로.
6. `AGENTS.md` §6은 이미 위 문구로 바뀜(2026-10-22) — 새 `.ts`/`.tsx` 파일이 그 규칙(`import type`, `enum`·`namespace`·`!`·`as unknown as` 없음)을 지키는지 확인.

### 건드릴 파일
- `tsconfig.json`, `tsconfig.strict-inventory.json` — 옵션 3개
- `src/testSupport/jsxLoaderHook.mjs` — `.ts`·`.tsx` 읽기
- `src/domain/smsHref.js` → `.ts`, `src/shared/ui/SwitchRow.jsx` → `.tsx`
- 가져오는 곳(경로 확장자만): `smsHref` 4곳(`MessageTemplateSheet.jsx`·`ShareFileModal.jsx`·`driverInviteSms.js`·`smsHref.test.js`), `SwitchRow` 2곳(`settings/ui/AppSettingsPage.jsx`·`FixedRouteBlock.jsx`)
- `package.json`·`package-lock.json` — 개발용 의존성 `@swc/core` 추가(`npm install -D @swc/core`)
- `.dependency-cruiser.cjs` — `options`에 `parser: 'swc'` 한 줄
- (반영 끝, 터미널 AI는 안 건드림) drivelog-docs `AGENTS.md` §6 한 줄 — 94-A `[x]` 문서 커밋에 포함

### 안 건드릴 것
- 나머지 운영 파일 전부(94-B 이후), 테스트 파일 확장자, `package.json` 테스트 명령(`test:unit`에 로더 안 붙임 — 위 결정), `vite.config.js`(Vite는 `.ts`/`.tsx`를 원래 읽음), `.oxlintrc.json`(oxlint는 `.ts`/`.tsx`를 원래 읽음), CI 설정(Node 22 그대로), 설명 주석 줄이기(94-B부터 폴더마다).

### 검증
- 바꾸기 전 빌드 결과 파일 목록·해시를 저장소 밖에 저장 → 바꾼 뒤 같아야 함.
- 바꾸기 전 의존 검사 결과(`npx depcruise src --config .dependency-cruiser.cjs --output-type json`)를 저장소 밖에 저장 → 바꾼 뒤 시범 2개의 확장자 말고는 모듈·의존 관계가 같아야 함, 시범 2개는 검사 결과에 들어 있어야 함.
- `npm run typecheck`·`npm run typecheck:strict-inventory`(옛 오류 수와 비교, 늘면 실패)·`npm test`(수 그대로, `smsHref.test.js`와 `SwitchRow`를 그리는 화면 테스트가 새 파일을 실제로 읽었는지 확인)·`npm run build`·`npm run depcheck`·`npm run lint`.
- grep `smsHref.js'|SwitchRow.jsx'` 0.

### 파일 길이
- `smsHref.ts` 약 7줄, `SwitchRow.tsx` 약 25줄, `jsxLoaderHook.mjs`(지금 25줄) 약 +5줄, `.dependency-cruiser.cjs`(지금 73줄) +2줄. 모두 200줄 이하.

### 코드에 넣을 주석(최종 문구)
- `jsxLoaderHook.mjs` 고친 줄: `// .jsx·.ts·.tsx를 esbuild로 바꿔 읽는다(.jsx·.tsx는 Node가 못 읽음).`
- `.dependency-cruiser.cjs` `parser` 줄: `// .ts·.tsx를 읽으려면 swc가 필요하다(typescript 7은 이 도구가 못 씀).`
- `smsHref.ts`·`SwitchRow.tsx`는 옛 머리 주석 그대로, `// @ts-check` 줄만 지움.

### 실패 시 처리
- 새 저장 장치·층 없음. 도구 하나라도 `.ts`/`.tsx`를 못 읽거나 빌드 결과가 다르면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
- 화면에서 볼 것: 앱 설정 화면과 고정 노선 설정의 켜고 끄는 줄(`SwitchRow`)이 예전처럼 눌리는지, 문자 보내기 버튼이 문자 앱을 여는지. 나머지는 CI 초록과 빌드 결과가 같다는 기록으로 확인.
