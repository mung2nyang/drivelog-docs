# docs/report.md — 현재 슬라이스 착수지시서

120-C: drivelog-docs `ae914c9`. 120-B: drivelog-docs `503a3d3`. 120-A: drivelog-docs `81f927a`. 119: drivelog-docs `4ef51fa`. 147: drivelog-docs `db47a3b`. 내역서 산재 빼기: drivelog-docs `6531e7b`. 143: drivelog-docs `a4f4738`. 144: drivelog-docs `a87a1b4`. 146: drivelog-docs `207f26c`. 145: drivelog-docs `7bd1146`. 141: drivelog-docs `397513b`. 104: drivelog-docs `ec18974`. 142: drivelog-docs `84fa347`. 148: drivelog-docs `d5dec47`. 138-B: drivelog-docs `8b59d03`. 138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 120-D. 가짜 서버의 "예전처럼" 옵션 지우기 `[x]`

- 근거: 로드맵 120 끝(120-C `20676b0`). 2026-11-01 보리 "승인,지워" `[확인: 2026-11-01 보리 — "승인,지워"]`.
- 2026-11-01 확정했다 `[확인: 2026-11-01 보리 — "응 너가해.착수지시서 확정, 작업 진행해"]`.
- 120-C로 테스트에서 `allowUnregistered`를 쓰는 곳이 없어졌다. 쓰지 않는 옵션을 가짜 서버에서 지운다.

### 쉬운 요약
- 가짜 서버에는 "등록 안 한 호출도 예전처럼 성공시키기" 옵션이 남아 있다. 이제 이 옵션을 쓰는 테스트가 없다.
- 옵션을 지워서, 누가 다시 켜서 엄격 검사를 피하는 일이 없게 한다.
- 바꾸는 것은 가짜 서버 파일과 그 옵션을 시험하던 시험 1개뿐이다. 앱 코드는 그대로다.

### 현재 상태 (코드 확인)
- `grep -rn allowUnregistered src` 결과는 두 파일에만 나온다.
  - `src/testSupport/fakeSupabaseClient.js`: 매개변수 받기(55~56줄), 주석(94줄), 옵션이 켜지면 빈 성공을 돌려주는 줄(97줄).
  - `src/data/cloud/fakeSupabaseClient.test.js`: 옵션 자체를 시험하는 시험 1개(61~71줄).
- 다른 테스트 49개 파일은 모두 `createFakeSupabase()`를 인자 없이 부른다(120-C 뒤 grep).
- `unregistered(table, method, ok)`의 셋째 인자 `ok`는 옵션이 켜졌을 때 돌려줄 기본값으로만 쓰인다. 부르는 곳은 6곳이다(select·upsert·insert·update·delete·rpc).
- 문서: STATUS.md·roadmap.md에 이 옵션 이름이 나온다. 모두 지난 슬라이스 기록이라 고치지 않는다. AGENTS.md·sot.md에는 나오지 않는다.

### 목표 상태 (기대 동작)
1. `createFakeSupabase()`는 옵션을 받지 않는다. 등록 안 한 호출은 언제나 "등록 안 한 호출: 표.동작" 오류다.
2. `unregistered`는 셋째 인자 없이 `(table, method)`만 받고 항상 오류를 던진다. 부르는 6곳도 셋째 인자를 뺀다.
3. 옵션을 시험하던 시험 1개를 지운다. 나머지 6개 시험은 그대로 통과한다. 엄격 기본값 시험과 `resetHandlers` 시험이 "등록 안 하면 오류"를 계속 확인한다.
4. `grep -rn allowUnregistered src` 결과가 0건이다.
5. 전체 테스트가 지금처럼 통과한다.

### 건드릴 파일
- `src/testSupport/fakeSupabaseClient.js`
  - 매개변수와 JSDoc의 `@param` 줄을 지운다.
  - `unregistered`에서 옵션 분기와 `ok` 인자를 지운다. JSDoc 반환은 `never`로 바꾼다.
  - 부르는 6곳에서 셋째 인자를 뺀다.
  - 94줄 주석은 이렇게 바꾼다(최종 문구): `// 등록 안 한 호출은 기다리는 곳에서 오류.`
- `src/data/cloud/fakeSupabaseClient.test.js`: 61~71줄 시험 1개를 지운다.
- 공용 영향: 가짜 서버는 테스트 50개 파일이 같이 쓴다. 다만 옵션을 넘기는 곳이 없으므로 동작은 바뀌지 않는다. 전체 테스트로 확인한다.

### 안 건드릴 것
- 앱 코드, 다른 테스트 파일, `emptyOkHandlers`.
- STATUS.md·roadmap.md의 지난 기록.

### 검증
- 자동 검사: `npm test`, `npm run typecheck`, `npm run lint`, `npm run depcheck`, `npm run build`.
- `grep -rn allowUnregistered src` 0건.
- 테스트 출력의 "등록 안 한 호출"이 시험 이름 1개에만 나오는지 본다(옵션 시험 이름이 빠져 2개 → 1개).
- 고치기 전 실패 확인은 해당 없다. 새 시험이 없고, 지우는 시험은 사라지는 기능을 시험하던 것이다.

### 파일 길이
- `src/testSupport/fakeSupabaseClient.js`: 196줄 → 195줄 정도. 200줄 감시 목록 파일이라 줄 수가 늘지 않게 한다.
- `src/data/cloud/fakeSupabaseClient.test.js`: 87줄 → 75줄 정도(테스트 파일).

### 실패 시 처리
- 옵션을 지운 뒤 깨지는 테스트가 나오면 멈춘다. 그 테스트가 옵션에 기대던 이유를 적어 수정 착수지시서로 다시 승인받는다.
- 옵션을 되살리거나 새 대비 장치를 만들지 않는다.

### 사용자 확인 방법
- 화면이 바뀌는 일이 아니다. CI 초록으로 확인한다.

### 진행 기록
- 2026-11-01 보리 "너가해"로 이 세션이 구현했다. react-app `b11d6b8`. 2026-11-01 보리 "푸시해줘"로 이 세션이 push했고, CI verify·deploy 초록(run 41305516799)이다. 바뀐 파일은 2개다.
- 결과
  - `grep -rn allowUnregistered src` 0건.
  - 가짜 서버 파일 196 → 195줄.
  - 부르는 6곳 모두 `unregistered(표, 동작)`.
  - 94줄 주석은 지시서 문구 그대로다.
- 검증
  - `npm test`: 963+942 통과, 실패 0. 지운 시험 1개만큼 줄었다.
  - "등록 안 한 호출"은 시험 이름 1개에만 나온다.
  - typecheck·lint·depcheck·build는 통과했다.
- §5 리뷰
  - 범위: 바뀐 파일은 지시서의 2개뿐이다.
  - 몰래 늘린 것: 없다.
  - 타입 꼼수: 없다(diff grep 0건). 반환 타입은 `never`다.
  - 주석: 지시서 최종 문구와 같다.
  - 200줄: 196 → 195줄이다.
  - 테스트 진실성: 지운 시험은 사라진 옵션을 시험하던 1개뿐이다. 엄격 기본값 시험은 그대로 남았다.
  - 공용 영향: 50개 파일이 옵션을 넘기지 않아 동작이 같다. 전체 테스트로 확인했다.
  - 목표 1~5: 모두 충족했다.
