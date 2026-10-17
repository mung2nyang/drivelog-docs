# docs/report.md — 현재 슬라이스 착수지시서

## 검사 라이브러리 마무리 — 파일마다 따로 있는 Valibot 작은 도우미를 한 파일로 `[x]`

> **진행(2026-10-17)**: 보리 "작업진행해". 코드 커밋 react-app `c8dae0b` — push 뒤 CI verify·deploy 초록(run 39038736984).
> - 바뀐 파일(지시서 목록과 같음): 새 `domain/schemaParts.js` 30줄(`isPlainObject`·`opt`·`str`·`bool`·`finiteNumber`·`stringOrNumber`·`nonEmptyString`·`by`·`plain(스키마, 덧붙일 조건?)`, 함정 두 가지 주석), 새 `domain/schemaParts.test.js` 5개. 6개 파일 줄 수: `persistDomainRecords.js` 127→106(1/6의 `optional()` 지우고 칸마다 `opt`, `isPlainObject`는 공용 것을 다시 내보냄, `isFiniteNumber`·`hasOnlyKeys` 안에서만), `persistDomainSchema.js` 102→93, `callDetailSchema.js` 83→75(`isPlainObject` 한 벌 지움), `persistDayRecord.js` 71→66, `persistDayRecordLegacy.js` 60→54, `persistDomainInvoice.js` 40→35.
> - 3/6의 "수수료 값은 숫자면 NaN도 받음"(`typeof`)과 하루 기록의 `count`(0 이상 정수)는 공용과 다른 규칙이라 각 파일에 그대로 둠.
> - 6개 슬라이스 테스트 77개 + 기존 테스트 전부 그대로 통과(결과 안 바뀜). 새 함정 테스트: Valibot 그대로는 배열 통과, `plain()`은 배열 거부, 덧붙일 조건(빈 객체 거부), 빈 값 거부, NaN·무한대 거부.
> - 테스트가 진짜 잡는지: `plain()`의 배열 거부를 객체 검사 **뒤**로 옮기면 "[]" 실패 확인 → 원래대로.
> - `npm test` 1033 + 485 통과, 타입 검사·빌드·린트 통과, 타입 꼼수 0. 묶음 222.45 → 222.17KB(압축, 줄어듦).
> - 개발 서버 비회원: 콜(입금 2건·수수료·`clientId: null`·톤수 소수)·설정(어두운 화면·켜기/끄기)·세금계산서 넣고 새로 고침 → 막기 화면 없이 그대로, 백업 다시 불러오기 정상. 콘솔 오류 없음.
> - 지시서와 다른 점: 없음. 진행 중 실수 1건(5/6 파일 수정 때 `JsonValue` 타입 줄을 같이 지워 타입 검사 오류 → 바로 되살림, 커밋 전).

검사 라이브러리 6/6 `[x]`(`7bac155`) 뒤 AI 제안 → 보리 "응 진행해"(2026-10-17).

### 쉬운 요약
- 6개 검사 파일에 같은 작은 도우미(선택 칸 표시, 글자, 유한 숫자, 글자·숫자, 배열 거부, 우리 함수로 검사)가 이름만 조금씩 다르게 따로 있습니다. 한 파일로 모아 6개 파일이 가져다 씁니다.
- 앞에서 찾은 함정 두 가지("배열 거부는 객체 검사 **앞**에", "Valibot 숫자는 NaN 거부")를 그 한 파일에 주석과 테스트로 못 박아, 다음에 검사를 새로 만들 때 반복하지 않게 합니다.
- 검사 결과는 한 건도 안 바뀝니다(6개 슬라이스 테스트 77개가 그대로 지킴).

### 현재 상태 (코드 확인)
- 같은 일을 하는 도우미가 파일마다 따로(`grep`):
  | 파일 | 따로 적은 도우미 |
  |---|---|
  | `store/persistDomainRecords.js`(1/6) | `str`·`bool`·`num`·`strOrNum`, 칸 묶음을 통째로 선택으로 바꾸는 `optional()` |
  | `store/persistDomainSchema.js`(2/6) | `opt`·`str`·`bool`·`num`·`plain`·`by` |
  | `lib/callDetailSchema.js`(3/6) | `opt`·`str`·`plainObject`, 그리고 공용과 똑같은 `isPlainObject` 한 벌 더 |
  | `store/persistDayRecord.js`(4/6) | `opt`·`by`·`plainObject` |
  | `store/persistDayRecordLegacy.js`(5/6) | `opt`·`str`·`strOrNum`·`plain` |
  | `store/persistDomainInvoice.js`(6/6) | `opt`·`str`·`num`·`plain` |
- 배열 거부 도우미가 4개 이름(`plain`·`plainObject`)·2가지 모양(빈 객체 거부 덧붙이기 있음/없음), 선택 칸 표시가 2가지 방식(칸마다 `opt` / 묶음 `optional()`).
- 옛 도우미: `isFiniteNumber`·`hasOnlyKeys`는 이제 `persistDomainRecords.js` 안에서만 씀(밖에서 쓰는 곳 0, `grep -rln`). `isPlainObject`(12개 파일)·`isStringOrFiniteNumber`(2개 파일)·`isStringRecord`(1개 파일)는 밖에서 씀.

### 목표 상태 (기대 동작)
1. 새 `src/domain/schemaParts.js` 한 파일에 공용 도우미: `isPlainObject`, `opt`, `str`, `bool`, `finiteNumber`, `stringOrNumber`, `nonEmptyString`, `by(검사 함수)`, `plain(스키마, 덧붙일 조건?)`(배열 거부를 객체 검사 **앞**에). 함정 두 가지는 이 파일 주석 1줄씩.
2. 6개 검사 파일은 따로 적던 도우미를 지우고 이 파일에서 가져다 씀. 선택 칸 표시는 칸마다 `opt` 한 가지로(1/6의 `optional()` 지움).
3. `callDetailSchema.js`의 `isPlainObject` 한 벌 지움.
4. `persistDomainRecords.js`의 `isPlainObject`는 공용 파일 것을 그대로 다시 내보내서 이를 쓰는 12개 파일은 안 고침. `isFiniteNumber`·`hasOnlyKeys`는 밖으로 안 내보냄(안에서만 씀).
5. 검사 결과는 한 건도 안 바뀐다. 3/6의 "수수료 값은 숫자면 NaN도 받음"(`typeof`)은 그 파일에 그대로 둔다(공용 `finiteNumber`와 다른 규칙이라).

### 건드릴 파일
- 새 `react-app/src/domain/schemaParts.js`(약 35줄) — `lib`·`store` 둘 다 이미 `domain`을 부르므로 여기 둠(`lib`→`store` 부르기를 새로 만들지 않음, 로드맵 59번 방향).
- 새 `react-app/src/domain/schemaParts.test.js` — 함정 두 가지를 못 박는 테스트(배열 `[]`·`[1]` 거부, `{}` 통과, 빈 객체 거부 조건, `finiteNumber` NaN·무한대 거부, `exactOptional` 빈 값 거부).
- `react-app/src/store/persistDomainRecords.js`, `persistDomainSchema.js`, `persistDayRecord.js`, `persistDayRecordLegacy.js`, `persistDomainInvoice.js`, `react-app/src/lib/callDetailSchema.js` — 도우미를 가져다 쓰게.
- 공용 영향: 밖으로 내보내는 검사 함수·`isPlainObject`·`isStringOrFiniteNumber`·`isStringRecord`·칸 목록은 이름·결과 그대로라 이를 쓰는 파일은 안 고침. `isFiniteNumber`·`hasOnlyKeys` 내보내기를 빼는 것은 밖에서 쓰는 곳 0개(위 grep).

### 안 건드릴 것
- 각 파일의 칸 선언 내용(칸 이름·규칙) — 도우미 이름만 바뀜.
- 금액 규칙 `isValidCurrencyAmount`, 날짜별 읽기, 종류 고르기.
- `isPlainObject`를 쓰는 12개 파일의 가져오는 곳(그대로 `persistDomainRecords.js`에서).

### 검증
- 6개 슬라이스 테스트 77개 + 기존 테스트 전부 그대로 통과(결과 안 바뀜의 증명).
- 새 `schemaParts.test.js`: 고치기 전엔 파일이 없어 실패(새 파일) → 만든 뒤 통과. 테스트가 진짜 잡는지: `plain`의 배열 거부를 객체 검사 뒤로 옮기면 실패하는지 확인 → 원래대로.
- `npm test`·`npm run typecheck`·`npm run build`·린트 통과, 타입 꼼수 0. 묶음 크기 같거나 조금 줄어야 함.
- 개발 서버 비회원: 거래처·콜(입금·수수료)·설정·세금계산서 넣고 새로 고침 → 막기 화면 없음, 백업 다시 불러오기 정상.

### 파일 길이
- 6개 파일은 도우미 줄만큼 줄어듦(파일마다 약 3~10줄). 새 파일 약 35줄. 모두 200줄 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 테스트 결과가 하나라도 바뀌면 고치지 않고 수정 착수지시서로 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤)
- 앱을 다시 열어 차량·거래처·일지·설정·세금계산서가 전과 그대로면 성공(바뀌는 게 없어야 정상).

### 확인할 점
- 없음.
