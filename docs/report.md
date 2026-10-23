# docs/report.md — 현재 슬라이스 착수지시서

108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 109-A 달력 칸·홈 오늘 카드 하루 금액에 파렛트 넣기 `[x]`

로드맵 109 `[확인: 2026-10-23 보리 — "문제가 큰 부분, 로드맵에 올려놔", 지시서 "작성해"]`. 둘로 나눔(AI 판단, 파일 약 9개): **109-A** 하루 금액 계산 고치기(이번) → **109-B** 운행일지 하루 화면 "오늘 운송료 합계" 줄(보리가 고른 시안 "다" — 제목 바로 아래 작은 글씨, 109-A 계산을 그대로 씀). 이번엔 109-A만.

### 쉬운 요약
- 달력 칸 금액과 홈 오늘 카드 금액이 파렛트 금액을 빼고 계산되고 있다. 파렛트를 입력한 날은 이 두 곳 금액이 실제보다 적게 보인다.
- 하루 금액 계산에 파렛트를 더해, 달 정산 합계·매출·운송비 내역서와 같은 기준으로 맞춘다.
- 파렛트를 안 쓰는 계정·날은 숫자가 그대로다.

### 현재 상태 (코드 확인)
- 하루 금액: `domain/calendarBadges.ts:33-35` `dayFareTotal(record, unitPrice)` = 고정 노선 횟수 × 단가 + `callFareTotal`. 파렛트 없음.
- 쓰는 곳 2곳: 달력 칸 `features/calendar/ui/CalendarGrid.jsx:43` `dayWorkBadgeLabel(record, { inputMode, unitPrice })`(금액 입력 계정만 금액, 횟수 입력 계정은 "N회"), 홈 오늘 카드 `features/home/ui/HomeTodayCard.jsx:21-22`(금액 입력 계정은 금액, 횟수 입력 계정은 "N회, 금액").
- 파렛트를 넣고 있는 다른 계산(같은 조건 — 고정 노선 켜짐 + 거래처 파렛트 켜짐, 장수 × 거래처 `palletPrice`):
  - 달 정산 합계 `domain/monthSettlement.ts:101-105`(조건 `activeFixedOn && fixedRouteClient?.palletOn`)
  - 매출 `domain/financeOwnerDetail.ts:47-66`(`activeFixedOn && activePalletOn`)
  - 운송비 내역서 하루 줄 `features/documents/api/reportSummary.js:51-53`
- 그래서 파렛트를 쓰는 달은 달력 하루 칸을 다 더해도 아래 달 정산 합계와 안 맞는다.
- 단가 출처: `data/store/useMonthSettlement.ts:24-29` — `unitPrice`·`fixedRouteClient`·`activeFixedOn`을 만들지만 밖으로는 `unitPrice`만 돌려줌(38줄).

### 목표 상태 (기대 동작)
1. 하루 금액 = 고정 노선 횟수 × 단가 + **파렛트 장수 × 파렛트 단가** + 세부 운송료. 파렛트는 달 정산 합계와 같은 조건(고정 노선 켜짐 + 그 거래처 파렛트 켜짐)일 때만 더한다.
2. 달력 칸(금액 입력 계정)과 홈 오늘 카드 금액이 1번 숫자로 나온다.
3. 파렛트만 입력한 날(고정 0회·세부 없음)도 달력 칸에 금액이 보인다(금액 입력 계정). 횟수 입력 계정 칸은 지금처럼 "N회" — 파렛트는 운행 횟수가 아니라 횟수에 안 넣는다.
4. 파렛트가 꺼진 거래처·휴무 날·파렛트 0장인 날은 지금과 숫자가 같다.
5. 한 달 동안 하루 칸 금액을 더한 값이 달 정산 합계의 "운송료 + 파렛트" 부분과 같다(수수료·부가세는 하루 칸에 원래 안 들어감 — 그대로).
6. 기사 차량(서브 차량) 달력도 그 차량의 고정 노선 설정·거래처 파렛트 단가로 같은 규칙.

### 건드릴 파일
- `react-app/src/domain/calendarBadges.ts` — `dayFareTotal(record, unitPrice, palletPrice = 0)`에 `getPalletCount(record) × palletPrice` 더함, `dayWorkBadgeLabel` 옵션에 `palletPrice` 추가. 33줄 위 설명 주석을 아래로(최종 문구):
  `// 하루 금액 — 고정 노선 횟수 × 단가 + 파렛트 장수 × 파렛트 단가 + 세부 운송료. 파렛트 단가는 쓰는 쪽이 켜짐 조건을 보고 넘긴다(꺼졌으면 0).`
- `react-app/src/data/store/useMonthSettlement.ts` — `palletPrice`(= `activeFixedOn && fixedRouteClient?.palletOn ? palletPrice : 0`)를 함께 돌려줌. 달 합계 계산은 안 바꿈.
- `react-app/src/features/calendar/ui/CalendarPage.jsx`·`CalendarGrid.jsx` — `palletPrice`를 받아 `dayWorkBadgeLabel`에 넘김.
- `react-app/src/features/home/ui/HomePage.jsx`·`HomeTodayCard.jsx` — 같은 방식으로 넘김.
- 테스트(200줄 예외): `domain/calendarBadges.test.js` — 파렛트 더함·파렛트 단가 0이면 그대로·파렛트만 있는 날 금액 표시·횟수 모드는 "N회" 그대로. 달력 화면 테스트 1개 — 파렛트 켠 거래처로 하루 칸 금액 합 = 달 정산 운송료+파렛트. 홈 오늘 카드 테스트 1개.
- 문서(`[x]` 때): `docs/sot.md`에 "하루 금액(달력 칸·홈·일지)에 파렛트 포함, 달 합계와 같은 조건" 한 줄, STATUS·로드맵 109-A `[x]`.

### 안 건드릴 것
- `domain/monthSettlement.ts`·`financeOwnerDetail.ts`·`reportSummary.js` — 이미 파렛트를 넣음(위 현재 상태 근거).
- 파렛트 수수료(sot §459 "파렛트에는 수수료를 안 뗀다")·부가세 규칙 — 하루 칸은 원래 수수료·부가세 전 금액.
- 운행일지 하루 화면 — 109-B에서.
- 운비서 답(달·연 단위, 달 정산과 같은 계산) — 하루 금액을 안 씀.

### 검증
- CI 초록(테스트·타입·빌드·코드 검사·의존 방향).
- 새 테스트는 고치기 전 코드에서 실패하는지 확인(revert-check).
- 브라우저: 파렛트를 넣은 날의 달력 칸·홈 오늘 카드 금액, 달력 칸 한 달 합과 달 정산 합계 비교.

### 파일 길이
- `calendarBadges.ts` 82 → 약 85줄, `useMonthSettlement.ts` 39 → 약 42줄, `CalendarPage.jsx` 88 → 약 89줄, `CalendarGrid.jsx` 52 → 약 54줄, `HomePage.jsx` 86 → 약 87줄, `HomeTodayCard.jsx` 55 → 약 57줄.
- 모두 200줄 아래. 테스트 파일은 예외.

### 실패 시 처리
- 새 저장·재시도·대비 장치 없음. 계산 한 줄 고치기다.
- 달력 칸 합과 달 정산 합계가 안 맞으면 멈추고 원인과 수정 계획을 다시 써서 승인받는다.

### 진행 기록
- 교차검증: 현재 상태 근거(줄 번호·조건)는 코드와 맞음. 지시서에 없던 것 1가지 — `dayWorkBadgeLabel`은 "횟수 0, 금액 0이면 칸 비움"이라, 파렛트를 그대로 더하면 횟수 입력 계정의 파렛트만 있는 날 칸에 "0회"가 새로 생김. 목표 3(횟수 입력 칸은 지금처럼)에 맞게, 횟수 입력일 때 칸을 보일지는 파렛트를 빼고 판단(주석 1줄). 홈 오늘 카드도 횟수 입력·파렛트만 있는 날은 지금처럼 "아직 기록이 없습니다"(횟수가 있는 날은 "N회, 금액"에 파렛트 포함).
- 시작할 때 워킹카피에 커밋 안 된 `CalendarMonthSummary.jsx`·`.test.js`(빨간 점 설명 문구 "미수금") 변경이 있었음 — 이번 작업이 아니라 커밋에서 빼고 그대로 둠.
- 구현: `dayFareTotal(record, unitPrice, palletPrice = 0)`, `useMonthSettlement`가 `palletPrice`(= `activeFixedOn && fixedRouteClient?.palletOn`일 때 `parseCurrencyValue(palletPrice)`, 아니면 0)를 돌려줌, 달력·홈이 넘김. 기사 차량 달력은 같은 훅이 그 차량 `subFixedOn`·거래처로 계산(목표 6).
- 줄 수: `calendarBadges.ts` 82 → 79(옛 4줄 설명 주석을 1줄로), `useMonthSettlement.ts` 39 → 42, `CalendarPage.jsx` 88 → 89, `CalendarGrid.jsx` 52 → 53, `HomePage.jsx` 86 → 87, `HomeTodayCard.jsx` 55 → 56.
- 검사: 테스트 887 + 765(새 5개), 타입·코드 검사·의존 방향(연결 1720 → 1721, `useMonthSettlement` → `money.ts`)·빌드 통과.
- 고치기 전 실패 확인: 새 테스트 5개 중 4개 실패(파렛트 더함, 파렛트만 있는 날, 달력 칸·칸 합, 홈 오늘 카드), 1개("파렛트 단가 0·휴무면 그대로")는 안전 확인으로 통과.
- 코드 커밋: react-app `f6488ba`(9개 파일, push 안 함). 남은 것: 보리 push → CI 초록 → 브라우저 확인 → `[x]` 때 sot 한 줄·STATUS·로드맵 109-A → 109-B 지시서.
- push 뒤 CI verify·deploy 초록(run 39904469468, react-app `f6488ba`). 개발 화면 테스트 계정은 파렛트를 안 써서(달 정산에 파렛트 줄 없음) 달력 칸 한 달 합 355.8만 ≈ 운송료 3,559,258원으로 기존 동작만 확인 — 파렛트 넣은 날은 보리 화면 확인 필요.
- 브라우저 확인(AI, 개발 화면): 23일 고정 5회 × 50,000 + 파렛트 5장 × 5,000 + 세부 200,000 → 달력 칸 "47.5만", 홈 오늘 카드 "오늘 475,000원", 달 정산 "파렛트 회수 청구액 25,000원"과 맞음. 데이터는 바꾸지 않음(파렛트 5장은 이미 입력돼 있었음).
- 2026-10-23 보리 승인("109-A 승인, [x]로 확정해"). `[x]`.

### 사용자 확인 방법
1. 파렛트를 켠 거래처가 고정 노선에 연결된 계정(금액 입력)으로 로그인.
2. 오늘 일지에서 파렛트 2장 입력 → 아래 메뉴 [달력] → 오늘 칸 금액이 파렛트 금액만큼 늘었는지.
3. [홈] → 오늘 카드 금액도 같은 숫자인지.
4. 파렛트를 0장으로 되돌리면 두 곳 숫자가 원래대로 돌아오는지.

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
