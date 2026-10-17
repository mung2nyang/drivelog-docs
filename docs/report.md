# docs/report.md — 현재 슬라이스 착수지시서

## 저장 자료 검사를 검사 라이브러리로 (1/6) — 라이브러리 들이기 + `persistDomainRecords.js` `[x]`

> **진행(2026-10-17)**: 보리 "1. 권장으로(Valibot) 2. 이어서 / 작업진행해". 코드 커밋 react-app `6486657` — push 뒤 CI verify·deploy 초록(run 39034103144).
> - 바뀐 파일(지시서 목록과 같음): `package.json`·`package-lock.json`(Valibot 1.5.0), `store/persistDomainRecords.js` 139→127(`if (` 46→1 — 남은 1개는 그대로 둔 `isStringRecord`), 새 `store/persistDomainRecords.test.js` 34개.
> - 동작 기록 테스트 34개(4종 × 칸마다 틀린 종류·모르는 칸·꼭 있는 칸 빠짐·선택 칸 없음·객체 아님·값 비어 있음·NaN/무한대, + 글자/숫자 둘 다·허용 값 다른 값)를 **고치기 전 코드에서 먼저 통과** → 바꾼 뒤 그대로 통과. 테스트가 진짜 잡는지: 차량 `tonnage`를 글자·숫자 둘 다로 느슨하게 하면 "tonnage=5" 실패 확인 → 원래대로.
> - `npm test` 985 + 485 통과(기존 `owner-state`·`guestBackup`·`persist` 테스트 포함), 타입 검사·빌드 통과, 타입 꼼수 0. 앱 첫 묶음 221.33 → 222.50KB(압축, +1.2KB).
> - 개발 서버 비회원: 거래처·콜·기사 차량(수수료·개인정보 포함)·지출 넣고 새로 고침 → 막기 화면 없이 그대로, 백업 만들어 다시 불러오기 → "백업 데이터를 복원했습니다.". 콘솔 오류 없음.
> - 참고(지시서에 없던 것): Valibot이 `typescript`를 선택 의존성으로 적어 두어 `npm install`이 `package-lock.json`에서 `typescript`(와 딸린 패키지 약 20개)의 "개발용" 표시를 뺌. 앱 묶음에는 안 들어감(빌드 크기 위와 같음), CI는 전부 설치하므로 영향 없음.
> - 지시서와 다른 점: 위 참고 1건 말고 없음.

보리 요청(2026-10-17): "if로 짠 게 많은데 리팩토링 가능한지" → "표 방식 말고 라이브러리를 가져오는 방식은?" → "라이브러리 방향으로 지시서 써 줘". 같은 모양(칸마다 `if ('칸' in value && 틀린 종류) return false`)의 검사 파일이 6개, if 약 180개. **이번엔 라이브러리를 들이고 가장 큰 `persistDomainRecords.js` 하나만**, 나머지 5개는 결과를 보고 한 파일씩 따로 슬라이스로.
(앞서 다른 작업 창이 쓴 "표 방식" 지시서 초안은 보리 지시로 이 지시서로 바꿈 — 초안 사본은 AI 임시 폴더에 둠.)

### 쉬운 요약
- 저장된 차량·거래처·지출·기사 자료가 올바른 모양인지 검사하는 코드를, 직접 짠 if 46줄 대신 검사 전용 라이브러리의 짧은 선언("이 칸은 글자, 이 칸은 숫자, 이 칸은 둘 중 하나")으로 바꿉니다.
- 검사 결과(통과/거부)는 한 건도 바뀌면 안 됩니다. 이 검사는 앱을 켤 때와 비회원 백업을 불러올 때 쓰여서, 바뀌면 비회원이 막기 화면에 갇힐 수 있습니다 — 그래서 지금 동작을 못 박는 테스트를 먼저 씁니다.

### 라이브러리 고르기 (확인할 점 1)
같은 거래처 검사(칸 6개)를 세 가지로 만들어 실제로 묶어 잼(AI 임시 폴더, 앱 코드 안 건드림). 앱 첫 화면 묶음은 지금 약 221KB(압축).
| 후보 | 늘어나는 크기(압축) | 비고 |
|---|---|---|
| **Valibot 1.5** | **약 1.8KB** | 쓰는 부분만 들어감. 가장 작음 → **권장** |
| Zod 4 mini(`zod/mini`) | 약 5.8KB | Zod의 작은 판. 쓰는 법은 Valibot과 비슷 |
| Zod 4(보통) | 약 92KB | 가장 널리 쓰이지만 통째로 들어가 큼 → 비권장 |
- 세 가지 모두 설정으로 지금 검사와 똑같이 맞출 수 있음을 확인: 모르는 칸 거부, 칸은 있는데 값이 비어 있음(`undefined`) 거부, 숫자 아님(NaN)·무한대 거부, 허용 값 밖 거부, 꼭 있는 칸 빠짐 거부, 배열·`null` 거부.
  - Valibot: `strictObject`(모르는 칸 거부) + `exactOptional`(비어 있는 값 거부) + 숫자에 `finite` 붙임.

### 현재 상태 (코드 확인)
- `react-app/src/store/persistDomainRecords.js` 139줄, `if (` 46개. 검사 함수 4개: `isPersistedCar`(66~92줄, 허용 칸 26·꼭 있는 칸 `number`), `isPersistedClient`(95~113, 22칸·꼭 `id`·`companyName`), `isPersistedExpense`(116~128, 12칸·꼭 `id`·`date`·`kind`), `isPersistedDriver`(131~139, 9칸·꼭 `id`). 허용 칸 목록과 검사 줄이 1:1(빠진 칸 0).
- 이 파일을 부르는 곳(테스트 빼고 12개, `grep -rn "from .*persistDomainRecords" src`):
  - **검사 함수 4개 모두: `store/persistDomainSchema.js`** — 앱을 켤 때(`owner-state.js` `initializeOwnerFromPersist`)와 비회원 백업 불러오기(`lib/guestBackup.js`)의 형식 검사. 틀리면 비회원은 막기 화면.
  - `isPersistedClient`·`isPersistedExpense`: `lib/memberRestoreRecords.js`(회원 파일 불러오기).
  - 도우미 `isPlainObject`·`hasOnlyKeys`·`isFiniteNumber`·`isStringOrFiniteNumber`: store·lib 11개 파일.
  - `isStringRecord`·`PERSONAL_INFO_KEYS`·`BUSINESS_INFO_KEYS`: `lib/hydrateMergeCars.js`.
- 기존 테스트: `owner-state.test.js` 400~407줄이 `isPersistedCar`를 직접 일부 검사, `guestBackup.test.js`의 거부 테스트(68)와 `persist.test.js`가 저장 경로로 간접 검사. 4개 함수 전부를 칸마다 검사하는 테스트는 없음.

### 목표 상태 (기대 동작)
1. 4개 검사 함수가 어떤 입력에도 지금과 같은 참/거짓을 돌려준다(이름·인자·내보내기 그대로).
2. 4개 함수 안의 if 줄이 라이브러리 선언(차량·거래처·지출·기사 각 1개)으로 바뀌고, 따로 적던 허용 칸 목록 `CAR_KEYS`·`CLIENT_KEYS`·`EXPENSE_KEYS`·`DRIVER_KEYS`는 선언 안으로 들어가 없어진다.
3. 밖에서 쓰는 도우미(`isPlainObject`·`hasOnlyKeys`·`isFiniteNumber`·`isStringOrFiniteNumber`·`isStringRecord`·`PERSONAL_INFO_KEYS`·`BUSINESS_INFO_KEYS`)는 이름·동작 그대로(다른 11개 파일이 아직 씀 — 나머지 슬라이스에서 정리).
4. 허용 값 목록은 지금 `persistDomainEnums.js` 것을 그대로 씀(읽기만).

### 건드릴 파일
- `react-app/package.json`·`package-lock.json` — 고른 라이브러리 1개 추가(`dependencies`).
- `react-app/src/store/persistDomainRecords.js` — 4개 검사 함수를 라이브러리 선언으로.
- 새 `react-app/src/store/persistDomainRecords.test.js` — 지금 동작을 못 박는 테스트(아래).
- 공용 영향: 내보내는 이름·동작이 그대로라 부르는 12개 파일은 안 고침.

### 안 건드릴 것
- 같은 모양의 나머지 5개(`persistDomainSchema.js` 36개, `lib/callDetailSchema.js` 45개, `persistDayRecord.js` 22개, `persistDayRecordLegacy.js` 20개, `persistDomainInvoice.js` 12개) — 이번 결과를 보고 한 파일씩 따로.
- 손으로 적은 타입 설명(JSDoc) — 라이브러리 선언에서 타입을 뽑아 바꾸는 일은 나중에 따로.
- 저장 키·저장 형식·화면.

### 검증
- **먼저** 새 테스트를 지금 코드에 대고 써서 통과시킨 뒤 바꾸고, 바꾼 뒤에도 그대로 통과(동작 그대로의 증명). 4종 각각:
  ① 모든 칸 올바른 자료 → 통과 ② 칸마다 틀린 종류 하나씩 → 거부(칸 수만큼, 손으로 적은 목록) ③ 모르는 칸 → 거부 ④ 꼭 있는 칸 빠짐 → 거부 ⑤ 선택 칸 전부 없음 → 통과 ⑥ 객체 아님(`null`·배열·글자) → 거부 ⑦ 칸은 있는데 값이 `undefined` → 거부 ⑧ 숫자 칸에 NaN·무한대 → 거부 ⑨ 허용 값 칸(종류·결제주기 등)에 목록 밖 값 → 거부 ⑩ `personalInfo`·`businessInfo` 안에 모르는 칸·틀린 종류 → 거부.
- 테스트가 진짜 잡는지: 바꾼 코드에서 칸 하나의 검사를 일부러 느슨하게 해 보면 ②가 실패하는지 확인 → 원래대로.
- 기존 `owner-state.test.js`·`guestBackup.test.js`·`persist.test.js` 그대로 통과.
- `npm test`·`npm run typecheck`·`npm run build` 통과, 빌드 크기 늘어난 양을 진행 기록에 적음. 타입 꼼수(`any`·`unknown`·`@ts-ignore`·이중 단언) 0개.
- 개발 서버 비회원: 차량·거래처·지출 하나씩 넣고 새로 고침 → 막기 화면 없이 그대로 보임, 백업 파일 받아 다시 불러오기 → "백업 데이터를 복원했습니다."

### 파일 길이
- `persistDomainRecords.js` 139 → 약 110.
- 새 테스트는 테스트 파일 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(검사 코드 모양만 바꿈). 테스트가 바꾸기 전·후 다르게 나오면 고치지 않고 수정 착수지시서로 다시 승인받는다.
- 라이브러리를 들였는데 결과를 맞출 수 없는 칸이 나오면 멈추고 보고(그 칸만 직접 검사로 남길지 묻기).

### 사용자 확인 방법 (배포 뒤)
- 앱을 다시 열어 차량·거래처·지출·기사 목록이 전과 똑같이 보이면 성공(바뀌는 게 없어야 정상).
- 비회원: 앱 설정 → 데이터 관리 → 백업 파일 저장 후 불러오기가 전처럼 된다.

### 확인할 점
1. **라이브러리**: Valibot(약 1.8KB, 권장)으로 할까요, Zod mini(약 5.8KB)로 할까요? (보통 Zod는 약 92KB라 비권장)
2. 나머지 5개 파일도 같은 라이브러리로 한 파일씩 이어 가는 계획 그대로 갈까요(이번 결과 보고 결정해도 됩니다)?
