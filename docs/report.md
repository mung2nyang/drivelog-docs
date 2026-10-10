# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 1-B — 운행 기록 서버 변환 두 벌 정리 `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` A2. 앞 슬라이스 1-A `[x]`(react-app `255da47`).

### 현재 상태 (코드 확인)
- 운행 상세를 서버 칸(`transport_details`)으로 바꾸는 코드가 두 벌:
  - `lib/dayLogCloudCommit.js:47` `replaceTransportDetails` — **지금 실제로 쓰는 것**(로그인 일지 저장)
  - `lib/syncWorkData.js:16` `syncWorkData` 함수 안 — **아무도 안 부름**. 근거: `grep -rnw syncWorkData src` 결과 호출 0건(주석·파일 이름만). `fbe91d5`(서버 성공 후에만 반영으로 전환) 때 호출이 빠짐.
- 점검 때는 "두 벌이 어긋날 위험"으로 봤으나, 확인해 보니 한 벌은 **죽은 코드**. 그래서 합치기가 아니라 죽은 쪽을 지우면 끝남.
- 같은 파일의 `upsertDailyLog`(일지 한 줄 저장)는 `dayLogCloudCommit.js:22`·`syncExpenseRecords.js:11`이 씀 → 그대로 둠.
- `dayLogCloudCommit.js:45` 주석 "syncWorkData.js의 루프 내용과 동일" — 지우고 나면 틀린 말이 됨.

### 목표 상태 (기대 동작)
1. `syncWorkData.js`에서 안 쓰는 `syncWorkData` 함수와 그것만 쓰던 불러오기(`KEYS`·`keyFor`·`readJson`, 타입 설명 2줄) 삭제. `upsertDailyLog`만 남음. 파일 머리 주석은 지금 역할 1줄로.
2. `dayLogCloudCommit.js:45` 주석에서 "syncWorkData.js의 루프 내용과 동일." 문구만 삭제.
3. **동작 변화 없음.** 일지 저장·지출 저장 그대로.

### 건드릴 파일
- `react-app/src/lib/syncWorkData.js` — 안 쓰는 함수·불러오기 삭제(83줄 → 약 30줄)
- `react-app/src/lib/dayLogCloudCommit.js` — 주석 한 구절 삭제

### 안 건드릴 것
- `upsertDailyLog` — 쓰는 곳 2곳(`dayLogCloudCommit.js:22`, `syncExpenseRecords.js:11`). 이 함수 설명의 `unknown` 1곳은 이번에 새로 넣는 게 아니라 3-B(`unknown` 정리)에서 처리.
- 다른 파일 주석 속 `syncWorkData.js` 언급(`workData.js:29·64`, `commitHelpers.js:41`, `persist.js:43`, `workDataTombstones.js:3`, `syncDeletedWorkDates.js`, `workData.test.js:228`) — 4번(이력형 주석 정리)·2번(안 쓰는 파일)에서 처리.
- 파일 이름 `syncWorkData.js` — 바꾸면 불러오는 곳 2곳도 바꿔야 해서 이번엔 안 바꿈.

### 테스트
- 지워지는 함수를 검사하는 테스트 없음. 근거: `grep -rln syncWorkData` 테스트 파일 → `workData.test.js`의 주석 1줄뿐.
- 플레이북 트리거(`lib/*commit*`) 해당 — 저장 경로 파일이지만 바뀌는 건 주석 한 구절. 저장 동작 코드는 그대로라 새 테스트 없음. 기존 `npm test`·`typecheck` 전부 통과로 확인.

### §6 200줄
- `dayLogCloudCommit.js` 199줄 → 199줄 이하 유지(주석 한 줄 안에서 구절만 삭제).
- `syncWorkData.js` 83줄 → 줄어듦.

### 실패 시 처리
- 새 저장·대비 장치 없음(§7 해당 없음). 실패 시 수정 착수지시서 작성 후 대기.

### 브라우저 확인 (보리, 휴대폰 getdrivelog.com)
- 로그인 계정으로 일지에 운행 1건(거래처·금액) 추가 → 앱을 껐다 켜도 그대로 있는지.
- 같은 날 주유·정비 지출 1건 추가 → 껐다 켜도 그대로 있는지.

### 진행 결과
- `syncWorkData.js` 83→28줄(`upsertDailyLog`만 남음), `dayLogCloudCommit.js` 199줄 유지(주석 구절만 삭제).
- 로컬 `npm test` 전부 통과(단위 839 / 화면 365, 실패 0), `npm run typecheck` 오류 0, `oxlint` 두 파일 경고 0.
- 바뀐 줄에 `any`·`unknown`·`@ts-ignore`·`@ts-expect-error` 새로 없음.
- react-app `22951d4`(보리 지시로 커밋·push AI가 함), CI verify 초록, 보리 휴대폰 확인(같은 와이파이 개발 서버) "괜찮아"·최종 `[x]` 승인(2026-10-10).
