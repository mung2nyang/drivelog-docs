# docs/report.md — 현재 슬라이스 착수지시서

## 저장 자료 검사를 검사 라이브러리로 (2/6) — `persistDomainSchema.js`(설정·개인정보) `[x]`

> **진행(2026-10-17)**: 보리 "작업진행해". 코드 커밋 react-app `d7541f3` — push 뒤 CI verify·deploy 초록(run 39034809148).
> - 바뀐 파일(지시서 목록과 같음): `store/persistDomainSchema.js` 121→102(`if (` 36→10, 남은 10개는 `matchesDomainSchema`의 종류 고르기), 새 `store/persistDomainSchema.test.js` 10묶음.
> - 동작 기록 테스트(설정 37칸 칸마다 틀린 값 54건, 개인정보 12칸, 지운 날, 입구 10종류, 빈 배열·`null`·값 비어 있음·NaN/무한대)를 **고치기 전 코드에서 먼저 통과** → 바꾼 뒤 그대로 통과.
> - **진행 중 고친 것(지시서 설계 안)**: 배열 거부를 처음엔 객체 검사 **뒤**에 붙였더니 테스트 5묶음 실패 — Valibot은 객체 검사를 통과하면 새 객체를 넘겨서 뒤에서는 배열인지 알 수 없음. 배열 거부를 객체 검사 **앞**으로 옮겨 통과(같은 도우미 `plain()` 1개, 순서만). 1/6 파일은 꼭 있는 칸 덕분에 영향 없음(1/6 테스트로 확인).
> - 테스트가 진짜 잡는지: 개인정보 검사에서 배열 거부를 빼면 2묶음 실패 확인 → 원래대로.
> - `npm test` 995 + 485 통과(기존 `notifOff`·`owner-state`·`guestBackup`·`persist` 포함), 타입 검사·빌드 통과, 타입 꼼수 0. 묶음 222.50 → 222.72KB(압축).
> - 개발 서버 비회원: 어두운 화면·콜 상세·거리·결제 켜고 새로 고침 → 막기 화면 없이 설정 그대로, 백업 다시 불러오기 → "백업 데이터를 복원했습니다.". 콘솔 오류 없음.
> - 지시서와 다른 점: 없음(위 순서 고침은 설계 안).

1/6 `[x]`(`6486657`, Valibot 들임 + `persistDomainRecords.js`) 다음. 보리 결정(2026-10-17): 같은 라이브러리로 6개 파일 한 파일씩 이어서.

### 쉬운 요약
- 앱 설정·개인정보·기사 연결·노선·기사 차량별 설정이 올바른 모양인지 검사하는 if 36개를 Valibot 선언으로 바꿉니다.
- 검사 결과(통과/거부)는 한 건도 바뀌면 안 됩니다. 이 파일이 앱을 켤 때와 비회원 백업 불러오기의 **모든 자료 검사 입구**(`matchesDomainSchema`)라, 틀리면 비회원이 막기 화면에 갇힙니다 — 1/6처럼 동작 기록 테스트를 먼저 씁니다.

### 현재 상태 (코드 확인)
- `react-app/src/store/persistDomainSchema.js` 121줄, `if (` 36개.
  - `isPersistedDriverLink`(40~43, 칸 5개 모두 글자·모두 선택), `isRoutePreset`(46~49, 칸 3개 모두 꼭 있는 글자), `isPersistedSubCarSettings`(52~59, 칸 7개 모두 선택), `isPersistedSettings`(62~91, 칸 37개 모두 선택: 켜기/끄기 14·글자 8·허용 값 4·목록 9·`unitPrice` 글자/숫자·`subCarSettings` 묶음), `isPersistedProfile`(94~97, 칸 12개 모두 글자·모두 선택), `isPersistedWorkDataDeletedDates`(100~103, 날짜 → 글자).
  - `matchesDomainSchema`(109~121) — 자료 종류별로 위 검사를 고르는 입구(if 10개는 "어느 종류인가" 고르기라 검사 if가 아님).
- 부르는 곳: `matchesDomainSchema`만 밖에서 씀 — `store/persistDomainRead.js`(앱 켤 때 읽기), `lib/guestBackup.js`(비회원 백업 불러오기)(`grep -rn persistDomainSchema src`). 나머지 내보내기(`isPersistedSettings`·`isPersistedProfile`·`isPersistedWorkDataDeletedDates`)는 이 파일 안에서만 쓰임.
- 테스트: `persistDomainSchema.notifOff.test.js`(설정 `notifOff` 칸 1건)와 저장 경로 테스트들이 간접 검사. 칸마다 검사하는 테스트는 없음.
- **조사에서 찾은 주의점**: Valibot의 `strictObject`·`record`는 **배열도 객체로 받아 통과**시킴(AI 임시 시험: `[]`·`[true]` 통과). 지금 코드는 `isPlainObject`로 배열을 거부. 1/6의 4개 검사는 꼭 있는 칸(`number`·`id`)이 있어 배열이 걸러졌지만(1/6 테스트 "객체 아님" 통과로 확인), 이번 파일은 **칸이 모두 선택인 검사가 4개**(기사 연결·기사 차량별 설정·설정·개인정보)와 날짜 묶음·`subCarSettings` 묶음이 있어 그대로 옮기면 배열이 통과됨 → 배열 거부를 덧붙여야 함.

### 목표 상태 (기대 동작)
1. `matchesDomainSchema`와 내보내는 3개 검사가 어떤 입력에도 지금과 같은 참/거짓(이름·인자·내보내기 그대로, `isPersistedProfile`의 타입 좁히기도 그대로).
2. 검사 if 36개가 Valibot 선언으로 바뀌고, 따로 적던 칸 목록(`DRIVER_LINK_KEYS`·`ROUTE_PRESET_KEYS`·`SUB_CAR_SETTINGS_KEYS`·`SETTINGS_KEYS`·`PROFILE_KEYS`)은 선언 안으로 들어가 없어진다.
3. 배열은 모든 객체 자리에서 지금처럼 거부(배열 거부를 붙인 작은 도우미 1개, 파일 안에서만).
4. 차량·거래처 목록 검사는 1/6의 `isPersistedCar`·`isPersistedClient`를 그대로 씀(1/6 파일은 안 고침).
5. `matchesDomainSchema`의 종류 고르기는 그대로(검사가 아니라 입구).

### 건드릴 파일
- `react-app/src/store/persistDomainSchema.js` — 검사 if를 Valibot 선언으로.
- 새 `react-app/src/store/persistDomainSchema.test.js` — 동작 기록 테스트(아래).
- 공용 영향: 내보내는 이름·결과 그대로라 부르는 `persistDomainRead.js`·`guestBackup.js`는 안 고침.

### 안 건드릴 것
- `persistDomainRecords.js`(1/6), `persistDayRecord.js`·`persistDomainInvoice.js`(이 파일이 부르지만 각자 다른 슬라이스), `persistDomainEnums.js`(읽기만).
- 저장 키·형식·화면.

### 검증
- **먼저** 새 테스트를 지금 코드에 대고 써서 통과 → 바꾼 뒤 그대로 통과. 검사마다:
  ① 모든 칸 올바름 → 통과 ② 칸마다 틀린 종류 하나씩 → 거부(설정 37칸 전부, 손으로 적은 목록) ③ 모르는 칸 → 거부 ④ 꼭 있는 칸 빠짐 → 거부(노선) ⑤ 빈 객체 `{}` → 통과(모두 선택인 검사) ⑥ **배열·`null`·글자·숫자 → 거부(빈 배열 `[]` 꼭 포함)** ⑦ 값이 `undefined` → 거부 ⑧ 숫자 칸 NaN·무한대 → 거부 ⑨ 목록 칸 안에 틀린 항목 하나 → 거부(차량·거래처·기사 연결·노선·횟수·고정 위치·끈 알림) ⑩ `subCarSettings`: 배열·틀린 차량 설정 → 거부, 빈 묶음 → 통과 ⑪ 지운 날 묶음: 날짜 아닌 키·글자 아닌 값·배열 → 거부.
  - `matchesDomainSchema`: 10개 종류 각각 올바른 값 통과·틀린 값 거부, 모르는 종류 → 거부.
- 테스트가 진짜 잡는지: 바꾼 코드에서 배열 거부를 일부러 빼 보면 ⑥이 실패하는지 확인 → 원래대로.
- 기존 `persistDomainSchema.notifOff.test.js`·`owner-state`·`guestBackup`·`persist` 테스트 통과. `npm test`·`npm run typecheck`·`npm run build` 통과, 타입 꼼수 0.
- 개발 서버 비회원: 앱 설정 여러 개 바꾸고(입력 방식·켜기/끄기·노선·고정 위치) 새로 고침 → 막기 화면 없이 그대로, 백업 만들어 다시 불러오기 → "백업 데이터를 복원했습니다.".

### 파일 길이
- `persistDomainSchema.js` 121 → 약 100.
- 새 테스트는 테스트 파일 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 테스트가 바꾸기 전·후 다르게 나오면 고치지 않고 수정 착수지시서로 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤)
- 앱을 다시 열어 설정(입력 방식·켜기/끄기·노선)과 개인정보가 전과 그대로면 성공.
- 비회원: 백업 저장 후 불러오기가 전처럼 된다.

### 확인할 점
- 없음(라이브러리·진행 방식은 1/6에서 정함). 위 "배열 통과" 주의점은 이번 테스트 ⑥으로 막습니다.
