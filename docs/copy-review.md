# 앱 문구 점검 — 어색한 문구와 수정안 (2026-10-08)

**보리 결정(2026-10-08): ① 용어는 F절 추천대로 통일 ② 말투는 "~습니다"로 통일 ③ 공지사항 임시 글은 비움(출시 때 실제 공지) ④ 온보딩 화면(`OnboardingPage.jsx`) 문구는 전부 제외 — 보리가 나중에 직접 다듬음.** 아래 수정안은 이 결정에 맞춰 고쳐 둠.
조사만 함. 코드 수정 없음. 대상: `react-app/src` 화면에 보이는 한국어 문구 1,087개 전수.
제외: 개발자용 콘솔 기록(`[boot] …` 등), 법정 서식 문구(일상점검표·세금계산서 엑셀 칸 이름 — 서식 그대로여야 함).

---

## A. 사실과 다른 안내 (먼저 고칠 것)

| 위치 | 현재 | 문제 | 수정안 |
|---|---|---|---|
| `CustomerCenterPage.jsx:27` (자주 묻는 질문) | 게스트 데이터는 앱 설정의 **백업 파일 내보내기·가져오기**로 옮길 수 있습니다. | 실제 버튼 이름은 "백업 저장하기 / 백업 불러오기" | 로그인 계정은 다른 휴대폰에서 같은 계정으로 로그인하면 됩니다. 비회원 기록은 앱 설정의 [백업 저장하기]로 파일을 만든 뒤, 새 휴대폰에서 [백업 불러오기]로 옮길 수 있습니다. |
| `CustomerCenterPage.jsx:23` | 차량 관리에서 차량을 추가하고 **기본 차량**을 변경할 수 있습니다. | 앱 안에 "기본 차량"이라는 말이 없음(앱은 "메인 차량") | 메뉴 → 차량 관리에서 차량을 추가하거나 수정할 수 있습니다. |
| `NoticePage.jsx:8-20` (공지사항 3개) | [필독] 서비스 이용 안내 / 데이터 백업 권장 안내 / 최근 업데이트 안내 (2026.08.01~08.11) | 실제 공지가 아니라 화면을 채우려고 넣은 **임시 글**. 날짜·내용 모두 지어낸 것 | 출시 전 **실제 공지로 바꾸거나 전부 삭제**. 예: "운행 일지를 시작합니다" 1개만 |
| `ComingSoonPage.jsx:13` | 이 화면은 다음에 옮깁니다. | 이관 작업 때 쓰던 개발 메모가 사용자에게 보임 | 준비 중인 화면입니다. |

## B. 개발 용어가 그대로 보이는 곳

사용자는 "클라우드·동기화·세션·로컬·행·도메인·JSON"을 모름. → "서버", "이 휴대폰", "로그인 정보" 정도로.

| 위치 | 현재 | 수정안 |
|---|---|---|
| `HydrationRetryBanner.jsx:61` | 클라우드 데이터를 불러오지 못했습니다. 로컬 데이터로 계속 쓸 수 있어요. | 서버에서 기록을 불러오지 못했습니다. 이 휴대폰에 있는 기록으로 계속 쓸 수 있습니다. |
| `HydrationRetryBanner.jsx:50·53` | 클라우드 데이터를 다시 불러왔습니다. / 다시 시도했지만 아직 클라우드 데이터를 불러오지 못했습니다. | 기록을 다시 불러왔습니다. / 다시 시도했지만 아직 불러오지 못했습니다. |
| `HydrationRetryBanner.jsx:63` | 재시도 중... | 다시 시도하는 중… |
| `useAppSession.js:74` | 로그인은 유지됐지만 클라우드 데이터를 일부 못 불러왔습니다. | 로그인은 됐지만 기록 일부를 불러오지 못했습니다. |
| `InviteRedeemModal.jsx:31` | 연동은 됐지만 클라우드 데이터를 일부 못 불러왔습니다. | 연동은 됐지만 기록 일부를 불러오지 못했습니다. |
| `EmployerLinkCard.jsx:14` | 연동은 해제됐지만 내 계정 데이터를 일부 못 불러왔습니다. | 연동은 해제됐지만 내 기록 일부를 불러오지 못했습니다. |
| `AppSettingsPage.jsx:87` | 클라우드 동기화 중입니다. 잠시 후 다시 시도해 주세요. | 기록을 저장하는 중입니다. 잠시 후 다시 시도해 주세요. |
| `cloudSession.js:119` | 클라우드 동기화가 아직 준비되지 않았습니다. | 아직 기록을 불러오는 중입니다. 잠시 후 다시 시도해 주세요. |
| `cloudSession.js:154·160`, `clientCloudSave.js:12` | 세션이 바뀌었습니다 / 세션이 바뀌어 저장을 중단했습니다 / 다른 계정의 데이터를 저장할 수 없습니다 | 로그인 정보가 바뀌어 저장하지 못했습니다. 화면을 새로고침한 뒤 다시 시도해 주세요. (세 곳 같은 문구로) |
| `outboxFlush.js:52` | 선택한 차량이 아직 클라우드에 동기화되지 않았습니다. | 방금 등록한 차량이 아직 저장 중입니다. 잠시 후 다시 시도해 주세요. |
| `taxInvoices.js:39` | 차량 정보가 아직 서버에 등록되지 않았습니다. | (위와 같은 문구) |
| `directMutations.js:46·69·198` | 삭제할 차량 **행**을 / 거래처 **행**을 / 기사 연동 **행**을 찾지 못했습니다. | 이미 삭제됐거나 찾을 수 없습니다. 화면을 새로고침해 주세요. |
| `driverLinkRpc.js:32` | 서버가 저장 결과를 돌려주지 않았습니다. | 저장이 끝났는지 확인하지 못했습니다. 새로고침해서 확인해 주세요. |
| `directMutations.js:168` | 초대 코드 생성에 반복적으로 실패했습니다. | 초대 코드를 만들지 못했습니다. 잠시 후 다시 눌러 주세요. |
| `AppSettingsBackupSection.jsx:75` | 파일 내용이 손상되었거나 **JSON** 파일이 아닙니다. | 백업 파일이 아니거나 파일이 손상되었습니다. |
| `guestBackup.js:83·92·101~138·172` | 유효한 백업 데이터가 아닙니다 / 유효한 **도메인**이 없습니다 / **settings·profile·workData·workLogs·subWorkData** 형식이 올바르지 않습니다 등 9개 | 전부 하나로: 백업 파일이 손상되었거나 운행일지 백업 파일이 아닙니다. |
| `notifications.js:121` | 아직 백업한 적이 없습니다. **브라우저 데이터** 삭제 시 기록이 사라질 수 있습니다. | 아직 백업한 적이 없습니다. 앱을 지우거나 휴대폰을 바꾸면 기록이 사라질 수 있습니다. |
| `CustomerCenterPage.jsx:19` | …**브라우저 데이터**를 지우거나 기기를 바꾸면… | …앱을 지우거나 휴대폰을 바꾸면… |
| `lib/logPendingLifecycle.js:122` | 운행 대기 기록을 확인할 수 없습니다. | 기록을 불러오는 중입니다. 잠시 후 다시 시도해 주세요. |
| `RoutePresetEditor.jsx:36` | …일일운행에서 **원탭**으로 횟수를 기록하세요. | …일일운행에서 한 번 눌러 횟수를 기록하세요. |
| `FixedRouteChips.jsx:19` | 자주 다니는 노선 **원탭** 기록 | 자주 다니는 노선 (누르면 1회 추가) |

## C. AI가 쓴 것 같은 빈말 (빼거나 짧게)

| 위치 | 현재 | 수정안 |
|---|---|---|
| `NoticePage.jsx:44-45` | 새로운 소식을 확인하세요 / 서비스 업데이트와 중요한 안내를 가장 먼저 전해드립니다. | **삭제** (제목 "공지사항"만으로 충분) |
| `NotificationPanel.jsx:24` | 확인이 필요한 알림 내역입니다. | **삭제** |
| `CustomerCenterPage.jsx:196` | 궁금한 내용을 빠르게 확인해 보세요. | **삭제** |
| `DataDownloadModal.jsx:52` | …내 기기로 다운로드합니다.\n세무 증빙이나 개인 보관을 위한 내보내기 기능입니다. | 지금까지 입력한 운행·정산·차량 기록 전체를 파일로 받습니다. (둘째 줄 삭제) |
| `notifications.js:101` | 관련 법령에 따라 출발 전 일상점검표 작성이 의무화되었습니다. **안전한 운행을 위해** 아래 버튼을 눌러 기능을 켜주세요. | 출발 전 일상점검표 작성이 의무화되었습니다. 아래 버튼을 누르면 일상점검표를 쓸 수 있습니다. |
| `PersonalInfoPage.jsx:18` | **이 작업은** 취소할 수 없습니다. 한 번 더 확인해 주세요 | 탈퇴하면 되돌릴 수 없습니다. 정말 탈퇴하시겠습니까? |
| `notifications.js:120` | 마지막 백업**으로부터** N일이 지났습니다. 최신 데이터로 백업해 주세요. | 마지막 백업 후 N일이 지났습니다. 지금 백업해 주세요. |

## D. 딱딱하거나 번역투인 문장

| 위치 | 현재 | 수정안 |
|---|---|---|
| `ReportPage.jsx:154` | 세부 내역서가 조회되었습니다. | 세부 내역서를 불러왔습니다. |
| `ReportShareModal.jsx:62` | 이 기기에서는 파일 공유를 지원하지 않습니다. | 이 휴대폰에서는 파일 공유를 할 수 없습니다. [PDF 다운로드]로 저장한 뒤 보내 주세요. |
| `ReportShareModal.jsx:44` | 내보낼 화면을 찾지 못했습니다. | 내역서를 만들지 못했습니다. 창을 닫았다가 다시 시도해 주세요. |
| `DailyInspectionExportBar.jsx:39` | 내보낼 서식을 찾지 못했습니다. | 점검표를 만들지 못했습니다. 창을 닫았다가 다시 시도해 주세요. |
| `ReportShareModal.jsx:81` | 특정 거래처의 상세내역을 조회하고, 거래처 연락처가 등록되어 있는지 확인해 주세요. | 문자로 보내려면 [세부내역]에서 거래처를 하나 고르고, 그 거래처에 연락처가 있는지 확인해 주세요. |
| `CarListPage.jsx:29` | **해당** 차량을 삭제하시겠습니까? 이 차량으로 기록된 운행 내역도 함께 삭제되며 복구할 수 없습니다. | 이 차량을 삭제하시겠습니까? 이 차량의 운행 기록도 함께 지워지며 되돌릴 수 없습니다. |
| `ClientListPage.jsx:114` | **해당 업체를** 삭제하시겠습니까? | 이 거래처를 삭제하시겠습니까? |
| `CarFormModal.jsx:110` | **해당** 차량(기사) 운행 매출 중 기사에게 지급할 비율(%)을 설정합니다. | 이 차량 매출 중 기사에게 지급할 비율(%)입니다. |
| `AppSettingsPage.jsx:113` | 금액은 만 원 단위로 요약되어 표시됩니다. | 금액은 만 원 단위로 줄여서 표시합니다. |
| `EmployerLinkCard.jsx:59` | 연동된 차주**와의** 연결 관리 | 차주와 연결 관리 |
| `CalendarMonthSummary.jsx:49` | 이번 달 총 N원의 미수금이 있습니다. | 이번 달 미수금 N원 |
| `CalendarMonthSummary.jsx:28` | …운송료에서 거래처 수수료를 뺀 금액을 기준으로 **계산해 빼요**. | 차량 관리에 적은 이 차량의 수수료입니다. 거래처 수수료를 뺀 운송료에서 이만큼 다시 뺍니다. |
| `durableWriteGuard.js:78` | 마지막 편집을 아직 안전하게 저장하지 못했습니다. 그래도 나가시겠습니까? | 방금 고친 내용이 아직 저장되지 않았습니다. 그래도 나가시겠습니까? |
| `outboxFlush.js:65` | 같은 차량에 이미 겹치는 기간으로 연결되어 있거나 초대된 기록이 있습니다. | 이 차량은 같은 기간에 이미 다른 기사가 연결(또는 초대)되어 있습니다. |
| `invoices.js:83` | 사업자등록번호**란이 입력이 안 되어 있어요**. 먼저 입력해 주세요. | 사업자등록번호를 먼저 입력해 주세요. |
| `CallDetailForm.jsx:74` | ↺ 직전 항목과 동일하게 채우기 | ↺ 바로 전 내역 그대로 채우기 |
| `CallDetailForm.jsx:104` | 부가세 포함 금액으로 계약하셨다면 ÷1.1 한 금액을 입력해 주세요. | 부가세 포함 금액이면 1.1로 나눈 금액을 넣어 주세요. (예: 110,000원 → 100,000원) |
| `CallDetailForm.jsx:194` | 부가세 해제 | 부가세 없음 |
| `CallDetailCard.jsx:37·39` | 상차지 **미상** / 하차지 **미상** | 상차지 없음 / 하차지 없음 |
| `ReceivableItemCard.jsx:60` (미수금 화면) | 상차지 미상 / 하차지 미상 | 상차지 없음 / 하차지 없음 (3-A 배포 확인 때 발견) |
| `ReceivableItemCard.jsx:101` | **이 건** 입금 완료 | 입금 완료 |
| `DriverConnectionPage.jsx:147` | (계약기간) ~ 계속 | ~ 종료일 없음 |
| `DailyInspectionModal.jsx:122` | (예시)창닦이기 불량 | 예: 창닦이기 불량 → 와이퍼 교체 |
| `DailyInspectionExportBar.jsx:71` | ※ PDF,이미지 등 내보내기시 선택된 '월'의 전체 점검표가 '법정 서식'으로 저장됩니다. | ※ PDF·이미지로 내보내면 선택한 달 전체가 법정 서식으로 저장됩니다. |
| `TaxInvoiceTab.jsx:32-33` | 2. 홈택스에서 사업자로 **로그인을 해주세요**. / 3. ***일괄작성 파일**에서 파일 선택을… | 2. 홈택스에 사업자로 로그인해 주세요. / 3. [일괄작성 파일]에서 [파일 선택]을 눌러 앱에서 받은 엑셀을 올려 주세요. |

## E. 맞춤법·띄어쓰기

| 위치 | 현재 | 수정안 |
|---|---|---|
| `DailyInspectionNotice.jsx:86` | 작성이 완료 되었습니다. | 작성이 완료되었습니다. (또는 "작성 완료") |
| `CallDetailCard.jsx:28·48·56` | 운행거리:12km / 출발:… ➜ 도착:… / 비고:- | 운행거리 12km / 출발 … → 도착 … / 비고: - |
| `CallDetailCard.jsx:70` | 전화걸기 | 전화 걸기 |
| `CallDetailForm.jsx:151·204·229` | 직접입력 또는 선택 / 금액입력 / 특이사항 입력 | 직접 입력 또는 선택 / 금액 입력 |
| `MessageSettingsPage.jsx:69·78·87` | 미수금 문자**보내기** / 입금 요청 문자보내기 / 운행 완료 문자보내기 | 문자 보내기 |
| `ReportPage.jsx:166` | 세부내역(거래처선택) | 세부 내역 (거래처 선택) |
| `PersonalInfoPage.jsx:18` | 마침표 빠짐 | (C의 수정안으로 해결) |

## F. 같은 것을 다르게 부르는 곳 (하나로 통일 — 결정 필요)

| 지금 섞여 쓰는 말 | 추천 | 비고 |
|---|---|---|
| 운행 일지 / 운행일지 | **운행일지** | 보리 결정(2026-10-09) — **앱 이름 포함**(홈 화면 아이콘 이름·탭 제목·로딩 화면 "운행 일지" → "운행일지"). 문구 정리 4에서 |
| 고정 노선 / 고정노선 | **고정 노선** | |
| 초대 코드 / 초대코드 | **초대 코드** | |
| 할당 / 배정 (할당 차량, 할당 중, 배정차량) | **배정** | "할당"은 컴퓨터 용어 느낌 |
| 비회원 / 게스트 | **비회원** | 첫 화면 버튼이 "비회원으로 시작하기" |
| 기사차량 / 기사 차량, 배정차량 | **기사 차량**, **배정 차량** | |
| 기사연동관리(마이페이지) / 기사 연동 관리(화면 제목) | **기사 연동 관리** | |
| 미수금/정산(마이페이지) / 미수금/정산 관리(메뉴) | **미수금/정산 관리** | |
| 문자 발송(기사 초대) / 문자 보내기 | **문자 보내기** | |
| 업체명 / 거래처명, "해당 업체" | **거래처명**, "이 거래처" | |
| 사업자 번호 / 사업자번호 / 사업자등록번호 | **사업자등록번호** | 세금계산서 칸 이름과 같게 |
| 차량톤수 / 차량 톤수, 입금은행 / 입금 은행 | 띄어 씀 | |

## G. 말투 — "~습니다"로 통일 (보리 결정 2026-10-08)

"~해 주세요", "~하세요"(부탁·안내)는 "~습니다" 말투와 함께 써도 자연스러워 그대로 둠. 아래 "~해요" 문장만 바꿈.

| 위치 | 현재 | 수정안 |
|---|---|---|
| `App.jsx:119` | 설정을 저장했어요. | 설정을 저장했습니다. |
| `App.jsx:68` | …언제든 마이페이지에서 로그인할 수 있어요. | …언제든 마이페이지에서 로그인할 수 있습니다. |
| `useBackToExit.js:7` | 뒤로가기를 한 번 더 누르면 종료돼요 | 뒤로가기를 한 번 더 누르면 종료됩니다. |
| `CustomerCenterPage.jsx:19` | …기록이 사라질 수 있어요. 게스트는… | 로그인하면 서버에 자동 저장됩니다. 비회원은 이 휴대폰에만 저장되므로, 앱을 지우거나 휴대폰을 바꾸면 기록이 사라질 수 있습니다. 앱 설정의 백업으로 꼭 보관해 주세요. |
| `CustomerCenterPage.jsx:18·22·26·30` (질문) | 자동으로 저장되나요? / 어떻게 하나요? / 옮길 수 있나요? / 어디서 확인하나요? | **그대로** — 사용자가 묻는 질문이라 자연스러움 |
| `WelcomeProfileView.jsx:41` | 처음 오셨네요. 이름과 휴대전화 번호를 알려 주시면 바로 시작할 수 있어요. | 처음 오셨습니다. … 는 어색 → "이름과 휴대전화 번호를 입력하면 바로 시작할 수 있습니다." |
| `CalendarMonthSummary.jsx:11` | …+ 부가세예요. …차량 지출 기록이에요. | 운송료 + 파렛트 회수 청구액 − 수수료 + 부가세입니다. 아래 정비비·주유비·통행료는 합계에서 빼지 않은 이번 달 차량 지출 기록입니다. |
| `CalendarMonthSummary.jsx:21` | …이 거래처 운송료에서 빼요. | 거래처 등록 때 적은 수수료만큼 이 거래처 운송료에서 뺍니다. |
| `CalendarMonthSummary.jsx:28` | (D절 수정안) | |
| `FixedRouteClientModal.jsx:106` | 고정노선 거래처 연결을 해제할까요? | 고정 노선 거래처 연결을 해제하시겠습니까? |
| `RunCountChips.jsx:98` | N회 버튼을 삭제할까요? | N회 버튼을 삭제하시겠습니까? |
| `CustomerCenterPage.jsx:211` | 무엇을 도와드릴까요? | 그대로 (인사말) |

## 슬라이스 나누기 (안)

문구만 바꾸는 작업이라 파일 수가 많아도 위험은 낮음. 화면 묶음별 4개.

1. **사실 오류·임시 글** — A절 + 같은 파일의 C절 빈말: `NoticePage`·`CustomerCenterPage`·`ComingSoonPage`·`NotificationPanel` (4개)
2. **개발 용어 오류 문구** — B절: `lib/*`·`HydrationRetryBanner`·`useAppSession`·`InviteRedeemModal`·`EmployerLinkCard`·`AppSettingsBackupSection` 등
3. **화면 문장·맞춤법·말투** — C·D·E·G절 나머지(온보딩 제외): 일지·내역서·홈 카드·일상점검표 등
4. **용어 통일** — F절: 여러 화면에 흩어져 있어 마지막에 한 번에. 문구를 검사하는 테스트도 같이 고침

---

## 손 안 대도 되는 것 (참고)

- 일상점검표 법정 서식 문구, 세금계산서 엑셀 칸 이름 — 정해진 서식이라 그대로.
- 문자 기본 문구(`messageTemplates.js`) — 거래처에 보내는 글이라 "~습니다"가 맞음.
- 버튼·칸 이름 대부분(저장, 취소, 차량번호 등) — 문제없음.


---

## 문구 정리 4 (용어 통일) 조사 — 2026-10-09

조사만. 주석·테스트 제목 빼고 **화면에 보이는 글자**만 셈.

| 용어 | 코드 | 글자를 검사하는 테스트 | 메모 |
|---|---|---|---|
| 운행 일지 → **운행일지** | 21곳 / 14파일 (+ `index.html` 2·`manifest.webmanifest` 2) | 8줄 / 6파일 (`pwaManifest`·`LoadingScreen`·`CalendarPage`·`CarDriverConnectPanel`·`CallDetailList`) | 앱 이름: 탭 제목·홈 화면 아이콘 이름·로딩 화면·상단 배너·사이드메뉴·첫 화면 로고 |
| 고정노선 → 고정 노선 | 7곳 / 5파일 | 3줄 / 2파일 | `financeOwnerDetail.js:65` 이름 없는 고정 노선 거래처의 기본 표시 이름 — 내역서 금액 묶음 이름으로도 쓰임(테스트 `CalendarPage.test.js:400`) |
| 초대코드 → 초대 코드 | 10곳 / 5파일 | 6줄 / 5파일 | `inviteCode.security.test.js:86`은 서버(DB 함수)가 주는 문구라 DB 쪽은 안 바뀜 — 앱 문구만 |
| 할당 → 배정 | 12곳 / 4파일 | 5줄 / 2파일 | 기사 연결 상태 "할당 예정/중/종료" → "배정 예정/중/종료" |
| 기사차량·배정차량 → 기사 차량·배정 차량 | 9곳 / 7파일 | 4줄 / 2파일 | **주의**: 홈 정산 카드의 "기사차량 수수료" 기본 이름은 (i) 설명을 만들 때 "○○ 차량 …" 모양을 잘라 쓰는 코드(`CalendarMonthSummary.jsx:26`)와 겹침 — "기사 차량 수수료"로 바꾸면 설명이 "이 차량의 수수료(수수료)입니다"로 깨짐. 바꾸려면 그 한 줄도 손봐야 함 |
| 게스트 → 비회원 | 0곳 (문구 정리 1에서 다 바뀜) | — | 테스트 안 설명 글에만 남음(화면 무관) |
| 기사연동관리 → 기사 연동 관리 | 1곳 (`MyPage.jsx:144`) | 0 | |
| 미수금/정산 → 미수금/정산 관리 | 1곳 (`MyPage.jsx:45`) | 확인 필요 | 사이드메뉴는 이미 "미수금/정산 관리" |
| 문자 발송 → 문자 보내기 | 1곳 (`DriverFormModal.jsx:64`) | 1줄 | |
| 업체명 → 거래처명 | 3곳 / 2파일 | 1줄 | 거래처 등록 창 칸 이름·안내 |
| 사업자 번호·사업자번호 → 사업자등록번호 | 5곳 / 5파일 | 1줄 | 세금계산서 엑셀 칸 이름은 홈택스 서식이라 제외 |
| 차량톤수·입금은행 → 차량 톤수·입금 은행 | 4곳 / 2파일 | 2줄 / 2파일 | 운송비 내역서 표 머리 |

- 합계: 코드 약 **75곳 / 약 35파일**, 글자를 검사하는 테스트 약 **30줄 / 약 20파일**. 테스트 제목·설명 글에 옛 용어가 들어간 곳은 약 99곳(화면과 무관, 안 바꿔도 됨).
- 앱 밖에서 "운행 일지"가 남는 곳(이번 범위 밖): 처리방침 초안(`docs/privacy-policy-draft.md`·`react-app/privacy-draft/privacy.html`, 9곳 — 10-P ⓒ-2에서), Google Cloud 로그인 동의 화면의 앱 이름(보리 설정), 이미 설치한 홈 화면 앱 이름(다시 설치해야 바뀜).
- 나누기(안): **4-A 앱 이름·"운행일지"**(약 16파일 + 테스트 6) / **4-B 나머지 용어**(약 20파일 + 테스트 14).

---

## 문구 정리 5 — 2026-10-25 (보리 수정안)

보리가 작성한 수정안(`C:/dev/teat/문구정리-2026-10-25-수정안.txt`, 135개)을 AI가 코드·이 문서 결정·sot와 대조. 보리 결정(2026-10-25):
- 화면이 깨지거나 뜻이 바뀌는 4개(날씨 문장 띄어쓰기, "이번 달 정산액:", "운행료 합계", 내역서 문자 "연락처를 등록해 주세요")와 10월 결정과 부딪히는 것("휴대폰"→"기기" 5곳, "데이터를 불러왔습니다", 홈 오늘 카드 "~예요", 일상점검표 법정 서식 문구 9개, "기본 거래처")은 **뺀다**.
- sot·로드맵에 그대로 적힌 문구는 고칠 때 그 기록도 같이 바꾼다.
- 결제 조건 "익월·익익월"은 **"다음 달·다다음 달"**로.
- 온보딩 문구도 **이번에 같이**.
- 마침표는 **새로 붙이지 않는다**(원문에 마침표가 없던 곳).

AI 정리: 중복 3개·수정 불필요 2개·마침표만 붙이는 2개 뺌, 수정안이 "이러면 원문 유지"라고 조건을 단 3개(전체 손익 탭, 달력 "총 N회 운행", 만 원 표시 예시 — 화면이 실제로 "11.5만"으로 보여 "11.5만 원" 예시가 틀림)는 원문 유지, 조사·변수 때문에 깨지는 3개는 메모대로 맞춤, 위치가 틀린 1개(`ExpenseFormModal.jsx:136-137` → 실제 `CarFormModal.jsx`)는 실제 위치에서 고침. 적용 중 정함(2026-10-25 보리): 수정안의 가운뎃점(·) 3곳은 로드맵 87 규칙대로 쉼표·빗금으로. "자동 입력:"은 문자 양식 4곳 모두 같은 문구라 4곳 다 바꿈. 5-B: 날씨 "시·군·구"도 같은 규칙으로 "시, 군, 구", #94(문자 구간 "○월분 N건")는 원문 유지. 확정 105개 — **A 묶음 70개(계정·온보딩·설정·고객센터·차량·거래처·기사·서류·달력·일지)**, **B 묶음 35개(운비서·알림·미수금·매출·결제 조건)**.

| # | 묶음 | 위치 | 지금 | 바꿀 문구 | 메모 |
|---|---|---|---|---|---|
| 1 | A | `features/account/ui/DataDownloadModal.jsx:84` | 더할 기록이 없습니다. 파일의 기록이 모두 이미 있습니다. | 추가할 기록이 없습니다. 파일에 있는 기록이 모두 이미 저장되어 있습니다. |  |
| 2 | A | `features/account/ui/DataDownloadModal.jsx:102` | ${res.toast} (먼저 저장됨: ${done}) | ${res.toast} 먼저 저장된 항목: ${done} |  |
| 3 | A | `features/account/ui/DataDownloadModal.jsx:115` | 이 파일에서 지금 없는 것만 더합니다. 이미 있는 기록은 내용이 달라도 건드리지 않습니다. | 이 파일에 있는 항목 중 현재 저장되어 있지 않은 것만 추가합니다. 이미 저장된 기록은 내용이 달라도 변경하지 않습니다. |  |
| 4 | A | `features/account/ui/DataDownloadModal.jsx:120` | 일지만 불러옵니다. 거래처, 지출, 세금계산서와 다른 차량 기록은 건너뜁니다. | 운행일지만 불러옵니다. 거래처, 지출, 세금계산서 및 다른 차량의 기록은 불러오지 않습니다. |  |
| 5 | A | `features/account/ui/DataDownloadModal.jsx:141` | 받아 둔 파일에서 지금 없는 날짜의 일지만 내 운행일지에 더합니다. 이미 있는 날짜는 건드리지 않습니다. | 백업 파일에서 현재 저장되어 있지 않은 날짜의 운행일지만 추가합니다. 이미 기록이 있는 날짜의 운행일지는 변경하지 않습니다. |  |
| 6 | A | `features/account/ui/DataDownloadModal.jsx:162` | 불러오기는 받아 둔 파일에서 지금 없는 일지, 거래처, 지출, 세금계산서만 더합니다. | 파일을 불러오면 현재 저장되어 있지 않은 운행일지, 거래처, 지출, 세금계산서만 추가합니다. |  |
| 7 | A | `features/account/ui/InviteRedeemModal.jsx:36` | 연동은 됐지만 기록 일부를 불러오지 못했습니다. | 연동은 완료했지만 일부 기록을 불러오지 못했습니다. |  |
| 8 | A | `features/account/ui/InviteRedeemModal.jsx:53` | 연동이 완료되면 기사님이 입력한 운행, 매출 내역이 차주에게 공유됩니다. | 연동이 완료되면 기사님이 입력한 운행 및 매출 내역이 차주와 공유됩니다. |  |
| 9 | A | `features/account/ui/LinkStatusChip.jsx:36` | 연동 해제는 개인정보 → 소속 연결에서 할 수 있습니다. | 개인정보 > 소속 연결에서 연동을 해제할 수 있습니다. |  |
| 10 | A | `features/account/ui/OnboardingPage.jsx:65, 79, 92` | 선택 사항이에요. | 선택 항목이에요. |  |
| 11 | A | `features/account/ui/OnboardingPage.jsx:65` | 이 항목들은 나중에 설정 화면에서 언제든 켜고 끌 수 있어요. | 나중에 설정에서 언제든 이 항목을 켜거나 끌 수 있어요. |  |
| 12 | A | `features/account/ui/OnboardingPage.jsx:78` | 수금 관리까지 하시겠어요? | 미수금도 관리하시겠어요? |  |
| 13 | A | `features/account/ui/OnboardingPage.jsx:82` | 네, 입금 안 된 것까지 챙기고 싶어요 | 네, 아직 입금되지 않은 운송료도 관리할게요 |  |
| 14 | A | `features/account/ui/OnboardingPage.jsx:83` | 아니요, 기록만 남길게요 | 아니요, 운행 기록만 관리할게요 |  |
| 15 | A | `features/account/ui/PersonalInfoPage.jsx:79` | 차주가 입력한 정보입니다 (수정 불가) | 차주가 입력한 정보입니다. 수정할 수 없습니다 | 마침표 안 붙임 |
| 16 | A | `features/calendar/ui/CalendarMonthSummary.jsx:10` | 운송료 + 파렛트 회수 청구액 − 수수료 + 부가세입니다. 아래 정비비, 주유비, 통행료는 합계에서 빼지 않은 이번 달 차량 지출 기록입니다. | 합계는 운송료와 파렛트 회수 청구액에 부가세를 더하고 수수료를 뺀 금액입니다. 아래 정비비, 주유비, 통행료는 이 합계에서 차감하지 않은 이번 달 차량 지출입니다. |  |
| 17 | A | `features/calendar/ui/CalendarMonthSummary.jsx:20` | 거래처 등록 때 적은 수수료${shown}만큼 이 거래처 운송료에서 뺍니다. | 거래처를 등록할 때 설정한 수수료${shown}를 이 거래처의 운송료에서 차감합니다. | 조사 "을(를)" → "를"(수수료에 붙음) |
| 18 | A | `features/calendar/ui/CalendarMonthSummary.jsx:31` | 차량 관리에 적은 이 차량의 수수료${shown}입니다. ${from} 계산해 이만큼 뺍니다. | 차량 관리에서 설정한 이 차량의 수수료${shown}입니다. ${from} 계산해 차감합니다. | ${from}이 "운송료 전체에서"라 수정안 "기준으로" 문장이 깨짐 — 맞춤 |
| 19 | A | `features/cars/ui/CarDriverConnectPanel.jsx:44` | 기사를 초대해 차량을 배정하세요. | 기사를 초대하고 이 차량을 배정하세요. |  |
| 20 | A | `features/cars/ui/CarDriverIncomeFields.jsx:41-42` | 4대보험 근로자는 산재보험료를 차주가 전액 부담하고, 3.3% 세금은 떼지 않습니다. / 3.3% 사업소득자는 산재보험료를 차주와 기사가 반반 부담하고, 기사 정산액에서 3.3% 세금을 뗍니다. | 4대 보험 근로자는 산재보험료를 차주가 전액 부담하며, 사업소득세 3.3%는 공제하지 않습니다. / 사업소득자(3.3%)는 산재보험료를 차주와 기사가 절반씩 부담하며, 기사 정산액에서 사업소득세 3.3%를 공제합니다. |  |
| 21 | A | `features/cars/ui/CarShareBaseField.jsx:27` | 예) 운송료 100만원, 거래처 수수료 10만원, 기사 몫 30% → 운송료 전체 30만원 / 거래처 수수료 뗀 뒤 27만원 | 예: 운송료가 100만 원이고 거래처 수수료가 10만 원일 때, 기사 몫이 운송료 전체 기준 30%이면 30만 원, 수수료 차감 후 기준 30%이면 27만 원입니다. |  |
| 22 | A | `features/clients/ui/OwnerScopedClientsView.jsx:27` | 차주와 공유하는 거래처 목록입니다. 등록, 수정, 삭제가 차주 화면에도 함께 반영됩니다. | 차주와 공유하는 거래처 목록입니다. 여기서 거래처를 등록, 수정, 삭제하면 차주 화면에도 반영됩니다. |  |
| 23 | A | `features/day-log/ui/CallDetailForm.jsx:62` | 면세 거래로 부가세가 적용되지 않습니다. | 면세 거래에는 부가가치세가 부과되지 않습니다. |  |
| 24 | A | `features/day-log/ui/CallDetailForm.jsx:67` | 거래처를 선택하면 결제 조건에 맞춰 자동 입력됩니다. | 거래처를 선택하면 결제 조건에 따라 자동으로 입력됩니다. |  |
| 25 | A | `features/day-log/ui/CallDetailForm.jsx:87` | ${Number(dateKey.slice(8, 10))}일 일지 세부 입력${value ? ' 수정' : ''} | ${Number(dateKey.slice(8, 10))}일 운행일지 세부 내역${value ? ' 수정' : ' 입력'} |  |
| 26 | A | `features/day-log/ui/CallDetailForm.jsx:90` | ↺ 바로 전 내역 그대로 채우기 | ↺ 이전 내역 불러오기 |  |
| 27 | A | `features/day-log/ui/CallDetailForm.jsx:156` | 부가세 포함 금액이면 1.1로 나눈 금액을 입력해 주세요. (예: 110,000원 → 100,000원) | 부가가치세가 포함된 금액이라면 1.1로 나눈 공급가액을 입력해 주세요. (예: 110,000원 → 100,000원) |  |
| 28 | A | `features/day-log/ui/DailyInspectionNotice.jsx:90` | 모든 항목이 비어 있어 ${month}월 ${day}일 점검표가 지워집니다. 되돌릴 수 없습니다. | 모든 항목을 비우면 ${month}월 ${day}일 점검표가 삭제됩니다. 삭제한 점검표는 복구할 수 없습니다. | sot·로드맵 문구도 같이 |
| 29 | A | `features/day-log/ui/MessageTemplateSheet.jsx:48` | 보낼 양식을 선택하면 문자 앱에서 내용을 확인하고 수정할 수 있습니다. | 문구를 선택하면 문자 앱에서 내용을 확인하고 수정할 수 있습니다. |  |
| 30 | A | `features/day-log/ui/callDetailFormHelpers.js:94` | 입력한 운송료: ${fare}원. 이 금액이 맞으면 저장해 주세요. | 입력한 운송료는 ${fare}원입니다. 이 금액이 맞으면 저장해 주세요. |  |
| 31 | A | `features/documents/ui/DailyInspectionExportBar.jsx:45` | ※ PDF나 이미지로 내보내면 선택한 달의 전체 점검표가 법정 서식으로 저장됩니다. | ※ 선택한 달의 점검표 전체를 법정 서식의 PDF 또는 이미지 파일로 저장합니다. |  |
| 32 | A | `features/documents/ui/TaxInvoiceTab.jsx:34-38` | 1. 엑셀 저장 후 발급하러 가기를 누르세요. 2. 홈택스에 사업자로 로그인해 주세요. 3. [일괄작성 파일]에서 [파일 선택]을 눌러 앱에서 받은 엑셀을 올려 주세요. 4. [엑셀파일 변환하기] 버튼을 눌러주세요. 5. [일괄 발급] 버튼을 눌러주세요. | 1. 엑셀 파일을 저장한 뒤 [발급하러 가기]를 누르세요. 2. 홈택스에 사업자 계정으로 로그인하세요. 3. [일괄작성 파일]에서 [파일 선택]을 눌러 저장한 엑셀 파일을 선택하세요. 4. [엑셀파일 변환하기]를 누르세요. 5. [일괄 발급]을 누르세요. |  |
| 33 | A | `features/drivers/ui/DriverConnectionPage.jsx:116-117` | 연동 상태와 차량 계약기간을 관리합니다. 기사 초대는 로그인 후 쓸 수 있습니다. 로그인하면 배정 차량과 시작일을 넣어 초대할 수 있습니다. | 기사 연동 상태와 차량 계약 기간을 관리합니다. 기사를 초대하려면 로그인한 뒤 배정 차량과 계약 시작일을 입력하세요. |  |
| 34 | A | `features/drivers/ui/DriverConnectionPage.jsx:146` | ~ 종료일 없음 | ~ 종료일 미정 |  |
| 35 | A | `features/drivers/ui/DriverFormModal.jsx:106-107` | 초대 코드는 1회만 사용할 수 있습니다. 기존 연동을 해제하고 다시 연결할 경우, 새 코드를 발급해 주세요. | 초대 코드는 한 번만 사용할 수 있습니다. 연동을 해제한 뒤 다시 연결하려면 새 초대 코드를 발급해 주세요. |  |
| 36 | A | `features/drivers/ui/LinkedDriverClientsPage.jsx:43-44` | ${ctx.car?.driverName \|\| getShortCarNum(ctx.plate)} 거래처 / ${link?.driverName \|\| '기사'} 기사 거래처 | ${ctx.car?.driverName \|\| getShortCarNum(ctx.plate)} 담당 거래처 / ${link?.driverName \|\| '기사'}님의 거래처 | sot·로드맵 문구도 같이 |
| 37 | A | `features/cars/ui/CarFormModal.jsx`(수정안의 `ExpenseFormModal.jsx:136-137`) | 건당(또는 월) 고정으로 기사에게 지급할 금액을 설정합니다. / 이 차량 매출 중 기사에게 지급할 비율(%)을 설정합니다. | 기사에게 건별 또는 월별로 지급할 고정 금액을 설정합니다. / 이 차량의 매출 중 기사에게 지급할 비율을 설정합니다. |  |
| 38 | B | `features/home/api/unbiseoQuestion.ts:11` | 달이나 연으로 물어봐 주세요. 예: 이번 달 매출 얼마야 | 월별 또는 연도별로 물어봐 주세요. 예: 이번 달 매출은 얼마야? |  |
| 39 | B | `features/home/api/unbiseoQuestion.ts:12` | 아직 답할 수 없는 질문이에요. 매출·순이익·지출·미수금·운행 횟수를 물어봐 주세요. | 아직 답변할 수 없는 질문이에요. 매출, 순이익, 지출, 미수금 또는 운행 횟수를 물어봐 주세요. |  |
| 40 | B | `features/home/api/unbiseoQuestion.ts:14-15` | 순이익은 달이나 연으로 물어봐 주세요. / 거래처별 매출은 달이나 연으로 물어봐 주세요. | 순이익은 월별 또는 연도별로 물어봐 주세요. / 거래처별 매출은 월별 또는 연도별로 물어봐 주세요. |  |
| 41 | B | `features/home/api/unbiseoQuestion.ts:88` | ${head}남은 미수금은 ${formatWon(amount)}입니다(${withSub ? '기사 차량 포함, ' : ''}${count}건). | ${head}남은 미수금은 ${formatWon(amount)}입니다. ${withSub ? '기사 차량을 포함해 ' : ''}${count}건입니다. |  |
| 42 | B | `features/home/api/unbiseoQuestion.ts:92` | ${question.client} ${label} 매출은 공급가액 ${formatWon(supply)}, 부가세 포함 ${formatWon(amount)}입니다. | ${question.client}의 ${label} 매출은 공급가액 기준 ${formatWon(supply)}이며, 부가가치세를 포함하면 ${formatWon(amount)}입니다. |  |
| 43 | B | `features/home/api/unbiseoQuestion.ts:97-98` | ${label} ${isDriver ? '정산액은' : '순이익은'} ${formatWon(amount)}입니다. / ${label} ${isDriver ? '운송 수입(정산 전)은' : '매출은'} ${formatWon(amount)}입니다. | ${label} ${isDriver ? '정산액은' : '순이익은'} ${formatWon(amount)}입니다. / ${label} ${isDriver ? '정산 전 운송 수입은' : '매출은'} ${formatWon(amount)}입니다. |  |
| 44 | B | `features/home/api/unbiseoWeather.ts:15-17` | 지난 날씨는 알려 드리지 않아요. 모레까지만 알려 드릴 수 있어요. 위치를 허용하거나 지역을 말해 주세요. | 과거 날씨는 안내하지 않아요. 날씨는 모레까지 확인할 수 있어요. 위치 정보 사용을 허용하거나 지역명을 말해 주세요. |  |
| 45 | B | `features/home/api/unbiseoWeather.ts:84` | ${withParticle(weather.place, '은', '는')} 찾지 못했어요. 시나 구 이름으로 말해 주세요. | ${weather.place} 지역을 찾지 못했어요. 시, 군, 구 이름을 말해 주세요. | sot·로드맵 문구도 같이 |
| 46 | B | `features/home/ui/HomeAssistantBar.jsx, UnbiseoDock.tsx` | Ai 운비서 | AI 운비서 | sot·로드맵 문구도 같이 |
| 47 | B | `features/notifications/api/notifications.js:61` | ${Number(dateKey.slice(5, 7))}월 ${Number(dateKey.slice(8, 10))}일 운행일지${car} 저장이 실패했습니다. | ${Number(dateKey.slice(5, 7))}월 ${Number(dateKey.slice(8, 10))}일 운행일지${car}를 저장하지 못했습니다. | sot·로드맵 문구도 같이; 조사 "을(를)" → "를"(운행일지에 붙음) |
| 48 | B | `features/notifications/api/notifications.js:123-124` | 오늘 운행일지가 비어 있습니다. 홈에서 [운행 기록하기]를 눌러 일지나 휴무를 남겨 주세요. | 오늘 작성한 운행일지가 없습니다. 홈에서 [운행 기록하기]를 눌러 운행 기록을 추가하거나 휴무를 등록해 주세요. |  |
| 49 | B | `features/notifications/api/notifications.js:136-137` | 일상점검표가 의무화되었습니다. 관련 법령에 따라 출발 전 일상점검표를 작성해야 합니다. [바로 사용하기]를 누르면 켜집니다. | 출발 전 일상점검표 작성이 의무화되었습니다. 관련 법령에 따라 운행을 시작하기 전에 점검표를 작성해야 합니다. [바로 사용하기]를 눌러 기능을 켜세요. |  |
| 50 | B | `features/receivables/hooks/useReceivablesActions.js:96-97` | ${clientName} ${month}월분 입금 완료 처리 / 미수 ${items.length}건, ${formatWon(amount)}을 모두 입금 완료로 바꿉니다. 되돌리기는 한 건씩만 할 수 있습니다. | ${clientName}의 ${month}월 미수금을 모두 입금 완료로 처리할까요? 미수 ${items.length}건, ${formatWon(amount)}입니다. 취소하려면 각 건을 하나씩 처리해야 합니다. |  |
| 51 | B | `features/receivables/ui/ReceivableItemCard.jsx:82` | ${formatWon(item.paidAmount)} 입금, ${formatWon(item.remainingAmount)} 남음 | ${formatWon(item.paidAmount)} 입금 / ${formatWon(item.remainingAmount)} 미수 |  |
| 52 | B | `features/receivables/ui/ReceivablesListPage.jsx:118` | D-3 이내 또는 연체된 미수금이 없습니다. | 입금 예정일이 3일 이내로 다가왔거나 이미 지난 미수금이 없습니다. |  |
| 53 | B | `features/revenue/ui/OwnerMonthlyCards.jsx:86` | 당월 부가세(공급가액 기준 10%) | 이번 달 부가가치세(공급가액의 10%) |  |
| 54 | B | `features/revenue/ui/OwnerMonthlyCards.jsx:120` | 운송 수입 합계(정산 전) | 정산 전 운송 수입 합계 | sot·로드맵 문구도 같이 |
| 55 | B | `features/revenue/ui/OwnerMonthlyCards.jsx:113` | 당월 유가보조금 환급 | 이번 달 유가보조금 환급액 |  |
| 56 | A | `features/settings/ui/AppSettingsBackupSection.jsx:94` | 운행일지, 기사 연동 기록, 세금계산서와 설정을 함께 보관하고 복원합니다. | 운행일지, 기사 연동 기록, 세금계산서, 앱 설정을 함께 백업하고 복원합니다. |  |
| 57 | A | `features/settings/ui/AppSettingsPage.jsx:143` | 고정 노선을 끄면 세부 입력이 필수로 켜집니다. | 고정 노선을 끄면 세부 입력이 자동으로 필수 설정됩니다. |  |
| 58 | A | `features/support/ui/CustomerCenterPage.jsx:28` | "나의 문의/건의 확인" 탭에서 접수 내역과 답변 상태를 확인하실 수 있습니다. | [나의 문의/건의 확인]에서 문의 접수 내역과 답변 상태를 확인할 수 있습니다. |  |
| 59 | A | `features/support/ui/InquiryForm.jsx:59` | 문의 내용을 자세히 적어주세요 | 문의 내용을 자세히 입력해 주세요 | 마침표 안 붙임 |
| 60 | B | `features/notifications/ui/NotificationsPage.jsx:35` | 오늘 운행일지 비어 있음 | 오늘 작성한 운행일지 없음 |  |
| 61 | B | `features/receivables/ui/ReceivablesListPage.jsx:107` | 입금완료 | 입금 완료 |  |
| 62 | A | `features/account/ui/PersonalInfoPage.jsx:151` | 로그인하러 가기 | 로그인하기 |  |
| 63 | B | `features/home/api/unbiseoParse.ts:34` | 말이 너무 길거나 비었어요. 300자까지 다시 말해 주세요. | 입력 내용이 너무 길거나 비어 있어요. 300자 이내로 다시 입력해 주세요. |  |
| 64 | B | `features/home/api/unbiseoParse.ts:35` | 알아듣지 못했습니다. 다시 말해 주세요. | 내용을 이해하지 못했어요. 다시 말씀해 주세요. |  |
| 65 | B | `features/home/api/unbiseoSentence.ts:91` | 운행 기록으로 알아듣지 못했어요. 다시 말해 주세요. | 운행 기록 요청으로 이해하지 못했어요. 다시 말씀해 주세요. |  |
| 66 | B | `features/home/hooks/useSpeechInput.ts:18` | 마이크 권한을 켜 주세요. 글로 써도 됩니다. | 마이크 사용을 허용해 주세요. 음성 대신 직접 입력해도 됩니다. |  |
| 67 | B | `features/home/hooks/useSpeechInput.ts:19` | 말소리가 들리지 않았어요. 다시 눌러 말해 주세요. | 음성을 인식하지 못했어요. 다시 눌러 말씀해 주세요. |  |
| 68 | B | `features/home/hooks/useSpeechInput.ts:20` | 받아쓰기를 하지 못했어요. 글로 써 주세요. | 음성을 글자로 바꾸지 못했어요. 직접 입력해 주세요. |  |
| 69 | B | `features/home/ui/HomeTodoCard.jsx:23` | 오늘 일상점검 안 함 | 오늘 일상점검표 미작성 | sot·로드맵 문구도 같이 |
| 70 | B | `features/receivables/ui/ReceivablesListPage.jsx:48` | 월별 묶음 정산 | 월별 미수금 정산 |  |
| 71 | A | `features/settings/ui/MessageSettingsPage.jsx:81, 91, 101` | 자동 입력: {거래처}, {운행구간}, {운송료} | 자동으로 입력되는 항목: {거래처}, {운행구간}, {운송료} |  |
| 72 | A | `features/settings/ui/RoutePresetEditor.jsx:48` | 부산→대구처럼 항상 같은 구간만 오간다면, 노선을 등록해 두고 일일운행에서 한 번 눌러 횟수를 기록하세요. | 부산→대구처럼 같은 구간을 자주 운행한다면 노선으로 등록해 두세요. 운행일지에서 해당 노선을 누르면 운행 횟수가 기록됩니다. |  |
| 73 | A | `features/drivers/ui/EmployerLinkCard.jsx:16` | 연동은 해제됐지만 내 기록 일부를 불러오지 못했습니다. | 연동을 해제했지만 일부 기록을 불러오지 못했습니다. |  |
| 74 | A | `features/drivers/ui/SettlementSummaryCard.jsx:69` | 최종 실수령 정산액 | 최종 실수령액 |  |
| 75 | A | `features/documents/ui/DailyInspectionShareModal.jsx:17` | 파일을 저장하고 안내 문구로 문자 앱을 엽니다. 받는 사람은 직접 골라 주세요. | 파일을 저장한 뒤 문자 앱을 엽니다. 받는 사람을 직접 선택해 주세요. |  |
| 76 | A | `features/documents/ui/ShareFileModal.jsx:80` | 보내는 방법이나 파일 형식을 선택해 주세요. | 전송 방법과 파일 형식을 선택해 주세요. |  |
| 77 | A | `features/account/ui/WelcomeProfileView.jsx:56` | 서비스 이용 및 본인 확인을 위해 실명이 필요합니다. | 서비스 이용과 본인 확인을 위해 실명을 입력해 주세요. |  |
| 78 | B | `domain/clientPaymentTerms.ts:5-10` | 당일/수시 정산, 운행 건별 정산 (N일 후), 익월 지정일 정산, 익월 말일 정산, 익익월 지정일 정산, 익익월 말일 정산 | 당일 또는 수시 정산 / 운행일로부터 N일 후 정산 / 다음 달 지정일 정산 / 다음 달 말일 정산 / 다다음 달 지정일 정산 / 다다음 달 말일 정산 | 정산 문구 함수(getPaymentTermLabel)의 "익월 N일 정산" 등도 같이: 다음 달 N일 / 다다음 달 N일 / 운행일로부터 N일 후 정산 |
| 79 | A | `features/day-log/ui/FixedCountSection.jsx:58` | 운행 횟수 빠른 선택 | 운행 횟수 선택 |  |
| 80 | A | `features/day-log/ui/FixedRouteChips.jsx:19` | 자주 다니는 노선 (누르면 1회 추가) | 자주 운행하는 노선 (누르면 운행 횟수 1회 추가) |  |
| 81 | A | `features/day-log/ui/CallDetailList.jsx:74` | + 운행일지 추가 | + 세부 내역 추가 |  |
| 82 | A | `features/account/ui/PersonalInfoPage.jsx:132` | 대표자/연락처 / 대표자 기본 정보 | 대표자 및 연락처 / 대표자 정보 |  |
| 83 | A | `features/clients/ui/ClientFormModal.jsx:68` | 이름 (담당자) | 담당자 이름 |  |
| 84 | A | `features/account/ui/PersonalInfoPage.jsx:82, 83` | 사업자명 (상호) / 사업자명을 입력하세요 | 상호(사업자명) / 상호를 입력해 주세요 | 마침표 안 붙임 |
| 85 | A | `features/day-log/ui/CallDetailForm.jsx:188-189` | 비고 / 특이사항 입력 | 메모 / 특이 사항을 입력해 주세요 | 마침표 안 붙임 |
| 86 | A | `features/expenses/ui/ExpenseFormModal.jsx:90, 135` | 항목명을 입력하세요 / 비용을 입력하세요 | 항목명을 입력해 주세요 / 비용을 입력해 주세요 | 마침표 안 붙임 |
| 87 | B | `features/receivables/ui/ReceivableItemCard.jsx:64` | 운행 구간 미등록 | 운행 구간이 등록되지 않았습니다 | 마침표 안 붙임 |
| 88 | A | `features/settings/ui/AppSettingsBackupSection.jsx:92` | 데이터 관리 (백업 / 복구) | 데이터 관리(백업/복원) |  |
| 89 | A | `features/account/ui/PersonalInfoPage.jsx:79` | 세금계산서와 서류 출력에 사용되는 정보입니다. | 세금계산서와 각종 서류를 발급할 때 사용하는 정보입니다. |  |
| 90 | A | `features/cars/ui/CarListPage.jsx:29` | 연동 중인 기사에게는 해제 요청이 가고, 기사가 동의하거나 3일이 지나면 해제됩니다. | 연동 중인 기사에게 연동 해제 요청이 전달됩니다. 기사가 동의하거나 3일이 지나면 연동이 해제됩니다. |  |
| 91 | A | `features/drivers/ui/DriverUnlinkControls.jsx:19` | (${due} 자동 해제) | (${due}에 자동으로 연동 해제) | sot·로드맵 문구도 같이 |
| 92 | B | `features/receivables/ui/ReceivableItemCard.jsx:77` | 입금 예정일 미등록 | 입금 예정일이 등록되지 않았습니다 | 마침표 안 붙임 |
| 93 | B | `features/receivables/ui/ReceivablesDetailPage.jsx:63` | 입금 예정일 미등록 | 입금 예정일이 등록되지 않았습니다 | 마침표 안 붙임 |
| 94 | B | `features/home/api/unbiseoSms.ts:79-82` | ${monthLabel(pick.month, today)} ${picked.length}건 / 전체 ${picked.length}건 / ${first} 외 ${picked.length - 1}건 | ${monthLabel(pick.month, today)} 미수금 ${picked.length}건 / 전체 미수금 ${picked.length}건 / ${first} 외 ${picked.length - 1}건의 미수금 | **원문 유지** — 화면 버튼이 아니라 거래처 문자 {운행구간} 자리에 들어가는 말이라 "미수금 3건 … 미수 상태"로 겹침 `[확인: 2026-10-25 보리 — "가"]` |
| 95 | B | `features/home/ui/HomeAssistantPanel.tsx:42` | 고정 노선이 꺼져 있어요. 켤까요? / 콜 상세가 꺼져 있어요. 켤까요? | 고정 노선 기능이 꺼져 있어요. 켤까요? / 운행 세부 입력 기능이 꺼져 있어요. 켤까요? | sot·로드맵 문구도 같이 |
| 96 | A | `features/account/ui/PersonalInfoPage.jsx:19-20` | 모든 운행 기록, 거래처, 정산 데이터가 영구적으로 삭제되며 복구할 수 없습니다. / 탈퇴하면 되돌릴 수 없습니다. 마지막으로 한 번 더 확인해 주세요. | 모든 운행 기록, 거래처 정보, 정산 데이터가 영구 삭제되며 복구할 수 없습니다. / 탈퇴 후에는 계정을 복구할 수 없습니다. 계속 진행할까요? |  |
| 97 | A | `features/support/ui/AdminInquiriesPage.tsx:35, features/support/ui/MyInquiriesList.jsx:36` | 문의 목록 불러오는 중 | 문의 목록을 불러오는 중… |  |
| 98 | A | `features/day-log/ui/DailyInspectionNotice.jsx:119` | 일상점검표 작성이 필요합니다. | 일상점검표를 작성해 주세요. | sot·로드맵 문구도 같이 |
| 99 | A | `features/documents/ui/ReportSummaryContent.jsx:59` | 해당 월의 운송 내역이 없습니다. | 선택한 달의 운송 내역이 없습니다. |  |
| 100 | A | `features/account/ui/WelcomeProfileView.jsx:42` | 이름과 전화번호를 입력해 주세요. | 이름과 휴대전화 번호를 입력해 주세요. |  |
| 101 | B | `features/home/api/unbiseoWeather.ts:71` | ${day.min}~${day.max}도 | 최저 ${day.min}도, 최고 ${day.max}도 |  |
| 102 | A | `features/day-log/ui/ExpenseGroups.jsx:61` | ${kindItem.label} 합계 | ${kindItem.label} 지출 합계 |  |
| 103 | A | `features/account/ui/AuthIntroView.jsx:37` | Google 계정으로 로그인 | Google 계정으로 로그인하기 |  |
| 104 | A | `features/settings/ui/RoutePresetEditor.jsx:55` | ${route.loadLoc} → ${route.unloadLoc} 삭제 | ${route.loadLoc} → ${route.unloadLoc} 노선 삭제 |  |
| 105 | A | `features/account/ui/PersonalInfoPage.jsx:181` | 다음 | 다음 단계 |  |
