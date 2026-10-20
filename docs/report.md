# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 59-E 기능 폴더 2 — 미수금·매출·서류를 `src/features/`로 `[x]`

보리 승인(2026-10-20, 59-D `[x]`). 59번 다섯 번째 조각(로드맵 59 `[확인: 2026-10-19 보리]`). 59-D에서 정한 **자리 정하는 규칙**을 그대로 씀: ① 화면·부품·디자인 → `features/<기능>/ui/`, 훅 → `hooks/`, 그 기능만 쓰는 저장·서버·내보내기 함수 → `api/` ② `lib/` 함수를 두 기능 이상(또는 앱 틀·`data/`)이 쓰면 → `data/sync/` ③ 다른 기능이 쓰는 화면 부품·훅은 주인 기능에 두고 "기능 사이 부르기" 목록만(보리 결정 2026-10-20, 59 끝에서 정함). 옮기기·경로만, `git mv`, 경로 줄만 바뀌는 200줄 넘는 파일은 §6 예외.

> **진행(2026-10-20)**: 보리 "착수지시서 확정,작업진행해". 코드 커밋 react-app `1e062a1`(93개 파일: 옮김 72 + 경로 고침 21).
> - 교차검증(AGENTS §1): 표의 파일·쓰는 곳 모두 코드와 같음(`ShareFileModal`은 `ReportShareModal`·`DailyInspectionShareModal`만, `useReceivablesData`는 홈도, `ownerFinance`·`dailyInspections`는 여러 기능). 고친 것 ① `components/TaxInvoicePage.workInvoiceSoT.test.js`(`ReportPage`를 검사) 목록에 없었음 → 서류 `ui/`로 같이 옮김 ② `lib/receivablesPaths.test.js`는 옮길 곳에 같은 이름 테스트가 이미 있어 `features/receivables/ui/receivablesPaths.lib.test.js`로 이름 바꿔 옮김 ③ 옮길 파일 "약 60"이 아니라 72개.
> - 손으로 고친 줄 3개: `features/documents/ui/ReportDetailView.test.js:128` 읽는 경로(`report/report.css` → 같은 폴더 `report.css`), 주석 속 옛 경로 2줄(`domain/financeOwnerDetail.js:3`, `domain/financeTypes.js:59`). 나머지는 같은 대본. `package.json` 안 바뀜(이미 범위 안).
> - 목표 2: 옛 경로 grep 0건. 다섯 폴더와 `shared/ui/ShareFileModal.jsx` 없어짐.
> - **목표 3 미달**: `shared/`→`components/`·`features/` 부르기가 `ShareFileModal` 1곳뿐이라던 지시서와 달리 1곳이 더 있었음 — `shared/ui/CardActionButtons.jsx:2` → `components/day-log/icons.jsx`(일지 그림 아이콘; 거래처 `ClientFormModal`·`ClientListItem`도 씀). 테스트 `shared/ui/PageHeader.test.js:18-19`도 `MyPage`·`RevenuePage`를 부름(테스트라 제외 가능). `ShareFileModal` 쪽은 없어짐. 아이콘 파일을 `shared/ui`로 옮기는 건 범위 밖이라 안 함 — 보리 결정 필요(이번에 옮길지 / 일지 조각 59-G에서 할지).
> - 빌드 비교: 옮기기 전·후 48개 파일, 해시 지우고 비교 → 이름·내용 모두 같음.
> - `git diff -M`: 72개 모두 이름 바뀜으로 잡힘, 경로 아닌 바뀐 줄은 위 3줄뿐.
> - `npm test` 944 + 559 = 1503 통과(합계 같음 — `lib/` 테스트 일부가 `features/`(app) 범위로), 타입 검사·빌드 통과.
> - 개발 서버(비회원, 운행 1건 30만 원): 미수금 목록 → 상세(입금 예정일·D-41), 매출 월·년 전환(10월 300,000원), 서류 발급 세금계산서·운송비 내역서 탭, 내역서 [공유] → "내역서 보내기" 창(`ShareFileModal`) 열림, 콘솔 오류 없음. 일상점검표 탭은 비회원에겐 원래 안 보임(`DocumentIssuePage.jsx:45` `ownerKey !== 'guest'`) — 배포본 로그인에서 확인.
> - 목표 5 "기능 사이 부르기"(59-D 목록에 더해짐): 새 세 기능끼리는 0건. 기능 밖에서: `components/home/HomePage.jsx:9` → `receivables/hooks/useReceivablesData.js`, (테스트) `components/modalOutsideClick.test.js:21` → `documents/ui/TaxInvoiceDraftModal.jsx`, `shared/ui/PageHeader.test.js:19` → `revenue/ui/RevenuePage.jsx`.
> - 지시서 오류(데스크톱 AI): 목표 3을 `ShareFileModal` 1곳만 보고 적음(`shared/` 전체를 grep하지 않음) — 아이콘 1곳을 놓침. 짝 테스트 `TaxInvoicePage.workInvoiceSoT.test.js`도 목록에서 빠뜨림. 옮길 파일 "약 60"(실제 72).
> - push 뒤 CI verify·deploy 초록(run 39386457900).
> - §5 리뷰(데스크톱 AI): ①범위 = 옮김 72(모두 이름 바뀜) + 경로 21 ②새 파일 0, 새 저장 키 0 ③타입 꼼수 0 ④줄 수 그대로(§6 예외) ⑤테스트 수 1,424 → 1,424, 테스트 바뀐 줄은 경로뿐(`receivablesPaths.lib.test.js`는 같은 이름 충돌로 이름만 바꿈) ⑥공용 값 — 해당 없음 ⑦목표 1·2·4·5 충족, **목표 3 미충족 1곳**: `shared/ui/CardActionButtons.jsx:2` → `components/day-log/icons.jsx`(아이콘을 쓰는 곳: `CardActionButtons`·거래처 `ClientFormModal`·`ClientListItem` + 일지 화면 안). 경로 아닌 바뀐 줄 3(주석 2·읽는 경로 1).
> - 배포본 로그인 확인(2026-10-20, 내장 브라우저): 묶음 파일 이름이 59-D 배포와 같음(빌드 결과 이름·내용 모두 같다는 교차검증과 맞음). 미수금 목록 3묶음 → 테스터거래처 상세(2026.10.10, 200,000원, D-41 — 입금 처리 안 함), 매출 월(당월 순이익 2,284,586원)·년 전환(10월 2,284,586원), 서류 발급 세 탭(세금계산서 작성 전 1건·일상점검표 표·운송비 내역서), 내역서 [공유] → "내역서 보내기" 창 열고 닫음, 홈 "미수금 10건 2,509,258원" 그대로, 콘솔 오류 없음.
> - 목표 3 예외 `[확인: 2026-10-20 보리 — "아이콘 1곳은 59-G에서 shared/ui로 옮겨. 59-E는 그 1곳만 예외로 하고 승인"]`(AGENTS §2 의도적 제외 항목). 로드맵 59-G에 적음.
> - 보리 최종 승인(2026-10-20).

### 쉬운 요약
- 미수금·매출·서류 발급(운송비 내역서·세금계산서·일상점검표) 화면과 그 화면만 쓰는 부품·훅·내보내기 함수를 `features/receivables`·`features/revenue`·`features/documents`로 모읍니다.
- 여러 기능이 같이 쓰는 함수 2개(미수·정산 계산과 저장 `ownerFinance`, 일상점검표 저장·읽기 `dailyInspections`)는 `data/sync`로 내립니다.
- 59-A 때 공용 부품으로 옮겼던 파일 보내기 창(`ShareFileModal`)은 실제로 서류 발급만 써서 서류 폴더로 내립니다 — 공용 폴더가 기능 폴더의 디자인 파일을 부르던 거꾸로 된 연결도 함께 없어집니다.
- 화면·저장·내보내기 동작은 그대로입니다.

### 현재 상태 (코드 확인 — 파일마다 쓰는 곳 grep)
| 옮길 곳 | 파일 | 쓰는 곳 |
|---|---|---|
| `features/receivables/ui` | `components/ReceivablesPage`(화면 주소 묶음)·`receivables/ReceivablesListPage`·`ReceivablesDetailPage`·`ReceivableItemCard`·`receivablesPaths.js`·`receivables.css`·`receivablesGroupHead.css` + 테스트 `ReceivablesPages`·`receivablesPaths`(같은 폴더) + `lib/receivablesPaths.test.js`(이 파일을 검사) | 미수금 화면 안 |
| `features/receivables/hooks` | `useReceivablesActions`(+ 테스트)·`useReceivablesData`※·`useConfirm.jsx` | ※`useReceivablesData`는 홈 `HomePage`도 씀 |
| `features/revenue/ui` | `components/RevenuePage`·`revenue/` 전부(`OwnerRevenueView`·`DriverRevenueView`·`OwnerMonthlyCards`·`RevenueNav`·`RevenuePeriodParts`·`revenueFormat.js`·`revenuePeriod.js`·`driverRevenueScope.js`·디자인 2 + 테스트 5) | 매출 화면 안 |
| `features/documents/ui` | `documents/` 전부(`DocumentIssuePage`·`TaxInvoiceTab`·`DailyInspectionMonthSheet`·`DailyInspectionExportBar`·`DailyInspectionLegalForm`·`DailyInspectionShareModal`·`MonthNavigator`·디자인 2 + 테스트 2), `components/ReportPage`·`ReportDetailView`·`ReportShareModal`·`ReportSummaryContent`·`TaxInvoiceDraftModal`·`TaxInvoiceEntryList`(+ 테스트 5), `components/report/report.css`, `components/tax-invoice/tax-invoice.css`, **`shared/ui/ShareFileModal.jsx`** | `ShareFileModal`은 `ReportShareModal`·`DailyInspectionShareModal`(둘 다 서류)만 씀, 지금 `components/report/report.css`를 부름(`shared/ui/ShareFileModal.jsx:3`) |
| `features/documents/api` | `lib/report`(+ 테스트)·`reportDetail`·`reportSummary`(+ 테스트)·`reportExport`(+ 테스트)·`taxInvoiceActions`(+ 테스트)·`taxInvoiceExcel`·`taxInvoiceExcelBuilder`(+ 테스트) | 서류 화면만 |
| `data/sync` | `lib/ownerFinance`(+ 이 파일을 검사하는 `lib/receivablesCloudCommit.test.js`)·`lib/dailyInspections`(+ 테스트) | `ownerFinance`: 서류·미수금·매출·기사·알림 / `dailyInspections`: 일지·서류·홈·회원 백업 |

- 이번 조각 밖: `lib/invoices.js`(계산서 저장 + 계산 규칙 재수출 껍데기)·`syncTaxInvoicesTable.js`·`lib/receivables.js`·`lib/finance.js`(재수출 껍데기) — 59-H 껍데기 정리 때. `DataDownloadModal`(마이페이지) — 59-F.

### 목표 상태 (기대 동작)
1. 위 표대로 옮김(짝 테스트 포함). `components/receivables/`·`revenue/`·`documents/`·`report/`·`tax-invoice/` 폴더 없어짐, `shared/ui/ShareFileModal.jsx` 없어짐(서류 폴더로).
2. 옛 경로 grep 0건: `components/(receivables|revenue|documents|report|tax-invoice)/`, `components/(ReceivablesPage|RevenuePage|ReportPage|ReportDetailView|ReportShareModal|ReportSummaryContent|TaxInvoiceDraftModal|TaxInvoiceEntryList)`, `shared/ui/ShareFileModal`, `lib/(report|reportDetail|reportSummary|reportExport|taxInvoiceActions|taxInvoiceExcel|taxInvoiceExcelBuilder|ownerFinance|dailyInspections)(\.js)?'`.
3. `shared/`에서 `features/`·`components/`를 부르는 곳 0건(grep — 지금 `ShareFileModal` 1곳).
4. 테스트 수 그대로, 전부 통과(`features/**`·`data/**`는 이미 실행 범위 — `package.json` 안 바뀜, 확인만). 빌드 결과 내용 같음(묶음 이름이 바뀌면 진행 기록에).
5. "기능 사이 부르기" 목록(규칙 ③)을 진행 기록에 grep 결과로 — 59-D 목록에 이어 적음.

### 건드릴 파일
- 옮김(`git mv`) 약 60개, 경로만 고침(부르는 파일 약 30개 + 옮긴 파일 안 상대 경로 + 테스트 `mock.module`·`import()`·경로로 읽는 테스트).
- 동작 코드·문구는 안 바꿈.

### 안 건드릴 것
- 위 "이번 조각 밖" 목록, 기능 사이 부르기 정리(59 끝), 92번(59-I).
- 엑셀·PDF 내보내기 내용, 세금계산서 저장 순서 — 위치만.

### 검증
- `npm test`·타입 검사·빌드 통과, 테스트 수 그대로.
- 목표 2·3 grep 0건, 목표 5 목록을 진행 기록에.
- `git diff -M --stat`: 옮긴 파일이 이름 바뀜으로 잡히는지, 경로 아닌 바뀐 줄 목록을 진행 기록에.
- 빌드 결과 내용 비교(이름 해시 뺌) — 차이를 진행 기록에.
- 개발 서버(비회원): 미수금 목록·상세, 매출(월·년), 서류 발급 세 탭(세금계산서·운송비 내역서·일상점검표), 내역서 [공유] 창 열기 → 콘솔 오류 없음.

### 파일 길이
- 옮기기만 해서 줄 수 그대로. 200줄 넘는 파일(`ReportPage.jsx` 235 등)은 경로 줄만 — §6 예외.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(위치·경로만).
- 테스트가 하나라도 실패하면 멈추고 어느 경로인지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 미수금 목록 → 상세 열기(입금 처리는 안 함).
2. 매출 화면(월·년 전환) 금액 그대로.
3. 서류 발급 세 탭 열기, 운송비 내역서 [공유] 창 열고 닫기.
4. 홈의 미수금 줄 그대로(기능 사이 부르기 `useReceivablesData`), 콘솔 오류 없음.
