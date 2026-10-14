# docs/report.md — 현재 슬라이스 착수지시서

## 바로 수정 — 확인 창 통일 + 말투 맞추기 `[x]`

보리 지시(2026-10-14): Hallmark 점검(오늘 작업분)에서 나온 "확인 창이 두 종류로 섞임"(큰 문제)·"말투 섞임"(중간)을 **바로 고쳐** — 예전 "바로 수정" 방식(착수지시서 승인 단계 없이 진행, 이 기록으로 대신).

### 고칠 것
1. **확인 창 통일:** 오늘 만든 브라우저 기본 확인 창 2곳을 앱 확인 창(`ConfirmModal`)으로 바꾼다.
   - 새 `components/shared/confirmDialog.jsx` — 어디서든 `await confirmDialog({ title, message, confirmLabel, cancelLabel, danger })`로 묻고, `AppShell`에 한 번 붙인 `<ConfirmDialogHost/>`가 앱 확인 창으로 그린다. 확인 창을 그릴 곳이 없으면(테스트 등) 기본 창으로 대신 묻는다.
   - `ConfirmModal`에 취소 버튼 이름(`cancelLabel`, 기본 "취소") 칸 추가.
   - 36번 입력 중 나가기(`inlinePanelActions.js`) → 제목 "저장하지 않은 입력이 있습니다" / 본문 "지금 나가면 입력한 내용이 사라집니다." / [계속 입력] [나가기].
   - 43-C 큰 운송료(`CallDetailForm.jsx` 저장 버튼 → 판단은 `callDetailFormHelpers.js`로, 화면 파일 200줄 유지) → 제목 "운송료가 1,000만원을 넘습니다" / 본문 "입력한 운송료: N원. 이 금액이 맞으면 저장해 주세요." / [금액 고치기] [이 금액으로 저장].
2. **말투 맞추기(앱 전체가 쓰는 "~습니다/~주세요"로):** 위 두 곳 + 44번 확인 창. 44번은 긴 문장 제목을 짧은 제목 + 본문으로 나눔 → 제목 "{거래처} {M}월분 입금 완료 처리" / 본문 "미수 N건, X원을 모두 입금 완료로 바꿉니다. 되돌리기는 한 건씩만 할 수 있습니다." / [취소] [입금 완료]. (`useConfirm`이 본문을 받도록 칸 추가.)

### 안 고치는 것
- 35번 저장 실패 확인(`durableWriteGuard.js`) — 화면 이동 여러 곳이 바로 답을 받아야 하는 구조라 바꾸면 범위가 커짐. 기본 확인 창 그대로(따로).
- 점검의 작은 문제들(입금완료 띄어쓰기, 41번 긴 줄, 산재 이름, 45번 빨간색) — 따로.

### 결과(2026-10-14)
- 줄 수: `confirmDialog.jsx` 새 46, `ConfirmModal.jsx` 47, `AppShell.jsx` 170, `inlinePanelActions.js` 52, `DayLogPage.jsx` 233(기존 줄만), `CallDetailForm.jsx` 200, `callDetailFormHelpers.js` 98, `useConfirm.jsx` 37, `useReceivablesActions.js` 145.
- 테스트: 새 `confirmDialog.test.js` 3개, 오늘 만든 확인 창 테스트 3곳 기대 문구 갱신(`inlinePanelActions.test.js`는 `.jsx` 로더 추가). `npm test` 873+387 통과, `typecheck` 통과.
- 개발 서버(기사 계정 로그인 상태, 보기·취소만): 세부 입력 창 연 채 ← → 앱 확인 창 "저장하지 않은 입력이 있습니다 / 지금 나가면… [계속 입력][나가기(빨강)]" → 계속 입력 → 일지에 머묾. 운송료 20,000,000 → [저장] → "운송료가 1,000만원을 넘습니다 / 입력한 운송료: 20,000,000원… [금액 고치기][이 금액으로 저장]" → 금액 고치기 → 값 그대로 → 입력 창 취소(저장 없음).
- 코드 커밋 react-app `00365c4`. 보리 push → CI 초록(verify·deploy) → §5 리뷰 → 보리 최종 승인(2026-10-14).

### 파일·줄 수(예상)
새 `confirmDialog.jsx` 약 55, `ConfirmModal.jsx` +2, `AppShell.jsx` 168 → 약 170, `inlinePanelActions.js` 약 +5, `DayLogPage.jsx` 233 그대로(기존 줄만 고침), `CallDetailForm.jsx` 200 그대로, `callDetailFormHelpers.js` 약 +12, `useConfirm.jsx` +2, `useReceivablesActions.js` 문구. 테스트: 오늘 만든 확인 창 테스트 3곳 기대 문구 갱신 + 새 확인 창 테스트.
