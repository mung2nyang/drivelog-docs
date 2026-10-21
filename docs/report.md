# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 98-B 허용 예외 목록 + "아래층이 위층을 부르면 실패" 자동 검사를 CI에 `[x]`

> **진행(2026-10-21) — 코드 커밋 `38f7365`, push·CI 대기**: 보리 "착수지시서확정 작업진행해. 정할점은 권함으로" → `[확인: 2026-10-21 보리]` 1 `dependency-cruiser` 들이기 / 2 JSDoc 타입 표기는 검사 밖, 타입 3개를 `domain/`으로 옮기는 일은 로드맵에 따로(문서 쪽에서 등재).
> - 교차검증(AGENTS §1): CI 3단계·`package.json`에 `oxlint`만·기능 사이 부르기 8곳 모두 지시서와 같음. 고친 것: 최신판 18.7.0은 나온 지 이틀이라 3주 된 **18.5.0**으로 들임. 들이면서 vite와 같이 쓰는 개발용 부품 `picomatch`가 4.0.5 → 4.0.7(2026-08-24판)로 올라감(잠금 파일에서 지워진 줄은 `picomatch` 3줄뿐, 나머지는 새 항목 39개 추가).
> - 한 일: 새 `.dependency-cruiser.cjs`(73줄 — 층 규칙 5개, 허용 예외 8곳 목록에서 예외 파일별 규칙을 만듦, 테스트 파일·`src/testSupport` 제외, JSDoc 타입 표기는 안 봄), `package.json` `depcheck` 명령 + 개발용 의존성, `ci.yml` 빌드 다음에 "의존 방향 검사 (npm run depcheck)" 단계(`if: !cancelled()`, 머리 주석 "아래 4개"). `src/` 변경 0. `docs/architecture-target.md` §4에 층 규칙 표·허용 예외 8곳 표(워킹카피, 문서 커밋 때).
> - `npm run depcheck`: `✔ no dependency violations found (414 modules, 1343 dependencies cruised)`.
> - 위반 흉내(넣은 줄·뺀 줄은 모두 되돌림, `git status`로 확인): ① `shared/ui/SwitchRow.jsx`에 홈 화면 가져오기 → `error shared-below-data: src/shared/ui/SwitchRow.jsx → src/features/home/ui/HomePage.jsx`, 끝 코드 1 ② 예외 목록에서 `linked-driver.css` 뺌 → `error no-cross-feature: src/features/clients/ui/ScopedClientsList.jsx → src/features/drivers/ui/linked-driver.css`, 끝 코드 1 ③ `useReceivablesData` 뺌 → `HomePage.jsx → useReceivablesData.js` 실패 ④ 예외 파일 `HomePage`가 목록에 없는 거래처 부품을 부름 → 실패 ⑤ 예외 아닌 `CarListPage`가 다른 기능·`app/App.jsx`를 부름 → `no-cross-feature`·`features-below-app` 실패.
> - 결과: `npm test` unit 866 + app 641 = 1507 전부 통과, 타입 검사·빌드 통과, lint 경고 50 그대로. 빌드 결과 48개 파일 이름·내용 모두 98-A와 같음(`picomatch` 올라간 뒤에도).
> - 코드 커밋 react-app `38f7365`(push 안 함). push 뒤 CI verify에 새 단계가 보이고 초록인지 확인 필요.
> - push 뒤 CI verify·deploy 초록(run 39531500197). verify 단계 목록에 **"의존 방향 검사 (npm run depcheck)" 성공**(빌드 다음, 배포 준비 전) 확인.
> - §5 리뷰(데스크톱 AI): ①범위 = `.dependency-cruiser.cjs`(새)·`ci.yml`·`package.json`·`package-lock.json` 4개 + 문서 `architecture-target.md`(워킹카피), `src/` 0 ②새 파일 1(검사 설정, 지시서대로), 새 저장 키 0 ③타입 꼼수 0 ④설정 73줄 ⑤테스트 그대로(앱 코드 변경 0) ⑥공용 영향 — 잠금 파일에서 지워진 줄은 `picomatch` 4.0.5 3줄뿐(4.0.7로, vite가 같이 쓰는 개발용 부품), 나머지는 추가 ⑦목표 1~5 대조. 설정 내용 확인: 층 규칙 5개가 목표 1의 표와 같음, 허용 예외 8곳이 98-A 진행 기록 목록과 글자까지 같음, 예외 파일도 목록에 없는 다른 기능을 부르면 실패하도록 파일별 규칙을 따로 만듦(위반 흉내 ④로 확인됨), 테스트 파일·`testSupport` 제외. 들인 판 `^18.5.0`은 잠금 파일로 고정(CI는 `npm ci`).
> - 배포 화면 확인 안 함 — `src/` 변경 0이라 화면 영향 없음(빌드 결과 98-A와 같음은 교차검증에서 확인).
> - 보리 최종 승인(2026-10-21). JSDoc 타입 3개(`JsonValue`·`JsonRecord`·`DriverRecord`)를 `domain/`으로 옮기는 일은 로드맵 100으로 등재(보리 결정 "권함으로").

보리 지시 "98-B 착수지시서 지금 작성해줘"(2026-10-21). 로드맵 98 `[확인: 2026-10-21 보리 — "권하는대로"]`의 뒤쪽. 98-A `[x]` `d820a4d`로 기능 사이 부르기 14 → 8곳. 목표 구조 문서의 "규칙은 의존 분석 검사로 자동 확인할 수 있다(CI에 넣을지는 착수 때 보리 결정)"(`architecture-target.md` §4) — 98번에서 넣기로 정함.
CI 파일을 고치는 슬라이스(배포 관문) — 검사가 실패하면 그 push는 배포되지 않음.

### 쉬운 요약
- 지금은 폴더 사이 "누가 누구를 부르는지" 규칙을 사람이 grep으로만 확인합니다. 이걸 CI가 매번 자동으로 검사하게 해서, 규칙을 어기는 코드가 들어오면 CI가 빨강이 되게 합니다.
- 화면 안에 다른 기능 부품을 끼워 넣는 8곳은 "허용 예외"로 문서와 검사 설정에 이름을 적어 둡니다.
- 앱 화면·동작은 하나도 바뀌지 않습니다(검사 도구와 CI 단계만 더함).

### 현재 상태 (코드 확인 — grep, 운영 파일·실제 `import`만)
- 층 사이 부르기: `domain` → (다른 폴더) 0, `shared` → `app`·`features`·`data` 0, `data` → `app`·`features` 0, `features` → `app` 0. 허용되는 방향만 있음: `data` → `shared`(11 — 실패 문구·주소 기준)·`domain`, `shared` → `domain`(2), `features` → `data`·`shared`·`domain`, `app` → 전부.
- 기능 사이 부르기 8곳(98-A 진행 기록): 코드 5 — 일지 `CallClientQuickAdd` → 거래처 `ClientFormModal`, 일지 `DayLogExpenses` → 차량 유지비 `ExpenseFormModal`, 홈 `HomePage` → 미수금 `useReceivablesData`, 기사 `LinkedDriverClientsPage` → 거래처 `ScopedClientsList`, 계정 `PersonalInfoPage` → 기사 `EmployerLinkCard` / 디자인 3 — 차량 `CarDriverConnectPanel` → 기사 `driver-connection.css`, 거래처 `ScopedClientsList` → 기사 `linked-driver.css`, 기사 `LinkedDriverClientsPage` → 거래처 `client-management.css`.
- 실제 `import`가 아닌 **JSDoc 타입 표기**만 아래층에서 위층을 가리키는 곳: `domain/` 19개 파일이 `import('../data/sync/pendingWorkDataWritesTypes.js').JsonValue`·`JsonRecord`, `import('../data/sync/syncTypes.js').DriverRecord`를 씀(실행할 때는 아무것도 안 부름 — 확인할 점 2).
- 검사 도구: 지금 없음(`package.json`에 `oxlint`만). CI(`.github/workflows/ci.yml`)는 테스트·타입 검사·빌드 3단계.

### 목표 상태 (기대 동작)
1. 층 규칙(운영 파일의 실제 `import`, 테스트 파일 제외):
   - `domain/` → `domain/`만(+ 외부 라이브러리)
   - `shared/` → `shared/`·`domain/`
   - `data/` → `data/`·`shared/`·`domain/`
   - `features/<기능>/` → 자기 기능·`data/`·`shared/`·`domain/`, 다른 기능은 **허용 예외 8곳만**
   - `app/` → 모두
2. 검사 명령 `npm run depcheck` — 지금 코드에서 통과(위반 0). 일부러 위반 줄을 하나 넣으면 실패하고 위반한 파일·부른 파일을 보여 줌(진행 기록에 그 출력, 넣은 줄은 되돌림).
3. CI `verify`에 "의존 방향 검사 (npm run depcheck)" 단계 추가(테스트·타입 검사·빌드와 같은 방식 — 하나가 실패해도 나머지 계속).
4. `docs/architecture-target.md`에 "층 규칙"과 "허용 예외 8곳" 절을 추가(검사 설정과 같은 목록).
5. 앱 코드(`src/`) 변경 0 — 테스트·빌드 결과 그대로.

### 건드릴 파일
- 확인할 점 1에 따라:
  - (권함) `dependency-cruiser` 들이기: `package.json`(개발용 의존성 + `depcheck` 명령)·`package-lock.json`, 새 설정 파일 `.dependency-cruiser.cjs`(층 규칙 + 허용 예외 8곳, 각 규칙에 한국어 설명).
  - (다른 안) 직접 만든 검사 대본 `scripts/depcheck.mjs`(약 80줄, 외부 의존성 없음) + `package.json` 명령.
- `.github/workflows/ci.yml` — 단계 1개.
- 문서: `docs/architecture-target.md`(문서 커밋 때 함께).
- `src/`는 안 건드림.

### 안 건드릴 것
- 앱 코드·테스트, 허용 예외 8곳의 정리(그대로 둠 — 보리 결정 2026-10-21), JSDoc 타입 표기(확인할 점 2), 97·99번.

### 검증
- `npm run depcheck` 통과, `npm test`·타입 검사·빌드 그대로 통과(앱 코드 변경 0).
- 위반 흉내: 임시로 `src/shared/ui/`의 한 파일에 `features/` 가져오기 한 줄을 넣어 `depcheck` 실패 출력 확인 → 되돌림(진행 기록에 출력). 허용 예외 중 하나를 목록에서 잠깐 빼면 그 곳이 실패로 나오는지도 확인 → 되돌림.
- push 뒤 CI에 새 단계가 보이고 초록인지(데스크톱 AI가 `ci-check`로 단계별 확인).

### 파일 길이
- 설정 파일 약 60줄(권한 안) 또는 대본 약 80줄. 200줄 이내.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(검사만 더함).
- 지금 코드에서 검사가 위반을 찾으면(예상 0) 고치지 말고 멈추고 목록을 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법
- 배포 화면은 바뀌지 않음. push 뒤 GitHub Actions의 verify 안에 "의존 방향 검사" 단계가 초록인지(AI가 확인해 보고).

### 확인할 점
1. 검사 도구를 들여 쓸까요, 직접 만들까요?
   - **권함: `dependency-cruiser` 들이기** — JavaScript 폴더 의존 규칙 검사에 널리 쓰는 도구. 규칙·예외를 설정 파일에 이름으로 적고, 위반하면 어느 파일이 어느 파일을 불렀는지 바로 보여 줌. 개발용 의존성이라 배포되는 앱 크기는 그대로, CI 설치 시간만 조금 늘어남.
   - 다른 안: 직접 만든 대본(약 80줄) — 의존성 0, 대신 가져오기 모양(`import()`·`mock` 등)을 우리가 계속 손봐야 함.
2. `domain/` 19개 파일의 **JSDoc 타입 표기**가 `data/`의 타입(`JsonValue`·`JsonRecord`·`DriverRecord`)을 가리킵니다. 실행 때는 아무것도 안 불러 이번 검사(실제 `import`만)에는 걸리지 않습니다. **권함: 이번엔 검사 대상에서 빼고, 이 타입 3개를 `domain/`으로 옮기는 일을 로드맵에 따로 올리기**(타입 정의 자리만 옮기는 작은 일, 약 50개 파일의 표기 경로가 바뀜). 다른 안: 지금은 두기.
