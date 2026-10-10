# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 2-A — 안 쓰는 파일 지우기 `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` B1. 앞 슬라이스 1-B `[x]`(react-app `22951d4`).
2번(안 쓰는 파일·함수)은 파일 수가 많아 **2-A 파일 / 2-B 함수**로 나눔. 이 지시서는 2-A만.

### 현재 상태 (코드 확인)
| 파일 | 상태 · 근거 |
|---|---|
| `src/lib/syncDeletedWorkDates.js` | 옛 "삭제 목록 모아 두었다 나중에 서버에서 지우기" 방식. 운영 코드 호출 0건(`grep -rnw syncDeletedWorkDates src` → 자기 테스트·주석만). `fbe91d5` 이후 지금 삭제는 `lib/dailyLogRemoval.js`가 바로 처리 |
| `src/lib/syncDeletedWorkDates.test.js` | 위 파일만 검사하는 테스트(150줄) |
| `src/components/CarFormModal.jsx` | 한 줄 재수출 껍데기. 불러오는 곳 0건 — 모두 `cars/CarFormModal.jsx`를 직접 부름 |
| `src/components/ClientFormModal.jsx` | 한 줄 재수출 껍데기. 불러오는 곳 0건 — 모두 `clients/ClientFormModal.jsx`를 직접 부름 |
| `src/main-calendar.css` | 빈 파일(주석 1줄). 어디서도 불러오지 않음. `day-log/inlineSheetCss.test.js:10~12`만 이 파일을 읽어 "잘못된 규칙이 없다"를 확인 — 빈 파일이라 늘 통과하는 의미 없는 검사 |

### 목표 상태 (기대 동작)
1. 위 5개 파일 삭제.
2. `inlineSheetCss.test.js`에서 `main-calendar.css`를 읽는 3줄(10~12) 삭제. 같은 테스트의 나머지 검사(`day-log.css`)는 그대로.
3. **동작·화면 변화 없음.**

### 건드릴 파일
- 삭제: `src/lib/syncDeletedWorkDates.js`, `src/lib/syncDeletedWorkDates.test.js`, `src/components/CarFormModal.jsx`, `src/components/ClientFormModal.jsx`, `src/main-calendar.css`
- 수정: `src/components/day-log/inlineSheetCss.test.js` (3줄 삭제)

### 안 건드릴 것
- `store/commitHelpers.js` `commitWorkDataDeletedDates` — 이번 삭제 뒤 운영 코드에서 안 쓰게 되지만(남는 사용처 `lib/workData.test.js:14·36` 테스트뿐), `store/**`라 따로 확인 필요 → 2-B 후보로 넘김.
- "삭제 목록"(`domain/workDataTombstones.js`, `lib/workData.js`, `lib/hydrate.js:130`) — 아직 운영 코드가 읽고 씀. 이번 범위 아님.
- 다른 파일 주석 속 `syncDeletedWorkDates.js`·`main-calendar.css` 언급(`workData.js:35`, `commitHelpers.js:110`, `ownerWorkDataHooks.js:71`, `shared-controls.css:1` 등) — 4번(주석 정리)에서.

### 테스트
- 지우는 테스트: `syncDeletedWorkDates.test.js` 전체, `inlineSheetCss.test.js` 3줄. **검사 대상 자체가 없어지는 것이라 약화가 아님**(§5 5번) — 남은 운영 코드 중 이 테스트들이 지키던 동작은 없음(근거: 위 호출 0건).
- 새 테스트 없음. 로컬 `npm test`·`typecheck` 전부 통과로 확인.
- 플레이북 트리거: 지우는 `syncDeletedWorkDates.js`가 동기화 파일이지만 호출 0건이라 저장·삭제 동작 변화 없음.

### §6 200줄
- 삭제만 있음. 수정하는 `inlineSheetCss.test.js`는 테스트 파일(예외)이고 줄어듦.

### 실패 시 처리
- 새 저장·대비 장치 없음(§7 해당 없음). 실패 시 수정 착수지시서 작성 후 대기.

### 브라우저 확인 (보리, 휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
- 차량 관리에서 차량 추가 창, 거래처 관리에서 거래처 추가 창이 전처럼 열리는지.
- 일지에서 운행 1건 삭제 → 껐다 켜도 지워진 채인지.
- 달력·일지 화면 모양이 전과 같은지.

### 진행 결과
- 5개 파일 삭제, `inlineSheetCss.test.js` 3줄 삭제.
- 로컬 `npm test` 전부 통과(단위 832 / 화면 365, 실패 0 — 단위가 839→832로 준 7건은 지운 `syncDeletedWorkDates.test.js`의 테스트), `npm run typecheck` 오류 0, `npm run build` 성공(지운 파일을 부르는 곳 없음 확인).
- react-app `183fe39`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-10).
