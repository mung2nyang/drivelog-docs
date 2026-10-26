# docs/report.md — 현재 슬라이스 착수지시서

114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 125 오늘 파렛트만 입력해도 "오늘 작성한 운행일지가 없습니다" 알림 `[x]`

보리 QA 제보(2026-10-26) `[확인: 2026-10-26 보리 — QA 제보]`. 셀프 테스트 제보라 117 앞에 끼움(로드맵 맨 위 순서). 앞 슬라이스 114 `[x]`(react-app `3505ff6`).

### 쉬운 요약
- 오후 6시가 지나면 "오늘 일지를 안 썼다"는 알림이 뜨는데, 파렛트만 입력한 날도 안 쓴 날로 봅니다.
- 알림 판정에 파렛트 장수도 넣어, 파렛트만 입력해도 알림이 뜨지 않게 합니다.
- 바뀌는 건 이 알림 하나뿐이고 저장·금액은 그대로입니다.

### 현재 상태 (코드 확인)
- 판정이 휴무·콜 상세·고정 노선 횟수만 보고 파렛트 장수(`palletCount`)는 안 봄 — `features/notifications/api/notifications.js:113-117`.
- 파렛트만 있는 날도 일지로 저장됨 — `domain/day-record.ts:140` 빈 날 판정에 `pallets === 0`이 들어 있음.
- 같은 판정을 하는 공용 함수는 없음 — `domain/`에서 `export function (has|is)…` 검색 결과 `isOffDay`·`isInspectionEmpty`뿐. 그래서 이 파일 판정에 한 줄을 더함(새 공용 함수 안 만듦).
- 판정은 메인 일지만 봄(`workDataByLogId.main`, `:107`) — 그대로 둠.

### 목표 상태 (기대 동작)
1. 오후 6시 뒤 오늘 기록에 파렛트가 1장 이상이면 "오늘 작성한 운행일지가 없습니다" 알림이 안 뜬다.
2. 파렛트가 0이고 휴무·콜·고정 노선도 없으면 지금처럼 뜬다.
3. 휴무·콜·고정 노선이 있으면 안 뜨는 것, 오후 6시 전에는 안 뜨는 것은 그대로다.

### 건드릴 파일
- `react-app/src/features/notifications/api/notifications.js` — 판정에 파렛트 한 줄, 기록 타입 표기 두 곳(`:109`·`:111`)에 `palletCount` 칸 추가.
- `react-app/src/features/notifications/api/notifications.test.js` — 테스트 1개 추가(아래 코드).
- 공용 스타일·공용 값 변경 없음.

```js
  test('18시 이후: 파렛트만 있어도 알림이 없다', () => {
    mock.timers.enable({ apis: ['Date'], now: new Date(2026, 8, 5, 20, 0, 0).getTime() })
    const ownerKey = 'today-owner-pallet'
    commitWorkData(ownerKey, { [todayKey()]: { palletCount: 3 } })
    assert.equal(collectNotifications(ownerKey).some((item) => item.id.startsWith('today:')), false)
  })
```

### 안 건드릴 것
- 일지 저장 규칙(`domain/day-record.ts`) — 이미 파렛트 있는 날을 남김(`:140`).
- 홈 오늘 카드·달력 — 이미 파렛트를 반영함(로드맵 109-A·109-B `[x]`).
- 알림 문구·시간(오후 6시)·닫기, 기사 차량 일지(지금도 메인만 봄 — 범위 밖).

### 검증
- 새 테스트가 고치기 전 코드에서 실패하는지 먼저 확인(고치기 전 실패 확인), 고친 뒤 통과.
- 테스트·타입·빌드·코드 검사는 CI가 봄.
- 플레이북: 해당 없음 — 저장·삭제·동기화 없이 기록을 읽어 알림만 만듦(AGENTS §4 판별 한 줄).
- 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `notifications.js` 174 → 175줄(200 이하).
- `notifications.test.js` 144 → 약 151줄(테스트 파일 예외).

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음). 실패하면 수정 착수지시서를 다시 써서 승인받음.
- 새 주석 없음(판정 줄만 추가).

### 사용자 확인 방법
1. 오후 6시 이후, 고정 노선·파렛트가 켜진 로그인 계정에서 오늘 일지를 엶.
2. 운행 횟수 0, 콜 없이 파렛트만 1장 넣고 저장 표시를 봄.
3. 홈 위 종 모양 알림에 "오늘 작성한 운행일지가 없습니다"가 없으면 성공.
4. 파렛트를 0으로 되돌리면 그 알림이 다시 뜨는지 봄.

### 진행 기록
- 2026-10-26 교차검증: 파일·줄(`notifications.js:107·109·111·113-117`, `day-record.ts:140`)·현재 동작 모두 지시서와 일치, 고친 점 없음.
- 고치기 전 실패 확인: 새 테스트 1개만 실패(그 외 10개 통과). 고친 뒤 이 파일 11개 통과, 로컬 `npm test` 전체 통과.
- 코드 커밋 react-app `05d4723`, push 뒤 CI verify·deploy 초록(run 40325914654).
- 배포본 확인(2026-10-26 21:55 뒤, 데스크톱 AI, 로그인 테스트 계정): 10-26 일지가 파렛트 5장·고정 0·콜 없음인 상태에서 알림 "알림이 없습니다"(목표 1) → 파렛트 0으로 바꾸자 "오늘 작성한 운행일지가 없습니다" 뜸(목표 2) → 5장으로 되돌림, 다시 불러와 5장·25,000원·알림 없음 확인.
- §5 점검: ① 범위 — 바뀐 파일 `notifications.js`·`notifications.test.js` 2개, 지시서와 같음 ② 새 파일·저장 장치 없음 ③ `any`·`unknown`·`@ts-ignore` 없음 ④ `notifications.js` 175줄 ⑤ 새 테스트 1개, 기존 테스트 그대로 ⑥ 공용 클래스·상태값 변경 없음 ⑦ 목표 1·2는 배포본, 3은 기존 테스트로 확인.

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
