# docs/report.md — 현재 슬라이스 착수지시서

141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 145 설정을 한 번도 저장 안 한 기사 차량은 세부 입력을 켤 수 없음 `[x]`

전수점검 K2·B7(`docs/full-code-review.md` 63·144·352줄). 보리 "ㅇㅇ"(2026-10-30) — 141 다음 순서. DB 변경 없음.

### 쉬운 요약
- 기사 차량에서 고정 노선을 끄면 "운행일지 세부 입력"은 자동으로 켜져야 합니다(앱 설정 화면 안내 "고정 노선을 끄면 세부 입력이 자동으로 필수 설정됩니다", 메인 차량과 같은 규칙).
- 그런데 그 기사 차량의 앱 설정을 한 번도 바꾼 적이 없으면 이 규칙이 안 먹어서, 세부 입력이 "꺼짐 + 잠김"으로 보이고 그 차량 일지에 운행을 넣을 칸이 없습니다(고정 노선도 꺼져 있어서).
- 설정을 저장 안 한 기사 차량도 같은 규칙을 따르게 합니다. 지금 서버에 이 상태인 기사 차량이 5대 있고, 고치면 이 차량들의 세부 입력이 켜짐으로 보입니다.

### 현재 상태 (코드 확인)
- 차량별 기본값 `domain/practiceSettings.ts` `defaultCarSettings()` = `normalizeCarSettings()` — 고정 노선 켜짐으로 가정해 `callDetail: false`.
- 저장값이 있는 기사 차량은 읽을 때 `normalizeSubCarSettingsMap`이 `subFixedOn`을 넘겨 "꺼지면 세부 입력 켬"이 먹음. 저장값이 없을 때만 위 기본값을 그대로 씀:
  - 일지·달력·서류 `domain/carSettingsScope.ts:11` `resolveLogSettings`
  - 앱 설정 화면 `features/settings/ui/AppSettingsPage.jsx:36`(보이는 값), `:58`(처음 저장할 때 바탕값)
- 앱 설정 화면은 고정 노선이 꺼져 있으면 세부 입력 스위치를 잠금(`AppSettingsPage.jsx:148` `disabled={!fixedOn}`) → 꺼진 채로 못 바꿈. 우회는 고정 노선을 켰다가 세부 입력 켜고 다시 끄기.
- 서버(2026-10-30 읽기, 보관 안 된 기사 차량 10대): 고정 노선 꺼짐·차량 설정 없음 5대(이번에 고쳐지는 차량), 꺼짐·있음 1대, 켜짐·없음 2대, 켜짐·있음 2대.

### 목표 상태 (기대 동작)
1. 기사 차량 고정 노선이 꺼져 있고 그 차량 설정을 저장한 적이 없으면, 앱 설정 화면 "운행일지 세부 입력"이 켜짐(잠김)으로 보이고 아래 항목 칩이 나옴.
2. 같은 차량의 운행일지에 세부 입력 추가 단추가 보임(일지·달력·서류가 같은 값을 읽음).
3. 고정 노선이 켜진 기사 차량의 기본값은 지금과 같음(세부 입력 꺼짐).
4. 설정을 이미 저장한 기사 차량, 메인 차량은 그대로.

### 건드릴 파일
- `src/domain/practiceSettings.ts` — `defaultCarSettings(subFixedOn = true)`로 받아 `normalizeCarSettings({}, subFixedOn)`.
- `src/domain/carSettingsScope.ts` — 저장값 없을 때 `defaultCarSettings(settings.subFixedOn ?? true)`.
- `src/features/settings/ui/AppSettingsPage.jsx` — 36줄 `defaultCarSettings(fixedOn)`(37줄 `fixedOn`을 한 줄 위로), 58줄 `defaultCarSettings(current.subFixedOn ?? true)`.
- 주석 추가 없음(`normalizeCarSettings` 위 설명이 이미 규칙을 적고 있음).
- 테스트:
  - `domain/practiceSettings.test.js` — `defaultCarSettings(false).callDetail`이 true. 인자 없이는 지금처럼 false(기존 테스트 그대로).
  - `domain/carSettingsScope.test.js` — 기사 차량 고정 노선 꺼짐·저장값 없음 → `callDetail` true.
  - 새 파일 `features/settings/ui/AppSettingsPage.subCallDetail.test.js` — 기사 차량 앱 설정(`/logs/11가1111/settings`), 고정 노선 꺼짐·저장값 없음 → 세부 입력 스위치 켜짐·잠김, 항목 칩 보임. 고정 노선 켜짐이면 꺼짐(안전 확인). 화면 그리는 방식은 `AppSettingsPage.dailyInspection.test.js`와 같게.

### 안 건드릴 것
- 저장된 기사 차량 설정 읽기(`normalizeSubCarSettingsMap`, 이미 규칙 따름), 메인 차량 설정(`normalizeSettings`).
- 세부 입력 스위치 잠금·안내 문구, 고정 노선 스위치 동작.
- 서버 설정 자료(5대 차량 설정을 새로 저장하지 않음 — 읽을 때 기본값만 바뀜).

### 검증
- `npm test`, 타입 검사, 코드 검사, 의존 검사, 빌드(CI).
- 새 테스트가 고치기 전 코드에서 실패하는지 확인(revert-check). 고정 노선 켜짐 기본값 테스트는 안전 확인.
- 로컬 비회원 화면: 기사 차량 등록 → 메인 설정은 그대로, 기사 차량 고정 노선만 끔 → 그 차량 앱 설정 세부 입력 켜짐·칩 보임 → 그 차량 일지에 세부 내역 추가 단추 → 시험 차량 지움.
- [x] 때 sot에 "설정 저장 안 한 기사 차량도 고정 노선 꺼지면 세부 입력 켬" 한 줄.

### 파일 길이
- `practiceSettings.ts` 123 → 123, `carSettingsScope.ts` 12 → 12, `AppSettingsPage.jsx` 182 → 182. 모두 200 이하(테스트 파일은 예외).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 기본값 만드는 곳에 고정 노선 값을 넘기는 것만 바꿈. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 배포본 차량 관리에서 기사 차량 하나(예: 99가9998)의 운행일지 → 앱 설정을 엶.
2. 고정 노선이 꺼져 있으면 "운행일지 세부 입력"이 켜짐(잠김)으로 보이고 아래 항목 칩이 나오면 성공.
3. 그 차량 운행일지 하루 화면에 "+ 세부 내역 추가"가 보이면 성공.

### 진행 기록
- 코드 커밋 react-app `c18d66b`. 전체 테스트(단위 956·화면 934), 타입 검사, 코드 검사, 의존 검사, 빌드 통과.
- 고치기 전 실패 확인(revert-check): 새 테스트 4개 중 3개(기본값·일지 설정 읽기·앱 설정 화면 꺼짐 차량) 고치기 전 실패 → 고친 뒤 통과. "고정 노선 켜짐 → 세부 입력 꺼짐"은 고치기 전에도 통과 = 안전 확인.
- 로컬 비회원 화면: 기사 차량 33가3333 시험 등록, 기사 차량 고정 노선만 끔(차량 설정 저장 없음) → 그 차량 앱 설정 세부 입력 켜짐·잠김·항목 칩 4개, 하루 일지에 "+ 세부 내역 추가". 시험 차량·설정 원래대로.
- push 뒤 CI verify·deploy 초록(run 41052308645). 배포본 seed 계정: 99가9998은 기사 차량 고정 노선 켜짐이라 세부 입력 꺼짐·안 잠김(지금과 같음, 안전 확인). 배포 파일에 새 기본값 넘기기(`subFixedOn ?? true`) 들어 있음 확인. 고정 노선 꺼진 경우는 계정 자료를 바꿔야 해서 로컬·자동 테스트로 확인.
- §5 리뷰: 바뀐 파일 6개 = 건드릴 파일(소스 3·테스트 2·새 테스트 파일 1), 새 주석 없음(새 테스트 파일 첫 줄 설명만), 새 저장 장치·타입 꼼수 없음, 기존 테스트 줄 삭제 없음, 소스 모두 200줄 이하.
