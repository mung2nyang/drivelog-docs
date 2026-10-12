# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 35 — 일지 서버 저장이 실패하면 화면을 나가기 전에 확인 `[x]`

보리 지시 2026-10-12 "35번부터 착수지시서 써줘". 근거: 외부 검수(2026-10-12) 결과 `docs/roadmap.md` 35번. 급한 순서 ①(데이터 유실). 앞 슬라이스 군더더기 4-F `[x]`(react-app `fca9751`)와 무관.

### 쉬운 요약
- 로그인한 사람이 일지(운행·횟수 등)를 고쳤는데 인터넷이 끊겨 서버 저장이 실패하면, 지금은 제목 옆에 "저장 실패"만 뜨고 **뒤로가기·하단 탭·새로고침을 해도 아무 확인 없이 나가져서 고친 내용이 사라진다.**
- 이미 있는 "방금 고친 내용이 아직 저장되지 않았습니다. 그래도 나가시겠습니까?" 확인 창이 이 경우에도 뜨게 한다. 새 저장 장치·자동 재시도는 만들지 않는다.
- 끝나면: 저장 실패 상태에서 나가려 하면 확인 창이 뜨고, [취소]를 누르면 화면에 남아 인터넷이 돌아온 뒤 다시 고쳐 저장할 수 있다.

### 현재 상태 (코드 확인)
- 서버 저장 실패 때 표시는 있음: `useDayDraft.js:126-129` — 실패면 `setAutoSaveStatus('failed')`(제목 옆 "저장 실패", `AutoSaveStatus.jsx:3`) + 토스트, 주석 "draft는 화면에 남는다. 재시도 큐 없음." → 외부 검수 보고의 "토스트만 잠깐"은 틀렸음(정정).
- 나가기 전 확인은 있음: `durableWriteGuard.js:71-74` `confirmLeaveIfUnsafe()` — `isDurableWriteBroken()`이 참일 때만 창. 위쪽 뒤로가기 `DayLogPage.jsx:94`, 탭 이동 `AppShell.jsx:64`, 새로고침 당기기 `usePullToRefresh.js:88`, 창 닫기·새로고침 `pendingWriteRetryListeners.js:58`(`guardBeforeUnload`)이 다 이걸 씀.
- **빈틈**: 서버 저장 실패 갈래는 이 목록(`markUnsafeRegistrationFailure`)에 올리지 않음 → 확인 창이 안 뜸. 근거: 비회원 갈래만 올림(`dayDraftLifecycle.js:29`), 기존 테스트가 일부러 "안 올림"을 검사(`App.test.js` 슬라이스 D 테스트 `assert.equal(hasUnsafeRegistration(ownerKey, dateKey), false)`).
- 화면을 나가면(언마운트) 한 번 더 저장 시도(`useDayDraft.js:181-183`), 그것도 실패하면 고친 내용은 사라짐(화면 draft가 없어지므로).

### 목표 상태 (기대 동작)
1. 로그인 메인 일지에서 서버 저장이 실패하면 그 날짜를 "아직 저장 안 됨" 목록에 올린다 → 일지 화면에 있는 동안 위쪽 뒤로가기·하단 탭·당겨서 새로고침·창 닫기에서 기존 확인 창이 뜬다.
2. 다시 고쳐서 서버 저장이 성공하면 목록에서 내린다(기존 `markSaved` → `clearUnsafeRegistrationFailure`, `useDayDraft.js:80-86`) → 확인 창이 더 안 뜬다.
3. 확인 창에서 [확인]으로 나가면(일지 화면이 닫히면) 그 날짜를 목록에서 내리고 마지막 저장을 1번 더 시도한다(기존 동작). 이때 실패해도 다시 올리지 않는다 — 고친 내용이 이미 화면에서 없어져 이후 다른 화면에서 경고를 띄워도 의미가 없기 때문. 실패 토스트는 그대로 뜬다.
4. 비회원·서브 차량(로컬 저장 갈래)은 지금 그대로 — 그 갈래의 목록 올리기·내리기·다시 들어올 때 덮어 보여 주기를 바꾸지 않는다.
5. 다시 들어올 때 덮어 보여 주기는 로그인 갈래에서 계속 안 함(`useDayDraft.js:41-43`, 서버 정본 우선) — 목록에 올려도 일지 초기값에 영향 없음.

코드 주석(최종 문구):
- 실패 갈래: `// draft는 화면에 남는다. 나가기 전 확인에 걸리도록 표시만 하고 재시도 큐는 없다.`
- 언마운트: `// 로그인 갈래: 화면이 닫히면 draft도 사라지므로 표시를 내린 뒤 마지막으로 한 번 더 시도한다.`

### 건드릴 파일
- `react-app/src/components/day-log/useDayDraft.js` — 실패 갈래에서 `markUnsafeRegistrationFailure(pendingOwner, dateKey, patch)`(화면이 열려 있을 때만), 언마운트에서 로그인 갈래면 `clearUnsafeRegistrationFailure` 후 마지막 시도. `commitNow` 의존성에 `pendingOwner` 추가.
- `react-app/src/app/App.test.js` — 슬라이스 D 테스트의 `hasUnsafeRegistration … false` 단언을 **설계 변경에 맞춰 `true`로**, 복구 뒤 `false` 단언 추가. 새 테스트 1개: 실패 상태에서 위쪽 뒤로가기 → `window.confirm` 호출 1번·[취소]면 화면 남음 / [확인]이면 나간 뒤 목록 비어 있음.
- 공용 값 영향 목록(`isDurableWriteBroken`을 읽는 곳 전부): `DayLogPage.jsx:94`, `AppShell.jsx:64`, `MainPageRoute.jsx:102`, `useAppSession.js:119`(로그아웃 전 확인 — 저장 실패 중 로그아웃해도 확인 창이 뜨게 됨, 의도대로), `usePullToRefresh.js:88`, `pendingWriteRetryListeners.js:58`. 같은 목록을 쓰는 `logPendingLifecycle.js:33-36,62-71,94-98`(서브 차량 번호 바꾸기·삭제 때 그 차량 표시 정리 — 로그인 메인 일지 표시는 메인 키라 서브 차량 작업 대상 아님, 근거: `pendingOwnerForLog`가 서브만 다른 키).

### 안 건드릴 것
- `lib/dayLogCloudCommit.js`(서버 저장 자체) — 실패 결과 모양 `{ cloud, ok, toast }` 그대로 씀.
- `lib/durableWriteGuard.js` — 함수 추가·문구 변경 없음.
- 비회원 갈래 `commitLocal`·`queueFailedDayWrite`(`dayDraftLifecycle.js`) — 목표 4.
- 로드맵 36번(콜 상세 입력 창에 쓰다 만 내용이 뒤로가기로 사라짐) — 그 내용은 아직 일지 draft에 들어가기 전이라 이번 경로와 다름, 다음 슬라이스.

### 검증
- `npm test`(전체), `npm run typecheck`.
- 플레이북(`docs/testing-playbook.md` §3·§6) 해당 항목: Supabase throw 주입(기존 `transport_details` 실패 주입 재사용) → Store·localStorage 저장 전 값 유지, durable 큐 0건, 성공 토스트 없음, "저장 실패" 표시 / 복구 단계에서 표시 내림·서버 수렴. 새 테스트는 수정 코드를 되돌리면 실패하는지 확인하고 원문 로그를 이 파일에 첨부.
- 알려진 한계(적어 둠): 로그인이 바뀌어 실패한 경우(`SESSION_CHANGED_TOAST`)도 똑같이 표시 — 일지 화면이 열려 있는 동안만이고 나가면 내려가므로 남지 않음.

### 파일 길이
- `useDayDraft.js` 186줄 → 약 192줄(200 이하).
- `App.test.js` 1081줄 → 약 1130줄(테스트 파일 예외).

### 실패 시 처리
- 새 저장 장치·재시도 큐·대비 장치 없음(기존 확인 목록 재사용만). 검증이 실패하면 코드는 그대로 두고 이 파일에 "수정 착수지시서"를 써서 다시 승인받는다.

### 사용자 확인 방법 (보리 휴대폰 또는 PC 크롬)
1. 로그인 → 아무 날짜 일지를 연다.
2. 인터넷을 끈다(휴대폰: 비행기 모드 / PC 크롬: F12 → Network → Offline).
3. 고정 노선 횟수를 바꾸거나 운행을 하나 추가 → 제목 옆 "저장 실패"가 뜨는지.
4. 위쪽 뒤로가기 → **"방금 고친 내용이 아직 저장되지 않았습니다. 그래도 나가시겠습니까?"** 창이 뜨면 성공. [취소] → 화면에 남는지. 하단 탭도 같은 창.
5. 인터넷을 켜고 횟수를 한 번 더 바꿈 → "저장 완료" → 뒤로가기 때 창이 안 뜨면 성공.
6. (덤) 다시 인터넷 끄고 실패 → 뒤로가기 [확인]으로 나감 → 다른 탭으로 옮길 때 창이 또 뜨지 **않으면** 성공.

### 검증 결과 (2026-10-12, 브라우저 확인 전 — `[~]`)
- `npm run typecheck` 통과, `npm test` 전체 통과(unit 832 + app 366, 실패 0).
- 바뀐 파일: `src/components/day-log/useDayDraft.js`(186 → 190줄), `src/app/App.test.js`(슬라이스 D 단언 `false`→`true` + 복구 뒤 `false` 1줄, 새 테스트 "로드맵 35 — … 뒤로가기" 1개). 새 파일·새 저장 키·타입 꼼수 없음.
- 되돌려서 실패 확인(플레이북 §6) — `useDayDraft.js`를 HEAD로 되돌리고 실행:
```
✖ 슬라이스 D — 로그인 클라우드 일지: 서버 upsert 실패는 Fail-Fast(…) (2093.5696ms)
  AssertionError [ERR_ASSERTION]: 로드맵 35: 실패한 날짜는 나가기 전 확인 목록에 올라가야 한다
  false !== true
      at TestContext.<anonymous> (…/src/app/App.test.js:909:12)
✖ 로드맵 35 — 로그인 일지 서버 저장 실패 중 뒤로가기: … (2044.3127ms)
  AssertionError [ERR_ASSERTION]: 저장 실패 중엔 나가기 전 확인 대상이어야 한다
  false !== true
      at TestContext.<anonymous> (…/src/app/App.test.js:982:12)
ℹ tests 2  ℹ pass 0  ℹ fail 2  (exit=1)
```
- 언마운트 때 표시 내리는 1줄만 빼고 실행:
```
AssertionError [ERR_ASSERTION]: 나간 뒤 마지막 시도가 실패해도 표시가 다시 올라가면 안 된다
true !== false
    at TestContext.<anonymous> (…/src/app/App.test.js:995:12)
ℹ fail 1
```
- 보리 브라우저 확인 통과(2026-10-12) → 코드 커밋 react-app `04a8de9`. 보리 push·CI verify 초록·최종 `[x]` 승인(2026-10-12).
