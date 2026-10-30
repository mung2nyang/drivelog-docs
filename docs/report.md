# docs/report.md — 현재 슬라이스 착수지시서

146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 144. 운비서로 점검표를 저장해도 홈 할 일 카드가 그대로 `[x]`

로드맵 144(⑤, 전수점검 H1 `full-code-review.md` 5-3). 2026-10-30 보리 "응"(조사·지시서 요청). 순서상 146 다음.

### 쉬운 요약
- 홈 할 일 카드의 "오늘 일상점검표 미작성"은 홈을 열 때 서버를 한 번만 읽는다. 그래서 같은 홈 화면에서 운비서로 [저장하기]를 눌러도 카드가 그대로 남는다.
- 점검표를 저장하거나 지우는 데 성공하면 "이 차량·이 날짜 점검표가 생겼다(또는 없어졌다)"는 신호를 앱 안에 보낸다. 홈 할 일 카드는 이 신호를 듣고 바로 고친다.
- 그러면 운비서로 저장하는 즉시 카드가 사라진다. 다른 화면에 갔다 올 필요가 없어진다.

### 현재 상태 (코드 확인)
- 카드가 어떻게 정해지나: `features/home/hooks/useTodayInspectionMissing.js:18-25`는 열쇠(사용자·차량·날짜)마다 `fetchDailyInspection`을 한 번만 부른다. 그 결과로 카드를 보일지 정한다. 저장 뒤에 다시 읽는 길은 없다.
- 운비서 저장 경로: `useUnbiseoAssistant.ts`의 `saveInspection`이 `features/home/api/unbiseoInspection.ts:50`의 `saveAllGood`를 부르고, 이것이 다시 `saveDailyInspection`을 부른다. 홈 카드(`HomeAssistantBar`)는 홈 화면 안에 있어서 홈이 다시 열리지 않는다.
- 서버 저장·지우기를 부르는 곳 3군데(`grep saveDailyInspection\|deleteDailyInspection`):
  - `features/home/api/unbiseoInspection.ts:50`(운비서)
  - `features/day-log/ui/DailyInspectionNotice.jsx:77`·`:93`(일지 점검표 창, 저장·지우기)
  - `features/account/api/memberRestoreInspections.js:102`(백업 되살리기)
- 기존 신호 방식: `data/sync/failedDayLogDates.ts`에 `listeners = new Set`과 구독(`subscribeFailedDayLogDates`) 틀이 이미 있다.
- sot §4-16 점검표 지우기(112)에는 "홈 할 일 … 열 때 다시 읽어 그대로 맞음"이라고 적혀 있다. 이 말은 다른 화면에서 고친 경우만 해당한다. 같은 화면에서 저장했을 때 어떻게 되는지는 sot에 정해져 있지 않다(`grep 미작성\|홈 할 일` 결과 없음).

### 목표 상태 (기대 동작)
1. 홈에 "오늘 일상점검표 미작성"이 보일 때 운비서(홈 카드든 떠 있는 운비서든)로 오늘 점검표를 저장해 성공하면, 서버를 다시 읽지 않고도 카드가 바로 사라진다. 할 일이 하나도 없으면 "할 일 없음"이 보인다.
2. 신호는 서버 저장·지우기가 **성공한 뒤에만** 보낸다. 실패하거나, 막혀서 서버를 부르지 않았거나, 세션이 바뀌어 던진 경우에는 보내지 않는다. 그러면 카드도 그대로다.
3. 지우기에 성공하면 "없음" 신호가 간다. 홈이 같은 차량·날짜를 보고 있다면 카드가 다시 보인다. 지금 화면 구조에서 홈과 일지 창이 동시에 떠 있는 일은 없지만, 신호 자체는 맞게 보낸다.
4. 다른 차량이나 다른 날짜의 신호에는 홈 카드가 반응하지 않는다.
5. 처음 열 때 서버를 읽는 횟수는 지금과 같다(한 번). 확인 중이거나 읽기에 실패하면 안 보이는 것도 그대로다.

### 건드릴 파일
- `react-app/src/data/sync/dailyInspectionChanges.ts` — **새 파일**(약 15줄).
  - `notifyDailyInspectionChanged({ vehicleId, workDate, saved })`와 `subscribeDailyInspectionChanges(listener)` 두 함수만 둔다. 구독을 풀 함수를 돌려준다.
  - 휴대폰 보관·버전 번호는 두지 않는다.
  - 따로 파일로 만드는 까닭: `dailyInspections.ts`를 흉내 내는 테스트가 7개 있다(memberBackup·memberRestore·memberRestoreInspections·DailyInspectionNotice·DocumentIssuePage·unbiseoInspection·HomeAssistantPanel). 이 파일에 새 함수를 넣으면 이 테스트들의 흉내도 전부 고쳐야 한다. 따로 두면 그 테스트들은 그대로 둘 수 있다.
  - 파일 첫 줄 주석: `// 일상점검표 저장·지우기 성공 알림(앱 안에서만, 보관 없음) — 열어 둔 화면이 다시 읽지 않고 맞추게.`
- `react-app/src/data/sync/dailyInspections.ts`
  - `saveDailyInspection`은 오류 검사를 모두 통과한 뒤 맨 끝에서 `notifyDailyInspectionChanged({ vehicleId, workDate, saved: true })`를 부른다.
  - `deleteDailyInspection`은 0줄 검사를 통과한 뒤 맨 끝에서 `notifyDailyInspectionChanged({ vehicleId, workDate, saved: false })`를 부른다.
  - 주석은 새로 달지 않는다.
- `react-app/src/features/home/hooks/useTodayInspectionMissing.js` — 효과를 하나 더 둔다.
  - `active`일 때 구독한다.
  - 신호의 차량(문자로 바꿔 비교)과 날짜가 같으면 `setResult({ key: loadKey, missing: !saved })`.
  - 주석: `// 같은 화면에서 저장·지우면 다시 읽지 않고 맞춘다.`
- 테스트
  - `react-app/src/data/sync/dailyInspections.test.js` — 저장 성공은 "있음" 신호, 지우기 성공은 "없음" 신호, 실패(서버 오류·0줄·로그인 안 함)는 신호 없음.
  - `react-app/src/features/home/ui/HomePage.test.js` — 카드가 보이는 상태에서 진짜 `notifyDailyInspectionChanged`로 같은 차량·오늘 "있음"을 보낸다. 그러면 카드가 사라지고 "할 일 없음"이 보이며, 서버 읽기 횟수는 1 그대로다. 다른 날짜·다른 차량 신호에는 카드가 그대로다.
  - 기존 흉내(`fetchDailyInspection`·`saveDailyInspection`)는 그대로 둔다.

### 안 건드릴 것
- 운비서 점검표 카드(`unbiseoInspection.ts`·`useUnbiseoAssistant.ts`) — 저장은 이미 `saveDailyInspection`을 거치므로 신호가 자동으로 간다.
- 일지 점검표 창(`DailyInspectionNotice.jsx`) — 자기 화면 상태를 이미 스스로 고친다(sot 112). 신호를 듣게 하지 않는다.
- 서류 발급 한 달 표·탭 숨김(`fetchMonthDailyInspections`·`hasAnyDailyInspection`) — 열 때 읽는 방식 그대로.
- DB·서버 함수·`HomePage.jsx` — 바꾸지 않는다.
- 자정이 지난 뒤 날짜가 안 바뀌는 문제(H6) — 이번 범위 밖.

### 검증
- 자동 검사: `npm test`, `npm run typecheck`, `npm run lint`, `npm run depcheck`, `npm run build`.
- 고치기 전 실패 확인(revert-check)
  - 되돌릴 대상: `dailyInspections.ts`와 `useTodayInspectionMissing.js`.
  - 새 파일 `dailyInspectionChanges.ts`는 되돌릴 대상이 없어 그대로 남는다.
  - 기대 결과:
    - HomePage의 "신호 뒤 카드 사라짐"은 실패해야 한다.
    - `dailyInspections.test.js`의 "저장·지우기 성공 신호"도 실패해야 한다.
    - "다른 차량·날짜는 그대로"와 "실패하면 신호 없음"은 고치기 전에도 통과한다(안전 확인).
- 로컬 화면: 손님 모드에서는 카드가 안 보이므로(`ownerKey !== 'guest'` 조건) 테스트로 대신한다.
- 배포 뒤 로그인 탭에서 확인할 것
  - 처음 열 때 서버 읽기가 한 번인지와 번들에 새 코드가 들어갔는지만 본다.
  - 실제 저장은 하지 않는다(계정 자료를 바꾸면 안 됨).

### 파일 길이
- `dailyInspectionChanges.ts`: 0 → 약 15
- `dailyInspections.ts`: 109 → 약 112
- `useTodayInspectionMissing.js`: 29 → 약 38
- 테스트 파일은 제한 밖(`HomePage.test.js` 369 → 약 395, `dailyInspections.test.js` 113 → 약 140).

### 실패 시 처리
- 휴대폰 보관·재시도·다시 읽기 타이머 같은 새 장치는 만들지 않는다. 신호는 앱이 켜져 있는 동안 메모리에서만 오간다.
- 검사가 실패하면 고치지 않고 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법
- 직원이나 기사 계정으로 로그인하고, 앱 설정에서 일상점검표를 켠다. 오늘 점검표가 없는 상태에서 홈을 연다. 할 일에 "오늘 일상점검표 미작성"이 보여야 한다.
- 홈 운비서에 "점검표 모두 양호로 해줘"라고 말하고 [저장하기]를 누른다. "모두 양호로 저장했어요"가 뜨는 순간 할 일 카드의 "오늘 일상점검표 미작성"이 사라지면 성공이다(다른 화면에 갔다 오지 않고).
- 주의: 실제 계정에 오늘 점검표 1장이 저장된다. 필요하면 일지의 점검표 연필 → 모두 비우고 [저장]으로 지울 수 있다.

### 진행 기록
- 2026-10-30 보리 "응"으로 진행 승인. react-app `69dd8f5` 코드 커밋(푸시 전).
- 자동 검사 모두 통과: `npm test`(957개, 937개 두 묶음), typecheck, lint, depcheck, build.
- 고치기 전 실패 확인: 새 테스트 2개 모두 고치기 전 코드에서는 실패하고 고친 뒤 통과한다.
  - 지시서와 다른 점: "다른 차량·날짜는 그대로"와 "실패하면 알림 없음"을 따로 떼지 않고 같은 테스트 안에 넣었다. 그래서 안전 확인용으로 따로 통과하는 테스트는 없다.
- 줄 수: `dailyInspectionChanges.ts` 14, `dailyInspections.ts` 112, `useTodayInspectionMissing.js` 37.
- 로컬 화면: 손님 모드에서는 이 카드가 보이지 않아 테스트로 대신했다.
- 푸시 뒤 CI verify·deploy 초록(run 41059500348).
- 배포본은 seed 로그인 탭에서 읽기만 했다.
  - 번들 `index-DDWLyPLH.js`에 지우기 성공 알림과 홈 카드 구독 코드가 들어 있다.
  - 홈을 열면 `daily_inspections`를 1번 읽고 "오늘 일상점검표 미작성"이 보인다.
  - 저장은 하지 않았다.
- §5 리뷰
  - 바뀐 파일이 지시서의 "건드릴 파일"과 같다.
  - 새 파일은 지시서에 적은 1개다.
  - 타입 꼼수·로드맵 번호가 없다.
  - 주석 2개는 지시서 문구 그대로다.
  - 줄 수 제한 안이다.
  - 기존 테스트는 바꾸지 않았다.
