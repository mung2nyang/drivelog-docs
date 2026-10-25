# docs/report.md — 현재 슬라이스 착수지시서

24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 24-J-2 운비서 앱 — 일상점검표 "모두 양호" 카드와 [저장하기] `[x]`

보리 지시(2026-10-25, 24-J-1 `[x]` 뒤): "24-J-2 착수지시서 작성해". 24-J의 둘째(마지막) 조각이다. 서버(J-1, react-app `c992cb7`)는 이미 배포됐다.
- 확인할 점 1~5는 J-1 때 모두 "가"로 정해졌다 `[확인: 2026-10-24 보리 — "확인할 점 1~5 전부 가로"]`. 이번에 새로 물을 것은 없다.
- 규칙은 sot "점검표 말 알아듣기" 줄을 따른다.

### 쉬운 요약
- "오늘 점검표 모두 양호"라고 하면 카드에 "오늘 일상점검표를 모두 양호로 저장할게요."와 [저장하기]가 보인다. 누르면 일지 점검표 창과 똑같이 저장된다.
- 앱 설정 "일상점검표 사용"이 꺼진 사람, 휴무 날, 이미 저장된 날은 문장만 보이고 버튼은 없다.

### 현재 상태 (코드 확인)
- 앱은 점검표 답을 읽지 못한다. `readAnswer`(`api/unbiseoParse.ts`)에 inspection 갈래가 없어 마지막 줄에서 "알아듣지 못했습니다"가 된다.
- 일지의 점검표 안내 줄(`day-log/ui/DailyInspectionNotice.jsx`)이 쓰는 재료는 이렇다.
  - 켜짐: 그 일지 설정 `dailyInspectionOn`(`DayLogPage.jsx` 123줄)이고, 메인은 `resolveLogSettings(settings, 'main')`이 곧 설정이다(`domain/carSettingsScope.ts` 10줄).
  - 차량: `vehicleSupabaseIdForLog(ownerKey, 'main')`. 서버에 차량이 없으면 안내 줄을 숨긴다.
  - 휴무 날은 숨긴다(`isOff`).
  - 그날 1장을 `fetchDailyInspection`으로 읽고, `saveDailyInspection`(차량+날짜로 덮어씀)으로 저장한다.
  - 점검자 이름은 차량 기사 이름, 없으면 내 이름이다(`car.driverName || profile.name`).
- 연동 기사가 계약 기간 밖 날짜를 열면 일지 전체가 기간 안내 화면이 된다(`DayLogPage.jsx` 102줄). 그래서 점검표에도 닿지 않는다.
- 운비서 카드(`HomeAssistantPanel.tsx`)는 버튼 자리가 하나다. 떠 있는 운비서(`UnbiseoDock.tsx` 75줄)도 같은 카드를 쓴다.
- 기다림 문장은 날씨처럼 `waiting(…)`으로 보인다(`useUnbiseoAssistant.ts` 92~99줄).

### 목표 상태 (기대 동작)
1. 서버 답이 inspection이면 앱이 `{ date }`를 읽는다. 날짜 모양이 틀리면 오늘이다.
2. 아래 순서로 보고, 처음 걸리는 것 하나만 보인다. 문장 앞 날짜는 오늘이면 "오늘", 아니면 "M월 D일"이다.
   1. 메인 일지 설정 `dailyInspectionOn`이 꺼짐 → "앱 설정에서 일상점검표 사용을 켜면 쓸 수 있어요." 버튼 없음.
   2. 연동 기사이고 계약 기간 밖 → 지금 운행 카드와 같은 기간 안내(`assignmentPeriodNotice`). 버튼 없음.
   3. 그날 휴무 → "휴무 날은 일상점검표를 쓰지 않아요." 버튼 없음.
   4. 메인 차량이 서버에 없음 → "일상점검표를 저장할 차량이 없어요." 버튼 없음.
   5. 그날 점검표를 서버에서 읽는다. 기다리는 동안 "점검표를 확인하는 중…"이 보이고 입력이 잠긴다.
      - 이미 있음 → "{날짜} 일상점검표는 이미 저장되어 있어요." 버튼 없음, 덮어쓰지 않음.
      - 읽기 실패 → "잠시 뒤 다시 시도해 주세요."
      - 없음 → "{날짜} 일상점검표를 모두 양호로 저장할게요."와 [저장하기].
3. [저장하기]를 누르면 `saveDailyInspection`으로 11항목 모두 양호(`allGoodItems()`)를 저장한다. 조치 기록은 빈 칸이고, 점검자 이름은 일지 창과 같다.
   - 저장 중에는 버튼이 잠긴다(두 번 눌림 막기).
   - 성공 → 문장이 "{날짜} 일상점검표를 모두 양호로 저장했어요."로 바뀌고 버튼이 사라진다.
   - 실패 → 일지 창과 같은 실패 알림(오류 문구, 없으면 `SERVER_SAVE_FAIL`)이 뜨고 버튼은 남는다.
4. 마이크로 물었으면 2번 문장을 소리로 읽는다(지금 읽기 방식). 저장 뒤 문장은 읽지 않는다(손으로 누른 것이라서).
5. 계정이 바뀌면 받아 온 결과를 버린다(지금 `send`의 계정 확인 그대로). 저장 중 계정이 바뀌어도 화면에 결과를 붙이지 않는다.
6. 홈 카드와 떠 있는 운비서 둘 다 같다. 다른 종류(운행·지출·질문·고정·거래처·문자·날씨)는 지금과 같다.

### 건드릴 파일
- `src/features/home/api/unbiseoInspection.ts`(새): 순서 2의 상태 판단, 그날 점검표 읽기, 모두 양호 저장, 문장 만들기.
- `src/features/home/api/unbiseoParse.ts`: `UnbiseoResult`에 inspection, `readAnswer`에 한 줄.
- `src/features/home/api/unbiseoSentence.ts`: `describeResult`에 inspection 한 줄(기다림 문장).
- `src/features/home/hooks/useUnbiseoAssistant.ts`: `send`에서 inspection이면 상태를 받아 문장을 보이고, [저장하기]용 저장 함수를 돌려준다.
- `src/features/home/ui/HomeAssistantPanel.tsx`: [저장하기] 버튼(저장 중 잠금).
- 시험: 새 `unbiseoInspection.test.js`, `unbiseoParse.test.js`(inspection 읽기), `HomeAssistantPanel.test.js`(버튼·잠금·저장 뒤 문장).

### 안 건드릴 것
- 서버 함수(J-1에서 끝남, 배포본 지문이 커밋과 같음 — 진행 기록 `8e025d8`).
- 점검표 저장·읽기 함수 `data/sync/dailyInspections.ts`(그대로 부름), 일지 점검표 창·안내 줄, 설정 화면.
- `UnbiseoDock.tsx`(같은 카드를 그대로 씀 — 75줄).

### 검증
- 로컬 `npm test`·타입·검사기(CI가 다시 함). 새 시험은 고치기 전 코드에서 실패하는지 확인한다.
- 가짜 서버·가짜 저장소로 시험한다: 순서 2의 여섯 갈래, 저장 성공·실패, 두 번 눌림, 계정 바뀜. 저장 몸통이 일지 창 저장과 같은지(11항목 양호, 점검자 이름, 차량·날짜)도 본다.
- 배포본: 데스크톱 AI가 앱 안 브라우저에서 시험 계정으로 한다. 지난 날(빈 날) 저장 → 일지 점검표 "작성 완료"와 서류 한 달 표에 O가 보임 → 같은 날 다시 말하면 "이미 저장되어 있어요". 설정 꺼짐·휴무 날은 문장만인지 본다. **시험으로 저장한 점검표는 지우지 않는다**(지우기 기능이 없음). 그래서 시험 날짜는 보리가 정한다.

### 파일 길이
| 파일 | 지금 | 예상 |
|---|---|---|
| `api/unbiseoParse.ts` | 190 | ~194 |
| `api/unbiseoSentence.ts` | 83 | ~85 |
| `hooks/useUnbiseoAssistant.ts` | 140 | ~155 |
| `ui/HomeAssistantPanel.tsx` | 83 | ~95 |
| `api/unbiseoInspection.ts`(새) | — | ~80 |

- `unbiseoParse.ts`가 195를 넘을 것 같으면 멈추고 분리 설계(칸 읽기 함수들을 따로 떼기)를 보고한다.
- 200줄 감시 목록 파일은 없다.

### 코드에 넣을 주석(최종 문구)
- `unbiseoInspection.ts` 첫 줄: `// 운비서 일상점검표 — 일지 안내 줄과 같은 조건·같은 저장으로 그날 점검표를 모두 양호로.`
- `useUnbiseoAssistant.ts` `send` 안: `// 점검표는 그날 저장 여부까지 읽은 뒤 문장을 만든다.`
- `HomeAssistantPanel.tsx` 버튼 위: `// 점검표는 [저장하기]로 바로 저장한다(저장 중 잠금).`

### 실패 시 처리
- 새 저장·재시도·임시 보관은 만들지 않는다(AGENTS §7). 저장 실패는 일지 창과 같은 알림만 띄운다.
- 시험이나 배포본이 기대와 다르면 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법
- 휴대폰에서 일상점검표 사용을 켠 계정으로, 운비서 마이크를 누르고 "오늘 점검표 모두 양호"라고 말한다.
  - "오늘 일상점검표를 모두 양호로 저장할게요."가 들리고 [저장하기]가 보인다 → 누른다 → "저장했어요"로 바뀐다.
  - 오늘 일지의 점검표 줄이 "작성 완료"이고, [보기]를 누르면 11항목이 모두 양호다.
- 같은 말을 다시 하면 "이미 저장되어 있어요"가 나온다.
- 설정에서 일상점검표 사용을 끄고 말하면 켜는 방법 안내 문장만 나온다.

### 진행 기록
- 2026-10-25 보리: "착수지시서 확정 작업진행해" — 터미널 AI.
  - 교차검증: 지시서의 코드 사실(`DayLogPage.jsx` 102줄 기간 안내 화면·123줄 안내 줄 `dailyInspectionOn`, `resolveLogSettings` 10줄, `vehicleSupabaseIdForLog`, 점검자 이름 `car.driverName || profile.name`, 운비서 카드 버튼 자리, 기다림 문장 92~99줄)이 모두 맞아 고친 것 없음.
  - 구현: react-app `1b080ee`(push 안 함).
    - 새 `api/unbiseoInspection.ts`(60줄): `checkInspection`(순서 2의 다섯 갈래와 그날 1장 읽기), `saveAllGood`(11항목 양호·조치 기록 빈 칸, 실패면 오류 문구 또는 `SERVER_SAVE_FAIL`), `savedSentence`. 주석은 지시서 문구.
    - `unbiseoParse.ts`(193줄): 결과에 inspection, `readAnswer` 2줄. `unbiseoSentence.ts`(85줄): `INSPECTION_WAIT`, `describeResult` 한 줄, 날짜 말(`dateLabelOf`)을 내보냄.
    - `useUnbiseoAssistant.ts`(164줄): `send`에서 점검표면 확인 문장·[저장하기] 값, `saveInspection`(저장 중 잠금, 계정 바뀜·다른 말이면 결과 안 붙임). `HomeAssistantPanel.tsx`(85줄): [저장하기].
  - 지시서와 다른 점:
    1. 설정·계약 기간·휴무·차량·점검자 이름은 훅이 아니라 `unbiseoInspection.ts`가 묻는 그 순간 store에서 읽는다(질문 숫자와 같은 방식). 훅의 `send` 안에서 연동 기사 정보를 읽으면 검사기(oxlint react immutability)가 경고를 내서 옮김. 그래서 훅은 예상(~155)보다 길지만 164줄.
    2. 계약 기간 밖 문장은 운행 카드의 두 줄(제목·본문)을 한 문장으로 이어 보인다(예: "계약기간이 끝난 날짜입니다. 10월 20일까지 기록할 수 있습니다.").
    3. `HomePage.test.js`(지시서에 없던 파일)의 가짜 점검표 모듈에 `saveDailyInspection` 한 줄을 더함 — 홈 화면이 이제 이 함수를 불러와서. 시험 내용은 그대로.
    4. 화면 시험 파일에 알림 받기(`withToast`)를 둘러 저장 실패 알림을 확인한다.
  - 검증: lint·typecheck·depcheck(1801)·npm test(892·869) 통과.
  - 새 시험 10개(칸 읽기 1·점검표 판단과 저장 4·화면 5).
    - 칸 읽기 1개와 화면 5개는 고치기 전 코드에서 실패하고, 고친 뒤 통과한다.
    - 점검표 판단과 저장 4개는 새 파일 시험이다. 고치기 전 코드에서는 날짜 말 함수를 내보내지 않아 파일을 불러오지 못해 실패하는데, 동작 확인으로 치지 않는다.
    - `HomePage.test.js`의 기존 시험은 고치기 전에도 통과한다(안전 확인).
- 2026-10-25 보리 push 뒤 CI verify·deploy 초록(run 40158342704).
- §5 리뷰(데스크톱 AI): 범위 = 지시서 파일 + `HomePage.test.js` 1줄(교차검증 3번에 기록), 새 저장·재시도 없음(저장은 기존 `saveDailyInspection`), 타입 꼼수 없음, 운영 코드 주석에 로드맵 번호 없음, 200줄 안(최대 `unbiseoParse.ts` 193 — 195 한도 안), 주석 세 줄 지시서 문구 그대로. 훅이 예상보다 긴 164줄인 이유는 교차검증 1번.
- 배포본 시험(데스크톱 AI, 앱 안 브라우저, 테스트 계정, 2026-10-25 — 보리 "오늘로해"): "오늘 점검표 모두 양호" → "오늘 일상점검표를 모두 양호로 저장할게요."+[저장하기] → 누름 → "오늘 일상점검표를 모두 양호로 저장했어요."(버튼 사라짐, 실패 알림 없음) → 같은 말 다시 → "오늘 일상점검표는 이미 저장되어 있어요."(버튼 없음) → 10월 25일 일지 "일상점검표 작성이 완료되었습니다. [보기]" → 보기 창 11항목 모두 양호, "점검자: 테스트 (자동 서명)", 조치 기록 "-". 설정 꺼짐·휴무 날 문장은 계정 설정·일지를 바꿔야 해서 배포본에서는 안 함(시험으로만). 저장한 10-25 점검표는 남아 있음 — 보리가 지우기 기능을 로드맵 112로 올림.
  - 남은 일: 보리 push → CI → 데스크톱 AI 배포본 시험(시험 날짜는 보리가 정함, 저장한 점검표는 지우지 않음) → 보리 휴대폰 확인.

---

## 로드맵 94 `.ts` 전환 — 남은 조각 (94-A·B·C `[x]`, D~G는 출시 전 순서 뒤)

지난 지시서·진행 기록: 94-A drivelog-docs `3ee010c`, 94-B `90ef647`, 94-C `0c0eafe`(`git show <해시> -- docs/report.md`).

| 조각 | 범위 | 운영 파일 | 비고 |
|---|---|---|---|
| ~~94-A~~ `[x]` | 설정 + 시범 2개 | 2 | react-app `a009f2b` |
| ~~94-B~~ `[x]` | `domain/` | 58 | react-app `855fb6a` |
| ~~94-C~~ `[x]` | `data/` | 81 | react-app `079aee8` |
| 94-D | `shared/` + `app/` + `main.jsx` + `testSupport/` | 69 | |
| 94-E | `features/` 일지·달력·지출 | 39 | |
| 94-F | `features/` 서류·미수금·매출 | 38 | |
| 94-G | `features/` 나머지 8개 | 64 | 마무리: `allowJs` 끄기, `// @ts-check` 규칙 정리 |

- 순서 `[확인: 2026-10-22 보리 — "그럼 반만, 응"]`: D~G는 출시 전 순서(23·24 → 34 → 79) 뒤.
- 방식(B·C와 같음): AI가 손으로, 밖 파일은 경로 끝만(기계 비교), `mock.module` 지정 포함. 증명: 타입 설명 파일(`.d.ts`, 저장소 밖에서 뽑음) 비교 + 빌드 결과. 옛 JSDoc 형 변환 주석이 빠지면 압축 변수 이름이 바뀌어 해시가 달라질 수 있음(94-C) — 그때는 저장소 밖에서 옛·새를 압축 없이 빌드해 같은지로 증명(보리 승인 방식).
- 의존 검사 연결 수는 JSDoc `import('…')`이 `import type`이 되며 타입 전용 연결만큼 늘어남 — 늘어난 것이 그것뿐인지 확인.
- 94-D: `// @ts-check` 없는 4개(`app/guestSessionPersist.js`·`app/routes/ComingSoonRoute.jsx`·`app/routes/lazyPages.js`·`app/routes/sessionGate.js`)는 처음 타입 검사를 받음. `.jsx`는 `.tsx`로 — 화면 테스트 로더가 이미 읽음(94-A).
