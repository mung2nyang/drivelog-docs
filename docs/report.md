# docs/report.md — 현재 슬라이스 착수지시서

133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 134 새 콜 상세가 저장 대기 검사에 걸림 `[x]`

전수점검(2026-10-26) B5, 로드맵 134 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 한 조각으로 끝냄(고칠 곳 1곳). DB 변경 없음.

### 쉬운 요약
- 휴대폰에 일지를 저장하다 실패하면(저장 공간 부족 등), 앱은 그 내용을 "저장 대기"로 따로 보관했다가 다시 저장합니다.
- 그런데 그 화면에서 새로 넣거나 고친 콜이 들어 있으면, 콜 안의 빈 칸 하나 때문에 저장 대기 보관이 거절됩니다. 그러면 새로고침했을 때 그 콜을 잃을 수 있습니다.
- 고치고 나면 빈 칸을 만들지 않아서, 저장 대기 보관이 정상으로 받습니다. 휴대폰 저장이 이미 실패한 드문 경우에만 생기는 문제입니다.

### 현재 상태 (코드 확인)
- 콜을 만들거나 고칠 때 `domain/call-details.ts:67`이 `linkedLoadIndex: existing?.linkedLoadIndex`로 칸을 늘 만듦. 앱 안에서 이 칸에 값을 넣는 곳은 없어서(검색 결과 값 쓰는 곳은 테스트·예시 자료뿐), 새 콜과 고친 콜은 "칸은 있는데 값 없음"이 됨.
- 검사기는 칸이 없거나 글자인 것만 받음. 값 없는 칸은 거부함(`data/store/callDetailSchema.ts:45` `opt(str)`, `domain/schemaParts.ts:11` `opt = v.exactOptional`).
- 일지 저장은 이 콜 목록을 `structuredClone`으로 복사함(`features/day-log/hooks/dayDraftLifecycle.js:119`). 이 복사는 값 없는 칸도 그대로 남김.
- 비회원·기사 차량·서버 차량 없는 메인 일지는 휴대폰에 저장하고, 실패하면 저장 대기에 등록함(`dayDraftLifecycle.js:81-89`). 등록할 때 검사(`data/sync/pendingWorkDataWrites.ts:23` → `durablePatchSchema.ts:27`)가 그 콜을 거부해 `false`를 돌려줌. 그러면 그 내용은 메모리에만 남고(`durableWriteGuard.ts:20-21`), 새로고침하면 사라짐.
- 휴대폰 저장이 성공할 때는 JSON으로 바뀌면서 값 없는 칸이 빠져 문제가 없음. 직접 재현: 새 콜을 넣은 기록은 검사 `false`, JSON을 한 번 거친 뒤에는 `true`.
- 재현 테스트: `docs/full-code-review-failing-tests.md` B5.

### 목표 상태 (기대 동작)
1. 새로 만든 콜에는 `linkedLoadIndex` 칸이 없음.
2. 고친 콜은 원래 `linkedLoadIndex` 값이 있으면 그대로 이어받고, 없으면 칸을 만들지 않음.
3. 새 콜이나 고친 콜이 들어 있는 하루 기록도 저장 대기 등록이 받아들여짐(`registerPendingDayWrite`가 `true`).
4. 운행 횟수 세기(`day-record.ts:80`, `financeCore.ts:156`, `financeOwnerDetail.ts:76`)는 "값 없음"과 "칸 없음"을 같게 보므로 결과가 그대로임.

### 건드릴 파일
- `src/domain/call-details.ts` — `:67` 한 줄을 "원래 값이 있을 때만 칸을 넣는" 모양으로 바꿈. 주석 한 줄 추가: `// 값 없는 칸은 저장 대기 검사가 거부하므로 원래 값이 있을 때만 이어받는다.`
- `src/data/sync/pendingWorkDataWrites.newCallDetail.test.js`(새 테스트 파일) — 테스트 2개:
  - 새 콜에는 `linkedLoadIndex` 칸이 없고, 그 콜이 든 하루 기록이 저장 대기에 등록됨(전수점검 B5 재현 테스트를 바탕으로 함).
  - `linkedLoadIndex: '0'`인 콜을 고치면 그 값이 그대로 남음.
- 영향 목록: `buildCallDetail`은 `upsertCallDetail`에서만 부르고(`call-details.ts:95`), 화면에서는 `features/day-log/hooks/useCallDetailActions.js:41` 한 곳만 부름.

### 안 건드릴 것
- 검사기(`callDetailSchema.ts`, `durablePatchSchema.ts`)는 그대로 둠. 값 없는 칸을 받게 넓히면 다른 저장 검사도 같이 느슨해짐.
- 일지 저장 흐름(`dayDraftLifecycle.js`)과 저장 대기 장치는 바꾸지 않음.
- 운행 횟수 세기와 혼짐 규칙(sot 627줄, 로드맵 139)은 이번 범위 밖임.

### 검증
- 새 테스트를 고치기 전 코드에서 먼저 돌려 실패하는지 확인함.
- 로컬 `npm test` 전체와 타입 검사. 다른 테스트가 실패하면 고치지 않고 수정 착수지시서를 씀.

### 파일 길이
- `call-details.ts` 116 → 약 117줄(200줄 이하).
- 새 테스트 파일 약 35줄(테스트는 줄 제한 밖).

### 실패 시 처리
- 새 저장 장치, 재시도, 임시 보관을 만들지 않음.
- 테스트나 타입 검사가 실패하면 바로 고치지 않음. 수정 계획을 다시 써서 승인받음.

### 사용자 확인 방법
- 배포본에서 휴대폰 저장 실패(저장 공간 부족)를 일부러 일으킬 수 없어서, 목표 1~3은 자동 테스트로 확인함.
- 배포본에서는 로그인 탭으로 오늘 일지에 시험 콜을 하나 넣고, 새로고침해도 남는지, 달력 운행 횟수가 1 늘었는지 본 뒤 그 콜을 지움.

### 진행 기록
- 2026-10-29 보리 "진행해".
- 새 테스트 중 "새 콜" 테스트는 고치기 전 코드에서 실패함(새 콜에 `linkedLoadIndex` 칸이 있음). "고친 콜" 테스트는 고치기 전에도 통과함(값 유지가 깨지지 않게 지키는 테스트). 고친 뒤 둘 다 통과.
- 타입 검사 통과. `npm test` 전체 통과: `test:unit` 934개, `test:app` 919개, 실패 0.
- 코드 커밋 react-app `98889da`(push 안 함). 바뀐 파일 2개 = 지시서 "건드릴 파일" 그대로.
- 리뷰 점검: 범위 밖 파일 없음. 새 저장 장치·재시도 없음. 금지 타입 표시 없음. 주석은 지시서 최종 문구 그대로이고 로드맵 번호·날짜 없음. `call-details.ts` 117줄(200줄 이하). 지우거나 약하게 만든 테스트 없음.
- 보리 "푸시까지"로 AI가 push. CI verify·deploy 초록(run 40851627881).
- 배포본 확인(로그인 탭): 오늘(10월 29일) 일지에 시험 콜(상차지 "검수테스트상차") 저장 → 새로고침 뒤 남음, 홈 오늘 카드에 보임, 매출 "총 30회 운행" → 시험 콜 삭제 뒤 "총 29회 운행"(1회로 셈). 주소를 직접 열 때 콘솔의 404는 깊은 주소용 `404.html` 배포 방식이라 이번 변경과 무관.
- 보리 최종 승인 "승인".

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
