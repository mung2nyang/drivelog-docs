# docs/report.md — 현재 슬라이스 착수지시서

126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 122 DB 변경이 있는 슬라이스의 배포 순서를 상시 규칙으로 `[x]`

유지보수성 점검(2026-10-26), 로드맵 122 `[확인: 2026-10-26 보리 — "로드맵에 올려줘"]`. 순서는 117 뒤·115 앞(115·116이 DB 변경을 탈 가능성이 큼). 앞 슬라이스 126 `[x]`(react-app `d4275c3`). **문서만 바꿈 — react-app 코드·DB 변경 없음.**

### 쉬운 요약
- 서버(DB)를 바꾸는 작업은 "SQL 실행 → 제대로 됐는지 확인 → 앱 push" 순서여야 합니다. 앱을 먼저 올리면 그 사이 저장이 실패합니다.
- 이 순서가 지금은 114번 지시서·커밋 메시지에만 있고 규칙 문서에는 없습니다.
- 플레이북(`docs/testing-playbook.md`)에 상시 규칙으로 적고, 착수지시서에 "배포 순서" 줄을 꼭 넣게 합니다.

### 현재 상태 (확인)
- 순서가 적힌 곳은 114 지시서(drivelog-docs `5f55ee1` "순서: DB `0028` 실행·사후검증 → 앱 push. 앱을 먼저 내보내면 함수가 없어 모든 로그인 일지 저장이 실패한다")와 react-app `3505ff6` 커밋 메시지뿐.
- `AGENTS.md` §9 DB 절차는 ① 읽기 진단 → ② 멱등 SQL → ③ 완성본 → ④ 사후검증 `SELECT`까지 — "앱 push보다 먼저"는 없음.
- `docs/testing-playbook.md`(126줄, §1~§8)에도 DB·배포 순서 없음(`push`·`SQL`·`마이그레이션` 검색 0건).
- 서버에 어느 마이그레이션까지 적용됐는지 자동으로 아는 장치 없음 — 지금은 사후검증 `SELECT`로 그 함수·정책이 있는지 봄(자동 장치는 로드맵 121에서 같이 볼 것).
- DB를 바꾼 지난 슬라이스: `0020`~`0028`(37-B·55-B·66·69·61-B·23-A·34-B·34-C·114) — 모두 보리 승인 뒤 SQL Editor에서 실행.

### 목표 상태 (기대 동작)
1. 플레이북에 "DB 변경이 있는 슬라이스의 배포 순서" 절이 생기고, 순서·실패 때 처리·확인 방법이 적혀 있다.
2. DB 변경이 있는 착수지시서에는 "배포 순서" 줄이 들어가야 한다는 것이 적혀 있다.
3. `AGENTS.md` §9 끝에 플레이북 §9를 가리키는 한 줄(아래 문구)이 있다 `[확인: 2026-10-27 보리 — "AGENTS.md §9 끝에 그 한 줄 더해"]`.

### 건드릴 파일
- `drivelog-docs/docs/testing-playbook.md` — 맨 끝에 §9 새로(아래 문구 그대로).
- `drivelog-docs/AGENTS.md` §9 맨 끝에 한 줄(보리 승인, 위 목표 3) — 다른 줄은 손대지 않음.
- react-app·DB·공용 스타일 변경 없음.

추가할 문구(플레이북 맨 끝):
```markdown
## 9. DB 변경이 있는 슬라이스의 배포 순서 (2026-10-27 추가)

- **순서: ① SQL Editor에서 마이그레이션 실행 → ② 사후검증 `SELECT`로 기대 출력과 대조 → ③ 그다음 앱 push → CI 초록 → 배포본 확인.**
  앱을 먼저 올리면 그 사이 새 함수·칸·정책을 부르는 저장이 실패한다.
- 마이그레이션은 옛 앱도 그대로 돌게 쓴다(보존적, `AGENTS.md` §9) — 그래서 DB를 먼저 바꿔도 지금 배포본은 멀쩡하다.
  옛 함수·칸을 지워야 하면 새 앱 배포가 끝난 뒤 별도 마이그레이션으로.
- ①·② 중 하나라도 기대와 다르면 push하지 않는다. 되돌림 SQL(또는 다시 실행해도 안전한 수정본)과 수정 착수지시서를 써서 다시 승인받는다.
- 마이그레이션 파일과 그 앱 코드는 같은 코드 커밋에 넣고, 사후검증 `SELECT`와 기대 출력은 마이그레이션 파일 끝 주석에 둔다(예: `0028_save_day_log.sql`).
- 착수지시서에 **"배포 순서"** 줄을 꼭 넣는다 — 무엇을 먼저 실행하고 무엇을 확인한 뒤 push하는지, 앱을 먼저 올리면 무엇이 실패하는지.
- 서버에 어디까지 적용됐는지는 자동으로 알 수 없다 — 착수 전 읽기 진단(`AGENTS.md` §9 ①)으로 그 함수·정책·칸이 지금 있는지 먼저 본다.
```

`AGENTS.md` §9 끝(`- 절차: ① 읽기전용 …` 줄 바로 아래)에 더할 한 줄:
```markdown
- 배포 순서: SQL 실행·사후검증을 앱 push보다 먼저 — 상세는 `docs/testing-playbook.md` §9.
```

### 안 건드릴 것
- `AGENTS.md`의 위 한 줄 밖 — 축약·수정·삭제 없음(편집권 보리, §10).
- 플레이북 §1~§8·머리말 — 그대로.
- 마이그레이션 적용 기록을 자동으로 남기는 장치 — 로드맵 121에서.

### 검증
- 문서만 바뀜 — 테스트·CI 대상 아님. 바뀐 파일이 `testing-playbook.md`·`AGENTS.md` 둘뿐이고 `AGENTS.md`는 한 줄 추가뿐인지 `git diff --stat`·`git diff -- AGENTS.md`로 확인.
- 위 문구가 지난 실제 순서(114 지시서 "DB 실행·사후검증 → 앱 push")와 같은지 대조.

### 파일 길이
- `testing-playbook.md` 126 → 약 140줄(문서, 200줄 규칙은 코드 파일만).

### 실패 시 처리
- 새 장치 없음(문서 규칙만). 문구를 고쳐야 하면 지시서를 고쳐 다시 승인받음.

### 커밋
- 코드가 없어 커밋은 `[x]` 때 문서 커밋 1번(이 지시서·STATUS·로드맵과 함께).

### 사용자 확인 방법
1. `docs/testing-playbook.md` 맨 아래 §9를 열어 순서가 "SQL 실행 → 사후검증 → push"인지 봄.
2. `AGENTS.md` §9 끝에 그 한 줄이 있고, 다른 줄은 그대로인지 봄.

### 정한 것
- `AGENTS.md` §9에 한 줄 더함 `[확인: 2026-10-27 보리 — "AGENTS.md §9 끝에 그 한 줄 더해"]` — 플레이북은 저장 경로를 건드릴 때만 여는 문서라(`AGENTS.md` §4 트리거) DB만 바꾸는 슬라이스(예: 34-B)에서는 안 열릴 수 있어서.

### 진행 기록
- 교차검증: 플레이북 126줄·§8까지·DB 순서 문구 없음, `AGENTS.md` §9 절차 줄 위치, 114 순서 문구(drivelog-docs `5f55ee1` "DB `0028` 실행·사후검증 → 앱 push", react-app `3505ff6`), 예시 파일 `0028_save_day_log.sql` 있음 — 모두 일치. 고친 점 없음.
- 플레이북 끝에 §9 추가(126 → 137줄), `AGENTS.md` §9 끝 한 줄 추가. `git diff --stat` 두 파일뿐, `AGENTS.md`는 추가 1줄뿐.
- 데스크톱 AI 확인(2026-10-27): 플레이북 §9 10줄이 지시서 문구와 글자까지 같음(줄마다 비교), `AGENTS.md`는 §9 "절차" 줄 바로 아래 한 줄 추가뿐·문구 지시서와 같음, react-app 변경 없음. 목표 1·2·3 충족.
- 미커밋. 남은 것: 보리 최종 승인 → `[x]` 문서 커밋 1번.

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
