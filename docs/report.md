# docs/report.md — 현재 슬라이스 착수지시서

127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 128 차량 창 저장(기사 연동 탭)이 기사 계약 종료일을 지움 `[x]`

폴더별 전수점검(2026-10-26) M1(높음), 로드맵 128 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 순서 ① 127 → 135 중 두 번째. 앞 슬라이스 127 `[x]`(react-app `f87c75f`). **DB 변경 없음**(앱 코드만).

### 쉬운 요약
- 기사가 배정된 기사 차량을 차량 관리에서 열어 톤수 같은 것만 고쳐 저장해도, 앱이 그 기사의 계약 종료일을 빈 값으로 다시 저장합니다. 차량 창에는 종료일 칸이 없는데 늘 "종료일 없음"으로 넘기기 때문입니다.
- 그러면 기사 관리의 계약 기간이 "종료일 없음"이 되고, 계약 기간 밖 안내가 사라지며, 서버에서 기사가 볼 수 있는 기간도 끝없이 늘어납니다.
- 기존 기사를 고칠 때는 원래 종료일을 그대로 넘깁니다. 새 기사를 초대할 때는 지금처럼 종료일 없음.

### 현재 상태 (코드 확인, 2026-10-28)
- `features/cars/api/carInviteFromDraft.js:50-62` `saveInviteAfterVehicle`: 배정 정보를 `upsertDriver`에 넘길 때 `endDate: ''` 고정(`:58`).
- `domain/drivers.ts:109-112` `upsertDriver`: 고칠 때 `{ ...기존, ...새 값 }`이라 빈 종료일이 기존 종료일을 덮음. 서버 저장은 `data/sync/requestDriverInviteSave.ts:81` `assignmentEnd = driver.endDate || null` → `assignment_end = null`.
- 차량 창에는 계약 시작일·종료일 칸이 없음(`features/cars/ui/CarFormModal.jsx` — `inviteStartDate`는 타입 설명에만 있고 화면 칸 없음, `grep inviteStartDate features/cars/ui`). 시작일은 수정 창을 열 때 기존 값을 채워 둬서(`features/cars/ui/CarListPage.jsx:75` `linked?.startDate`) 안 바뀜.
- 이 길을 부르는 곳은 `features/cars/hooks/useCarListActions.js:90` 한 곳(`grep saveInviteAfterVehicle`), "기사 연동" 탭으로 저장할 때만(`:89` `skipInvite = connectMode === 'log'`). 연동 중·초대 대기 기사 모두 해당.
- 실패 테스트 기록: `docs/full-code-review-failing-tests.md` M1(재현됨, `'' !== '2026-12-31'`).

### 목표 상태 (기대 동작)
1. 기존 기사가 배정된 차량을 "기사 연동" 탭으로 저장하면 그 기사의 계약 종료일이 그대로다(Store·서버 모두).
2. 계약 시작일도 그대로다(지금과 같음 — 확인만).
3. 새 기사 초대(기존 기사 없음)는 지금처럼 종료일 없음.
4. 이름·전화·초대 코드·차량번호 저장은 지금과 같다.

### 건드릴 파일
- `react-app/src/features/cars/api/carInviteFromDraft.js` — `inviteDriverId`로 `drivers`에서 기존 기사를 찾아 `endDate: existing?.endDate || ''`. 주석 최종 문구: `// 차량 창에는 종료일 칸이 없어 기존 기사의 종료일을 그대로 넘긴다.`
- 새 `react-app/src/features/cars/api/carInviteEndDate.test.js` — `requestDriverInviteSave`를 바꿔치기해 넘긴 목록을 봄: ⓐ 계약 "2026-11-01 ~ 2026-12-31" 기사, 수정 창 초안 그대로(톤수만 고침) 저장 → 종료일 `2026-12-31`·시작일 `2026-11-01` 그대로(실패 테스트 기록 M1과 같은 모양) ⓑ 기존 기사 없는 새 초대 → 종료일 `''`.
- 공용 스타일·공용 값 변경 없음(`upsertDriver`는 안 바꿈).

### 안 건드릴 것
- `domain/drivers.ts` `upsertDriver` — 기사 관리 수정 창은 종료일 칸이 있어 "빈 값 = 종료일 지움"이 맞는 동작이라 그대로.
- `requestDriverInviteSave.ts` 서버 저장 — 넘겨받은 종료일을 그대로 씀(`:81`), 그대로.
- 차량 창에 종료일 칸 넣기 — 범위 밖(기사 관리에서 고침).
- 기존 `carInviteFromDraft.test.js` — 바꿔치기 없이 쓰는 파일이라 그대로 두고 새 파일로.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(§5 세션 — 이 길은 세션 확인을 `requestDriverInviteSave`가 맡음, 그대로).
- 새 테스트 ⓐ가 고치기 전 코드에서 실패하는지 먼저 확인(기록 M1처럼 `'' !== '2026-12-31'`), 고친 뒤 통과. 기존 `carInviteFromDraft.test.js` 그대로 통과. 로컬 `npm test` 전체 통과.
- 테스트·타입·빌드·코드 검사는 CI가 봄. 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `carInviteFromDraft.js` 84 → 약 87줄, 새 `carInviteEndDate.test.js` 약 60줄.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음 — 넘기는 값 하나만 바꿈). 실패하면 수정 착수지시서를 다시 써서 승인받음.

### 사용자 확인 방법
1. 배포본 로그인 테스터 계정 → 기사 관리에서 기사 차량(예: 99가9998) 기사의 계약 종료일을 확인(없으면 하나 넣고 저장).
2. 차량 관리 → 그 기사 차량 [수정] → "기사 연동" 탭 그대로 톤수만 바꿔 저장.
3. 기사 관리로 돌아가 새로고침 → 계약 종료일이 그대로면 성공. 확인 뒤 톤수·종료일을 원래대로 되돌림.

### 누가 하나
- 데스크톱 AI가 앱 코드·코드 커밋까지, push는 보리 `[확인: 2026-10-28 보리 — "확정, 너가 진행"]`.

### 진행 기록
- 코드: react-app `077e00a`(파일 2개, 지시서 "건드릴 파일" 그대로, 주석은 지시서 최종 문구 그대로). 줄 수 `carInviteFromDraft.js` 86, 새 `carInviteEndDate.test.js` 64.
- 고치기 전 실패 확인: `carInviteFromDraft.js`만 마지막 커밋으로 되돌려 새 테스트 실행 → 2개 중 ⓐ 실패(ⓑ는 "지금과 같음"이라 통과가 맞음), 복구 뒤 통과.
  ```
  ✖ 기존 기사가 있는 차량을 톤수만 고쳐 저장해도 계약 시작일·종료일이 그대로다
      actual: '',
      expected: '2026-12-31',
  ℹ tests 2  ℹ pass 1  ℹ fail 1  (exit=1)
  ```
- 로컬: `test:unit` 919 통과·`test:app` 901 통과(실패 0), `typecheck`·`lint` 통과.
- push(보리) 뒤 CI verify·deploy 초록(run 40624409123).
- 배포본(로그인 테스터 탭, getdrivelog.com `/app/cars`, 새 묶음 파일 `index-CU0By1lL.js`): 이 계정에 연동 중·초대 대기 기사가 없음(서버 `driver_links`는 연결 해제된 22가2222 한 줄, 2026-10-12 ~ 2026-10-22). 22가2222 [수정]은 "운행일지" 탭으로 열려(기존 기사로 안 잡힘) 고친 길을 타지 않음 → 저장하지 않고 [취소]. 그래서 "사용자 확인 방법" 1~3은 못 함 — 테스트 ⓐ로만 확인.
- 보리 지시("시험 기사 만들어 확인")로 다시 확인: 기사 관리 [+ 초대] → "128시험"(010-0000-1280)·99가9998·계약 2026-11-01 ~ 2026-12-31 저장 → 차량 관리 99가9998 [수정]이 "기사 연동" 탭으로 열림 → 톤수만 5 → 6 저장, 알림 "기사 배정 정보를 수정했습니다."(고친 길을 탐) → 새로고침 뒤 기사 관리 계약기간 `2026-11-01 ~ 2026-12-31` 그대로, 서버 `driver_links` 그 줄 `assignment_end = 2026-12-31`(저장 시각 갱신됨). 정리: [초대 취소]("초대를 삭제했습니다.") → 99가9998 톤수 5·기사명 "66시험기사"로 되돌려 "운행일지" 탭 저장 → 새로고침 뒤 기사 관리 초대 0, 차량 "99가9998 [66시험기사] 운행일지 (5톤)". 차량의 기사 전화는 원래 값을 몰라 "010-0000-1280"으로 남음(시험 값). 정리 뒤 서버 읽기는 연결 응답 없음으로 못 함 — 새로고침 화면(서버에서 불러온 값)으로 확인.
- §5 점검: ① 범위 — 바뀐 파일 2개가 "건드릴 파일"과 같음 ② 새 저장 키·큐·대비 장치 없음(넘기는 값 하나만) ③ 새 `any`·`unknown`·무시 주석 없음 ④ 200줄 넘는 파일 없음 ⑤ 기존 테스트 지우거나 약화한 것 없음(테스트 diff에 지운 줄 0) ⑥ 공용 값 변경 없음 — `upsertDriver`는 그대로, 바뀐 건 `saveInviteAfterVehicle` 안의 넘기는 값뿐(호출처 `useCarListActions.js:90` 한 곳) ⑦ 목표 1·3(새 테스트 ⓐ·ⓑ)·2(ⓐ의 시작일 확인)·4(기존 `carInviteFromDraft.test.js` 통과) 충족, 목표 1은 배포본 시험 기사로도 확인.

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
