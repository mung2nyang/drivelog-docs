# docs/report.md — 현재 슬라이스 착수지시서

105번: 105-A drivelog-docs `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 105-B 코드 검사기 경고 0 + CI 검사 단계 `[x]`

로드맵 105번 두 번째(마지막) 조각. 105-A `[x]`(react-app `d7a69fa`, drivelog-docs `aa5c8b9`) 뒤 남은 경고 29개를 0으로 하고 CI에 넣는다. 결정은 105-A 때 받음 `[확인: 2026-10-23 보리 — "권함으로"]`: 경고도 실패, `only-export-components` 끔, 테스트 파일만 `immutability`·`globals` 끔, 안 쓰는 변수 지움.

### 쉬운 요약
- 남은 경고 29개를 없애고, 앞으로 push할 때마다 CI가 코드 검사기도 돌리게 한다. 경고가 하나라도 생기면 CI가 빨강이 된다.
- 앱 동작은 바뀌지 않는다(운영 코드는 주석 1줄만, 나머지는 설정·테스트 파일).

### 현재 상태 (코드 확인)
- `npx oxlint` 29개(2026-10-23, HEAD `d7a69fa`): 테스트 `immutability` 11·`globals` 1, 테스트 안 쓰는 변수 8, `only-export-components` 8(`FieldError.jsx` 2·`SideMenu.jsx` 2·`confirmDialog.jsx` 3·`OffToggle.jsx` 1), `PageLoadErrorBoundary.jsx:30` 1.
- `package.json` `"lint": "oxlint"` — 경고만 있으면 성공(끝 코드 0)으로 끝남. `.github/workflows/ci.yml` verify에 검사 단계 없음(테스트·타입·빌드·의존 방향 4개만).
- 아래 설정을 저장소 밖 임시 파일로 시험(`oxlint -c`): 남는 경고가 정확히 안 쓰는 변수 8 + `PageLoadErrorBoundary` 1 = 9개, `--deny-warnings`면 끝 코드 1.
- 테스트 파일은 246개 모두 `*.test.js`(`find src -name "*.test.*"`).

### 목표 상태 (기대 동작)
1. `npm run lint` 결과 경고 0·오류 0, 끝 코드 0.
2. 경고가 하나라도 있으면 `npm run lint`가 실패(끝 코드 1)한다.
3. CI verify에 "코드 검사 (npm run lint)" 단계가 있고, 다른 4개 단계처럼 하나가 실패해도 나머지는 계속 돈다(`if: ${{ !cancelled() }}`). 이 단계가 실패하면 배포하지 않는다(기존 `success()` 조건 그대로).
4. `only-export-components`는 꺼지고, 테스트 파일에서만 `immutability`·`globals`가 꺼진다. 운영 코드에서는 두 규칙이 그대로 켜져 있다.
5. 안 쓰는 변수 8개가 테스트에서 없어지고, 테스트 수·통과는 그대로다(지운 것은 어디서도 읽지 않던 값 — 단언 삭제 없음).
6. `PageLoadErrorBoundary.jsx`는 코드는 그대로 두고 30줄 위에 검사 끄기 주석 1줄만 단다.

`.oxlintrc.json` 최종 모습(시험한 것과 같음, 테스트 파일 이름은 앞으로 `.ts`로 바뀌어도 되게 3가지):
```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "plugins": ["react", "oxc"],
  "rules": {
    "react/rules-of-hooks": "error",
    "react/only-export-components": "off"
  },
  "overrides": [
    {
      "files": ["**/*.test.js", "**/*.test.ts", "**/*.test.tsx"],
      "rules": {
        "react/immutability": "off",
        "react/globals": "off"
      }
    }
  ]
}
```
- `package.json`: `"lint": "oxlint --deny-warnings"`.
- `ci.yml`: "의존 방향 검사" 다음에 단계 추가, 위 주석 "아래 4개는" → "아래 5개는".
  ```yaml
      - name: 코드 검사 (npm run lint)
        if: ${{ !cancelled() }}
        run: npm run lint
  ```

코드에 넣을 주석 최종 문구:
- `PageLoadErrorBoundary.jsx` 30줄 위: `// oxlint-disable-next-line react/no-did-update-set-state -- 다른 화면으로 가면 안내를 풀어야 해서, 화면이 바뀐 때 한 번만 바꾼다.`

### 건드릴 파일
- `.oxlintrc.json` — 위 최종 모습
- `package.json` — `lint` 한 줄
- `.github/workflows/ci.yml` — 단계 1개 추가, 주석 숫자 1곳
- `src/app/shell/PageLoadErrorBoundary.jsx` — 주석 1줄
- 테스트 4개 파일(안 쓰는 변수 8개):
  - `src/app/App.test.js` — `totalSupabaseCalls` 함수와 그 위 설명 주석 4줄(18~21줄), 가져오기 목록에서 `hasPendingDayWrites`·`getUnsafeRegistrationPatch`·`clearUnsafeRegistrationFailure`·`subscribe` 이름만
  - `src/data/sync/receivablesCloudCommit.test.js` — 가져오기 목록에서 `getState`
  - `src/app/accountPermissionUi.test.js` — 읽지 않는 `selectedPage` 변수와 그것을 채우던 줄(82줄 `onSelect`는 빈 함수로)
  - `src/features/day-log/ui/CallDetailList.test.js` — `mountList(props)`의 안 쓰는 `props`(부르는 4곳 모두 인자 없음, 52·73·98·124줄)

### 안 건드릴 것
- `only-export-components`가 걸린 4개 파일(`FieldError.jsx`·`SideMenu.jsx`·`confirmDialog.jsx`·`OffToggle.jsx`) — 규칙을 끄므로 코드 그대로.
- 테스트 `immutability`·`globals`가 걸린 10개 파일 — 설정으로 끄므로 그대로.
- 운영 코드 동작, 새 파일, 새 라이브러리 — 없음(oxlint는 이미 `devDependencies`에 있음, `package.json:36`).
- `AGENTS.md` §4의 CI 설명(테스트·타입·빌드) — AI는 못 고침. 의존 방향 검사도 지금 거기 없음. 고칠지는 보리 판단.

### 검증
- 로컬 `npm run lint`(목표 1), `npm test`(테스트 수가 지금과 같은지 — 105-A 진행 기록 884 + 757), `npm run typecheck`.
- 실패 흉내(목표 2·4, 98-B와 같은 방식, 확인 뒤 바로 되돌림·커밋 안 함): ① 운영 파일 하나에 안 쓰는 변수 → `npm run lint` 끝 코드 1 ② 운영 파일에서 `immutability` 경고 만들기 → 잡힘(테스트 밖에서는 꺼지지 않음) ③ `.test.js`에서 같은 것 → 안 잡힘.
- push 뒤 CI에 "코드 검사" 단계가 보이고 초록인지(목표 3).

### 파일 길이
- `PageLoadErrorBoundary.jsx` 43 → 44, `.oxlintrc.json` 8 → 18, `package.json` 40 → 40, `ci.yml` 84 → 89. 테스트 파일은 줄어듦(제한 예외). 모두 200줄 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 시험·CI가 실패하면 코드는 그대로 두고 이 파일에 수정 착수지시서를 써서 다시 승인받는다.

### 진행 기록
- 2026-10-23 터미널 AI 교차검증 — 지금 설정(`only-export-components` warn)·`"lint": "oxlint"`·CI 4단계·안 쓰는 변수 8곳(줄 번호 포함) 모두 지시서와 맞음. 고친 사실 하나: `App.test.js`의 `totalSupabaseCalls`를 지우면 그 안에서만 쓰던 `callCounts`(16줄 `createFakeSupabase()` 꺼내기)가 새로 안 쓰는 변수가 됨 → 꺼내기 목록에서 `callCounts`도 뺌(다른 곳에서 안 읽음, grep 3건 = 꺼내기·주석·함수 안).
- 결과: 8개 파일. `npm run lint`(`oxlint --deny-warnings`) 경고 0·끝 코드 0. 줄 수 `.oxlintrc.json` 17·`ci.yml` 88·`PageLoadErrorBoundary.jsx` 44. 전체 테스트 884 + 757 그대로 통과(단언 삭제 없음), 타입 검사·빌드·의존 검사 통과.
- 실패 흉내(저장소 안 임시 파일, 확인 뒤 바로 지움·커밋 안 함): ① 운영 파일에 안 쓰는 변수 → 끝 코드 1 ② 운영 파일에서 `globals`(밖 변수 바꾸기) → 잡힘·끝 코드 1, `immutability`는 105-A 이전 `MessageTemplateSheet.jsx`(`window.location.href =`)를 그대로 복사한 운영 파일 → 잡힘·끝 코드 1 ③ 같은 두 코드를 `.test.js`로 → 안 잡힘·끝 코드 0. (손으로 만든 짧은 `props.value =` 예시는 검사기가 화면 부품으로 보지 않아 원래부터 안 잡힘 — 그래서 실제 코드로 확인.)
- 코드 커밋 react-app `6119398`(2026-10-23, 8개 파일 — 설정 3·운영 1·테스트 4, push 안 함). 남은 것: 보리 push → CI verify에 "코드 검사" 단계 초록 확인 → 배포본 확인 → §5 리뷰 → 최종 승인. `AGENTS.md` §4 CI 설명은 안 건드림(보리 판단).
- push 뒤 CI verify·deploy 초록(run 39891498006), verify 안 "코드 검사 (npm run lint)" 단계 초록. §5 리뷰(데스크톱 AI): 범위 8개 파일 지시서와 같음(`callCounts` 1개 더 뺀 것은 교차검증 기록대로), 새 파일·라이브러리 없음, 타입 꼼수 0, 200줄 이하, 단언 삭제 없음, 목표 1~6 충족. 배포본 홈 정상, 앱 파일 이름(`index-BfhXmTpQ.js`)이 105-A 배포 때와 같음 — 운영 코드 변화가 주석 1줄뿐이라 빌드 결과가 같음.
- 보리 최종 `[x]` 승인(2026-10-23).

### 사용자 확인 방법
1. push 뒤 GitHub Actions 실행 화면: verify 안에 "코드 검사 (npm run lint)"가 있고 초록.
2. 배포본(getdrivelog.com)이 전과 같이 열림 — 화면 바뀐 것 없음.

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
