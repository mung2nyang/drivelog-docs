# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 47-B 미수금 화면·달력 점·하단 메뉴 손질 `[x]`

> **진행(2026-10-16)**: 보리 "착수지시서 확정, 작업 진행해". 코드 커밋 react-app `f1a28aa`(push 전).
> 새 테스트 4개 고치기 전 실패 확인(기사 계정은 "메인 차량" 배지가 실제로 보여서 실패) → 고친 뒤 `npm test` 1,342개 통과.
> **지시서와 다른 점 1개**: 설명 줄 스타일을 `calendar-grid.css`(184줄 → 202줄, 200줄 넘음) 대신 미수금 카드 스타일이 있는 `calendar.css`(100 → 118줄)에 넣음. `calendar-grid.css`는 안 바뀜.
> AI 개발 서버 비회원(결제·수금 잠깐 켜고 시험 운행 1건 → 확인 뒤 지우고 설정 되돌림): 달력 "● 빨간 점: 미수금이 남은 날", 상세 0건 "남은 미수금이 없습니다.", 하단 메뉴 그림 숨김·지금 탭 표시 확인.
> ①(기사 배지 없음)은 연동 기사 계정이 필요해 테스트로 확인. 리뷰 §5: 새 파일은 테스트 2개뿐, 타입 꼼수 없음, 전부 200줄 이하.
> 보리 push 뒤 CI verify·deploy 초록(run 38840511592).
> 배포본 확인(보리 지시, 이미 로그인된 차주 시험 계정, 보기만·데이터 안 바꿈): 미수금 목록 차주 배지 그대로("메인 차량"·"2222"), 기록 없는 거래처 주소 "남은 미수금이 없습니다.", 달력 미수금 카드 아래 "● 빨간 점: 미수금이 남은 날"(빨간 점 5곳 있는 달), 하단 메뉴 그림 숨김·지금 탭 표시.
> 연동 기사 계정 배지 없음(①)은 그 계정 로그인이 필요해 배포본에서 못 봄 — 테스트로만 확인. 보리 최종 `[x]` 승인(2026-10-16).

로드맵 47번(글자·표시 손질 묶음, ⑤)의 나머지 4건. 47-A `[x]`(`a9696bf`) 다음, 47번 마지막 조각.
파일은 7개지만 대부분 1~3줄이고, 다시 쪼개면 47번이 3조각이 돼서(AGENTS §3) 한 번에 한다.

### 쉬운 요약
- 기사 미수금 화면에 자기 차가 "메인 차량"으로 나오던 것을 없애고, 미수금이 없는 거래처 주소에 "모두 입금 완료"라고 잘못 말하던 안내를 바로잡는다.
- 달력의 빨간 점이 무슨 뜻인지 한 줄 설명을 붙이고, 하단 메뉴를 화면 읽기 기능이 더 정확히 읽게 손본다.
- 금액·저장은 하나도 안 바뀐다.

### 현재 상태 (코드 확인)
- ④ 연동 기사 계정은 배정 차량 일지를 `main` 칸에 담고(`hydrateEmployedDriver.js:6-7·40-50`), 차량 목록엔 그 차를 기사 차량(`sub`)으로 둔다(`hydrateEmployedDriverCar.js:16`).
  미수금은 `main` 칸에 "메인 차량" 이름을 붙이고(`financeReceivables.js:43`), 기사 차량이 하나라도 있으면 차량 배지를 보여서(`useReceivablesData.js:31`, `ReceivablesListPage.jsx:73`, `ReceivableItemCard.jsx:34`) 기사 화면에 자기 차가 "메인 차량"으로 보인다.
  미수금 화면은 지금 기사 계정인지 모른다(`AppShellRoutes.jsx:91` — 일지·서류 화면은 `isEmployedDriver`를 받음 `:72·92`).
- ⑤ 미수금 상세 주소의 거래처·월에 남은 미수가 0건이면 늘 "모든 미수금이 입금 완료 처리되었습니다."(`ReceivablesDetailPage.jsx:67`) — 기록이 아예 없는 거래처여도 같은 말.
- ⑥ 달력 날짜 왼쪽 위 빨간 점(`CalendarCell.jsx:49`, 미수 남은 날)에 설명이 없다. 달력 위 "이번 달 미수금 N원이 있습니다." 카드(`CalendarMonthSummary.jsx:41-50`)는 점이 있을 때만 보인다(같은 달 미수 합계 > 0).
- ⑦ 하단 메뉴 버튼 4개는 **이미 글자 이름이 있다**("홈"·"달력"·"매출"·"마이페이지" `<span>`, `BottomNav.jsx:72`) — 로드맵 문구 "이름 없음"은 정확하지 않음. 남은 문제는 그림(svg)이 읽기 대상에 그대로 있고(`:71`), 지금 탭 표시가 색뿐이라는 것(`:69`).

### 목표 상태 (기대 동작)
1. 연동 기사 계정 미수금 목록·상세에 차량 배지("메인 차량"·차량 번호)가 안 보인다. 차주 화면 배지는 지금 그대로.
2. 미수금 상세에서 남은 건이 0건이면 **"남은 미수금이 없습니다."** — 다 입금한 경우·기록 없는 거래처 둘 다 맞는 말.
3. 달력 "이번 달 미수금" 카드 아래에 작은 빨간 점과 함께 **"빨간 점: 미수금이 남은 날"** 한 줄이 보인다. 카드가 안 보이면(미수 0) 이 줄도 없다.
   시안: `[!] 이번 달 미수금 130,000원이 있습니다.` / `● 빨간 점: 미수금이 남은 날`(점은 달력 점과 같은 색·크기, 글씨는 카드 글씨보다 작게)
4. 하단 메뉴: 그림은 읽기에서 빼고, 지금 탭에 "현재 화면" 표시(`aria-current="page"`). 보이는 모양은 그대로.
5. 금액·저장·화면 이동은 하나도 안 바뀐다.

### 건드릴 파일
- `react-app/src/app/AppShellRoutes.jsx` — 미수금 화면에 `isEmployedDriver` 넘김(`:72·92`와 같은 방식, 1줄)
- `react-app/src/components/ReceivablesPage.jsx` — 받아서 목록·상세에 넘김
- `react-app/src/components/receivables/useReceivablesData.js` — 기사 계정이면 `hasSubCars` false
- `react-app/src/components/receivables/ReceivablesListPage.jsx` — 받은 값을 `useReceivablesData`에 넘김
- `react-app/src/components/receivables/ReceivablesDetailPage.jsx` — 같은 일 + `:67` 문구
- `react-app/src/components/calendar/CalendarMonthSummary.jsx` + `calendar-grid.css` — 설명 줄, 새 클래스 `unpaid-dot-legend`(이 줄 전용, 다른 곳 안 씀)
- `react-app/src/components/BottomNav.jsx` — svg `aria-hidden`, 지금 탭 `aria-current`
- 테스트: `CalendarMonthSummary.test.js`(설명 줄 있음·없음), 새 `components/BottomNav.test.js`, 새 `components/receivables/ReceivablesPages.test.js`(기사 계정 배지 없음·차주 배지 있음·0건 문구)
- 공용 영향: `.unpaid-dot` 클래스는 안 바꿈(새 클래스만 추가). `hasSubCars`는 미수금 화면 3곳만 씀(`grep hasSubCars` → `ReceivablesListPage.jsx:73·122`, `ReceivablesDetailPage.jsx:73`).

### 안 건드릴 것
- `financeReceivables.js`의 "메인 차량" 이름(`:43`) — 차주 화면에선 맞는 이름, 기사 화면에선 배지를 안 그리므로 안 보임.
- 미수금 금액·입금 처리(`useReceivablesActions.js`) — 손대지 않음.
- 달력 점 모양·위치(`calendar-grid.css:159-173`), 하단 메뉴 모양(`bottom-nav.css`).
- 48~54번(UI 점검), 서버(DB).

### 검증
- 로컬 `npm test` — 새 테스트는 고치기 전 실패 확인(revert-check).
- 브라우저: AI가 개발 서버 비회원으로 ②·③·④ 확인(미수 운행 1건 기기에만 넣고 확인 뒤 지움). ①은 연동 기사 계정이 필요 → 테스트 + 보리 확인.

### 파일 길이
- `AppShellRoutes.jsx` 105 → 105 / `ReceivablesPage.jsx` 23 → 24 / `useReceivablesData.js` 33 → 34
- `ReceivablesListPage.jsx` 129 → 131 / `ReceivablesDetailPage.jsx` 104 → 106
- `CalendarMonthSummary.jsx` 166 → 170 / `calendar-grid.css` 184 → 195 / `BottomNav.jsx` 77 → 78
- 전부 200줄 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(표시만 바꿈). 실패하면 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법
1. 연동 기사 계정 → 미수금/정산 관리 → 목록·상세 카드에 "메인 차량" 배지가 없으면 성공.
2. 차주 계정(기사 차량 있음) → 미수금 → 배지("메인 차량"·차량 번호) 그대로.
3. 미수 운행이 있는 달 달력 → 위 미수금 카드 아래 "● 빨간 점: 미수금이 남은 날".
4. 미수금 상세에서 마지막 건을 입금 완료 → "남은 미수금이 없습니다."
