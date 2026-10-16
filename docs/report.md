# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 56-B-1 실패 문구를 공용 파일 한 곳에서 (파일마다 적은 같은 문구 모으기) `[x]`

> **진행(2026-10-16)**: 보리 "착수지시서 확정, 작업 진행해"(16개 한 슬라이스 승인). 코드 커밋 react-app `7154809`(push 전) — 코드 16개 + 테스트 2개.
> "공용 파일 밖 글자 0곳" 새 테스트 고치기 전 실패 확인 → 통과, `npm test` 1,362개 통과, typecheck 통과. ① 글자를 상수로 비교하던 기존 테스트 13개 파일 그대로 통과, ③ 옛 글자 1곳(`receivablesCloudCommit.test.js:23`) 새 문구로.
> 줄 수: 파일마다 +1(가져오기), 가장 긴 `dayLogCloudCommit.js` 197.
> AI 개발 서버 비회원: 앱 설정·알림·마이페이지 정상, 바꾼 파일 불러오기 오류 없음(콘솔의 "MyPage 불러오기 실패"는 서버를 다시 켜기 전 남은 옛 기록 — 다시 열어 정상 확인).
> 리뷰 §5: 바뀐 파일 = 지시서 목록 그대로, 새 파일 없음, 타입 꼼수 없음. push는 보리 예외 지시("예외로")로 AI, CI verify·deploy 초록(run 38889740852). 보리 최종 `[x]` 승인(2026-10-16).

로드맵 56-B(⑤). 56-A `[x]`(`bfb87f2`)에서 만든 `lib/failureMessages.js`를 실제로 쓰게 하는 단계. 56-B는 둘로 나눈다:
- **56-B-1(이번)**: 파일마다 따로 적은 같은 실패 문구 상수를 공용 파일에서 가져오게 바꿈 + "다시 로그인한 뒤" 9곳을 sot ③ 문구로
- **56-B-2(다음, 따로 지시서)**: 화면에 손으로 반복된 "저장 → 실패하면 기록 남기고 알림"(조사해 보니 화면 쪽 약 24곳)을 공용 함수 하나로

### 쉬운 요약
- 같은 "저장에 실패했습니다. 네트워크…" 문구가 15개 파일에 따로 적혀 있어서, 문구를 바꾸려면 15곳을 다 고쳐야 한다. 이걸 공용 파일 한 곳에서 가져오게 바꾼다.
- "로그인 정보가 바뀌어…" 문구 중 아직 "다시 로그인한 뒤"로 남은 9곳을 56-A에서 정한 ③("화면을 새로고침한 뒤")으로 맞춘다.
- 보이는 문구는 ③ 9곳만 바뀌고, 나머지는 글자가 같다. 저장·동작은 하나도 안 바뀐다.

### 현재 상태 (`grep` 결과)
- ① `'저장에 실패했습니다. 네트워크 상태를 확인해 주세요.'`를 상수로 따로 적은 곳 14개 파일: `DailyInspectionNotice.jsx:15` · `NotificationsPage.jsx:13` · `usePersonalInfoDraft.js:11` · `clientCloudSave.js:11` · `dayLogCloudCommit.js:24` · `directMutationActions.js:32` · `driverUnlink.js:12` · `memberRestoreInspections.js:18` · `memberRestoreRecords.js:125` · `requestDriverInviteSave.js:20` · `supportInquiryMutations.js:13` · `taxInvoiceActions.js:10` · `vehicleDeletion.js:22` · `vehicleMutations.js:19`, 글자를 직접 쓴 곳 1개: `AppSettingsPage.jsx:48`.
- ③ 옛 문구 `'로그인 정보가 바뀌어 저장하지 못했습니다. 다시 로그인한 뒤 시도해 주세요.'` 9개 파일: `clientCloudSave.js:12` · `dayLogCloudCommit.js:25` · `directMutationActions.js:33` · `driverUnlink.js:13` · `ownerFinance.js:35` · `requestDriverInviteSave.js:21` · `supportInquiryMutations.js:14` · `vehicleDeletion.js:23` · `vehicleMutations.js:20`.
- 이 문구를 보는 테스트: ① 13개 파일(대부분 각 파일의 상수를 가져와 비교 — 글자가 같아 그대로 통과 예상), ③ 옛 글자를 직접 보는 곳 1개(`receivablesCloudCommit.test.js`).
- 바꿀 파일 전부 200줄 이하(가장 긴 `dayLogCloudCommit.js` 196줄).

### 목표 상태 (기대 동작)
1. 위 15개 파일이 ① 문구를 `lib/failureMessages.js`의 `SERVER_SAVE_FAIL`에서 가져온다(파일 안 상수 이름은 그대로 두고 값만 공용 상수로 — 다른 곳이 그 이름을 가져다 쓰기 때문).
2. 위 9개 파일의 로그인 바뀜 문구가 `SESSION_CHANGED_FAIL`(③ "화면을 새로고침한 뒤 다시 시도해 주세요.").
3. 화면에 보이는 문구 변화는 ③ 9곳뿐, ① 글자는 그대로.
4. 저장·재시도·알림 띄우는 때는 그대로.
5. 이 일이 끝나면 `failureMessages.js` 밖에서 ①·③ 문구를 글자로 적은 곳이 0곳(`grep`으로 확인하는 테스트).

### 건드릴 파일
- 위 ① 15개 + ③ 9개 — 겹치는 파일이 많아 **실제 파일은 16개**: `components/` 4개(`AppSettingsPage.jsx`·`day-log/DailyInspectionNotice.jsx`·`notifications/NotificationsPage.jsx`·`usePersonalInfoDraft.js`), `lib/` 12개(`clientCloudSave`·`dayLogCloudCommit`·`directMutationActions`·`driverUnlink`·`memberRestoreInspections`·`memberRestoreRecords`·`ownerFinance`·`requestDriverInviteSave`·`supportInquiryMutations`·`taxInvoiceActions`·`vehicleDeletion`·`vehicleMutations`). 파일마다 가져오기 1줄 + 상수 값 1~2줄.
- 테스트: `failureMessages.test.js`에 "공용 파일 밖 글자 0곳" 검사 1개, `receivablesCloudCommit.test.js` ③ 기대 문구 1줄을 새 문구로(의도한 변경).
- 공용 영향: 파일 안 상수 이름을 그대로 두므로 그 이름을 가져다 쓰는 곳(`CLIENT_SAVE_FAIL_TOAST` 등)은 그대로.
- **슬라이스 크기**: 파일 16개라 AGENTS §3 "1~3개 파일"보다 많지만, 파일마다 같은 한두 줄 바꾸기라 나누면 "무의미한 기계적 분할"(§6)이 됨 → 한 번에 하는 예외 요청(아래 질문).

### 안 건드릴 것
- 화면의 "저장 → 실패 알림" 반복 코드(56-B-2), 그 밖의 실패 문구(PDF·공유·불러오기 등).
- 각 파일의 저장 흐름·상수 이름·`errorReport`.

### 검증
- 로컬 `npm test` — "공용 파일 밖 글자 0곳" 새 테스트는 고치기 전 실패 확인, 나머지는 그대로 통과해야 함(①은 글자 같음).
- 브라우저: 실패를 일부러 내기 어려워 테스트로 확인. AI가 개발 서버 비회원으로 앱이 그대로 뜨는지만 확인.

### 파일 길이
- 모든 파일 +1줄 안팎(가져오기). 가장 긴 `dayLogCloudCommit.js` 196 → 약 197, 전부 200 이하.
- 테스트 파일은 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(문구 가져오는 곳만). 실패하면 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법
- 화면 확인 없음(실패할 때만 보이는 문구). 로그인 정보가 바뀌었을 때 안내가 "화면을 새로고침한 뒤…"로 나오는 것만 바뀜.

### 보리 확인 필요
- 파일 16개를 한 번에 바꾸는 것(파일마다 한두 줄)을 이번 슬라이스 하나로 해도 될지(추천 — 나누면 같은 일을 여러 번 커밋).
