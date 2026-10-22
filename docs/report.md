# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 100 계산 규칙(`domain/`)이 가리키는 타입 3개를 `domain/`으로 `[x]`

> **결정 `[확인: 2026-10-22 보리 — "한 슬라이스로. 착수지시서 확정, 작업 진행해"]`**: 85개 파일 한 슬라이스. 커밋은 보리 지시 뒤.

> **진행(2026-10-22, 데스크톱 AI)** `[x]` 코드 커밋 react-app `de91e4d`, CI 초록, 보리 최종 승인(2026-10-22).
> - 바꾸기 전: 옛 경로를 가리키는 85개 파일 목록과 옛 코드 빌드 결과(파일 62개 해시)를 저장소 밖에 저장.
> - 한 일: 새 `domain/jsonTypes.js`(14줄)·`domain/driverTypes.js`(20줄)에 정의를 글자 그대로 옮김. `pendingWorkDataWritesTypes.js` 50 → 39줄, `syncTypes.js` 87 → 71줄(`OutboxPayload.previousDriverSnapshot`은 새 자리를 가리킴). 85개 파일의 99곳 경로를 파일마다 상대 경로를 계산하는 스크립트(저장소 밖)로 바꿈 — 옛 경로가 실제로 `data/sync`의 그 파일을 가리키는지 하나씩 확인하며 바꿈.
> - 대조: 옛 경로 grep 0, `domain/` 운영 파일의 `../data/` 0. 바뀐 파일 목록 = 85개 + 정의 지운 2개(계획과 같음) + 새 파일 2개. 바뀐 낱말 198개(지운 99·넣은 99)를 경로만 지우고 비교하면 양쪽이 같음 — 경로 말고 바뀐 글자 0.
> - `data/` 안에서 다시 이름을 붙이는 `atomicPersist.js`·`hydrateMergeTypes.js`도 85개에 들어 있어 새 자리를 가리키게 됨(별칭은 데이터 쪽 것이라 그대로).
> - 결과: 타입 검사 통과, `npm test` unit 878·app 663 전부 통과(테스트 수 그대로), **빌드 결과 파일 62개 해시까지 옛 커밋과 같음**, `npm run depcheck` 위반 0, lint 경고 50 그대로.
> - 코드 커밋 react-app `de91e4d`(2026-10-22, 89개 파일, push 안 함 — push는 보리).
> - push 뒤 CI verify·deploy 초록(run 39712649021). §5 리뷰 7개 문제 없음(옛 경로 0, `domain/` 운영 파일 `../data/` 0, 경로 말고 바뀐 낱말 0, 타입 꼼수 0, 테스트는 경로 17줄만, 200줄 넘는 바뀐 파일은 `taxInvoiceExcelBuilder.js` 251줄 하나 — 원래 251줄·응집도 예외 주석 있음).
> - 지시서 정정: "파일 길이"의 "전부 200줄 이하 유지"는 틀림 — 경로만 바뀐 `taxInvoiceExcelBuilder.js`는 원래 251줄(응집도 예외 주석 있음, 줄 수 그대로).
> - 보리 최종 승인(2026-10-22). 다음 91.

보리 지시 "100번 착수지시서 써줘"(2026-10-22). 로드맵 100 `[확인: 2026-10-21 보리 — 98-B 착수 때 "권함으로"]`, 순서 102 → **100** → 91 → 94.

### 쉬운 요약
- 계산 규칙 폴더(`domain/`)는 맨 아래층이라 다른 폴더를 보면 안 되는데, 타입 설명(코드 옆 "이 값은 이런 모양" 메모) 일부가 위층인 데이터 폴더(`data/`)의 타입 3개를 가리킵니다. 이 3개를 `domain/`으로 옮기고, 가리키는 곳의 경로만 고칩니다.
- 앱 동작·화면·저장은 그대로입니다. 타입 설명은 실행되지 않는 메모라 빌드 결과도 같아야 합니다.

### 확인할 점 (하나)
- **한 슬라이스로 할지**: 경로만 바뀌는 파일이 85개(테스트 16개 포함)라 "한 슬라이스 1~3개 파일" 기준을 크게 넘습니다. **권함: 한 번에.** 바뀌는 건 경로 글자뿐이고, "옛 경로 0건" grep 하나와 타입 검사로 전부 증명됩니다. 나눠도 각 조각 검증 방법이 같아 이득이 없습니다(59번 폴더 옮기기 때와 같은 방식).

### 현재 상태 (코드 확인, react-app `db5ea29`)
- 타입 정의 자리: `JsonValue`·`JsonArray`·`JsonRecord` → `data/sync/pendingWorkDataWritesTypes.js:22~31`, `DriverRecord` → `data/sync/syncTypes.js:7~21`.
- `domain/` 운영 파일 19개가 가리킴(`JsonValue` 16번·`JsonRecord` 6번·`DriverRecord` 3번 — `domain/drivers.js`는 둘 다). 그 밖에 `domain/`에서 `data/`를 가리키는 운영 파일 없음(grep `../data/`, 남는 2개는 테스트 `drivers-cloud.test.js`·`receivables-invoices.test.js`가 실행 함수를 부르는 것 — 범위 밖).
- 이 타입들을 가리키는 파일 전체 85개: `JsonValue`·`JsonRecord` 56개(`data/` 28·`domain/` 18·`features/` 10·`app/` 1 — 운영·테스트 합), `DriverRecord` 32개(`data/` 18·`domain/` 3·`features/` 11). 모두 `/** @typedef {import('…').이름} 이름 */` 같은 설명 줄.
- 같은 파일의 다른 타입(`EffectivePatch` 24번 등 큐 타입, `OutboxPayload`·`AppSession` 등)은 데이터 쪽 것이라 그대로. `OutboxPayload.previousDriverSnapshot`(syncTypes.js:32)만 `DriverRecord`를 씀.
- 의존 방향 검사(98-B `npm run depcheck`)는 실행되는 가져오기만 보고 타입 설명 줄은 안 봄 — 그래서 지금까지 안 잡혔음.

### 목표 상태 (기대 동작)
1. 새 `domain/jsonTypes.js`에 `JsonValue`·`JsonArray`·`JsonRecord`(설명 주석 포함, 내용 그대로), 새 `domain/driverTypes.js`에 `DriverRecord`(그대로). 둘 다 타입 전용(`export {}`).
2. `data/sync/pendingWorkDataWritesTypes.js`에서 위 3개 정의를 지우고, `data/sync/syncTypes.js`에서 `DriverRecord` 정의를 지움. `OutboxPayload`는 `import('../../domain/driverTypes.js').DriverRecord`를 가리킴. 이어받기용 별칭은 남기지 않음(가리키는 곳을 모두 고치므로).
3. grep `pendingWorkDataWritesTypes.js').Json`·`syncTypes.js').DriverRecord` 결과 0. grep `../data/`가 `domain/` 운영 파일에서 0.
4. 타입 검사 통과, 테스트 수 그대로 전부 통과, 빌드 결과 파일(이름 해시 포함) 옛 커밋과 같음.

### 건드릴 파일
- 새 파일 2개: `domain/jsonTypes.js`, `domain/driverTypes.js`
- 정의 지움: `data/sync/pendingWorkDataWritesTypes.js`(50 → 약 40줄), `data/sync/syncTypes.js`(87 → 약 73줄)
- 경로만: 위 85개 파일(운영 69·테스트 16). 바꾸는 방법은 파일마다 새 파일까지의 상대 경로를 계산하는 스크립트(저장소 밖에 둠)로 `import('…/data/sync/pendingWorkDataWritesTypes.js').Json…` → `import('…/domain/jsonTypes.js').Json…`, `import('…/data/sync/syncTypes.js').DriverRecord` → `import('…/domain/driverTypes.js').DriverRecord`. 다른 글자는 안 바꿈.

### 안 건드릴 것
- 큐 타입(`EffectivePatch`·`DurableReadResult` 등)과 서버 저장 타입(`OutboxPayload`·`CarRecord`·`DriverLinkRow`·`AppSession`)의 자리, 실행 코드 전부, `domain/` 테스트 2개가 `data/` 함수를 부르는 것, 의존 방향 검사 설정(타입 줄까지 검사하게 넓히는 것은 따로 — 원하면 로드맵 등재), 91·94번.

### 검증
- 바꾸기 전: 옛 경로 grep 결과 85개 파일 목록을 저장소 밖에 저장. 바꾼 뒤 `git diff --stat` 파일 목록이 그 목록 + 새 파일 2개 + 정의 지운 2개와 같은지 대조.
- 바뀐 줄 검사: 모든 바뀐 줄이 경로 글자만 다른지(`git diff --word-diff`에서 바뀐 낱말이 경로뿐).
- `npm run typecheck`(이 작업의 핵심 증명 — 경로가 틀리면 여기서 걸림)·`npm test`·`npm run build`·`npm run depcheck`, lint 경고 수 그대로.
- 빌드 결과를 옛 커밋과 비교(파일 이름 해시까지 같아야 함 — 타입 설명은 빌드에 안 들어감).
- 화면 확인은 안 함(실행 코드 변화 0 — 빌드 결과가 같으면 증명됨).

### 파일 길이
- 새 `domain/jsonTypes.js` 약 14줄, `domain/driverTypes.js` 약 20줄. `pendingWorkDataWritesTypes.js` 50 → 약 40, `syncTypes.js` 87 → 약 73. 경로만 바뀌는 85개 파일은 줄 수 변화 0(한 줄 안의 글자만 바뀜). 전부 200줄 이하 유지.

### 코드에 넣을 주석(최종 문구)
- `domain/jsonTypes.js` 머리: `// JSON 값 모양 타입 정본(타입 전용 모듈). 계산 규칙·데이터·화면이 같이 쓴다.` — 그 아래 옛 설명 주석 그대로.
- `domain/driverTypes.js` 머리: `// 기사 레코드 타입 정본(타입 전용 모듈).`

### 실패 시 처리
- 새 저장 장치·층 없음. 타입 검사나 빌드 비교가 실패하면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
- 화면에서 볼 것은 없습니다(동작 변화 0). 확인은 CI 초록과, 빌드 결과가 옛 커밋과 같다는 기록으로 합니다.
