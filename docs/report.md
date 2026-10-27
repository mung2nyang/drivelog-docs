# docs/report.md — 현재 슬라이스 착수지시서

118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 127 연동 기사 [다시 시도]가 연동 확인 실패를 "연동 없음"으로 보고 화면을 비움 `[x]`

폴더별 전수점검(2026-10-26) A1, 로드맵 127 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 순서 ① 127 → 135 중 첫 번째(118 뒤·79 앞). 앞 슬라이스 118 `[x]`(react-app `38c1726`). **DB 변경 없음**(앱 코드만).

### 쉬운 요약
- 서버에서 기록을 못 불러오면 화면 위에 노란 띠와 [다시 시도] 단추가 뜹니다. 연동 기사가 이 단추를 누를 때, 앱은 먼저 "이 사람이 어느 차주에 연동돼 있나"를 서버에 물어봅니다.
- 그 물음이 실패해도 지금은 "연동 없음"으로 받아들여, 차주 기록 대신 기사 본인 계정의 빈 기록을 불러와 화면을 덮고 "다시 불러왔습니다"라고 띄웁니다.
- 앱을 켤 때처럼 "확인 실패"와 "연동 없음"을 나눠, 확인이 실패하면 불러오지 않고 "아직 불러오지 못했습니다"를 띄우고 노란 띠를 그대로 둡니다.

### 현재 상태 (코드 확인, 2026-10-27)
- `data/hydrate/hydrate.ts:188-196` `retryHydrate`: `fetchLinkedDriverLink`(`data/sync/driverLinkRpc.ts:100-103`, 확인 실패도 `null`)로 연동을 보고, 차주 id가 지금 칸과 같을 때만 `employedDriver: true`.
- 확인이 실패하면 `employedDriver: false` → 차주 경로(`hydrate.ts:84-89`)가 프로필·차량·거래처를 **기사 본인 id**로 읽음 → 그 빈 결과로 차주 칸을 덮고(`:161-164` `replaceOwnerState`) 상태 "준비 완료".
- 앱 켤 때는 이미 구분함: `data/cloud/appSession.ts:49-62` `buildBootSession`이 `checkLinkedDriverLink`(`driverLinkRpc.ts:84-97`, 실패면 `{ ok: false }`)로 보고, 실패면 1초 뒤 1번 더, 그래도 실패면 본인 칸으로 들어가지 않음.
- [다시 시도]를 부르는 곳은 `app/shell/HydrationRetryBanner.jsx:40-51` 한 곳(`grep retryHydrate`): 오류가 나면 `'다시 시도했지만 아직 불러오지 못했습니다.'`, 띠는 상태가 "실패"인 동안 계속 보임.
- `fetchLinkedDriverLink` 설명 주석(`driverLinkRpc.ts:99`)이 사용처에 "다시 불러오기"를 적고 있음.

### 목표 상태 (기대 동작)
1. [다시 시도] 때 연동 확인이 실패하면 불러오기를 하지 않고 오류를 낸다 — 서버 읽기 0번, 화면 기록 그대로, 상태는 "실패" 그대로(노란 띠 유지), 알림은 지금 실패 문구 `다시 시도했지만 아직 불러오지 못했습니다.`
2. 연동 확인이 성공하면 지금과 같다 — 연동 차주가 지금 칸과 같으면 기사 경로, 아니면 차주 경로.
3. 차주(연동 없는 사용자) [다시 시도]도 지금과 같다(확인 성공·연동 없음 → 차주 경로).
4. 재시도 횟수는 늘리지 않는다(앱 켤 때의 "1초 뒤 1번 더"는 넣지 않음 — 사용자가 단추를 다시 누르면 됨).

### 건드릴 파일
- `react-app/src/data/hydrate/hydrate.ts` — `retryHydrate`가 `checkLinkedDriverLink`를 쓰고, 확인 실패면 `throw new Error('연동 확인 실패')`. 주석 최종 문구: `// 연동 확인이 실패하면 불러오지 않는다 — "연동 없음"으로 보면 기사 화면이 본인 빈 칸으로 덮인다.`
- `react-app/src/data/sync/driverLinkRpc.ts` — 주석만: `:99`의 사용처 목록에서 "·다시 불러오기"를 뺌 → `/** 위 확인의 예전 모양(실패도 null) — 앱이 켜진 뒤 사용처(연동 해제·초대코드)는 그대로 쓴다. */`
- 새 `react-app/src/data/hydrate/hydrateRetry.test.js` — ⓐ 연동 기사 세션(사용자 id ≠ 칸)에서 `driver_links` 조회 실패 → `retryHydrate` 오류, `profiles`·`vehicles` 조회 0번, 상태 "실패" 그대로, 그 칸 기록 그대로 ⓑ 같은 세션에서 조회 성공·차주 id 같음 → 기사 경로(차주 경로가 읽는 `vehicles` 조회 0번 — 기사 경로도 본인 `profiles`는 읽음, `hydrateEmployedDriver.ts:77`)로 "준비 완료".
- 공용 스타일·공용 값 변경 없음.

### 안 건드릴 것
- 앱 켤 때(`appSession.ts` `buildBootSession`) — 이미 구분함(위 "현재 상태").
- `buildCloudAppSession`(`appSession.ts:39-44`)의 `fetchLinkedDriverLink` — 앱이 켜진 뒤 연동 해제·초대코드 흐름이고 이번 범위 밖(전수점검에서 따로 지적 없음).
- 노란 띠 문구·모양(`HydrationRetryBanner.jsx`) — 그대로.
- 확인 성공·연동 없음인데 칸이 다른 경우(연동이 풀린 뒤 [다시 시도]) — 지금 동작 그대로, 이번 범위 밖.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(§5 세션).
- 새 테스트 ⓐ가 고치기 전 코드에서 실패하는지 먼저 확인(옛 코드는 차주 경로로 `profiles`를 읽고 "준비 완료"가 되어 실패해야 함), 고친 뒤 통과. 기존 `hydrate.test.js` 재시도 테스트(`:100-120`, 차주) 그대로 통과. 로컬 `npm test` 전체 통과.
- 테스트·타입·빌드·코드 검사는 CI가 봄. 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `hydrate.ts` 196 → 약 198줄(200줄 감시 목록 파일 — 넘지 않음), `driverLinkRpc.ts` 줄 수 그대로, 새 `hydrateRetry.test.js` 약 60줄.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음 — 확인 실패를 오류로 돌려줄 뿐). 실패하면 수정 착수지시서를 다시 써서 승인받음.

### 사용자 확인 방법
1. 연동 확인 실패는 화면에서 만들 수 없어 테스트로만 확인.
2. 배포본에서 로그인 테스터(차주) 계정으로 평소처럼 열림 — 노란 띠 없이 기록이 보이면 성공(다시 불러오기 경로를 안 건드린 차주 쪽 회귀 확인).

### 누가 하나
- 데스크톱 AI가 앱 코드·코드 커밋까지, push는 보리 `[확인: 2026-10-27 보리 — "127 착수지시서 확정, 너가 작업 진행해"]`.

### 진행 기록
- 코드: react-app `f87c75f`(파일 3개, 지시서 "건드릴 파일" 그대로, 주석은 지시서 최종 문구 그대로). 줄 수 `hydrate.ts` 198, `driverLinkRpc.ts` 119(그대로), 새 `hydrateRetry.test.js` 59.
- 작업 중 바로잡음: 새 테스트 ⓑ의 기준을 "`profiles` 조회 0번"에서 "`vehicles` 조회 0번"으로 — 기사 경로도 본인 `profiles`(이름·전화)는 읽음(`hydrateEmployedDriver.ts:77`). 지시서 "건드릴 파일" 줄도 같이 고침.
- 고치기 전 실패 확인: `hydrate.ts`만 마지막 커밋으로 되돌려 새 테스트 실행 → 2개 중 ⓐ 실패(ⓑ는 "지금과 같음"이라 통과가 맞음), 복구 뒤 통과.
  ```
  ✖ 연동 확인이 실패하면 불러오지 않고 오류 — 서버 읽기 0번, 상태 "실패"·그 칸 기록 그대로
    AssertionError [ERR_ASSERTION]: Missing expected rejection.
      actual: undefined,
      expected: /연동 확인 실패/,
  ℹ tests 2  ℹ pass 1  ℹ fail 1  (exit=1)
  ```
- 로컬: `test:unit` 919 통과·`test:app` 899 통과(실패 0), `typecheck`·`lint` 통과.
- push(보리) 뒤 CI verify·deploy 초록(run 40497828152).
- 배포본(로그인 테스터 차주 탭, getdrivelog.com `/app/expenses`): 새 묶음 파일(`index-Cs5Juu-t.js`)로 열림, 노란 띠 없음, 10월 정비 기록 그대로(합계 230,000원). 콘솔 404는 바로 열기 주소(`/app/...`) 문서 요청 — 118 확인 때도 똑같이 난 기존 호스팅 동작, 이번 변경과 무관.
- §5 점검: ① 범위 — 바뀐 파일 3개가 "건드릴 파일"과 같음 ② 새 저장 키·큐·대비 장치 없음(확인 실패를 오류로 돌려줄 뿐) ③ 새 `any`·`unknown`·무시 주석 없음 ④ 200줄 넘는 파일 없음(`hydrate.ts` 198) ⑤ 기존 테스트 지우거나 약화한 것 없음(테스트 diff에 지운 줄 0) ⑥ 공용 값 변경 없음 — `fetchLinkedDriverLink`는 그대로 두고 `retryHydrate`만 `checkLinkedDriverLink`로 ⑦ 목표 1(새 테스트 ⓐ)·2(ⓑ)·3(기존 `hydrate.test.js` 차주 재시도 통과)·4(재시도 추가 없음) 충족.

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
