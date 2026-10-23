# docs/report.md — 현재 슬라이스 착수지시서

110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 110-B 일지 저장 실패를 알림 목록에 보이기 `[x]`

로드맵 110 두 번째(마지막) 조각. 110-A `[x]`(react-app `d3ecf58`, drivelog-docs `b57e0b1`, sot §4-18)가 남긴 실패 날짜 목록으로 알림을 만든다. 정한 것 `[확인: 2026-10-23 보리]`: ② 문구 제목 "M월 D일 운행일지 저장이 실패했습니다.", 내용 "일지를 확인한 후 저장이 안 된 내용이 있으면 다시 입력해 주세요." `[확인: 2026-10-23 보리 — "에→이로 바꾸고 나 문구를 이걸로"]`, 누르면 그 날짜 일지 ③ [닫기] 없음 — 다시 저장되거나 그 날짜 일지를 열면 저절로 사라짐 ④ 알림 받기 스위치로 못 끔.

### 쉬운 요약
- 로그인 계정의 일지가 서버 저장에 실패하면, 알림 목록(종)에 "10월 22일 운행일지 저장이 실패했습니다."가 맨 위에 남는다. 앱을 껐다 켜도 남는다.
- 누르면 그 날짜 일지가 열리고, 열면 알림이 사라진다(110-A 규칙). 다시 실패하면 다시 생긴다.
- [닫기] 단추가 없고, 알림 받기 설정에도 이 종류는 없다(끌 수 없음).

### 현재 상태 (코드 확인)
- 실패 날짜 목록: `data/sync/failedDayLogDates.ts`(65줄) `listFailedDayLogDates(ownerKey)` → `{ logId, dateKey }[]`, `subscribeFailedDayLogDates(listener)`. 읽을 때마다 새 배열을 만들어 화면 구독용 "지금 값" 함수는 없음.
- 알림 만들기: `features/notifications/api/notifications.js`(156줄) `collectNotifications` — 항목 `{ id, kind, page, title, body, dateLabel, actionLabel?, dismissLabel? }`, 닫은 id 제외, 마지막에 `notifOff`에 든 종류 제외(154~155줄).
- 알림 다시 세기: `features/notifications/hooks/useNotifications.js`(38줄) — Store 값·화면 주소가 바뀔 때만. 실패 목록은 Store가 아니라 바뀌어도 안 다시 셈.
- 알림 화면: `features/notifications/ui/NotificationsPage.jsx`(105줄) — 알림 받기 스위치 목록 `kinds`(33~40줄), 항목을 누르면 `onOpenPage(item.page)`(89줄), 모든 항목에 [닫기](98줄).
- 화면 이동: `app/shell/AppShell.jsx:50-53` `pagePath(page)` = `/app/` + (표에 없으면 page 그대로) → `page`를 `day/2026-10-22`로 주면 `/app/day/2026-10-22`, 기사 차량 일지는 `logs/<logId>/day/<날짜>`(`AppShellRoutes.jsx:70`). AppShell은 안 고쳐도 됨.
- 알림 숫자(종 위 빨간 숫자)는 `AppShell.jsx:118` `useNotifications(...).length`.

### 목표 상태 (기대 동작)
1. 실패 목록에 있는 날짜·차량마다 알림 하나: 제목 "{M}월 {D}일 운행일지 저장이 실패했습니다." 내용 "일지를 확인한 후 저장이 안 된 내용이 있으면 다시 입력해 주세요."(보리 문구, 띄어쓰기만 고침), 기사 차량 일지면 제목의 "운행일지" 뒤에 차량 짧은 번호(예: "10월 22일 운행일지(5678) 저장이 실패했습니다."). 일지 화면에서 뜨는 실패 알림 문구(109-B "…저장에 실패했습니다. 네트워크 상태를 확인해 주세요.")는 그대로. 날짜 줄은 비움(제목에 날짜).
2. 이 알림들은 목록 맨 위, 날짜 오래된 순.
3. 누르면 그 날짜 일지로 간다 — 메인은 `day/<날짜>`, 기사 차량은 `logs/<logId>/day/<날짜>`.
4. [닫기] 단추가 없다. 다른 알림의 [닫기]는 그대로.
5. 알림 받기 스위치 목록에 이 종류가 없고, `notifOff`에 무엇이 있어도 이 알림은 빠지지 않는다.
6. 실패가 생기거나 지워지면(110-A) 알림 목록과 종 위 숫자가 화면을 옮기지 않아도 바로 바뀐다.
7. 비회원·서버 밖 차량 일지(휴대폰 대기 목록, 자동 재시도)는 이번에 알림을 만들지 않는다 — 110-A 목록이 로그인 갈래만이라서.

### 건드릴 파일
- `react-app/src/data/sync/failedDayLogDates.ts` — 화면 구독용 "지금 값" 함수 하나(바뀔 때만 달라지는 값, 예: 저장된 글 그대로 또는 바뀜 횟수). 65 → 약 72줄.
- `react-app/src/features/notifications/api/notifications.js` — 실패 알림 항목 만들기(`kind: 'saveFailed'`, `noDismiss: true`), 맨 앞에 넣기, `notifOff` 거르기에서 이 종류 제외. 항목 타입에 `noDismiss?: boolean`. 156 → 약 175줄. 실패 알림 만드는 부분 위 주석(최종 문구):
  `// 일지 저장 실패(로그인) — 휴대폰에 남은 실패 날짜마다. 닫기·끄기 없이, 다시 저장되거나 그 날짜 일지를 열면 사라진다.`
- `react-app/src/features/notifications/hooks/useNotifications.js` — 실패 목록 구독(`useSyncExternalStore(subscribeFailedDayLogDates, 지금 값)`)을 다시 세는 조건에 더함. 38 → 약 42줄.
- `react-app/src/features/notifications/ui/NotificationsPage.jsx` — `item.noDismiss`면 [닫기] 안 그림. 105 → 약 106줄.
- 테스트(200줄 예외): 알림 만들기 — 실패 날짜 2개(메인·기사 차량) → 제목·page·맨 앞·닫은 id/notifOff 무시. 알림 화면 — 실패 알림에 [닫기] 없음, 누르면 `onOpenPage('day/2026-10-22')`, 알림 받기에 이 종류 없음. 구독 — 목록에 더하면 숫자가 바로 늘어남.

### 안 건드릴 것
- `app/shell/AppShell.jsx`(198줄)·`AppShellRoutes.jsx` — 주소 변환이 이미 됨.
- 110-A 기록·지우기 규칙, 일지 저장 흐름.
- 다른 알림 종류·문구·[닫기].
- 휴대폰 푸시(앱이 꺼져 있을 때 알림) — 로드맵 11번.

### 검증
- CI 초록(테스트·타입·빌드·코드 검사·의존 방향).
- 새 테스트는 고치기 전 코드에서 실패하는지 확인(revert-check).
- 브라우저(개발 화면): 실패를 흉내 낼 수 있으면(인터넷 끊기) 일지 저장 실패 → 종 숫자 +1 → 알림 화면 맨 위 실패 알림·[닫기] 없음 → 누르면 그 날짜 일지 → 돌아오면 알림 사라짐. 흉내가 안 되면 테스트로 대신하고 보리 휴대폰 확인.

### 파일 길이
- `failedDayLogDates.ts` 65 → 약 72, `notifications.js` 156 → 약 175, `useNotifications.js` 38 → 약 42, `NotificationsPage.jsx` 105 → 약 106줄. 모두 200줄 아래. 테스트는 예외.

### 실패 시 처리
- 새 저장·재시도 없음(110-A 목록을 읽기만).
- 구독 때문에 화면이 계속 다시 그려지면(지금 값이 매번 달라짐) 멈추고 보고.

### 진행 기록
- 교차검증: 줄 번호·흐름(`collectNotifications` 마지막 `notifOff` 거르기, `useNotifications` 다시 세기 조건, `NotificationsPage` [닫기]·`onOpenPage(item.page)`, `pagePath`가 `day/…`·`logs/<logId>/day/…`를 그대로 주소로)은 코드와 맞음.
- `failedDayLogDates.ts` 65 → 72줄: `failedDayLogDatesVersion()` — 목록을 쓸 때마다 1 오르는 번호(바뀔 때만 달라져 다시 그리기 반복 없음).
- `notifications.js` 156 → 174줄: 실패 알림을 다른 알림보다 먼저 넣음(날짜 → 차량 순 정렬), id `saveFailed:<logId>:<dateKey>`, 기사 차량은 제목에 `getShortCarNum`(예: "(5678)"). 닫은 id는 안 보고, `notifOff` 거르기에서 `saveFailed` 제외. 문의 알림은 원래대로 그 뒤.
- `useNotifications.js` 38 → 41줄: `useSyncExternalStore(subscribeFailedDayLogDates, failedDayLogDatesVersion)`을 다시 세는 조건에 더함 — 종 위 숫자(`AppShell`)도 같은 훅이라 같이 바뀜.
- `NotificationsPage.jsx` 105줄 그대로: `noDismiss`면 [닫기] 안 그림. 문의 알림 항목 타입에는 이 칸이 없어 `'noDismiss' in item`으로 확인. 알림 받기 목록(`kinds`)은 손대지 않아 이 종류 스위치 없음.
- 검사: 테스트 892 + 776(새 3개), 타입·코드 검사·의존 방향(1727 → 1730)·빌드 통과.
- 고치기 전 실패 확인: 새 테스트 3개(알림 만들기 2, 알림 화면 1 — [닫기] 없음·누르면 `day/2026-10-22`·스위치 없음·더하고 지우면 바로 바뀜) 모두 수정 전 코드에서 실패.
- 브라우저: 로그인 개발 화면에서 서버 실패 흉내는 하지 않음 — 테스트로 대신, 보리 휴대폰 확인.
- 코드 커밋: react-app `513ef5e`(6개 파일, push 안 함). 남은 것: 보리 push → CI 초록 → 휴대폰 확인 → `[x]` 때 STATUS·로드맵 110 `[x]`.
- push 뒤 CI verify·deploy 초록(run 39913365148, react-app `513ef5e`). 브라우저(AI, 개발 화면): 휴대폰 실패 목록에 10월 21일을 임시로 넣고 새로고침 → 알림 화면 맨 위 "10월 21일 운행일지 저장이 실패했습니다." / "일지를 확인한 후 저장이 안 된 내용이 있으면 다시 입력해 주세요.", [닫기] 없음, 알림 받기에 이 종류 없음 → 누르면 `/app/day/2026-10-21` 열림·목록에서 지워짐 → 알림 화면에서 사라짐. 실제 서버 저장 실패(인터넷 끊기)와 화면을 안 옮겼을 때 종 숫자 바로 늘어남은 화면에서 안 봄(테스트로만) — 보리 휴대폰 확인 필요. 임시 값은 지움.
- 2026-10-23 보리 승인("승인 처리해"). `[x]`.

### 사용자 확인 방법
1. 로그인 계정으로 일지를 열고, 인터넷을 끈 뒤 횟수 바꾸기 → 빨간 아이콘·실패 알림 문구.
2. 다른 화면으로 가기 → 종 위 숫자가 1 늘었는지.
3. 앱을 완전히 껐다 켜기 → 종 숫자 그대로, 알림 화면 맨 위에 "10월 22일 운행일지 저장이 실패했습니다." / "일지를 확인한 후 저장이 안 된 내용이 있으면 다시 입력해 주세요.", [닫기] 없음, 알림 받기 목록에 이 종류 없음.
4. 알림 누르기 → 그 날짜 일지가 열림 → 뒤로 가서 알림 화면 → 알림이 사라졌는지.

---

## 로드맵 94 `.ts` 전환 — 남은 조각 (94-A·B·C `[x]`, D~G는 출시 전 순서 뒤)

지난 지시서·진행 기록: 94-A drivelog-docs `3ee010c`, 94-B `90ef647`, 94-C `0c0eafe`(`git show <해시> -- docs/report.md`).

| 조각 | 범위 | 운영 파일 | 비고 |
|---|---|---|---|
| ~~94-A~~ `[x]` | 설정 + 시범 2개 | 2 | react-app `a009f2b` |
| ~~94-B~~ `[x]` | `domain/` | 58 | react-app `855fb6a` |
| ~~94-C~~ `[x]` | `data/` | 81 | react-app `079aee8` |
| 94-D | `shared/` + `app/` + `main.jsx` + `testSupport/` | 69 | |
| 94-E | `features/` 일지·달력·지출 | 39 | |
| 94-F | `features/` 서류·미수금·매출 | 38 | |
| 94-G | `features/` 나머지 8개 | 64 | 마무리: `allowJs` 끄기, `// @ts-check` 규칙 정리 |

- 순서 `[확인: 2026-10-22 보리 — "그럼 반만, 응"]`: D~G는 출시 전 순서(23·24 → 34 → 79) 뒤.
- 방식(B·C와 같음): AI가 손으로, 밖 파일은 경로 끝만(기계 비교), `mock.module` 지정 포함. 증명: 타입 설명 파일(`.d.ts`, 저장소 밖에서 뽑음) 비교 + 빌드 결과. 옛 JSDoc 형 변환 주석이 빠지면 압축 변수 이름이 바뀌어 해시가 달라질 수 있음(94-C) — 그때는 저장소 밖에서 옛·새를 압축 없이 빌드해 같은지로 증명(보리 승인 방식).
- 의존 검사 연결 수는 JSDoc `import('…')`이 `import type`이 되며 타입 전용 연결만큼 늘어남 — 늘어난 것이 그것뿐인지 확인.
- 94-D: `// @ts-check` 없는 4개(`app/guestSessionPersist.js`·`app/routes/ComingSoonRoute.jsx`·`app/routes/lazyPages.js`·`app/routes/sessionGate.js`)는 처음 타입 검사를 받음. `.jsx`는 `.tsx`로 — 화면 테스트 로더가 이미 읽음(94-A).
