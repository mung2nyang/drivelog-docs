# docs/report.md — 현재 슬라이스 착수지시서

131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 131-B 첫 설정·거래처·기사 초대 창 [저장]을 두 번 눌러도 한 번만 `[x]`

폴더별 전수점검(2026-10-26) I2·R2·J2, 로드맵 131 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 앞 조각 131-A 차량 창 `[x]`(react-app `b87b810`). DB 변경 없음.

### 쉬운 요약
- 처음 가입 뒤 첫 설정 마지막 단계의 [완료하기](와 [건너뛰기]), 거래처 창 [저장], 기사 초대 창 [저장]도 처리 중에 막혀 있지 않습니다. 빨리 두 번 누르면 메인 차량이 두 대 생기거나(첫 설정), 같은 거래처가 두 개 생기거나, 초대 저장이 두 번 갑니다.
- 131-A와 같은 방식으로 처리 중에는 다시 눌러도 무시하고, 버튼은 "저장 중…"으로 잠급니다. 거래처 창은 쓰는 곳이 세 군데라 창 안에서 잠가 한 번에 막습니다.

### 현재 상태 (코드·서버 확인, 2026-10-28)
- 첫 설정: `features/account/ui/OnboardingPage.jsx:39-42` `goNext`가 마지막 단계면 `onFinish(wizard)`를 부르고 기다리지 않음. [완료하기](`:143`)와 [건너뛰기 >](`:141`) 둘 다 `goNext`. `app/App.jsx:125-132` `onFinish`는 `applyOnboardingWizard`(저장 실패해도 오류를 던지지 않고 안내만 돌려줌 — `data/sync/onboardingFinish.ts:36`) 뒤 홈으로. 두 번 누르면 둘 다 같은 "차량 없음" 목록으로 시작해 메인 차량 저장 요청이 두 번 감.
- 거래처 창: `features/clients/ui/ClientFormModal.jsx:41-50` `handleSave`가 `onSave()`를 기다리지 않고, [저장](`:123`)은 늘 눌림. 쓰는 곳 3군데 — `ClientListPage.jsx:116`(`save` `:79` async), `ScopedClientsList.jsx:40`(`useScopedClients.js:67` `save` async), `day-log/ui/CallClientQuickAdd.jsx:63`(`save` `:41` async)(grep).
- 기사 초대 창: `features/drivers/ui/DriverFormModal.jsx:47-56` `handleSave` 같은 모양, [저장](`:136`). 쓰는 곳 `DriverConnectionPage.jsx:179` 한 곳(`save` `:59` async).
- 서버(테스트 프로젝트, 읽기 조회 2026-10-28): `clients`는 고유 번호(`id`) 말고 겹침 막기 없음 → 거래처 두 개가 됨. `driver_links`는 초대 코드가 겹치지 못함(`driver_links_invite_code_key`) → 같은 코드 두 번째 저장은 서버에서 실패. `vehicles`도 같은 번호 막기 없음(131-A에서 확인) → 첫 설정 두 번이면 메인 차량 두 대.

### 목표 상태 (기대 동작)
1. 첫 설정 마지막 단계에서 [완료하기]를 처리 중에 또 누르거나 [건너뛰기]를 눌러도 저장은 한 번만 한다. 처리 중엔 [완료하기]가 잠기고 "저장 중…", [건너뛰기]도 잠긴다. 끝나면 지금처럼 홈으로 간다.
2. 거래처 창 [저장]을 처리 중에 또 눌러도 저장은 한 번. 처리 중엔 버튼이 잠기고 "저장 중…". 세 곳(거래처 목록·기사 차량 전용 거래처·콜 상세 "+ 추가") 모두 같다.
3. 기사 초대 창 [저장]도 같다.
4. 거래처·초대 저장이 실패해 창이 남으면 잠금이 풀려 다시 누를 수 있다.
5. 입력 검사(빈 칸 빨간 표시·커서), 첫 설정 앞 단계의 [다음]·[건너뛰기]는 지금과 같다.

### 건드릴 파일
- `react-app/src/features/account/ui/OnboardingPage.jsx` — 마지막 단계에서 `onFinish`를 기다리며 진행 표시(`useRef`)로 다시 누름을 무시, `finishing` 상태로 [완료하기] `disabled`·글자 `저장 중…`, [건너뛰기] `disabled`. 주석 최종 문구: `// 마지막 단계 저장 중엔 다시 눌러도 무시 — 메인 차량이 두 번 등록되지 않게.`
- `react-app/src/features/clients/ui/ClientFormModal.jsx` — 검사를 통과하면 `onSave()`를 기다리는 동안 다시 누름 무시, [저장] `disabled`·`저장 중…`. `onSave` 형 설명을 `() => void|Promise<void>`로. 주석 최종 문구: `// 저장 중엔 다시 눌러도 무시 — 같은 거래처가 두 번 생기지 않게.`
- `react-app/src/features/drivers/ui/DriverFormModal.jsx` — 같은 방식. `onSave` 형 설명 같이. 주석 최종 문구: `// 저장 중엔 다시 눌러도 무시 — 같은 초대가 두 번 가지 않게.`
- `react-app/src/features/clients/ui/ClientFormModal.test.js`·`react-app/src/features/drivers/ui/DriverFormModal.test.js` — 각각 추가: 풀어 주기 전까지 안 끝나는 `onSave`로 [저장] 두 번 → 1번만 불림·"저장 중…" 잠김(기대 2·3), 실패처럼 끝나면 잠금 풀림·다시 누르면 1번 더(기대 4).
- 새 `react-app/src/features/account/ui/OnboardingPage.saveOnce.test.js` — 마지막 단계까지 간 뒤 안 끝나는 `onFinish`로 [완료하기] 두 번 + [건너뛰기] → `onFinish` 1번·[완료하기] "저장 중…" 잠김(기대 1).
- 공용 값 영향: 세 파일 모두 창 안의 새 상태라 밖에서 읽는 곳 없음. 받는 값(`onSave`·`onFinish`)의 형 설명만 넓힘 — 넘기는 곳은 모두 async 함수(위 grep). 스타일 변경 없음.

### 안 건드릴 것
- `DriverConnectionPage.jsx`의 [해제 요청]·[요청 취소]·[동의]·[초대 취소](J2 끝 문장, 낮음) — 이번엔 [저장]만. 그 파일은 194줄이라 손대면 200줄을 넘길 수 있음.
- 서버에 같은 번호 차량·같은 이름 거래처 막기 — DB 변경이 필요한 다른 작업. 같은 이름 거래처 등록 막기는 로드맵 140(R1)에서.
- `App.jsx` `onFinish`·`applyOnboardingWizard` — 그대로(창 쪽에서 막음).
- 거래처·초대·첫 설정 저장 순서와 안내 문구 — 그대로.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(§2·§6).
- 새 테스트가 고치기 전 코드에서 실패하는지 먼저 확인(두 번 불림), 고친 뒤 통과. 기존 `ClientFormModal.test.js`·`DriverFormModal.test.js`·`ClientListPage.assistant.test.js`·`DriverConnectionPage.*.test.js`·`App.*.test.js` 그대로 통과. 로컬 `npm test` 전체 통과. 타입·빌드는 CI.

### 파일 길이
- `features/account/ui/OnboardingPage.jsx` 150 → 약 160
- `features/clients/ui/ClientFormModal.jsx` 127 → 약 136
- `features/drivers/ui/DriverFormModal.jsx` 140 → 약 149
- 모두 200줄 아래. 테스트 파일은 예외.

### 실패 시 처리
- 새 저장 장치·재시도·임시 보관 없음. 실패 안내는 지금 문구 그대로, 잠금만 풂.
- 테스트나 배포본 확인이 기대와 다르면 코드를 더 고치지 않고 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법 (배포본, 로그인 계정)
1. 거래처 관리 → [+ 추가] → 시험 거래처 이름 입력 → [저장]을 빠르게 두 번 → 목록에 한 개만 있고, 새로고침해도 한 개면 성공. 누르는 순간 "저장 중…"으로 바뀌는지도 봄. 확인 뒤 시험 거래처 지움.
2. 기사 관리 → 초대 → 시험 기사 입력 → [저장]을 빠르게 두 번 → 초대가 한 건만 있고 오류 안내가 없으면 성공. 확인 뒤 시험 초대 지움.
3. 첫 설정은 새 계정이 있어야 해서 배포본에서는 생략하고 자동 테스트로만 확인(보리가 새 계정으로 보려면: 첫 설정 마지막 단계 [완료하기]를 빠르게 두 번 → 차량 관리에 메인 차량 한 대).

### 진행 기록
- 2026-10-28 착수 승인(보리 "확정, 진행해"). 플레이북 §2·§6 열람.
- 새 테스트를 고치기 전 코드에서 실행(종료 코드 1). 두 번 눌림 3개 실패, "끝나면 잠금 풀림" 2개는 원래도 통과(잠금이 풀리는지 지키는 확인):
  ```
  ✖ 마지막 단계에서 [완료하기] 두 번·[건너뛰기]를 눌러도 저장은 1번, [완료하기]는 "저장 중…"으로 잠김 (47.9607ms)   actual: 3  expected: 1
  ✖ (거래처 창) 저장 중에 [저장]을 또 눌러도 저장은 1번, 버튼은 "저장 중…"으로 잠김 (9.1825ms)                    actual: 2  expected: 1
  ✔ (거래처 창) 저장이 끝나고 창이 남으면 잠금이 풀려 다시 누르면 1번 더 (11.7825ms)
  ✖ (기사 초대 창) 저장 중에 [저장]을 또 눌러도 저장은 1번, 버튼은 "저장 중…"으로 잠김 (12.7812ms)                 actual: 2  expected: 1
  ✔ (기사 초대 창) 저장이 끝나고 창이 남으면 잠금이 풀려 다시 누르면 1번 더 (7.0589ms)
  ℹ tests 13 / pass 10 / fail 3 / duration_ms 1494.1225 (기존 테스트 8개 포함)
  ```
- 고친 뒤: 13개 통과, React act 경고 없음. 로컬 `npm test` 전체 통과(927 + 918), 타입 검사 통과.
- 코드 커밋 react-app `fc18319`(push 전). 파일 길이: `OnboardingPage.jsx` 160, `ClientFormModal.jsx` 134, `DriverFormModal.jsx` 147.
- 리뷰 점검: 바뀐 파일이 지시서 "건드릴 파일"과 같음, 기존 테스트 줄 지우거나 약화 없음(추가만), 새 저장 장치 없음, 타입 꼼수 없음, 주석은 지시서 최종 문구 그대로.
- push(보리) 뒤 CI verify·deploy 초록(run 40644898796).
- 배포본 확인(로그인 탭, getdrivelog.com):
  1. 거래처: "시험131B" 넣고 [저장] 빠르게 두 번 → 버튼 "저장 중…"·잠김 확인, 서버에 1개만 생김(전체 3 → 4), 새로고침 뒤에도 1개. 삭제 뒤 3개로 원래대로.
  2. 기사 초대: "시험131B"(99가9998, 시작일 10-28) 넣고 [저장] 빠르게 두 번 → "저장 중…"·잠김 확인, 오류 없이 초대 1개만 생김(전체 1 → 2). [초대 취소] 뒤 1개로 원래대로.
  3. 첫 설정 마지막 단계는 자동 테스트로만 확인(지시서대로).
- 2026-10-28 보리 최종 승인.

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
