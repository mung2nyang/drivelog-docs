# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 4-D-2 — 이력형 주석 줄이기: `components/` 나머지 `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` C, 메모 규칙 "코드 주석은 결론 1~2줄". 앞 슬라이스 4-D-1 `[x]`(react-app `6f01c5b`). 4번 계획: 4-A~4-D-1 `[x]` → **4-D-2 (이번)** → 4-E `lib/`. 이번부터 `kickoff-brief` 스킬 형식.

### 쉬운 요약
- 화면 부품 폴더(`components/`) 중 아직 안 한 32개 파일의 주석에서 "Step 3·재감사·로드맵 N번" 같은 작업 기록을 빼고, 필요한 설명만 1~2줄로 남긴다.
- 주석만 바뀌어 앱 동작·화면은 그대로다. 코드를 읽을 때 지금과 상관없는 옛 경위가 안 보이게 된다.

### 현재 상태 (코드 확인)
- 32개 파일에 작업 기록 주석이 있음(근거: 4번 공통 검색 패턴 `재감사|Step N|슬라이스 X|로드맵 N|§N|migration-|바닐라|보리 지시` 등). 대부분 파일 머리 1줄, `DriverConnectionPage.jsx`·`DocumentIssuePage.jsx`·`revenue/` 4개가 여러 줄.
- `DriverConnectionPage.jsx:58~62`: "drivers.js가 아직 @ts-check 대상이 아니라" 타입을 `null|undefined`로 억지로 맞추는 표시를 달아 뒀다는 설명 — **지금은 사실이 아님**(근거: `domain/drivers.js:1` `// @ts-check`, `:56` `@param {string|null|undefined} [editingId]`).

### 목표 상태 (기대 동작)
1. 아래 표대로 32개 파일 주석이 바뀐다(왼쪽 = 지울 작업 기록, 오른쪽 = 남는 최종 문구).
2. `DriverConnectionPage.jsx:58~62` 설명 4줄을 지우고, 그 설명이 가리키던 억지 타입 표시 `/** @type {null|undefined} */ (editingId)` → `editingId`로 고친다. **실행되는 코드는 같다**(괄호와 주석만 빠짐) — 이제 맞는 타입이 그대로 통하므로 타입 검사가 근거.
3. 실행 코드 변경 0. 동작·화면 변화 없음.

| 파일 | 지울 것 | 남는 최종 문구 |
|---|---|---|
| `AppSettingsBackupSection.jsx` | "AppSettingsPage에서 분리(§6 200줄)" | `// 게스트 전용 데이터 관리(백업/복구).` |
| `BootRetryScreen.jsx` | "로드맵 19:", "(18-E 안내 화면과 같은 문구)" | `// 앱 켤 때 연동 확인이 실패하면 어느 칸으로 들어갈지 몰라 들어가지 않고 이 화면.` |
| `ClientManagementPage.jsx` | "Step 7:" | `// 얇은 라우트 어댑터. 구현은 clients/ClientListPage.jsx.` |
| `CustomerCenterPage.jsx` | (§6 예외 — 그대로 둠) | 변경 없음 |
| `DriverConnectionPage.jsx` | 머리 4줄 경위, 58~61줄 | `// 기사 연동 화면. 저장·상태 변경·삭제는 lib/directMutationActions.js, 초대 폼은 DriverFormModal.jsx.` |
| `DriverFormModal.jsx` | "Step 0-4 감사 보완 4차: … 로직은 한 글자도 안 바꿨다" | `// 기사 초대 폼 모달.` |
| `FixedRouteBlock.jsx` | "AppSettingsPage.jsx에서 분리(… migration-audit-plan.md …)" | `// 앱 설정의 고정노선 묶음.` (14줄 `docs/sot.md §4-4e`는 규칙 근거라 남김) |
| `LoadFailScreen.jsx` | "로드맵 32:" | `// 비회원 기록을 못 읽으면 앱 대신 이 화면 — 빈 화면에서 입력해 기존 기록을 덮어쓰지 않게. 모양은 BootRetryScreen과 같음.` |
| `MaintFuelPage.jsx` | "AGENTS" 낱말 | `// 메인·서브 스코프를 한 화면에서 처리 — §6 예외(≤250).` |
| `PageLoadErrorBoundary.jsx` | "로드맵 33:" | 첫 줄 `// 화면을 그리다 오류(주로 업데이트 뒤 옛 화면 파일을 못 받음)가 나면 …` (나머지 그대로) |
| `PersonalInfoPage.jsx:46` | "(로드맵 7-A)" | `… 서버 delete_own_account도 거절.` |
| `PullRefreshIndicator.jsx` | "로드맵 21:" | `// 당겨서 새로고침 표시 — 평소엔 화면 위 밖에 숨어 있다가 당긴 만큼 내려옴. 모양은 pull-refresh.css.` |
| `ReportPage.jsx` | "(9-C-1)", "(9-C-2)" | `// §6: PDF/이미지 내보내기·공유 모달이 … 나란히 둠(응집도). 달 이동은 documents/MonthNavigator.jsx, 보는 달은 서류 발급이 줌` |
| `RoutePresetEditor.jsx` | 분리 경위 | `// 앱 설정의 노선 이름표(프리셋) 편집.` |
| `RunCountChips.jsx` | 분리 경위 | `// 앱 설정의 운행 횟수 버튼 편집.` |
| `SwitchRow.jsx` | 분리 경위 2줄 | `// 앱 설정의 켜기/끄기 줄 한 개.` |
| `cars/CarListPage.jsx:113` | "(로드맵 7-C)" | `// 연동 중이면 바로 끊지 않고 해제 요청 — 동의·3일 전까지 …` |
| `documents/DocumentIssuePage.jsx` | "(9-C-1, 옛 \"운송비 내역서\")", "(세금계산서 정리 ②)", "(9-C-1 결정)", "(9-C-2)", "(로드맵 9번 ⑨)", "(예전과 같음)" | 5줄 설명 내용은 그대로, 괄호 속 번호·경위만 뺌 |
| `drivers/CarBusinessInfoSection.jsx` | "(이관 감사 11-5·11-6)" | `… 정산 계좌 입력.` |
| `drivers/DriverUnlinkControls.jsx` | "(로드맵 7-C)" | `// 연동 중 기사 카드의 해제 영역 — 요청 없음: [해제 요청], …` |
| `drivers/EmployerLinkCard.jsx` | "(로드맵 7-C-2)" | `// 기사 개인정보 화면의 "소속 연결" 카드 — 해제 요청·취소·동의. …` |
| `drivers/LinkedDriverClientsPage.jsx` | "AGENTS" 낱말 | `// 연동/미연동 서브 거래처 화면. 두 모드 한 화면 응집 — §6 예외(≤250).` |
| `drivers/LinkedDriverManagementPage.jsx` | "AGENTS" 낱말 | `// 모드 판별은 domain/driverManagementContext.js — §6 응집도(≤250).` |
| `revenue/OwnerMonthlyCards.jsx` | "재감사 2차(FAIL 지적) — RevenuePage.jsx 분할 조각:", "D-2:" | `// 오너 월별 손익 카드 3장(순이익/수입/지출)과 접이식 상세 행.` / `// variant='driverSelf' — 소속기사(급여·유가보조금 숨김, 순이익에 정산율).` |
| `revenue/OwnerRevenueView.jsx` | 분할 경위, "Step 9 슬라이스 D:" | `// 차주(오너) 손익 화면. 기사 탭에서 개별 기사(차량) 드롭다운 필터.` |
| `revenue/RevenueNav.jsx` | 분할 경위 | `// 연/월 이동(DateNav). OwnerRevenueView·DriverRevenueView가 같이 쓴다.` |
| `revenue/driverRevenueScope.js` | "Step 9 slice C-2:"(영어 2줄) | `// 소속기사 본인 매출은 배정 차량 1대로 좁힌다 — getMonthlyFareRevenue는 그대로, 부르는 쪽이 settings·workData를 좁힌다.` |
| `revenue/revenueFormat.js` | 분할 경위 3줄 | `// 여러 매출 화면이 같이 쓰는 작은 순수 포맷 함수.` |
| `shared/AppDropdown.jsx` | "바닐라 `data-app-dropdown`과 같은" | ` * 버튼+목록으로 된 드롭다운. 네이티브 select 색만 바꾸는 방식은 쓰지 않는다.` |
| `shared/CardActionButtons.jsx` | "(§2-1-B)" | ` * 카드 수정/삭제 아이콘 버튼 공용화.` |
| `shared/Skeleton.jsx` | "로드맵 18-D:" | `// 불러오는 동안 글자·트럭 그림 대신 회색 뼈대(빛이 지나감). 모양은 skeleton.css.` |
| `side-menu/SideMenuIcons.jsx` | "(… 로드맵 15번)" | `// 사이드메뉴 전용 SVG 아이콘.` |

### 건드릴 파일
- `src/components/` 32개 — 위 표의 파일(주석만, `DriverConnectionPage.jsx`는 타입 표시 1곳 포함). `CustomerCenterPage.jsx`는 §6 예외 주석뿐이라 결과적으로 안 바뀔 수 있음.
- 공용 스타일·공용 값 변경 없음 → 영향 목록 없음.

### 안 건드릴 것
- `components/`의 다른 파일, CSS, 테스트 파일, 4-E `lib/`.
- §6 예외 이유 주석(`CustomerCenterPage.jsx`·`MaintFuelPage.jsx`·`ReportPage.jsx`·`cars/CarListPage.jsx`·`drivers/*`) — 규칙이 요구하는 표시라 내용 유지, "AGENTS"·번호 낱말만 뺌.
- `docs/sot.md` 절 번호(`FixedRouteBlock.jsx:14`) — 도메인 규칙 근거.

### 검증
- 32개 파일 모두 HEAD 판과 작업 판을 `esbuild --minify-whitespace --minify-syntax`(주석·공백만 지움, 이름 유지)로 바꿔 글자 하나까지 같은지 비교.
- `typecheck` 오류 0(2번 타입 표시 정리의 근거), 엄격 검사 목록 438 이하, `npm test`·`build` 통과.

### 파일 길이
- 32개 파일 모두 줄어들거나 같음. 200줄 넘는 `CustomerCenterPage.jsx` 228·`MaintFuelPage.jsx` 243·`ReportPage.jsx` 235·`cars/CarListPage.jsx` 230은 기존 §6 예외 그대로(늘지 않음). `DriverConnectionPage.jsx` 194 → 약 186.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 실패하면 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법 (휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
1. 메뉴 → 기사 관리(기사 연동) 열기 → 기사 1명 정보 고쳐 [저장] → 저장됨 안내가 뜨고 목록에 바뀐 값이 보이면 성공.
2. 매출 화면 → 기사 탭 드롭다운이 전처럼 바뀌면 성공.
3. 서류 발급 화면의 탭(세금계산서·운송비 내역서·일상점검표)이 전처럼 보이면 성공.

### 진행 결과
- 표대로 31개 파일 주석 정리(37줄 추가·52줄 삭제). `CustomerCenterPage.jsx`는 §6 예외 주석뿐이라 예상대로 변경 없음. 남은 이력 낱말 0곳(`.slice(`는 코드, `docs/sot.md §4-4e`는 규칙 근거라 그대로).
- 목표 2: `DriverConnectionPage.jsx` 설명 4줄·억지 타입 표시 삭제 → `typecheck` 오류 0으로 맞는 타입이 그대로 통함 확인. 194→187줄.
- **실행 코드 같음 확인**: 32개 파일 모두 HEAD 판과 비교(주석·공백만 제거, 이름 유지) 글자 하나까지 같음.
- 로컬 `npm test` 전부 통과(단위 832 / 화면 365), `typecheck` 오류 0, 엄격 검사 목록 438 그대로, `build` 성공.
- react-app `28fd903`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-10).
