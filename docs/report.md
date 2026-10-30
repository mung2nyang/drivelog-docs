# docs/report.md — 현재 슬라이스 착수지시서

104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 141 휴무를 켰다 끄면 노선 칩 횟수·파렛트만 되살아남 `[x]`

전수점검 F1(`docs/full-code-review.md` 55·261줄). 보리 "ㅇㅇ"(2026-10-30) — 104 다음 순서. DB 변경 없음.

### 쉬운 요약
- 운행일지에서 휴무를 켜면 운행 횟수만 0이 되고, 노선 칩 횟수("인천 → 평택 2회")와 파렛트 장수는 안 보일 뿐 그대로 남습니다. 그래서 휴무를 다시 끄면 그 둘만 되살아납니다.
- 결과: 칩은 "2회"인데 운행 횟수는 0, 파렛트만 금액에 잡히는 어긋난 화면이 됩니다. 그 뒤 칩을 누르고 빼도 칩과 운행 횟수가 계속 어긋납니다.
- 휴무를 켜면 운행 횟수·노선 칩 횟수·파렛트를 한꺼번에 0으로 만듭니다(sot §4-13 "고정 노선 횟수·파렛트는 휴무면 0"). 휴무를 꺼도 되살리지 않습니다(운행 횟수가 지금 그렇듯). 콜 상세는 지금처럼 지우지 않습니다.

### 현재 상태 (코드 확인)
- 휴무 단추 `features/day-log/ui/DayLogPage.jsx:114` — `patchDraft({ isOff: off, fixedCount: off ? 0 : draft.fixedCount })`. 운비서 휴무 `features/day-log/hooks/useAssistantDayOff.js:37`도 같은 한 줄. 둘 다 `fixedRouteCounts`·`palletCount`를 안 건드림.
- 화면 안 값(draft)엔 둘이 남아 있다가 휴무를 끄면 그대로 보임(`PalletSection.jsx:17`은 휴무면 빈 칸만 보여 줌, `FixedRouteChips.jsx`는 휴무면 눌리지만 않음).
- 저장 `domain/day-record.ts:133` `saveDayRecord` — 휴무면 운행 횟수·파렛트는 0으로 저장하지만 노선 칩 횟수(`fixedRouteCounts`)는 그대로 저장.
- 다시 열 때 `features/day-log/hooks/day-log-reducer.js:41` `initDayLogState` — 휴무 날이면 운행 횟수·파렛트는 0으로 읽지만 칩 횟수는 그대로 읽음.
- 노선 칩 횟수는 운행 횟수의 노선별 내역(`useCallDetailActions.js:32` `runRoute`가 둘을 같이 올리고 내림).
- 서버(2026-10-30 읽기): 휴무 날 19건 중 칩 횟수·파렛트·운행 횟수가 남은 날 0건, 휴무 아닌 날 중 칩 합이 운행 횟수보다 큰 날 0건 — 고칠 옛 자료 없음.

### 목표 상태 (기대 동작)
1. 휴무 단추로 휴무를 켜면 운행 횟수·노선 칩 횟수·파렛트가 모두 0. 다시 끄면 칩에 "N회"가 없고, 파렛트 칸이 비고, 운행 횟수 0.
2. 운비서 [휴무로 하기]도 1과 같음. [휴무 취소하기]는 휴무만 끔(지금과 같음).
3. 휴무 날로 저장하면 노선 칩 횟수도 빈 값으로 저장됨(운행 횟수·파렛트처럼).
4. 옛 자료에 휴무인데 칩 횟수가 남아 있으면 열 때 0으로 읽음.
5. 콜 상세는 휴무로 바꿔도 그대로(sot §4-13). 휴무 아닌 날의 칩·운행 횟수·파렛트 동작은 그대로.

### 건드릴 파일
- `src/features/day-log/hooks/day-log-reducer.js` — 휴무 바꾸기 값을 한 곳에 둠: `export function dayOffPatch(off)` → 켜면 `{ isOff: true, fixedCount: 0, palletCount: 0, fixedRouteCounts: {} }`, 끄면 `{ isOff: false }`. `initDayLogState`의 칩 횟수도 휴무면 `{}`.
  - 주석(최종 문구): `// 휴무를 켜면 운행 횟수·노선 칩 횟수·파렛트를 0으로(끄면 휴무만 끔).`
- `src/features/day-log/ui/DayLogPage.jsx` — 114줄 `onChange={(off) => patchDraft(dayOffPatch(off))}`, 불러오기 한 줄.
- `src/features/day-log/hooks/useAssistantDayOff.js` — 37줄 `patchDraft(dayOffPatch(off))`, 불러오기 한 줄, `patchDraft` 타입 주석에 두 칸 추가.
- `src/domain/day-record.ts` — `saveDayRecord`의 칩 횟수를 휴무면 `{}`(운행 횟수·파렛트와 같은 규칙).
- 테스트:
  - `features/day-log/ui/DayLogPage.callActions.test.js` — 새 화면 테스트: 파렛트 켠 고정 노선 거래처, 칩 2번 + 파렛트 3장 → 휴무 켬 → 휴무 끔 → 칩 "회" 없음, 파렛트 칸 빈 값, 운행 횟수 0.
  - `features/day-log/hooks/day-log-reducer.test.js` — `dayOffPatch` 켬·끔 값, 휴무 기록을 열면 칩 횟수 `{}`.
  - `domain/workData.test.js` — 휴무로 저장하면 칩 횟수 `{}`, 휴무 아닌 날은 칩 횟수 그대로(안전 확인).
  - 운비서 휴무는 같은 `dayOffPatch`를 부르므로 화면 테스트를 따로 안 둠(기존 `app/routes/MainPageRoute.assistantDayOff.test.js` 그대로 통과 확인).

### 안 건드릴 것
- 콜 상세(휴무로도 안 지움, sot §4-13), 휴무 확인 창(`OffToggle.jsx` `confirmOffWithCalls`).
- 운비서 휴무 카드 문구·버튼(sot 24-K-2), 운비서 "휴무 취소하고 기록"(휴무만 끄는 같은 저장).
- 서버 저장 모양(`syncWorkData.ts`, 칸 이름 그대로), DB.

### 검증
- `npm test`, 타입 검사, 코드 검사, 의존 검사, 빌드(CI).
- 새 테스트가 고치기 전 코드에서 실패하는지 확인(revert-check). 휴무 아닌 날 칩 횟수 유지 테스트는 안전 확인(고치기 전에도 통과).
- 로컬 비회원 화면: 파렛트 켠 고정 노선 거래처·노선 칩 준비 → 칩 2번, 파렛트 3 → 휴무 켬 → 끔 → 칩·파렛트·합계 0 확인 → 시험 자료 지움.
- [x] 때 sot §4-13에 "휴무를 켜면 노선 칩 횟수도 0, 끄면 되살리지 않음" 한 줄 보탬.

### 파일 길이
- `day-log-reducer.js` 78 → 약 85, `DayLogPage.jsx` 187 → 188, `useAssistantDayOff.js` 42 → 43, `day-record.ts` 187 → 187. 모두 200 이하(테스트 파일은 예외).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 휴무 바꿀 때 넣는 값과 저장 규칙만 바꿈. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 배포본에서 노선 칩이 있는 날의 운행일지를 엶(시험해도 되는 빈 날).
2. 칩을 두 번 눌러 "2회", 파렛트가 보이면 3장 입력.
3. 휴무를 눌렀다가 다시 누름.
4. 칩에 "회"가 없고, 파렛트 칸이 비고, 운송료 합계가 0원이면 성공.
5. 확인 뒤 그날은 빈 날로 그대로 두면 됨.

### 진행 기록
- 코드 커밋 react-app `1bce0c3`. 전체 테스트(단위 954·화면 932), 타입 검사, 코드 검사, 의존 검사, 빌드 통과. 기존 운비서 휴무 테스트 3개 그대로 통과.
- 고치기 전 실패 확인(revert-check): 화면 테스트 "휴무를 켰다 끄면 …모두 0"·저장 테스트 "휴무로 저장하면 노선 칩 횟수도 비운다" 실패 → 고친 뒤 통과. `day-log-reducer.test.js`는 고치기 전 코드에 `dayOffPatch`가 없어 파일째 실패(그 안 "휴무 기록을 열면 칩 횟수 비움"도 같이). "휴무 아닌 날은 칩 횟수 그대로"는 고치기 전에도 통과 = 안전 확인.
- 로컬 비회원 화면: 시험 고정 노선 거래처(파렛트 켬)·노선 칩 준비 → 10월 28일 칩 2번(2회)·파렛트 3장, 합계 115,000원 → 휴무 켬·끔 → 운행 횟수 0, 칩 "회" 없음, 파렛트 0, 합계 줄 없음, 그날 기록은 빈 날로 지워짐. 시험 거래처·설정 원래대로.
- push 뒤 CI verify·deploy 초록(run 41047897297). 배포본 seed 계정: 10월 30일 운행일지 정상(고정 노선·파렛트 칸, 콘솔 오류 없음), 배포 파일 `index-B4ntcoGc.js`에 휴무 켤 때 넣는 새 값(운행 횟수·파렛트·칩 횟수 0) 들어 있음 확인. 계정 자료는 안 바꿈(휴무 눌러 보기는 보리 확인 몫).
- §5 리뷰: 바뀐 파일 7개 = 건드릴 파일(소스 4·테스트 3), 새 주석은 지시서 문구 그대로 1줄, 새 저장 장치·타입 꼼수 없음, 기존 테스트 줄 삭제는 불러오기 1줄 교체뿐, 소스 모두 200줄 이하.
