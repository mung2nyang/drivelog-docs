# docs/report.md — 현재 슬라이스 착수지시서

144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 143. 운송비 내역서 [공유] 파일 모양이 다운로드와 다름 `[x]`

로드맵 143(⑤, 전수점검 G2 `full-code-review.md` 5-2).
- 2026-10-30 보리 "응"(조사·지시서 요청).
- 순서상 144 다음이다.

### 쉬운 요약
- [PDF 다운로드]·[이미지 저장]으로 만든 내역서는 날짜 표를 두 줄로 나눠 한 장에 담는다.
- [공유]로 카카오톡·문자에 보내는 파일은 날짜 표가 한 줄로 길게 이어져 여러 장이 된다. 거래처가 받는 파일이 사장님이 내려받은 파일과 다르다.
- [공유] 파일도 다운로드와 똑같이 두 줄 표로 만들어서 두 파일이 같아지게 한다.

### 현재 상태 (코드 확인)
- 두 줄 표 조건
  - `ReportSummaryContent.jsx:67`: `isExporting`일 때만 두 줄(`report-split-container`).
  - `ReportPage.jsx:120`: `isExporting={saving !== null}`. `saving`은 `useFileDownload`의 저장 중 표시라서 다운로드할 때만 켜진다.
- 공유 경로: `ReportShareModal.jsx:35-39`이 자기 `buildFile`로 `buildReportExportFile(exportRef.current, …)`를 바로 부른다. 이때 저장 중 표시는 켜지지 않으므로 화면의 한 줄 표를 그대로 찍는다.
- 다운로드가 두 줄로 찍히는 순서
  - 버튼 누름 안에서 `setSaving`을 한다.
  - 그다음 `buildReportExportFile`이 `await import('html2pdf.js')`를 하는 사이에 화면이 두 줄로 다시 그려진다.
  - 그 뒤에 찍는다.
  - 공유도 `ShareFileModal.jsx`의 버튼 누름 안에서 곧바로 `buildFile`을 부르므로, 같은 방식이면 같은 순서가 된다.
- 세부 내역 보기(`ReportDetailView`)에는 두 줄 표가 없어 다운로드·공유가 이미 같다.
- 일상점검표 공유(`DailyInspectionShareModal`)는 이미 화면이 넘겨준 `buildFile`을 쓰고, 두 줄 표 같은 구분도 없다.
- sot에 내보내기 모양에 대한 규칙이 없다(`grep 2단\|두 줄로\|split` 결과 없음).

### 목표 상태 (기대 동작)
1. 요약 보기 [공유]에서 카카오톡·문자 어느 쪽이든, PDF·이미지 어느 쪽이든 파일을 만드는 동안 날짜 표가 두 줄이다. 파일 모양이 [PDF 다운로드]·[이미지 저장]과 같다.
2. 파일을 다 만들면(실패해도) 화면은 다시 한 줄 표로 돌아온다. 다운로드와 같다.
3. 파일 이름, 공유 문구, 문자 받는 사람, 실패·미지원 안내는 그대로다.
4. 세부 내역 보기와 일상점검표 공유는 바뀌지 않는다.

### 건드릴 파일
- `react-app/src/features/documents/ui/ReportPage.jsx`
  - `sharing` 상태를 하나 둔다.
  - 공유용 `buildShareFile(type)`을 만든다. `setSharing(true)` → 다운로드와 같은 `buildFile(type)` → 끝나면(실패해도) `setSharing(false)` 순서다.
  - `isExporting={saving !== null || sharing}`로 바꾼다.
  - `ReportShareModal`에는 `exportRef` 대신 `buildFile={buildShareFile}`을 넘긴다.
  - 주석: `// [공유] 파일도 내려받기와 같은 내보내기 모양(날짜 표 두 줄)으로 만든다.`
- `react-app/src/features/documents/ui/ReportShareModal.jsx`
  - `exportRef` 속성을 없애고 `buildFile` 속성을 받는다(일상점검표 공유 창과 같은 방식).
  - 자기 `buildFile`과 쓰지 않게 되는 가져오기(`buildReportExportFile`·`reportBaseFileName`)를 지운다.
- 테스트
  - `react-app/src/features/documents/ui/ReportPage.test.js` — 새 테스트 1개. 가짜 html2pdf가 그리기 시작할 때 화면에 두 줄 표가 있었는지 적어 둔다.
    - 날짜가 있는 달에서 [공유] → 카카오톡 [PDF로 보내기]를 누르면 그릴 때 두 줄이고, 끝나면 한 줄로 돌아온다.
    - 같은 방식으로 다운로드도 두 줄인지 함께 확인한다.
  - `react-app/src/features/documents/ui/ReportShareModal.test.js` — 창을 그리는 4곳의 `exportRef`를 `buildFile: async () => fakeFile`로 바꾼다. 검사 내용은 그대로 둔다.

### 안 건드릴 것
- `ShareFileModal.jsx`(공용 창)·`DailyInspectionShareModal.jsx`·`DailyInspectionExportBar.jsx` — 일상점검표는 두 줄 표 구분이 없다.
- `ReportSummaryContent.jsx`·`reportExport.js`·`useFileDownload.js` — 두 줄 표 그리기와 파일 만들기는 그대로 쓴다.
- 공유 중에 [PDF 다운로드]·[이미지 저장]·[공유] 버튼을 잠그는 것 — 지금도 공유는 잠그지 않는다. 이번 범위 밖이다.
- 공용 CSS·DB는 바꾸지 않는다.

### 검증
- 자동 검사: `npm test`, `npm run typecheck`, `npm run lint`, `npm run depcheck`, `npm run build`.
- 고치기 전 실패 확인
  - `ReportPage.jsx`·`ReportShareModal.jsx` 두 파일을 같이 되돌리고 새 테스트를 돌린다(한쪽만 되돌리면 넘기는 속성이 서로 안 맞는다).
  - 기대 결과: 새 테스트의 "공유 그릴 때 두 줄"은 실패하고, "다운로드 두 줄"은 고치기 전에도 통과한다(안전 확인). `ReportShareModal.test.js`는 속성 이름만 바꾼 것이라 결과를 그대로 보고한다.
- 로컬 화면(손님 모드): 내역서 요약에서 [공유] → [PDF로 보내기]. 공유를 지원하지 않는 브라우저면 안내가 뜬다. 파일을 만들 때 두 줄이었는지는 테스트로 확인한다.
- 배포본: 배포 파일에 새 코드가 들어갔는지만 본다. 실제 보내기는 하지 않는다.

### 파일 길이
- `ReportPage.jsx`: 167 → 약 176
- `ReportShareModal.jsx`: 57 → 약 48
- 테스트 파일은 제한 밖(`ReportPage.test.js` 272 → 약 305).

### 실패 시 처리
- 새 저장 장치·재시도 장치는 만들지 않는다.
- 검사가 실패하면 고치지 않고 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법
- 운송 내역이 여러 날 있는 달의 서류 발급 → 운송비 내역서 [전체]를 연다.
- [공유] → 카카오톡 [PDF로 보내기]로 보낸(또는 공유 화면까지 띄운) 파일을 연다. 날짜 표가 두 줄로 나뉘어 [PDF 다운로드] 파일과 같은 모양이면 성공이다.
- 파일을 다 만든 뒤 화면 표가 한 줄로 돌아와 있는지도 본다.

### 진행 기록
- 2026-10-30 보리 "응"으로 진행 승인. react-app `c086bca` 코드 커밋(푸시 전).
- 자동 검사 모두 통과: `npm test`(957개, 938개 두 묶음), typecheck, lint, depcheck, build.
- 고치기 전 실패 확인
  - 새 테스트는 고치기 전 코드에서 "공유도 두 줄" 단계에서 실패했다. 같은 테스트의 "내려받기는 두 줄" 단계는 고치기 전에도 통과했다.
  - `ReportShareModal.test.js`의 카카오톡 테스트 2개는 고치기 전 코드에서 실패했다. 속성 이름(`exportRef` → `buildFile`)을 바꿨기 때문이다. 렌더·문자 테스트 2개는 통과했다.
- 지시서와 다른 점: `ReportShareModal`에서 쓰지 않게 된 `year`·`month` 속성도 뺐다. 이에 따라 `ReportPage`가 넘기던 값과 공유 창 테스트 4곳의 값도 함께 뺐다.
- 로컬 화면(손님 모드)
  - 10월에 1~20일 운행 기록을 임시로 넣고 운송비 내역서 [공유] → 카카오톡 [이미지로 보내기]를 눌렀다. 이때 `navigator.share`는 가짜로 바꿔 실제 공유 창이 뜨지 않게 했다.
  - 파일을 만드는 동안 두 줄 표가 생겼다가 끝나면 한 줄로 돌아왔다.
  - 파일 이름은 `2026년_10월_운송비내역서.png`로 그대로였다.
  - 확인 뒤 임시 기록은 원래대로(빈 값) 되돌렸다.
- 줄 수: `ReportPage.jsx` 177, `ReportShareModal.jsx` 45.
- 푸시 뒤 CI verify·deploy 초록(run 41066534096).
- 배포본 확인(seed 로그인 탭, 읽기만)
  - 서류 발급 화면 파일 `DocumentIssuePage-TPCXssyB.js`에 공유용 파일 만들기(만드는 동안 켜고 끝나면 끔)와 `isExporting: 저장 중 || 공유 중`이 들어 있다.
  - 실제 보내기는 하지 않았다.
- §5 리뷰
  - 바뀐 파일은 지시서 "건드릴 파일" 4개와 같다.
  - 새 파일·새 저장 키·타입 꼼수·로드맵 번호는 없다.
  - 코드 주석 1개는 지시서 문구 그대로다.
  - 테스트의 `eslint-disable-next-line no-await-in-loop` 1줄은 같은 파일의 기존 기다리기(`saveAs`)와 같은 형식이다.
  - 기존 가짜 html2pdf `from()`은 한 줄을 여러 줄로 펴고 기록 1줄만 더했다. 기존 검사는 약해지지 않았다.
  - 줄 수 제한 안이다.
- 배포본 공유 파일 확인(보리 "너가 확인해", seed 로그인 탭)
  - 실제로 보내지 않도록 `navigator.share`를 가짜로 바꾸고, 운송비 내역서 2026년 10월(11일치) [공유] → 카카오톡 [이미지로 보내기]로 파일을 받았다.
  - 받은 파일은 `2026년_10월_운송비내역서.png`, 1588×1586이다.
  - 화면에 띄워 보니 날짜 표가 두 줄(왼쪽 5~13일, 오른쪽 14~26일)로 나뉘어 있었다.
  - 만드는 동안만 두 줄이었고, 끝나면 화면은 한 줄로 돌아왔다.
  - 확인 뒤 띄운 그림을 지우고 새로고침했다. 계정 자료는 바꾸지 않았다.
