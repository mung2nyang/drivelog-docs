# docs/report.md — 현재 슬라이스 착수지시서

116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 118 지출 저장 중 일부만 서버에 반영될 수 있음 `[x]`

외부 코드 리뷰(2026-10-26), 로드맵 118 `[확인: 2026-10-26 보리 — "세개 로드맵에 올려놔", 117·118 "권장순서대로"]`. 앞 슬라이스 116 `[x]`(react-app `1357315`·DB `0030`). **DB 변경 없음**(앱 코드만).

### 쉬운 요약
- 지출 저장은 차량마다, 종류(주유·정비·기타)마다 서버에 따로 씁니다. 지금은 "넣기"와 "지우기"가 섞여 진행돼서, 한 항목을 **다른 차량이나 다른 종류로 옮기는 저장**이 중간에 끊기면 옛 자리에서는 지워졌는데 새 자리에는 아직 안 들어간 상태가 될 수 있습니다. 그러면 그 항목이 서버에서 사라집니다.
- 모든 넣기·고치기를 먼저 하고 지우기는 맨 뒤에 몰아서 합니다. 중간에 끊겨도 항목이 사라지지 않고, 최악이 "두 줄"(불러올 때 하나로 보이고, 다음 저장 때 정리됨)이 됩니다.
- 지금 화면에서는 항목을 옮기는 길이 없어서 실제로 일어나지는 않습니다(아래 "현재 상태"). 코드 주석이 약속한 "유실 대신 중복"을 모든 방향에서 지키도록 순서만 바꿉니다.

### 현재 상태 (코드 확인, 2026-10-27)
- `data/sync/expensesSave.ts:46-54`: 차주 저장은 차량 칸마다 `syncExpenseItemsForVehicle`를 차례로 부름. 순서는 `domain/expenseVehicleRouting.ts:59-62` 서브 칸 먼저·메인 맨 뒤. 주석(`:46`)은 "중간 실패 시 유실 대신 중복"이라고 적혀 있음.
- `data/sync/syncExpenseItems.ts:125-130`: 한 칸 안에서 주유 → 정비 → 기타 순으로 `syncKind`, `syncKind`(`:73`~)는 그 종류의 넣기·고치기 뒤 곧바로 지우기.
- 그래서 지우기가 넣기보다 먼저 오는 경우: ⓐ 서브 → 메인 옮김(서브 칸 지우기가 먼저, 메인 넣기가 나중) ⓑ 앞 서브 → 뒤 서브 옮김 ⓒ 같은 칸에서 종류 바꿈(주유 → 정비, 주유 → 기타, 정비 → 기타). 이때 끊기면 항목이 서버에서 사라짐. 메인 → 서브와 그 반대 방향 종류 바꿈만 "유실 대신 중복".
- 지금 화면에서 옮기는 길은 없음: 고치기 창은 차량·종류 칸이 없고(`features/expenses/ui/ExpenseFormModal.jsx`), 고칠 때 입력값은 원래 항목의 종류·차량번호를 그대로 가져감(`domain/expenseDraft.ts:26-33` `expenseToDraft`). 실제로 칸이 바뀌는 건 옛 항목을 메인 칸에서 연동 서브 칸으로 옮겨 넣을 때(5-B-2)뿐이고, 이건 메인 → 서브라 이미 "넣기 먼저".
- 로드맵 118의 "확인 못 한 것"(두 줄이 남으면 어느 차량 지출로 보이나): 불러오기는 메인 칸 지출을 먼저 읽고(`data/hydrate/hydrate.ts:130-145`) 서브 칸을 뒤에 붙인 뒤 id로 하나만 남김(`data/hydrate/hydrateOwnerDriverExpenses.ts:49`, 먼저 읽은 것 유지). 그래서 메인 칸 것이 보이고, 다음 저장 때 다른 칸 줄은 "이번에 다른 칸으로 간 항목"(`expensesSave.ts:49-50`)으로 지워짐.
- 지출 하나 넣기·고치기·지우기는 쓰기가 한두 번이라, 끊겨도 그날 빈 하루 기록 줄만 남음(지출을 넣을 때 원래 만드는 줄과 같은 모양) — 금액이 섞이는 경우는 못 찾음.

### 목표 상태 (기대 동작)
1. 차주 저장: 모든 차량 칸·모든 종류의 넣기·고치기가 끝난 뒤에 지우기를 한다. 넣기·고치기 중 하나라도 실패하면 지우기는 하나도 안 한다.
2. 연동 기사 저장(배정 차량 한 칸): 그 칸 안에서 세 종류 넣기·고치기 뒤에 지우기.
3. 무엇을 넣고·고치고·지우는지는 지금과 같다(대상 판단·순번·작성자 유지·옛 항목 옮겨 넣기·빠진 항목 다시 넣기 그대로). 바뀌는 건 지우기 시점뿐.
4. 실패하면 지금처럼 실패 알림, 화면은 옛 목록(Store 반영 없음).

### 건드릴 파일
- `react-app/src/data/sync/syncExpenseItems.ts` — `syncKind`가 지울 서버 행을 바로 지우지 않고 목록에 모은다. 넣기·고치기만 하는 `upsertExpenseItemsForVehicle`(지울 목록을 돌려줌)와 모은 행을 지우는 `deleteExpenseRows`를 새로 내보내고, 기존 `syncExpenseItemsForVehicle`는 "넣기·고치기 → 지우기"로 둘을 이어 부른다(이름·인자 그대로). 주석 최종 문구:
  - 지우기 모으는 곳: `// 지우기는 모아 두었다가 모든 넣기·고치기 뒤에 한다 — 중간에 끊겨도 유실 대신 중복.`
  - `upsertExpenseItemsForVehicle`: `/** 넣기·고치기만 하고, 지울 서버 행 목록을 돌려준다(지우기는 deleteExpenseRows). */`
  - `deleteExpenseRows`: `/** 모아 둔 서버 행을 지운다. */`
- `react-app/src/data/sync/expensesSave.ts` — 차주 경로에서 칸마다 `upsertExpenseItemsForVehicle`로 지울 목록을 모으고, 반복이 끝난 뒤 `deleteExpenseRows` 한 번. 연동 기사 경로는 `syncExpenseItemsForVehicle` 그대로(한 칸 안에서 2번 목표 충족). `:46` 주석을 `// 모든 칸 넣기·고치기 먼저, 지우기는 맨 뒤 — 중간 실패 시 유실 대신 중복(불러올 때 id로 정리).`로 바꿈.
- `react-app/src/data/sync/syncExpenseItems.test.js` — 새 테스트: 같은 칸에서 주유 → 정비로 바뀐 항목은 `maint.insert`가 `fuel.delete`보다 먼저, 정비 넣기가 실패하면 `fuel.delete` 0회.
- 새 `react-app/src/data/sync/expensesSave.order.test.js` — 차주 저장에서 서브 → 메인으로 옮긴 항목: 메인 넣기가 서브 지우기보다 먼저, 메인 넣기가 실패하면 서브 지우기 0회·Store 반영 없음.
- 공용 스타일·공용 값 변경 없음.

### 안 건드릴 것
- 서버 함수로 묶기(DB 변경) — 지금 화면에서 일어나지 않는 경우라 순서 바꾸기로 충분, 묶음 저장이 필요해지면 그때 따로.
- 고치기 창에 차량·종류 바꾸기를 넣는 것 — 범위 밖.
- 불러오기의 중복 정리(`hydrateOwnerDriverExpenses.ts`, `domain/expenses.ts` `dedupeExpensesById`) — 그대로(위 "현재 상태" 확인대로 다음 저장 때 정리됨).
- 하루 저장(114·116)·연동 기사 정산 등 다른 저장 — 그대로.
- 화면·알림 문구 — 그대로.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(§5 세션).
- 새 테스트가 고치기 전 코드에서 실패하는지 먼저 확인(옛 코드는 지우기가 넣기보다 먼저라 실패해야 함), 고친 뒤 통과. 기존 `syncExpenseItems.test.js`·`expensesSave.draft.test.js`·`fetchAllRows.saves.test.js` 그대로 통과. 로컬 `npm test` 전체 통과.
- 테스트·타입·빌드·코드 검사는 CI가 봄. 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `syncExpenseItems.ts` 130 → 약 142줄, `expensesSave.ts` 85 → 약 88줄, `syncExpenseItems.test.js` 126 → 약 145줄, 새 `expensesSave.order.test.js` 약 70줄. 200줄 넘는 파일 없음.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음 — 같은 쓰기의 순서만 바꿈). 실패하면 수정 착수지시서를 다시 써서 승인받음.

### 사용자 확인 방법
1. 로그인 테스터 계정 배포본에서 정비/주유/기타 화면 → 정비 하나 넣기 → 저장 → 새로고침해도 보임.
2. 그 항목 금액 고치기 → 저장 → 새로고침해도 바뀐 금액.
3. 그 항목 지우기 → 새로고침해도 없음.
4. 끊김(옮기다 실패)은 화면에서 만들 수 없어 테스트로만 확인.

### 누가 하나
- 데스크톱 AI가 앱 코드·코드 커밋까지, push는 보리 `[확인: 2026-10-27 보리 — "118 착수지시서 확정, 작업 진행해", "너가해"]`.

### 진행 기록
- 코드: react-app `38c1726`(파일 4개, 지시서 "건드릴 파일" 그대로, 주석은 지시서 최종 문구 그대로). 줄 수 `syncExpenseItems.ts` 145, `expensesSave.ts` 88, `syncExpenseItems.test.js` 138, 새 `expensesSave.order.test.js` 80.
- 고치기 전 실패 확인: 두 소스 파일만 마지막 커밋으로 되돌려 새 테스트 두 파일 실행 → 12개 중 4개 실패(새 4개 전부), 복구 뒤 통과.
  ```
  ✖ 서브 칸 항목을 메인으로 옮기면 메인에 먼저 넣고, 서브 칸 줄은 그다음 지운다
      actual: [ 'fuel.delete:srv-sub-back', 'fuel.insert:501:10' ]
      expected: [ 'fuel.insert:501:10', 'fuel.delete:srv-sub-back' ]
  ✖ 메인 넣기가 실패하면 서브 칸 줄을 지우지 않고, 화면 목록도 그대로   (actual: 1, expected: 0)
  ✖ 정비 → 기타로 바뀐 항목은 기타에 먼저 넣고, 정비 줄은 그다음 지운다
      actual: [ 'maint.delete:srv-drv', 'misc.insert' ]
      expected: [ 'misc.insert', 'maint.delete:srv-drv' ]
  ✖ 기타 넣기가 실패하면 정비 줄을 지우지 않는다   (actual: 1, expected: 0)
  ℹ tests 12  ℹ pass 8  ℹ fail 4  (exit=1)
  ```
- 로컬: `test:unit` 917 통과·`test:app` 899 통과(실패 0), `typecheck`·`lint` 통과. `typecheck:strict-inventory`는 원래 오류 782개(이번 두 파일 0개, CI 대상 아님).
- push(보리) 뒤 CI verify·deploy 초록(run 40494317454).
- 배포본(로그인 테스터 탭, getdrivelog.com `/app/expenses`): 10-27 정비 "118확인" 1,180원 넣기 → 새로고침 뒤 보임(합계 231,180원) → 2,180원으로 고치기 → 새로고침 뒤 2,180원 → 지우기 → 새로고침 뒤 없음(합계 230,000원). 서버 읽기: 그 항목 0줄, 10월 정비 합계 230,000원. 10-27 메인 차량에 빈 하루 기록 줄(fixed 0)이 남음 — 지출 넣을 때 원래 만드는 줄이고 지우기는 줄을 남기는 기존 동작(이번 변경과 무관).
- §5 점검: ① 범위 — 바뀐 파일 4개가 "건드릴 파일"과 같음 ② 새 저장 키·큐·대비 장치 없음(지우기 시점만 이동) ③ 새 `unknown`은 새 테스트의 `filterValue` 도우미 1곳(`cloudMemorySave.test.js`의 같은 도우미를 그대로 씀), 소스 파일에는 없음 ④ 200줄 넘는 파일 없음 ⑤ 기존 테스트 지우거나 약화한 것 없음(테스트 diff에 지운 줄 0) ⑥ `syncExpenseItemsForVehicle` 이름·인자 그대로, 새로 내보낸 두 함수는 `expensesSave.ts`에서만 씀 ⑦ 목표 1~4 충족(1·2 새 테스트 4개, 3 기존 테스트 통과, 4 실패 테스트의 Store 그대로 확인).

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
