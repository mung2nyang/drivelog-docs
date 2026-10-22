# docs/report.md — 현재 슬라이스 착수지시서

24-B-2 지시서·기록은 drivelog-docs `c115e2b`, 24-B-1은 `18646c9`, 24-A는 `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 24-B-3-A 운비서 — 계약 기간 밖·휴무 날짜 안내 `[x]` `[확인: 2026-10-23 보리 — "승인"]`

보리 지시(2026-10-23, 24-B-2 `[x]` 뒤): "작성해". 24-B-3에 같이 넣기로 한 두 가지 `[확인: 2026-10-23 보리 — "둘다 권함으로"]`. 24-B-3 전체(떠 있는 카드 + 이 두 가지)는 파일이 약 16개라 한 슬라이스로 너무 커서 **둘로 나눔(AI 판단)**: **이번 A** = 홈 카드에서 기간 밖·휴무 안내(지금 구조 그대로), **다음 B** = 다른 화면에서도 떠 있는 카드(시안 먼저). A를 먼저 하는 이유: 휴무 날 입력 창이 흐린 채 열려 못 누르는 문제를 먼저 막음(⑤ 불편이 ⑥ 새 기능보다 앞). B는 A가 넣은 판단을 훅째 옮기므로 다시 만들 일 없음.

### 쉬운 요약
- 연동 기사가 계약 기간 밖 날짜를 말하면 [기록하기] 대신 "계약기간 전 날짜입니다. 10월 5일부터 기록할 수 있습니다." 같은 안내가 나옵니다(일지 화면과 같은 문장).
- 그날이 휴무로 표시돼 있으면 "휴무를 취소하고 운행을 기록할까요?" + [휴무 취소하고 기록]. 누르면 그날 휴무가 꺼지고 채운 입력 창이 열립니다(닫아도 휴무는 꺼진 채).

### 현재 상태 (코드 확인)
- 카드: `HomeAssistantPanel.tsx`(55줄)는 운행 결과면 "콜 상세" 켜짐 여부만 보고 [기록하기] 또는 [켜기]. 날짜의 기간·휴무는 안 봄(`useUnbiseoAssistant.ts` 60줄 grep `isOff`·`assignment` 0).
- 기간 밖: 일지 `DayLogPage.jsx:72`가 연동 기사(`isEmployedDriver`)일 때 `assignmentPeriodNotice(dateKey, myLink.startDate, myLink.endDate)`로 판단해 입력 창 대신 안내 화면(`:99`). 이 함수는 화면 파일 `features/day-log/ui/AssignmentPeriodPage.jsx:18`에 있어 홈이 못 부름(의존 방향 — 기능끼리 못 부름). 같은 판단만 하는 `isDateWithinAssignment`는 `domain/drivers.ts:128`(146줄).
- 휴무: 일지 칸 전체가 `.modal-work-details.is-off`(`day-log-shell.css:106` `opacity: 0.3; pointer-events: none`)라 휴무 날 채운 입력 창이 열려도 못 누름(코드 읽기). 휴무 끄기는 `OffToggle` → `patchDraft({ isOff: false, fixedCount })`(`DayLogPage.jsx`, 끌 때는 확인 창 없음 — `confirmOffWithCalls`는 켤 때만). 기록은 `useOwnerWorkData(ownerKey)[날짜].isOff`(`data/store/ownerWorkDataHooks.ts:35`, `domain/day-record.ts:43` `isOffDay`).
- 이동: 카드 → `HomeAssistantBar` → `HomePage` → `AppShellRoutes.jsx:59` `onOpenAssistantCall(call)` → 주소 상태 `assistantCall` → `assistantCallState.js` `readAssistantCall`(30줄) → `MainPageRoute` → `DayLogPage`.

### 목표 상태 (기대 동작)
1. 운행 결과 아래 버튼 자리는 이 순서로 하나만: ① 연동 기사이고 그 날짜가 계약 기간 밖 → 기간 안내 두 줄(일지와 같은 문장), 버튼 없음 ② "콜 상세" 꺼짐 → 지금처럼 [켜기] ③ 그날 휴무 → "휴무를 취소하고 운행을 기록할까요?" + **[휴무 취소하고 기록]** ④ 그 밖 → 지금처럼 [기록하기]. 위의 결과 문장("…으로 기록할게요.")은 그대로.
2. [휴무 취소하고 기록] → 그 날짜 일지로 가서 휴무를 끄고(휴무 단추를 누른 것과 같은 저장) 채운 입력 창을 엶. 닫아도 휴무는 꺼진 채, 저장은 사람이 [저장].
3. 휴무 끄기는 주소 상태에 `cancelOff: true`가 있을 때만(모양 확인 — 참일 때만 참). 일지가 이미 휴무가 아니면 아무것도 안 바꿈. 주소 상태는 지금처럼 받자마자 지움(다시 그려도 휴무를 또 끄지 않음).
4. `assignmentPeriodNotice`는 `domain/drivers.ts`로 옮겨 홈·일지가 같이 씀. 문장·판단은 그대로(기존 시험 5줄 그대로 옮겨 통과).
5. 차주·비회원·기간 안 날짜·휴무 아닌 날은 24-B-2와 똑같이 동작.

### 건드릴 파일
- `src/domain/drivers.ts` — `assignmentPeriodNotice`(+ 날짜 글 만드는 도우미)를 옮겨 옴(146 → 약 165).
- `src/features/day-log/ui/AssignmentPeriodPage.jsx` — 함수 빼고 화면만(37 → 약 20). `DayLogPage.jsx` — 부르는 위치를 `domain/drivers.ts`로 + `cancelOff`면 열기 전 `patchDraft({ isOff: false, fixedCount })`(181 → 약 186).
- `src/features/home/hooks/useUnbiseoAssistant.ts` — 운행 결과 날짜의 기간 안내(연동 기사일 때, 일지와 같은 `session.accountType === 'employed_driver'`·연동된 기사 줄의 시작·종료일)와 휴무 여부를 내줌(60 → 약 75).
- `src/features/home/ui/HomeAssistantPanel.tsx` — 목표 1 순서대로 그림(55 → 약 68). `HomeAssistantBar.jsx`·`HomePage.jsx` — 이동 함수에 `{ cancelOff }` 두 번째 값(설명 주석만 1줄씩).
- `src/app/routes/AppShellRoutes.jsx` — 주소 상태에 `cancelOff` 넣기(줄 수 그대로). `src/app/routes/assistantCallState.js` — `cancelOff` 읽기(30 → 약 33).
- 테스트: `domain/drivers.assignment.test.js`(옮긴 5줄), `AssignmentPeriodPage.test.js`(그 5줄 빼고 부르는 위치만), `HomeAssistantPanel.test.js`(기간 전·후 안내·버튼 없음, 휴무 → 질문·버튼 → 넘기는 값에 `cancelOff`), `MainPageRoute.assistantCall.test.js`(휴무 날 `cancelOff` → 휴무 꺼지고 입력 창 열림·눌림, `cancelOff` 없으면 휴무 그대로, 다시 그려도 안 바뀜).
- 문서(`[x]` 때): sot §4-16 한 줄, 로드맵 24-B-3 줄 나눔(A `[x]`, B 남음).
- 파일 수 10개(테스트 4 따로)로 보통보다 많음 — 대부분 몇 줄짜리 연결(설명 주석·주소 상태 한 칸), 실제 일은 `useUnbiseoAssistant`·`HomeAssistantPanel`·`DayLogPage`·`drivers.ts` 넷.

### 안 건드릴 것
- 떠 있는 카드(24-B-3-B), 서버 함수, 저장 길(`useDayDraft`·`useCallDetailActions` — 휴무 끄기는 기존 `patchDraft` 그대로), 휴무 화면 모양(`day-log-shell.css`), 일지의 기간 안내 화면 모양(`AssignmentPeriodPage` 화면 부분 그대로), 지출·질문(24-C·D).

### 검증
- 위 테스트(실제 누르기·이동 경로). 기존 테스트 전부 통과·경고 늘지 않음(특히 일지·기간 안내·운비서). 타입·빌드·의존 방향은 CI.
- 플레이북: 휴무 끄기는 일지 저장 길을 지나므로 §2(호출 경로) 확인 — 새 저장 길 없이 `patchDraft` 한 번.
- 브라우저(보리 계정 localhost): 어떤 날을 휴무로 → 운비서에 그 날짜 운행 → 질문·[휴무 취소하고 기록] → 휴무 꺼진 일지에 채운 입력 창 → [저장] 1건. 기간 밖은 연동 기사 계정이 필요해 테스트로(보리 휴대폰 계정이 연동 기사면 거기서도).

### 파일 길이
- `drivers.ts` 146 → 약 165, `AssignmentPeriodPage.jsx` 37 → 약 20, `DayLogPage.jsx` 181 → 약 186, `useUnbiseoAssistant.ts` 60 → 약 75, `HomeAssistantPanel.tsx` 55 → 약 68, `assistantCallState.js` 30 → 약 33, 나머지 줄 수 그대로. 모두 200 이하.

### 코드에 넣을 주석(최종 문구)
- `useUnbiseoAssistant.ts`: `// [기록하기] 전에 일지와 같은 기준으로 계약 기간·휴무를 본다.`
- `DayLogPage.jsx`: `// 운비서에서 "휴무 취소하고 기록"을 눌렀으면 휴무를 끄고 연다(휴무 단추와 같은 저장).`

### 실패 시 처리
- 새 저장 장치·재시도 없음(AGENTS §7). 기대와 다르면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 달력에서 어제를 휴무로 표시 → 홈 Ai 운비서에 "어제 수원 화성 15만".
2. "…으로 기록할게요." 아래에 "휴무를 취소하고 운행을 기록할까요?" + [휴무 취소하고 기록].
3. 누르면 어제 일지로 가서 휴무가 꺼져 있고 입력 창이 채워진 채 눌림 → [저장] → 1건.
4. (연동 기사 계정) 계약 시작 전 날짜를 말하면 버튼 대신 "계약기간 전 날짜입니다. ○월 ○일부터 기록할 수 있습니다."

### 진행 기록
- 2026-10-23 터미널 AI: 교차검증 — 현재 상태(`AssignmentPeriodPage.jsx:18`·`DayLogPage.jsx:72·99`·`isOffDay`·`OffToggle` 끌 때 `{ isOff, fixedCount }`·`assistantCallState.js` 30줄)가 코드와 맞음. 구현 완료(커밋 전).
  - `domain/drivers.ts`(160줄)로 `assignmentPeriodNotice`·날짜 글 도우미를 옮김(문장·판단 그대로). `AssignmentPeriodPage.jsx`(18줄)는 화면만.
  - 홈: `useUnbiseoAssistant.ts`(69줄)가 운행 결과 날짜로 기간 안내(`employed_driver`일 때, 연동된 기사 줄의 시작·종료일)와 휴무 여부(`isOffDay`)를 내줌. `HomeAssistantPanel.tsx`(77줄)는 버튼 자리 하나 — 기간 밖 안내 두 줄(버튼 없음) → [켜기] → "휴무를 취소하고 운행을 기록할까요?"+[휴무 취소하고 기록] → [기록하기]. 이동 함수 두 번째 값 `{ cancelOff }`(`HomeAssistantBar.jsx`·`HomePage.jsx` 설명 1줄씩, `AppShellRoutes.jsx` 줄 수 그대로).
  - 일지: `cancelOff`는 `assistantCall` 안에 넣어 주소 상태 지울 때 같이 지워짐. `assistantCallState.js`(32줄)는 `=== true`일 때만 참. `DayLogPage.jsx`(185줄)는 입력 창을 열기 직전 `cancelOff`이고 지금 휴무면 `patchDraft`와 같은 동작(`{ isOff: false, fixedCount }`) 한 번 — `patchDraft` 함수 대신 같은 `dispatch({ type: 'patchDraft' })`를 직접 씀(효과 안에서 매번 새로 만들어지는 함수를 안 부르려고).
  - 시험: 기간 안내 5줄을 `domain/drivers.assignment.test.js`로 옮김(`AssignmentPeriodPage.test.js`는 화면 시험 4개 그대로). `HomeAssistantPanel.test.js` +3(연동 기사 기간 전·후 안내·버튼 없음·기간 안은 [기록하기], 차주는 안내 없음, 휴무 → 질문·버튼 → `cancelOff: true`)·기존 [기록하기]는 `cancelOff: false` 확인. `MainPageRoute.assistantCall.test.js` +4(`cancelOff` → 휴무 꺼짐·입력 창 채움·확인 창 0·저장 기록도 휴무 아님, 없으면 휴무 그대로, 글자 "true"·1은 거짓, 다시 그려도 휴무 안 건드림).
  - 로컬 확인: 테스트 884 + 723 전부 통과(act 경고 10 그대로), 타입 오류 0, lint 50 → 49(화면 파일에서 함수를 빼서 줄어든 것으로 봄), 의존 방향 위반 0, 빌드 성공.
  - 커밋 react-app `d7e750e`(2026-10-23, 13개 파일, push 안 함).
- 보리 push 뒤 CI verify·deploy 초록(run 39804299992, 약 2분 반).
- §5 리뷰(데스크톱 AI): ① 범위 — 13개 파일, 지시서 "건드릴 파일" 10개 + 테스트 3개(`AssignmentPeriodPage.test.js`는 옮긴 5줄 뺀 것)와 같음, 저장 길·CSS·서버 안 바뀜 ② 몰래 증설 없음(새 저장 칸·재시도 없음, `cancelOff`는 기존 주소 상태 한 칸) ③ 타입 꼼수 0 ④ 200줄 — 최대 `DayLogPage.jsx` 185 ⑤ 테스트 — 새 7개(홈 3·일지 4), 기간 안내 시험 5줄은 그대로 옮김(내용 같음), 기존 [기록하기] 시험엔 `cancelOff: false` 확인을 더함(약화 없음) ⑥ 공용 영향 — `assignmentPeriodNotice`를 부르는 곳은 `DayLogPage`·`useUnbiseoAssistant` 둘뿐(grep), 문장 그대로 ⑦ 목표 1~5 충족. 휴무 끄기는 `patchDraft` 함수 대신 같은 `dispatch({ type: 'patchDraft' })`(진행 기록에 이유) — 저장된 기록도 휴무 아님을 시험이 확인. 브라우저·휴대폰 확인은 보리 몫.

### 다음 24-B-3-B 미리 보기(이번 범위 아님)
- 떠 있는 카드: 운비서 상태를 앱 틀로 올리고(홈 카드와 같은 대화), 아래 메뉴 위에 늘 떠 있는 줄(줄이기 가능). 시안과 함께 정할 것 — 줄였을 때 모양, 띄울 화면(홈은 지금 카드로 두고 그 밖?), 줄인 상태 기억 여부, 가림(앱 틀 아래 여백 108px·알림 문구 위치 `toast.css` 76px도 같이 올려야 함).
- 알아 둔 걸림돌: 이미 그 날짜 일지에 있을 때 [기록하기]는 같은 주소라 일지가 새로 안 그려져 지금 방식(처음 한 번 받기)으로는 안 열림 → 이동마다 다른 `location.key`로 새 값을 알아보게 바꿔야 함. 입력 중인 창이 열려 있으면 "저장하지 않은 입력" 확인 먼저.

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
