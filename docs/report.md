# docs/report.md — 현재 슬라이스 착수지시서

내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 147. 문구 3개 `[x]`

- 근거: 로드맵 147(⑤, 전수점검 J1·E1·E2 `full-code-review.md`).
- 2026-11-01 보리가 "응"으로 조사와 지시서를 요청했다.
- 정해 둔 순서의 마지막이다(산재 빼기 다음).

### 쉬운 요약
1. **초대 문자**: 기사에게 보내는 초대 문자가 "[마이페이지 > 소속 연결]에서 코드를 입력하라"고 안내한다. 그런데 연동 전 기사 앱에는 그런 메뉴가 없다. 실제 이름인 "[마이페이지 > 초대 코드 입력]"으로 바꾼다.
2. **불러오기 실패 안내**: 로그인 계정이 서버에서 기록을 못 불러오면 "이 휴대폰에 있는 기록으로 계속 쓸 수 있습니다."라고 나온다. 하지만 로그인 기록은 휴대폰에 남기지 않는다. 그래서 빈 화면을 자기 기록 전부로 믿을 수 있다. "기록이 빠져 있을 수 있으니 다시 시도하라"는 뜻으로 바꾼다.
3. **고객센터 ←**: 마이페이지에서 연 고객센터의 ←가 마이페이지가 아니라 홈으로 간다. 다른 마이페이지 화면처럼 연 곳으로 돌아가게 한다. 같은 문제가 있는 "관리자" 화면도 같이 맞춘다.

### 현재 상태 (코드 확인)
- 초대 문자: `features/drivers/api/driverInviteSms.js:24`에 "[마이페이지 > 소속 연결]"이 있다.
  - 코드를 넣는 곳은 마이페이지 [초대 코드 입력]이다(`features/account/ui/MyPage.jsx:160`, 연동 안 된 로그인 계정에만 보임).
  - "소속 연결" 카드는 이미 연동된 기사의 개인정보 화면에만 있다(`features/drivers/ui/EmployerLinkCard.jsx:60`).
- 불러오기 실패 안내: `app/shell/HydrationRetryBanner.jsx:55`의 문구다.
  - 불러오기에 실패하면(`hydration.status = 'failed'`) 거래처·차량 같은 서버 저장은 막힌다(`data/cloud/cloudSession.ts:84` "아직 기록을 불러오는 중입니다…").
  - 개발용 기록 문구 `app/boot.js:47` "로컬 데이터로 계속합니다"도 같은 잘못된 전제를 담고 있다.
- 고객센터 ←: `app/routes/AppShellRoutes.jsx:94` 고객센터만 `goBack('/app')`으로 홈이 고정돼 있다.
  - 다른 마이페이지 화면(차량·거래처·설정·공지 등)은 `goBack(backTarget)`이다. 마이페이지에서 열었으면(`?back=mypage`) 마이페이지로, 아니면 홈으로 간다.
  - 관리자(`:95`)는 반대로 `goBack('/app/me')`로 마이페이지가 고정돼 있다. 그래서 옆 메뉴(홈 쪽)에서 열어도 ←가 마이페이지로 간다.
- sot: 세 문구와 ← 동작을 정한 줄이 없다(`grep 소속 연결\|휴대폰에 있는 기록\|고객센터` — §4-15는 관리자 권한만 정함).

### 목표 상태 (기대 동작)
1. 초대 문자 본문이 "운행일지 앱 실행 후 [마이페이지 > 초대 코드 입력]에서 위 코드를 입력해 주세요. (초대 저장 후 7일 동안 사용할 수 있습니다)"가 된다. 나머지 문장은 그대로다.
2. 불러오기 실패 띠 문구가 "서버에서 기록을 불러오지 못했습니다. 지금 보이는 기록은 빠져 있을 수 있으니 [다시 시도]를 눌러 주세요."가 된다. [다시 시도] 버튼과 알림은 그대로다.
3. 개발용 기록 문구 `app/boot.js:47`을 "[boot] hydrate 실패, 서버 기록 없이 계속합니다."로 바꾼다(화면에는 안 보임).
4. 마이페이지 → 고객센터 → ←는 마이페이지로 간다. 옆 메뉴에서 연 고객센터의 ←는 지금처럼 홈으로 간다.
5. 관리자도 같은 규칙이다. 마이페이지에서 열면 ←가 마이페이지로, 옆 메뉴에서 열면 홈으로 간다.
   - 알림에서 연 관리자 화면(`?back` 없음)은 ←가 마이페이지가 아니라 홈으로 바뀐다. 알림에서 연 다른 화면과 같아진다.

### 건드릴 파일
- `react-app/src/features/drivers/api/driverInviteSms.js` — 문자 안 메뉴 이름 1곳.
- `react-app/src/app/shell/HydrationRetryBanner.jsx` — 띠 문구 1줄.
- `react-app/src/app/boot.js` — 개발용 기록 문구 1줄.
- `react-app/src/app/routes/AppShellRoutes.jsx` — 고객센터·관리자 `onBack`을 `goBack(backTarget)`으로 바꾼다.
- 테스트
  - `react-app/src/data/sync/inviteCode.security.test.js` — 기존 "초대 문자" 테스트에 확인을 더한다. 본문에 "[마이페이지 > 초대 코드 입력]"이 있고 "소속 연결"이 없어야 한다.
  - `react-app/src/app/shell/HydrationRetryBanner.test.js` — **새 테스트 파일**이다. 불러오기 실패 상태면 새 문구가 보이고 "휴대폰에 있는 기록"이 없다. 실패가 아니면 띠가 없다.
  - `react-app/src/app/routes/AppShellRoutes.back.test.js` — **새 테스트 파일**이다.
    - 화면 묶음(`lazyPages.js`)을 ← 단추만 있는 가짜로 바꾼다.
    - `/app/support?back=mypage`·`/app/admin/inquiries?back=mypage`에서 ←를 누르면 `goBack('/app/me')`가 불려야 한다.
    - `?back=home`에서는 `goBack('/app')`이 불려야 한다.

### 안 건드릴 것
- `AppShell.jsx`의 `goBack`·`goToPage`(돌아가기 규칙 자체)는 그대로 쓴다.
- 옆 메뉴·마이페이지 목록·알림 화면은 바꾸지 않는다.
- 개인정보 화면의 "소속 연결" 카드와 연동 해제 안내(`LinkStatusChip.jsx`의 "개인정보 > 소속 연결")는 그대로 둔다. 연동된 기사에게는 맞는 안내다.
- 불러오기 실패 때 저장을 막는 동작과 다시 시도 동작은 그대로 둔다.
- DB·서버는 바꾸지 않는다.

### 검증
- 자동 검사: `npm test`, `npm run typecheck`, `npm run lint`, `npm run depcheck`, `npm run build`.
- 고치기 전 실패 확인: 바꾼 소스 4개를 되돌리면 새·바뀐 테스트가 실패해야 한다.
  - "실패가 아니면 띠 없음"과 "옆 메뉴에서 연 고객센터 ← 홈"은 고치기 전에도 통과한다(안전 확인).
- 로컬 화면(손님 모드)
  - 마이페이지 → 고객센터 → ← 를 누르면 마이페이지로 가야 한다.
  - 옆 메뉴 → 고객센터 → ← 를 누르면 홈으로 가야 한다.
  - 초대 문자와 실패 띠는 손님 모드에서 볼 수 없어 테스트로 대신한다.
- 배포본: 로그인 탭에서 고객센터 ← 두 경로만 본다. 문자는 보내지 않는다.

### 파일 길이
- `driverInviteSms.js` 26, `HydrationRetryBanner.jsx` 61, `boot.js` 50, `AppShellRoutes.jsx` 104 — 모두 줄 수가 그대로다.

### 실패 시 처리
- 새 장치는 만들지 않는다.
- 검사가 실패하면 고치지 않고 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법
- 마이페이지 → 고객센터 → 왼쪽 위 ← 를 누른다. 마이페이지로 돌아가면 성공이다.
- 옆 메뉴(≡) → 고객센터 → ← 를 누른다. 홈으로 가면 성공이다.
- 다음에 기사 초대 문자를 보낼 때 본문에 "[마이페이지 > 초대 코드 입력]"이 보이면 성공이다.

### 진행 기록
- 2026-11-01 보리 "응"으로 진행을 승인했다(관리자 ←도 같이). react-app `e910465` 코드 커밋(푸시 전).
- 자동 검사 모두 통과: `npm test`(957개, 942개 두 묶음), typecheck, lint, depcheck, build.
- 고치기 전 실패 확인
  - 초대 문자, 실패 띠 문구, 고객센터 ←, 관리자 ← 테스트 4개는 고치기 전 코드에서 실패하고 고친 뒤 통과한다.
  - "실패가 아니면 띠 없음"은 고치기 전에도 통과한다(안전 확인).
  - 지시서와 다른 점: "옆 메뉴에서 연 고객센터 ← 홈" 확인은 같은 테스트 안에 넣었다. 그래서 따로 통과하는 안전 확인은 없다.
- 새 테스트 파일 `AppShellRoutes.back.test.js`는 화면 묶음(`lazyPages.js`)만 가짜로 바꿨다. `.jsx` 파일을 흉내 내는 방법은 쓸 수 없어 홈·일지 화면은 진짜로 불러온다.
- 로컬 화면(손님 모드, 개발 서버가 다시 켜져 5174 포트)
  - 마이페이지 → 고객센터(`?back=mypage`) → ← 를 누르면 `/app/me`로 갔다.
  - 옆 메뉴 → 고객센터(`?back=home`) → ← 를 누르면 `/app`으로 갔다.
  - 관리자 화면과 초대 문자·실패 띠는 손님 모드에서 볼 수 없어 테스트로 대신했다.
- 줄 수는 4개 파일 모두 그대로다.
- 푸시 뒤 CI verify·deploy 초록(run 41278611921).
- 배포본 확인(seed 로그인 탭, 화면 이동만 하고 계정 자료는 바꾸지 않음)
  - ← 버튼 4가지 경로 모두 맞다.
    - 마이페이지 → 고객센터 → ← : `/app/me`
    - 마이페이지 → 관리자 → ← : `/app/me`
    - 옆 메뉴 → 고객센터 → ← : `/app`
    - 옆 메뉴 → 관리자 → ← : `/app`
  - 배포 파일에서 새 문구 확인: 초대 문자는 `DriverConnectionPage-*.js`, 실패 띠는 `index-*.js`에 들어 있다. 옛 문구 두 개는 어디에도 없다.
- §5 리뷰
  - 바뀐 파일은 지시서 "건드릴 파일" 7개(소스 4, 테스트 3)와 같다.
  - 새 파일은 지시서에 적은 테스트 2개다.
  - 타입 꼼수·로드맵 번호는 없다.
  - 줄 수는 그대로다.
