# docs/report.md — 현재 슬라이스 착수지시서

106: drivelog-docs `72a93ed`. 124-B-2: drivelog-docs `abceece`. 124-B-1: drivelog-docs `5c354fd`. 124-A: drivelog-docs `022a710`. 121-B: drivelog-docs `0aa4357`. 긴 주석 줄이기: drivelog-docs `59ddc9d`. 121-A: drivelog-docs `1590ef7`. 120-D: drivelog-docs `2ce5170`. 120-C: drivelog-docs `ae914c9`. 120-B: drivelog-docs `503a3d3`. 120-A: drivelog-docs `81f927a`. 119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 150-A 보안 PR #1 업데이트를 main에 넣기 `[x]`

로드맵 150(`docs/roadmap.md` 640행, ③). 보리가 지시서 작성을 지시했다(2026-11-02 "작성해"). 106에서 정한 처리 방식 "가"를 처음 쓰는 슬라이스다. 같은 업데이트를 main에 커밋하면 PR은 Dependabot이 스스로 닫는다. 150을 둘로 나눈다. 이번에는 A만 한다. B는 엑셀 아래 두 개이고, 따로 따진다.

### 쉬운 요약
- 빌려 쓰는 라이브러리 세 개를 보안 문제가 고쳐진 판으로 아주 조금 올린다. PDF 만들기가 쓰는 dompurify, 개발 도구 vite, 시험 도구가 쓰는 undici다.
- Dependabot이 연 PR #1과 같은 내용이다. PR을 합치는 대신 main에 직접 커밋한다.
- 끝나면 열린 보안 알림이 15개에서 2개로 줄고, PR #1은 저절로 닫힌다. 앱 동작은 바뀌지 않는다.

### 현재 상태 (코드 확인)
- 열린 알림은 15개다(`gh api .../dependabot/alerts`).
  - dompurify 3개(심각 1, 높음 1, 낮음 1): 배포되는 앱에 들어간다. 3.4.17에서 고쳐진다.
  - vite 5개(높음 1, 보통 3, 낮음 1): 개발 도구에만 있다. 7.3.7에서 고쳐진다.
  - undici 5개(높음 4, 보통 1): 개발 도구에만 있다. 8.12.2에서 고쳐진다.
  - minimatch 1개, uuid 1개(보통): exceljs 아래에 있다. 이번에는 다루지 않는다.
- 의존 경로(`npm ls`):
  - `html2pdf.js@0.14.0` → `dompurify@3.4.16`(`jspdf@4.2.1`도 같은 것을 씀)
  - `jsdom@30.0.1` → `undici@8.11.2`
  - `vite@7.3.6`(직접)
- dompurify를 쓰는 곳: PDF·이미지를 만드는 `src/features/documents/api/reportExport.js`(9·56행 `import('html2pdf.js')`). 이 함수를 부르는 화면은 내역서 화면(`ReportPage.jsx`)과 일상점검 내보내기(`DailyInspectionExportBar.jsx`)다. 시험은 `reportExport.test.js`, `ReportShareModal.test.js`가 있다.
- PR #1(`dependabot/npm_and_yarn/security-34e240c090`)은 `package.json` 1줄(vite `^7.3.6` → `^7.3.7`)과 `package-lock.json` 세 덩이(dompurify, undici, vite)만 바꾼다(+11 −11). 이 PR의 CI(41408142379)는 verify·db-test 모두 초록이다.

### 목표 상태 (기대 동작)
1. main의 `package.json`·`package-lock.json`이 PR #1과 글자까지 같다. dompurify 3.4.17, undici 8.12.3, vite 7.3.7이다.
2. main CI(verify·db-test·deploy)가 초록이다.
3. PR #1은 Dependabot이 닫는다. 합치지 않는다.
4. 열린 알림은 minimatch와 uuid 2개만 남는다.
5. 배포본에서 내역서 PDF 받기와 일상점검 PDF·이미지 받기가 전처럼 된다.

### 건드릴 파일
- `react-app/package.json`: vite 한 줄.
- `react-app/package-lock.json`: dompurify, undici, vite 세 덩이.
- 방법: PR 가지에서 두 파일을 그대로 가져온다(`git fetch origin <PR 가지>` → `git checkout FETCH_HEAD -- package.json package-lock.json`). 손으로 고치지 않는다. 그 뒤 `npm ci`로 내 컴퓨터의 `node_modules`도 맞춘다.

### 안 건드릴 것
- 앱 코드(`src/`), 서버 함수, CI 설정은 바꾸지 않는다. 판이 아주 조금만 올라가서(3.4.16 → 3.4.17 등) 고칠 코드가 없다. PR #1 CI가 그대로 초록이었던 것이 근거다.
- exceljs 아래 minimatch·uuid는 150-B로 미룬다. Dependabot도 "exceljs 판을 내려야만 고쳐짐"이라 했고(실행 41407947791), 덮어쓰기(overrides)가 exceljs를 깨지 않는지 따로 따져야 한다.
- 보안과 상관없는 판 올리기(vite 8, supabase-js 2.118 등)는 하지 않는다.

### 검증
- 자동 검사: CI가 돌린다. lint, 타입, 시험, 빌드, DB 시험이다. 같은 파일로 PR #1 CI가 이미 초록이었으므로, 작업 가지 CI는 따로 돌리지 않고 main에 바로 커밋한다.
- 내 컴퓨터에서 `npm ci` 뒤 `npm ls dompurify undici vite`로 판을 확인한다. `git diff FETCH_HEAD -- package.json package-lock.json`이 비어 있는지도 본다.
- push 뒤 확인할 것:
  - Dependabot 알림 수가 2개로 줄었는지(`gh api`).
  - PR #1이 닫혔는지(`gh pr view 1`). Dependabot이 닫는 데 시간이 걸릴 수 있다.

### 파일 길이
- `package.json` 41 → 41줄.
- `package-lock.json` 5419 → 5419줄. 이 파일은 npm이 만드는 파일이라 200줄 제한 대상이 아니다.

### 실패 시 처리
- 새 장치(재시도, 따로 고정하는 설정 등)를 만들지 않는다.
- CI가 빨갛거나 PDF가 깨지면 고치지 않고 원인과 수정 계획을 다시 써서 승인을 받는다(되돌림 커밋 포함).

### 사용자 확인 방법
- 배포 뒤 휴대폰에서 다음 두 가지를 확인한다.
  1. 내역서 화면 → PDF 받기(또는 공유) → PDF가 열리고 표·글자가 전과 같으면 성공이다.
  2. 일상점검 → 내보내기 → PDF와 이미지가 전처럼 나오면 성공이다.
- GitHub에서 PR #1이 닫히고 Security 탭 알림이 2개면 성공이다.

### 확인할 점
1. 화면 확인을 누가 할지 정해 주세요. AI가 로그인 탭(getdrivelog.com)에서 해도 되지만, PDF 받기는 파일 내려받기라 그때마다 보리 허락이 필요합니다. 휴대폰에서 보리가 해 주시는 쪽이 간단합니다(AI 권함: 보리 휴대폰).

### 진행 기록
- 2026-11-02 보리 확정("너가 진행해,확정 진행해"). 확인할 점 1은 AI가 화면 확인한다(PDF·이미지 받기 허락 포함).
- PR 가지(`9f7d0a9`)에서 두 파일을 그대로 가져왔다. PR과 차이 없음(`git diff --cached FETCH_HEAD` 빈 결과), +11 −11. `npm ci` 뒤 dompurify 3.4.17, undici 8.12.3, vite 7.3.7 확인.
- react-app main 코드 커밋 `f3d528f`. 보리 push를 기다린다.
- 보리 push(`f3d528f`). CI 41414165293 초록(verify·db-test·deploy 성공).
- PR #1은 Dependabot이 닫았다(2026-11-02 08:37, "더 이상 필요 없음" 댓글). GitHub 의존 목록(SBOM 08:39)도 dompurify 3.4.17·undici 8.12.3·vite 7.3.7로 바뀌었다.
- 배포본 화면 확인(로그인 탭, 테스트 계정 2026년 10월):
  - 내역서 PDF: `2026년_10월_운송비내역서.pdf` 244KB가 만들어졌다.
  - 일상점검 PDF: `일상점검표_2026년10월_11가1111.pdf` 526KB, 가로 A4 1쪽, 끝 표시 정상.
  - 일상점검 이미지: `...11가1111.png` 2246×1172, 빈 그림이 아니다(어두운 점 7%).
  - 오류 창은 없었다.
- 알림이 다시 계산되어 열린 알림이 minimatch·uuid(보통) 2개만 남았다. 고쳐짐으로 바뀐 것은 16개다(처음 열린 15개 가운데 13개에, 의존 목록을 다시 읽으며 새로 잡혔다가 바로 고쳐진 것이 더해진 수).
- 목표 대조: 1 PR과 같음, 2 CI 초록, 3 PR #1은 Dependabot이 닫음, 4 열린 알림 2개, 5 배포본 PDF·이미지 정상. 범위: `f3d528f`은 `package.json`·`package-lock.json` 2개 파일 +11 −11.
- 2026-11-02 보리 최종 승인("승인").
