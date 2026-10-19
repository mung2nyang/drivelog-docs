# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 89-B 아무 일도 안 하게 된 `syncToCloud`/`sync` 옵션 빼기 `[x]`

> **진행(2026-10-19)**: 보리 "착수지시서 작업진행해줘". 코드 커밋 react-app `aed061c`(77개 파일, +368 / −374).
> - 교차검증(AGENTS §1): 받는 곳·넘기는 곳 목록·줄 번호가 코드와 같음. 옵션을 읽는 코드 0곳 확인(`commitBatch` 구조분해·`replaceOwnerState`의 `sync`는 넘기기만). 고친 것 없음.
> - 목표 1 grep(`syncToCloud`, `\bsync: (true|false)\b`, `sync = true`, `src` 전체·테스트 포함) **0건**.
> - 운영 16개: `commitHelpers.js` 108→106(`commit`·도우미 9개 옵션 인자·JSDoc, `commitLogWorkData`·`commitDismissedNotifications`의 칸), `owner-state.js` 136→134(머리 주석 1줄, `sync` 칸·꺼내기, 넘기는 2곳), `app-store.js` 옵션 타입 1줄, `lib/*` 13개는 칸만(`syncIdMerge.js` 두 곳은 `{ persist: true }`만 남아 인자째 삭제), `vehicleDeletion.js:77` 주석 → `// 서버 쓰기는 이미 끝났다.`
> - 테스트 61개: 옵션 칸·인자만 기계적으로 지움(같은 규칙 정규식 한 번, 지운 `sync:` 줄은 전부 `replaceOwnerState(...)` 호출임을 눈으로 확인). 줄 수 많은 파일: `vehicleMutations.test` 33, `CalendarPage.test` 27, `owner-state.test` 26, `DocumentIssuePage.test` 21, `cloudMemorySave.test` 18, `App.clientsCars` 15, `App.test` 13, `HomePage.test` 11, `App.guestDurable` 10, 나머지 52개 파일은 1~6줄. 단언은 하나도 빼지 않음. `app-store.test` describe·test 이름·메시지를 "커밋은 Supabase를 부르지 않는다"로, `App.test:612` 주석 정리.
> - `git diff`: 바뀐 줄은 옵션 칸·옵션 인자·JSDoc·주석·테스트 이름뿐(코드 동작 줄 없음).
> - `npm test` 1004 + 497 통과(89-A와 같은 수 — 지운 테스트 없음), 타입 검사·빌드 통과. 새 테스트 없음·revert-check 해당 없음(지시서대로).
> - 문서: `docs/sot.md` 세 곳의 `{ syncToCloud: false }` 표기 삭제(정한 동작 문장 그대로). 문서 커밋 때 함께.
> - 개발 서버(비회원): 설정 켜기·거래처 2개·일지 1건 저장 → 새로고침 뒤 그대로, 콘솔 오류 없음.
> - push 뒤 CI verify·deploy 초록(run 39202371303).
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서(운영 16·테스트 61·`sot.md`) ②새 파일·저장 키 0, 지운 파일 0 ③타입 꼼수 0 ④200줄 이내(`hydrate.js` 199 그대로) ⑤테스트 수 1,422 → 1,422, `assert.` 5,247 → 5,247(빠진 단언 0) ⑥공용 옵션 — 읽던 곳 0을 grep으로 다시 확인 ⑦목표 1 grep 0건. 바뀐 줄에서 옵션 표기를 지우고 비교하면 남는 차이는 JSDoc·주석·테스트 이름, 도우미 9개 `options` 인자, `{ persist: true }`만 남아 인자째 지운 3곳(`syncIdMerge.js` 2·`app-store.test` 원자성 테스트 1 — 기본값과 같음, 규칙 4)뿐.
> - 배포본 로그인 확인(2026-10-19, 내장 브라우저): 새 묶음 파일로 열림, 차량 3·거래처 1·연동 기사 1 그대로, 오늘 일지 2회 저장 → 새로고침 뒤 그대로 → 비워서 되돌림, 콘솔 오류 없음.
> - 보리 최종 승인(2026-10-19).

보리 "응"(2026-10-19, 89-A `[x]` 승인 + 89-B 지시서). 89-A에서 "89-A 바로 다음 따로" `[확인: 2026-10-19 보리]`.
동작 변화 없는 기계적 정리. 플레이북 트리거(`store/**`) 해당 — 저장 경로 동작은 안 바뀜(옵션을 읽는 코드가 이미 0곳).

### 쉬운 요약
- 저장 함수에 "서버로 보낼지" 표시(`syncToCloud`, `sync`)를 넘기는 곳이 많은데, 88-A·89-A 뒤로 이 표시를 읽는 코드가 하나도 없습니다.
- 넘기는 곳과 받는 칸을 모두 지웁니다. 읽는 사람이 "이걸 끄면 뭐가 달라지지?" 하고 헷갈리지 않게 하려는 정리입니다.
- 화면·저장 결과는 그대로입니다.

### 현재 상태 (코드 확인)
- 받는 쪽: `store/app-store.js:138` `commitBatch` 옵션 타입에만 `syncToCloud`(89-A에서 꺼내 쓰는 줄 삭제 — 구조분해 `:142`에 없음). `store/commitHelpers.js` `commit()`의 `options`는 `syncToCloud` 하나뿐(`:22`), 도우미 9개 JSDoc `{ syncToCloud?: boolean }`(`:32·57·62·67·72·77·82·87·104`). `store/owner-state.js` `replaceOwnerState`의 `sync`(`:104·107`) → `commitBatch`에 `syncToCloud: sync`로 넘김(`:132·135`), `initializeOwnerFromPersist` `syncToCloud: false`(`:52·70`).
- 넘기는 쪽(운영, grep `syncToCloud|sync: false`): `lib/clientCloudSave.js:50`, `lib/directMutationActions.js:58·90·120`, `lib/driverUnlink.js:78`, `lib/expenses.js:38·57`, `lib/guestBackup.js:156`, `lib/hydrate.js:73·164`, `lib/invoices.js:39`, `lib/practiceSettings.js:58`, `lib/profile.js:50`, `lib/requestDriverInviteSave.js:60·126`, `lib/syncIdMerge.js:21·29`, `lib/vehicleDeletion.js:77(주석)·78`, `lib/vehicleMutations.js:90·146`, `store/commitHelpers.js:50·99`.
- 테스트: 61개 파일 312줄 — `{ syncToCloud: false }` 271, `{ sync: false }` 18, 나머지는 `persist`·`extraWrites`·`replaceWorkLogs`와 섞인 모양 23. 테스트 파일은 타입 검사 대상 아님(`tsconfig.json` `checkJs: false`, 운영 파일만 `// @ts-check`) → 남겨도 깨지진 않지만 옛 뜻으로 읽힘.

### 목표 상태 (기대 동작)
1. `src` 전체(테스트 포함) grep `syncToCloud` 0건, `\bsync: (true|false)\b`·`sync = true` 0건(다른 낱말 `syncExpenseItems` 등은 대상 아님).
2. `commitBatch` 옵션 타입에서 `syncToCloud` 삭제. `replaceOwnerState` 옵션에서 `sync` 삭제.
3. `commitHelpers.js`: `commit(domain, ownerKey, value)`로 옵션 인자 삭제, 도우미 9개(`commitWorkData`·`commitCars`·`commitClients`·`commitSettings`·`commitExpenses`·`commitInvoices`·`commitDrivers`·`commitProfile`·`commitWorkDataDeletedDates`)의 `options` 인자·JSDoc 삭제.
4. 넘기는 쪽 고치는 규칙: `syncToCloud`/`sync` 칸만 지움. 그래서 옵션 객체가 비면 인자째 지움. `{ persist: true }`만 남으면(`syncIdMerge.js:21·29`) 기본값과 같으므로 인자째 지움. `persist: false`·`extraWrites`·`replaceWorkLogs` 등 다른 칸은 그대로.
5. 저장 결과(Store·도메인 키·notify 횟수·서버 호출)는 지금과 같음 — 기존 테스트가 그대로 통과.

### 건드릴 파일
- 운영(16개, 넘기는 곳·받는 곳만): `store/app-store.js`, `store/commitHelpers.js`, `store/owner-state.js`(+ 머리 주석 `:3` 삭제), `lib/clientCloudSave.js`, `lib/directMutationActions.js`, `lib/driverUnlink.js`, `lib/expenses.js`, `lib/guestBackup.js`, `lib/hydrate.js`, `lib/invoices.js`, `lib/practiceSettings.js`, `lib/profile.js`, `lib/requestDriverInviteSave.js`, `lib/syncIdMerge.js`, `lib/vehicleDeletion.js`(`:77` 주석 → `// 서버 쓰기는 이미 끝났다.`), `lib/vehicleMutations.js`.
- 테스트(61개, 위 4번 규칙 그대로 기계적으로): 바꾼 줄 수를 파일별로 진행 기록에. 단언은 하나도 빼지 않음.
  - `store/app-store.test.js:44-55` describe "commit* options.syncToCloud:false는 예약을 건너뛴다" — 단언(상태 그대로·Supabase 0회)은 지금도 맞는 동작이라 남기고 이름·메시지만 "커밋은 Supabase를 부르지 않는다"로.
  - `app/App.test.js:612` 주석의 "syncToCloud 기본값 true" 문구 정리(주석만).
- 문서: `docs/sot.md:626·650·652`의 `{ syncToCloud: false }` 표기 빼기(정한 동작은 그대로 — "서버 기록 1회 후 Store").

### 안 건드릴 것
- `persist` 옵션·`extraWrites`·`replaceWorkLogs`·`mergeWorkLogs`·`replaceDriverExpenses` — 지금도 쓰임(`batchWrites.js:44`, `app-store.js:142-163`).
- 파일 이름의 sync(`syncExpenseItems.js`·`syncTaxInvoicesTable.js`·`syncVehiclesClients.js`·`syncWorkData.js`·`syncIdMerge.js`) — 이름 정리는 59번.
- 93번(테스트만 살리는 함수) — 따로.

### 검증
- `npm test`·타입 검사·빌드 통과(테스트 수 그대로 — 지우는 테스트 없음).
- 목표 1 grep 0건을 진행 기록에.
- 새 테스트 없음(동작 변화 없음 — revert-check 해당 없음).
- `git diff`에서 바뀐 줄이 옵션 칸·옵션 인자·JSDoc·주석뿐인지 확인해 진행 기록에 한 줄.
- 개발 서버(비회원): 차량·거래처 추가, 일지 저장, 설정 하나 바꾸기 → 새로고침 뒤 그대로, 콘솔 오류 없음.

### 파일 길이
- 줄어들거나 그대로: `store/commitHelpers.js` 108 → 약 100, `store/owner-state.js` 136 → 약 135, `store/app-store.js` 174 그대로, `lib/hydrate.js` 199 그대로, `lib/vehicleMutations.js` 154 → 약 153, 나머지 `lib/*` 그대로(한 줄 안에서 칸만 지움).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(지우기만).
- 테스트가 하나라도 실패하면 옵션이 아직 어딘가에서 읽힌다는 뜻 — 고치지 말고 멈추고 어디서 읽는지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, AI가 로그인 계정으로)
1. 앱 켜기 → 차량·거래처·기사 목록 그대로.
2. 일지 한 건 저장 → 새로고침 뒤 그대로 → 되돌림.
