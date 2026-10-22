# docs/report.md — 현재 슬라이스 착수지시서

23-B 지시서·기록은 drivelog-docs `4e692ce`(23-A는 `ac81245`, 103은 `27aa8b3`).

## 로드맵 23-C 문의 알림(앱 안) — 사용자 "답변 도착"·관리자 "새 문의" `[x]` `[확인: 2026-10-22 보리 — "휴대폰에서 답변 도착 알림 확인했어, 23-C [x] 승인"]`

보리 지시(2026-10-22, 23-B `[x]` 뒤): "23-C 문의 알림 착수지시서 작성해". 로드맵 23-C `[확인: 2026-10-22 보리 — "응 그렇게 하자"]`(메일 알림 대신 앱 안 알림). 서버는 23-A로 끝 — 이번엔 앱만, DB 변경 없음.

### 정한 것 (셋) `[확인: 2026-10-22 보리 — "권하는대로 지시서 작성해줘"]`
1. **사용자 "답변 도착"이 사라지는 때** — 지금 다른 알림처럼 [닫기]를 누르면 사라짐. 관리자가 답변을 **수정하면 다시 뜸**(바뀐 답을 봐야 하니). 누르면 고객센터 **"나의 문의/건의 확인" 탭이 바로** 열림. (탭을 열기만 해도 사라지게 하는 안은 안 함 — 본 기록을 따로 남겨야 해서.)
2. **관리자 "새 문의"** — 문의마다가 아니라 **한 줄로 "답변 대기 문의 N건"**. [닫기]를 눌러도 **새 문의가 들어오면 다시 뜸**, 모두 답하면 사라짐. 누르면 관리자 화면.
3. **"알림 받기" 스위치** — "문의 답변 도착"(로그인 계정 모두) 추가, "새 문의"(관리자 계정에만 보임) 추가. 다른 알림처럼 끌 수 있음.

### 쉬운 요약
- 앱을 켜거나 화면을 옮길 때 서버에 물어봐서, 문의한 사람에게는 "문의에 답변이 도착했습니다", 보리(관리자)에게는 "답변 대기 문의 N건"을 알림 목록과 종 숫자에 띄웁니다.
- 앱을 열어야 보입니다(앱이 꺼져 있을 때 휴대폰을 울리는 푸시는 11번 앱 포장 때).
- 서버·문의 화면·답변 방식은 그대로입니다.

### 현재 상태 (확인 근거)
- 알림 목록은 `features/notifications/api/notifications.js`(156줄) `collectNotifications(ownerKey, session)`이 **휴대폰에 있는 값(Store)으로만** 만든다 — 연체 미수금·기사 초대·오늘 일지·일상점검·백업. 서버에서 가져오는 알림은 없음(같은 파일에 `supabase` 0).
- 알림 줄 모양: `{ id, kind, page, title, body, dateLabel, actionLabel?, dismissLabel? }`. [닫기] → `dismissNotification(ownerKey, id)` → 닫은 id 목록 `dismissedNotifications`(기존 저장 칸, `data/store/app-store.ts:41`)에 들어가 다시 안 보임. 끈 종류는 설정 `notifOff`로 뺌(`notifications.js:153-155`).
- 끌 수 있는 종류 이름 목록 `domain/practiceSettings.ts:11` `NOTIF_KINDS = ['overdue', 'today', 'driverInvite', 'backup']` — 목록에 없는 이름은 저장할 때 버려짐(`normalizeNotifOff`). 스위치 목록은 `NotificationsPage.jsx:30-35`(101줄).
- 알림을 쓰는 곳: `AppShell.jsx:114` `useNotifications(ownerKey, null, location.pathname)` → 종 숫자 `notifications.length`(홈 `AppTopBar`). 알림 화면 `NotificationsPage.jsx:28` `useNotifications(ownerKey, session)`. 훅 `features/notifications/hooks/useNotifications.js`(31줄)는 Store 값을 구독해 다시 셈, 세 번째 값(화면 주소)이 바뀌어도 다시 셈.
- 알림을 누르면 `onOpenPage(item.page)` → `AppShell` `goToPage` → `PAGE_PATH`(이름 → 주소). 고객센터는 `support` → `/app/support`, 첫 탭은 늘 FAQ(`CustomerCenterPage.jsx:53` `useState('faq')`, 109줄) — 주소로 탭을 고를 방법 없음.
- 서버: 사용자는 본인 문의를 읽을 수 있음(`id·title·answer·answered_at` 등, sot §4-15). 관리자는 `admin_list_support_inquiries()`(답변 대기 먼저·최근 먼저). 관리자인지는 `data/cloud/adminRole.ts` `useIsAdmin()`(23-B).
- 의존 방향: `notifications` 기능은 `support` 기능을 못 부름(`.dependency-cruiser.cjs` no-cross-feature) → 문의를 읽는 부분은 `data/cloud`에 둠.
- 관련 기존 테스트: `notifications.test.js`·`notifications.dailyInspection.test.js`·`notifications.dateLabel.test.js`·`NotificationsPage.test.js`·`persistDomainSchema.notifOff.test.js`·`settingsSave.serial.test.js`(끈 종류 저장).

### 목표 상태 (기대 동작)
1. **언제 물어보나**: 로그인 계정일 때만, 앱을 켤 때와 화면 주소가 바뀔 때(지금 알림이 다시 세는 때와 같음), 알림 화면을 열 때. 비회원은 서버를 안 부름. 서버 오류면 문의 알림만 조용히 안 보임(다른 알림은 그대로, 알림 문구·재시도 없음). 받은 값은 화면 메모리에만(새 저장 없음), 계정이 바뀌면 이전 계정 값은 버림.
2. **사용자 "답변 도착"**: 본인 문의 중 답변이 있는 것마다 한 줄 — 제목 "문의에 답변이 도착했습니다", 내용 문의 제목, 날짜 줄 "답변 MM.DD". id는 `inquiryAnswer:<문의 id>:<답변 시각>` → [닫기]하면 사라지고, 답변이 수정돼 시각이 바뀌면 다시 뜸. 누르면 고객센터 "나의 문의/건의 확인" 탭.
3. **관리자 "새 문의"**: 관리자 계정이고 답변 대기 문의가 1건 이상이면 한 줄 — 제목 "답변 대기 문의 N건", 내용 가장 최근 대기 문의 제목. id는 `adminInquiry:<가장 최근 대기 문의 id>` → [닫기]해도 새 문의가 들어오면 다시 뜸, 0건이면 없음. 누르면 관리자 화면.
4. **스위치**: "알림 받기"에 "문의 답변 도착"(`inquiryAnswer`, 로그인 계정), "새 문의"(`adminInquiry`, 관리자 계정에만) — 끄면 목록·종 숫자에서 빠짐. `NOTIF_KINDS`에 두 이름 추가(저장할 때 안 버려지게).
5. **종 숫자**에 위 알림도 셈(홈 종 아이콘).
6. **고객센터 탭 주소**: `/app/support?tab=my`로 열면 "나의 문의/건의 확인" 탭이 처음부터 선택됨. 주소에 없거나 모르는 값이면 지금처럼 FAQ.
7. 다른 알림(연체·초대·오늘 일지·일상점검·백업)의 동작·순서는 그대로, 문의 알림은 목록 맨 뒤.

### 건드릴 파일
- 새 `src/data/cloud/inquiryNotices.ts` — `fetchMyAnsweredInquiries(userId)`(본인 문의 `id·title·answer·answered_at` 읽기 → 답 있는 것만), `fetchPendingInquirySummary()`(관리자 함수로 대기 건수·가장 최근 대기 문의). 응답은 칸마다 모양 확인(AGENTS §6).
- 새 `src/features/notifications/api/inquiryNoticeItems.ts` — 받은 값 → 알림 줄(목표 2·3, 닫은 id·끈 종류 빼기). 계산만, 서버·저장 없음.
- 새 `src/features/notifications/hooks/useInquiryNotices.ts` — 목표 1(언제 물어보나, 계정 바뀜 버리기).
- `src/features/notifications/hooks/useNotifications.js` — 기존 목록 뒤에 문의 알림 붙이기(31 → 약 40).
- `src/features/notifications/ui/NotificationsPage.jsx` — 스위치 2개(관리자 것은 관리자만)(101 → 약 106).
- `src/domain/practiceSettings.ts` — `NOTIF_KINDS`에 `'inquiryAnswer'`, `'adminInquiry'`.
- `src/app/shell/AppShell.jsx` — `PAGE_PATH`에 `'my-inquiries': 'support?tab=my'` 1줄(191 → 192).
- `src/features/support/ui/CustomerCenterPage.jsx` — 처음 탭을 주소 `tab=my`에서(109 → 약 113).
- 새 테스트: `src/data/cloud/inquiryNotices.test.js`, `src/features/notifications/api/inquiryNoticeItems.test.js`, `src/features/notifications/hooks/useInquiryNotices.test.js`(또는 알림 화면 테스트에 붙임), `CustomerCenterPage.test.js`에 `?tab=my` 한 개 추가, `persistDomainSchema.notifOff.test.js`에 새 두 이름 남는지 한 개 추가.
- 문서(`[x]` 때): sot §4-15에 문의 알림 규칙 한 줄.

### 안 건드릴 것
- 서버(SQL 없음), `notifications.js` `collectNotifications`(그대로 — 새 알림은 훅에서 뒤에 붙임), 닫은 알림 저장 방식(기존 `dismissedNotifications` 그대로 씀 — 새 저장 칸 없음), 관리자 화면·답변 저장(23-B), 답변 문구 버튼(23-D), 휴대폰 푸시(11번).
- `testSupport/stubSupabaseClient.js`(그대로 — 새 테스트는 `fakeSupabaseClient.js`).

### 검증
- 새 테스트: 서버 응답 모양 확인(이상한 줄 버림), 답 있는 문의만 알림·id에 답변 시각, 닫은 id·끈 종류는 빠짐, 관리자 0건이면 없음·새 대기 문의면 id가 바뀜, 비회원은 서버 안 부름, 서버 오류면 문의 알림만 빠짐, 계정이 바뀌면 이전 값 안 보임, 스위치 두 개(관리자 것은 관리자만), `?tab=my`로 "나의 문의" 탭, `notifOff`에 새 이름이 남음.
- 기존 알림 테스트 전부 그대로 통과, act 경고·처리 안 된 오류 늘지 않음(플레이북 §6). 타입·빌드·의존 방향은 CI.
- 브라우저: 휴대폰 계정(답변 받은 시험 문의 있음) → 종 숫자 +1, 알림 화면에 "문의에 답변이 도착했습니다" → 누르면 "나의 문의/건의 확인" 탭 → [닫기]하면 사라짐. 보리 계정 → 휴대폰으로 새 문의 넣기 → 화면 옮기면 "답변 대기 문의 1건" → 누르면 관리자 화면 → 답변하면 사라짐.

### 파일 길이
- 새 파일: `inquiryNotices.ts` 약 70줄, `inquiryNoticeItems.ts` 약 60줄, `useInquiryNotices.ts` 약 50줄 — 200 이하.
- 고치는 파일: `useNotifications.js` 31 → 약 40, `NotificationsPage.jsx` 101 → 약 106, `practiceSettings.ts` 줄 수 그대로, `AppShell.jsx` 191 → 192, `CustomerCenterPage.jsx` 109 → 약 113.
- 파일 수 8개(새 3·고침 5, 테스트 따로)로 보통 슬라이스보다 많음 — 사용자 알림과 관리자 알림은 같은 "서버에서 물어 와 알림에 붙이기"를 같이 써서 한 슬라이스로 둠. 고치는 5개는 몇 줄씩.

### 코드에 넣을 주석(최종 문구)
- `inquiryNotices.ts`: `// 알림용 문의 읽기 — 내 문의 중 답변 온 것, 관리자면 답변 대기 건수(로드맵 23-C). 저장 없음.`
- `inquiryNoticeItems.ts`: `// 문의 알림 줄 만들기 — 답변 시각·최근 대기 문의를 id에 넣어 바뀌면 다시 뜬다.`
- `useInquiryNotices.ts`: `// 앱을 켤 때·화면을 옮길 때 서버에 물어 문의 알림을 만든다. 실패하면 문의 알림만 빠진다.`

### 실패 시 처리
- 새 저장 장치·재시도·대기열 없음(AGENTS §7). 서버 오류는 문의 알림만 빠지는 것으로 끝(사용자에게 알림 문구 없음 — 뒤에서 도는 확인이라). 테스트·브라우저가 기대와 다르면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 휴대폰(답변 받은 시험 문의가 있는 계정) → 홈 종 아이콘에 숫자 → 누르면 "문의에 답변이 도착했습니다"(내용: 문의 제목).
2. 그 알림 누르기 → 고객센터 "나의 문의/건의 확인" 탭이 바로 열리고 답변이 보임.
3. 알림 화면에서 [닫기] → 사라지고 숫자 줄어듦. 보리가 관리자 화면에서 그 답변을 수정하면 → 휴대폰에서 화면을 옮길 때 다시 뜸.
4. 휴대폰에서 새 문의 → 보리 계정(PC)에서 화면 옮기기 → "답변 대기 문의 1건" → 누르면 관리자 화면 → 답변 저장 뒤 화면 옮기면 사라짐.
5. 알림 화면 "알림 받기"에 "문의 답변 도착"(두 계정)·"새 문의"(보리 계정에만), 끄면 그 알림이 빠짐.

### 진행 기록
- 2026-10-22 보리 "착수지시서 확정,작업진행해". 교차검증: 알림 줄 모양·닫기·`NOTIF_KINDS`·스위치·`PAGE_PATH`·의존 방향 모두 지시서와 같음.
- 지시서와 다른 점 2개: ① `?tab=my`는 `CustomerCenterPage`가 주소를 직접 읽지 않고, 이미 주소 값을 읽는 `AppShellRoutes.jsx`가 `initialTab`으로 넘김(1줄 고침, 파일 1개 늘어남) — 고객센터 기존 테스트가 주소 없이 화면을 그려서. ② `AppShell`은 `useNotifications`에 세션을 안 넘기므로(`null`) 문의 알림 훅은 세션을 `useSession()`에서 직접 읽음. 답변 날짜 줄은 서버 시각을 이 휴대폰 날짜로 바꿔 'MM.DD'.
- 결과(커밋 전): 새 테스트 3개 파일 + 기존 2개 파일에 1개씩, 27개 통과. `npm test` 883(878 + 5)·690(680 + 10) 전부 통과, act 경고 10 그대로. 타입 오류 0, lint 경고 50 그대로, 의존 검사 위반 0(430개 모듈), 빌드 통과. 줄 수: `inquiryNotices.ts` 52·`inquiryNoticeItems.ts` 55·`useInquiryNotices.ts` 33, `useNotifications.js` 38·`NotificationsPage.jsx` 105·`practiceSettings.ts` 110(그대로)·`AppShell.jsx` 192·`CustomerCenterPage.jsx` 110·`AppShellRoutes.jsx` 104(그대로). 브라우저 확인은 아직.
- 코드 커밋 react-app `1b8981c`(2026-10-22, 14개 파일, push 안 함). 남은 것: 보리 push → CI → 배포본 브라우저 확인(사용자 확인 방법 1~5) → §5 리뷰 → 최종 승인.
- 보리 push 뒤 CI verify·deploy 초록(run 39770001992). 데스크톱 AI 브라우저 확인(보리 계정 로그인된 localhost:5174, 코드는 `1b8981c`와 같음): 홈 종 숫자 1, 알림 화면 "답변 대기 문의 1건 / 실험1", 스위치에 "문의 답변 도착"·"새 문의", 알림을 누르면 `/app/admin/inquiries`(답변 대기 1건), `/app/support?tab=my`면 "나의 문의/건의 확인" 탭, 콘솔 오류 0. [닫기]는 보리 기록이라 안 누름. 사용자 쪽("답변 도착", 확인 방법 1~3)은 휴대폰 계정이라 보리 몫.
- §5 리뷰(데스크톱 AI): ① 범위 — 14개 파일, 지시서 + 진행 기록의 다른 점(`AppShellRoutes.jsx` 1줄)과 같음 ② 몰래 증설 없음(받은 값은 화면 메모리, 닫기는 기존 `dismissedNotifications`) ③ 타입 꼼수 0 ④ 200줄 — 최대 `AppShell.jsx` 192 ⑤ 테스트 — 새 3개 파일 + 기존 2개 파일에 1개씩, 기존 테스트 약화 없음. 주소 `?tab=my` → `initialTab` 연결은 테스트 없이 브라우저로만 확인 ⑥ 공용 영향 — `NOTIF_KINDS`에 이름 추가뿐(저장 값 검사 테스트 추가됨) ⑦ 목표 1~7 코드 대조 맞음.

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
