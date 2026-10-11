# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 4-E-2 — 이력형 주석 줄이기: `lib/` 나머지 (4번 마지막) `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` C. 앞 슬라이스 4-E-1 `[x]`(react-app `0bc69f8`). 4번(이력형 주석)의 마지막 슬라이스 — 끝나면 운영 코드 전체 정리 완료.

### 쉬운 요약
- `lib/`에서 남은 27개 파일의 주석에서 "Step 4 도메인 폴더 이동", "슬라이스 A (보리 승인, 2026-08-31)", "로드맵 18-B" 같은 작업 기록을 뺀다.
- 주석만 바뀌어 동작은 그대로다. 이걸로 앱 코드 전체에서 작업 기록형 주석 정리가 끝난다.

### 현재 상태 (코드 확인)
- 27개 파일에 작업 기록 주석(근거: 4번 공통 검색 패턴). 옛 경로용 재수출 파일 5개(`calendar`·`finance`·`formatPhone`·`money`·`receivables`)는 똑같은 3줄 경위.
- 지금과 다른 설명: `clientMutations.js:5` "LS·dirty·syncAll 없이"(syncAll은 이제 없음, 근거: `syncQueue.js:3` "syncAll은 쓰지 않는다"), `requestDriverInviteSave.js:11` "세션 무효화(AGENTS §9)"(AGENTS §9는 DB 변경 규칙 — 세션 규칙은 `docs/testing-playbook.md` §5).

### 목표 상태 (기대 동작)
1. 아래 "부록 — 바뀔 주석 최종 문구"대로 27개 파일 주석이 바뀐다.
2. §6 예외 이유(`taxInvoiceExcelBuilder.js:3`)와 법정 서식·점검표 설명은 내용 유지.
3. 실행 코드·타입 설명 변경 0. 동작 변화 없음.

### 건드릴 파일 (27개, `src/lib/`)
`calendar`, `callDetailSchema`, `cars`, `chunkReload`, `clientMutations`, `clients`, `dailyInspections`, `driverLinkRpc`, `driverUnlink`, `drivers`, `expenses`, `finance`, `formatPhone`, `guestBackup`, `haptic`, `invoices`, `mainDayLogRouting`, `money`, `ownerFinance`, `practiceSettings`, `receivables`, `registerServiceWorker`, `requestDriverInviteSave`, `supportInquiryMutations`, `taxInvoiceExcelBuilder`, `vehicleDeletion`, `vehicleMutations` (`.js`)
- 주석만 고쳐 묶음(앞 슬라이스와 같은 판단). 공용 스타일·값 변경 없음.

### 안 건드릴 것
- 재수출 파일 5개의 `// @ts-check` 없음 — 이번 범위 아님(점검 문서 F에 기록됨). 테스트 파일.

### 검증
- 27개 파일 모두 HEAD 판과 작업 판을 `esbuild --minify-whitespace --minify-syntax`(주석·공백만 지움, 이름 유지)로 비교해 글자 하나까지 같은지 확인.
- `typecheck` 오류 0, 엄격 검사 목록 438 이하, `npm test`·`build` 통과. 마지막에 운영 코드 전체에서 작업 기록 낱말 검색이 0곳인지 확인.

### 파일 길이
- 27개 모두 줄어들거나 같음. 200줄 넘는 `taxInvoiceExcelBuilder.js` 251은 기존 §6 예외(늘지 않음).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 실패하면 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법 (휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
1. 거래처 관리에서 거래처 1곳 추가 → 껐다 켜도 그대로면 성공.
2. 차량 관리에서 차량 정보 1곳 고치기 → 껐다 켜도 바뀐 값이면 성공.
3. 일지에서 일상점검표 열기·저장 → 다시 열었을 때 그대로면 성공.

### 진행 결과
- 부록 표대로 27개 파일 주석 정리(36줄 추가·86줄 삭제), 못 찾은 문구 0.
- **실행 코드 같음 확인**: 27개 파일 모두 HEAD 판과 비교(주석·공백만 제거, 이름 유지) 글자 하나까지 같음.
- 로컬 `npm test` 전부 통과(단위 832 / 화면 365), `typecheck` 오류 0, 엄격 검사 목록 438 그대로, `build` 성공.
- **운영 코드 전체 검색에서 범위 밖 놓친 곳 2곳 발견**(4-D 검색 방식이 `/** … */` 한 줄 주석·영어 "slice"를 못 잡음): `components/SideMenu.jsx:14` "(로드맵 15번)", `components/cars/CarDriverConnectPanel.jsx:7` "(slice F mockup)" — 이번 범위 밖이라 안 건드림, 보리 결정(2026-10-11) "추천대로" — 4-F 작은 슬라이스로 따로.
- react-app `ce6e118`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-11).

---

### 부록 — 바뀔 주석 최종 문구
"앞 낱말만 삭제"는 줄 맨 앞(또는 괄호 속)의 기록 낱말만 빼고 나머지는 그대로라는 뜻.

| 위치 | 바뀔 문구(최종) |
|---|---|
| `calendar.js`·`finance.js`·`formatPhone.js`·`money.js`·`receivables.js` 1~3줄 | `// 옛 경로(../lib/<파일>.js)를 쓰는 화면을 위한 domain/<파일>.js 재수출 — 모두 domain/ 경로로 바꾸면 지울 수 있다.` |
| `callDetailSchema.js:69~78` | ` * payments[] 항목은 도메인 타입(callDetail.js)대로 전부 선택 필드 — id 없는 옛 항목·금액이 통화 글자("1,000")인 값도 실제로 있고 정상 계산된다.` / ` * 있는 필드만 타입을 검사하고, 없는 필드는 통과시킨다.` |
| `cars.js`·`clients.js`·`invoices.js` 3~5줄 | `// 차량(거래처·계산서) localStorage I/O + domain/<파일>.js 재수출 배럴.` (파일마다 이름만 다름) |
| `chunkReload.js:2` | 앞 "로드맵 33:" 삭제 |
| `clientMutations.js:2·5~6` | "Step 7:" 삭제 / `// 로그인이면 서버 직접 1회(바뀐 행만) 후 Store(메모리)만 바꾼다. 게스트는 commitOrToast로 로컬 저장.` |
| `clientMutations.js:75` | "(로드맵 6)" 삭제 |
| `dailyInspections.js:3` | `// 일상점검표 서버 읽기·저장(daily_inspections 표, 차량+날짜 1장). 로컬 보관·재시도 큐 없음 — 일지를 열 때 읽고 저장은 바로 서버.` |
| `dailyInspections.js:108` | ` * 탭 숨김 판단: 그 차량 점검표가 1장이라도 있는지.` |
| `driverLinkRpc.js:2~7` | `// 로그인 사용자의 기사 초대 저장을 upsert_driver_link_idempotent RPC 1회로 끝낸다(outbox·재시도 큐 없음) — 그 RPC 호출과,` / `// RPC no-op update로는 못 바꾸는 기존 행 필드 보정만 담는다. 같은 차량번호 1명 규칙은 domain/drivers.js upsertDriver가 저장 전에 본다.` |
| `driverLinkRpc.js:99` | "(로드맵 19: 실패를" → "(실패를" |
| `driverUnlink.js:2` | 앞 "로드맵 7-C:" 삭제 |
| `drivers.js:3~5` | `// 기사 persist 배럴(loadDrivers)과 saveDrivers→commitDrivers + domain/drivers.js 재수출. 화면 읽기는 useOwnerDrivers / readOwnerDrivers.` |
| `expenses.js:2~5` | `// 지출 저장(saveExpenses) + domain/expenses.js 재수출 배럴. 화면 읽기는 useOwnerExpenses.` |
| `expenses.js:34` | "(로드맵 5-B-1)" 삭제 |
| `guestBackup.js:96` | `  // 중첩 구조 및 타입 런타임 검증` ("(AGENTS §6)" 삭제) |
| `haptic.js:2` | 앞 "로드맵 18-B:" 삭제 |
| `mainDayLogRouting.js:2~5` | `// "이 일지 저장이 클라우드 직접 저장 경로인가"를 logId(메인·기사 차량번호)별로 한 곳에서 판정한다(useDayDraft·dayLogCloudCommit·pendingWriteRetryListeners 공유).` / `// 무거운 supabase 실행기를 끌어오지 않도록 app-store·cloudSession만 의존한다.` |
| `ownerFinance.js:2~3` | 2줄 삭제(`// @ts-check`만 남음) |
| `practiceSettings.js:2~5` | `// 앱 설정 localStorage I/O와 화면 테마 적용(applyTheme) + domain/practiceSettings.js 재수출 배럴.` |
| `registerServiceWorker.js:2` | 앞 "로드맵 18-E:" 삭제 |
| `requestDriverInviteSave.js:2~13` | `// 로그인 사용자의 기사 초대 생성·수정을 outbox·재시도 큐 없이 upsert_driver_link_idempotent RPC 1회로 저장한다 — 성공하면 Store 갱신,` / `// 실패하면 토스트만 띄우고 중단. 게스트·불완전 입력은 로컬만. 같은 차량번호 1명 규칙은 upsertDriver가 저장 전에 본다.` / `// 세션은 RPC 전에 캡처하고 각 await 직후·Store 반영 직전에 재검증한다 — 바뀌었으면 부작용 없이 조용히 중단.` |
| `requestDriverInviteSave.js:54` | 앞 "사용자 지시 2번:" 삭제 |
| `supportInquiryMutations.js:3` | 앞 "Step 10 5-2/5-3:" 삭제 |
| `taxInvoiceExcelBuilder.js:3` | `// 세금계산서 엑셀 양식 — 셀 주소·병합이 한 덩어리라 §6 응집도 우선(~250+ 허용).` |
| `vehicleDeletion.js:2` | 앞 "슬라이스 C(2026-09-01):" 삭제 |
| `vehicleMutations.js:2` | 앞 "Step 7:" 삭제 |
