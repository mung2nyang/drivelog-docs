# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 99-C 고객센터 화면 나누기 — 안에 든 부품 2개를 각자 파일로 `[x]`

> **결정 `[확인: 2026-10-21 보리 — "권하는대로 테스트 2개 추가하는 걸로"]`**: 확인할 점 1 권함대로 — 옮기기 전 테스트 2개(문의 접수 성공 → 알림·칸 비우기, 목록 불러오기 실패 → 알림·빈 목록 안내)를 `CustomerCenterPage.test.js`에 더해 옛 코드에서 먼저 통과시킨 뒤 옮김. 승인된 설계의 "테스트 추가 없음"은 이 결정으로 바뀜.
> 다음: 터미널 AI가 교차검증 뒤 99-C 착수.

> **99-C 진행(2026-10-21) — 코드 커밋 `71f0ca6`** `[x]`: 보리 "착수지시서확정,작업진행해".
> - 교차검증(AGENTS §1): 229줄·부품 4개 줄 위치·상수 3개 쓰는 곳·`AppSession` 안 쓰임·부르는 곳 1곳·테스트 7개 모두 지시서와 같음. 고친 것 없음.
> - 테스트 먼저: `CustomerCenterPage.test.js`에 새 묶음 "문의 접수·목록 실패 알림" 2개 — ① 로그인 상태에서 제목·내용을 넣고 접수 → 서버 넣기 1번, 알림 `문의가 접수되었습니다.`, 두 칸 비워짐 ② 목록 조회 오류 → 알림 `문의 목록을 불러오지 못했습니다.`, 빈 목록 안내, 카드 0. **옛 코드에서 9개 모두 통과** → 옮긴 뒤에도 9개 통과. 기존 7개는 안 고침(바뀐 줄은 가져오기 2줄뿐 — `withToast` 추가, `stubSupabaseCallCounts` 함께 가져옴). 테스트 파일 192 → 258줄(§6 예외).
> - 한 일: 새 `InquiryForm.jsx`(64줄 — 머리 주석 1줄, `INQUIRY_TYPES`), 새 `MyInquiriesList.jsx`(67줄 — 머리 주석 1줄, `SupportInquiryRow` 타입 설명, `FETCH_FAIL_TOAST`). `CustomerCenterPage.jsx` 229 → **109줄**: `GuestLoginPrompt`·`FAQ_ITEMS`·탭 화면만, 머리 주석 "고객센터 탭 화면(FAQ는 여기, 문의 작성은 InquiryForm, 내 문의는 MyInquiriesList).", 안 쓰이게 된 가져오기와 `AppSession` 타입 설명 지움.
> - 줄 대조(목표 4): 옮긴 두 부품 본문에서 다른 줄은 `function InquiryForm` → `export default function InquiryForm`, `function MyInquiriesList` → `export default function MyInquiriesList` 두 줄뿐. 화면 파일에 새로 생긴 줄은 머리 주석·`useState`만 가져오기·두 부품 가져오기뿐.
> - 결과: `npm test` unit 866 + app 651(2개 늘어남) = 1517 전부 통과, 타입 검사·빌드·`npm run depcheck`(위반 0) 통과, lint 경고 50 그대로.
> - 개발 서버(비회원): 고객센터 FAQ 첫 항목 펼침(−, 답 보임), 1:1 문의/건의·나의 문의/건의 확인 탭 모두 "로그인 후 이용해 주세요" 안내. 콘솔 오류 0건. 로그인 상태의 작성 칸·목록은 배포본에서(데스크톱 AI).
> - 코드 커밋 react-app `71f0ca6`(push 안 함).
> - push 뒤 CI verify·deploy 초록(run 39537571869) — 테스트·타입 검사·빌드·의존 방향 검사 모두 성공.
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서의 4개 파일 ②새 층·저장 키 0, 새 파일 2개는 지시서대로 ③타입 꼼수 0 ④`CustomerCenterPage` 109·`InquiryForm` 64·`MyInquiriesList` 67, 테스트 258(§6 예외) ⑤새 테스트 2개는 서버 넣기 호출 수·알림 문구·칸 값·카드 수를 직접 확인(거짓 통과 없음), 기존 7개는 가져오기 2줄만 바뀜 ⑥공용 영향 없음(부르는 곳 `AppShellRoutes` 1곳 그대로) ⑦목표 1~5 대조. 옛 파일과 새 세 파일을 줄 단위로 대조 — 옛 파일에 없던 줄은 머리 주석 3줄, 가져오기 줄, `export default`가 붙은 두 줄뿐.
> - 배포본 로그인 확인(데스크톱 AI, 새 빌드 `index-0FciVk4h.js`): FAQ 첫 항목 펼침(−, 답 보임), 1:1 문의/건의 탭에 문의 유형·제목·내용 칸이 보임(접수는 안 함), 나의 문의/건의 확인 탭에 "아직 접수한 문의나 건의가 없습니다."(이 계정은 문의 0건 — 답변 표시 카드는 테스트로만 확인). 콘솔 오류 0건.
> - 보리 최종 승인(2026-10-21). 다음 99-B 서류.

보리 지시 "작성해"(2026-10-21). 97·99 분리설계 `[확인: 2026-10-21 보리 — "권하는대로 진행하는걸로"]`(설계 원문은 `git show 1e9e6e2:docs/report.md`의 "분리설계" 절)의 두 번째 슬라이스. 순서 97 → **99-C** → 99-B → 99-A.

### 쉬운 요약
- 고객센터 화면 파일(229줄) 안에 들어 있던 "문의 작성 칸"과 "내 문의 목록"을 각자 파일로 옮깁니다. 내용은 한 글자도 안 바꿉니다.
- 화면 파일은 약 115줄이 됩니다. 화면 모양과 동작은 그대로입니다.

### 현재 상태 (코드 확인 — `features/support/ui/CustomerCenterPage.jsx` 229줄, 마지막 변경 `26abaa1`)
- 한 파일 안에 부품 4개: `GuestLoginPrompt`(44~51줄, 로그인 안내), `InquiryForm`(57~106줄, 문의 작성 — `requestSupportInquirySave`·`AppDropdown`·`ClearableInput`·`useToast`), `MyInquiriesList`(112~163줄, 내 문의 목록 — `fetchMyInquiries`·`SkeletonLines`·`useToast`), 탭 화면 `CustomerCenterPage`(171~229줄).
- 상수: `FAQ_ITEMS`(화면만), `INQUIRY_TYPES`(문의 작성만), `FETCH_FAIL_TOAST`(내 문의 목록만). 타입 설명 `AppSession`(15줄)은 파일 안에서 안 쓰임, `SupportInquiryRow`는 내 문의 목록만.
- 머리 주석(2줄): "하위 패널을 파일 분리하면 같이 읽어야 해서 응집 유지(§6)" — 이번에 뒤집는 판단.
- 이 화면을 부르는 곳은 `app/routes/AppShellRoutes.jsx` 1곳(바뀌지 않음).
- 테스트 `CustomerCenterPage.test.js` 7개(단언 28): FAQ 기본·게스트면 작성 칸 대신 안내·로그인이면 작성 칸·옆 메뉴·게스트 목록 안내·빈 목록·답변 대기/완료 표시. **문의 접수(성공하면 칸 비우기·알림)와 목록 불러오기 실패 알림은 테스트 없음**(grep `requestSupportInquirySave`·`문의 목록을 불러오지` 0건) — 확인할 점 1.

### 목표 상태 (기대 동작)
1. 새 `features/support/ui/InquiryForm.jsx`: `InquiryForm` + `INQUIRY_TYPES`를 그대로 옮김(`export default`). 필요한 가져오기만.
2. 새 `features/support/ui/MyInquiriesList.jsx`: `MyInquiriesList` + `FETCH_FAIL_TOAST` + `SupportInquiryRow` 타입 설명을 그대로 옮김(`export default`).
3. `CustomerCenterPage.jsx`에는 `GuestLoginPrompt`·`FAQ_ITEMS`·탭 화면만 남고 두 부품을 가져와 씀. 안 쓰이게 된 가져오기와 안 쓰이던 `AppSession` 타입 설명은 지움. 머리 주석은 "고객센터 탭 화면(FAQ는 여기, 문의 작성·내 문의는 각자 파일)" 정도 1줄로.
4. 옮긴 두 부품의 본문 줄은 옛 줄과 같음(다른 줄은 가져오기·`export default` 붙은 줄뿐).
5. 화면 모양·동작·디자인 파일 변화 0. 기존 테스트 7개는 고치지 않고 그대로 통과.

### 건드릴 파일
- `features/support/ui/CustomerCenterPage.jsx`(줄어듦)
- 새 `features/support/ui/InquiryForm.jsx`, 새 `features/support/ui/MyInquiriesList.jsx`
- `features/support/ui/CustomerCenterPage.test.js`에 테스트 2개 추가(확인할 점 1 결정)

### 안 건드릴 것
- `features/support/api/supportInquiryMutations.js`, `customer-center.css`, `NoticePage`, 라우트, 99-B·99-A 화면.

### 검증
- 새 테스트 2개를 **옮기기 전 옛 코드에서 먼저 통과** 확인 → 옮긴 뒤에도 그대로 통과.
- `npm test`·타입 검사·빌드·`npm run depcheck` 통과, lint 경고 수 그대로. 기존 7개 테스트의 단언 수 28 그대로.
- 옛 파일과 새 두 파일을 줄 단위로 대조해 본문이 같은지(진행 기록에 다른 줄 목록).
- 배포본 로그인 확인(데스크톱 AI): 고객센터 FAQ 펼치기, 1:1 문의 탭에 작성 칸이 보이는지(접수는 안 함), 나의 문의 탭 목록·답변 상태 표시가 전과 같은지.

### 파일 길이
- `CustomerCenterPage.jsx` 약 115, `InquiryForm.jsx` 약 65, `MyInquiriesList.jsx` 약 70. 테스트 파일은 §6 예외(지금 192줄 + 약 45).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(코드 자리만 옮김).
- 새 테스트가 옛 코드에서 실패하면 화면을 고치지 말고 멈추고, 기대와 다른 동작을 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법
- 화면은 바뀌지 않음. push 뒤 AI가 CI와 배포본 고객센터 세 탭을 확인해 보고.

### 확인할 점
1. **옮기기 전 테스트 2개를 더할까요?** 승인된 설계에서는 "기존 테스트 7개로 충분, 추가 없음"으로 적었는데, 다시 보니 7개는 두 부품이 **보이는지**만 확인하고, 옮기는 코드 중 동작 부분 두 곳은 테스트가 없습니다. **권함: 2개 추가** — ① 로그인 상태에서 문의를 접수하면 성공 알림이 뜨고 제목·내용 칸이 비워짐 ② 내 문의 목록을 못 불러오면 "문의 목록을 불러오지 못했습니다." 알림과 빈 목록 안내. 97처럼 옛 코드에서 먼저 통과시킨 뒤 옮깁니다. 다른 안: 설계대로 추가 없이 줄 대조만.
