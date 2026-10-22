# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 103 CI에서 테스트 파일 맨 끝 결과가 가끔 빠짐 `[x]` `[확인: 2026-10-22 보리 — "103 [x] 승인. 마무리해."]`

보리 지시 "작성해"(2026-10-22, 94-C `[x]` 뒤, 103번 등재 `[확인: 2026-10-22 보리 — "로드맵에 등록해"]`). 플레이북 §6(테스트 진실성·Clean Run) 확인함. 운영 코드는 안 건드림.

### 결정 — CI 테스트 단계 시간 제한 `[확인: 2026-10-22 보리 — "응 그렇게해그럼"]`
- **CI 테스트 단계에 15분 제한 한 줄**(`timeout-minutes: 15` — 지금 테스트는 약 2분). 강제 종료 옵션을 뺀 뒤 누가 가짜 서버 연결 없이 테스트를 쓰면 그 파일이 안 끝나 CI가 GitHub 기본 제한(6시간)까지 멈추는 것을, 15분 뒤 빨강으로 바로 보이게 함.

### 쉬운 요약
- CI가 테스트 파일마다 "다 끝나면 바로 강제로 끄기" 옵션을 쓰는데, 리눅스에서는 끄는 순간 아직 못 보낸 마지막 결과가 버려져 맨 끝 테스트가 통과로도 실패로도 안 잡히는 일이 생깁니다(두 번 확인됨).
- 이 옵션을 넣은 이유는 테스트 10개 파일이 진짜 서버 연결을 불러와 프로세스가 안 끝나기 때문입니다. 이 10개 파일도 다른 62개 테스트처럼 이미 있는 가짜 서버 연결을 쓰게 하고, 강제로 끄기 옵션을 뺍니다.
- 그러면 모든 테스트 결과가 빠짐없이 CI에 잡히고, 테스트가 실제 서버 주소로 연결 객체를 만들지 않게 됩니다. 앱 동작은 안 바뀝니다(테스트·설정만).

### 현재 상태 (확인 근거)
- 빠진 결과 두 번 모두 파일 맨 끝: CI run 39736913821(`079aee8`) `domain/finance.test.js` 마지막 테스트 1개, run 39712649021(`de91e4d`) `domain/clients.test.js` 마지막 묶음(테스트 2개). 둘 다 그 묶음의 앞부분만 찍히고 끝 줄 없이 다음 파일로 넘어감, 실패로 안 셈. 이 PC(윈도)에서는 878개 그대로.
- `package.json` `test:unit`·`test:app`에 `--test-force-exit` — `ce08638`(2026-09-03, 테스트 명령 나눌 때)에 들어옴, 이유 기록 없음. 추정 원인: 강제 종료가 아직 못 보낸 출력을 버림(리눅스는 출력 보내기가 비동기, 윈도는 동기라 이 PC에서는 안 생김 — 확인 안 함, 두 번 모두 파일 끝이라는 것과 맞음).
- 옵션 없이 돌리면(이 PC, 파일 변경 없음) 10개 파일이 테스트는 다 통과한 뒤 안 끝남 — 400초 뒤 강제로 멈춤: `data/store/ownerDataHooks.test.js`, `data/sync/onboardingFinish.test.js`, `app/shell/usePullToRefresh.test.js`, `features/notifications/api/notifications.test.js`·`notifications.dateLabel.test.js`·`notifications.dailyInspection.test.js`, `features/documents/api/reportSummary.test.js`, `features/documents/ui/TaxInvoiceDraftModal.test.js`, `features/day-log/ui/CallDetailForm.test.js`·`AssignmentPeriodPage.test.js`.
- 원인(추적함): 10개 모두 진짜 `data/cloud/supabaseClient.ts`를 가짜로 안 바꿈(`mock.module`·`stubSupabaseClient` 0) → `createClient`가 Supabase 로그인 모듈을 만들면서 탭 사이 로그인 맞추기 창구(`BroadcastChannel`, `GoTrueClient.js:258`)를 엶 — 가짜 화면(`setupDom.js`)이 `window`를 만들어 브라우저로 여기기 때문. 이 창구가 프로세스를 붙잡음.
- 이미 있는 해결 도구: `testSupport/stubSupabaseClient.js`(머리 주석이 바로 이 "안 끝나고 매달리는 문제"용), 테스트 62개가 씀 — 맨 먼저 불러오고 시험 대상은 `await import`로 뒤에 불러오는 방식.
- 시험(파일 변경 없음): 10개 파일을 이 도구를 먼저 불러온 채 옵션 없이 돌리니 57개 전부 통과, 10초에 스스로 끝남.

### 목표 상태 (기대 동작)
1. 10개 테스트 파일이 `stubSupabaseClient.js`를 맨 먼저 불러오고, 진짜 서버 연결을 거쳐 오는 시험 대상은 그 뒤 `await import`로 불러옴(다른 62개와 같은 방식). 테스트 내용·단언은 안 바뀜.
2. `package.json` `test:unit`·`test:app`에서 `--test-force-exit`만 뺌(나머지 옵션 그대로).
3. `ci.yml` 테스트 단계(`테스트 (npm test)`)에 `timeout-minutes: 15`.
4. `npm test`가 옵션 없이 스스로 끝나고 878·663 그대로 전부 통과. 옵션 없이 따로 돌려도 안 끝나는 파일 0.
5. push 뒤 CI에서도 878·663, 각 테스트 파일의 마지막 결과까지 찍힘.

### 건드릴 파일
- 위 10개 테스트 파일 — 가져오는 순서·방식만
- `package.json` — 테스트 명령 2줄에서 옵션 하나씩 빼기
- `.github/workflows/ci.yml` — 테스트 단계에 한 줄
- §3 "1~3개 파일" 예외: 같은 원인·같은 고침이 10개 파일에 걸려 있어 한 슬라이스로(나누면 옵션을 뺄 수 없는 중간 상태가 생김).

### 안 건드릴 것
- 운영 코드 전부(`supabaseClient.ts` 포함 — 탭 사이 로그인 맞추기는 앱에 필요), `stubSupabaseClient.js`·`setupDom.js`, 다른 테스트 파일, 테스트 내용·단언, 빌드·타입·의존 검사 설정.

### 검증
- 고치기 전 실패 확인(플레이북 §6): 지금 코드로 옵션 없이 돌리면 10개 파일이 안 끝남(위 기록, 400초) — 고친 뒤 같은 명령이 스스로 끝나야 함.
- `npm test`(옵션 빠진 명령)로 878·663 전부 통과, 걸린 시간 기록. 10개 파일 각각 옵션 없이 따로 실행해 스스로 끝나는지.
- 10개 파일 diff가 가져오기 줄 말고 안 바뀌었는지(테스트 이름·단언 그대로), 테스트 이름 목록이 고치기 전과 같은지.
- 빌드 결과는 안 바뀜(운영 코드 안 건드림) — `npm run build` 해시 같음. 타입 검사·lint·의존 검사.
- push 뒤 CI 기록에서 878·663, `finance.test.js`·`clients.test.js` 마지막 테스트가 찍혔는지 데스크톱 AI가 확인.

### 파일 길이
- 10개 테스트 파일(지금 38~190줄, 합계 1,019줄) — 가져오기 줄이 1~5줄 늘 수 있음. 테스트 파일은 200줄 제한 예외지만 `CallDetailForm.test.js`(190)는 200 안쪽 유지 목표.
- `package.json`·`ci.yml` 길이 변화 없음 / +1줄.

### 코드에 넣을 주석(최종 문구)
- 새 주석 없음(`stubSupabaseClient.js` 머리 주석이 이미 이유를 설명함).

### 실패 시 처리
- 새 저장 장치·층 없음. 옵션을 빼고도 안 끝나는 파일이 남거나 테스트 수가 달라지면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
- 화면에서 볼 것은 없음(앱 코드 안 바뀜). push 뒤 CI 초록·테스트 수 878·663을 데스크톱 AI가 확인해 보고.

### 진행 기록
- 2026-10-22 보리 "착수지시서 확정,작업진행해". 교차검증: 10개 파일·`--test-force-exit` 2줄·도구 사용 방식 모두 지시서와 같음. 정적 `import`는 가짜 바꾸기보다 먼저 묶이므로 앱 모듈을 정적으로 불러오던 4개(`onboardingFinish`·알림 3개)는 `await import`로 옮김(`domain/`은 서버 연결을 안 거쳐 그대로), 나머지 6개는 도구 한 줄만.
- 결과(커밋 전): 10개 파일 diff는 가져오기 줄만(+29 −15, 테스트 이름·단언 그대로). `npm test`(옵션 없음) 878·663 전부 통과·스스로 끝남, 이 PC 1분 24초. 10개 각각 따로 옵션 없이 1~8초에 스스로 끝남(합계 57개 통과). 빌드 해시 94-C 커밋과 같음(62개 파일), 타입 검사 통과, lint 경고 50 그대로, 의존 검사 위반 0. `CallDetailForm.test.js` 191줄.
- `ci.yml` 테스트 단계 `timeout-minutes: 15` `[확인: 2026-10-22 보리 — "권함으로"]`.
- 코드 커밋 react-app `af0d443`(2026-10-22, 12개 파일, push 안 함). 남은 것: 보리 push → CI(878·663, 파일 끝 결과까지) → §5 리뷰 → 최종 승인.
- (데스크톱 AI) push 뒤 CI verify·deploy 초록(run 39753613464). 테스트 878·663, 묶음 182·51(이 PC와 같음), 전에 빠졌던 `finance.test.js` 마지막 테스트·`clients.test.js` 마지막 묶음 모두 찍힘. 테스트 단계 65초(강제 종료 옵션 없이 리눅스에서도 모든 파일이 스스로 끝남), 의존 검사 1,619 그대로.
- §5 리뷰: ①범위 — 12개 = 지시서 "건드릴 파일"과 같음(테스트 10·`package.json`·`ci.yml`) ②증설 — 새 파일·저장 장치·의존성 없음(이미 있는 `stubSupabaseClient.js` 사용) ③타입 꼼수 — 없음 ④200줄 — 테스트 파일 예외, `CallDetailForm.test.js` 191 ⑤테스트 — 바뀐 줄은 가짜 연결 한 줄과 가져오기 방식(`import` → `await import`)뿐, 테스트 이름·단언 그대로, 수 그대로 ⑥공용 — 운영 코드 안 바뀜(빌드 해시 같음) ⑦기대 동작 1~5 충족. `--test-force-exit` 남은 곳 0, `timeout-minutes: 15`는 테스트 단계에.

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
