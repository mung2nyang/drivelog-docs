# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 57-B 입력 칸 창 7개·문자 양식 시트도 공용 창 껍데기로 `[x]`

> **진행(2026-10-16)**: 보리 "응, 착수지시서 확정, 작업 진행해"(입력 창 바깥 누름·Esc 모두 안 닫힘, 9개 파일 한 번에). 코드 커밋 react-app `294a63d`(push 전).
> 새 테스트 9개(입력 창 6개 창 표시·Esc 안 닫힘 반복, 고정 노선 창, 바깥막 이름, 문자 시트) 고치기 전 실패 확인 → `npm test` 1,375개 통과, typecheck 통과. 기존 "바깥 클릭 안 닫힘" 테스트 그대로 통과.
> 만들다 알게 된 것: 고정 노선 창은 화면 맨 바깥(`document.body`)에 그려지고 해제 확인 창이 바깥막 안에 있어서, 확인 창을 껍데기 바로 옆에 나란히 둠(겹친 창 맨 위만 Esc 규칙 그대로). 지출 창 inline 모드는 상자만.
> 줄 수: `ModalShell.jsx` 63, `ExpenseFormModal.jsx` 180→182, 나머지 같거나 1~2줄 변동, 전부 200 이하.
> AI 개발 서버 비회원 375px: 거래처 창 role=dialog·이름·키보드 위치 창 안, 바깥 누름·Esc 안 닫힘, 취소로 닫히고 "+ 추가"로 키보드 위치 돌아감. 하루 화면 주유 입력(inline) 모양 그대로·창 표시 없음. 문자 시트·나머지 창은 테스트로.
> 리뷰 §5: 건드릴 파일 = 지시서 목록 그대로, 새 파일 없음, 타입 꼼수 없음. push는 보리 예외 지시("예외로")로 AI, CI verify·deploy 초록(run 38919274957). 보리 최종 `[x]` 승인(2026-10-17).

로드맵 57번(⑤). 57-A `[x]`(`2f03f9a`, 공용 `ModalShell` + 바깥 누르면 닫히는 창 6개 파일) 다음.
보리 결정(2026-10-16): **입력 칸이 있는 창은 바깥을 눌러도 안 닫히게 그대로, 컴퓨터 Esc 키로도 안 닫히게**(실수로 입력이 날아가지 않게).

### 쉬운 요약
- 차량·거래처·기사·지출·고정 노선 거래처·초대 코드·세금계산서 작성 창 7개와, 운행 카드의 문자 양식 고르기 시트를 57-A의 공용 창 껍데기로 바꾼다.
- 입력 창은 지금처럼 **취소·저장 버튼으로만** 닫힌다(바깥 누름·Esc 모두 안 닫힘). 문자 양식 시트(입력 칸 없음)는 지금처럼 바깥 누름으로 닫히고 Esc로도 닫힌다.
- 휴대폰에서 보이는 모양·동작은 그대로. 바뀌는 건 화면 읽기 기능이 "창"으로 알아듣는 것과, 닫힐 때 원래 누른 자리로 키보드 위치가 돌아가는 것.

### 현재 상태 (코드 확인)
- 입력 칸 창 7개는 바깥막(`modal-overlay`)에 누름 처리가 없어 안 닫힘(로드맵 8번 결정, `modalOutsideClick.test.js:68`): `cars/CarFormModal.jsx:77` · `clients/ClientFormModal.jsx:25` · `DriverFormModal.jsx:49` · `ExpenseFormModal.jsx:176`(안쪽 상자는 `:40` — 하루 화면 안에 그대로 넣는 `inline` 모드도 같은 상자를 씀) · `FixedRouteClientModal.jsx:71` · `InviteRedeemModal.jsx:44` · `TaxInvoiceDraftModal.jsx:21`. 화면 읽기 "창" 표시 없음, Esc 처리 없음.
- 문자 양식 시트(`day-log/MessageTemplateSheet.jsx:48-49`)는 자기만의 바깥막(`message-template-overlay`, 아래에서 올라오는 모양)과 `<section role="dialog" aria-modal>`, 바깥 누르면 닫힘, Esc 없음.
- `ModalShell`(57-A)은 바깥막 CSS 이름이 `modal-overlay`로 고정.

### 목표 상태 (기대 동작)
1. 입력 창 7개가 `ModalShell`을 `closeOnOverlay={false}`·`closeOnEscape={false}`로 쓴다 — 바깥 누름·Esc 모두 안 닫힘, 취소·저장 버튼 그대로.
2. 지출 창의 `inline` 모드(하루 화면 안)는 지금처럼 껍데기 없이 상자만.
3. 문자 양식 시트는 `ModalShell`을 바깥막 이름 `message-template-overlay`로 쓴다 — 바깥 누름·Esc로 닫힘, 모양 그대로.
4. 8개 모두 화면 읽기 "창"(aria-modal)·창 이름, 열 때 키보드 위치는 창 자체(57-A와 같음 — 첫 입력칸에 두면 휴대폰 자판이 저절로 뜸), 닫힐 때 원래 누른 자리로.
5. 휴대폰 모양·누르는 동작 그대로.

### 건드릴 파일
- `react-app/src/components/shared/ModalShell.jsx` — 바깥막 CSS 이름을 받는 값 하나 추가(기본 `modal-overlay` 그대로)
- 입력 창 7개 파일 + `day-log/MessageTemplateSheet.jsx` = 8개
- 테스트: `ModalShell.test.js`에 "바깥막 이름 바꿈" 1개, `modalOutsideClick.test.js`에 "입력 창은 Esc로도 안 닫힘" 1개(7개 창 반복), 문자 양식 시트 바깥 누름·Esc 1개. 기존 테스트 그대로 통과해야 함.
- 공용 영향: CSS 이름 그대로 써서 스타일 안 바꿈. `ModalShell` 새 값은 기본값이 지금과 같아 57-A 6개 파일 영향 없음.
- **슬라이스 크기**: 파일 9개 + 테스트 — 파일마다 껍데기 두 줄을 바꾸는 같은 일이라 한 번에(56-B-1·57-A와 같은 방식). 나누길 원하시면 알려 주세요.

### 안 건드릴 것
- 창 안 내용·버튼·문구·CSS 파일.
- **같은 CSS 이름이 두 파일에 따로 정의된 곳 10곳**(조사해 보니 9곳이 아니라 10곳: `.modal-overlay`·`.support-card`·`.support-panel`·`.support-detail`·`.faq-item`·`.driver-section-heading`·`.inline-icon`·`.maint-fuel-page`·`.mypage-shortcut-icon`·`.work-log-page`) — 합치면 화면 모양이 바뀔 수 있는 다른 성격의 일이라 이번에 안 함. 57-B가 끝난 뒤 따로 할지 정해 주세요.

### 검증
- 로컬 `npm test` — 새 테스트는 고치기 전 실패 확인.
- 브라우저: AI가 개발 서버 비회원으로 거래처·차량·지출 창(바깥 누름·Esc 안 닫힘, 취소로 닫힘, 닫힌 뒤 키보드 위치)과 문자 양식 시트(바깥 누름·Esc 닫힘) 확인, 시험 기록은 지움.

### 파일 길이
- `ModalShell.jsx` 62 → 약 64 / 입력 창 7개는 지금과 같거나 1~2줄 줄어듦(가장 긴 `ExpenseFormModal.jsx` 180 → 약 182) / `MessageTemplateSheet.jsx` 69 → 약 69 — 전부 200 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(창 껍데기만). 실패하면 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법 (보리 휴대폰)
1. 거래처 추가·차량 추가·주유 추가 창을 열고 바깥(회색)을 누름 → **안 닫힘**, 취소 → 닫힘. 모양 그대로.
2. 운행 카드의 문자 버튼 → 아래에서 올라오는 양식 고르기 → 바깥 누름 → 닫힘.

### 보리 확인 필요
- 위 결정(입력 창은 바깥 누름·Esc 모두 안 닫힘)이 맞는지.
- 파일 9개를 한 번에 해도 되는지.
