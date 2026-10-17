# docs/report.md — 현재 슬라이스 착수지시서

## 저장 자료 검사를 검사 라이브러리로 (4/6) — `store/persistDayRecord.js`(하루 기록) `[x]`

> **진행(2026-10-17)**: 보리 "작업진행해". 코드 커밋 react-app `eb8ff67` — push 뒤 CI verify·deploy 초록(run 39036620234).
> - 바뀐 파일(지시서 목록과 같음): `store/persistDayRecord.js` 82→71(`if (` 22→3, 남은 3개는 그대로 둔 날짜별 읽기 `parsePersistedWorkDataMap`), 새 `store/persistDayRecord.test.js` 6개. 안 쓰게 된 `isNonNegativeInteger`·`hasOnlyKeys`·`isFiniteNumber` 가져오기 지움(같은 파일 안). `DAY_RECORD_KEYS`는 선언 칸 이름에서(14칸·순서 그대로 테스트로 확인).
> - 동작 기록 테스트(14칸 틀린 값 41건, 빈 하루·모르는 칸·값 비어 있음, 경계 값 18건, 노선 묶음 배열·소수·음수, 날짜별 읽기의 `'off'`·날짜 아닌 키, 칸 목록 순서)를 **고치기 전 코드에서 먼저 통과** → 바꾼 뒤 그대로 통과.
> - 테스트가 진짜 잡는지: 노선 묶음의 배열 거부를 빼면 "fixedRouteCounts=[]" 실패 확인 → 원래대로.
> - `npm test` 1008 + 485 통과, 타입 검사·빌드·린트 통과, 타입 꼼수 0. 묶음 222.64KB(압축).
> - 개발 서버 비회원: 휴무 하루·고정 횟수·파렛트·노선 횟수·거리 있는 하루·콜 있는 하루 넣고 새로 고침 → 막기 화면 없이 달력·값 그대로, 백업 다시 불러오기 → "백업 데이터를 복원했습니다.". 콘솔 오류 없음.
> - 지시서와 다른 점: 없음.

3/6 `[x]`(`3bbb260`) 다음. 보리 결정(2026-10-17): Valibot으로 6개 파일 한 파일씩.

### 쉬운 요약
- 하루 운행 기록(휴무·고정 횟수·파렛트·콜 상세 목록·노선별 횟수·운임·운행거리·옛 주유/정비/기타 목록 등 14칸)이 올바른지 검사하는 if들을 Valibot 선언으로 바꿉니다.
- 검사 결과는 한 건도 바뀌면 안 됩니다. 이 검사가 틀리면 그 달 일지 전체가 안 읽힙니다(앱 켤 때·비회원 백업·회원 파일 불러오기) — 동작 기록 테스트를 먼저 씁니다.

### 현재 상태 (코드 확인)
- `react-app/src/store/persistDayRecord.js` 82줄, `if (` 22개.
  - `isPersistedDayRecord`(31~58): 칸 14개 모두 선택, **빈 객체 `{}`는 거부**. `isOff` 켜기/끄기, `fixedCount`·`palletCount`·`count` 0 이상 정수, `dailyDistance` 0 이상 유한 숫자, `fare`·`fixedFare`·`totalFare` 금액 규칙(3/6의 `isValidCurrencyAmount`), `assignedVehicleNumber` 글자, `fixedRouteCounts` 묶음(값은 0 이상 정수, 배열 거부), `callDetails` 콜 상세 목록(3/6 `isPersistedCallDetail`), `fuelItems`·`maintItems`·`miscItems` 옛 목록(5/6 파일 검사를 부름).
  - `parsePersistedWorkDataMap`(64~75)·`isPersistedWorkDataMap` — 날짜별로 하루 기록을 읽고 옛 `'off'` 표기를 `{ isOff: true }`로 바꾸는 **읽기 함수**라 그대로 둠(if 2개).
  - `DAY_RECORD_KEYS` 내보냄 — `lib/hydrateMergeWork.js`가 서버 기록에서 모르는 칸을 거르는 데 씀.
- 부르는 곳: `store/persist.js`(앱 켤 때 일지 읽기)·`lib/guestBackup.js`·`lib/memberRestore.js`(`parsePersistedWorkDataMap`), `store/persistDomainSchema.js`(`isPersistedWorkDataMap`), `lib/hydrateMergeWork.js`(`DAY_RECORD_KEYS`).
- 기존 테스트: `lib/assignedVehicleDay.test.js` 등이 간접 검사. 칸마다 검사하는 테스트는 없음.
- 앞 슬라이스 주의점 그대로: 배열 거부는 객체 검사 **앞**에(2/6), Valibot `number`의 NaN 거부 차이(3/6) — 이 파일의 숫자 칸은 지금도 모두 유한 숫자만 받아 차이 없음(`isFiniteNumber`·`Number.isInteger`).

### 목표 상태 (기대 동작)
1. `isPersistedDayRecord`·`parsePersistedWorkDataMap`·`isPersistedWorkDataMap`·`canonicalizeDayRecord`가 어떤 입력에도 지금과 같은 결과(이름·인자·타입 좁히기 그대로).
2. `isPersistedDayRecord`의 칸 검사 if가 Valibot 선언으로 바뀐다.
3. `DAY_RECORD_KEYS`는 그대로 내보내되 선언의 칸 이름에서 만든다(14칸·순서 그대로).
4. `parsePersistedWorkDataMap`(날짜 확인·`'off'` 바꾸기)은 그대로.

### 건드릴 파일
- `react-app/src/store/persistDayRecord.js` — 하루 기록 검사를 Valibot 선언으로.
- 새 `react-app/src/store/persistDayRecord.test.js` — 동작 기록 테스트.
- 공용 영향: 내보내는 이름·결과 그대로라 부르는 5개 파일은 안 고침.

### 안 건드릴 것
- `persistDayRecordLegacy.js`(5/6), `lib/callDetailSchema.js`(3/6 끝남), `hydrateMergeWork.js`.
- 저장 키·형식·화면.

### 검증
- **먼저** 새 테스트를 지금 코드에 대고 써서 통과 → 바꾼 뒤 그대로 통과:
  ① 14칸 모두 올바른 하루 → 통과 ② 칸마다 틀린 종류 하나씩 → 거부(정수 칸에 소수·음수·글자, 거리 음수·NaN·무한대, 금액 규칙 위반, 묶음·목록 자리에 배열/객체 바꿔 넣기 등, 손으로 적은 목록) ③ 모르는 칸 → 거부 ④ 빈 하루 `{}` → 거부 ⑤ 배열·`null`·글자 → 거부 ⑥ 값이 `undefined` → 거부 ⑦ 경계 값 통과(횟수 0, 거리 0·소수, 금액 빈 글자·"1,000원", 빈 목록, 빈 노선 묶음) ⑧ 노선 묶음에 배열·소수·음수 → 거부 ⑨ 콜 상세 목록에 틀린 콜 하나 → 거부 ⑩ 옛 목록(주유·정비·기타)에 틀린 항목 → 거부 ⑪ `parsePersistedWorkDataMap`: `'off'` → `{ isOff: true }`, 날짜 아닌 키·틀린 하루 하나 → `null`, 배열 → `null` ⑫ `DAY_RECORD_KEYS` 14칸·순서 그대로.
- 테스트가 진짜 잡는지: 노선 묶음의 배열 거부를 빼 보면 ⑧이 실패하는지 확인 → 원래대로.
- 기존 테스트 전부 통과. `npm test`·`npm run typecheck`·`npm run build` 통과, 타입 꼼수 0.
- 개발 서버 비회원: 휴무 하루·고정 횟수 하루·콜 있는 하루 넣고 새로 고침 → 막기 화면 없이 달력·일지 그대로, 백업 다시 불러오기 정상.

### 파일 길이
- `persistDayRecord.js` 82 → 약 75.
- 새 테스트는 테스트 파일 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 테스트가 바꾸기 전·후 다르게 나오면(설계 안의 순서·설정 실수가 아닌 경우) 고치지 않고 수정 착수지시서로 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤)
- 달력과 일지(휴무·고정 횟수·콜 상세)가 전과 그대로, 앱을 다시 켜도 그대로면 성공.

### 확인할 점
- 없음. 참고: 배열 거부 도우미가 2/6·3/6 파일에 따로 있고 이번에도 하나 생깁니다. 6/6이 끝나면 공용 도우미 한 파일로 모으는 작은 정리를 따로 제안드리겠습니다.
