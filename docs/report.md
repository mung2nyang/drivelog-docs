# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 99-A 운행일지 화면 나누기 — 콜 상세·고정 노선 처리 흐름을 훅으로 `[x]`

> **진행(2026-10-21) — 코드 커밋 `ca32941`** `[x]`: 보리 "착수지시서확정 작업진행해줘".
> - 교차검증(AGENTS §1): 234줄·처리 흐름 줄 위치·긴 주석 4곳·`patchDraft` 밖 사용 3곳·`onWorkChanged`·테스트 현황 모두 지시서와 같음. 고친 것 없음.
> - 테스트 먼저: 새 `DayLogPage.callActions.test.js`(152줄, 6개 — 고정 장소 저장 함수만 가짜로 실패). ①은 빈 일지에서 [+ 운행일지 추가]로 입력, ②~④·⑥은 미리 넣은 콜 1개(부산 → 대구, 50,000원)로 시작. 처음엔 입력 칸을 여는 3개가 끝나지 않고 멈춤 — 입력 칸의 뒤로가기 붙잡기(`usePanelBackGuard`)가 브라우저 기록을 보는데 테스트가 `MemoryRouter`(가짜 기록)로 그려 붙잡기를 끝없이 다시 쌓은 것. 화면 문제가 아니라 테스트 준비 문제라 `App.test.js`처럼 `BrowserRouter`로 바꿈(파일 머리 주석에 이유 1줄) → **옛 코드에서 6개 모두 통과** → 옮긴 뒤 같은 6개 통과(단언 그대로).
> - 한 일: 새 `hooks/useCallDetailActions.js`(78줄 — `patchDraft`·`runRoute`·`saveCall`·`deleteCall`·`togglePayment`·`togglePinnedLocation`, 알림·`ownerKey`는 안에서 `useToast`·`useSession`). `DayLogPage.jsx` 234 → **171줄**: 처리 흐름용 가져오기 8개 옮김, 머리 주석 4줄 → 2줄, 긴 주석 3곳 → 1줄씩(네 번째 123~124줄은 훅으로 옮기며 1줄).
> - 줄 대조(목표 1): 옮긴 본문에서 다른 줄은 함수 이름 4개(`handleRouteRun`→`runRoute`·`handleSaveCall`→`saveCall`·`handleTogglePinnedLocation`→`togglePinnedLocation`·`handleTogglePayment`→`togglePayment`), 주석 2줄 → 1줄, 도메인 함수 `togglePinnedLocation`을 `togglePinnedLocationIn`이라는 별명으로 가져옴(훅이 돌려주는 함수 이름과 겹쳐서, 2줄), `deleteCall`은 184줄 JSX 한 줄을 그대로 옮김. 그 밖 같음.
> - 결과: `npm test` unit 866 + app 661(6개 늘어남) = 1527 전부 통과, 타입 검사·빌드·`npm run depcheck`(위반 0) 통과, lint 경고 50 그대로.
> - 개발 서버(비회원, 거래처 한진 + 콜 1건): "세부 입력을 저장했습니다."·카드 1 → 수금 단추 "수금"·"수금 처리했습니다." → 다시 "미수"·"수금을 취소했습니다." → [수정]에 장소 칩 "부산, 대구" → 저장 "세부 입력을 수정했습니다." → [삭제] 카드 0. 콘솔 오류 0건. 고정 노선 칩은 테스트로만(비회원 기본 설정에 노선 없음).
> - 코드 커밋 react-app `ca32941`(push 안 함).
> - push 뒤 CI verify·deploy 초록(run 39543827717) — 테스트·타입 검사·빌드·의존 방향 검사 모두 성공.
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서의 3개 파일 ②새 층·저장 키 0, 새 파일 2개(훅·테스트)는 지시서대로 ③타입 꼼수 0 ④`DayLogPage` 171·`useCallDetailActions` 78, 테스트 152(§6 예외) ⑤새 테스트 6개는 카드 수·알림 문구·수금 단추 글자·칩 횟수와 고정 횟수 칸 값·저장 함수 호출 수를 직접 봄(가짜는 고정 장소 저장 함수 하나뿐), `BrowserRouter`로 바꾼 이유는 파일 머리 1줄 ⑥공용 영향 없음(화면 받는 값·`useDayDraft`·`inlinePanelActions` 그대로, `onWorkChanged` 그대로 — 91번) ⑦목표 1~4 대조. 옮긴 본문은 옛 줄과 같고 다른 줄은 진행 기록의 목록(이름 4개·주석 1줄·도메인 함수 별명) 그대로 확인. 훅 부르기는 일찍 끝나는(`periodNotice`) 줄보다 앞이라 훅 순서 규칙 문제 없음.
> - 배포본 로그인 확인(데스크톱 AI, 새 빌드 `index-BbnTa4H2.js`): 10월 21일 운행일지에서 [+ 운행일지 추가] → 즐겨찾기 장소 칩들 보임 → 확인상차 → 확인하차·테스터거래처·10,000원 저장 → 카드 1(미수) → [미수] "수금 처리했습니다."·단추 "수금" → 다시 "수금을 취소했습니다." → [삭제] 카드 0. 새로고침해도 카드 0(넣은 데이터 되돌림 확인). 고정 노선 칩은 이 계정 설정에 노선이 없어 테스트로만. 콘솔 오류 0건.
> - 보리 최종 승인(2026-10-21). **99번 끝.** 다음 101번.

보리 지시 "작성해"(2026-10-21). 97·99 분리설계 `[확인: 2026-10-21 보리 — "권하는대로 진행하는걸로"]`(설계 원문 `git show 1e9e6e2:docs/report.md`의 "분리설계" 절)의 네 번째이자 99번 마지막 슬라이스. 순서 97 → 99-C → 99-B → **99-A** → 101 → 100 → 91 → 94.

### 쉬운 요약
- 운행일지 화면 파일(234줄)에는 화면 짜기와 함께 "콜 상세 저장·삭제·수금 바꾸기, 고정 노선 횟수, 고정 장소 저장" 처리 흐름이 들어 있습니다. 이 흐름을 훅 파일 하나로 옮겨 화면 파일을 약 180줄로 줄입니다.
- 긴 설명 주석 4곳은 결론 1줄씩으로 줄입니다(경위는 이미 로드맵·커밋에 있음).
- 이 흐름들은 지금 알림 문구까지 확인하는 테스트가 없습니다. 그래서 97·99-C처럼 옮기기 전 동작 테스트를 먼저 붙입니다. 화면 모양·동작은 그대로입니다.

### 현재 상태 (코드 확인 — `features/day-log/ui/DayLogPage.jsx` 234줄, 마지막 변경 `26abaa1`)
- 처리 흐름(약 45줄): `patchDraft`(102줄)·`indexOfCall`(104줄), `handleRouteRun`(112~116줄, 고정 노선 칩 +/−), `handleSaveCall`(119~128줄, 콜 상세 새로 저장·수정 → 알림 "세부 입력을 저장했습니다."/"세부 입력을 수정했습니다."), `handleTogglePinnedLocation`(131~139줄, 고정 장소 저장 → 실패 알림 "고정 장소 저장에 실패했습니다."), `handleTogglePayment`(142~151줄, 수금 처리/취소 → 알림 "수금 처리했습니다."/"수금을 취소했습니다."), 콜 삭제는 184줄 JSX 안에 한 줄로.
- 긴 설명 주석: 82~86줄(고정노선 거래처 찾기), 94~96줄(나가기 확인), 106~107줄(인라인 패널), 123~124줄(콜 id 타입 좁히기).
- `patchDraft`는 처리 흐름 밖에서도 씀(휴무 토글·고정 횟수·팔레트 칸, 162·168·171줄).
- `onWorkChanged`(55·62·65줄)는 안 쓰이는 값 — **91번 몫이라 이번엔 그대로**.
- 테스트: 이 화면을 혼자 그리는 테스트는 `AssignmentPeriodPage.test.js`(배정 기간 안내 4개)뿐. 위 알림 문구 5개를 확인하는 테스트 0개(grep), 고정 노선 횟수는 `App` 테스트들이 저장 값으로 봄.

### 목표 상태 (기대 동작)
1. 새 `features/day-log/hooks/useCallDetailActions.js`(약 75줄): `useCallDetailActions(draft, dispatch, { dateKey, clients, settings, editingCallId })` → `{ patchDraft, runRoute, saveCall, deleteCall, togglePayment, togglePinnedLocation }`. 알림·`ownerKey`는 훅 안에서 `useToast()`·`useSession()`. 본문은 지금 함수들을 그대로 옮김(이름만 위처럼, 콜 삭제는 JSX 한 줄을 `deleteCall(id)`로).
2. `DayLogPage.jsx`(약 180줄)에는 데이터 읽기·보이는 값 계산·나가기 확인·인라인 패널 묶기·화면 짜기만 남음. 처리 흐름에 쓰던 가져오기(`applyFixedRouteRun`·`getFixedRouteCounts`·`upsertCallDetail`·`removeCallDetail`·`getDetailPaymentSummary`·`togglePinnedLocation`·`toggleCallPaymentStatus`·`savePracticeSettings`)는 훅으로 옮겨감.
3. 긴 주석 4곳 → 결론 1줄씩(123~124줄 것은 훅으로 옮기면서). 머리 주석(2~5줄)도 지금 구조에 맞게 1~2줄로.
4. 알림 문구·저장 값·화면 모양 변화 0. `onWorkChanged`·`useDayDraft`·`inlinePanelActions`는 안 바뀜.

### 건드릴 파일
- `features/day-log/ui/DayLogPage.jsx`(줄어듦), 새 `features/day-log/hooks/useCallDetailActions.js`
- 새 `features/day-log/ui/DayLogPage.callActions.test.js`(옮기기 전 동작 테스트)

### 안 건드릴 것
- `onWorkChanged`·`useDayDraft`·`dayDraftLifecycle`(91번), `useExpenseForm`(101번), `CallDetailForm`·`CallDetailList`·`inlinePanelActions`, 디자인 파일, 도메인 계산 함수.

### 검증
- **옮기기 전** 새 테스트를 옛 코드에서 먼저 통과 확인(그리는 방법은 `AssignmentPeriodPage.test.js`처럼 화면 혼자 + `withSession`, 알림은 `withToast`): ① 콜 상세 새로 저장 → 카드 1개, "세부 입력을 저장했습니다." ② 그 카드 수정 → "세부 입력을 수정했습니다." ③ 수금 단추 → "수금 처리했습니다.", 다시 → "수금을 취소했습니다." ④ 콜 삭제 → 카드 0개 ⑤ 고정 노선 칩 +1 → 칩 횟수와 고정 횟수가 같이 1 오름 ⑥ 고정 장소 저장이 실패하면 "고정 장소 저장에 실패했습니다.". 폼 입력이 너무 복잡한 흐름은 미리 넣은 콜로 시작해도 됨(진행 기록에 적음). 옮긴 뒤 같은 테스트 통과(단언 그대로).
- 옮긴 함수 본문을 옛 줄과 줄 단위로 대조(진행 기록에 다른 줄 목록).
- `npm test`·타입 검사·빌드·`npm run depcheck` 통과, lint 경고 수 그대로.
- 배포본 로그인 확인(데스크톱 AI): 운행일지에서 콜 상세 추가 → 수금 처리 → 취소 → 삭제, 고정 노선 칩 +1 → −1로 되돌림(데이터는 처음 상태로 되돌림), 즐겨찾기 장소 칩 보임.

### 파일 길이
- `DayLogPage.jsx` 약 180, `useCallDetailActions.js` 약 75. 테스트 파일은 §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(코드 자리만 옮김, 고정 장소 저장 실패 알림도 지금 그대로).
- 새 테스트가 옛 코드에서 실패하면 화면을 고치지 말고 멈추고, 기대와 다른 동작을 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법
- 화면은 바뀌지 않음. push 뒤 AI가 CI와 배포본 운행일지(콜 상세·수금·고정 노선)를 확인해 보고.

### 확인할 점
- 없음(승인된 분리설계 그대로. 줄 수 추정만 설계의 "약 165"에서 "약 180"으로 — 처리 흐름 밖에서도 쓰는 `patchDraft` 호출 줄이 남기 때문).
