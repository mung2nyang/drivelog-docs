# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 99-B 서류(운송비 내역서) 화면 나누기 — 내려받기 코드를 한 벌로 `[x]`

> **결정 `[확인: 2026-10-21 보리 — "권하는대로 일상점검표와 같이 쓰는 걸로"]`**: 확인할 점 1 권함대로 — 훅 이름 `useFileDownload`, 운송비 내역서와 일상점검표(`DailyInspectionExportBar.jsx`)가 같이 씀. 저장 중 상태는 하나라 운송비 내역서도 저장하는 동안 두 버튼이 함께 막힘. 승인된 설계의 `useReportDownload`(운송비 내역서만)는 이 결정으로 바뀜.
> 다음: 터미널 AI가 교차검증 뒤 99-B 착수.

> **99-B 진행(2026-10-21) — 코드 커밋 `546b805`** `[x]`: 보리 "착수지시서 확정 작업진행해".
> - 교차검증(AGENTS §1): 줄 위치·설정 값·쓰는 곳 모두 지시서와 같음. 운송비 이미지 설정(`windowWidth`·`windowHeight`, 종이 설정 없음)과 PDF 설정(여백·화질·A4 세로)이 `reportExport.js`와 값까지 같음 확인. 고친 것 없음.
> - 테스트 먼저: `ReportPage.test.js`에 내려받기 3개(가짜 html2pdf, 링크 누르기 기록) — **옛 코드에서 6개 모두 통과**.
> - 한 일(코드 바뀜, 커밋 안 함): `reportExport.js`에 `buildReportExportFile`·`downloadFile`(95줄), 새 `hooks/useFileDownload.js`(31줄), `report.js`에 `reportBaseFileName` 넣고 이미지 이름 함수 2개 지움(64줄), `ReportPage.jsx` 236 → 163줄, `ReportShareModal.jsx` 61 → 53줄, `DailyInspectionExportBar.jsx` 90 → 63줄. 테스트: `ReportPage.test.js` PDF 링크 이름 1개 더함(7개 통과), `reportExport.test.js` 2개 더함(4개 통과), `report.test.js` 묶음 2개(테스트 2·단언 4) 지움(30개 통과).
> - 결과: unit 866 통과, 빌드·`depcheck` 통과, lint 경고 50 그대로. **실패 2가지**:
>   ① `features/documents/ui/ReportShareModal.test.js` 파일 전체 실패 — 이 기존 테스트가 `reportExport.js`를 가짜로 바꾸면서 `createReportImageFile`·`createReportPdfFile` 두 이름만 줌. 공유 창이 이제 `buildReportExportFile`을 가져오므로 "그런 이름 없음"으로 불러오기부터 실패. 지시서 "건드릴 파일"에 이 테스트가 빠져 있었음(제가 교차검증 때 못 잡음).
>   ② 타입 검사 — 제가 새로 쓴 `reportExport.test.js:66`의 링크 누르기 기록 함수에서 `this` 타입 표시가 없음(TS2683 2건). `ReportPage.test.js`는 `@ts-check`가 없어 같은 줄이 안 걸림.
>   화면 코드 문제 아님.
> - **수정 계획(승인 필요)**: ① `ReportShareModal.test.js`의 가짜 목록에 `buildReportExportFile: async () => fakeFile` 한 줄 더함(단언은 그대로 — 공유 창이 만든 파일을 넘기는지만 봄; 내보내기 모드 붙였다 떼기는 `reportExport.test.js`의 새 테스트가 봄) ② `reportExport.test.js:66` 함수에 `/** @this {HTMLAnchorElement} */` 한 줄. 고친 뒤 전체 테스트·타입 검사·개발 서버 확인 → 커밋.
> - 수정(보리 "승인" `[확인: 2026-10-21 보리]`): 위 ①·② 그대로 한 줄씩.
> - 결과: `npm test` unit 866 + app 655(4개 늘어남 = `ReportPage` 4 + `reportExport` 2 − `report` 2) = 1521 전부 통과, 타입 검사 0건, 빌드·`depcheck`(위반 0) 통과, lint 경고 50 그대로. `pdf-export-mode`를 붙이는 운영 코드는 이제 `reportExport.js` 한 곳뿐(grep).
> - 개발 서버(비회원): 서류 발급 → 운송비 내역서 탭 버튼 "PDF 다운로드 / 이미지 저장 / 공유", [PDF 다운로드] → "PDF 저장" 확인 창(취소함), [공유] → "내역서 보내기" 창. 콘솔 오류 0건. 실제 내려받기는 누르지 않음(보리 휴대폰 확인). 일상점검표 탭은 비회원에게 안 보여(`DocumentIssuePage.jsx:45` 로그인 + 점검표 사용일 때만) 배포본에서.
> - 코드 커밋 react-app `546b805`(push 안 함).
> - **push 뒤 CI 빨강(run 39539944282, 데스크톱 AI 확인)**: verify의 테스트 단계만 실패(타입 검사·빌드·depcheck 성공), **deploy는 건너뜀 — 배포본은 아직 99-C 빌드**. 그래서 보리 휴대폰 "다운받아져" 확인은 옛 코드(html2pdf `.save()`)를 본 것이고, 새 코드로 다시 확인해야 함.
>   실패 3개 모두 `ReportPage.test.js`의 새 내려받기 테스트: ① 이미지 저장 — 링크 `[]`(기대 `…운송비내역서.png`) ② 이미지 그리기 실패 — 알림 `[]` ③ PDF 링크 — `['…운송비내역서.png']`(기대 `.pdf`). ③의 `.png`는 ①의 이미지 내려받기가 테스트가 끝난 **뒤에** 끝나서 다음 테스트로 넘어온 것.
>   원인: 테스트의 `saveAs`가 [저장]을 누른 뒤 **한 번만**(`setTimeout 0`) 기다리고 바로 확인함. 이미지 쪽은 `import('html2pdf.js')` → 그림 그리기 → PNG 만들기로 기다릴 일이 여러 번이라, 빠른 내 컴퓨터에선 한 번에 끝나지만 CI에선 덜 끝난 상태에서 확인함. 화면 코드 문제가 아니라 테스트의 기다리는 방법 문제.
> - **수정 계획(승인 필요)**: `ReportPage.test.js`의 `saveAs`만 고침 — 고정 한 번 대신 **알림이 뜰 때까지**(성공·실패 모두 알림으로 끝남) 짧게 여러 번 기다리고, 정한 횟수 안에 안 뜨면 "알림이 안 뜸"으로 실패. 단언은 하나도 안 바꿈. 확인: 전체 테스트를 3번 돌려 모두 통과, 기다리는 줄을 옛 방식으로 되돌리면 여전히 불안정할 수 있음은 기록만. 수정 커밋 하나 더(이력 되감기 없음) → 보리 push → CI 초록·deploy 확인 → **보리 휴대폰으로 다시** 운송비 내역서 PDF·이미지, 일상점검표 PDF.
> - 수정 계획 보리 "승인" `[확인: 2026-10-21 보리]`. 다음: 터미널 AI가 `saveAs` 기다리기만 고쳐 수정 커밋.
> - **수정(2026-10-21) — 수정 커밋 `952ba87`**: 보리 "수정 착수지시서 작업해줘". `ReportPage.test.js`의 `saveAs`만 고침 — [저장]을 누른 뒤 알림이 뜰 때까지 20ms씩 최대 100번(약 2초) 기다리고, 그래도 안 뜨면 `알림이 안 뜸`으로 실패. 단언·앱 코드 변경 0.
> - 1초 뒤 링크 주소 정리(`URL.revokeObjectURL`)가 테스트 정리 뒤에 돌아도 괜찮은지 확인: 테스트 환경에 원래 함수가 있어(`typeof` function) 되돌린 뒤에도 오류 없음.
> - 결과: `ReportPage.test.js` 혼자 3번 7개 모두 통과, `npm test` 전체 3번 모두 866 + 655 = 1521 통과, 타입 검사 0건, lint 경고 50 그대로. 옛 방식(한 번만 기다리기)이 이 컴퓨터에선 통과했던 것처럼 빠른 컴퓨터에선 차이가 안 보이므로, CI 초록이 실제 확인.
> - 수정 커밋 react-app `952ba87`(push 안 함, `546b805` 위에 하나 더 — 이력 되감기 없음).
> - push 뒤 CI verify·deploy 초록(run 39541492066) — 테스트·타입 검사·빌드·의존 방향 검사 모두 성공, 이번엔 deploy까지 됨.
> - §5 리뷰(데스크톱 AI, `546b805`+`952ba87`): ①범위 = 지시서 파일 + 빠졌던 `ReportShareModal.test.js` 1줄(수정 승인분) ②새 층·저장 키 0, 새 파일 `hooks/useFileDownload.js` 1개(지시서대로) ③타입 꼼수 0 ④`ReportPage` 236 → 164, `useFileDownload` 31, `reportExport` 95, `report` 64, `DailyInspectionExportBar` 90 → 63, `ReportShareModal` 61 → 53 ⑤테스트: 새 내려받기 4개는 링크 이름·알림·html2pdf 호출 순서·`pdf-export-mode` 떼기를 직접 봄, 수정 커밋은 기다리기만 바뀌고 단언 0줄 변경. 지운 것은 이미지 이름 함수 2개의 테스트 2개(단언 4) ⑥공용 영향: `pdf-export-mode`를 붙이는 운영 코드는 `reportExport.js` 한 곳뿐(grep), 일상점검표의 `downloadFile`·`save`는 글자 그대로 옮겨짐(콘솔 앞말만 `일상점검표 내보내기 실패:` 그대로) ⑦목표 1~6 대조. 작은 차이 1: 예전 화면은 내보낼 영역이 없으면 조용히 끝났고, 지금은 실패 알림(그 영역은 화면에 늘 있어 실제로는 안 생김).
> - 배포본 로그인 확인(데스크톱 AI, 새 빌드 `index-CH2pYiRv.js`): 운송비 내역서 탭 버튼 3개, [PDF 다운로드] → "PDF로 저장하시겠습니까?" 확인 창(취소함), [공유] → "내역서 보내기" 창, 일상점검표 탭 버튼 3개·안내 문구. 콘솔 오류 0건. 실제 내려받기는 누르지 않음 — **보리 휴대폰으로 다시 확인 필요**(앞선 "다운받아져"는 deploy 건너뛴 옛 빌드).
> - 보리 휴대폰 확인 "운송비 내역서 PDF·이미지, 일상점검표 PDF 다운되는 것 확인", 최종 승인(2026-10-21). 다음 99-A 운행일지.

보리 지시 "작성해"(2026-10-21). 97·99 분리설계 `[확인: 2026-10-21 보리 — "권하는대로 진행하는걸로"]`(설계 원문 `git show 1e9e6e2:docs/report.md`의 "분리설계" 절, 확인할 점 2 "PDF도 파일 만들기 → 링크로 내려받기")의 세 번째 슬라이스. 순서 97 → 99-C → **99-B** → 99-A.

### 쉬운 요약
- 운송비 내역서 화면(236줄)의 "PDF 다운로드"·"이미지 저장" 코드는 공유 창과 같은 설정을 한 벌 더 갖고 있습니다. 이걸 이미 있는 공용 코드로 바꿔서 한 벌만 남깁니다. 화면 파일은 약 165줄이 됩니다.
- 다시 보니 일상점검표 탭의 내려받기 버튼(`DailyInspectionExportBar.jsx`)이 **이미 정확히 그 방식**("파일 만들기 → 링크로 내려받기", 같은 알림 문구)으로 되어 있습니다. 그래서 새로 만들 훅을 두 곳이 같이 쓰게 하는 것을 권합니다(확인할 점 1).
- 파일 이름·용지·화질·알림 문구는 그대로입니다. 바뀌는 것은 PDF를 내려받는 방법(정해 둔 것)과, 내려받는 동안 두 버튼을 함께 막는 점(확인할 점 1)입니다.

### 현재 상태 (코드 확인)
- `features/documents/ui/ReportPage.jsx` 236줄. `handleDownloadPdf`(72~99줄)는 html2pdf를 직접 불러 `.save()`로 저장하고, `handleDownloadImage`(101~145줄)는 그림을 그려 링크로 내려받음. 둘 다 `pdf-export-mode`를 붙였다 떼고, 저장 중 상태를 **따로 2개**(`savingPdf`·`savingImage`)로 둠 — 하나를 저장하는 동안 다른 버튼은 눌림.
- `features/documents/api/reportExport.js`(67줄): `createReportPdfFile`·`createReportImageFile` — 위 두 함수와 **같은 html2pdf 설정**. 지금 공유 창(`ReportShareModal.jsx`)과 일상점검표(`DailyInspectionExportBar.jsx`)가 씀.
- `ReportShareModal.jsx`(61줄): `pdf-export-mode` 붙이기 → 파일 만들기 → 떼기를 직접 함(27~43줄). 파일 기본 이름도 직접 만듦(`buildReportFileName(...).replace(/\.pdf$/, '')`).
- `DailyInspectionExportBar.jsx`(90줄): 자기 안에 `downloadFile(file)`(12~22줄, 링크 내려받기)과 `save(type)`(45~57줄, 저장 중 상태 **1개** `'pdf'|'image'|null`, 알림 문구 4개가 `ReportPage`와 글자까지 같음). 저장하는 동안 두 버튼 다 막힘.
- `api/report.js`의 `buildReportImageFileName`·`buildDetailReportImageFileName`은 `ReportPage`만 씀(+ `report.test.js`).
- 테스트: `ReportPage.test.js` 3개는 탭·세부 창·뒤로가기만. **내려받기 테스트 0개**. `DailyInspectionExportBar` 테스트 0개. `reportExport.test.js` 2개는 방향(세로·가로)과 파일 이름.

### 목표 상태 (기대 동작)
1. `reportExport.js`에 두 함수 더함:
   - `downloadFile(file)` — `DailyInspectionExportBar`에서 그대로 옮김.
   - `buildReportExportFile(element, type, baseName)` — `pdf-export-mode`를 붙이고 `createReportPdfFile`/`createReportImageFile`로 파일을 만든 뒤 반드시 뗌(공유 창 27~43줄 내용 그대로).
2. 새 `features/documents/hooks/useFileDownload.js`(약 40줄): `useFileDownload(buildFile, logLabel)` → `{ saving, download }`. `saving`은 `'pdf'|'image'|null` 하나, `download(type)`는 이미 저장 중이면 무시 → 파일 만들기 → `downloadFile` → 알림(성공 "PDF를 저장했습니다."/"이미지를 저장했습니다.", 실패 "PDF 저장에 실패했습니다. 다시 시도해 주세요."/"이미지 저장에 실패했습니다."). `DailyInspectionExportBar`의 `save` 본문을 옮긴 것.
3. `api/report.js`에 `reportBaseFileName(viewMode, year, month, clientFilter)` 하나(확장자 없는 이름). `ReportPage`·`ReportShareModal`이 같이 씀. 안 쓰이게 되는 `buildReportImageFileName`·`buildDetailReportImageFileName`과 그 테스트 묶음 2개는 지움.
4. `ReportPage.jsx`(약 165줄): 내려받기 함수 2개·저장 중 상태 2개 대신 `useFileDownload`. PDF·이미지 모두 "파일 만들기 → 링크로 내려받기". 버튼 글자("PDF 저장 중…" 등)와 `isExporting`은 `saving` 값으로. 머리 주석은 1줄로 다시 씀.
5. `ReportShareModal.jsx`는 `buildReportExportFile`·`reportBaseFileName`을 씀(동작 그대로). `DailyInspectionExportBar.jsx`는 `downloadFile`·`save` 대신 `useFileDownload`(동작 그대로).
6. 내려받는 파일 이름·용지(A4 세로)·여백·화질은 지금과 같음.

### 건드릴 파일
- `features/documents/ui/ReportPage.jsx`(줄어듦), `ReportShareModal.jsx`, `DailyInspectionExportBar.jsx`
- `features/documents/api/reportExport.js`, `features/documents/api/report.js`
- 새 `features/documents/hooks/useFileDownload.js`
- 테스트: `ReportPage.test.js`(내려받기 테스트 추가), `reportExport.test.js`(새 두 함수), `report.test.js`(지우는 함수의 묶음 2개만 지움)

### 안 건드릴 것
- html2pdf 설정 값, 공유 창 `ShareFileModal.jsx`, 화면 그리기 부품(`ReportSummaryContent` 등), 디자인 파일, 99-A 운행일지.

### 검증
- **옮기기 전** `ReportPage.test.js`에 내려받기 테스트를 붙여 옛 코드에서 통과 확인(html2pdf는 `reportExport.test.js`처럼 가짜로): ① 이미지 저장 → 확인 창 [저장] → 링크 이름 `2026년_N월_운송비내역서.png`, 알림 "이미지를 저장했습니다.", 끝나면 `pdf-export-mode` 떨어짐 ② PDF 저장 성공 알림·`pdf-export-mode` 떨어짐 ③ 그림 그리기 실패면 실패 알림·`pdf-export-mode` 떨어짐. 옮긴 뒤 같은 테스트 통과 + PDF 링크 이름 `…운송비내역서.pdf` 확인 하나 추가(옛 코드는 `.save()`라 링크가 없어서 옮긴 뒤에만).
- `reportExport.test.js`: `buildReportExportFile`이 성공·실패 모두에서 `pdf-export-mode`를 떼는지, `downloadFile`이 파일 이름으로 링크를 누르는지.
- `npm test`·타입 검사·빌드·`npm run depcheck` 통과, lint 경고 수 그대로. 지운 테스트 단언 수를 진행 기록에 적음.
- 배포본 로그인 확인(데스크톱 AI): 운송비 내역서 화면·버튼 3개·공유 창 열림, 일상점검표 탭 버튼. **실제 파일 내려받기는 데스크톱 AI가 누르지 않음**(브라우저 내려받기는 보리 허락 사항) — 아래 사용자 확인으로.

### 파일 길이
- `ReportPage.jsx` 약 165, `useFileDownload.js` 약 40, `reportExport.js` 약 95, `report.js` 약 65, `DailyInspectionExportBar.jsx` 약 75, `ReportShareModal.jsx` 약 55. 테스트 파일은 §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(내려받기 실패는 지금처럼 알림만).
- 새 테스트가 옛 코드에서 실패하면 화면을 고치지 말고 멈추고, 기대와 다른 동작을 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법
- push 뒤 **휴대폰에서 보리 확인**(분리설계 확인할 점 2 결정): 서류 발급 → 운송비 내역서에서 [PDF 다운로드]·[이미지 저장]이 받아지고 파일 이름이 `2026년_10월_운송비내역서.pdf/.png`인지, 일상점검표 탭 [PDF 다운로드]도 그대로인지.

### 확인할 점
1. **내려받기 훅을 일상점검표와 같이 쓸까요?** 승인된 설계는 운송비 내역서만 보고 `useReportDownload`로 적었는데, 일상점검표 버튼 줄에 같은 코드(링크 내려받기 + 저장 중 상태 + 같은 알림 4개)가 이미 있습니다. **권함: 이름을 `useFileDownload`로 하고 두 곳이 같이 쓰기** — 같은 코드가 세 벌(운송비 PDF·운송비 이미지·일상점검표)에서 한 벌로 줄어듭니다. 그러면 저장 중 상태가 하나가 되어, 운송비 내역서도 **하나를 저장하는 동안 두 버튼이 함께 막힙니다**(일상점검표는 지금도 이렇게 동작함, 두 번 눌러 `pdf-export-mode`가 꼬이는 것도 막힘). 다른 안: 운송비 내역서에만 `useReportDownload`(설계 그대로, 일상점검표 코드는 따로 남고 두 버튼도 따로 막힘).
