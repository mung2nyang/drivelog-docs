# docs/report.md — 현재 슬라이스 착수지시서

132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 133 백업 불러오기가 기사 차량 고정 노선 거래처 연결을 풂 `[x]`

전수점검(2026-10-26) I1, 로드맵 133 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 한 조각으로 끝냄(고칠 곳 1곳). DB 변경 없음.

### 쉬운 요약
- 백업 파일을 불러올 때, 계정에 메인 차량 고정 노선 거래처가 이미 있으면, 파일에 든 기사 차량 전용 고정 노선 거래처의 "고정 노선 연동"이 꺼진 채로 들어옵니다.
- 그러면 그 기사 차량의 고정 노선 금액이 기사 차량 단가가 아니라 차주 메인 단가로 계산됩니다.
- 고치고 나면 앱의 다른 곳과 똑같이, 고정 노선 연결을 차량마다 한 곳씩 따로 봅니다.

### 현재 상태 (코드 확인)
- 앱 규칙: 고정 노선 거래처는 차량 범위마다 한 곳임. 메인은 범위 없음이고, 기사 차량은 그 차량번호가 범위임(sot §4-4e 1·3번). 거래처를 저장할 때도 같은 범위 안에서만 다른 연결을 끔(`domain/clients.ts:115-120`).
- 계산: 고정 노선 금액은 그 범위의 연결 거래처를 먼저 찾고, 없으면 메인(범위 없음) 거래처로 물러남(`domain/clients.ts:12-20`, `resolveFixedUnitPrice`). 기사 차량 정산·매출이 이 함수를 씀(`financeCore.ts:80-82`, `driverShareBase.ts:24-25`, `useMonthSettlement.ts:25`).
- 불러오기: `features/account/api/memberRestoreRecords.js:68` `newClientsAgainst`가 "지금 계정에 연결 거래처가 하나라도 있는지"를 범위와 상관없이 봄. 하나라도 있으면 파일에서 들어오는 연결 거래처를 모두 끔(`:76`). 고르기(`pickMemberRecords`, `:117`)와 저장 직전 다시 고르기(`restoreClients`, `:131`)가 둘 다 이 함수를 씀.
- 불러오는 거래처는 메인 것과 내 미연동 기사 차량 것뿐임. 연동 기사 차량 범위 거래처는 이미 건너뜀(`:109-110`, `isOwnUnlinkedCar`).
- 재현 테스트: `docs/full-code-review-failing-tests.md` I1. 메인에 연결 거래처 A가 있을 때, 파일의 기사 차량(34나5678) 전용 연결 거래처 B가 꺼짐.

### 목표 상태 (기대 동작)
1. 불러오기에서 고정 노선 연결은 범위마다 따로 봄. 범위는 거래처의 차량번호(`scopedToVehicleNumber`, 앞뒤 빈칸 무시)이고, 없으면 메인임.
2. 그 범위에 지금 연결 거래처가 이미 있으면, 파일에서 들어오는 같은 범위 거래처의 연결은 끔(지금과 같음).
3. 그 범위에 연결 거래처가 없으면, 파일의 첫 연결 거래처는 연결된 채로 들어오고, 같은 범위의 두 번째부터는 끔.
4. 메인에 연결 거래처가 있어도, 내 미연동 기사 차량 범위의 연결 거래처는 연결된 채로 들어옴. 그래서 그 차량 고정 노선 금액이 그 거래처 단가로 계산됨.
5. 거래처 고르기·건너뛰기 규칙(같은 업체명·번호, 연동 차량 건너뜀)과 지출·세금계산서 불러오기는 그대로임.

### 건드릴 파일
- `src/features/account/api/memberRestoreRecords.js` — `newClientsAgainst`의 `linkedTaken`(참·거짓 하나)을 "연결 거래처가 있는 범위 모음"으로 바꿈. 들어오는 거래처는 자기 범위가 모음에 있으면 연결을 끄고, 없으면 모음에 더함. 주석은 함수 설명 한 줄만 고침: `지금 없는 것만 더한 거래처 목록(새 것은 옛 서버 번호 지움, 고정노선 연결은 차량 범위마다 한 곳만).`
- `src/features/account/api/memberRestoreRecords.test.js` — 테스트 1개 추가: 지금 계정에 메인 연결 거래처가 있을 때, 파일의 기사 차량(34나5678) 전용 연결 거래처 2개와 메인 연결 거래처 1개를 넣으면 연결이 [켬, 끔, 끔]으로 들어옴. 기존 테스트 `:81`(메인끼리 한 곳만)은 그대로 둠.
- 영향 목록: `newClientsAgainst`는 이 파일 안에서만 씀(`:117`, `:131`). 다른 파일에서 부르지 않음(검색 결과 `memberRestore.js:161`은 `restoreClients`를 부름).

### 안 건드릴 것
- 고정 노선 계산(`domain/clients.ts`)과 거래처 저장 규칙은 이미 범위마다 한 곳이라 그대로 둠(`:115-120`).
- 일지·점검표·지출·세금계산서 불러오기(`memberRestore.js`, `memberRestoreInspections.js`)는 바꾸지 않음.
- 서버 데이터는 고치지 않음. 이 문제로 연결이 꺼진 거래처가 실제로 있는지는 화면 기록으로 가릴 수 없어서, 있더라도 사용자가 고정 노선 화면에서 다시 켜면 됨.

### 검증
- 새 테스트를 고치기 전 코드에서 먼저 돌려 실패하는지 확인함.
- 로컬 `npm test` 전체와 타입 검사. 다른 테스트가 실패하면 고치지 않고 수정 착수지시서를 씀.

### 파일 길이
- `memberRestoreRecords.js` 176 → 약 177줄(200줄 이하).
- `memberRestoreRecords.test.js` 153 → 약 168줄(테스트는 줄 제한 밖).

### 실패 시 처리
- 새 저장 장치, 재시도, 임시 보관을 만들지 않음.
- 테스트나 타입 검사가 실패하면 바로 고치지 않음. 수정 계획을 다시 써서 승인받음.

### 사용자 확인 방법
- 배포본에서 실제로 불러오기를 하면 서버에 거래처가 새로 생김. 그래서 목표 1~4는 자동 테스트로 확인함.
- 배포본에서는 마이페이지 → 데이터 다운로드 / 불러오기 → 불러오기 창이 지금처럼 열리는지, 설정의 고정 노선 거래처와 단가가 그대로인지만 봄(로그인 탭).

### 진행 기록
- 2026-10-29 보리 "확정,진행해".
- 새 테스트는 고치기 전 코드에서 실패함: `cl-b`(기사 차량 34나5678 전용)가 `false`로 들어옴. 고친 뒤 통과.
- 타입 검사 통과. `npm test` 전체 통과: `test:unit` 932개, `test:app` 919개, 실패 0.
- 코드 커밋 react-app `575cb27`(push 안 함). 바뀐 파일 2개 = 지시서 "건드릴 파일" 그대로.
- 리뷰 점검: 범위 밖 파일 없음. 새 저장 장치·재시도 없음. 금지 타입 표시 없음. 고친 주석은 지시서 최종 문구 그대로이고 로드맵 번호·날짜 없음. `memberRestoreRecords.js` 177줄(200줄 이하). 지우거나 약하게 만든 테스트 없음(기존 `:81` 그대로 통과).
- 보리 "예외로 푸시까지"로 AI가 push. CI verify·deploy 초록(run 40841543364).
- 배포본 확인(로그인 탭): 마이페이지 → 데이터 다운로드 / 불러오기 창이 열리고 문구 그대로. 거래처 화면 "테스터거래처" 고정 노선 연동 그대로, 앱 설정 고정 노선 "테스터거래처 1회 50,000원, 파렛트 5,000원" 그대로. 콘솔 오류 없음.
- 보리 최종 승인 "승인".

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
