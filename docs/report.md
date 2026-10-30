# docs/report.md — 현재 슬라이스 착수지시서

142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 104 CI 설정의 GitHub 도구 버전 올리기(Node.js 20 지원 종료 경고) `[x]`

보리 "로드맵에 올려놔"(2026-10-22), "ㅇㅇ"(2026-10-30) — 142 다음 순서. 앱 코드·DB 변경 없음, CI 설정 파일 하나만.

### 쉬운 요약
- 자동 검사·배포(CI)는 GitHub이 주는 도구 5개를 빌려 씁니다. 지금 쓰는 판은 지원이 끝나 가는 Node.js 20용이라, CI가 돌 때마다 경고가 뜹니다.
- GitHub이 Node.js 20을 완전히 끊으면 검사와 배포가 멈출 수 있어서, 5개를 모두 Node.js 24용 최신 판으로 올립니다.
- 앱 화면과 기능은 바뀌지 않습니다. 하는 일도 같고(받기 → 준비 → 검사 5개 → 배포), 도구 판 번호만 바뀝니다.

### 현재 상태 (코드 확인)
- 설정 파일 `react-app/.github/workflows/ci.yml`(88줄) 하나뿐(`.github/workflows`에 이 파일만, docs 저장소엔 CI 없음).
- 마지막 CI(run 41043632877) 경고: verify 쪽 `actions/checkout@v4`·`actions/setup-node@v4`·`actions/upload-artifact@v4`(`upload-pages-artifact@v3` 안에서 씀), deploy 쪽 `actions/configure-pages@v5`·`actions/deploy-pages@v4` — "Node.js 20용이라 Node.js 24로 강제로 돌림". 지금은 성공.
- 각 도구 최신 판(2026-10-30 GitHub 확인)과 실행 환경(`action.yml`):
  - checkout `v7.0.1` — node24
  - setup-node `v7.1.0` — node24
  - upload-pages-artifact `v5.0.0` — 안에서 upload-artifact `v7.0.0` 사용
  - configure-pages `v6.0.0` — node24
  - deploy-pages `v5.0.1` — node24
- 판 사이 바뀐 점 중 우리와 닿는 것(각 판 발표문 확인):
  - setup-node v5: 캐시 자동 감지가 생김. 우리는 `cache: npm`을 이미 적어 두어서 동작이 같음. v7: `NODE_AUTH_TOKEN` 빈 값 내보내기를 뺐는데, 우리는 npm에 올리지 않아 상관없음.
  - checkout v6: 로그인 정보를 다른 파일에 둠. 우리는 받기만 해서 상관없음.
  - upload-pages-artifact v4: 점으로 시작하는 파일은 배포 묶음에서 빠짐. 우리 빌드의 점 파일은 `dist/.nojekyll` 하나(`public/.nojekyll`)뿐인데, 이 파일은 "브랜치에서 바로 배포"할 때만 쓰임. 우리는 CI 배포(`deploy-pages`)라 Jekyll이 돌지 않아 빠져도 같음.
  - 나머지(configure-pages v6, deploy-pages v5)는 Node 24로 바꾼 것뿐.
- 빌드에 쓰는 Node 판(`node-version: '22'`)은 경고와 상관없음. 함께 뜨는 "ubuntu-latest가 Ubuntu 26으로 바뀜"은 안내라 할 일 없음.

### 목표 상태 (기대 동작)
1. `ci.yml`의 `uses:` 5줄이 위 최신 판(큰 번호 `@v7`·`@v7`·`@v5`·`@v6`·`@v5`)을 씀.
2. push 뒤 CI의 verify·deploy가 초록이고, "Node.js 20 is deprecated" 경고가 없음.
3. 배포가 끝난 getdrivelog.com이 지금처럼 열림(로그인 화면, 깊은 주소 새로고침, 아이콘·오프라인 화면 파일).
4. 검사 단계(테스트·타입·빌드·의존·코드 검사)와 배포 조건은 그대로.

### 건드릴 파일
- `react-app/.github/workflows/ci.yml` — `uses:` 5줄만:
  - `actions/checkout@v4` → `@v7`
  - `actions/setup-node@v4` → `@v7`
  - `actions/upload-pages-artifact@v3` → `@v5`
  - `actions/configure-pages@v5` → `@v6`
  - `actions/deploy-pages@v4` → `@v5`
- 주석 추가 없음.

### 안 건드릴 것
- 앱 코드 전부, `package.json`, 빌드 Node 판(`'22'`), 검사 단계·순서·조건, `runs-on: ubuntu-latest`.
- 점 파일 넣기 옵션(`include-hidden-files`) — `.nojekyll`은 CI 배포에서 하는 일이 없어 안 넣음.
- `public/.nojekyll` 파일 — 그대로 둠(지워도 되지만 이번 범위 밖).

### 검증
- 이 파일은 로컬에서 돌릴 수 없어 push 뒤 CI가 곧 검증이다. 보리 push → CI verify·deploy 초록 확인(ci-check), 실행 경고 목록에 Node.js 20 경고가 없는지 확인.
- 배포본: getdrivelog.com 로그인 탭에서 첫 화면, 깊은 주소(`/app/cars`) 새로고침, `manifest.webmanifest`·`sw.js`·`offline.html` 열림 확인.
- 앱 코드가 그대로라 로컬 테스트·revert-check 대상 없음(새 테스트 없음).

### 파일 길이
- `ci.yml` 88 → 88줄.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. CI나 배포가 실패하면 GitHub Pages는 그 전 배포본을 그대로 보여 주므로 화면은 안 멈춤. 실패 기록을 보고 수정 착수지시서를 써서 다시 승인받음(되돌리기도 수정 커밋으로).

### 사용자 확인 방법
1. push 뒤 GitHub 저장소 Actions 화면에서 이번 실행이 초록인지 봄.
2. 실행 화면 아래 "Annotations"에 "Node.js 20 is deprecated"가 없으면 성공(Ubuntu 26 안내는 남아도 됨).
3. getdrivelog.com을 새로고침해 평소처럼 열리는지 봄.

### 진행 기록
- 코드 커밋 react-app `b6b0308` — `ci.yml` `uses:` 5줄만 바꿈(88줄 그대로). 큰 번호 태그 5개(v7·v7·v5·v6·v5)가 GitHub에 있는 것 확인. 로컬 검사 대상 없음(앱 코드 그대로).
- push 뒤 CI verify·deploy 초록(run 41045433373), 경고는 Ubuntu 26 안내만 — Node.js 20 경고 없어짐.
- 배포본 seed 계정: 차량 관리 화면 정상, `manifest.webmanifest`·`sw.js`·`offline.html`·`favicon.svg`·`404.html` 200, 깊은 주소(`/app/cars`)는 전과 같이 404.html로 앱이 열림, `.nojekyll`은 예상대로 빠짐(404).
- §5 리뷰: 바뀐 파일 1개 = 건드릴 파일, `uses:` 5줄 외 변경 없음, 새 주석·장치 없음, 88줄 그대로.
