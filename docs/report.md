# docs/report.md — 현재 슬라이스 착수지시서

129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 130 설정을 연달아 고치면 앞 변경이 되돌아감 `[x]`

폴더별 전수점검(2026-10-26) K1·K5, 로드맵 130 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 129 다음 차례(① 데이터 저장·유실). DB 변경 없음.

### 쉬운 요약
- 설정 화면의 노선 삭제, 횟수 버튼 추가·고치기·삭제는 그 화면을 그릴 때 받아 둔 옛 설정을 바탕으로 저장합니다. 로그인 상태에서는 서버 저장이 끝나야 화면 값이 바뀌기 때문에, 연달아 고치면 두 번째 저장이 첫 번째 변경을 옛 값으로 덮습니다. 예를 들어 노선 A를 지우고 바로 B를 지우면 A가 되살아나고, 스위치를 끄고 바로 노선을 지우면 스위치가 다시 켜집니다.
- 저장 차례가 왔을 때의 최신 설정에서 바뀐 목록 하나만 고치도록 바꿉니다. 저장 함수는 이미 차례대로 하나씩 처리하고 이런 방식을 받도록 만들어져 있어서, 화면 두 곳이 그 방식을 쓰게만 하면 됩니다.
- 횟수 버튼 칸을 값을 바꾸지 않고 벗어날 때(길게 눌러 삭제 창이 뜰 때 포함)도 서버에 저장하던 것을 멈춥니다(K5).

### 현재 상태 (코드 확인, 2026-10-28)
- 저장 창구 `data/sync/settingsSave.ts:21-28` `savePracticeSettings`는 계정별로 하나씩 차례대로 처리하고, `:30-33`에서 그때의 최신 설정(`readOwnerSettings`) 위에 넘겨받은 값을 얹습니다. 넘겨받는 값은 객체이거나 "최신 설정을 받아 바꿀 값을 돌려주는 함수"입니다(`:15-16` `SettingsPatch`). 화면 쪽 `features/settings/ui/AppSettingsPage.jsx:44-52` `patch`는 받은 값을 그대로 넘깁니다. 차량별 항목(`patchActive` `:55-60`)은 이미 함수로 넘깁니다.
- 설정 전체를 넘기는 곳 3군데(최신 설정 위에 옛 전체를 덮음, 다른 칸까지 되돌림):
  - `features/settings/ui/RoutePresetEditor.jsx:55` 노선 × → `removeFixedRoutePreset(settings, …)` 결과 전체.
  - `features/settings/ui/RunCountChips.jsx:71` 길게 눌러 삭제 확인 → `removeRunCountPreset(settings, …)` 전체.
  - `RunCountChips.jsx:103-106` 칸을 벗어날 때 → `replaceRunCountPreset(settings, …)` 전체. 값이 그대로여도 매번 저장(K5).
- 목록 하나만 넘기지만 옛 목록을 바탕으로 만드는 곳 2군데(빨리 두 번 누르면 하나만 추가될 수 있음): `RoutePresetEditor.jsx:34-39` [추가], `RunCountChips.jsx:75-82` [+].
- 횟수 버튼 삭제·고치기는 화면을 그릴 때의 순서 번호로 찾습니다(`domain/practicePresets.ts:83-96`). 최신 설정에서 순서가 달라졌으면 다른 버튼이 바뀔 수 있습니다. 횟수 값은 겹치지 않습니다(`normalizeRunCountPresets` `:18`).
- 두 화면은 `features/settings/ui/FixedRouteBlock.jsx:40·47` 한 곳에서만 씁니다. 이 파일과 두 화면의 `onPatch` 형 설명은 객체만 받는다고 적혀 있습니다(`FixedRouteBlock.jsx:15`, `RoutePresetEditor.jsx:14`, `RunCountChips.jsx:18`).
- 횟수 버튼 삭제·고치기 함수를 쓰는 곳은 `RunCountChips.jsx`뿐입니다(grep 결과: `domain/practiceSettings.test.js`는 노선 추가·삭제와 횟수 추가만 씀).
- 실패 테스트 기록 `docs/full-code-review-failing-tests.md` K1(재현됨, 노선 A·B 연달아 삭제 시 A가 되살아남).

### 목표 상태 (기대 동작)
1. 노선 A를 지우고 바로 B를 지우면 둘 다 지워진 채로 남는다(로그인, 서버 저장이 늦을 때).
2. 횟수 버튼을 고치고 바로 다른 칸을 고치면 둘 다 남는다. 삭제한 직후 다른 칸을 고쳐도 그 칸이 맞게 바뀐다(값으로 찾음).
3. 스위치(상하차지 사용·운행 횟수 버튼 사용 등)를 바꾸고 바로 노선·횟수 버튼을 고쳐도 스위치가 되돌아가지 않는다.
4. [추가]·[+]를 빨리 두 번 눌러도 둘 다 추가된다(최대 10개 넘으면 추가 안 됨, 지금처럼 화면 기준 초과면 안내 문구).
5. 횟수 버튼 칸을 값을 바꾸지 않고 벗어나면 저장하지 않는다. 길게 눌러 삭제 창이 뜰 때도 저장하지 않는다.
6. 비회원도 지금과 같이 저장된다. 그 밖의 설정 저장(스위치·차량별 항목·테마)은 그대로다.

### 건드릴 파일
- `react-app/src/domain/practicePresets.ts` — `removeRunCountPreset`·`replaceRunCountPreset`이 순서 번호 대신 버튼 값(`count`)으로 찾게 바꿈. 값이 없으면 설정을 그대로 돌려줌. 주석 최종 문구:
  - `/** 값으로 찾아 지운다 — 앞 저장으로 순서가 바뀌어도 같은 버튼(1개 남으면 그대로). */`
  - `/** 값으로 찾아 바꾼다 — 없으면 그대로. */`
- `react-app/src/features/settings/ui/RoutePresetEditor.jsx` — [추가]·× 모두 `onPatch((current) => ({ [key]: …(current, …)[key] }))` 형태로, 바뀐 목록 하나만 최신 설정에서 계산. [추가]의 빈 칸 검사·최대 개수 안내는 지금처럼 화면 값으로 먼저 하고, 최신 설정에서 추가가 안 되면 아무것도 바꾸지 않음. `onPatch` 형 설명을 `SettingsPatch`로. 주석 최종 문구(함수 위 한 줄): `// 저장 차례가 왔을 때의 최신 설정에서 노선 목록만 바꾼다.`
- `react-app/src/features/settings/ui/RunCountChips.jsx` — [+]·삭제 확인·칸 벗어남 모두 같은 함수 형태. 삭제·고치기는 버튼 값으로. 칸 벗어남은 입력값이 원래 값과 같으면(앞뒤 공백 빼고 문자열 비교) 저장 안 함. `onPatch` 형 설명을 `SettingsPatch`로. 주석 최종 문구:
  - 함수 위: `// 저장 차례가 왔을 때의 최신 설정에서 횟수 버튼 목록만 바꾼다.`
  - 칸 벗어남: `// 값이 그대로면 저장하지 않는다(삭제 창이 뜰 때 칸을 벗어나는 것 포함).`
- `react-app/src/features/settings/ui/FixedRouteBlock.jsx` — `onPatch` 형 설명만 `SettingsPatch`로(동작 변경 없음, 두 화면에 그대로 넘기기 때문에 타입 검사를 통과하려면 필요).
- `react-app/src/features/settings/ui/RunCountChips.test.js` — 마운트 도우미가 함수로 받은 값을 그 테스트 설정에 적용해 기록(검사 내용은 그대로, 약화 아님). 추가: 값 그대로 벗어나면 저장 없음, 길게 눌러 삭제 창이 떠도 저장 없음.
- 새 `react-app/src/features/settings/ui/RoutePresetEditor.staleOverwrite.test.js` — 실패 테스트 기록 K1 그대로 + 같은 방식(가짜 서버 저장 20ms)으로 횟수 버튼 연달아 고치기(기대 2), 스위치 끈 뒤 노선 삭제(기대 3), [추가] 두 번(기대 4).
- 공용 값 영향: `onPatch`는 `AppSettingsPage.jsx:175` → `FixedRouteBlock` → 두 화면 경로 하나뿐(grep). `AppSettingsPage`의 `patch`는 이미 함수를 받음(`patchActive`가 사용 중). 스타일 변경 없음.

### 안 건드릴 것
- `data/sync/settingsSave.ts` — 차례대로 처리하는 장치와 함수 형태를 이미 지원(`:15-28`). 함수가 아무것도 안 바꿔도(빈 값) 서버에 한 번 저장되는 것은 그대로 둠(최대 개수 초과처럼 드문 경우만).
- `AppSettingsPage.jsx` — `patch`가 받은 값을 그대로 넘겨서 고칠 것 없음(`:44-52`).
- `domain/practicePresets.ts`의 노선 추가·삭제, 횟수 추가 함수 — 그대로(`practiceSettings.test.js` 그대로 통과).
- 문자 문구 설정(K3, 로드맵 135)·기사 차량 세부 입력 기본값(K2, 로드맵 145) — 다른 번호.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(설정 저장 경로를 건드리므로 §1·§5).
- 새 테스트가 고치기 전 코드에서 실패하는지 먼저 확인(K1 기록처럼 노선 되살아남), 고친 뒤 통과. 기존 `RunCountChips.test.js`·`RoutePresetEditor.test.js`·`practiceSettings.test.js`·`AppSettingsPage.*.test.js` 그대로 통과. 로컬 `npm test` 전체 통과. 타입·빌드는 CI.

### 파일 길이
- `domain/practicePresets.ts` 96 → 약 100
- `features/settings/ui/RoutePresetEditor.jsx` 69 → 약 75
- `features/settings/ui/RunCountChips.jsx` 133 → 약 140
- `features/settings/ui/FixedRouteBlock.jsx` 52 → 52
- 모두 200줄 아래. 테스트 파일은 예외.

### 실패 시 처리
- 새 저장 장치·재시도·임시 보관 없음. 기존 차례 대기 장치와 저장 실패 안내(`SERVER_SAVE_FAIL`)를 그대로 씀.
- 테스트나 배포본 확인이 기대와 다르면 코드를 더 고치지 않고 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법 (배포본, 로그인 계정)
1. 앱 설정 → 고정 노선 → 상하차지 사용 켜기 → 노선 두 개 추가 → × 두 개를 빠르게 연달아 누르기 → 새로고침해도 둘 다 없으면 성공.
2. 운행 횟수 버튼 사용 켜기 → [+]를 빠르게 두 번 → 버튼 두 개가 늘었으면 성공. 첫 칸을 눌러 숫자를 바꾸고 바로 둘째 칸을 바꾸기 → 새로고침해도 둘 다 바뀐 값이면 성공.
3. 상하차지 사용을 켠 채 운행 횟수 버튼 사용 끄기 → 바로 노선 × → 새로고침해도 운행 횟수 버튼 사용이 꺼져 있으면 성공.
4. 확인용으로 바꾼 노선·버튼은 원래대로 되돌림.

### 진행 기록
- 2026-10-28 착수 승인(보리 "확정, 진행해"). 플레이북 §1·§5·§6 열람.
- 새 테스트 `RoutePresetEditor.staleOverwrite.test.js` 6개를 고치기 전 코드에서 실행 → 6개 모두 실패(종료 코드 1):
  ```
  ✖ 노선 A를 지우고 바로 B를 지우면 둘 다 지워진 채로 남는다 (90.5916ms)      actual: [ '부산 → 대구' ]  expected: []
  ✖ 스위치를 끈 뒤 바로 노선을 지워도 스위치가 되돌아가지 않는다 (58.2793ms)   actual: true  expected: false
  ✖ [추가]를 빨리 두 번 누르면 노선 둘 다 추가된다 (60.4581ms)                actual: [ '평택 → 용인' ]  expected: [ '부산 → 대구', '평택 → 용인' ]
  ✖ 횟수 버튼 두 칸을 연달아 고치면 둘 다 남는다 (75.3717ms)                  actual: [ 1, 8, 3 ]  expected: [ 7, 8, 3 ]
  ✖ 횟수 버튼을 지운 직후 다른 칸을 고쳐도 그 칸이 바뀐다 (778.3068ms)         actual: [ 1, 9, 3 ]  expected: [ 9, 3 ]
  ✖ [+]를 빨리 두 번 누르면 횟수 버튼 둘 다 추가된다 (61.0268ms)              actual: [ 1, 2, 3, 4 ]  expected: [ 1, 2, 3, 4, 5 ]
  ℹ tests 6 / pass 0 / fail 6 / duration_ms 2381.6864
  ```
- 고친 뒤: 새 테스트 6개 통과. `RunCountChips.test.js`는 마운트 도우미가 함수로 받은 값을 그 테스트 설정에 적용해 기록하도록 바꾸고(검사 내용 그대로), K5 테스트 2개 추가 → 통과. 로컬 `npm test` 전체 통과(927 + 909), 타입 검사 통과.
- 코드 커밋 react-app `8ac03b1`(push 전). 파일 길이: `practicePresets.ts` 101, `RoutePresetEditor.jsx` 75, `RunCountChips.jsx` 143, `FixedRouteBlock.jsx` 52.
- 리뷰 점검: 바뀐 파일이 지시서 "건드릴 파일"과 같음(`git show --stat`), 새 저장 장치 없음, 타입 꼼수 없음(새 테스트의 `Function`·`unknown` 표기는 커밋 전에 구체 형으로 바꿈), 주석은 지시서 최종 문구 그대로.
- 원래 있던 것: `RunCountChips.test.js` "짧게 누르고 떼면 확인 창이 뜨지 않는다"에서 React act 경고 1건. 테스트가 `pointerup`을 act 밖에서 보내서 생기며, 이번에 바꾸지 않은 줄(`endPress`·그 테스트 본문 그대로)이라 범위 밖으로 둠.
- push(보리) 뒤 CI verify·deploy 초록(run 40637877236).
- 배포본 확인(데스크톱 AI, 로그인 계정, 보리 지시): 배포 파일에 새 코드(칸 벗어남 값 비교) 들어간 것 확인. 원래 상태 = 고정 노선 켬·상하차지 꺼짐(숨은 노선 부산→대구·대구→부산)·횟수 버튼 켬 [2,3,4,5,6].
  1. 상하차지 켬 → 시험 노선 A·B [추가] 연달아(저장 끝나기 전 화면은 옛 목록) → 둘 다 추가됨 → × 연달아 → 새로고침 뒤 둘 다 없음, 기존 노선 2개 그대로. 통과.
  2. [+] 연달아 → 7·8 둘 다 추가. 첫 칸 12·둘째 칸 13 연달아 고침(실제 클릭·입력) → 새로고침 뒤 [12,13,4,5,6,7,8]. 통과. (스크립트로만 칸을 벗어나게 한 첫 시도는 창에 초점이 없어 벗어남이 안 일어나 저장되지 않았음 — 앱 문제 아님.)
  3. 시험 노선 C 추가 → 운행 횟수 버튼 사용 끄고 곧바로 C × → 새로고침 뒤 스위치 꺼짐·C 없음. 통과.
  4. 되돌림: 운행 횟수 버튼 켬, 7·8 길게 눌러 삭제, 12→2·13→3, 상하차지 끔 → 새로고침 뒤 원래 상태와 같음.
- 2026-10-28 보리 최종 승인.

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
