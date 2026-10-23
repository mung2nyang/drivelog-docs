# docs/report.md — 현재 슬라이스 착수지시서

109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 109-B 운행일지 하루 화면 운송료 합계 한 줄 `[x]`

로드맵 109 두 번째(마지막) 조각. 109-A `[x]`(react-app `f6488ba`, drivelog-docs `a5b3d61`)가 고친 하루 금액 계산을 그대로 쓴다. 보리 요청 "고정노선이랑 세부입력 같이 사용했을 때 해당 화면에서 총 얼마인지 확인할 수 없어", 시안 "다"(제목 바로 아래 작은 글씨) `[확인: 2026-10-23 보리 — "다가 좋아"]`, 파렛트 포함 `[확인: 2026-10-23 보리]`, 구분 기호(가운뎃점 등) 금지 `[확인: 2026-10-23 보리]`.

### 쉬운 요약
- 운행일지 하루 화면 제목 바로 아래에 그날 운송료 합계를 작은 글씨 한 줄로 보인다(예: "운송료 합계 475,000원").
- 숫자는 달력 칸·홈 오늘 카드와 같은 계산(고정 노선 + 파렛트 + 세부 운송료)이라 세 화면이 같다.
- 횟수·파렛트를 바꾸거나 운행을 추가·수정·삭제하면 저장을 기다리지 않고 바로 바뀐다.

### 현재 상태 (코드 확인)
- 일지 화면은 각 칸 금액만 따로 보임: 고정 노선 금액·파렛트 금액(`FixedCountSection.jsx:56`, `PalletSection.jsx:37`), 세부 내역 합계(`CallDetailList.jsx:66-69`, 부가세 더하고 수수료 뺀 다른 기준). 하루 합계 없음.
- 제목 묶음: `DayLogPage.jsx:101-106` `PageHeader` — 제목 아래에 이미 자동 저장 상태 작은 줄(`AutoSaveStatus.jsx`, 평소엔 빈 칸으로 자리만)이 있음. 그 다음이 휴무 단추(`OffToggle`, 107줄).
- 하루 금액 정본: `domain/calendarBadges.ts` `dayFareTotal(record, unitPrice, palletPrice)`(109-A).
- 일지의 단가·파렛트 조건: `DayLogPage.jsx:80-81` `fixedRouteClient`(`fixedUnitPrice`·`palletPrice`), `palletVisible = settings.fixedOn && fixedRouteClient?.palletOn` — `settings`는 차량별로 고른 값(`MainPageRoute.jsx:37` `resolveLogSettings`)이라 달 정산 파렛트 조건과 같음.
- 입력 중 값 `draft`(`isOff`·`fixedCount`·`palletCount`·`callDetails`)는 저장 값과 같은 모양(`dayDraftLifecycle.js:112-115`).

### 목표 상태 (기대 동작)
1. 휴무가 아니면 제목 묶음(제목 + 저장 상태 줄) 바로 아래, 휴무 단추 위에 가운데 정렬 작은 글씨 한 줄: "운송료 합계" + 금액(`formatWon`). 금액만 본문 색·굵게, 앞 글은 흐린 색.
2. 금액 = `dayFareTotal(draft, fixedRouteClient?.fixedUnitPrice, palletVisible ? 파렛트 단가 : 0)` — 같은 날 달력 칸(금액 입력 계정)·홈 오늘 카드와 같은 숫자.
3. 입력하는 즉시 바뀐다(draft 기준).
4. 금액이 0원이면 글을 숨기되 한 줄 높이는 늘 비워 둔다 — 처음 입력할 때 아래 칸이 밀려 내려가지 않게 `[확인: 2026-10-23 보리 — "줄을 숨기는데 그 공간을 항상 비게 해놔서 안 밀려 내려가게"]`. 숨길 때는 화면 읽기 프로그램도 읽지 않게(`visibility: hidden` 또는 같은 효과).
5. 휴무여도 4번처럼 글만 숨기고 자리는 비워 둔다 — 휴무를 켜고 끌 때도 아래 칸이 안 움직인다.
6. 구분 기호·내역 풀이·아이콘·움직이는 효과 없음. 숫자는 `tabular-nums`(바뀔 때 자릿수 흔들림 없음).
7. 기존 칸별 금액 줄(고정 노선·파렛트·세부 내역 합계)은 그대로.

### 건드릴 파일
- `react-app/src/features/day-log/ui/DayFareTotal.jsx` (새 파일, 약 25줄) — 금액 계산(`dayFareTotal` 호출)과 한 줄 그리기. 맨 위 주석(최종 문구):
  `// 일지 제목 아래 하루 운송료 합계 — 달력 칸·홈과 같은 계산(고정 노선 + 파렛트 + 세부 운송료).`
- `react-app/src/features/day-log/ui/DayLogPage.jsx` — `PageHeader` 다음, `OffToggle` 앞에 `<DayFareTotal ... />` 한 줄과 import 1줄.
- `react-app/src/features/day-log/ui/day-log-shell.css` — `.work-log-page .day-fare-total` 규칙 하나(새 클래스 — `grep -rn day-fare-total src` 0건이라 다른 화면 영향 없음). 제목 묶음 아래 여백(`header-controls.css:57` `.settings-header` `margin-bottom: 20px`, 공용)은 안 고치고 이 줄 쪽 여백만으로 맞춤. 색·크기는 기존 토큰(`--sub-text-color`·`--text-color`·`--fs-3`)만.
- 테스트(200줄 예외): 새 `DayFareTotal.test.js` — 고정 5회 × 50,000 + 파렛트 5장 × 5,000 + 세부 200,000 → "475,000원", 파렛트 꺼진 거래처면 파렛트 뺌, 0원·휴무면 줄 요소는 남고 글은 숨김(보이는 글 없음). `DayLogPage.callActions.test.js`에 1개 — 횟수를 바꾸면 줄 금액이 즉시 바뀜.

### 안 건드릴 것
- `domain/calendarBadges.ts` — 109-A 계산 그대로 씀.
- `PageHeader.jsx`·`header-controls.css` — 공용(여러 화면 제목)이라 안 건드림.
- `FixedCountSection.jsx`·`PalletSection.jsx`·`CallDetailList.jsx` 기존 금액 줄(목표 7).
- 저장 — 표시만.

### 검증
- CI 초록(테스트·타입·빌드·코드 검사·의존 방향).
- 새 테스트는 고치기 전 코드에서 실패하는지 확인(revert-check).
- 브라우저: 23일 일지 줄 "475,000원" = 달력 칸 47.5만 = 홈 475,000원, 횟수 단추를 누를 때 아래 칸이 안 밀림, 320px 폭에서 한 줄, 다크·라이트 모드 글자 대비.

### 파일 길이
- `DayLogPage.jsx` 180 → 약 182줄.
- `DayFareTotal.jsx` 새로 약 25줄.
- `day-log-shell.css` 158 → 약 170줄.
- 모두 200줄 아래. 테스트 파일은 예외.

### 실패 시 처리
- 새 저장·재시도·대비 장치 없음. 표시만 하는 줄이다.
- 달력 칸·홈과 숫자가 다르게 나오면 멈추고 보고한다.

### 정한 것 (보리 확인)
1. 문구 "운송료 합계" — 지난 날짜 일지에서 "오늘"이 틀리지 않게 `[확인: 2026-10-23 보리 — "응"]`.
2. 0원·휴무면 글은 숨기고 자리는 비워 둠(목표 4·5).

### 진행 기록
- 교차검증: 줄 번호·조건(`DayLogPage.jsx:80-81·101-107`, `palletVisible`, `draft` 모양)은 코드와 맞음. 숨기는 방법은 `visibility: hidden` 대신 같은 효과로 — 0원·휴무면 줄 요소(높이 20px)만 남기고 글을 넣지 않음(화면 읽기 프로그램도 읽을 글이 없음). 저장 상태 줄(`.autosave-status`)이 제목 아래 20px 여백 안에 떠 있어, 겹치지 않게 이 줄은 음수 여백 없이 그 여백 다음에 둠(`margin: 0 0 8px`). 실제 간격은 보리 화면 확인.
- 구현: 새 `DayFareTotal.jsx`(23줄) — `dayFareTotal(draft, fixedUnitPrice, palletOn ? palletPrice : 0)`, 휴무면 0. `DayLogPage.jsx` 180 → 182줄(import 1, 줄 1). `day-log-shell.css` 158 → 175줄(`.work-log-page .day-fare-total`·`strong` 두 규칙, 기존 토큰만, `tabular-nums`).
- 검사: 테스트 887 + 769(새 4개), 타입·코드 검사·의존 방향(1721 → 1724, 새 파일의 연결 3개)·빌드 통과.
- 고치기 전 실패 확인: `DayLogPage.callActions.test.js` 1개(횟수를 바꾸면 합계가 바로 바뀜)는 수정 전 코드에서 실패. `DayFareTotal.test.js` 3개는 새 파일만 시험해 되돌릴 것이 없어 통과(정상).
- 보리 지시(2026-10-23)대로 워킹카피에 남아 있던 `CalendarMonthSummary.jsx`·`.test.js`(빨간 점 설명 문구 "미수금")를 같은 커밋에 넣음.
- 코드 커밋: react-app `34e5f56`(7개 파일, push 안 함). 남은 것: 보리 push → CI 초록 → 브라우저 확인(위 사용자 확인 방법) → `[x]` 때 STATUS·로드맵 109 `[x]`.
- push 뒤 CI verify·deploy 초록(run 39906609859, react-app `34e5f56`). 브라우저(AI, 개발 화면): 22일 일지 "운송료 합계 612,346원" = 달력 칸 61.2만, 줄 높이 20px.

### 수정 착수지시서 — 저장 표시를 제목 옆 동그라미 아이콘으로, 그 자리에 합계 `[x]`

2026-10-23 보리 화면 확인 중 지적: "저장 완료 글자를 좀 더 작게 만든 뒤 운행일지 옆에다가 붙이고, 저장 부분에 운송료 합계를 넣는 건?" → 시안(저장 표시는 제목 옆, 제목 아래에 합계) `[확인: 2026-10-23 보리 — "시안대로 109-B 수정 지시서 써"]`. 첫 시도는 아래 진행 기록대로 320px에서 글씨가 메뉴 단추와 33px 겹쳐 멈춤 → 저장 표시를 글씨 대신 동그라미 아이콘으로, 저장 실패는 날짜를 넣은 알림 문구로 알림 `[확인: 2026-10-23 보리 — "저장 부분 동그라미 아이콘으로", "22일 운행일지 입력이 실패했습니다 이렇게", "응 그럼 그렇게 진행하자"]`. 알림 목록(종)에 저장 실패를 남기는 것은 저장 쪽 조사가 먼저라 이번 범위 밖(로드맵 등재 여부는 보리 결정 대기). 109-B 코드 커밋(`34e5f56`) 뒤라 수정 커밋 하나 더.

#### 쉬운 요약
- 제목 아래 "저장 중…/저장 완료/저장 실패" 글씨 대신, 제목 오른쪽에 작은 동그라미 아이콘(약 15px)을 둔다 — 저장 중은 회색 도는 원, 완료는 체크 원, 실패는 빨간 느낌표 원.
- 저장 표시가 있던 제목 바로 아래 자리에 "운송료 합계 612,346원"을 넣는다. 제목 아래가 두 줄에서 한 줄로 줄어 아래 칸이 위로 약 28px 올라온다.
- 일지 저장이 실패하면 뜨는 알림 문구에 날짜를 넣는다: "10월 22일 운행일지 저장에 실패했습니다. 네트워크 상태를 확인해 주세요."
- 연동 해제로 기사 계정에 복사된 날에만 제목 옆에 붙는 "배정 차량 12가3456" 글씨는 제목 다음 줄로 내린다.

#### 현재 상태 (코드 확인)
- 제목 묶음: `DayLogPage.jsx:101-106` `PageHeader` `titleExtra={<AutoSaveStatus …/>}` → `PageHeader.jsx:20-24` `.modal-title-stack` 안에 제목 + 저장 표시. `titleExtra`를 쓰는 곳은 일지 화면뿐(`grep -rn "titleExtra=" src` 1건).
- 저장 표시: `AutoSaveStatus.jsx`(8줄) 글씨 `idle ' '·pending '저장 중…'·saved '저장 완료'·failed '저장 실패'`. 모양 `day-log-shell.css:134-148` `.autosave-status` — 제목 바로 아래 가운데, `--fs-2`·굵기 600, 높이 20px. 실패 색 `day-log.css:76` `#e0453a`.
- 합계 줄: 109-B로 `PageHeader` 다음에 따로 둔 `<DayFareTotal>`(높이 20px + 아래 8px). 제목 아래 여백 20px(`header-controls.css:57`, 공용) 안에 저장 표시가 떠 있고 그 다음에 합계 줄이 와서 두 줄.
- 저장 실패 알림 문구: `dayDraftLifecycle.js:81` 휴대폰 저장 실패 `DEVICE_SAVE_FAIL`, `:97` 서버 저장 실패 `result.toast`(`data/sync/dayLogCloudCommit.ts:97·109` `SAVE_FAIL_TOAST`, `:107` 다른 계정으로 바뀜 `SESSION_CHANGED_TOAST`). 공용 문구는 `shared/utils/failureMessages.js:5·8` — 다른 화면도 씀. 날짜 없음.
- "배정 차량": `DayLogPage.jsx:102` 제목 안 `span.assigned-vehicle-tag`(그날 기록에 `assignedVehicleNumber`가 있을 때만 — 연동 해제 때 기사 계정에 보관된 날, 로드맵 7-D). 지금은 제목 옆 같은 줄, 좁으면 다음 줄로 꺾임(`day-log-shell.css` `.assigned-vehicle-tag`).
- 성공(초록) 색 토큰은 없음(`shared/styles/variables.css`는 `--danger-fill`만).

#### 목표 상태 (기대 동작)
1. 저장 표시는 제목 오른쪽에 약 6px 띄운 약 15px 동그라미 아이콘. 글씨는 없음. 평소(idle)는 아이콘 없음(자리만).
   - 저장 중: 회색(`--sub-text-color`) 원이 돈다. 움직임 줄이기 설정(`prefers-reduced-motion: reduce`)이면 돌지 않는 회색 원.
   - 저장 완료: 회색(`--sub-text-color`) 체크 원. 새 색을 만들지 않는다.
   - 저장 실패: 지금 실패 글씨와 같은 빨간색 느낌표 원.
2. 아이콘이 바뀌어도 제목 자리는 안 움직인다(제목 오른쪽 바깥에 떠 있음, 제목은 화면 가운데).
3. 화면 읽기 프로그램은 상태를 글로 읽는다 — 아이콘 옆에 화면에는 안 보이는 글("저장 중", "저장 완료", "저장 실패"), 바뀔 때 읽어 주게(`role="status"`).
4. 제목 바로 아래(지금 저장 표시 자리)에 "운송료 합계 금액"이 가운데로. 금액·조건·숨기기 규칙(0원·휴무면 글 없음)은 109-B 그대로. 따로 있던 합계 줄은 없어져 휴무 단추가 약 28px 위로.
5. 합계가 나타나거나 사라져도 아래 칸은 안 움직인다(제목 아래 여백 안에 떠 있음).
6. 일지 저장 실패 알림 문구 앞에 날짜: "{M}월 {D}일 운행일지 " + 지금 문구. 서버 저장 실패·휴대폰 저장 실패 두 가지만. 다른 계정으로 바뀐 경우 문구와 다른 화면의 공용 문구는 그대로.
7. "배정 차량" 글씨가 있는 날은 그 글씨가 늘 제목 다음 줄 가운데에 작게. 저장 아이콘은 제목 첫 줄 오른쪽, 합계는 "배정 차량" 줄 아래.
8. 320·375·414px 폭에서 아이콘이 메뉴 단추와 겹치지 않고, 제목·합계는 각각 한 줄.

#### 건드릴 파일
- `react-app/src/features/day-log/ui/AutoSaveStatus.jsx` — 글씨 대신 아이콘(인라인 SVG, 앱의 다른 아이콘과 같은 방식) + 화면에 안 보이는 상태 글. 8 → 약 30줄. 맨 위 주석(최종 문구):
  `// 일지 자동 저장 상태 — 제목 옆 작은 아이콘(저장 중·완료·실패). 실패는 아이콘이 남고 날짜를 넣은 알림 문구가 따로 뜬다.`
- `react-app/src/features/day-log/ui/DayLogPage.jsx` — `titleExtra`에 저장 아이콘과 `<DayFareTotal>`을 함께, 따로 있던 `<DayFareTotal>` 줄 지움, "배정 차량" 글씨를 제목 다음 줄로 옮기는 자리 조정. 182 → 약 183줄.
- `react-app/src/features/day-log/ui/day-log-shell.css` — `.autosave-status`(제목 오른쪽, 돌기·움직임 줄이기), `.day-fare-total`(제목 아래), `.assigned-vehicle-tag`(다음 줄). 175 → 약 190줄. 바꾸는 규칙 위 주석(최종 문구):
  `/* 저장 아이콘 — 제목 오른쪽에 띄워 아이콘이 바뀌어도 제목이 안 움직인다. */`
  `/* 제목 아래 하루 운송료 합계 — 제목 아래 여백 안에 떠 있어 0원·휴무로 글이 빠져도 아래 칸이 안 움직인다. */`
- `react-app/src/features/day-log/ui/day-log.css` — 실패 색 규칙이 아이콘에도 맞게(색 값은 그대로).
- `react-app/src/features/day-log/hooks/dayDraftLifecycle.js` — 81·97줄 알림 문구 앞에 날짜(`dateKey`에서 월·일). 180 → 약 184줄.
- 테스트(200줄 예외): `useDayDraft.cloudSerial.test.js:166` 등 실패 문구 기대값에 날짜, 저장 상태 아이콘·읽기 글 테스트, 합계가 제목 묶음 안에 있음.

#### 안 건드릴 것
- `PageHeader.jsx`·`header-controls.css` — 공용(여러 화면 제목). 여백 20px도 그대로.
- `shared/utils/failureMessages.js` 공용 문구 — 다른 화면이 씀. 일지에서 앞에 날짜만 붙임.
- `dayLogCloudCommit.ts`·저장 방식·재시도 — 표시와 문구만.
- 알림 목록(종)에 저장 실패 남기기 — 이번 범위 밖.
- 합계 계산·문구·숨기기 규칙(109-B 그대로).

#### 검증
- CI 초록(테스트·타입·빌드·코드 검사·의존 방향).
- 새 테스트는 고치기 전 코드에서 실패하는지 확인(revert-check).
- 브라우저: 320·375·414px에서 아이콘과 메뉴 단추 거리 재기("배정 차량" 있는 날 포함), 저장 중·완료·실패 바뀔 때 제목 위치 그대로, 합계 있는 날·0원·휴무 날 휴무 단추 위치 같음, 다크·라이트, 움직임 줄이기 켰을 때 안 돎.

#### 파일 길이
- `AutoSaveStatus.jsx` 8 → 약 30, `DayLogPage.jsx` 182 → 약 183, `day-log-shell.css` 175 → 약 190, `day-log.css` 78 → 약 80, `dayDraftLifecycle.js` 180 → 약 184줄. 모두 200줄 아래(`DayLogPage.jsx`·`dayDraftLifecycle.js`·`day-log-shell.css`는 200줄 가까움 — 넘으면 멈추고 보고).

#### 실패 시 처리
- 새 저장·재시도·대비 장치 없음.
- 아이콘으로도 320px에서 메뉴 단추와 겹치면 멈추고 잰 숫자로 보고 — 보리가 정한다.

#### 진행 기록 — 실패 시 처리로 멈춤(코드 안 고침)
- 고치기 전에 목표 모양(저장 표시 `left: 100%` + 6px, `--fs-floor`, 합계는 제목 아래 `top: 100%` 가운데)을 저장소 밖 임시 화면에 실제 CSS(`variables`·`app-base`·`header-controls`·`day-log-shell`)로 그려 Chrome(Windows 글꼴)에서 위치를 잼. 제목 "10월 22일 운행일지"(폭 158px), 저장 표시 "저장 완료".
- 저장 표시 오른쪽 끝이 메뉴 단추 왼쪽 끝을 넘는 길이: 320px 폭 33px, 375px 폭 6px, 414px 폭 안 겹침(14px 남음). "배정 차량" 붙는 날은 320px 53px, 375px 53px, 414px 50px(제목이 두 줄로 꺾이거나 길어짐).
- 합계 줄(폭 133px)은 세 폭 모두 화면 안 한 줄.
- 휴대폰 글꼴은 폭이 조금 다를 수 있으나 320px 33px는 차이가 커서 겹침으로 판단. 임시 파일은 scratchpad에만, react-app 변경 없음.
- 보리가 정할 것: 저장 표시를 어디에 둘지(예: 제목 옆 대신 다른 자리, 또는 좁은 화면에서만 다른 자리) — 정하면 수정 지시서 갱신.
- 2026-10-23 보리 결정: 제목 옆 동그라미 아이콘 + 날짜 넣은 실패 알림 문구 + "배정 차량" 다음 줄 — 위 수정 지시서에 반영. 아이콘(약 15px + 6px)이면 글씨(약 56px)보다 약 36px 줄어 320px 33px 겹침이 거의 0이 되리라 봄(실측 필요).

#### 진행 기록 2 — 아이콘 실측, 다시 멈춤(코드 안 고침)
- 같은 임시 화면에서 저장 표시를 15px 상자 + 6px 띄움, 제목 첫 줄 가운데(위에서 6px), "배정 차량"은 다음 줄(`display: block`)로 바꿔 잼.
- 아이콘 오른쪽 끝 ~ 메뉴 단추 왼쪽 끝: 320px **-1px(1px 겹침)**, 375px 27px, 414px 46px. "배정 차량" 있는 날도 제목 첫 줄 폭이 같아 같은 숫자. 계산상 360px는 약 19px 남음.
- "약" 범위 안에서 띄움 4px·아이콘 14px로 줄이면 320px에서 2px 남음(이것도 Windows 글꼴 기준, 휴대폰 글꼴이면 달라질 수 있음).
- 다른 발견: "배정 차량"을 다음 줄로 내리면 제목 묶음이 27 → 44px로 커져, 그 아래에 띄운 합계 줄이 제목 아래 여백 20px을 2px 넘어 휴무 단추 쪽으로 나감(겹침 2px). 보통 날은 6px 남음.
- 보리가 정할 것: ① 320px 1px 겹침 — 띄움 4px·아이콘 14px로 줄여 진행할지, 다른 방법인지. ② "배정 차량" 날 2px — 그날만 합계 줄 아래 여백을 늘려(휴무 단추가 그날만 약 2~8px 내려감) 진행할지.
- 2026-10-23 보리 결정: "둘 다 권함대로 진행해" — ① 띄움 4px·아이콘 14px ② "배정 차량" 날만 제목 아래 여백 28px.

#### 진행 기록 3 — 구현
- `AutoSaveStatus.jsx` 8 → 32줄: 글씨 대신 인라인 SVG(저장 중 = 흐린 원 + 도는 호, 완료 = 체크 원, 실패 = 느낌표 원) + `.sr-only` 상태 글, `role="status"`. 평소(idle)는 아이콘 없음. 클래스 `autosave-{상태}`.
- `day-log-shell.css` 175 → 193줄: `.autosave-status`(제목 첫 줄 가운데 `top: 6px`, `left: 100%` + 4px, 14px, 돌기 0.9초, 움직임 줄이기면 멈춤), `.day-fare-total`을 제목 묶음 기준 `top: 100%` 가운데로 띄움, `.assigned-vehicle-tag` `display: block`(다음 줄), `.work-log-page.has-assigned-vehicle .settings-header { margin-bottom: 28px }`. 화면 읽기 글 규칙은 한 줄로 써 줄 수를 줄임.
- `day-log.css`는 안 고침: 실패 색 `.autosave-status-failed { color }`가 shell보다 뒤에 불려 이미 이기고, 아이콘이 `currentColor`라 그대로 빨간색.
- `DayLogPage.jsx` 182 → 181줄: `titleExtra`에 아이콘 + 합계, 따로 있던 합계 줄 지움, "배정 차량" 날 `has-assigned-vehicle` 클래스. `DayFareTotal.jsx`는 숨기기 주석 한 줄만.
- `dayDraftLifecycle.js` 180 → 182줄: `failHead`("{M}월 {D}일 운행일지 ") — 휴대폰 저장 실패, 서버 저장 실패(`result.toast === SERVER_SAVE_FAIL`일 때만)에 붙임. 다른 계정으로 바뀜 문구, 지출 저장 실패(같은 `DEVICE_SAVE_FAIL`, `App.guestDurable.test.js:566·662`)는 그대로.
- 기대값 바꾼 기존 테스트 3개: `useDayDraft.cloudSerial.test.js` `FAIL_FAST`, `App.guestDurable.test.js:813·952`(9월 25·26일). 기존 `.autosave-status` 글 확인(`저장 실패`/`저장 완료` 포함)은 화면 읽기 글로 그대로 통과.
- 새 테스트: `AutoSaveStatus.test.js`(상태 4개 아이콘·읽기 글·실패 클래스), `DayLogPage.callActions.test.js` 합계·아이콘이 제목 묶음 안.
- 검사: 테스트 887 + 770, 타입·코드 검사·의존 방향·빌드 통과.
- 고치기 전 실패 확인: 새 테스트 1개 + 바꾼 테스트 4개(합계 묶음, 실패 문구 3개) 모두 수정 전 코드에서 실패.
- 실제 CSS로 다시 잰 값(저장소 밖 임시 화면, Chrome·Windows 글꼴): 아이콘 ~ 메뉴 단추 320px 2px, 375px 30px, 414px 49px("배정 차량" 날도 같음). 합계 줄 ~ 휴무 단추 6px(모든 경우), 휴무 단추 위치는 합계 있는 날·0원 날 같음(보통 날 64px, "배정 차량" 날 72px). 합계 줄 한 줄·화면 안. 다크모드·움직임 줄이기는 휴대폰 확인 필요.
- 코드 커밋: react-app `455e77d`(9개 파일, push 안 함). 남은 것: 보리 push → CI 초록 → 휴대폰 확인 → `[x]` 때 STATUS·로드맵 109 `[x]`.
- push 뒤 CI verify·deploy 초록(run 39909275175, react-app `455e77d`). 브라우저(AI, 개발 화면 22일): 저장 아이콘 오른쪽 끝 ~ 메뉴 단추 거리 320px 3px·375px 31px·414px 50px(안 겹침), 제목 한 줄(높이 27px), 합계 "운송료 합계 162,346원" 제목 아래 한 줄. 저장 중·완료·실패 아이콘 모양, 실패 알림 문구, "배정 차량" 있는 날은 데이터를 바꿔야 해서 화면에서 안 봄 — 보리 휴대폰 확인 필요.
- 2026-10-23 보리 승인("승인 다음"). 109-B·수정 `[x]`.

#### 사용자 확인 방법
1. 22일 일지 열기 → 제목 오른쪽에 작은 체크 원, 제목 바로 아래 "운송료 합계 612,346원", 그 아래 바로 휴무 단추.
2. 횟수 단추 누르기 → 아이콘이 도는 원 → 체크 원으로 바뀌는 동안 제목이 안 흔들리는지, 합계가 바로 바뀌는지.
3. 아무것도 없는 날·휴무 날 열기 → 합계 글만 없고 휴무 단추 위치는 22일과 같은지.
4. 인터넷을 끄고 횟수 바꾸기 → 빨간 느낌표 원, "10월 22일 운행일지 저장에 실패했습니다. 네트워크 상태를 확인해 주세요." 알림 문구.

### 사용자 확인 방법
1. 아래 메뉴 [달력] → 23일 칸 금액(47.5만) 확인 → 23일 눌러 일지 열기.
2. 제목 아래 작은 글씨 "운송료 합계 475,000원"이 보이는지.
3. 횟수 단추를 4회로 바꿔 보기 → 합계가 425,000원으로 바로 바뀌고 아래 칸이 안 밀리는지 → 다시 5회로.
4. [휴무] 켜기 → 글이 사라지고 아래 칸은 그 자리 그대로, 끄기 → 다시 보임.
5. 아무것도 입력 안 한 날 열기 → 제목 아래가 한 줄 비어 있고, 횟수를 넣으면 그 자리에 글이 나타나며 아래 칸이 안 밀림.

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
