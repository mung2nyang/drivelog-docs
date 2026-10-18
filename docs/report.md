# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 81 기사 초대 번호를 겹치지 않는 번호로 `[x]`

> **진행(2026-10-18)**: 보리 "작업진행해". 코드 커밋 react-app `3fe2af2` — push 뒤 CI verify·deploy 초록(run 39091491726).
> - **진행 중 보리 결정(지시서와 다른 점)**: 지시서대로 `drivers.js`가 `payments.js`의 `generateLocalId`를 가져오면 파일 고리가 새로 생김(`drivers.js` → `payments.js` → `finance.js` → `financeCore.js` → `drivers.js`, 동작엔 문제 없음). 보리 "1번으로": 번호 만드는 함수를 새 `domain/localId.js`로 옮기고 쓰는 3곳(`call-details.js`·`drivers.js`·`expenses.js`)과 `payments.js` 자신은 가져오는 줄만 바꿈 → 고리 안 생김, 번호 규칙 한 곳.
> - 바뀐 파일: 새 `domain/localId.js` 10줄, `domain/payments.js`(함수 정의 → 가져오기, 9줄 줄어듦), `domain/call-details.js`·`domain/expenses.js`(가져오는 곳 1줄), `domain/drivers.js` 183→184(`generateLocalId('drv')`), `domain/drivers-cloud.test.js` 테스트 2개.
> - 테스트: 81-1(같은 1/1000초 두 기사 → 번호 다름·`drv_시각_무작위`)은 고치기 전 코드에서 실패, 고친 뒤 통과. 81-2(옛 `drv-123` 수정 → 번호 그대로)는 안전 확인(revert-check).
> - `npm test` 1035 + 485 통과(77번 지출 번호 테스트·콜 상세·입금 테스트 포함), 타입 검사·린트 통과.
> - 개발 서버 비회원: 기사 차량에 기사 1명 추가 → 번호 `drv_1792291216470_e3rizg`, 새로 고침 뒤 막기 화면 없이 그대로. 콘솔 오류 없음.

보리 "권장 순서대로 진행해"(2026-10-18) — AI 권장 순서 **81 → 61 → 62 → 63 → 76 → 60 → 59**(그 뒤 23·24 → 34 → 79, 41-B는 미룸). 근거: `docs/roadmap.md` 81번(①, 낮음, 77번 착수 중 발견).

### 쉬운 요약
- 기사를 새로 초대할 때 붙는 번호가 "저장한 시각(1/1000초)"만으로 만들어져, 같은 순간 두 건이면 번호가 겹칠 수 있습니다. 77번(지출 번호)처럼 시각 + 무작위 글자로 바꿔 겹치지 않게 합니다.
- 이미 만들어진 기사 번호(`drv-숫자`)는 그대로 둡니다.

### 현재 상태 (코드 확인)
- `react-app/src/domain/drivers.js:133` `list.push({ id: \`drv-${Date.now()}\`, status: 'pending', ...next })` — 새 기사 번호.
- 이 번호는 서버 초대 저장의 중복 막기 열쇠로도 쓰임: `lib/requestDriverInviteSave.js:112` `idempotencyKey: driver.id` → `lib/driverLinkRpc.js` `p_idempotency_key`. 서버 쪽은 글자(`text`)이고 비어 있지만 않으면 됨(`supabase/migrations/0001_driver_links_idempotency_key.sql:20,30`) — 모양이 바뀌어도 서버는 그대로 받음.
- 번호 모양(`drv-`)을 읽어서 쓰는 곳 없음(`grep -rn "'drv\|drv_\|startsWith('drv" src` → 테스트 자료와 입력칸 이름 `drvName` 등만).
- 77번 선례: `domain/expenses.js:134` `generateLocalId('exp')`(`domain/payments.js:23` — `${prefix}_${Date.now()}_${무작위 6자}`), 테스트 `domain/expenses.test.js` "77-1·77-2".
- 저장 검사: 기사 `id`는 글자면 통과(`store/persistDomainRecords.js` DRIVER) — 새 모양도 통과.

### 목표 상태 (기대 동작)
1. 새 기사 번호는 `drv_시각_무작위` 모양이라, 같은 1/1000초에 두 기사를 만들어도 번호가 다르다.
2. 옛 번호(`drv-숫자`) 기사는 그대로 수정·삭제·초대 저장된다.
3. 서버 초대 저장은 지금처럼 기사 번호를 중복 막기 열쇠로 쓴다(서버 변경 없음).

### 건드릴 파일
- `react-app/src/domain/drivers.js` — 133줄 번호 만들기를 `generateLocalId('drv')`로(가져오기 1줄 추가).
- `react-app/src/domain/drivers-cloud.test.js` — 테스트 2개 추가(77번과 같은 모양).

### 안 건드릴 것
- 서버(Supabase) 함수·표 — 받는 값이 글자라 그대로.
- `requestDriverInviteSave.js`·`driverLinkRpc.js` — 번호를 그대로 넘기기만 함.
- 이미 저장된 기사 번호.

### 검증
- 새 테스트: ① 시각을 고정하고 기사 두 명을 연달아 만들면 번호가 다르고 `drv_시각_무작위` 모양 ② 옛 번호(`drv-123`) 기사를 수정하면 그 번호 그대로.
- 고치기 전 코드로 돌리면 ①은 실패(번호 같음), ②는 통과(안전 확인) — revert-check.
- `npm test`·`npm run typecheck` 통과.
- 개발 서버 비회원: 기사 차량에 기사 추가 → 목록에 보이고 새로 고침 뒤 그대로(새 번호 모양이 저장 검사를 통과).

### 파일 길이
- `domain/drivers.js` 183 → 184(200줄 이하).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 검증이 실패하면 고치지 않고 수정 착수지시서로 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤)
- 차주 계정: 기사 관리 → 기사 초대 → 저장이 전처럼 되고 초대 코드가 나오면 성공(겉으로 바뀌는 것 없음).

### 확인할 점
- 없음(77번과 같은 방식).
