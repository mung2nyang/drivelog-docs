# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 55-B — 앱에서 난 오류를 우리 Supabase에 모은다 `[x]`

> **진행(2026-10-14):** 착수 승인, SQL은 보리 지시로 AI 실행. 0021 실행 성공(경고는 `drop policy if exists`·`revoke` 때문, 데이터 변경 없음). 사후검증: RLS 켜짐, 정책 1개(로그인 사용자 넣기만 `user_id = auth.uid()`), 권한 authenticated INSERT만·anon 없음.
> 앱: 새 `lib/errorReport.js` 61줄, `PageLoadErrorBoundary.jsx` 41→43, `main.jsx` 21→23. 테스트 새 8개(`errorReport.test.js` 7, `PageLoadErrorBoundary.test.js` 1 — 개발 모드 React가 오류 처리를 두 번 부를 수 있어 "1번 이상·모두 같은 문구"로 단언, 같은 문구 거르기는 `errorReport` 테스트가 확인). 테스트 파일에서 `object` 경유 이중 형 변환을 쓰지 않으려고 `installErrorReporting` 인자 형을 작게 정함. `npm test` 872+379 통과, `typecheck` 통과.
> 실제 확인: 개발 서버(테스트 계정 로그인)에서 시험 기록 1건 → `client_errors`에 1줄(id 1, user 7ba50d27, error, "55-B 시험 기록", /app). 보리 답: **시험 줄은 그대로 둔다.**
> 코드 커밋 react-app `0e739c8`, 보리 push → CI 초록(verify·deploy) → §5 리뷰 → 보리 최종 승인(2026-10-14).
>
> **보리 답(2026-10-14):** 1. **로그인 사용자만** 모은다(비회원 안 보냄, 서버도 로그인 사용자 자기 id로만 넣기). 2. 처리방침 메모 → 로드맵 출시 순서 3번(10-P ⓑ)에 "앱 오류 기록" 추가함.

55번(`7faa889`)에 이어서. 보리 결정(2026-10-14): **오류 수집은 지금 넣는다, 우리 Supabase에 모은다**(바깥 서비스 안 씀). 55번 착수지시서 질문 1에 대한 답이 바뀐 것.

### 쉬운 요약
- **무엇을:** 앱에서 오류가 나면(화면 그리다 오류, 버튼 처리 중 오류 등) 오류 내용을 서버의 새 "오류 기록" 표에 한 줄 남긴다. 보리는 Supabase 화면에서 표로 본다.
- **왜:** 지금은 사용자 휴대폰에서 오류가 나도 우리가 알 길이 없다(콘솔에만 찍힘).
- **사용자에게는:** 아무것도 안 보인다. 기록이 실패해도 앱 동작엔 영향이 없다.

### 현재 상태 (코드 확인)
- 오류를 모으는 곳 없음. 화면 오류 안내(`PageLoadErrorBoundary.jsx:23`)는 `console.error`만, 앱 전체 오류(`window error`·`unhandledrejection`)는 받는 곳 없음(검색 0건).
- 서버 연결: `src/supabaseClient.js:10` `supabase`. 로그인 사용자 id: `lib/cloudSession.js:32` `getCloudUserId()`.
- 마지막 DB 기록 파일 `0020`. 앱 버전 표시값 없음(`package.json` "0.0.0").

### 목표 상태 (기대 동작)
1. **서버(DB, §9 절차):** 새 표 `client_errors` — 칸: 번호, 시각, 사용자 id(로그인 사용자 본인), 종류(`render`/`error`/`unhandledrejection`), 오류 문구(최대 500자), 오류 위치(stack, 최대 2,000자), 화면 주소(경로만, `?` 뒤는 안 남김), 기기 정보(user agent, 최대 200자).
   - RLS: **로그인 사용자만, 자기 id로만 넣기** 허용. **읽기·고치기·지우기는 아무도 못 함**(보리는 Supabase 관리 화면에서 봄).
   - 칸 길이 제한을 서버도 검사(너무 긴 값 거절).
2. **앱:** 새 `lib/errorReport.js` `reportClientError({ kind, message, stack })` — 로그인 상태면 표에 한 줄 넣고, **결과를 기다리지도 다시 시도하지도 않는다**(실패는 조용히 무시). 비로그인·비회원이면 안 보낸다(질문 1).
3. 한 번 켠 앱에서 **최대 10건**까지만 보내고, 같은 문구는 한 번만 보낸다(오류가 반복돼도 표가 넘치지 않게).
4. 보내는 곳: 화면 오류 안내 2곳(`PageLoadErrorBoundary`의 오류 처리, 55번 바깥·안쪽 모두 같은 부품), 앱 전체 `window error`·`unhandledrejection`(`main.jsx`에서 한 번 등록).
5. 오류 기록 자체에서 오류가 나도 다시 기록하지 않는다(무한 반복 방지).
6. 개인정보: 입력값·주소의 `?` 뒤는 보내지 않는다. 다만 오류 문구에 우연히 값이 섞일 수 있음 → 처리방침(10-P)에 "앱 오류 기록(문구·화면 주소·기기 정보), 보관 기간" 항목을 넣도록 로드맵 10-P 메모에 추가.

### 건드릴 파일
- **서버:** `react-app/supabase/migrations/0021_client_errors.sql` — 표·칸 길이 검사·RLS(넣기만). 보리 지시로 AI가 SQL Editor에서 실행 또는 보리 실행, 사후검증(표·정책 조회 + 시험 한 줄 넣기 → 확인 후 그 한 줄은 남김 여부 보리 결정).
- **새 파일** `react-app/src/lib/errorReport.js`(약 50줄) — 보내기·10건 제한·같은 문구 한 번·경로만·길이 자르기.
- `react-app/src/components/PageLoadErrorBoundary.jsx`(41) — 오류 처리에서 `reportClientError` 1줄.
- `react-app/src/main.jsx`(21) — 전체 오류 받기 등록 1~2줄.
- 테스트: `errorReport.test.js`(로그인 때만 보냄·10건 제한·같은 문구 한 번·주소 `?` 제거·길이 자르기·보내기 실패해도 조용함), `PageLoadErrorBoundary.test.js`에 "오류 나면 보내기 1번" 1개.
- **테스트 영향 미리 찾음:** `PageLoadErrorBoundary`를 그리는 테스트(`PageLoadErrorBoundary.test.js`, 55번 `App.errorBoundary.test.js`)는 오류 때 보내기가 불리는데, 비로그인이라 안 보냄 → 결과 같음. 가짜 서버(`fakeSupabaseClient`)에 `client_errors` 처리기가 없어 불리면 오류가 날 수 있으니 테스트에서 확인. 바뀌어야 하면 멈추고 수정 착수지시서.

### 안 건드릴 것
- 화면 안내 모양·문구 — 그대로.
- 바깥 오류 서비스(Sentry 등) — 안 씀(보리 결정).
- 오류를 앱 안에서 보여 주는 화면 — 없음(보리는 Supabase 화면에서 봄).
- 오래된 기록 자동 지우기 — 이번엔 없음(필요하면 나중에, 보관 기간은 처리방침과 함께 정함).
- 플레이북: 새 저장이지만 **재시도·대기열·기기 저장 없음**(보내고 끝) → §7 "새 레이어"가 아니라 일회성 기록. 그래도 저장 경로라 열람.

### 검증
- 서버 사후검증(위).
- `npm test` 전체.
- 화면: 개발 서버(실제 계정 로그인 상태)에서 일부러 오류를 내긴 어려움 → 브라우저 콘솔에서 `reportClientError`를 직접 한 번 불러 표에 한 줄 들어가는지 Supabase에서 확인(시험 줄은 보리 결정대로 남기거나 지움).

### 파일 길이
- `errorReport.js` 새 약 50, `PageLoadErrorBoundary.jsx` 41→약 43, `main.jsx` 21→약 23, `0021` 새 약 40(SQL)
- 테스트 파일은 예외

### 실패 시 처리
- 기록 실패는 조용히 무시(재시도·대기열·기기 저장 만들지 않음). SQL·테스트·확인 결과가 목표와 다르면 멈추고 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법
1. Supabase → Table Editor → `client_errors` 표에서 시험 한 줄이 보이면 성공.
2. 앞으로 오류가 나면 이 표에 쌓인다(사용자 화면엔 변화 없음).

### 확인할 점 (보리 답 필요)
1. **비회원(로그인 안 한 사람) 오류도 모을까요?** 모으려면 "아무나 넣기"를 열어야 해서, 앱 주소만 알면 누구나 표에 글을 넣을 수 있어요(장난 기록 위험). **로그인 사용자만 모으는 걸 추천**해요.
2. 처리방침(10-P)에 "앱 오류 기록" 항목을 넣도록 로드맵 메모에 추가해도 될까요?
