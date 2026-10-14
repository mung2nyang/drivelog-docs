# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 64 미수금 화면에서 기사 차량 운행 입금 처리가 서버에 안 저장됨 `[x]`

> 진행(2026-10-14): 코드 커밋 react-app `9478746`. 테스트 1,273개 통과, 타입 검사 통과, 빌드 성공. 엄격 검사 경고 457개(늘지 않음). 새 테스트 H-1~H-3은 고치기 전 코드로 되돌리면 실패함을 확인. 개발 서버에서 시험 계정 기사 차량 22가2222에 "64번시험" 운행(10,000원)을 만들어 입금 완료 → 서버에 "수금 완료"로 저장됨, 새로고침 뒤 미수금 목록에서 빠지고 일지에도 "수금"으로 보임(시험 운행은 보리 결정으로 그대로 둠). 보리 push, CI 초록(verify·deploy), 최종 승인(2026-10-14).

보리 지시(2026-10-14, "64번부터"). 근거: 코드 리뷰(2026-10-14) → `docs/roadmap.md` 64번. 8-B(미수 쓰기 창구 통일)가 메인 차량만 서버로 보내던 빈틈을 메운다. 저장 경로라 `docs/testing-playbook.md` 읽음.

### 쉬운 요약
- 로그인 차주가 미수금 화면에서 **기사 차량** 운행을 입금 완료·부분 입금·입금 취소·한 달치 입금 완료 하면, 지금은 화면에서만 바뀌고 서버에는 안 올라가서 앱을 다시 켜면 미수로 돌아온다.
- 일지 화면이 이미 쓰는 서버 저장 길을 미수금 화면에서도 기사 차량에 똑같이 쓰게 한다.
- 끝나면 기사 차량 입금 처리도 다시 켰을 때 그대로 남는다. 실패하면 지금 메인 차량처럼 "저장에 실패했습니다"가 뜨고 화면은 바뀌지 않는다.

### 현재 상태 (코드 확인)
- `lib/ownerFinance.js:55-94` `persistReceivableWorkDataChange`: 메인(`main`)만 바뀐 날짜를 `commitMainDayLogToCloud`/`commitMainDayLogMapToCloud`로 서버에 쓴다.
- 서브 차량은 `persistSubLogChanges`(`ownerFinance.js:39-47`) → `commitLogWorkData`. 로그인 계정이면 메모리만 바꾸고 서버·기기 저장소 모두 안 씀(`store/commitHelpers.js:44-55`, `cloud`면 `persist: false`).
- 서버 저장 함수는 이미 서브 차량을 지원한다: `commitMainDayLogToCloud`·`commitMainDayLogMapToCloud`가 `vehicleSupabaseIdForLog(ownerKey, logId)`로 차량번호별 서버 차량을 찾음(`lib/mainDayLogRouting.js:14-23`). 일지 화면은 서브 차량도 이 길로 저장(`useDayDraft.js:124-125`).
- 미수금 목록에 서브 차량 운행이 나오는 근거: `buildFinanceSettings`가 `subPaymentOn: true`(`ownerFinance.js:104`) → `getReceivableItems`가 서브 차량 소스 추가(`domain/financeReceivables.js:45-49`).
- 테스트에 서브 차량 경우 없음(`receivablesCloudCommit.test.js` A~G 전부 `logId: 'main'`).

### 목표 상태 (기대 동작)
1. 로그인 차주가 미수금 화면에서 서버에 있는 기사 차량 운행을 입금 완료·부분 입금·입금 취소하면, 그 날짜가 그 차량의 서버 일지(`daily_logs`+`transport_details`)에 저장되고 성공한 뒤에만 화면(Store)이 바뀐다.
2. 한 달치 입금 완료에 메인·기사 차량 운행이 섞여 있으면 차량마다 서버에 저장한다. 순서는 메인 → 기사 차량(목록 순).
3. 중간에 실패하면 거기서 멈추고, 이미 저장된 차량·날짜만 화면에 반영, 나머지는 그대로 두고 지금과 같은 안내(전부 실패 "저장에 실패했습니다…", 일부 성공 "일부만 저장되었습니다…", 세션 바뀜 "로그인 정보가 바뀌어…")를 띄운다.
4. 비회원·서버에 아직 없는 차량(`supabaseId` 없음)은 지금처럼 기기에만 저장(동작 변화 없음).
5. 메인 차량만 바뀐 경우의 동작·안내·서버 호출 횟수는 지금과 같다(기존 테스트 A~G 그대로 통과).

### 건드릴 파일
- `react-app/src/lib/ownerFinance.js` — `persistReceivableWorkDataChange`를 "바뀐 날짜가 있는 차량(main + 서브)마다 같은 판단: 서버 차량이면 서버 저장, 아니면 기기 저장"으로 바꾸고, `persistSubLogChanges`는 이 반복으로 흡수. 넣을 주석(최종 문구): `// 차량마다(메인 → 기사 차량) 서버에 있으면 서버 저장, 없으면 기기 저장. 실패한 차량에서 멈춘다.`
- `react-app/src/lib/receivablesCloudCommit.test.js` — 기사 차량 시나리오 테스트 추가(아래 검증).
- 공용 값 변경 없음(함수 이름·돌려주는 모양 `{ ok, partial, toast }` 그대로 — 호출부 `useReceivablesActions.js:47,102` 2곳만, `grep persistReceivableWorkDataChange` 확인).

### 안 건드릴 것
- `lib/dayLogCloudCommit.js` — 이미 `logId`별 차량을 고름(`vehicleSupabaseIdForLog` 사용, 위 근거). 지우고 넣는 사이 끊김 문제는 69번 몫.
- `store/commitHelpers.js` `commitLogWorkData` — 서버 성공 뒤 메모리 반영용으로 그대로 씀.
- 미수 계산(`domain/financeReceivables.js`)·화면(`components/receivables/*`) — 저장 길만 바뀜.
- 서버(DB)·권한 — 변경 없음. 차주가 기사 차량 일지를 서버에 쓰는 것은 일지 화면이 이미 하는 일(`useDayDraft.js:124`).

### 검증
- 자동: `npm test`(CI가 재실행), `npm run typecheck`·`build`.
- 새 테스트(`receivablesCloudCommit.test.js`, 가짜 서버):
  - H-1 기사 차량 운행 입금 완료 → 그 차량 번호(`vehicle_id`)로 `daily_logs` upsert 1회·`transport_details` 교체, 성공 뒤 Store 서브 일지에 입금 반영.
  - H-2 기사 차량 서버 실패 → Store 서브 일지 그대로, "저장에 실패했습니다…".
  - H-3 한 달치에 메인+기사 차량 → 둘 다 서버 저장 / 메인 성공·기사 실패 → `partial: true`, 메인만 반영.
  - H-4 비회원 기사 차량 → 서버 호출 0회, 기기 저장(지금 동작 유지).
  - 진실성: 고치기 전 코드로 되돌려 H-1·H-3이 실패하는 것 확인(플레이북 §6).
- 브라우저: 개발 서버에서 실제 데이터는 건드리지 않게, 시험 계정·시험 기사 차량 운행으로만(아래 사용자 확인).

### 파일 길이
- `lib/ownerFinance.js` 161줄 → 약 170줄(200줄 이하).
- `receivablesCloudCommit.test.js` 253줄 → 약 330줄(테스트 파일 예외).

### 실패 시 처리
- 새 저장 장치·재시도 큐·임시 보관 없음(AGENTS §7). 서버 실패는 지금 메인 차량과 같은 Fail-Fast 안내만.
- 검증이 실패하면 코드를 멈추고 이 파일에 수정 착수지시서를 써서 다시 승인받는다.

### 사용자 확인 방법
1. 차주 시험 계정으로 로그인 → 기사 차량(시험용) 운행 하나를 운송료 넣고 "미수"로 저장.
2. 미수금 화면 → 그 거래처·달 → 그 운행 [입금 완료] → "입금 완료 처리했습니다".
3. 앱을 완전히 껐다 켜거나 새로고침 → 미수금 화면에 그 운행이 **다시 나오지 않으면** 성공(지금은 다시 나옴).
4. 그 기사 차량 일지에서 그 운행이 "수금"으로 보이면 성공.
