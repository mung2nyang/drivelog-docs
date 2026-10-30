# docs/report.md — 현재 슬라이스 착수지시서

148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 142 기사 차량 운행거리 줄을 그 차량 설정으로 `[x]`

전수점검 G1·L1(`docs/full-code-review.md` 278·365줄). 보리 "ㅇㅇ"(2026-10-30) — 148 다음 순서. DB 변경 없음.

### 쉬운 요약
- "계기판"(운행거리 입력) 스위치는 차량마다 따로 저장되는데, 기사 차량의 달력 정산 카드와 운송비 내역서는 메인 차량 스위치를 봅니다.
- 그래서 기사 차량만 켜 두면 거리를 매일 넣어도 달력·내역서에 거리 줄이 안 나오고, 반대로 메인만 켜면 기사 차량 내역서에 거리 줄이 나옵니다(달력은 기사 차량이면 아예 안 나옴).
- 두 화면이 그 차량의 스위치를 보게 합니다. 일지 입력 화면·서류 화면은 이미 차량별로 읽음.

### 현재 상태 (코드 확인)
- 달력 `features/calendar/ui/CalendarPage.jsx:44-45` — `// 서브차량 실거리: subDistanceOn 설정이 없어 메인만.` + `isMain && !!settings.distanceOn`(설정은 공용 값, 차량별로 안 덮음).
- 내역서 `features/documents/ui/ReportPage.jsx:118` — `practiceSettings.distanceOn`(공용 = 메인 값). 이 화면은 `logKey`로 기사 차량 내역서도 그림(37줄).
- 차량별 값: 앱 설정 "계기판" 칩 `AppSettingsPage.jsx:68`이 보고 있는 차량 설정에 저장, 기사 차량은 `settings.subCarSettings[차량번호]`. 읽는 함수 `domain/carSettingsScope.ts` `resolveLogSettings(settings, logId)` — 일지(`MainPageRoute.jsx:37`)·서류 화면(`DocumentIssuePage.jsx:30`)이 이미 씀.
- 거리 계산 자체는 차량 상관없이 같음(sot §4-24) — 이번엔 줄을 보일지만.

### 목표 상태 (기대 동작)
1. 기사 차량 달력 정산 카드: 그 차량 "계기판"이 켜져 있고 거리가 있으면 "실 운행거리 N km" 줄이 보임. 꺼져 있으면 안 보임.
2. 기사 차량 운송비 내역서: 그 차량 "계기판"을 따름(메인 값과 무관).
3. 메인 차량 달력·내역서는 지금과 같음(메인 "계기판").
4. 거리 숫자·다른 줄은 그대로.

### 건드릴 파일
- `src/features/calendar/ui/CalendarPage.jsx` — 44-45줄을 `const distanceOn = !!resolveLogSettings(settings, logId).distanceOn`로(틀린 주석 지움), `resolveLogSettings` 불러오기.
- `src/features/documents/ui/ReportPage.jsx` — 118줄을 `!!resolveLogSettings(practiceSettings, logKey).distanceOn`로, 불러오기.
- 테스트:
  - `features/calendar/ui/CalendarPage.test.js` — 새 테스트: 메인 계기판 끔·기사 차량 계기판 켬, 기사 차량 콜 상세 거리 30 → 기사 차량 달력에 "실 운행거리 30 km".
  - `features/documents/ui/ReportPage.test.js` — 새 테스트: 같은 설정으로 기사 차량 내역서(`logId`)에 "월간 총 운행거리" 보임, 반대(메인 켬·기사 차량 끔)면 안 보임.

### 안 건드릴 것
- 거리 계산(`getRecordTotalDistance`)·달력 소수점 표시(`CalendarMonthSummary.jsx:68` 반올림 없음 — 계기판 거리는 늘 정수라 그대로).
- 일지 입력·콜 상세 카드 거리 표시(이미 차량별 설정), 서류 화면(`DocumentIssuePage.jsx`, 이미 차량별).
- 매출 화면 거리(설정 스위치 없이 늘 보임).

### 검증
- `npm test`, 타입 검사, 코드 검사, 의존 검사, 빌드(CI).
- 새 테스트가 고치기 전 코드에서 실패하는지 확인(revert-check). 메인 화면 기존 테스트는 그대로 통과(안전 확인).
- 로컬 비회원 화면: 기사 차량 등록 → 그 차량 앱 설정 "계기판" 켬 → 콜 상세에 계기판 숫자 넣어 저장 → 그 차량 달력에 "실 운행거리" 줄 → 운송비 내역서에 "월간 총 운행거리" → 시험 차량 지움.
- [x] 때 sot에 "거리 줄은 그 차량 계기판 설정" 기록.

### 파일 길이
- `CalendarPage.jsx` 89 → 약 89, `ReportPage.jsx` 166 → 약 167. 모두 200 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 설정을 읽는 곳만 바꿈. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 배포본 기사 차량(예: 22가2222)의 앱 설정에서 "계기판" 켬(메인은 그대로).
2. 그 차량 운행일지에서 콜 상세 하나에 계기판 출발·도착 숫자를 넣고 저장.
3. 그 차량 달력 정산 카드에 "실 운행거리 N km", 서류 → 운송비 내역서에 "월간 총 운행거리 N km"가 보이면 성공.
4. 확인 뒤 넣은 계기판 숫자와 "계기판" 스위치는 원래대로 되돌림.

### 진행 기록
- 코드 커밋 react-app `75c14b6`. 전체 테스트(단위 952·화면 928), 타입 검사, 코드 검사, 의존 검사, 빌드 통과.
- 새 테스트 2개 모두 고치기 전 코드에서 실패 → 고친 뒤 통과(revert-check).
- 로컬 비회원 화면: 기사 차량 22가2222 시험 등록, 그 차량 설정에서 계기판 켬(메인은 꺼짐) → 기사 차량 달력 "실 운행거리", 운송비 내역서 "월간 총 운행거리" 보임, 메인 달력엔 안 보임. 시험 차량·기록 지움.
- push 뒤 CI verify·deploy 초록(run 41043632877). 배포본 seed 계정: 22가2222는 계기판 꺼짐 → 그 차량 달력·운송비 내역서에 거리 줄 없음(설정 그대로, 데이터 안 바꿈).
- §5 리뷰: 바뀐 파일 4개 = 건드릴 파일과 같음, 새 저장 장치·타입 꼼수·새 주석 없음(틀린 주석 1줄 지움), 기존 테스트 줄 삭제는 불러오기 1줄 교체뿐, 200줄 이하.
