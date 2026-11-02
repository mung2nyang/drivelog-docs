# docs/report.md — 현재 슬라이스 착수지시서

150-B: drivelog-docs `693140b`. 150-A: drivelog-docs `9b8eb01`. 106: drivelog-docs `72a93ed`. 124-B-2: drivelog-docs `abceece`. 124-B-1: drivelog-docs `5c354fd`. 124-A: drivelog-docs `022a710`. 121-B: drivelog-docs `0aa4357`. 긴 주석 줄이기: drivelog-docs `59ddc9d`. 121-A: drivelog-docs `1590ef7`. 120-D: drivelog-docs `2ce5170`. 120-C: drivelog-docs `ae914c9`. 120-B: drivelog-docs `503a3d3`. 120-A: drivelog-docs `81f927a`. 119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 123-A 미수금 CSS(`receivables.css` 419줄) 나누기 `[x]`

로드맵 123(`docs/roadmap.md` 605행, ⑥). 보리가 지시서 작성을 지시했다(2026-11-02 "응"). 123은 CSS 4개라서 파일마다 슬라이스 하나로 나눈다. 이번에는 A(`receivables.css`)만 한다. B~D는 `call-detail-form.css` 285줄, `report.css` 257줄, `daily-inspection.css` 254줄이다.

### 쉬운 요약
- 미수금 화면의 꾸밈 파일 하나가 419줄로, 규칙(200줄)의 두 배가 넘는다. 이것을 화면 조각별로 네 파일로 나눈다.
- 글자 하나 바꾸지 않고 옮기기만 한다. 화면 모양은 그대로다.
- 끝나면 미수금 화면을 고칠 때, 고칠 조각의 파일만 열면 된다.

### 현재 상태 (코드 확인)
- `src/features/receivables/ui/receivables.css`는 419줄이고, 200줄 예외 표시가 없다. 목록 화면(`ReceivablesListPage.jsx` 12행)과 상세 화면(`ReceivablesDetailPage.jsx` 12행)이 둘 다 이 파일을 불러온다.
- 운행 카드(`ReceivableItemCard.jsx`)는 두 화면이 함께 쓴다(두 파일 10행). 카드 스스로는 CSS를 불러오지 않는다.
- 클래스별로 쓰는 곳을 모두 찾았다(클래스마다 `src` 전체 검색).
  - `receivable-*` 클래스는 모두 미수금 기능 안에서만 쓴다. 상세 화면만 쓰는 것, 카드만 쓰는 것, 목록만 쓰는 것으로 깔끔하게 갈린다.
  - 다른 곳과 겹치는 것은 세 가지뿐이다. `.management-badge.car-type`(공용 `.management-badge`보다 강한 두 단계 지정), `.receivable-partial-input-row .clearable-input`·`.input-box`(이 줄 안으로만 범위를 묶음), `.receivables-page`(`management-common.css` 8행과 함께 씀)다. 셋 다 이 파일 안에 그대로 남기거나 같은 선택자 그대로 옮기므로 다른 화면에는 영향이 없다.
- 이 CSS를 읽는 시험은 없다(`src` 시험 파일에서 `receivables.css` 검색 0건).

### 목표 상태 (기대 동작)
1. 네 파일로 나뉘고, 모두 200줄 이하다.
2. 규칙 내용(선택자, 속성, 값)은 한 글자도 바뀌지 않는다. 바뀌는 것은 파일 위치, 머리 주석, 불러오기 줄뿐이다.
3. 목록 화면(두 탭), 상세 화면, 카드의 열림 상태(입금 기록 펼침, 부분 입금 입력 열림)에서 모든 요소의 계산된 스타일이 바꾸기 전과 같다.
4. CI(verify, db-test, deploy)가 초록이다.

### 건드릴 파일
- `src/features/receivables/ui/receivables.css`(남김): 목록·상세 공통 부분. 화면 탭, 화면 안 카드 틀, 월별 묶음 카드, 요약, 상세 보기·완료 버튼, 차량 배지, 묶음과 카드가 함께 쓰는 묶음 선택자다(지금 1~144행). 머리 주석만 바꾼다.
- `src/features/receivables/ui/receivables-detail.css`(새 파일): 상세 화면만 쓰는 위쪽 합계 요약(178~224행)과 아래쪽 전부 입금 버튼(397~419행). `ReceivablesDetailPage.jsx`가 불러온다.
- `src/features/receivables/ui/receivable-item-card.css`(새 파일): 운행 카드 본문. 줄, 정보, 금액, D-day(145~177행)와 날짜·경로·기한·비고(225~282행)다. `ReceivableItemCard.jsx`가 불러온다.
- `src/features/receivables/ui/receivable-item-payment.css`(새 파일): 카드의 입금 처리. 입금 상태, 입금 기록, 입금·부분 입금·되돌리기 버튼, 부분 입금 입력 줄이다(283~396행). `ReceivableItemCard.jsx`가 불러온다.
- `ReceivablesDetailPage.jsx`, `ReceivableItemCard.jsx`: 불러오기 줄만 더한다.
- 머리 주석 최종 문구(파일마다 한 줄):
  - `receivables.css`: `/* 미수금 목록·상세 공통: 화면 탭, 월별 묶음 카드, 요약, 차량 배지. */`
  - `receivables-detail.css`: `/* 미수금 상세 화면: 위 합계 요약과 아래 전부 입금 버튼. */`
  - `receivable-item-card.css`: `/* 미수금 운행 카드 본문: 날짜·경로·금액·기한·비고. */`
  - `receivable-item-payment.css`: `/* 미수금 운행 카드의 입금 처리: 상태, 기록, 입금·부분 입금·되돌리기 버튼. */`
- 지금 머리 주석(1~2행)의 "side-menu.css에서 분리 + 원본 style.css 이식"은 옮긴 경위라서 빼고 위 문구로 바꾼다.
- 공용 영향 목록: 위 "현재 상태"의 세 가지뿐이다. 이번에 그 규칙의 내용과 범위는 바뀌지 않는다.

### 안 건드릴 것
- 규칙 내용, 클래스 이름, JSX의 className은 바꾸지 않는다.
- `receivablesGroupHead.css`(18줄, 목록만 씀)는 이미 따로 있고 짧아서 그대로 둔다.
- 다른 세 CSS(123-B~D)와 `management-common.css` 같은 공용 CSS는 건드리지 않는다.

### 검증
- 자동 검사: CI(lint, 타입, 시험, 빌드, 의존 검사)가 돌린다.
- 옮기기 확인: 바꾸기 전 파일의 규칙 덩이 모음과 바꾼 뒤 네 파일의 규칙 덩이 모음이 같은지 스크립트로 비교한다. 머리 주석은 빼고, 순서를 무시하고 비교한다.
- 화면 확인: 로그인 탭(getdrivelog.com)에서 계산된 스타일을 비교한다.
  - 바꾸기 전 배포본에서 목록 화면(두 탭), 상세 화면, 카드 열림 상태 두 가지를 열고, `.receivables-page` 아래 모든 요소의 계산된 스타일 전체를 지문(해시)으로 남긴다.
  - push·배포 뒤 같은 상태에서 다시 떠서 지문이 같은지 본다.
  - 다르면 어느 요소의 어느 값이 다른지 뽑는다. 다만 저장하는 버튼은 누르지 않는다. 입금 처리, 되돌리기, 부분 입금 확정이 그렇다.
- CSS 순서: 나뉜 파일들은 서로 다른 클래스만 다룬다. 그래서 묶음 안에서 순서가 바뀌어도 결과가 같아야 하고, 위 지문 비교로 확인한다.

### 파일 길이
- `receivables.css` 419 → 약 143줄
- `receivables-detail.css` 새 파일, 약 71줄
- `receivable-item-card.css` 새 파일, 약 92줄
- `receivable-item-payment.css` 새 파일, 약 115줄
- `ReceivableItemCard.jsx` 133 → 135줄
- `ReceivablesDetailPage.jsx` 105 → 106줄
- 모두 200줄 이하다.

### 실패 시 처리
- 새 장치를 만들지 않는다.
- 지문이 다르거나 CI가 빨가면 고치지 않고, 원인과 수정 계획을 다시 써서 승인을 받는다.

### 사용자 확인 방법
- AI가 위 지문 비교로 확인한다.
- 보리는 원하면 휴대폰으로 확인한다. 마이페이지 → 미수금 → 목록 두 탭 → 거래처 하나 눌러 상세 → 입금 기록 펼치기 → 부분 입금 누르기(확정은 누르지 않음). 모양이 전과 같으면 성공이다.

### 진행 기록
- 2026-11-02 보리 확정("웅").
- 바꾸기 전 지문(배포본 `f3d528f`, 로그인 탭, 테스트 계정 2026년 10월). 같은 상태에서 두 번 떠서 같은 값이 나오는 것을 확인했다.
  - 목록 첫 탭: 요소 57개 `5047f3b056`.
  - 목록 둘째 탭(입금 예정 미수금): 비어 있음, 요소 15개 `b94cd31d5e`.
  - 상세(테스터거래처): 요소 65개 `2e822fc324`.
  - 같은 상세에서 첫 카드의 부분 입금 입력을 연 상태: 요소 69개 `712ae45df3`.
- 테스트 계정에 부분 입금·입금 기록·입금 예정 미수금 데이터가 없어서 확인하지 못한 상태가 있다. 입금 상태 partial, 입금 기록 펼침, 되돌리기 버튼, 목록 둘째 탭의 카드다. 이 상태들은 규칙 덩이 비교로 갈음한다. 저장해야 생기는 상태라 만들지 않았다.
- 지시서와 다른 점: 줄 수가 `receivables.css` 142줄(약 143), `receivables-detail.css` 72줄(약 71)이다. 나머지는 지시서대로다.
- 규칙 덩이 비교: 바꾸기 전 55덩이와 나눈 뒤 55덩이가 같다(머리 주석 빼고, 순서 무시).
- `npm run lint`, `npm run depcheck`(위반 0), `npm run build` 통과. 빌드에서 네 파일은 한 묶음(`ReceivablesPage-*.css`)으로 들어간다.
- react-app main 코드 커밋 `a022e12`. 보리 push를 기다린다.
- 보리 push(`a022e12`). CI 41418860126 초록(verify·db-test·deploy 성공).
- 배포본 지문 비교. 새 CSS 묶음 `ReceivablesPage-CO4N5RHy.css`를 불러온 것을 확인한 뒤 같은 상태에서 다시 떴다. 4개 모두 바꾸기 전과 같다.
  - 목록 첫 탭: 57개 `5047f3b056`.
  - 목록 둘째 탭: 15개 `b94cd31d5e`.
  - 상세(테스터거래처): 65개 `2e822fc324`.
  - 부분 입금 입력을 연 상태: 69개 `712ae45df3`.
  - 부분 입금 확정은 누르지 않았다.
- 목표 대조:
  - 1: 네 파일 142·72·92·115줄로 모두 200줄 이하.
  - 2: 규칙 55덩이가 그대로.
  - 3: 지문 4개가 같음. 데이터가 없는 상태는 2로 갈음.
  - 4: CI 초록.
- 범위: `a022e12`은 미수금 `ui/` 6개 파일만 바꿨다(CSS 4개, JSX 2개). 새 주석은 머리 주석 4줄뿐이고 지시서 문구 그대로다.
- 2026-11-02 보리 최종 승인("승인").
