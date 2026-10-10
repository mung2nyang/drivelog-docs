# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 3-B-2 — `unknown` 정리: 백업·복원·저장소 읽기 묶음 `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` F, AGENTS §6·§5 3번. 앞 슬라이스 3-B-1 `[x]`(react-app `7fe99a1`).
3-B 계획: 3-B-1 지출 기록 `[x]` → **3-B-2 (이번) `lib/` 15개 파일** → 3-B-3 나머지(`domain/` 13개, `app/`·`components/`, 그리고 `lib/report.js` — 화면 `ReportSummaryContent.jsx`와 값을 주고받아 그쪽과 같이).

### 현재 상태 (코드 확인)
`lib/` 15개 파일에 `unknown` 약 30곳: 저장소에서 읽은 값의 설명, 백업 파일 조립·검사, 서버 줄 설명, 오류 보관 변수 등.

### 목표 상태 (기대 동작)
1. 15개 파일의 `unknown`(설명 문장 속 낱말 제외) 0곳. 바꾸는 규칙은 3-B-1과 같음(`JsonValue`·`Record<string, JsonValue|undefined>`). 추가로:
   - 백업 객체(`guestBackup.js`·`memberBackup.js`) → `Record<string, JsonValue>`
   - `unknown[]` → `Array<JsonValue>`
   - `registerServiceWorker.js` 등록 결과 → `Promise<ServiceWorkerRegistration|void>`
2. **실행 코드가 바뀌는 곳 2군데** (설명만으로는 타입 검사가 안 맞아서 — 사전 시험으로 확인):
   - ⓐ `guestBackup.js:68` 비회원 백업 만들 때 보조 차량 고르는 조건에 "차량이 목록 모양이 아님"·"차량 번호가 글자" 확인 추가. 저장된 차량은 언제나 객체·글자 번호라 **결과 같음**. 근거: 기존 테스트 `guestBackup.test.js:53·146`(보조 차량 일지 백업·복원)이 그대로 통과해야 함.
   - ⓑ `dayLogCloudCommit.js:178` 일지 서버 저장의 첫 오류를 보관할 때 `Error` 모양으로 맞춤(`error instanceof Error ? error : new Error(String(error))`). 이 값은 콘솔 기록(194줄)과 "오류 있었나" 확인에만 씀 → 화면·저장 동작 같음.
3. 나머지는 설명(주석 속 타입)만 바뀜.

### 건드릴 파일 (15개)
`src/lib/` — `cars.js`, `clients.js`, `cloudStorage.js`, `dailyInspections.js`, `dayLogCloudCommit.js`, `drivers.js`, `guestBackup.js`, `invoices.js`, `memberBackup.js`, `memberRestore.js`, `notifications.js`, `registerServiceWorker.js`, `supportInquiryMutations.js`, `syncWorkData.js`, `taxInvoiceExcelBuilder.js`
- 파일 수가 기준보다 많지만 대부분 1~2줄 설명 바꿈이라 묶음(앞 슬라이스와 같은 판단).

### 안 건드릴 것
- `lib/report.js` → 3-B-3. 설명 문장 속 낱말 "unknown"(`hydrateMergeTypes.js`·`outboxErrors.js` 주석) → 4번 주석 정리 때.
- 테스트 파일.

### 사전 시험 (임시 복사본, 2026-10-10)
- `tsc --noEmit` 오류 0건. 엄격 검사 목록 438 → **438(변화 없음)**. 15개 파일 `unknown` 0곳.

### 테스트
- 새 테스트 없음. ⓐ는 기존 `guestBackup.test.js`, ⓑ는 기존 `dayLogCloudCommit` 관련 테스트(`App.test.js`·`memberRestore.test.js`·`receivablesCloudCommit.test.js`)가 그대로 통과해야 함.
- 플레이북 트리거 해당(`lib/cloud*`·`lib/*commit*`·`localStorage` 직접 읽는 파일·백업 복원). 저장·삭제 동작 변화 없음 — 실행 줄 변경 ⓐⓑ 2곳만, 나머지는 `git diff`로 설명 줄뿐임을 확인해 결과에 적음.

### §6 200줄
- 15개 파일 모두 200줄 이하. 설명 줄 1줄씩 늘 수 있음(`dayLogCloudCommit.js` 199줄은 늘지 않음 — 그 파일은 바꾸는 줄만 있음).

### 실패 시 처리
- 새 저장·대비 장치 없음(§7 해당 없음). 실패 시 수정 착수지시서 작성 후 대기.

### 브라우저 확인 (보리, 휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
- 비회원: 앱 설정 → [백업 저장하기] 파일 받기 → [백업 불러오기]로 다시 넣기 → 일지·차량 그대로.
- 로그인: 데이터 다운로드(백업) 받아지는지, 일지 운행 1건 추가 → 껐다 켜도 그대로.
- 알림(🔔) 화면이 전처럼 뜨는지.

### 진행 결과
- 15개 파일 `unknown` 0곳(41줄 추가·29줄 삭제).
- 실행 줄 변경은 ⓐ `guestBackup.js` 보조 차량 조건 1줄, ⓑ `dayLogCloudCommit.js` 첫 오류 보관 1줄뿐. 그 밖의 실행 줄 변경 10줄은 줄 안의 `/** @type {…} */` 설명 부분만(`unknown`→`JsonValue` 계열).
- 로컬 `npm test` 전부 통과(단위 832 / 화면 365 — `guestBackup.test.js` 보조 차량 백업·복원 포함), `typecheck` 오류 0, 엄격 검사 목록 438 그대로, `build` 성공, `oxlint`(`src/lib`) 경고 0. `dayLogCloudCommit.js` 199줄 유지.
- react-app `dab2c5c`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-10).
