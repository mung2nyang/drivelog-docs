# docs/report.md — 현재 슬라이스 착수지시서

24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 105-A 코드 검사기 경고 — 운영 코드 20개 정리 `[x]`

로드맵 105번(아키텍처 점검 2026-10-23) 첫 조각. 보리 2026-10-23 "권함으로 착수지시서 작성해" — 아래 결정 3가지는 105-B에서 쓴다. **이번엔 105-A만.**
- 결정(105-B용): ① 경고도 CI 실패로 셈(`oxlint --deny-warnings`) ② "개발 중 자동 새로고침" 경고(`only-export-components`)는 규칙을 끔(새 파일 안 만듦) ③ 테스트 파일에서만 전역값 바꾸기 규칙 2개(`immutability`·`globals`)를 끄고, 안 쓰는 변수 8개는 지움.

### 쉬운 요약
- 코드 검사기가 운영 코드에서 찾은 경고 20개를 고친다. 대부분은 "화면을 그린 직후 한 번 더 그림"이나 "계산을 매번 다시 함" 같은 낭비이고, 보이는 동작은 그대로다.
- 끝나면 경고가 49개에서 29개로 준다. 남은 29개(테스트 20·자동 새로고침 8·오류 화면 1)와 CI 검사 단계는 105-B에서 다룬다.

### 현재 상태 (코드 확인)
- `npx oxlint` 경고 49개(2026-10-23 다시 뽑음, 로드맵 기록과 같음). 이번 대상 20개는 아래 "건드릴 파일"의 줄들.
- 서버에서 불러오는 화면 4곳이 불러오기 시작할 때 "불러오는 중" 상태를 effect 안에서 바로 바꿈 → 화면을 두 번 그림(`set-state-in-effect`).
- `|| {}`, `|| []`처럼 값이 없을 때 매번 새 빈 묶음을 만들어 계산 재사용(`useMemo`)이 매번 다시 돎: `ReportPage.jsx:50`(3개), `LinkedDriverManagementPage.jsx:76·79`(3개), `DayLogPage.jsx:69`(1개).
- `useReceivablesData.js:21~27`: 설정 계산이 읽지 않는 일지(`workDataByLogId`)를 다시 계산 조건에 넣었고, 실제로 읽는 거래처(`buildFinanceSettings`가 `readOwnerClients` 호출, `data/sync/ownerFinance.ts:80`)는 조건에 없음.
- 그리는 중에 ref 값을 씀: `ModalShell.jsx:27`, `useConfirm.jsx:12`.
- 진짜 문제가 아닌 경고 3개: `useAppSession.js:103`(저장된 기록 채우기는 effect에서 해야 함), `usePersonalInfoDraft.js:50`(저장 함수가 남은 값을 저장하려고 자기 자신을 다시 부름), `MessageTemplateSheet.jsx:37`(문자 앱 열기 주소 이동).

### 목표 상태 (기대 동작)
1. `npx oxlint` 결과가 29개이고, 아래 "건드릴 파일" 줄의 경고는 0개다(남은 29개는 테스트 20·`only-export-components` 8·`PageLoadErrorBoundary.jsx:30` 1).
2. 서버에서 불러오는 4곳(`MyInquiriesList`·`DailyInspectionNotice`·`DailyInspectionMonthSheet`·`useTodayInspectionMissing`): 결과에 "어느 요청의 결과인지" 열쇠(사용자·차량·날짜·다시 시도 횟수)를 같이 담고, 열쇠가 지금 것과 다르면 "불러오는 중"으로 본다. effect 안에서는 결과가 온 뒤에만 상태를 바꾼다. 보이는 순서(불러오는 중 → 결과/실패, [다시 시도] 누르면 다시 불러오는 중)는 지금과 같다.
3. `OwnerRevenueView`: 기사 차량이 없어졌을 때 "기사" 탭·고른 기사를 effect로 되돌리지 않고, 화면에 쓸 값을 그 자리에서 고른다(기사 차량이 없으면 "차주", 고른 기사가 없어졌으면 "전체 기사 합산"). 보이는 결과는 같다. 다른 점 하나: 없어졌던 기사 차량이 다시 생기면 전에 고른 탭·기사가 되살아난다.
4. 빈 묶음은 파일 맨 위 고정 상수로 바꿔 매번 같은 것을 쓴다(`DayLogPage`의 기존 `EMPTY_WORK`와 같은 방식). 계산 결과는 같다.
5. `useReceivablesData`: 다시 계산 조건에서 일지를 빼고 거래처 목록(`useOwnerClients`)을 넣는다 — `LinkedDriverManagementPage.jsx:64~71`과 같은 방식. 거래처가 바뀌면 미수금 설정이 바로 다시 계산된다.
6. `ModalShell`: 최신 닫기 함수 담기를 그리는 중이 아니라 그린 뒤(`useEffect`, 조건 없음)로 옮기고(`usePersonalInfoDraft.js:26`과 같은 방식), 열 때 초점 effect 조건에 `initialFocusRef`를 넣는다(넘기는 곳은 `ConfirmModal.jsx:21` 하나, `useRef`라 바뀌지 않음 — 착수 때 확인). Esc·바깥 누름·초점 동작은 같다.
7. `useConfirm`: 그리는 중에 ref를 맞추지 않고, 확인 창을 열 때 ref에 담고 닫을 때 비운다. 동작은 같다.
8. `MessageTemplateSheet`: `window.location.href = …`를 `window.location.assign(…)`으로. 동작은 같다.
9. `useAppSession.js:103`·`usePersonalInfoDraft.js:50`은 코드를 그대로 두고 그 줄 바로 위에 검사 끄기 주석 1줄만 단다(최종 문구 아래).

코드에 넣을 주석 최종 문구:
- `useAppSession.js` 103줄 위: `// oxlint-disable-next-line react/set-state-in-effect -- 저장된 기록 채우기는 화면 밖 작업이라 effect에서 하고 결과만 담는다.`
- `usePersonalInfoDraft.js` 50줄 위: `// oxlint-disable-next-line react/immutability -- 저장 중 바뀐 값이 남으면 한 번 더 저장한다(자기 호출).`
- (규칙 이름 표기는 착수 때 `oxlint`로 실제 꺼지는지 확인하고, 다르면 이름만 고친다.)

### 건드릴 파일
- `src/features/support/ui/MyInquiriesList.jsx` — 목표 2
- `src/features/day-log/ui/DailyInspectionNotice.jsx` — 목표 2
- `src/features/documents/ui/DailyInspectionMonthSheet.jsx` — 목표 2
- `src/features/home/hooks/useTodayInspectionMissing.js` — 목표 2
- `src/features/revenue/ui/OwnerRevenueView.jsx` — 목표 3
- `src/features/documents/ui/ReportPage.jsx` — 목표 4
- `src/features/drivers/ui/LinkedDriverManagementPage.jsx` — 목표 4
- `src/features/day-log/ui/DayLogPage.jsx` — 목표 4(`pinnedLocations`)
- `src/features/receivables/hooks/useReceivablesData.js` — 목표 5
- `src/shared/ui/ModalShell.jsx` — 목표 6. 공용 창 껍데기(사용처 15개 파일) — 바뀌는 건 내부 ref 담는 시점뿐, 받는 값(props)·CSS는 그대로
- `src/features/receivables/hooks/useConfirm.jsx` — 목표 7(사용처 `ReceivablesListPage`·`ReceivablesDetailPage`)
- `src/features/day-log/ui/MessageTemplateSheet.jsx` — 목표 8
- `src/app/useAppSession.js`, `src/features/account/hooks/usePersonalInfoDraft.js` — 목표 9, 주석 1줄씩

### 안 건드릴 것
- 테스트 파일 경고 20개, `only-export-components` 8개, `PageLoadErrorBoundary.jsx`, `.oxlintrc.json`, `.github/workflows/ci.yml`, `package.json` — 105-B.
- 저장·동기화 코드(`src/data/**`) — 이번 파일 중 `localStorage`를 직접 쓰는 곳 없음(14개 파일 grep 0건). `useAppSession.js`는 주석 1줄만이라 저장 경로 동작 변화 없음.
- 새 파일·새 저장 키 없음.

### 검증
- 로컬 `npm test`, `npm run typecheck`, `npx oxlint`(목표 1 숫자 대조).
- 기존 테스트가 지켜 주는 것: `DailyInspectionNotice.test.js`(불러오는 중·실패·다시 시도), `OwnerRevenueView.vehicleScope.test.js`(기사 탭·기사 고르기), `ModalShell.test.js`(Esc·겹친 창·초점). 기존 테스트는 지우거나 약하게 바꾸지 않는다.
- 새 테스트는 만들지 않는다 — 목표 2~4·6~9는 보이는 동작이 같아 기존 테스트로 증명. 목표 5(거래처 바뀌면 바로 다시 계산)는 브라우저 확인.

### 파일 길이
- `LinkedDriverManagementPage.jsx` 198 → 200 이하(빈 묶음 상수 2줄 — 넘으면 상수를 한 줄로). 감시 목록 파일.
- `DayLogPage.jsx` 179 → 약 180, `ReportPage.jsx` 164 → 약 165, `useAppSession.js` 145 → 146, `OwnerRevenueView.jsx` 129 → 약 125, `DailyInspectionMonthSheet.jsx` 125 → 약 127, `DailyInspectionNotice.jsx` 106 → 약 108, `usePersonalInfoDraft.js` 72 → 73, `MyInquiriesList.jsx` 67 → 약 68, `ModalShell.jsx` 63 → 약 64, `MessageTemplateSheet.jsx` 61 → 61, `useConfirm.jsx` 37 → 약 39, `useReceivablesData.js` 33 → 약 34, `useTodayInspectionMissing.js` 27 → 약 29. 모두 200줄 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 테스트나 화면 확인이 실패하면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받는다.

### 진행 기록
- 2026-10-23 터미널 AI 교차검증 — `npx oxlint` 49개 중 운영 코드 29개, 그중 이번 대상 20개가 지시서 "건드릴 파일" 줄과 정확히 같음(나머지 9 = `only-export-components` 8 + `PageLoadErrorBoundary.jsx:30`). 줄 번호만 조금 다름(경고가 뜨는 줄은 빈 묶음을 쓰는 `useMemo` 쪽 — `ReportPage.jsx:55~63`, `LinkedDriverManagementPage.jsx:98·103`, `DayLogPage.jsx:72`). `initialFocusRef`를 넘기는 곳은 `ConfirmModal.jsx:21` 하나(`useRef`)와 `ModalShell.test.js`(`createRef` 한 번) 확인. 검사 끄기 규칙 이름 `react/set-state-in-effect`·`react/immutability`는 그대로 꺼짐 확인.
- 정한 것: ① 불러오기 열쇠 = 내 문의 `userId`, 일상점검 안내 줄 `사용자|차량|날짜|다시 시도`, 일상점검표 `사용자|차량|연-달|다시 시도`, 홈 할 일 `사용자|차량|날짜`. 다른 점 하나: 휴무를 켰다 끄면 전에는 "불러오는 중"부터 다시 보였는데, 이제는 같은 열쇠의 지난 결과가 바로 보이고 뒤에서 다시 읽어 바꿈(같은 차량·날짜라 내용은 같음). ② "불러오는 중" 대신 값은 파일 위 타입 붙인 상수(`LOADING`)로(타입 검사가 빈 묶음 모양을 잡음). ③ `LinkedDriverManagementPage.jsx`는 상수 3줄을 넣고 기존 형 설명 줄을 옮겨 정확히 200줄.
- 결과: 14개 파일, 모두 200 이하(최대 `LinkedDriverManagementPage.jsx` 200·`DayLogPage.jsx` 180). `npx oxlint` 29개(테스트 20·`only-export-components` 8·`PageLoadErrorBoundary.jsx` 1) — 목표 1 그대로. 전체 테스트 884 + 757 통과(기존 테스트 안 바꿈·새 테스트 없음, 지시서대로), 타입 검사·빌드·의존 검사 통과. 저장·동기화 코드 안 바뀜.
- 코드 커밋 react-app `d7a69fa`(2026-10-23, 14개 파일, push 안 함). 남은 것: 보리 push → CI → 브라우저 확인(아래 1~8) → §5 리뷰 → 최종 승인.
- push 뒤 CI verify·deploy 초록(run 39889929378). §5 리뷰(데스크톱 AI): 범위 14개 파일 지시서와 같음, 새 파일·저장 키 없음, 타입 꼼수 0, 최대 200줄, 테스트 안 바뀜, `ModalShell` 받는 값 그대로, 목표 1~9 충족. 지시서에 미리 안 적은 설명 주석 5줄(불러오기 열쇠 4·매출 1) 들어감 — 결론 1줄짜리라 그대로 둠(보리 판단).
- 배포본 확인(데스크톱 AI, 보리 계정, 2026-10-23): 1 내 문의 빈 목록 안내 / 2 일지 "일상점검표 작성이 필요합니다."·홈 "오늘 일상점검 안 함" / 3 일상점검표 10월 서버 기록(9·10·12일) 표시, 9월로 바꾸면 "불러오는 중" → 표 / 4 매출 기사 탭 합산 558,000원 → 22가2222 고르면 568,000원 / 5 내역서 계 3,525,510원 = 홈 정산 합계, 기사 관리 22가2222 운송료 710,000·정산금 142,000 = 매출 기사 탭 / 7 입금완료 확인 창 열림(초점 "취소")·취소·Esc 닫힘, 닫은 뒤 초점 "입금완료"로 돌아옴, 입금 처리 안 함 — 모두 통과. 6(거래처 단가 바꾸기)·8(문자 앱 열기)은 계정 자료를 바꾸거나 다른 앱을 여는 일이라 AI가 안 함 → 보리 확인 완료, 최종 `[x]` 승인(2026-10-23).

### 사용자 확인 방법
1. 고객센터 → 내 문의: 잠깐 회색 막대 뒤 목록(또는 "아직 접수한 문의나 건의가 없습니다").
2. 로그인 계정 일지(오늘): 일상점검 안내 줄이 회색 막대 뒤 뜸. 홈 할 일에 "오늘 일상점검 안 함"이 전과 같이 보이거나 안 보임.
3. 서류 발급 → 일상점검표: 달을 바꾸면 회색 → 표.
4. 매출 → 차주 손익: "기사" 탭과 기사 고르기가 전과 같이 바뀜.
5. 운송비 내역서·기사 관리 화면: 숫자가 전과 같음.
6. 거래처 단가를 바꾼 뒤 미수금 화면: 바뀐 값이 바로 반영됨.
7. 미수금 화면에서 입금 처리 확인 창: 열고 취소·확인, Esc로 닫기가 전과 같음.
8. 일지 콜에서 문자 보내기: 문자 앱이 열림.

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
