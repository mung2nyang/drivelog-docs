# docs/report.md — 현재 슬라이스 착수지시서

## 원본(바닐라) 앱 코드를 문서 저장소에서 분리 — 태그로 보존 후 지움 `[x]`

> **진행(2026-10-17)**: 보리 "응 작업진행해". 태그 `original-app-final` = `580131b`(지우기 직전, 원본 20개 포함 확인). 커밋 `f3023b0`(원본 20개 지움 + `README.md`, 29,549줄 삭제, push 전).
> - 남은 파일 20개: `.claude/settings.json`, `.gitignore`, `AGENTS.md`, `CLAUDE.md`, `README.md`, `STATUS.md`, `docs/` 14개. `CLAUDE.md`가 가리키는 `AGENTS.md`·`STATUS.md` 그대로.
> - `AGENTS.md` 고친 것은 워킹카피에 두고 이 슬라이스 문서 커밋에 넣음.
> - 보리 할 일: `git push` + `git push origin original-app-final`(태그) 완료 — GitHub에 태그 `580131b` 확인, main에 문서 파일만. GitHub Settings → Pages 끄기(권장, 보리 몫).
> - 보리 최종 승인(2026-10-17).
> - 지시서와 다른 점: 없음.

보리 지시(2026-10-17): "원본 코드는 이제 동작 대조 안 해, 필요없어. 리액트랑 분리. 원본 문서는 문서로, 원본 코드는 보존용으로(대신 AI는 읽지 마)". 방식은 보리 선택 "태그로 보존 + 지움".

### 쉬운 요약
- 문서 저장소(`ubiquitous-parakeet`) 맨 위에 섞여 있는 옛 원본 앱 파일을, 지금 상태 그대로 `original-app-final` 표식(태그)으로 남긴 뒤 저장소에서 지웁니다.
- 저장소에는 문서(`AGENTS.md`·`STATUS.md`·`CLAUDE.md`·`docs/`·`README.md`)만 남습니다. 문서 위치가 그대로라 규칙·기능 모음(스킬) 경로는 안 바뀝니다.
- 원본은 GitHub에서 그 태그로 언제든 볼 수 있고, AI는 읽지 않습니다.

### 현재 상태 (확인)
- `git ls-files` 40개 중 원본 앱 파일 20개: `car-management.js`, `client-management.js`, `driver-link.js`, `finance-sync.js`, `finance.js`, `maint-fuel-misc.js`, `mypage.js`, `notifications.js`, `script.js`, `supabase-config.js`, `supabase-sync.js`, `ui-widgets.js`, `index.html`, `style.css`, `images/` 3개, `tests/core-logic.test.js`, `package.json`, `package-lock.json`.
- `README.md` 설명이 "Vanilla SPA reference"(원본 앱 소개).
- GitHub Pages가 이 저장소 맨 위(main)를 원본 앱 사이트로 내보내도록 켜져 있음 — 지금도 상태 "errored"(`gh api repos/mung2nyang/ubiquitous-parakeet/pages`).
- `AGENTS.md`: 보리 지시("그 문서 문구 고쳐줘")로 원본 관련 문장을 이미 고침(워킹카피, 이 슬라이스 문서 커밋에 같이 들어감) — 4번째 줄 "원본 앱 코드는 태그 `original-app-final`로 보존만, AI는 읽지도 대조하지도 않음", §7·§8 제목의 "원본 §0-1" 뺌, §8-5 "원본과 동일한가" → "정해져 있는가(`docs/sot.md`)", §8-6(원본 grep) 지움.

### 목표 상태 (기대 동작)
1. 태그 `original-app-final`이 지우기 직전 커밋을 가리키고, GitHub에도 올라가 있다(원본 20개 파일을 그 태그로 볼 수 있음).
2. 문서 저장소 main에 원본 앱 파일 20개가 없다. 남는 것: `AGENTS.md`, `STATUS.md`, `CLAUDE.md`, `README.md`, `.gitignore`, `.claude/settings.json`, `docs/`.
3. `README.md`가 "문서·원칙 저장소, 원본 앱 코드는 태그 `original-app-final`에 보존(읽지 않음)"을 말한다.
4. react-app은 그대로(원본 파일을 부르는 곳 없음 — react-app은 별도 저장소).

### 건드릴 파일 (문서 저장소)
- 지움: 위 원본 앱 파일 20개(`git rm`).
- `README.md` — 설명 3줄로 바꿈.
- `AGENTS.md` — 이미 고친 것(위).
- 태그 `original-app-final` 만들기.

### 안 건드릴 것
- `docs/` 안 옛 원본 파일 위치 표기(예: `driver-link.js:370`) — 당시 기록이라 그대로.
- `docs/archive/` — 당시 기록.
- react-app.

### 검증
- 지우기 전 태그를 만들고 `git show original-app-final --stat`, `git ls-tree -r --name-only original-app-final`에 원본 20개가 있는지.
- 지운 뒤 `git ls-files`가 문서 파일만인지(20개 줄어 20개).
- `AGENTS.md`·`STATUS.md`·`docs/` 경로가 그대로인지(`CLAUDE.md`가 가리키는 파일이 열리는지).

### 파일 길이
- 해당 없음(지우기와 `README.md` 3줄).

### 실패 시 처리
- 새 장치 없음. 태그를 만들기 전에는 아무것도 지우지 않는다.
- 태그·지우기가 잘못되면 되감지 않고 수정 착수지시서로 다시 승인받는다.

### push·커밋 순서 (보리가 할 일 포함)
1. AI: 태그 만들기 → 원본 20개 지우기·`README.md` → 커밋(1번째, 이 저장소의 "코드" 커밋).
2. **보리: push** — 커밋과 함께 태그도 올려야 함: `git push` 그리고 `git push origin original-app-final`(태그는 따로 올려야 GitHub에 보임).
3. **보리(권장)**: GitHub 저장소 → Settings → Pages → 끄기(볼 사이트가 없어짐).
4. 보리 최종 승인 → AI 문서 커밋(2번째: STATUS·지시서 `[x]` + `AGENTS.md` 고친 것).

### 사용자 확인 방법
1. GitHub `ubiquitous-parakeet` 첫 화면에 문서 파일만 보인다.
2. GitHub에서 브랜치/태그 고르기 → Tags → `original-app-final` → 원본 파일들이 보인다.

### 확인할 점
- 없음(방식은 보리가 고름). push 때 태그도 같이 올려 주셔야 하는 것만 기억해 주세요.
