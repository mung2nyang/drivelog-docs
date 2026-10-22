# docs/report.md — 현재 슬라이스 착수지시서

23-A 지시서·기록은 drivelog-docs `ac81245`(103은 `27aa8b3`).

## 로드맵 23-B 문의 관리자 화면 `[x]` `[확인: 2026-10-22 보리 — "내가 확인했으니 그냥 승인해"]`

보리 지시(2026-10-22, 23-A `[x]` 뒤): "23-B 관리자 화면 착수지시서 작성해". 로드맵 23번 순서 A(서버 `[x]`) → **B(관리자 화면)** → C(사용자 답변 알림) → D(보리 메일 알림) 중 **이번엔 B만**. 서버는 23-A에서 끝남(sot §4-15) — 이번엔 앱 화면만.

### 정한 것 — 메뉴 이름과 자리 `[확인: 2026-10-22 보리 — "이름을 관리자로 하거나, 옆 메뉴(햄버거)에도 넣을 수 있습니다. 이렇게"]`
- 이름 **"관리자"**, 두 곳: 마이페이지 아래 목록 "고객센터" 다음 줄 + 옆 메뉴 "설정" 칸 "고객센터" 다음 줄. 관리자 계정에만 보임. 답변 대기 건수 숫자는 안 붙임.

### 쉬운 요약
- 보리 계정으로 로그인하면 마이페이지와 옆 메뉴에 "관리자"가 생깁니다. 누르면 모든 문의가 답변 대기 먼저 보이고, 문의마다 "답변하기"를 눌러 답을 쓰거나 고칠 수 있습니다.
- 관리자가 아닌 계정은 메뉴가 안 보이고, 주소를 직접 쳐서 들어와도 홈으로 돌아갑니다(진짜 막는 건 23-A 서버).
- 답변을 저장하면 문의한 사람의 "나의 문의/건의 확인"에 바로 "답변 완료"와 답이 보입니다(23-A 흉내 확인에서 확인). "답변 도착" 알림은 23-C.

### 현재 상태 (확인 근거)
- 서버: 함수 `is_admin()`·`admin_list_support_inquiries()`(칸 `id·type·title·content·status·answer·answered_at·created_at·user_name`, 답변 대기 먼저·최근 먼저)·`admin_answer_support_inquiry(inquiry_id, answer_text)` 있음, `authenticated`만 부를 수 있음(23-A 사후검증). 관리자가 아니면 오류 코드 `42501`, 빈 답변 `22023`, 없는 문의 `P0002`(`0025_support_admin.sql`).
- 앱: 이 함수들을 부르는 곳 없음(`src` grep `is_admin`·`admin_` 0). 서버 함수 부르는 방식은 `supabase.rpc(이름, 인자)`(`data/sync/driverLinkRpc.ts:106` 등).
- 마이페이지 `features/account/ui/MyPage.jsx` **197줄** — 아래 목록(`mypage-notice-link` 버튼들)의 마지막이 "고객센터"(`onOpen('support')`). 이동은 `onOpen(page)` → `AppShell.jsx` `PAGE_PATH`(이름 → 주소) → `navigate`.
- 옆 메뉴 `app/shell/SideMenu.jsx`(138줄): "설정" 칸 마지막이 "고객센터"(`pick('support')` → `onSelect` → `AppShell` `goToPage`). 맨 앞에 `if (!open) return null`이 있어 이 안에서 훅을 부를 수 없음 → `AppShell.jsx`(187줄)가 관리자 여부를 구해 넘김. 아이콘은 `app/shell/side-menu/SideMenuIcons.jsx`(85줄). 이 메뉴를 그리는 기존 테스트: `SideMenu.banner`·`App.guestDurable`·`accountPermissionUi`·`taxInvoiceMenuMoved`·`CustomerCenterPage`.
- 화면 주소: `app/routes/AppShellRoutes.jsx`(102줄)에 `<Route path="support" …>`, 지연 불러오기 `app/routes/lazyPages.js`(21줄).
- 고객센터 화면 `features/support/ui/`: `MyInquiriesList.jsx`(목록 카드 `my-inquiry-*` 클래스, 불러오기 중 `SkeletonLines`, 실패 시 알림 + 빈 목록), `InquiryForm.jsx`(글 칸·`useToast`). 서버 호출은 `features/support/api/supportInquiryMutations.js`(`captureSession`·`assertSessionStillCurrent`·`assertCloudWriteReady`로 세션 바뀜·저장 준비 확인).
- 의존 방향 검사(`.dependency-cruiser.cjs`): 기능은 다른 기능을 못 부름 → 마이페이지(`account`)와 관리자 화면(`support`)이 같이 쓰는 "관리자인지"는 `data/`에 둬야 함.
- 새 운영 파일은 TS 표기(AGENTS §6) → `.ts`/`.tsx`. 화면 테스트 로더는 `.tsx`를 읽음(94-A, `shared/ui/SwitchRow.tsx`).
- 테스트 가짜 서버: `testSupport/fakeSupabaseClient.js`는 `rpc` 지원(`handlers.rpc[이름]`, `countOf('rpc', 이름)`), `stubSupabaseClient.js`는 `rpc` 없음(마이페이지를 그리는 기존 테스트 5개가 씀 — `MyPage.dataDownload`·`MyPage.inviteModal`·`LinkStatusChip`·`app/accountPermissionUi`·`app/taxInvoiceMenuMoved`).

### 목표 상태 (기대 동작)
1. **관리자인지 확인** `useIsAdmin()`: 로그인 계정일 때만 서버 `is_admin()`을 한 번 부름. 결과는 확인 중 / 관리자 / 아님 셋. 비회원은 서버를 안 부르고 "아님". 서버 오류·응답 모양이 이상하면 "아님"(메뉴 숨김 — 안전한 쪽). 따로 기억해 두지 않음(새 저장 없음).
2. **메뉴 두 곳**: 관리자일 때만 "관리자" 줄 — 마이페이지 아래 목록 "고객센터" 다음(같은 `mypage-notice-link` 모양), 옆 메뉴 "설정" 칸 "고객센터" 다음(같은 `dropdown-item` 모양, 방패 아이콘). 누르면 `/app/admin/inquiries`. 관리자가 아니거나 확인 중이면 아무것도 안 보임.
3. **관리자 화면** `/app/admin/inquiries`: 머리 "관리자" + ←(마이페이지로), 그 아래 "문의 관리" 제목 카드. 확인 중이면 뼈대 화면, 관리자가 아니면 홈(`/app`)으로 바로 돌려보냄. 관리자면 서버 목록을 불러와 "답변 대기 N건" 한 줄 + 카드 목록(서버 순서 그대로 — 답변 대기 먼저). 문의 0건이면 "아직 들어온 문의가 없습니다.". 못 불러오면 "문의 목록을 불러오지 못했습니다." 알림 + 빈 목록 안내.
4. **카드**: 유형·상태(답변 대기/답변 완료)·제목·내용(줄바꿈 그대로)·문의한 사람 이름(없으면 "이름 없음")·접수 시각(날짜+시각). 답변이 있으면 "운영자 답변"과 답변 시각. 버튼 "답변하기"(답변 없을 때)/"답변 고치기"(있을 때) → 그 카드 안에 글 칸(지금 답변이 채워진 채, 2000자까지) + "저장"·"취소". 한 번에 한 카드만 열림. 글이 비었거나 공백뿐이면 "저장"을 못 누름.
5. **저장**: 서버 `admin_answer_support_inquiry` 한 번. 저장 중엔 버튼 잠금(두 번 누름 막기). 성공 → "답변을 저장했습니다." 알림, 글 칸 닫고 목록을 서버에서 다시 불러옴. 실패 → 글 칸·쓴 글 그대로 두고 알림: 관리자 아님(`42501`) "관리자만 답변할 수 있습니다.", 없는 문의(`P0002`) "없는 문의입니다. 목록을 다시 불러옵니다."(+ 목록 다시 불러옴), 그 밖 `SERVER_SAVE_FAIL`(기존 저장 실패 문구), 세션이 바뀌면 `SESSION_CHANGED_FAIL`. 재시도·대기열 없음.
6. 서버 응답은 칸마다 모양을 확인해 좁힘(AGENTS §6 — `id`가 없거나 글자가 아니면 그 줄은 버림, 나머지 칸은 글자 아니면 빈 값).
7. 일반 사용자 화면(고객센터·나의 문의)은 그대로.

### 건드릴 파일
- 새 `src/data/cloud/adminRole.ts` — `fetchIsAdmin()` + `useIsAdmin()`(목표 1). `data/cloud`에 두는 이유: `account`·`support` 두 기능이 같이 씀(의존 방향 검사).
- 새 `src/features/support/api/adminInquiries.ts` — `fetchAdminInquiries()`(목표 6), `requestAdminAnswerSave(id, answer)` → `{ ok, toast }`(목표 5, 기존 `supportInquiryMutations.js`와 같은 세션·저장 준비 확인).
- 새 `src/features/support/ui/AdminInquiriesPage.tsx` — 화면 틀·문·목록 불러오기(목표 3).
- 새 `src/features/support/ui/AdminInquiryCard.tsx` — 카드 한 장 + 답변 글 칸(목표 4·5).
- 새 `src/features/account/ui/AdminEntryLink.tsx` — 마이페이지 "관리자" 줄(목표 2). 마이페이지가 197줄이라 따로 둠.
- `src/features/account/ui/MyPage.jsx` — 부르기 1줄 + "고객센터" 다음에 `<AdminEntryLink …/>` 1줄.
- `src/app/routes/lazyPages.js` — `AdminInquiriesPage` 1줄. `src/app/routes/AppShellRoutes.jsx` — 부르기 목록 1줄 + `admin/inquiries` 주소 1줄(← 는 `goBack('/app/me')`). `src/app/shell/AppShell.jsx` — `PAGE_PATH`에 `admin: 'admin/inquiries'` 1줄 + `useIsAdmin()` 부르기·`<SideMenu isAdmin={…}>` 넘기기(3줄).
- `src/app/shell/SideMenu.jsx` — `isAdmin` 받기 + "고객센터" 다음 "관리자" 버튼(`pick('admin')`). `src/app/shell/side-menu/SideMenuIcons.jsx` — `AdminIcon`(방패) 1개 추가.
- `src/features/support/ui/customer-center.css` — **새 클래스만 추가**(`admin-inquiry-*`: 건수 줄·이름·글 칸·버튼 줄). 기존 클래스는 안 고침(공용 클래스 영향 없음 — 카드 모양은 기존 `my-inquiry-*`를 그대로 씀).
- 새 테스트 4개: `src/features/support/api/adminInquiries.test.js`, `src/features/support/ui/AdminInquiriesPage.test.js`, `src/features/account/ui/MyPage.adminEntry.test.js`, `src/app/shell/SideMenu.admin.test.js`(아래 검증).
- 문서(`[x]` 때): sot §4-15에 "관리자 화면은 마이페이지·옆 메뉴 → 관리자, `/app/admin/inquiries`" 한 줄.

### 안 건드릴 것
- 서버(23-A로 끝, SQL 없음), 일반 사용자 화면 `CustomerCenterPage.jsx`·`MyInquiriesList.jsx`·`InquiryForm.jsx`·`supportInquiryMutations.js`(그대로 — 목표 7), 답변 도착 알림(23-C), 메일(23-D).
- `testSupport/stubSupabaseClient.js`에 `rpc` 안 넣음 — 넣으면 기존 테스트들의 동작이 바뀔 수 있음. 새 테스트는 `fakeSupabaseClient.js`(이미 `rpc` 있음)를 씀. 기존 마이페이지 테스트는 로그인 세션이면 `useIsAdmin`이 `rpc` 없음 오류를 받아 "아님"으로 처리하므로 그대로 통과해야 함(목표 1) — 기존 테스트 통과·경고 0으로 확인.

### 검증
- 새 테스트(가짜 서버 `rpc`로, 화면은 실제 누르기 경로로):
  - `adminInquiries.test.js`: 목록 — 이상한 줄 버림·빈 칸 처리, 오류면 실패를 알림. 저장 — `admin_answer_support_inquiry`를 `{ inquiry_id, answer_text }`로 1번, 공백뿐이면 안 부름, `42501`/`P0002`/그 밖 오류마다 문구, 저장 중 세션이 바뀌면 세션 문구.
  - `AdminInquiriesPage.test.js`: 관리자 아님 → `/app`으로 이동·목록 안 부름. 관리자 → "답변 대기 N건"·카드·이름 보임. "답변하기" → 글 칸 → 저장 → 서버 1번·알림·목록 다시 불러옴. 실패 → 쓴 글 그대로. 한 번에 한 카드만 열림.
  - `MyPage.adminEntry.test.js`: 관리자면 "관리자" 보이고 누르면 `admin`으로, 아니면 안 보임, 비회원이면 서버를 안 부름.
  - `SideMenu.admin.test.js`: `isAdmin`이 참일 때만 "관리자"가 "고객센터" 다음에 보이고, 누르면 `onSelect('admin')`·메뉴 닫힘.
- 기존 테스트 전부(특히 마이페이지·옆 메뉴·고객센터 테스트) 그대로 통과, act 경고·처리 안 된 오류 0(플레이북 §6 Clean Run). 타입 검사·빌드·의존 방향 검사(`npm run depcheck`)는 CI.
- 브라우저: 보리 계정 → 마이페이지·옆 메뉴 "관리자" → 23-A 때 휴대폰으로 넣은 시험 문의가 보임 → 답변 저장 → 그 계정의 "나의 문의/건의 확인"에 답변 완료. 다른 계정 → 메뉴 없음, 주소를 직접 쳐도 홈으로.

### 파일 길이
- 새 파일: `adminRole.ts` 약 35줄, `adminInquiries.ts` 약 100줄, `AdminInquiriesPage.tsx` 약 80줄, `AdminInquiryCard.tsx` 약 100줄, `AdminEntryLink.tsx` 약 30줄 — 모두 200 이하.
- 고치는 파일: `MyPage.jsx` 197 → 199, `AppShellRoutes.jsx` 102 → 104, `lazyPages.js` 21 → 22, `AppShell.jsx` 187 → 약 191, `SideMenu.jsx` 138 → 약 145, `SideMenuIcons.jsx` 85 → 약 93, `customer-center.css` 34 → 약 42.
- 파일 수가 12개(새 5·고침 7, 테스트 4 따로)로 보통 슬라이스(1~3개)보다 많음 — 고치는 7개는 모두 몇 줄짜리 연결이고, 메뉴 없는 관리자 화면이나 화면 없는 메뉴는 쓸 수 없어 한 슬라이스로 둠.

### 코드에 넣을 주석(최종 문구)
- `adminRole.ts`: `// 이 계정이 관리자 명단(admin_users)에 있는지 — 메뉴·화면 문 표시용. 진짜 막는 건 서버 함수(0025).`
- `adminInquiries.ts`: `// 관리자 문의 목록·답변 저장 — 서버 함수(0025)로만, 로컬 저장 없음(Fail-Fast).`
- `AdminInquiriesPage.tsx`: `// 관리자 화면 — 문의 목록·답변(로드맵 23-B). 관리자가 아니면 홈으로.`
- `AdminInquiryCard.tsx`: `// 문의 카드 한 장 + 답변 쓰기·고치기 칸.`
- `AdminEntryLink.tsx`: `// 마이페이지 "관리자" 줄 — 관리자 계정에만 보인다.`

### 실패 시 처리
- 새 저장 장치·재시도·대기열 없음(AGENTS §7). 저장 실패는 알림 + 쓴 글 유지로 끝. 테스트·브라우저 확인이 기대와 다르면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 보리 계정(ya01na111@gmail.com)으로 로그인 → 마이페이지 아래 목록과 옆 메뉴(햄버거) "설정" 칸에 "고객센터" 다음 "관리자"가 보이면 성공.
2. "관리자" → 휴대폰으로 넣었던 시험 문의가 "답변 대기"로, 문의한 사람 이름과 함께 보임.
3. "답변하기" → 답 쓰고 "저장" → "답변을 저장했습니다." + 카드가 "답변 완료"로 바뀜.
4. 휴대폰(문의한 계정) → 고객센터 → "나의 문의/건의 확인"에 "답변 완료"와 답이 보임.
5. 휴대폰 계정의 마이페이지·옆 메뉴엔 "관리자"가 없음, 주소창에 `/app/admin/inquiries`를 쳐도 홈으로 감.

### 진행 기록
- 2026-10-22 보리 "착수지시서 확정,작업진행해". 교차검증: 파일 길이·메뉴 자리·`onOpen`/`PAGE_PATH`/`goBack` 흐름·`rpc` 방식·가짜 서버 도구·의존 방향 모두 지시서와 같음.
- 지시서와 다른 점 1개: `requestAdminAnswerSave` 결과에 `reload`(참이면 목록 다시 불러오기 — 성공·`P0002`) 칸을 더함. 화면이 오류 코드를 다시 알 필요 없게.
- 결과(커밋 전): 새 테스트 4개 17개 통과. `npm test` 878·680(663 + 새 17) 전부 통과, act 경고 10개는 전과 같음(새 0). 타입 검사 통과, lint 경고 50 그대로, 의존 검사 위반 0(427개 모듈), 빌드 통과(`AdminInquiriesPage` 따로 5.14 kB). 줄 수: `adminRole.ts` 36·`adminInquiries.ts` 98·`AdminInquiriesPage.tsx` 75·`AdminInquiryCard.tsx` 104·`AdminEntryLink.tsx` 17, `MyPage.jsx` 199·`AppShellRoutes.jsx` 104·`lazyPages.js` 22·`AppShell.jsx` 191·`SideMenu.jsx` 146·`SideMenuIcons.jsx` 93·`customer-center.css` 45. 브라우저 확인은 아직.
- 코드 커밋 react-app `59c97c1`(2026-10-22, 16개 파일, push 안 함). 남은 것: 보리 push → CI → 배포본 브라우저 확인(사용자 확인 방법 1~5) → §5 리뷰 → 최종 승인.
- 보리 push 뒤 CI verify·deploy 초록(run 39764776929). 배포본(getdrivelog.com, 비회원)에서 데스크톱 AI 확인: 마이페이지·옆 메뉴 "설정" 칸에 "관리자" 없음(개인정보·앱 설정·고객센터만), `/app/admin/inquiries` 직접 열면 `/app`으로 감. 관리자 쪽(사용자 확인 방법 1~4)은 보리 구글 로그인이 필요해 보리 몫.
- §5 리뷰(데스크톱 AI): ① 범위 — 16개 파일, 지시서 "건드릴 파일" 12개 + 테스트 4개와 같음, 단 **안 바꿀 줄 2개의 띄어쓰기가 빠짐**: `AppShellRoutes.jsx` `<Route path="notice"element=…`(속성 사이 빈칸 없음), `lazyPages.js` `NoticePage =lazy(`. 빌드·타입 검사는 통과(동작 같음), 모양만 깨짐 ② 몰래 증설 없음(로컬 저장·재시도 없음, `useIsAdmin`은 기억 없이 매번 확인) ③ 타입 꼼수 없음(`any`·`unknown`·`@ts-` 무시·이중 단언 0, 새 `// @ts-check`는 테스트 2개뿐) ④ 200줄 — 최대 `MyPage.jsx` 199 ⑤ 테스트 — 새 4개 17개, 기존 테스트 안 고침 ⑥ 공용 CSS — 새 `admin-inquiry-*` 클래스만 추가, 기존 클래스 안 고침 ⑦ 목표 1~7 코드 대조 맞음(지시서와 다른 점은 위 `reload` 칸 1개). 쓰이지 않는 클래스 `admin-inquiry-card` 1개(CSS 없음, 영향 없음).

### 수정 착수지시서 — 띄어쓰기 2줄 + 버튼 이름 `[x]`

보리(2026-10-22, 배포본 관리자 계정 확인 뒤): "고치고 관리자계정확인햇어, 이거 답변 수정으로 이름변경 외엔 수정할거없어". 관리자 쪽 사용자 확인 방법 1~4 보리 확인 끝. 23-B 안에서 **수정 커밋 1개 더**(AGENTS §3).

**쉬운 요약**: 이번 작업 중 실수로 빈칸이 빠진 두 줄을 원래대로 돌리고, 관리자 카드의 "답변 고치기" 버튼 이름을 "답변 수정"으로 바꿉니다. 동작은 안 바뀝니다.

**현재 상태**: `AppShellRoutes.jsx` `<Route path="notice"element=…`(속성 사이 빈칸 빠짐), `lazyPages.js` `export const NoticePage =lazy(`(`=` 뒤 빈칸 빠짐) — 둘 다 `59c97c1`에서 생김(`git show 59c97c1`). 버튼 이름은 `AdminInquiryCard.tsx:98` `{answered ? '답변 고치기' : '답변하기'}`, 테스트 `AdminInquiriesPage.test.js:116`·`:167`이 그 글자를 찾음(`src` grep "답변 고치기" 3곳이 전부).

**목표 상태**
1. 두 줄이 `59c97c1` 전 모양과 같음 — `git diff af0d443 -- 그 두 파일`에 관리자 줄 추가만 남음.
2. 답변이 있는 카드의 버튼이 "답변 수정". 답변 없는 카드는 그대로 "답변하기".
3. 테스트 2곳이 "답변 수정"을 찾고 통과.

**건드릴 파일**: `src/app/routes/AppShellRoutes.jsx`(빈칸 1개), `src/app/routes/lazyPages.js`(빈칸 1개), `src/features/support/ui/AdminInquiryCard.tsx`(글자 1곳), `src/features/support/ui/AdminInquiriesPage.test.js`(글자 2곳).

**안 건드릴 것**: 그 밖 전부(동작·서버·CSS). 쓰이지 않는 클래스 `admin-inquiry-card`는 보리가 수정할 것 없다고 해서 그대로.

**검증**: 위 목표 1의 `git diff`, `npm test` 전부 통과·경고 늘지 않음. CI.

**파일 길이**: 줄 수 변화 없음(`AppShellRoutes.jsx` 104, `lazyPages.js` 22, `AdminInquiryCard.tsx` 104).

**실패 시 처리**: 새 장치 없음. 기대와 다르면 멈추고 이 파일에 다시 써서 승인받음.

**사용자 확인 방법**: 관리자 화면에서 답변이 있는 문의의 버튼이 "답변 수정"으로 보이면 끝. 공지사항(마이페이지 → 공지사항)도 그대로 열림.

**진행 기록**: 2026-10-22 보리 "착수지시서 확정,작업진행해". 원인: 그 줄 앞에 새 줄을 넣는 편집에서 끝 빈칸이 빠짐. 고친 뒤 `git diff af0d443 -- AppShellRoutes.jsx lazyPages.js`는 관리자 줄 3개 추가만. "답변 고치기" grep 0, "답변 수정" 3곳. 4개 파일 +5 −5. `npm test` 878·680 전부 통과, 경고 수 그대로(act 10), 타입 오류 0, lint 50. 수정 커밋 react-app `02feb28`(2026-10-22, 4개 파일, push 안 함). 남은 것: 보리 push → CI → 배포본에서 "답변 수정" 확인 → 최종 승인. 보리 push 뒤 CI verify·deploy 초록(run 39767515237). 데스크톱 AI 확인: 커밋 내용이 지시서 목표 1~3과 같음(`af0d443`과 비교해 두 파일엔 관리자 줄 3개 추가만), 배포본(비회원)에서 마이페이지 → 공지사항이 열림(`/app/notice`), "관리자" 메뉴 없음.

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
