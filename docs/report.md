# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 58-C 거의 같은 공유 창 두 벌(운송비 내역서·일상점검표)을 하나로 `[x]`

> **진행(2026-10-17)**: 보리 "확정, 작업 진행해"(확인할 점 1 — 공유 못 하는 휴대폰 안내는 각자 그대로). 코드 커밋 react-app `7afe525` — push 뒤 CI verify·deploy 초록(run 39030614242, 커밋된 것만 검사 — 다른 작업 창 수정 섞이지 않음).
> - 바뀐 파일(지시서 목록과 같음): 새 `shared/ShareFileModal.jsx` 113줄(`buildShareSmsUrl` 포함), `ReportShareModal.jsx` 138→65(`buildReportSmsUrl`은 같은 이름으로 내보냄), `documents/DailyInspectionShareModal.jsx` 90→29, 새 `documents/DailyInspectionShareModal.test.js` 5개.
> - 점검표 창 동작 기록 테스트 5개(모양·문구, 카카오톡 공유 값, 공유 못 하는 휴대폰 안내, 실패/취소, 문자용 저장)를 고치기 전 코드에서 먼저 통과시킨 뒤 합침 → 그대로 통과. 내역서 창 기존 테스트 5개 통과. 고치기 전으로 돌려도 모두 통과(동작 안 바꾸는 정리라 맞음, revert-check).
> - `npm test` 951 + 485 통과, 타입 검사 통과.
> - 개발 서버 비회원 360px 어두운 화면: 서류 발급 → 운송비 내역서 → 공유 → "내역서 보내기" 창 문구·단추 그대로. 콘솔 오류 없음. 사진 `scratchpad\v58c\`. 점검표 창은 비회원에서 일상점검표 탭이 안 보여 테스트로. 실제 카카오톡·문자 보내기는 배포 뒤 휴대폰에서 보리.
> - 지시서와 다른 점: 없음.
> - **참고**: 작업 중(20:35~20:46) react-app 작업 폴더에 AI가 손대지 않은 수정이 생김 — `calendar/CalendarHeader.jsx`·`CalendarPage.jsx`·`calendar-grid.css`, `home/HomeTodoCard.jsx`·`HomePage.test.js`, `receivables/ReceivablesListPage.jsx`, 새 `receivables/receivablesGroupHead.css`. 58-C 커밋에는 넣지 않았고 건드리지 않음. 위 테스트·화면 확인은 그 수정이 섞인 상태에서 돌았으므로, 58-C만의 확인은 push 뒤 CI로 봄. 보리 확인: 다른 작업 창 작업, 그대로 둠(2026-10-17).

보리 "진행해"(2026-10-17, 58-B `[x]` 뒤). 근거: `docs/roadmap.md` 58번(⑥), `docs/code-cleanup-review.md` D("공유 창 처리 5묶음 겹침"). 58-A `[x]`(`b3de723`) → 58-B `[x]`(`2f231b1`) → **58-C(이번, 58번 마지막)**.

### 쉬운 요약
- 운송비 내역서 "내역서 보내기" 창과 일상점검표 "점검표 보내기" 창은 모양과 처리(카카오톡으로 파일 공유, 문자용 파일 저장 뒤 문자 앱 열기)가 거의 똑같이 두 벌입니다. 공용 창 하나로 모읍니다.
- 창 모양·문구·누르는 동작은 그대로입니다. 두 창이 다른 부분(제목, 안내 문구, 문자 받는 사람)은 각자 넘겨줍니다.

### 현재 상태 (코드 확인)
- `components/ReportShareModal.jsx`(138줄) — 운송비 내역서(`ReportPage.jsx:222`). `components/documents/DailyInspectionShareModal.jsx`(90줄) — 일상점검표(`DailyInspectionExportBar.jsx:86`, 이미 `buildReportSmsUrl`을 내역서 창에서 빌려 씀).
- 똑같은 것:
  1. 카카오톡: 창 닫기 → "카카오톡으로 보낼 PDF/이미지를 준비하고 있습니다." → 파일 만들기 → 공유 못 하는 휴대폰이면 안내 → `navigator.share`(파일·제목·문구) → 사용자가 취소한 것 말고 실패면 "카카오톡 파일 공유에 실패했습니다."
  2. 문자: 창 닫기 → "문자로 보낼 PDF/이미지를 저장하고 있습니다." → 파일 만들어 내려받기 → 문자 앱 열기 → 실패면 "문자용 파일 저장에 실패했습니다." → 1초 뒤 임시 주소 정리.
  3. 창 모양: 머리(제목·"보내는 방법이나 파일 형식을 선택해 주세요.") + 카카오톡·문자 두 칸 × PDF·이미지 단추.
- 다른 것:
  - 제목("내역서 보내기"/"점검표 보내기"), 칸 설명 문구, 공유 제목("운송비 내역서"/"일상점검표"), 보낼 문구(설정한 내역서 문구/"안녕하세요. N월 일상점검표입니다…").
  - 공유 못 하는 휴대폰 안내: 내역서 "이 휴대폰에서는 파일 공유를 할 수 없습니다. [PDF 다운로드]로 저장한 뒤 보내 주세요." / 점검표 "이 기기에서는 파일 공유를 지원하지 않습니다."
  - 문자 받는 사람: 내역서는 거래처 연락처(없으면 안내만 하고 창 안 닫음), 점검표는 비워 둠(문자 앱에서 직접 고름).
  - 파일 만들기: 내역서는 창 안에서(`pdf-export-mode`), 점검표는 밖에서 받아 옴.
- 테스트: 내역서 창 5개(`ReportShareModal.test.js`), 점검표 창 없음.

### 목표 상태 (기대 동작)
1. 위 같은 것 1~3이 공용 창 한 곳에만 있다.
2. 두 창의 제목·안내 문구·단추·안내 알림 문구·공유 제목·보낼 문구·문자 받는 사람이 지금과 같다(공유 못 하는 휴대폰 안내도 각자 지금 문구 그대로 — 확인할 점 1).
3. 내역서 창: 거래처 연락처가 없으면 문자 단추는 안내만 하고 창을 안 닫는다 — 지금과 같다.
4. `buildReportSmsUrl`(아이폰은 `&`, 그 밖은 `?`) 그대로 — 기존 테스트가 쓰는 이름도 그대로.

### 건드릴 파일
- 새 `react-app/src/components/shared/ShareFileModal.jsx` — 공용 창(모양 + 카카오톡·문자 처리 + `buildShareSmsUrl`).
- `react-app/src/components/ReportShareModal.jsx` — 파일 이름·회사명·연락처·파일 만들기만 남기고 공용 창에 넘김. `buildReportSmsUrl`은 공용 것을 같은 이름으로 내보냄.
- `react-app/src/components/documents/DailyInspectionShareModal.jsx` — 문구만 남기고 공용 창에 넘김.
- 새 테스트 `react-app/src/components/documents/DailyInspectionShareModal.test.js`.
- 공용 영향: CSS(`report/report.css`의 `report-share-*`) 이름·순서 그대로, 안 바꿈. 두 창을 부르는 `ReportPage.jsx`·`DailyInspectionExportBar.jsx`는 안 바꿈(같은 이름·같은 값).

### 안 건드릴 것
- 파일 만들기(`lib/reportExport.js`, 점검표 쪽 `buildFile`), 문구 설정(`lib/messageTemplates.js`).
- `ReportPage.jsx`, `DailyInspectionExportBar.jsx`, CSS.

### 검증
- **먼저** 점검표 창 새 테스트를 지금 코드에 돌려 통과(동작 기록): 제목·두 칸·단추 4개, 카카오톡 → 공유 제목 "일상점검표"·문구 "안녕하세요. 10월 일상점검표입니다…", 공유 못 하는 휴대폰 → "이 기기에서는…", 문자 → 받는 사람 비운 문자 주소·문구. 합친 뒤 그대로 통과.
- 내역서 창 기존 테스트 5개 그대로 통과(목표 2~4).
- `npm test`·`npm run typecheck` 통과.
- 개발 서버 비회원 360px: 서류 발급 → 운송비 내역서·일상점검표에서 보내기 창을 열어 모양이 전과 같은지(사진). 실제 카카오톡·문자 보내기는 휴대폰에서만 돼서 배포 뒤 보리.

### 파일 길이
- `ReportShareModal.jsx` 138 → 약 55
- `DailyInspectionShareModal.jsx` 90 → 약 30
- 새 `shared/ShareFileModal.jsx` 약 120
- 새 테스트 약 100(테스트 파일 예외)

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(지금 처리를 옮기기만).
- 합친 뒤 동작 기록 테스트가 깨지면 고치지 않고 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, 휴대폰)
1. 서류 발급 → 운송비 내역서 → 보내기 → 창 모양 그대로, 카카오톡 PDF 보내기가 전처럼 된다.
2. 서류 발급 → 일상점검표 → 보내기 → 창 모양 그대로, 카카오톡·문자 보내기가 전처럼 된다.

### 확인할 점
1. 공유를 못 하는 휴대폰 안내가 두 창에서 다릅니다(내역서 "이 휴대폰에서는 파일 공유를 할 수 없습니다. [PDF 다운로드]로 저장한 뒤 보내 주세요." / 점검표 "이 기기에서는 파일 공유를 지원하지 않습니다."). 이번엔 **각자 그대로** 둡니다(정리 작업이라 글자를 안 바꿈). 하나로 맞추길 원하시면 말씀해 주세요.
