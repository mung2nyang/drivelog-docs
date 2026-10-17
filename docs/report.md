# docs/report.md — 현재 슬라이스 착수지시서

## 저장 자료 검사를 검사 라이브러리로 (3/6) — `lib/callDetailSchema.js`(콜 상세) `[x]`

> **진행(2026-10-17)**: 보리 "작업진행해". 코드 커밋 react-app `3bbb260` — push 뒤 CI verify·deploy 초록(run 39036002039).
> - 바뀐 파일(지시서 목록과 같음): `lib/callDetailSchema.js` 136→83(`if (` 45→4, 남은 4개는 그대로 둔 금액 규칙 `isValidCurrencyAmount`), 새 `lib/callDetailSchema.fields.test.js` 7개. 머리 설명 9줄 → 1줄, 금액 빈 값 경위 4줄 → 1줄. 안 쓰게 된 `isNonNegativeFiniteNumber` 지움(같은 파일 안). `ALLOWED_CALL_DETAIL_KEYS`는 선언의 칸 이름에서 만듦(24칸·순서 그대로 테스트로 확인).
> - 동작 기록 테스트(24칸 틀린 값 41건, 빈 콜·id 없는 옛 콜, 경계 값 13건, 수수료 type·value 꼭 있음, 입금 항목 빈 항목 통과·배열 항목 거부, 칸 목록 순서)를 **고치기 전 코드에서 먼저 통과** → 바꾼 뒤 그대로 통과. 기존 `callDetailSchema.test.js` 2개 통과.
> - **진행 중 찾아 맞춘 것**: 수수료 `value`는 지금 코드가 숫자면 NaN도 받는데 Valibot `number`는 NaN을 거부 → `typeof` 검사로 지금과 같게 맞추고 테스트 1줄 추가(이 테스트도 고치기 전 코드에서 통과 확인). JSON엔 NaN이 안 생겨 실제 영향은 없었음.
> - 테스트가 진짜 잡는지: 입금 항목의 배열 거부를 빼면 "payments=[[]]" 실패 확인 → 원래대로.
> - `npm test` 1002 + 485 통과, 타입 검사·빌드·린트 통과, 타입 꼼수 0. 묶음 222.69KB(압축, 2/6과 거의 같음).
> - 개발 서버 비회원: 콜 하나에 입금 2건(빈 항목 포함)·수수료·`clientId: null`·화물 톤수 2.5 넣고 새로 고침 → 막기 화면 없이 그날 화면 그대로("수수료 - 30,000원" 등), 값 그대로. 콘솔 오류 없음.
> - 지시서와 다른 점: 없음(위 NaN 맞춤은 "결과 그대로" 목표 안).

2/6 `[x]`(`d7541f3`) 다음. 보리 결정(2026-10-17): Valibot으로 6개 파일 한 파일씩.

### 쉬운 요약
- 콜 상세 한 건(상차지·하차지·운임·거래처·입금 기록·수수료 등 24칸)이 올바른 모양인지 검사하는 if들을 Valibot 선언으로 바꿉니다.
- 검사 결과는 한 건도 바뀌면 안 됩니다. 이 검사는 하루 기록을 읽을 때(앱 켤 때·백업 불러오기)와 저장 대기 기록에 쓰여서, 틀리면 그날 기록이 통째로 안 읽힐 수 있습니다 — 동작 기록 테스트를 먼저 씁니다.
- 맨 위의 긴 설명 주석(9줄)과 중간의 사고 경위 주석(4줄)은 결론 1~2줄로 줄입니다(AGENTS 주석 규칙).

### 현재 상태 (코드 확인)
- `react-app/src/lib/callDetailSchema.js` 136줄, `if (` 45개.
  - `isValidCurrencyAmount`(32~45, 금액 글자 "1,000"·"1,000원"·빈 값·0 이상 정수) — 칸 검사가 아니라 금액 규칙이라 **그대로 둠**(4개).
  - `isValidCommissionSnapshot`(59~66): 칸 3개, `enabled`는 꼭 있는 켜기/끄기, `type`·`value`는 **꼭 있어야 함**(없으면 `undefined`라 거부됨 — 지금 코드 그대로), `type` 글자 또는 `null`, `value` 글자·숫자·`null`.
  - `isValidPayment`(73~81): 칸 4개 모두 선택, `amount`는 금액 규칙.
  - `isPersistedCallDetail`(88~93) + `matchesCallDetailFields`(105~136): 칸 24개 모두 선택, 단 **빈 객체 `{}`는 거부**, `id`는 있으면 빈 글자 아님, `clientId` 글자 또는 `null`, `cargoTonnage` 글자 또는 0 이상 유한 숫자, `fare`·`insuranceFee` 금액 규칙, `payments`는 입금 항목 목록.
  - `isValidCallDetail`(100~102): 위 + `id` 꼭 있음.
- 부르는 곳: `lib/durablePatchSchema.js`(`isValidCallDetail`, 저장 대기 기록), `store/persistDayRecord.js`(`isPersistedCallDetail`·`isValidCurrencyAmount`, 하루 기록 읽기), `store/persistDayRecordLegacy.js`(`isValidCurrencyAmount`), `lib/hydrateMergeWork.js`(`ALLOWED_CALL_DETAIL_KEYS`·`isPersistedCallDetail`, 서버 기록 합치기 — 칸 목록으로 모르는 칸을 걸러 냄).
- 기존 테스트 `lib/callDetailSchema.test.js` 2개(금액 규칙, id 필수 여부). 칸마다 검사하는 테스트는 없음.
- 2/6 주의점 그대로 적용: Valibot은 배열도 객체로 받음 → 입금 항목(칸 모두 선택)·콜 상세는 배열 거부를 객체 검사 **앞**에 둔다.

### 목표 상태 (기대 동작)
1. `isPersistedCallDetail`·`isValidCallDetail`·`isValidCurrencyAmount`가 어떤 입력에도 지금과 같은 참/거짓(이름·인자·타입 좁히기 그대로).
2. 칸 검사 if(수수료·입금 항목·콜 상세 24칸)가 Valibot 선언으로 바뀐다.
3. `ALLOWED_CALL_DETAIL_KEYS`는 그대로 내보내되, 선언의 칸 이름에서 만들어 두 곳에 따로 적지 않는다(같은 24칸·같은 순서 — `hydrateMergeWork.js`가 그대로 씀).
4. 긴 설명·경위 주석은 결론 1~2줄로(경위는 이 지시서와 git 기록에 있음).

### 건드릴 파일
- `react-app/src/lib/callDetailSchema.js` — 칸 검사를 Valibot 선언으로, 주석 줄임.
- 새 `react-app/src/lib/callDetailSchema.fields.test.js` — 동작 기록 테스트(기존 테스트 파일은 그대로 둠).
- 공용 영향: 내보내는 이름·결과 그대로라 부르는 4개 파일은 안 고침.

### 안 건드릴 것
- `isValidCurrencyAmount`의 금액 규칙(정규식 포함), `domain/money.js`.
- `persistDayRecord.js`·`persistDayRecordLegacy.js`(4/6·5/6), `durablePatchSchema.js`, `hydrateMergeWork.js`.

### 검증
- **먼저** 새 테스트를 지금 코드에 대고 써서 통과 → 바꾼 뒤 그대로 통과:
  ① 24칸 모두 올바른 콜 → 통과 ② 칸마다 틀린 종류 하나씩 → 거부(손으로 적은 목록) ③ 모르는 칸 → 거부 ④ 빈 객체 `{}`·빈 글자 `id` → 거부, `id` 없는 옛 콜 → 통과(`isValidCallDetail`은 거부) ⑤ 배열·`null`·글자 → 거부 ⑥ 값이 `undefined` → 거부 ⑦ `clientId: null` 통과, `cargoTonnage` 글자·0·소수 통과, 음수·NaN·무한대 거부 ⑧ 금액 칸에 "1,000원"·빈 글자 통과, "-1"·"1,00" 거부 ⑨ 수수료: `type`·`value` 빠지면 거부, `null`이면 통과, `enabled` 빠지면 거부, 모르는 칸 거부 ⑩ 입금 항목: 빈 항목 `{}` 통과, 배열 항목 `[]` 거부, 칸마다 틀린 종류 거부, `payments`가 목록이 아니면 거부 ⑪ `ALLOWED_CALL_DETAIL_KEYS`가 지금 24칸·순서와 같다.
- 테스트가 진짜 잡는지: 입금 항목의 배열 거부를 빼 보면 ⑩이 실패하는지 확인 → 원래대로.
- 기존 `callDetailSchema.test.js`·`persistDayRecord`·`hydrateMergeWork`·`durablePatch` 관련 테스트 통과. `npm test`·`npm run typecheck`·`npm run build` 통과, 타입 꼼수 0.
- 개발 서버 비회원: 콜 상세(운임·입금 일부·수수료 거래처) 넣고 새로 고침 → 막기 화면 없이 그대로, 백업 다시 불러오기 정상.

### 파일 길이
- `callDetailSchema.js` 136 → 약 85(주석 줄임 포함).
- 새 테스트는 테스트 파일 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 테스트가 바꾸기 전·후 다르게 나오면(지시서 설계 안의 순서·설정 실수가 아닌 경우) 고치지 않고 수정 착수지시서로 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤)
- 일지에서 콜 상세(운임·입금 기록 있는 것)를 열어 전과 그대로, 앱을 다시 켜도 그대로면 성공.

### 확인할 점
- 없음.
