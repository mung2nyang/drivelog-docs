# docs/report.md — 현재 슬라이스 착수지시서

138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 148 매출 화면 운행 거리를 달력과 같은 규칙으로 `[x]`

전수점검 B6(`docs/full-code-review.md` 45줄). 보리 "응"(2026-10-30) — ② 다음 순서 148 → 142 → 104 → … 의 첫 번째, 79는 자문 뒤로. DB 변경 없음.

### 쉬운 요약
- 매출 화면 "총 N회 운행 / N km"의 거리를 달력 정산 카드와 다른 방식으로 셉니다. 소수점을 지워 12.5km를 125km로 읽고, 콜 상세에 거리가 없을 때 쓰는 "하루 거리"는 아예 안 셉니다.
- 매출도 달력과 같은 함수 하나로 거리를 세게 합니다.
- 지금 서버 자료에서는 숫자가 바뀌지 않습니다(콜 상세 거리 254건 중 소수점 0건, 하루 거리는 서버에 저장하는 칸이 없음). 계기판 숫자로 세는 거리는 늘 정수라, 소수점·하루 거리는 비회원 휴대폰의 옛 자료에서만 나옵니다.

### 현재 상태 (코드 확인)
- 매출 `domain/financeOwnerDetail.ts:94` — 콜 상세마다 `parseCurrencyValue(detail.distanceKm)`(돈 읽는 함수라 숫자 아닌 글자·소수점을 지움), 하루 거리(`dailyDistance`)는 안 봄. 136줄 `Math.round`.
- 달력 `domain/monthSettlement.ts:17-25` `getRecordTotalDistance`(이 파일 전용) — 콜 상세에 거리가 하나라도 있으면 그 합(`parseFloat`), 없으면 하루 거리. 휴무 날 뺌(78줄).
- 매출 거리는 `OwnerMonthlyCards.jsx:77`(차주·기사·전체 손익), 기사 본인 화면(`driverSelfRevenue.ts`가 같은 함수 결과를 씀)에 보임.
- 계기판 입력 거리 `call-details.ts:82-91` `computeDistanceKm`은 정수(돈 읽는 함수로 읽음) — 이번 범위 밖.
- 서버 읽기 조회(2026-10-30): `transport_details.distance_km`(numeric) 값 있는 254건 중 소수점 0건.

### 목표 상태 (기대 동작)
1. 매출의 한 달 거리 = 휴무가 아닌 날마다 달력과 같은 규칙(콜 상세 거리 합, 없으면 하루 거리)으로 센 합. 소수점 그대로 더함("12.5" + "7.5" = 20).
2. 콜 상세에 거리가 하나라도 있으면 하루 거리는 안 더함(달력과 같음).
3. 휴무 날 거리는 안 셈(지금과 같음).
4. 화면 표시는 지금처럼 반올림 정수 km.
5. 달력 거리 계산은 그대로(같은 함수를 옮겨 같이 씀).

### 건드릴 파일
- `src/domain/day-record.ts` — `getRecordTotalDistance`를 `monthSettlement.ts`에서 옮겨 내보냄(내용 그대로). 주석 최종 문구: `/** 하루 운행 거리(km) — 콜 상세에 거리가 하나라도 있으면 그 합, 없으면 하루 거리(dailyDistance). */`
- `src/domain/monthSettlement.ts` — 파일 전용 함수를 지우고 `day-record.ts`에서 불러옴.
- `src/domain/financeOwnerDetail.ts` — 94줄 지우고, 휴무 아닌 날마다 `distanceKm += getRecordTotalDistance(record)`.
- 테스트 `domain/finance.test.js` — 새 테스트: 소수점 거리(12.5 + 7.5 = 20), 콜 상세에 거리 없는 날은 하루 거리, 둘 다 있으면 콜 상세만, 휴무 날 뺌, 같은 자료로 달력(`monthSettlementSummary`)과 매출 거리가 같음.

### 안 건드릴 것
- 계기판 입력 거리(`computeDistanceKm`), 콜 상세 카드·목록의 거리 표시(`CallDetailCard.jsx`·`CallDetailList.jsx`, 이미 `parseFloat`).
- 매출 운행 횟수 셈(`financeOwnerDetail.ts:72-79`가 `callCountsAsTrip`과 같은 규칙을 따로 씀) — 결과는 같아 이번 범위 밖.
- 달력 거리 소수점 표시(`CalendarMonthSummary.jsx:68` 반올림 없음), 운송비 내역서 거리 — 142번에서 같이 봄.

### 검증
- `npm test`, 타입 검사, 코드 검사, 의존 검사, 빌드(CI).
- 새 테스트(소수점·하루 거리·달력과 같음)가 고치기 전 코드에서 실패하는지 확인(revert-check). 휴무 날·콜 상세 우선은 안전 확인. 기존 `finance.test.js:99`(40km)·`monthSettlement.test.js` "6. 실거리"는 그대로 통과해야 함.
- 배포본: 아래 확인 방법.
- [x] 때 sot에 "거리는 달력·매출 같은 함수" 기록.

### 진행 기록
- 2026-10-30 보리 "응"(이대로 진행) → 데스크톱 AI가 코드. react-app `5854a32`(push 안 함).
- 로컬: 테스트 952·926 모두 통과, 타입 검사·코드 검사·의존 검사(위반 0)·빌드 통과. 파일 길이 187·137·156. 기존 `finance.test.js` 40km·`monthSettlement.test.js` "6. 실거리" 그대로 통과.
- 고치기 전 실패 확인 — 새 테스트 2개(소수점·하루 거리·휴무 날, 달력과 같은 값) 모두 고치기 전 코드에서 실패. 되돌린 파일 3개 원래대로 복구 확인.
- 로컬 화면 확인은 안 함 — 비회원 시험 자료에 거리가 없고 서버 자료는 정수 거리뿐이라 화면 숫자가 바뀌지 않음(지시서 확인 방법도 배포본만).
- push 뒤 CI verify·deploy 초록(run 41037279148).
- 2026-10-30 보리 "웅"(확정) → `[x]`, sot §4-24에 규칙 기록.
- 배포본(getdrivelog.com, 로그인 탭): 매출 10월 "총 29회 운행 / 0km / 0시간" — 배포 전(같은 날 138-A 확인 때)과 같음. 이 계정은 서버 콜 상세 거리 0 초과 0건(읽기 조회)이라 거리 숫자가 바뀌는 화면은 없음 — 거리 규칙은 자동 테스트로 확인.
- §5 리뷰: 바뀐 파일 4개 = "건드릴 파일"과 같음, 새 파일·저장 키·대비 장치·타입 꼼수·로드맵 번호 주석 없음, 새 주석은 지시서 문구 그대로, 지운 테스트 없음, 파일 길이 187·137·156, 목표 1~5 충족(1~3은 새 테스트, 4는 `Math.round` 그대로, 5는 기존 달력 테스트 통과).

### 파일 길이
- `day-record.ts` 177 → 약 186(200줄 감시 목록, 넘지 않음), `monthSettlement.ts` 147 → 약 138, `financeOwnerDetail.ts` 156 → 약 156.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 세는 함수만 하나로 모음. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 배포본 매출 → 월 매출 → 전체 손익·차주·기사 탭의 "총 N회 운행 / N km"가 전과 같음(서버 자료는 정수 거리뿐이라 숫자 그대로).
2. 운행 거리를 쓰는 차량의 달력 정산 카드 거리와 매출 차주 탭 거리가 같음.
3. (소수점·하루 거리는 서버에 없어 화면에서 볼 일이 없음 — 자동 테스트로 확인.)
