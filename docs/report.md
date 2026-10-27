# docs/report.md — 현재 슬라이스 착수지시서

125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 117 여러 날짜 저장 중 계정이 바뀌면 이전 계정 일지가 기기에 남음 `[x]`

외부 코드 리뷰(2026-10-26)에서 나옴, 로드맵 117 `[확인: 2026-10-26 보리 — "권장순서대로"]`. 앞 슬라이스 125 `[x]`(react-app `05d4723`). 같은 파일의 126(일지 저장 겹침)은 다음 슬라이스 — 이번엔 117만.

### 쉬운 요약
- 미수금 한꺼번에 입금 처리, 백업 불러오기처럼 여러 날짜를 차례로 서버에 저장하는 중에 로그아웃·계정 전환이 일어나면, 앞서 저장된 날짜들이 이전 계정 칸에 반영되고 휴대폰 저장 공간에 남을 수 있습니다.
- 반영하기 직전에 "아직 같은 계정인가"를 확인해, 바뀌었으면 반영하지 않습니다(날짜 하나 저장은 이미 이렇게 함).
- 서버에 저장된 것은 그대로이고, 다시 로그인하면 서버에서 불러옵니다.

### 현재 상태 (코드 확인)
- `data/sync/dayLogCloudCommit.ts:150` `commitMainDayLogMapToCloud`가 성공한 날짜가 하나라도 있으면 세션 확인 없이 `commitLogWorkData` — 세션이 바뀌어 반복을 멈춘 경우(`staleSession`, `:143`)에도.
- 날짜 하나 저장 `commitMainDayLogToCloud`는 반영 직전 `assertSessionStillCurrent`(`:104`) — 문제 없음.
- 반영 값은 저장 전 차량 일지 전체 + 성공 날짜(`partialNext = { ...previousData }`, `:130`).
- 부르는 곳 2곳: `data/sync/ownerFinance.ts:40`(미수금 일괄 입금), `features/account/api/memberRestore.js:140`(백업 불러오기) — 둘 다 `appliedDateKeys` 개수와 `toast`만 씀.
- 기존 테스트 G-4(`data/sync/receivablesCloudCommit.test.js:217-252`)가 지금 동작("2건 성공 후 세션 전환: 부분 Store")을 기대값으로 둠 — 이번에 기대값을 바꿈.

### 목표 상태 (기대 동작)
1. 여러 날짜 저장 중 세션이 바뀌면, 앞서 서버에 저장된 날짜가 있어도 이전 계정의 화면 데이터(Store)·휴대폰 저장 공간에 반영하지 않는다.
2. 그때 돌려주는 값은 지금과 같다 — `appliedDateKeys`(서버에 저장된 날짜), `partial`, 알림 "SESSION_CHANGED" 문구.
3. 세션이 그대로면 지금처럼 성공한 날짜만 반영한다(G-2 "일부만 저장"·G-3 그대로).

### 건드릴 파일
- `react-app/src/data/sync/dayLogCloudCommit.ts` — `:150` 반영 조건에 `isSessionStillCurrent(captured)` 추가, `cloudSession.ts`에서 가져오기 1개 추가. 주석 최종 문구(1줄, `:150` 바로 위):
  `// 세션이 바뀌었으면 반영하지 않는다 — 이전 계정 일지가 화면·기기에 남지 않게.`
- `react-app/src/data/sync/receivablesCloudCommit.test.js` — G-4 이름·기대값 바꿈:
  - 이름: `'G-4 — 2건 성공 후 세션 전환: Store 반영 없음 + SESSION_CHANGED 토스트'`
  - `appliedDateKeys`·`partial`·토스트 확인은 그대로, `2026-05-10`이 `'paid'` → `'unpaid'`(반영 안 됨), 휴대폰 저장 공간 `reactPracticeWorkData:rc-g4`가 비어 있는지 확인 1줄 추가(지금도 통과할 수 있는 지키는 확인 — 고치기 전 실패는 Store 확인이 맡음).
- 공용 스타일·공용 값 변경 없음.

### 안 건드릴 것
- 날짜 하나 저장 `commitMainDayLogToCloud` — 이미 반영 직전 확인(`:104`).
- `partialNext = { ...previousData }` 통째 반영 — 126에서 다룸.
- 부르는 곳 2곳(`ownerFinance.ts`·`memberRestore.js`) — 돌려주는 값이 같아 그대로(목표 2).
- 서버 쪽(DB 변경 없음), 이미 남았을 수 있는 옛 기록 정리(실사용자 전, 로드맵 117 "확인 못 한 것"은 이번 수정으로 새로 생기지 않음).

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(AGENTS §4 트리거 — 저장 경로 `*commit*`), 특히 §5 세션 무효화.
- 바꾼 G-4가 고치기 전 코드에서 실패하는지 먼저 확인(고치기 전 실패 확인), 고친 뒤 이 파일 전체 통과.
- 테스트·타입·빌드·코드 검사는 CI가 봄. 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `dayLogCloudCommit.ts` 165 → 167줄(200 이하).
- `receivablesCloudCommit.test.js` 370 → 약 371줄(테스트 파일 예외).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음). 실패하면 수정 착수지시서를 다시 써서 승인받음.

### 사용자 확인 방법
- 계정 전환 타이밍은 손으로 맞추기 어려워 테스트(G-4)로 확인. 배포본에서는 동작이 그대로인지만 봄:
1. 로그인 계정 미수금 화면에서 한 달 묶음 [입금 완료] → 확인 창 → 완료 알림, 목록에서 빠짐.
2. 그날 일지에서 입금을 되돌려 원래대로(시험한 것 되돌림).

### 진행 기록
- 교차검증: 줄 번호·현재 동작 모두 일치. 고친 점 1개 — `cloudSession.ts` 위치는 `data/sync/`가 아니라 `data/cloud/`(이미 가져오는 목록에 `isSessionStillCurrent` 1개 추가).
- 고치기 전 실패 확인: 바꾼 G-4가 고치기 전 코드에서 Store 확인으로 실패(종료 코드 1, 14개 중 13 통과).
  ```
  test at src\data\sync\receivablesCloudCommit.test.js:217:3
  ✖ G-4 — 2건 성공 후 세션 전환: Store 반영 없음 + SESSION_CHANGED 토스트 (1.2972ms)
    AssertionError [ERR_ASSERTION]: Expected values to be strictly equal:
    'paid' !== 'unpaid'
  ℹ tests 14  ℹ pass 13  ℹ fail 1  ℹ duration_ms 1125.1024
  ```
- 고친 뒤: 이 파일 14개 통과, 로컬 `npm test` 전체 통과(914·899개). 줄 수 `dayLogCloudCommit.ts` 167, 테스트 371 — 지시서대로.
- 코드 커밋 react-app `dc46f0c`, push 뒤 CI verify·deploy 초록(run 40461491661).
- 배포본 확인(2026-10-27, 데스크톱 AI, 로그인 테스트 계정): 미수금 22가2222 "테스트" 10월분 2건 500,000원 [전체 입금 완료 처리] → 확인 창 → "남은 미수금이 없습니다"·알림, 다시 불러와도 목록에서 빠짐(서버 저장) → 10-07·10-12 일지에서 [수금] 눌러 되돌림, 다시 불러와 2건 500,000원 원래대로.
- §5 점검: ① 범위 — 바뀐 파일 `dayLogCloudCommit.ts`·`receivablesCloudCommit.test.js` 2개, 지시서와 같음 ② 새 파일·저장 장치 없음 ③ `any`·`unknown`·`@ts-ignore` 없음 ④ `dayLogCloudCommit.ts` 167줄 ⑤ G-4 기대값 변경은 지시서에 밝힌 동작 변경, 다른 테스트 그대로 ⑥ 공용 클래스·상태값 변경 없음, 주석은 지시서 문구 그대로 ⑦ 목표 1·2는 G-4, 3은 G-2·G-3과 배포본으로 확인.

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
