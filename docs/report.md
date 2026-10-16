# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 57-A 창 껍데기 공용 부품 — 바깥을 누르면 닫히는 창부터 `[x]`

> **진행(2026-10-16)**: 보리 "착수지시서 확정, 작업 진행해". 코드 커밋 react-app `2f03f9a`(push 전).
> 새 `ModalShell.test.js` 4개 부품 없을 때 실패 확인 → 통과, `npm test` 1,366개 통과(기존 `modalOutsideClick`·`ReportShareModal`·`confirmDialog`·`App.clientsCars` 그대로), typecheck 통과.
> **지시서와 다른 점 1개**: 열릴 때 키보드 위치를 "창 안 첫 칸" 대신 **창 자체**(확인 창은 지시서대로 "취소")로 — 첫 칸이 입력칸이면 휴대폰 자판이 저절로 올라와서. 창에 테두리는 안 생김(outline 없음 측정).
> 줄 수: `ModalShell.jsx` 62 / `ConfirmModal.jsx` 41→30 / `DataDownloadModal.jsx` 177→170 / `DailyInspectionModal.jsx` 136→135 / `DailyInspectionShareModal.jsx` 91→90 / `ReportDetailView.jsx` 178→177 / `ReportShareModal.jsx` 139→138.
> AI 개발 서버 비회원 375px: 세부 내역 고르기 창 role=dialog·aria-modal·이름·키보드 위치 창 안 → Esc로 닫힘 → 원래 누른 탭으로 돌아감. 내역서 공유 창 같은 확인 + 바깥 누름 닫힘. 확인 창·점검표·데이터 창은 테스트로.
> 리뷰 §5: 건드릴 파일 = 지시서 목록 그대로 + 새 부품·테스트, 새 저장 없음(열린 창 순서는 메모리 목록), 타입 꼼수 없음. push는 보리 예외 지시("예외로")로 AI, CI verify·deploy 초록(run 38903766471). 보리 최종 `[x]` 승인(2026-10-16).

로드맵 57번(⑤, 코드 구조 점검). 56번 끝(56-B-2는 보리 결정으로 안 함) 다음. 57번은 둘로 나눈다:
- **57-A(이번)**: 공용 창 껍데기 `ModalShell` + **바깥을 누르면 닫히는 창**(입력 칸 없음) 6개 파일
- **57-B(다음, 따로 지시서)**: 입력 칸이 있는 창 7개(로드맵 8번 결정대로 바깥 눌러도 안 닫힘 — Esc로 닫을지는 그때 보리 결정) + 아래 문자 양식 시트 + 같은 CSS 이름 두 파일 중복 9곳

### 쉬운 요약
- 앱의 창(팝업) 겉모양 코드가 파일마다 복사돼 있어, 확인 창(`ConfirmModal`)만 컴퓨터 Esc 키로 닫히고 키보드 위치도 창 안으로 옮겨 준다.
- 공용 껍데기 하나를 만들어, 바깥을 누르면 닫히는 창 6개가 다 같은 방식으로 닫히고(바깥 누름·Esc), 화면 읽기 기능이 "창"으로 알아듣게 한다.
- 겉모양은 그대로다(같은 CSS 이름을 그대로 씀) — 그래서 그림 시안 대신 동작 시안.

### 현재 상태 (코드 확인)
- 창 껍데기(`modal-overlay` + 안쪽 상자)를 직접 그리는 곳 14개 파일 15곳(`grep modal-overlay`).
- Esc로 닫히고 열릴 때 키보드 위치를 창 안으로·닫힐 때 원래 자리로 돌리는 건 `ConfirmModal.jsx:20-33`뿐.
- 바깥 누르면 닫히는 창(입력 칸 없음): `ConfirmModal.jsx:36`, `DataDownloadModal.jsx:111·139·156`(3곳), `DailyInspectionModal.jsx:68`(고치는 중이면 안 닫힘), `DailyInspectionShareModal.jsx:60`, `ReportDetailView.jsx:41`(거래처 고르기 창), `ReportShareModal.jsx:108`.
- 화면 읽기용 "창" 표시: `ConfirmModal`(alertdialog)·`DailyInspectionModal`(dialog, aria-modal 없음)만, 나머지 4개 파일은 없음.
- 입력 칸이 있는 창 7개(`CarFormModal`·`ClientFormModal`·`DriverFormModal`·`ExpenseFormModal`·`FixedRouteClientModal`·`InviteRedeemModal`·`TaxInvoiceDraftModal`)는 로드맵 8번 결정으로 바깥 눌러도 안 닫힘(`modalOutsideClick.test.js:68`) — 57-B.

### 시안 (동작 — 겉모양은 그대로)
| | 지금 | 바꾼 뒤 |
|---|---|---|
| 바깥(회색) 누름 | 6개 다 닫힘(점검표는 고치는 중이면 안 닫힘) | 같음 |
| 컴퓨터 Esc 키 | 확인 창만 닫힘 | 6개 다 닫힘(점검표 고치는 중이면 안 닫힘) — **창이 겹쳐 있으면 맨 위 창만** |
| 열릴 때 키보드 위치 | 확인 창만 "취소"로 | 확인 창은 "취소" 그대로, 나머지는 창 안 첫 칸 |
| 닫힐 때 | 확인 창만 원래 누른 자리로 | 6개 다 원래 누른 자리로 |
| 화면 읽기 | 일부만 "창" | 6개 다 "창"(aria-modal) |
| 휴대폰 화면 모양 | — | 하나도 안 바뀜 |

### 목표 상태 (기대 동작)
1. 새 `components/shared/ModalShell.jsx`: 바깥 회색 막(`modal-overlay`) + 안쪽 상자(넘겨받은 CSS 이름 그대로), `role`·`aria-modal`·이름, 바깥 누름 닫기 켬/끔, Esc 닫기 켬/끔, 열릴 때 키보드 위치·닫힐 때 되돌리기, 창이 겹치면 맨 위 창만 Esc에 반응.
2. 위 6개 파일 8곳이 `ModalShell`을 쓴다. 안쪽 내용·CSS 이름·버튼은 그대로.
3. 확인 창은 지금처럼 "취소"에 키보드 위치, Esc로 취소.
4. 점검표 창(`DailyInspectionModal`)은 고치는 중이면 바깥 누름·Esc 둘 다 안 닫힘(지금 바깥 누름 규칙과 같게).
5. 휴대폰에서 보이는 모양·누르는 동작은 하나도 안 바뀐다.

### 건드릴 파일
- 새 `react-app/src/components/shared/ModalShell.jsx` (약 60줄 — 겹친 창 순서는 메모리 목록 하나, 저장 안 함)
- `react-app/src/components/ConfirmModal.jsx` · `DataDownloadModal.jsx` · `day-log/DailyInspectionModal.jsx` · `documents/DailyInspectionShareModal.jsx` · `ReportDetailView.jsx` · `ReportShareModal.jsx`
- 테스트: 새 `components/shared/ModalShell.test.js`(바깥 누름·Esc·끔 옵션·겹친 창 맨 위만·키보드 위치 되돌리기), 기존 `modalOutsideClick.test.js`·`ReportShareModal.test.js`·`confirmDialog.test.js`·`App.clientsCars.test.js`는 그대로 통과해야 함
- 공용 영향: CSS 이름 `.modal-overlay`·`.modal-content` 등은 그대로 쓰고 스타일 안 바꿈. `ConfirmModal`을 쓰는 곳(`shared/confirmDialog.jsx` 등)은 넘기는 값 그대로.

### 안 건드릴 것
- 입력 칸 창 7개, 문자 양식 시트(`MessageTemplateSheet.jsx`), CSS 중복 정리 — 57-B.
- 창 안 내용·문구·CSS 파일.

### 검증
- 로컬 `npm test` — 새 테스트는 고치기 전 실패 확인(부품이 없으니 실패).
- 브라우저: AI가 개발 서버 비회원으로 6개 창을 열어 바깥 누름·Esc·닫힌 뒤 키보드 위치 확인, 휴대폰 크기에서 모양 그대로(사진 비교).

### 파일 길이
- `ModalShell.jsx` 새 약 60 / `ConfirmModal.jsx` 41 → 약 25 / `DataDownloadModal.jsx` 177 → 약 175 / `DailyInspectionModal.jsx` 136 → 약 136 / `DailyInspectionShareModal.jsx` 91 → 약 90 / `ReportDetailView.jsx` 178 → 약 178 / `ReportShareModal.jsx` 139 → 약 138 — 전부 200 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(겹친 창 순서는 메모리 목록, 앱을 닫으면 사라짐). 실패하면 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법 (보리 휴대폰)
1. 마이페이지 → 데이터 다운로드, 서류 발급 → 내역서 공유·세부 내역 고르기, 운행일지 → 일상점검표 창을 열어 모양이 그대로인지, 바깥을 누르면 닫히는지.
2. (컴퓨터에서) 같은 창을 열고 Esc 키 → 닫힘.
