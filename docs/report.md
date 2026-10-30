# docs/report.md — 현재 슬라이스 착수지시서

134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 135-A 계정 설정에 문자 문구 자리 만들기 `[x]`

전수점검(2026-10-26) K3, sot 4-21 `[확인: 2026-10-26 보리 — "서버에 둬"]`. DB 변경 없음. 계정 설정은 서버 `profiles.settings`(JSON 한 칸)에 통째로 저장되므로, 그 안에 문구 칸을 더하면 됨(같은 방식 선례: 운비서 버튼 설정 `unbiseoDockOn`, react-app `14b89ff`).

**둘로 나눔.** 고칠 파일이 9개(설정 칸 3 + 문구 읽기·쓰기 1 + 문구 쓰는 화면 5)라 한 번에 하기엔 큼. 이번엔 A만 함.
- **135-A(이번)**: 계정 설정에 문구 칸을 만들고 정리·검사만 함. 화면 동작은 바뀌지 않음.
- **135-B(다음)**: 문자 문구 설정 화면·일지 문자 창·내역서 공유·운비서 문자가 휴대폰 대신 계정 설정에서 읽고 쓰게 바꿈.

### 쉬운 요약
- 문자 문구(미수금 안내·입금 요청·운행 완료·내역서 공유)가 지금은 휴대폰에만 저장돼서, 다른 휴대폰이나 앱 데이터 삭제 때 기본 문구로 돌아갑니다.
- 이번 A에서는 서버에 저장되는 계정 설정 안에 문구를 넣을 자리만 만듭니다. 화면에서 달라지는 것은 없습니다.
- 다음 B에서 문구 화면들이 이 자리를 쓰게 바꾸면, 로그인 회원의 문구가 서버에 남습니다.

### 현재 상태 (코드 확인)
- 문구 저장·읽기: `data/sync/messageTemplates.ts:16-24`·`:31-37`이 휴대폰 저장소 키 `messageTemplateCustomBodies`·`reportShareMessagePattern`만 씀. 계정과 상관없이 휴대폰 하나에 한 벌임.
- 계정 설정 저장: `data/sync/settingsSave.ts:33`이 지금 설정에 바꿀 칸만 얹어 `normalizeSettings`로 정리한 뒤, 로그인이면 `profiles.settings`에 통째로 올림(`profileCloudCommit.ts:22`). 비회원은 휴대폰 설정으로 저장함.
- 설정 정리: `domain/practiceSettings.ts:50-81` `normalizeSettings`가 아는 칸만 돌려줌. 모르는 칸은 버림. 차주(`hydrate.ts:105`)·연동 기사(`hydrateEmployedDriver.ts:87`)가 서버 설정을 불러올 때 모두 이 함수를 거침.
- 휴대폰 설정 검사: `data/store/persistDomainSchema.ts:32-48`. 목록 밖 칸이 있으면 설정 전체를 거부함.
- 설정 타입: `domain/financeTypes.ts:88` 근처(`FinanceSettings`).

### 목표 상태 (기대 동작)
1. 계정 설정에 칸 두 개가 생김: `messageTemplates`(문구 1~3: 미수금 안내·입금 요청·운행 완료, 글자 3개 묶음)와 `reportShareMessage`(내역서 공유 문구, 글자).
2. `normalizeSettings`는 바꾼 문구가 있을 때만 이 칸을 남김. `messageTemplates`는 글자 3개이고 셋 다 비어 있지 않을 때만, `reportShareMessage`는 비어 있지 않은 글자일 때만 남김. 앞뒤 빈칸은 지움. 아니면 칸 자체를 만들지 않음(값 없는 칸을 만들지 않음, 134와 같은 이유).
3. 휴대폰 설정 검사가 두 칸을 받아들임. 틀린 모양(개수가 3이 아님, 글자가 아님)은 지금처럼 거부함.
4. 다른 설정을 바꿔 저장해도 두 칸은 그대로 남음(`settingsSave.ts:33`이 지금 설정 위에 얹으므로 따로 고칠 것 없음, 테스트로 확인).
5. 문구 화면들은 아직 휴대폰 저장소를 씀. 화면 동작은 그대로임.

### 건드릴 파일
- `src/domain/financeTypes.ts` — `FinanceSettings`에 두 칸 추가. 주석 최종 문구:
  - `messageTemplates?: [string, string, string] // 문자 문구 1~3(미수금 안내·입금 요청·운행 완료), 없으면 기본 문구`
  - `reportShareMessage?: string // 내역서 공유 문자 문구, 없으면 기본 문구`
- `src/domain/practiceSettings.ts` — `normalizeSettings` 끝에 두 칸을 "있을 때만" 넣음(작은 고르기 함수 2개). 주석 최종 문구: `// 문자 문구는 바꾼 것만 둔다(없으면 기본 문구) — 모양이 틀리면 칸을 만들지 않는다.`
- `src/data/store/persistDomainSchema.ts` — `:47` 줄에 `messageTemplates: opt(v.tuple([str, str, str]))`, `reportShareMessage: opt(str)` 추가.
- 테스트:
  - `src/domain/practiceSettings.test.js` — 정리 규칙(남김·빈 문구·개수 틀림·앞뒤 빈칸·칸 없음).
  - 새 `src/data/store/persistDomainSchema.messageTemplates.test.js` — 검사가 받는 모양과 거부하는 모양.
  - `src/data/sync/settingsSave.serial.test.js` 또는 같은 폴더 새 테스트 — 다른 설정을 저장해도 문구 칸이 남음.
- 영향 목록: `normalizeSettings`를 쓰는 곳은 설정 저장·불러오기 전부임. 새 칸은 없으면 아예 안 생기므로 지금 저장된 설정의 모양은 그대로임(기존 `practiceSettings.test.js` 기대값이 바뀌지 않는지로 확인).

### 안 건드릴 것
- 문구 읽기·쓰기(`messageTemplates.ts`)와 문구를 쓰는 화면 5곳(문자 문구 설정, 일지 문자 창, 내역서 공유, 운비서 문자 계산·화면)은 135-B에서 함.
- DB는 바꾸지 않음. `profiles.settings`가 JSON 한 칸이라 새 칸이 그대로 들어감.
- 서버 설정 불러오기(`hydrate.ts`, `hydrateEmployedDriver.ts`)는 `normalizeSettings`를 거치므로 따로 고치지 않음.

### 검증
- 새 테스트를 고치기 전 코드에서 먼저 돌려 실패하는지 확인함.
- 로컬 `npm test` 전체와 타입 검사. 다른 테스트가 실패하면 고치지 않고 수정 착수지시서를 씀.

### 파일 길이
- `financeTypes.ts` 109 → 약 111줄, `practiceSettings.ts` 112 → 약 124줄, `persistDomainSchema.ts` 82 → 약 83줄(모두 200줄 이하).
- 테스트는 줄 제한 밖.

### 실패 시 처리
- 새 저장 장치, 재시도, 임시 보관을 만들지 않음.
- 테스트나 타입 검사가 실패하면 바로 고치지 않음. 수정 계획을 다시 써서 승인받음.

### 사용자 확인 방법
- 화면 동작이 바뀌지 않는 조각이라 목표 1~4는 자동 테스트로 확인함.
- 배포본에서는 로그인 탭으로 앱 설정 스위치 하나를 껐다 켜고 새로고침해 그대로인지, 마이페이지 → 문자 문구 설정의 문구가 지금과 같은지만 봄.

### 진행 기록
- 2026-10-30 보리 "확정, 진행해".
- 지시서대로 세 파일을 고침. 타입 검사 통과.
- 새 테스트 실행: 27개 중 26개 통과, 1개 실패. 실패한 것은 `persistDomainSchema.messageTemplates.test.js`의 "모양이 틀리면 거부" 테스트. 문구가 4개인 설정(`["가","나","다","라"]`)을 거부하지 않고 받아들임.
- 원인: 지시서에 적은 `v.tuple([str, str, str])`은 Valibot에서 앞 3개만 보고 뒤에 더 붙은 것은 그냥 둠. 목표 3("개수가 3이 아니면 거부")과 맞지 않음.

### 수정 착수지시서 1 — 문구 개수 검사를 정확히 3개로
- 고칠 곳: `src/data/store/persistDomainSchema.ts` 새로 넣은 줄 하나. `v.tuple([str, str, str])` → `v.strictTuple([str, str, str])`(정확히 3개만 받음). 다른 파일·테스트는 바꾸지 않음.
- 파일 길이: 83줄 그대로.
- 검증: 실패한 테스트가 통과하는지 보고, 새 테스트가 고치기 전 코드에서 실패하는지 확인. 이어서 타입 검사와 `npm test` 전체.
- 새 저장 장치·재시도 없음. 또 실패하면 다시 수정 계획을 씀.
- 2026-10-30 보리 "확정, 진행해"(수정 1). `v.strictTuple`로 바꾼 뒤 새 테스트 27개 전부 통과.
- 고치기 전 코드(세 파일을 마지막 커밋 상태로 잠깐 되돌림)에서는 새 테스트 3개 실패: 정리 규칙의 "문구 남김", 휴대폰 설정 다시 읽기, 로그인 서버 설정 유지. 나머지("틀린 모양 거부", "칸 없음")는 전에도 통과함(지금 동작을 지키는 테스트). 확인 뒤 원래대로 복구.
- 타입 검사 통과. `npm test` 전체 통과: `test:unit` 939개, `test:app` 919개, 실패 0.
- 코드 커밋 react-app `2979833`(push 안 함). 바뀐 파일: 코드 3개 = 지시서 "건드릴 파일" 그대로, 테스트 3개(`practiceSettings.test.js`, 새 `persistDomainSchema.messageTemplates.test.js`, `settingsSave.serial.test.js`).
- 리뷰 점검: 범위 밖 파일 없음. 새 저장 장치·재시도 없음. 금지 타입 표시 없음. 주석 3줄은 지시서 최종 문구 그대로이고 로드맵 번호·날짜 없음. 줄 수 `financeTypes.ts` 111, `practiceSettings.ts` 123, `persistDomainSchema.ts` 83(모두 200줄 이하). 지우거나 약하게 만든 테스트 없음.
- 보리 push. CI verify·deploy 초록(run 40991677073).
- 배포본 확인(로그인 탭): 앱 설정 "운행 시간" 끄기 → 새로고침 뒤 꺼짐 유지 → 다시 켜기 → 새로고침 뒤 켜짐(되돌림, "운비서 버튼"도 켜짐 그대로). 마이페이지 → 문자 문구 설정의 문구 4개가 지금과 같음(기본 문구). 주소를 직접 열 때 콘솔의 404는 깊은 주소용 `404.html` 배포 방식이라 이번 변경과 무관.
- 보리 최종 승인 "승인".

### 135-B에서 정할 것 (미리 여쭘)
- 이 휴대폰에 이미 바꿔 둔 문구가 있으면 B 적용 뒤 계정 설정으로 옮길지 여부. AI 권함: 옮기지 않음. 아직 실사용자가 없고, 옮기려면 "휴대폰 값이 있으면 서버로 올리는" 새 장치가 필요해서임. 옮기지 않으면 바꿔 둔 문구가 있던 사람만 한 번 기본 문구로 보임.

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
