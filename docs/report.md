# docs/report.md — 현재 슬라이스 착수지시서

135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 135-B 문자 문구를 계정 설정에서 읽고 쓰기 `[x]`

전수점검(2026-10-26) K3, sot 4-21 `[확인: 2026-10-26 보리 — "서버에 둬"]`. 135-A(react-app `2979833`)가 만든 계정 설정 칸 `messageTemplates`·`reportShareMessage`를 문구 화면들이 쓰게 바꿈. DB 변경 없음(`profiles.settings` JSON 안). 135의 두 조각 중 B, 이번이 마지막.

### 쉬운 요약
- 문자 문구 설정 화면에서 [저장]하면 휴대폰이 아니라 계정 설정에 저장됩니다. 로그인 회원은 서버에 남아 다른 휴대폰이나 앱 데이터 삭제 뒤에도 그대로입니다.
- 일지 문자 창·내역서 공유·운비서 미수금 문자가 모두 이 계정 설정의 문구를 읽습니다.
- 비회원은 다른 설정과 같이 휴대폰의 앱 설정에 저장됩니다(지금 다른 설정과 같은 길).

### 현재 상태 (코드 확인)
- 읽기·쓰기: `data/sync/messageTemplates.ts:14-47` — 휴대폰 저장소 키 `messageTemplateCustomBodies`·`reportShareMessagePattern`만 읽고 씀. 계정 설정 칸은 135-A 뒤 아무도 읽지 않음(`grep messageTemplates` — 운영 코드는 `financeTypes`·`practiceSettings`·`persistDomainSchema`뿐).
- 쓰는 곳(같은 grep): 설정 화면 `features/settings/ui/MessageSettingsPage.jsx:22-26`(읽기)·`:46`(저장)·`:56`(기본 복원), 일지 문자 창 `features/day-log/ui/MessageTemplateSheet.jsx:23`, 내역서 공유 `features/documents/ui/ReportShareModal.jsx:43`·`:48`, 운비서 문자 `features/home/api/unbiseoSms.ts:87`(`smsBody`, 부르는 곳은 `features/home/ui/UnbiseoSmsSteps.tsx:124` 하나). `unbiseoChoice.ts`·`UnbiseoSmsSteps.tsx`의 `MESSAGE_TEMPLATE_TITLES`는 이름표라 그대로.
- 계정 설정 저장 길: `data/sync/settingsSave.ts:21` `savePracticeSettings` — 계정마다 줄 세워 한 번씩, 로그인이면 서버 `profiles` 저장 성공 뒤에만 앱에 반영, 비회원은 앱 설정에 저장. 세션 바뀜 검사도 이 안에 있음(`:36-40`). 연동 기사는 자기 `profiles` 줄의 설정에 저장(`:39` `ownFieldsOnly`).
- 화면 읽기 방식: 설정 화면들은 `useSession()` → `useOwnerSettings(ownerKey)`(예: `AppSettingsPage.jsx:27-30`).

### 목표 상태 (기대 동작)
1. `messageTemplates.ts`의 읽기 함수 둘이 계정 설정을 받아 돌려줌 — 설정에 문구가 있으면 그것, 없으면 기본 문구. 휴대폰 저장소는 읽지도 쓰지도 않음. `saveMessageTemplateSettings`·`resetMessageTemplateSettings`는 지움.
2. 설정 화면 첫 값은 계정 설정의 문구(없으면 기본).
3. [저장] → `savePracticeSettings(ownerKey, { messageTemplates, reportShareMessage })`. 성공하면 "문자 문구를 저장했습니다.", 실패하면 공용 실패 문구 `SERVER_SAVE_FAIL`(설정 화면과 같음), 입력한 글은 그대로 둠. 빈 칸 막기는 지금 그대로.
4. [기본 문구로 복원] → 두 칸을 비운 설정을 저장(`{ messageTemplates: undefined, reportShareMessage: undefined }`, `normalizeSettings`가 칸을 만들지 않음 — 135-A 테스트). 성공해야 입력 칸을 기본 문구로 바꾸고 "기본 문구로 복원했습니다.", 실패하면 칸 그대로 + 공용 실패 문구.
5. 일지 문자 창·내역서 공유(카카오톡 글·문자 글)·운비서 문자 글이 계정 설정의 문구를 씀. 화면 모양·순서·자리 채우기(`{거래처}` 등)는 그대로.
6. 다른 앱 설정을 바꿔 저장해도 문구는 남음(135-A 목표 4, 테스트 있음).
7. 휴대폰에 남아 있는 옛 문구 키(`messageTemplateCustomBodies`·`reportShareMessagePattern`)는 읽지 않고 버림 — 옮기기·지우기 코드 없음. 바꿔 둔 사용자는 한 번 다시 입력 `[확인: 2026-10-30 보리 — "가: 버림"]`.

### 건드릴 파일
- `src/data/sync/messageTemplates.ts` — 읽기 함수가 `FinanceSettings`를 받음, 휴대폰 저장소 쓰기·지우기 함수 지움. 주석 최종 문구: `/** 계정 설정의 문구 1~3, 바꾼 문구가 없으면 기본. */`, `/** 계정 설정의 내역서 공유 문구, 없으면 기본. */`
- `src/features/settings/ui/MessageSettingsPage.jsx` — `useSession`·`useOwnerSettings`로 읽고 `savePracticeSettings`로 저장·복원.
- `src/features/day-log/ui/MessageTemplateSheet.jsx`, `src/features/documents/ui/ReportShareModal.jsx` — 안에서 `useSession`·`useOwnerSettings`로 설정을 읽어 넘김(부르는 `DayLogPage.jsx`·`ReportPage.jsx`는 안 건드림).
- `src/features/home/api/unbiseoSms.ts` — `smsBody` 첫 인자로 `ownerKey`(같은 파일 `readSmsItems`·`smsPhone`과 같은 모양), `readOwnerSettings(ownerKey)`에서 문구. 주석 `:85`를 `/** 계정 설정의 문구(바꾼 문구가 없으면 기본)를 일지 문자 창과 같은 함수로 채운다. */`로.
- `src/features/home/ui/UnbiseoSmsSteps.tsx` — `:124` 한 줄(`smsBody(ownerKey, …)`).
- 테스트: `MessageSettingsPage.test.js`(저장·복원이 계정 설정에, 실패 때 글 그대로), `MessageTemplateSheet.test.js`·`ReportShareModal.test.js`·`unbiseoSms.test.js`(휴대폰 저장소 대신 계정 설정에 문구를 넣고 반영 확인).

### 안 건드릴 것
- `settingsSave.ts`·`profileCloudCommit.ts`·`practiceSettings.ts`·`persistDomainSchema.ts`·`financeTypes.ts` — 저장 길과 칸은 135-A에서 끝남(`settingsSave.serial.test.js`·`practiceSettings.test.js`·`persistDomainSchema.messageTemplates.test.js`).
- `messageTemplates.ts`를 `domain/`으로 옮기기 — 부르는 곳 6곳 경로가 바뀌어 범위 밖. 문구 화면 모양·문구 자체.
- DB·서버 함수.

### 검증
- `npm test`, 타입 검사, 빌드(CI).
- 새·바뀐 테스트는 고치기 전 코드에서 실패하는지 확인(revert-check).
- 배포본: 아래 사용자 확인 방법.

### 진행 기록
- 2026-10-30 보리 "확정, 진행" → 데스크톱 AI가 코드. react-app `b5a1468`(push 안 함).
- 지시서와 다른 점: 테스트 파일 2개 더 고침 — `data/sync/messageTemplates.test.js`(휴대폰 저장소 기준이던 모듈 테스트를 계정 설정 기준으로, 지시서 목록에서 빠짐), `features/home/ui/UnbiseoSmsSteps.test.js`(옛 키 지우던 한 줄만 뺌).
- 로컬: 테스트 937·922 모두 통과, 타입 검사·코드 검사·빌드 통과. 고치기 전 실패 확인 — 새·바뀐 테스트 8개 모두 고치기 전 코드에서 실패, 나머지는 안전 확인.
- 브라우저(로컬 5174, 비회원): 미수금 문구에 "시험" 붙여 [저장] → 새로고침 뒤 남음, 앱 설정 `messageTemplates`에 저장·옛 키 없음 → [기본 문구로 복원] → 새로고침 뒤 기본 문구·두 칸 없음, 콘솔 오류 없음. 로그인 계정은 자동 테스트로만(배포본은 push 뒤 확인).
- push(보리) 뒤 CI verify·deploy 초록(run 40994197189).
- 배포본(getdrivelog.com, 로그인 계정 — 보리 지시로 데스크톱 AI): 미수금 문구에 "시험" 붙여 [저장] → 새로고침 뒤 남음(휴대폰 설정에는 문구 없음, 서버 profiles에서 받음) → 10-13 일지 문자 창 미수금 안내 끝에 "시험" → [기본 문구로 복원] → 새로고침 뒤 기본 문구(원래대로). 콘솔 404는 주소 직접 열기 때 페이지 주소뿐(호스팅 동작, 이번 변경과 무관).

### 파일 길이
- `messageTemplates.ts` 58 → 약 50, `MessageSettingsPage.jsx` 120 → 약 130, `MessageTemplateSheet.jsx` 59 → 약 62, `ReportShareModal.jsx` 53 → 약 57, `unbiseoSms.ts` 98 → 약 99, `UnbiseoSmsSteps.tsx` 154 → 154. 모두 200줄 아래.

### 실패 시 처리
- 새 저장 장치·재시도·임시 보관 없음. 저장 실패는 알림만, 입력은 화면에 남아 다시 [저장]. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법 (배포본, 로그인 계정)
1. 마이페이지 → 문자 문구 설정 → 미수금 안내 문구 끝에 "시험" 붙여 [저장] → "문자 문구를 저장했습니다."
2. 새로고침 → 다시 들어가 "시험"이 남아 있음. (다른 휴대폰·브라우저에서 같은 계정이면 거기서도 보임)
3. 운행일지 콜 상세의 문자 보내기 → 미수금 안내 글 끝에 "시험".
4. [기본 문구로 복원] → 기본 문구로 바뀌고, 새로고침 뒤에도 기본 문구.

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
