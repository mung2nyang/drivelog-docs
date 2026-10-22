# docs/report.md — 현재 슬라이스 착수지시서

24-B-1 지시서·기록은 drivelog-docs `18646c9`, 24-A는 `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 24-B-2 운비서 → 콜 상세 입력 창 채워 열기 `[x]` `[확인: 2026-10-23 보리 — "폰확인완료", "승인"]`

보리 지시(2026-10-22, 24-B-1 `[x]` 뒤): "작성해". 24-B(운행 한 건)의 두 번째 조각 — 24-B-1 카드에서 운행으로 알아들은 내용을 그날 일지의 **콜 상세 입력 창에 채워 열고**, 사람이 고친 뒤 [저장]. 저장은 지금 입력 창의 저장 그대로(새 저장 길 없음) — 로드맵 24 "확인 화면은 새로 안 만들고 지금 입력 창으로" `[확인: 2026-10-22 보리]`.

### 정한 것 `[확인: 2026-10-22 보리 — "입력 창 열기 말고 기록하기로 문구 바꿔", "2번은 설정을 켤까요? 이렇게 해"]`
1. **버튼**: 결과 문장 아래 **[기록하기]** 하나. 누르면 그 날짜 일지로 가서 입력 창이 채워진 채 열림(자동 저장 안 함 — 사람이 [저장]).
2. **"콜 상세"를 끈 계정**: 버튼 대신 "콜 상세가 꺼져 있어요. 켤까요?" + **[켜기]** — 누르면 앱 설정의 "콜 상세"를 켜고(지금 설정 저장 그대로, 앱 설정 화면의 켜기와 같은 값) 그 자리에 [기록하기]가 나옴. 저장 실패면 지금 설정 실패 문구.
- 보리 추가 요청 — **다른 화면에서도 떠 있는 Ai 운비서 카드**(아래 메뉴 위, 줄이기 가능)는 **24-B-3으로 따로** `[확인: 2026-10-22 보리 — "권하는대로"]`. 이번 B-2는 홈 카드에서 [기록하기]까지. 코드는 터미널 AI가(보리 "터미널 AI한테 시킬 거야").

### 쉬운 요약
- 운비서가 "10월 21일 수원 → 화성, 150,000원으로 기록할게요."라고 답하면 그 아래 [기록하기]가 생깁니다. "콜 상세"가 꺼진 계정은 "켤까요?" + [켜기]. 누르면 10월 21일 일지로 가서 세부 입력 창이 상차지 수원·하차지 화성·운송료 150,000원(거래처가 있으면 거래처도)으로 채워진 채 열립니다.
- 확인하고 [저장]을 누르면 지금처럼 저장됩니다. 닫으면 아무것도 안 남습니다. 뒤로 가면 홈으로 돌아갑니다.

### 현재 상태 (확인 근거)
- 운비서 카드: `features/home/hooks/useUnbiseoAssistant.ts`(38줄)가 보내기·결과를 갖고 `answer`엔 문장만(`{ said, sentence }`) — 운행 칸(`UnbiseoCall`)은 버림. 카드 `HomeAssistantPanel.tsx`(44줄), 줄 `HomeAssistantBar.jsx`(54줄), `HomePage.jsx`(69줄)는 `<HomeAssistantBar />`를 아무 값 없이 그림.
- 홈 → 일지 이동: `AppShellRoutes.jsx:62` `onOpenToday={(dateKey) => navigate(`/app/day/${dateKey}`, { state: { from: 'home' } })}`. 일지 닫기는 `from: 'home'`이면 뒤로가기(`workLogNavigation.js:22`).
- 일지 화면: `app/routes/MainPageRoute.jsx`(100줄)가 `location`을 읽고 `DayLogPage`에 날짜·거래처·설정을 넘김. `DayLogPage.jsx`(170줄)의 입력 창은 `settings.callDetail`이 켜져 있을 때만, 열기는 `dispatch({ type: 'openCallForm', id: null })`(새 항목), 저장은 `useCallDetailActions.saveCall`(지금 그대로).
- 입력 창 `CallDetailForm.jsx`(194줄): 처음 값은 `value`(수정 중인 항목)가 있으면 `draftFromDetail(value, dateKey, clients)`(`callDetailFormHelpers.js:34` — 거래처 이름으로 거래처를 찾아 입금 예정일도 채움, 운송료는 쉼표 붙은 글자로), 없으면 빈 칸. `value`가 있으면 제목이 "… 세부 입력 수정"이 됨 → 채워 열기는 `value`가 아니라 따로 받아야 "새 입력"으로 보임.
- 의존 방향: 홈 기능은 일지 기능을 못 부름 → 이동은 앱 틀(`AppShellRoutes`)이 맡고, 일지 쪽은 주소 상태(`location.state`)로 받음.

### 목표 상태 (기대 동작)
1. **카드 버튼**: 운행 결과일 때만 결과 문장 아래 [기록하기]. 지출·질문·관계없음·실패면 없음. "콜 상세"가 꺼져 있으면 "콜 상세가 꺼져 있어요. 켤까요?" + [켜기] → 켜지면 [기록하기](정한 것 2).
2. **이동**: 누르면 `/app/day/{알아들은 날짜}`로, 주소 상태에 `{ from: 'home', assistantCall: { loadLoc, unloadLoc, fare, client } }`.
3. **일지에서 받기**: 주소 상태의 `assistantCall`을 칸마다 모양 확인(글자는 100자까지, 금액은 0 이상 정수만, 이상하면 그 칸은 빈 칸 — AGENTS §6)한 뒤 일지 화면에 넘기고, **주소 상태에서는 바로 지움**(`from`은 남김) — 새로고침·앞뒤로 가기로 다시 열리지 않게.
4. **입력 창**: 일지가 열리면 새 입력 창이 자동으로 열리고 칸이 채워짐 — 상차지·하차지·운송료(쉼표)·거래처, 거래처가 내 목록과 같으면 입금 예정일도 지금 규칙대로. 제목은 "{일}일 일지 세부 입력"(수정 아님). 나머지 칸은 빈 칸(지금 새 입력과 같음).
5. **저장·닫기**: [저장]은 지금 저장 그대로(검사·알림 "세부 입력을 저장했습니다." 포함). [닫기]면 아무것도 안 남음. 저장하거나 닫은 뒤 "+ 추가"를 누르면 빈 입력 창(채운 값은 한 번만).
6. 이미 그날 다른 운행이 있어도 새 항목으로 하나 더(바꾸지 않음). 운비서 쪽 저장·재시도·대기열 없음.

### 건드릴 파일
- `src/features/home/hooks/useUnbiseoAssistant.ts` — 마지막 운행 결과(`call`)도 기억, "콜 상세" 켜짐 여부(`resolveLogSettings(설정, 'main').callDetail`)와 켜기(`savePracticeSettings(ownerKey, { callDetail: true })` — 앱 설정 화면 `AppSettingsPage.jsx:140`과 같은 값) 내줌(38 → 약 60).
- `src/features/home/ui/HomeAssistantPanel.tsx` — [기록하기] 또는 "켤까요?"+[켜기](44 → 약 62). `HomeAssistantBar.jsx`·`HomePage.jsx` — 이동 함수(`onOpenAssistantCall`)를 받아 넘김(각 +2~3줄).
- `src/app/routes/AppShellRoutes.jsx` — `HomePage`에 `onOpenAssistantCall` 1줄(104 → 105).
- `src/app/routes/MainPageRoute.jsx` — 주소 상태에서 `assistantCall` 읽기·모양 확인·지우기, `DayLogPage`에 넘기기(100 → 약 125). 모양 확인 함수가 길면 같은 폴더 `assistantCallState.js`로.
- `src/features/day-log/ui/DayLogPage.jsx` — `assistantCall`을 처음 한 번 받아 입력 창 열기·채운 값 넘기기·닫거나 저장하면 버리기(170 → 약 182).
- `src/features/day-log/ui/CallDetailForm.jsx` — `prefill` 받기(처음 값만, 194 → 약 197).
- `src/features/home/ui/home-assistant.css` — 버튼 새 클래스만(157 → 약 165).
- 테스트: `HomeAssistantPanel.test.js`(버튼·켜기·이동 값), 새 `app/routes/MainPageRoute.assistantCall.test.js`(또는 `DayLogPage` 테스트에 추가 — 주소 상태로 열기·채움·제목·저장·닫고 다시 추가는 빈 칸·주소 상태 지움·이상한 값 걸러냄), `CallDetailForm.test.js`에 `prefill` 1개.
- 문서(`[x]` 때): sot §4-16 앱 줄에 "입력 창 채워 열기", 로드맵 24-B-2.

### 안 건드릴 것
- 서버 함수(24-A), 저장 길(`useCallDetailActions`·`useDayDraft`·`dayLogCloudCommit` — 지금 그대로), 일지 화면의 "콜 상세 꺼짐" 규칙(확인할 점 2 권함대로면), 지출·질문(24-C·D), 홈 다른 카드.

### 검증
- 테스트(실제 누르기·이동 경로): 위 목표 1~6. 특히 저장하면 그날 콜 상세에 1건 늘고 칸이 맞음, 닫으면 0건, 주소 상태 지운 뒤 다시 그려도 안 열림, 금액 음수·글자 300자 같은 이상한 값은 빈 칸.
- 기존 테스트 전부 통과·경고 늘지 않음(특히 일지·입력 창·홈 테스트). 타입·빌드·의존 방향 확인.
- 플레이북: 저장 경로는 안 바꾸지만 일지 화면(`DayLogPage`)을 건드리므로 §2(호출 경로)·§5(세션 바뀜) 확인 — 채운 값은 화면 상태일 뿐 저장 전엔 어디에도 안 남음.
- 브라우저(보리 계정 localhost): 운비서에 "어제 수원 화성 15만" → [기록하기] → 10월 21일 일지에 채워진 입력 창 → [저장] → 일지에 1건, 뒤로 → 홈. 휴대폰에서 보리 확인.

### 파일 길이
- 위 "건드릴 파일"의 예상 줄 수 — 모두 200 이하(가장 긴 `CallDetailForm.jsx` 약 197). 넘을 것 같으면 `MainPageRoute`의 모양 확인을 따로 파일로.

### 코드에 넣을 주석(최종 문구)
- `MainPageRoute.jsx`: `// 운비서가 넘긴 운행(location.state.assistantCall)은 한 번만 — 받아서 넘기고 주소 상태에서 지운다(로드맵 24-B-2).`
- `DayLogPage.jsx`: `// 운비서가 채운 값으로 새 입력 창을 한 번 연다. 닫거나 저장하면 버린다.`
- `CallDetailForm.jsx`(prop 설명): `@param {...} [props.prefill] 새 입력의 처음 값(운비서) — 수정 아님`

### 실패 시 처리
- 새 저장 장치·재시도 없음(AGENTS §7). 기대와 다르면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 홈 → Ai 운비서 마이크 → "어제 수원 화성 15만" → "10월 21일 수원 → 화성, 150,000원으로 기록할게요." 아래 [기록하기]("콜 상세"가 꺼져 있으면 [켜기] 먼저).
2. 누르면 10월 21일 일지로 가고 세부 입력 창에 상차지 수원·하차지 화성·운송료 150,000이 채워져 있음.
3. [저장] → "세부 입력을 저장했습니다." + 일지에 그 운행 1건. 뒤로가기 → 홈.
4. 다시 해 보고 이번엔 [닫기] → 아무것도 안 생김. 일지에서 "+ 추가"를 누르면 빈 입력 창.

### 진행 기록
- 2026-10-22 터미널 AI: 교차검증 — 지시서의 현재 상태(줄 수·`AppShellRoutes.jsx:62`·`draftFromDetail`·`AppSettingsPage.jsx:140`의 `callDetail` 저장)가 코드와 맞음. 구현 완료(커밋 전).
  - 홈: `useUnbiseoAssistant.ts`(60줄)가 운행 결과(`call`)·"콜 상세" 켜짐 여부·켜기(`savePracticeSettings(ownerKey, { callDetail: true })`, 실패면 `SERVER_SAVE_FAIL`)를 내줌. `HomeAssistantPanel.tsx`(55줄)에 [기록하기] 또는 "콜 상세가 꺼져 있어요. 켤까요?"+[켜기]. `HomeAssistantBar.jsx`·`HomePage.jsx`·`AppShellRoutes.jsx`(104줄)가 `onOpenAssistantCall`을 넘김 → `/app/day/{날짜}`, 상태 `{ from: 'home', assistantCall }`.
  - 일지: 새 `app/routes/assistantCallState.js`(30줄, 칸마다 모양 확인 — 글자 100자 넘거나 글자 아니면 빈 칸, 금액은 0 이상 정수만). `MainPageRoute.jsx`(109줄)가 읽어 넘기고 주소 상태를 `{ from }`만 남겨 바꿔 씀. `DayLogPage.jsx`(181줄)가 처음 받은 값으로 새 입력 창을 한 번 열고, 닫히거나 다른 항목 수정으로 바뀌면 버림. `CallDetailForm.jsx`(197줄) `prefill`(처음 값만, 제목은 새 입력).
  - 지시서와 다른 점 1개: 입력 창은 **주소 상태가 지워진 뒤에** 연다. 같이 바뀌면 뒤로가기 붙잡기(`usePanelBackGuard`)가 기록 바뀜을 뒤로가기로 알고 열자마자 "저장하지 않은 입력이 있습니다" 확인 창을 띄움(시험에서 확인). 시험에 "확인 창 0번"을 넣음.
  - 시험: `HomeAssistantPanel.test.js` +3(켜짐 [기록하기]·넘기는 값, 꺼짐 [켜기] → 설정 켜짐 → [기록하기], 지출·실패엔 버튼 없음), 새 `MainPageRoute.assistantCall.test.js` 6개(채워 열기·제목·주소 상태 지움·붙잡기 맨 위·확인 창 없음, 저장 1건·칸 맞음·다시 추가는 빈 칸, 취소 0건·다시 추가는 빈 칸, 다시 그리면 안 열림, 콜 상세 꺼짐이면 안 엶, 이상한 값은 빈 칸), `CallDetailForm.test.js` +1(`prefill`).
  - 로컬 확인: 테스트 883 + 717 전부 통과(act 경고 10 그대로), 타입 오류 0, lint 50 그대로, 의존 방향 위반 0, 빌드 성공. 첫 전체 실행 때 `PersonalInfoPage.typing.test.js` 1개가 시간 초과로 실패 — 빌드와 동시에 돌린 탓, 따로·다시 돌리면 통과(이번에 안 건드린 파일).
  - 커밋 react-app `d132321`(2026-10-22, 13개 파일, push 안 함).
- 보리 push 뒤 CI verify·deploy 초록(run 39799017179, 실제 2분 반·대기 약 12분은 GitHub 쪽). 보리 휴대폰 확인 끝. 무료 구간 "잠시 뒤 다시"가 4번에 1번 성공꼴로 잦음 — 앱 탓 아님, 보리가 지켜보기로(너무 안 되면 AI 회사 바꾸기, 로드맵 11번).
- §5 리뷰(데스크톱 AI): ① 범위 — 13개 파일, 지시서 "건드릴 파일"과 같음(새 `assistantCallState.js` 포함), 저장 길·서버 함수 안 바뀜 ② 몰래 증설 없음(채운 값은 화면 상태만, 한 번 쓰고 버림) ③ 타입 꼼수 0 ④ 200줄 — 최대 `CallDetailForm.jsx` 196, `home-assistant.css` 181(지시서 약 165) ⑤ 테스트 — 새 10개, 기존 테스트 지운 줄은 시험 화면에 값 넘기는 방식 바꿈뿐(약화 없음) ⑥ 공용 영향 — 새 CSS 클래스 3개는 운비서 카드만, `prefill`은 새 입력에만 ⑦ 목표 1~6 충족(다른 점은 입력 창 여는 때 1개, 위 기록).

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
