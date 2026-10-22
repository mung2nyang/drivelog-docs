# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 91 일지 저장 훅 `useDayDraft.js` 단순하게 `[x]`

보리 지시 "91번 착수지시서 써줘"(2026-10-22). 로드맵 91, 순서 100 → **91** → 94(`[확인: 2026-10-21 보리 — "권하는 순서대로"]`). 90번(`8aa427a`)이 남긴 빈 칸 지우기를 같이 함(로드맵 91 "같이 지움").

> **결정 `[확인: 2026-10-22 보리 — "가. sot §3 그대로 두고 지시서 범위대로"]`**: 기기 저장 실패 장치 4종(대기 저장·"안전하지 않음" 표시·대기 주인·활동 등록)은 그대로 두고 훅 안의 얽힘만 푼다. sot §3 바뀌는 것 없음.

> **진행(2026-10-22, 터미널 AI·데스크톱 AI)** `[x]` 코드 커밋 react-app `73e2a52`, CI 초록, 브라우저 확인 1~4 통과, 보리 최종 승인(2026-10-22).
> - 교차검증: 지시서 사실(줄 수·메모 칸 11개·`onWorkChanged` 넘기는 곳 없음·`useMountedRef` 한 곳)이 코드와 맞음. 테스트 3개는 `useDayDraft(ownerKey, DK)`로 불러 인자를 빼도 안 고쳐도 됨. 일지 화면은 `key={ownerKey:logId:dateKey}`로 열려 저장 담당을 한 번 만들어도 됨(`MainPageRoute.jsx:65`).
> - 지시서와 다르게 한 것 1가지: 최신 draft를 그리는 중에 메모 칸에 넣으면 lint 경고가 2개 늘어(50 → 52), 그리기 직후(`useLayoutEffect`) 저장 담당에 넘기는 방식으로 바꿈. 그래서 훅 메모 칸은 `initialDraftRef` 1개만 남음(지시서 2개). 알림 함수도 효과에서 넘김(`setToast`). 효과 순서(열림 표시 → 타이머 → 옛 id 저장 → 닫을 때 저장 → 활동 내림)는 그대로.
> - 결과: `useDayDraft.js` 195 → 63줄, `dayDraftLifecycle.js` 97 → 180줄, `DayLogPage.jsx` 171 → 170줄, 테스트 46 → 31줄. 타입 검사 통과, `npm test` unit 878·app 663 전부 통과(수 그대로), 고친 테스트는 `dayDraftLifecycle.test.js` 하나, 빌드 성공, depcheck 위반 0, lint 경고 50 그대로. grep `onCommitted|onWorkChanged|useMountedRef` 운영 파일 0. 테스트 기록의 act 경고 10건은 차량·거래처·초대·설정 테스트 것(일지 아님).
> - 코드 커밋 react-app `73e2a52`(2026-10-22, 4개 파일). push 뒤 CI verify·deploy 초록(run 39716618533).
> - §5 리뷰(데스크톱 AI): 7개 문제 없음 — 바뀐 파일 4개 = 지시서 "건드릴 파일", 새 파일·저장 키·장치 0, 타입 꼼수 0, 모두 200줄 이하(`dayDraftLifecycle.js` 180), 테스트는 없어진 `onCommitted` 횟수 확인만 빠짐(세 장면 그대로), `onCommitted|onWorkChanged|useMountedRef` 운영 파일 0. 목표 2번 "메모 칸 2개"는 1개로 더 줄어 충족(위 다르게 한 것). 닫을 때 순서(닫힘 표시 → 남은 저장 → 활동 내림)·옛 판 처리·대기 저장 등록이 옛 코드와 같음을 대조. 훅이 돌려주는 `commitNow`는 바깥에서 부르는 곳 없음(grep).
> - 브라우저 확인(2026-10-22, 데스크톱 AI, 보리 지시 "너가 확인해", 개발 서버 5174·비회원): 1번 횟수 3 입력 → "저장 중…" → 1.5초 뒤 "저장 완료", 기기 저장 3, 닫았다 열면 3. 2번 9 입력 50ms 뒤 뒤로가기 → 누른 순간 기기 값은 옛 7(화면 전환 전), 0.1초 뒤 9(0.6초 타이머 전에 닫을 때 저장), 다시 열면 9. 3번 4 입력 50ms 뒤 새로고침 → 확인 창 없이 새로고침(navigation type reload), 열면 4·기기 값 4. 콘솔 에러 0.
> - 4번(로그인 메인 차량, 보리가 로그인한 뒤 데스크톱 AI, 빈 날짜 2026-10-31 사용): 3 입력 → 0.7초까지 "저장 중", 0.8초에 "저장 완료" → 페이지 새로 불러와도 3(로그인 일지는 기기에 안 남음 — 기기 저장 키에 10-31 없음 확인, 서버 값). 5 입력 50ms 뒤 뒤로가기 → 새로 불러오면 5(닫을 때 서버 저장). 끝나고 비워서 "저장 완료" → 새로 불러와도 처음과 같은 빈 날. 콘솔 에러 0.
> - 보리 최종 승인(2026-10-22). 다음 94.
> - 코드 커밋 react-app `73e2a52`(2026-10-22, 보리 "컴밋해", 4개 파일, push 안 함 — push는 보리).
> - 남은 것: 보리 push → CI → §5 리뷰 → 보리 최종 승인.

### 쉬운 요약
- 운행일지를 고치면 0.6초 뒤 저절로 저장하는 부분이, 저장 진행 상태(타이머·저장 대기·몇 번째 고침인지·서버 저장 중)를 메모 칸 11개에 흩어 들고 있고, 200줄을 맞추려 일부를 옆 파일로 빼면서 값 14개를 손으로 넘깁니다. 이 상태를 "저장 담당" 하나에 모아, 같이 읽어야 할 코드를 한 파일에 둡니다.
- 저장되는 때·내용·실패 안내·나가기 전 확인은 하나도 안 바뀝니다. 기존 저장 테스트가 고치지 않은 채로 전부 통과하는 것으로 증명합니다.

### 현재 상태 (코드 확인, react-app `de91e4d`)
- `features/day-log/hooks/useDayDraft.js` 195줄. 메모 칸(`useRef`) 10개 + `useMountedRef` 1개: `draftRef`·`timerRef`·`initialDraftRef`·`onCommittedRef`·`showToastRef`·`hasPendingRef`·`draftRevRef`·`savingRef`·`againRef`·`activityRef`·`mountedRef`(56~81줄).
- 기기 저장 길은 `dayDraftLifecycle.js:83` `commitLocalDayWrite`로 빠져 있고, 훅이 값 14개(메모 칸 5개 포함)를 넘김(`useDayDraft.js:93`). 서버 저장 결과 처리 `settleCloudDayWrite`도 값 11개를 받음(`useDayDraft.js:122~125`).
- `onCommitted` 인자는 아무도 안 넘김: `DayLogPage.jsx:53·55`가 `onWorkChanged`를 받아 넘기지만, 이 화면을 여는 유일한 곳 `app/routes/MainPageRoute.jsx:62~76`에 `onWorkChanged`가 없음(grep `onWorkChanged` 운영 파일 3줄 모두 일지 쪽 자기 선언). 90번에서 알림 다시 세기를 없앤 뒤 남은 빈 칸.
- `useMountedRef`는 `dayDraftLifecycle.js:12`에 있고 `useDayDraft.js` 한 곳만 씀(grep). `account/hooks/usePersonalInfoDraft.js`는 자기 안에 따로 씀 — 범위 밖.
- 이 훅을 거치는 테스트: `useDayDraft.cloudSerial`(S-1~S-4, 72-7)·`strictMode`(2개)·`unloadFlush`(72-5, 72-6)·`app/App.guestDurable`·`App.unsafeQuotaFailSyncSpy`·`App.legacyCallInit`·`App.test`·`DayLogPage.callActions` 등. `dayDraftLifecycle.test.js`(3개)만 안쪽 함수를 직접 부름.

### 목표 상태 (기대 동작)
1. 저장 진행 상태(타이머·저장 대기·판 번호·서버 저장 중·다시 저장·화면 열림·활동 등록 표)를 `dayDraftLifecycle.js`의 `createDayDraftSaver(...)` 하나가 들고, 기기 저장·서버 저장 결과 처리·"저장됨" 처리를 그 안에서 함. 훅은 `useState`로 이것을 한 번 만들고, 편집·마운트·닫기 때 그 메서드만 부름.
2. 훅의 메모 칸은 `draftRef`·`initialDraftRef` 2개만 남음(지금 11개). `useMountedRef`는 지움(열림 표시는 저장 담당 안으로).
3. 값 14개·11개를 넘기던 호출이 없어짐. `commitLocalDayWrite`·`settleCloudDayWrite`·`queueFailedDayWrite`는 저장 담당 안쪽으로 들어감. `settlePendingDayWrite`만 테스트를 위해 내보내되 인자는 (진행 상태, 화면 상태 바꾸기, 판 번호, 성공 여부) 4개로.
4. `useDayDraft(ownerKey, dateKey, logId)` — `onCommitted` 인자 없앰. `DayLogPage.jsx`의 `onWorkChanged` 칸과 설명 줄 없앰.
5. 저장 동작은 그대로: 0.6초 디바운스, 열기만 하면 저장 안 함, 옛 콜 id 채우기 저장, 같은 날짜 서버 저장 한 번에 하나, 옛 판 성공은 "저장 대기"를 안 내림, 닫을 때 남은 것 저장, 새로고침·숨김 때 바로 저장, 저장 공간 부족 때 대기 저장 등록(실패하면 "안전하지 않음" 표시), 실패 안내 문구·횟수.
6. 기존 테스트는 `dayDraftLifecycle.test.js` 말고 한 글자도 안 고친 채 전부 통과. `dayDraftLifecycle.test.js`는 같은 세 장면(옛 판 성공은 대기 유지·최신 판 성공은 "저장됨"·실패는 아무것도 안 바꿈)을 새 인자로 그대로 검사(없어진 `onCommitted` 횟수 확인만 빠짐).

### 건드릴 파일
- `features/day-log/hooks/useDayDraft.js` — 메모 칸을 저장 담당으로 바꾸고, `onCommitted` 인자와 긴 설명 주석 정리
- `features/day-log/hooks/dayDraftLifecycle.js` — `createDayDraftSaver` 만들고 기존 함수 4개를 그 안으로, `useMountedRef` 지움
- `features/day-log/ui/DayLogPage.jsx` — `onWorkChanged` 칸·설명 줄 지움, 훅 부르는 인자 하나 줄임
- `features/day-log/hooks/dayDraftLifecycle.test.js` — 위 6번대로 새 인자에 맞춤
- 공용 상태값 영향(§5-6): `onWorkChanged`·`onCommitted`를 쓰는 곳은 위 파일뿐(grep 운영 파일 3줄 + 이 테스트), `useMountedRef`도 위 파일뿐(grep).

### 안 건드릴 것
- 장치 4종 자체 — `data/sync/pendingWorkDataWrites*.js`·`durableWriteGuard.js`·`pendingLogOwner.js`·`dayDraftActivity.js`·`workDataSave.js`, 재시도 듣기(`app/pendingWriteRetryListeners.js`) — 부르는 방법·순서만 그대로 옮김.
- 서버 저장 `data/sync/dayLogCloudCommit.js`·갈래 판단 `mainDayLogRouting.js`, 화면 상태 `day-log-reducer.js`, 화면 모양.
- `usePersonalInfoDraft.js`의 같은 모양 코드(따로 원하면 로드맵 등재), sot §3(위 결정대로 그대로), 94번.

### 검증
- 플레이북 트리거 해당(기기 저장을 부름) — §3 실패 주입은 이미 `App.guestDurable`·`App.unsafeQuotaFailSyncSpy`가 저장 공간 부족을 화면 경로로 주입해 검사함. 이 테스트들을 안 고친 채 통과하는 것이 이번 증명. 새 실패 경로는 안 만듦.
- `npm test`(테스트 수 그대로, 고친 테스트 파일은 `dayDraftLifecycle.test.js` 하나뿐인지 `git diff --stat`으로), `npm run typecheck`, `npm run build`, `npm run depcheck`, lint 경고 수 그대로.
- 대조: grep `onCommitted|onWorkChanged|useMountedRef` 운영 파일 0, `useDayDraft.js`의 `useRef(` 2개.
- 브라우저: 아래 "사용자 확인 방법".

### 파일 길이
- `useDayDraft.js` 195 → 약 110줄. `dayDraftLifecycle.js` 97 → 약 160줄. `DayLogPage.jsx` 171 → 약 169줄. 모두 200줄 이하. 테스트 46 → 약 45줄.

### 코드에 넣을 주석(최종 문구)
- `dayDraftLifecycle.js` 머리: `// 일지 한 날짜의 저장 담당 — 타이머·저장 대기·판 번호·서버 저장 중을 한곳에 두고 기기/서버 저장과 결과 처리를 한다.`
- `useDayDraft.js` 머리: `// 일지 임시 편집 — Store 값을 복제해 들고, 고치면 0.6초 뒤 저장 담당(dayDraftLifecycle.js)이 저장한다. 닫을 때 남은 것을 저장한다.`
- 옛 판 처리(그대로 옮김): `// 더 최신 고침이 있으면 옛 저장 성공으로 "저장 대기"를 내리지 않는다 — 내리면 최신 고침이 유실된다.`
- 그 밖의 긴 설명 주석(훅 53~81·137~146·161~180줄)은 결론 1줄로 줄이고 경위는 이 지시서에만.

### 실패 시 처리
- 새 저장 장치·층 없음(장치 4종은 옮기기만). 테스트·빌드·화면 확인이 실패하면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. `npm run dev` → 비회원으로 달력 날짜 하나 열기 → 횟수 바꾸기 → 1초 뒤 "저장됨" 표시 → 닫았다 다시 열면 바뀐 값.
2. 고치자마자(1초 안에) 뒤로가기 → 다시 열면 바뀐 값.
3. 고치자마자 새로고침 → 확인 창 없이 새로고침되고 값이 남아 있음.
4. 로그인 계정 메인 차량으로 1·2 반복 → 같은 결과.
