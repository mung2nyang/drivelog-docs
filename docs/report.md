# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 97·99 200줄 넘는 화면 4개 나누기 — 분리설계 4개 + 이번 슬라이스 97(차량 유지비) `[x]`

> **결정 `[확인: 2026-10-21 보리 — "권하는대로 진행하는걸로"]`**: 확인할 점 1~4 모두 권함대로.
> 1 한 슬라이스에 한 화면, 순서 97 → 99-C → 99-B → 99-A / 2 99-B PDF도 "파일 만들기 → 링크로 내려받기"로(배포 뒤 휴대폰 PDF 받기 보리 확인) / 3 로드맵 101번 등재(99 다음·100 앞, 문서 쪽에서 등재함) / 4 달 이동 줄은 똑같은 복사본 한 벌만 `shared/ui/MonthNavigator.jsx`로 합침.
> 다음: 터미널 AI가 교차검증 뒤 97 착수.

> **97 진행(2026-10-21) — 코드 커밋 `2f3bd04`** `[x]`: 보리 "착수지시서확정,작업진행해줘".
> - 교차검증(AGENTS §1): `MaintFuelPage` 248줄·화면 테스트 0개·`MonthNavigator` 쓰는 곳 서류 3곳·`.maint-fuel-nav` 디자인 `controls-common.css`에 있음 — 지시서와 같음. 다른 점 1: 옛 차량 유지비 화살표는 `setViewDate((d) => shiftMonth(d, -1))`(바로 전 값 기준), `MonthNavigator`는 `onChange(shiftMonth(viewDate, -1))`(그린 값 기준) — 한 번 누를 때 결과 같음(서류 3곳이 이미 이 방식). `docs/architecture-target.md`·`sot.md`·`STATUS.md`에 `MonthNavigator` 언급 0이라 문서 경로 고칠 곳 없음.
> - 테스트 먼저(목표 3): 새 `features/expenses/ui/MaintFuelPage.test.js`(191줄, 7개 — 저장 함수만 가짜로, 성공이면 저장소에 넣고 실패면 오류). 옛 코드에서 처음 6 통과·1 실패 — ①의 날짜 순서를 제가 "최근 날짜 먼저"로 잘못 짐작함(실제 오래된 날짜 먼저, 지시서는 순서를 정하지 않음). 화면이 아니라 테스트 기대값 잘못이라 기대값만 실제 순서로 고침 → **옛 코드에서 7개 통과** → 나눈 뒤 같은 7개 통과(단언 그대로).
> - 한 일: `git mv features/documents/ui/MonthNavigator.jsx shared/ui/`(머리 주석 "여러 화면이 같이 쓰는 달 이동…"·안 경로 2줄만), 서류 3곳 경로 1줄씩. 새 `MaintFuelDayList.jsx`(88줄 — 종류별 아이콘·날짜별 목록·빈 목록 문구, 받는 값 `groups`·`kind`·`kindLabel`·`onEdit`·`onDelete`). `MaintFuelPage.jsx` 248 → **155줄**, 머리 주석의 "§6 예외(≤250)" 지움.
> - 대조: `git diff -M` — `MonthNavigator` 옮김으로 잡힘(6줄 바뀜 = 주석 1·경로 2). 목록 JSX·아이콘은 옛 줄과 같고, 다른 곳은 `openEdit`→`onEdit`·`remove`→`onDelete` 두 줄뿐.
> - 결과: `npm test` unit 866 + app 649(7개 늘어남) = 1515 전부 통과, 타입 검사·빌드·`npm run depcheck`(위반 0, 허용 예외 그대로) 통과, lint 경고 50 그대로.
> - 개발 서버(비회원): 차량 유지비에서 정비 1건 추가 → "내역을 등록했습니다."·날짜별 목록·합계 30,000원, 달 이동 줄(화살표 2·"2026년 10월"·높이 44px) 이전 달 → 9월 빈 목록, 주유 탭 → 빈 목록·0원. 서류 발급 달 이동도 그대로(이전 달 → 9월). 서브 차량 주소 `/app/logs/22나2222/expenses` 제목 "2222 차량 유지비"(제목 만드는 코드는 안 건드림). 콘솔 오류 0건.
> - 코드 커밋 react-app `2f3bd04`(push 안 함).
> - push 뒤 CI verify·deploy 초록(run 39535430155) — 테스트·타입 검사·빌드·의존 방향 검사 모두 성공.
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서의 7개 파일(`MaintFuelPage`·새 `MaintFuelDayList`·새 테스트·`MonthNavigator` 옮김·서류 3곳 경로 1줄씩) ②새 층·저장 키 0, 새 파일은 지시서대로 ③타입 꼼수 0 ④`MaintFuelPage` 155·`MaintFuelDayList` 88·`MonthNavigator` 38, 서류 `ReportPage`는 경로 1줄만(§6 확정 예외) ⑤새 테스트 7개 단언 26, 저장 함수만 가짜(성공이면 진짜 저장소에 넣음), 화면 글자·저장소 값·알림 문구를 직접 확인 — 거짓 통과 없음 ⑥공용 영향: `MonthNavigator`는 내용 그대로(`git diff -M` 84% 같음 — 주석 1·경로 2줄), `.maint-fuel-nav` 디자인 그대로 ⑦목표 1~4 대조. 목록 파일은 옛 줄과 줄 단위로 대조해 다른 줄이 `onEdit`·`onDelete` 2줄뿐.
> - 배포본 로그인 확인(데스크톱 AI, 새 빌드에 `MonthNavigator` 조각 따로 내려옴): 차량 유지비 10월 정비 50,000원 목록 → 이전 달 9월 빈 목록·0원 → 다음 달 10월로, 주유 탭 100,000원(60.5L·보조금 표시), 기타 탭 빈 목록·0원. 서브 차량 `/app/logs/22가2222/expenses` 제목 "기사연동 차량 유지비". 서류 발급 달 이동 이전 달 → 9월, 운송비 내역서 탭도 9월 그대로. 추가·삭제는 안 함(데이터 변경 0). 콘솔 오류는 깊은 주소 첫 열기 404(GitHub Pages 방식, 전부터 같음)뿐.
> - 보리 최종 승인(2026-10-21). 다음 99-C 고객센터.

보리 지시 "97·99번 착수지시서 지금 작성해줘"(2026-10-21). 순서 줄 `[확인: 2026-10-21 보리 — "권하는 순서대로"]`의 96 다음. AGENTS §6("기존 200줄 초과 파일은 분리설계안(책임 경계·모듈 구조·의존성)을 먼저 보고·승인")에 따라 **4개 화면의 분리설계를 이 한 번에 보고**하고(§3 "1회 보고로 끝낸다"), 만드는 일은 **한 슬라이스에 한 화면씩** 합니다(확인할 점 1). 이번 슬라이스는 **97 차량 유지비 화면**이고, 99의 세 화면은 이 설계대로 슬라이스마다 짧은 지시서를 따로 씁니다.

### 쉬운 요약
- 200줄이 넘는 화면 파일 4개를 읽기 쉬운 크기로 나눕니다. 화면 모양과 동작은 그대로입니다.
- 이번엔 차량 유지비 화면만 합니다. 날짜별 내역 목록을 따로 떼어 내고, 서류 화면과 똑같이 생긴 "달 이동 줄"을 같이 쓰게 합니다(이건 86번 때 미룬 일이라 확인할 점 4). 248줄이 약 150줄로 줄어듭니다.
- 이 화면은 지금 화면 테스트가 하나도 없습니다. 그래서 나누기 전에 동작 테스트를 먼저 붙여, 옛 코드와 새 코드 모두에서 통과하는지 봅니다.

### 현재 상태 (코드 확인 — `wc -l`, 운영 파일)
| 화면 | 줄 | 안에 섞인 것 |
|---|---|---|
| `features/expenses/ui/MaintFuelPage.jsx` | 248 | 달 이동 줄(24줄, `documents/ui/MonthNavigator.jsx`와 **글자까지 같은 복사본**, 같은 `maint-fuel-nav` 디자인), 종류별 아이콘(25줄), 날짜별 내역 목록(42줄), 저장·수정·삭제 흐름, 화면 틀 |
| `features/day-log/ui/DayLogPage.jsx` | 234 | 콜 상세 저장·수금 바꾸기·고정 장소·고정 노선 횟수 처리 흐름(약 45줄), 긴 설명 주석 4곳, 화면 조합 |
| `features/documents/ui/ReportPage.jsx` | 236 | PDF·이미지 내려받기 함수 2개(74줄) — `documents/api/reportExport.js`(공유 창이 쓰는 것)와 **같은 html2pdf 설정을 한 벌 더 가짐**, 화면 조합 |
| `features/support/ui/CustomerCenterPage.jsx` | 229 | 안에 부품 3개(`GuestLoginPrompt`·`InquiryForm`·`MyInquiriesList`, 합 약 110줄) + 탭 화면 |
- 테스트: `ReportPage.test.js` 3개·`CustomerCenterPage.test.js` 7개는 있음. **`MaintFuelPage`는 화면 테스트 0개**. 일지 화면의 콜 상세 저장·수금 바꾸기 알림 문구를 확인하는 테스트도 0개(grep `세부 입력을 저장했습니다`·`수금 처리했습니다`).
- `MonthNavigator`는 지금 서류 기능의 3곳(`ReportPage`·`DailyInspectionMonthSheet`·`TaxInvoiceTab`)이 씀. 디자인 `.maint-fuel-nav`는 이미 `shared/styles/controls-common.css`에 있음.

### 분리설계 (AGENTS §6 — 책임 경계·모듈 구조·의존성)
**97 차량 유지비 (이번 슬라이스)** — 248 → 약 150
- (확인할 점 4에 따라) `documents/ui/MonthNavigator.jsx` → **`shared/ui/MonthNavigator.jsx`로 옮김**(내용 그대로, 머리 주석만 "여러 화면이 같이 쓰는 달 이동"으로). 서류 3곳은 가져오는 경로만 바뀜. `MaintFuelPage`는 복사본 24줄 대신 이걸 씀(`shared` → `domain`만 부르므로 층 규칙 그대로). 안 하면 이 줄은 빠지고 화면은 약 178줄.
- 새 `features/expenses/ui/MaintFuelDayList.jsx`(약 85줄): 종류별 아이콘 + 날짜별 묶음 목록 + 빈 목록 문구. 받는 값은 `groups`·`kind`·`kindLabel`·`onEdit(item)`·`onDelete(id)`만. 그리기만 하고 저장은 모름.
- `MaintFuelPage.jsx`에 남는 것: 주소의 차량 읽기, 보는 달·종류 상태, 목록·합계 계산, 저장·수정·삭제 흐름(`persist`·`openAdd`·`openEdit`·`save`·`remove`), 위 카드·추가 버튼·입력 창. 머리 주석의 "§6 예외(≤250)"는 지움.
- 흐름을 훅으로 빼지 않는 이유: 60번 차량 화면은 여러 단계를 잇는 흐름이었지만 여기는 "배열 하나 저장" 한 단계뿐이고, 옮긴 뒤에도 150줄이라 기준 안. 일지 쪽 `useExpenseForm`과 겹치는 문제는 확인할 점 3으로 따로.

**99-C 고객센터** — 229 → 약 115
- `InquiryForm` → `features/support/ui/InquiryForm.jsx`, `MyInquiriesList` → `features/support/ui/MyInquiriesList.jsx`로 내용 그대로 옮김(`INQUIRY_TYPES`·`FETCH_FAIL_TOAST`도 쓰는 파일로). 작은 `GuestLoginPrompt`와 FAQ 목록은 화면에 남김. 머리 주석의 "응집 유지(§6)" 문구는 고침. 기존 테스트 7개가 화면째 그려서 두 부품을 다 지나가므로 테스트 추가 없음.

**99-B 서류(운송비 내역서)** — 236 → 약 150
- 내려받기 함수 2개를 새 `features/documents/hooks/useReportDownload.js`(약 50줄)로: 저장 중 상태 2개 + `download('pdf'|'image')` 하나. 안에서는 **이미 있는 `reportExport.js`의 `createReportPdfFile`·`createReportImageFile`을 그대로 쓰고**, 받은 파일을 링크로 내려받게 함 → 같은 html2pdf 설정이 두 벌인 문제가 사라짐. 링크 내려받기 함수(약 10줄)는 `reportExport.js`에 더함.
- 화면에는 탭·보기 상태, 세부 거래처 고르기, 확인 창·공유 창이 남음. 옮기기 전 내려받기 테스트(내보내기 모듈 흉내, 파일 이름·성공/실패 알림·`pdf-export-mode` 붙였다 떼기)를 먼저 붙임. PDF 내려받는 방법이 바뀌는 점은 확인할 점 2.

**99-A 운행일지** — 234 → 약 165
- 콜 상세·고정 노선 처리 흐름 5개(`handleRouteRun`·`handleSaveCall`·`handleTogglePayment`·`handleTogglePinnedLocation`·콜 삭제)를 새 `features/day-log/hooks/useCallDetailActions.js`(약 70줄)로. 받는 값은 `draft`·`dispatch`·`dateKey`·`clients`·`settings`, 알림·`ownerKey`는 훅 안에서 `useToast`·`useSession`으로.
- 긴 설명 주석 4곳(82~86·94~96·106~107·123~124줄)은 결론 1줄씩으로(조사 경위는 이미 로드맵·커밋에 있음).
- `onWorkChanged`(안 쓰이는 값)는 그대로 둠 — 91번 몫. 옮기기 전 흐름 테스트(저장·수정 알림, 수금 처리/취소, 고정 장소 저장 실패, 고정 노선 횟수)를 먼저 붙임.

### 목표 상태 (97 — 이번 슬라이스)
1. `MaintFuelPage.jsx` 200줄 이하(약 150). 새 `MaintFuelDayList.jsx` 200줄 이하.
2. (확인할 점 4 권함대로면) `MonthNavigator`가 `shared/ui`에 하나만 있고, 차량 유지비·서류 3곳이 그것을 씀. `features/` 안에 달 이동 복사본이 하나 줄어듦.
3. 새 `features/expenses/ui/MaintFuelPage.test.js`를 **옮기기 전 옛 코드에 먼저** 붙여 통과 확인 → 옮긴 뒤에도 그대로 통과. 다룰 것: ① 고른 달·종류의 내역만 날짜별로 나오고 합계가 맞음 ② 이전/다음 달 버튼으로 목록이 바뀜 ③ 종류 탭(정비·주유·기타)을 바꾸면 목록·합계가 바뀜 ④ 추가 → 저장하면 저장소에 들어가고 "내역을 등록했습니다." ⑤ 수정 → "내역을 수정했습니다." ⑥ 삭제 → 빠지고 "내역을 삭제했습니다." ⑦ 저장 실패면 실패 알림이 뜨고 입력 창이 닫히지 않음.
4. 화면 모양·디자인 파일 이름·동작 변화 0. `npm run depcheck` 통과(허용 예외 목록 변경 없음).

### 건드릴 파일 (97)
- `features/expenses/ui/MaintFuelPage.jsx`(줄어듦), 새 `features/expenses/ui/MaintFuelDayList.jsx`, 새 `features/expenses/ui/MaintFuelPage.test.js`
- (확인할 점 4 권함대로면) `features/documents/ui/MonthNavigator.jsx` → `shared/ui/MonthNavigator.jsx`(옮김)
- 그때 가져오는 경로만: `features/documents/ui/ReportPage.jsx`·`DailyInspectionMonthSheet.jsx`·`TaxInvoiceTab.jsx`(`ReportPage`는 200줄 넘지만 경로 줄만이라 §6 확정 예외)
- 문서: `docs/architecture-target.md`에 `MonthNavigator` 자리 언급이 있으면 경로만(문서 커밋 때)

### 안 건드릴 것
- 99의 세 화면(경로 줄 빼고), 일지 `useExpenseForm`, `domain/expenses.js`(확인할 점 3), 다른 달 이동 부품(`RevenueNav`·`SettlementSummaryCard`·`CalendarHeader` — 연 단위 보기·다른 받는 값이라 모양이 다름), 디자인 파일, `.dependency-cruiser.cjs`.

### 검증
- 새 테스트 7개를 옛 코드에서 먼저 실행해 통과 기록 → 나눈 뒤 같은 테스트 통과(단언 수 그대로). `npm test`·타입 검사·빌드·`npm run depcheck` 통과, lint 경고 수 그대로.
- `git show -M`로 `MonthNavigator`가 옮김(내용 같음)으로 잡히는지, `MaintFuelDayList`의 목록 JSX가 원래 줄과 같은지 대조.
- 배포본 로그인 확인(데스크톱 AI): 메뉴 → 차량 유지비에서 달 이동·종류 탭·목록·합계가 전과 같은지, 서류 발급의 달 이동도 그대로인지. 서브 차량 주소(`/app/logs/<차량>/expenses`) 제목도 확인. 내역 추가·삭제는 하지 않거나, 하면 되돌림.

### 파일 길이
- `MaintFuelPage.jsx` 약 150, `MaintFuelDayList.jsx` 약 85, `shared/ui/MonthNavigator.jsx` 38(그대로). 테스트 파일은 §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(코드 자리만 옮김).
- 새 테스트가 옛 코드에서 실패하면 화면을 고치지 말고 멈추고, 어떤 동작이 기대와 다른지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법
- 화면은 바뀌지 않음. push 뒤 AI가 CI와 배포본(차량 유지비·서류 발급의 달 이동)을 확인해 보고.

### 확인할 점
1. **나누는 순서와 크기** — 한 슬라이스에 한 화면씩(§3 "1~3개 파일") 하고, 순서는 **97 차량 유지비 → 99-C 고객센터 → 99-B 서류 → 99-A 운행일지**를 권합니다.
   - 97: 테스트가 0개라 가장 위험하고, 서류와 겹치는 달 이동 복사본이 있어 먼저 정리할 값이 큼.
   - 99-C: 부품을 그대로 옮기기만 해서 가장 쉬움(기존 테스트 7개로 충분).
   - 99-B: 같은 html2pdf 설정 두 벌을 하나로 합침(확인할 점 2 결정 필요).
   - 99-A: 가장 많이 쓰는 화면이고 91번(일지 저장 단순화)과 닿아 있어 마지막. 91 바로 앞이라 이어서 보기도 좋음.
2. **99-B PDF 내려받는 방법** — 지금 화면은 html2pdf의 자체 저장(`.save()`), 공유 창은 같은 설정으로 파일을 만든 뒤 넘깁니다. **권함: 화면도 공유 창과 같은 "파일 만들기 → 링크로 내려받기"로 맞추기** — 파일 이름·용지·여백·화질은 같고, 설정이 한 곳만 남습니다. 99-B 배포 뒤 휴대폰에서 PDF가 받아지는지 보리 확인이 필요합니다. 다른 안: `.save()`는 그대로 두고 이미지 쪽만 합침(겹침이 반만 사라짐).
3. **찾은 것 — 로드맵에 올릴까요?** 지출 입력 흐름(내역 → 입력값 바꾸기, 저장 실패 알림, 삭제)이 차량 유지비 화면과 일지 `useExpenseForm.js`에 **두 벌**입니다(날짜 고정 여부·삭제 알림만 다름). 둘을 합칠 자리인 `domain/expenses.js`가 이미 **203줄**(§6 예외 표시 없음)이라 이번 97에 넣으면 범위가 커집니다. **권함: "지출 입력 흐름 한 벌로 + `domain/expenses.js` 나누기"를 새 로드맵 101번으로 올리고 99 다음(100 앞)에 하기.** 다른 안: 올리지 않고 두 벌 유지.
4. **달 이동 줄을 공용 부품 하나로** — 86번 때 "공용 부품 합치기는 안 함(보리 결정, 58번에서 볼 수 있음)"으로 미뤘고, 58번은 다른 세 쌍만 합치고 끝났습니다. 그래서 이번에 합쳐도 되는지 여쭙니다. 차량 유지비의 달 이동 줄은 서류의 `MonthNavigator`와 **글자·디자인 이름까지 똑같은 복사본**이라 바꿔 끼워도 모양·동작 변화가 없습니다. **권함: 이 한 벌만 합치기**(위 설계). 나머지 3벌(`RevenueNav` 연 단위 보기, `SettlementSummaryCard`·`CalendarHeader` 다른 받는 값)은 모양이 달라 그대로 둡니다. 다른 안: 이번에도 안 합침 — 화면은 약 178줄로 기준 안.
