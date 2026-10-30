# docs/report.md — 현재 슬라이스 착수지시서

138-A: drivelog-docs `dd2c4a4`. 140: drivelog-docs `e861ea4`. 139: drivelog-docs `846327e`. 137: drivelog-docs `49613a4`. 136: drivelog-docs `e01b338`. 135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 138-B 기사 차량 "건당" 수수료 — 차량 창·저장에서 빼기 `[x]`

보리 "건당을 지우는게 낫지않아?", "응 그렇게 진행해"(2026-10-30), 전수점검 M2. 138-A(계산·표시, react-app `0be8c3b`, sot §4-23) 다음 조각, **이번이 마지막**. DB 변경 없음. 거래처 수수료의 "금액(건당)" 방식은 그대로.

### 쉬운 요약
- 옛날에 건당으로 저장된 기사 차량을 차량 창에서 열면 수수료 칸에 "5000 %"가 보이고, 숫자 하나만 고쳐도 100으로 잘려 저장될 수 있습니다.
- 이제 그런 차량은 수수료 칸이 빈 채로 열리고, 저장하면 언제나 비율(%)로 저장됩니다.
- 서버에는 건당 기사 차량이 0대라 회원이 볼 일은 없고, 비회원 휴대폰에 남은 옛 자료를 위한 정리입니다.

### 현재 상태 (코드 확인)
- 창 열기 `features/cars/ui/CarListPage.jsx:71-72` — 저장된 `commType`(건당이면 `'direct'`)과 수수료 값을 그대로 창에 넣음.
- 차량 창 `CarFormModal.jsx:155-162` — 수수료 칸은 비율 입력뿐(`onCommissionChange` → `formatPercentInput`, 100에서 자름), 뒤 글씨 "%". 168줄은 건당이면 "기사 몫 기준" 고르기를 숨김.
- 저장 직전 정리 `domain/carDriverFields.ts:52` — 창에서 온 `commType`이 `'direct'`면 그대로 `'direct'`로 저장.
- 테스트 `CarFormModal.test.js:122-136` — 건당 차량이면 "기사 몫 기준"을 숨기는지 확인.

### 목표 상태 (기대 동작)
1. 옛 건당 기사 차량을 수정 창으로 열면 수수료 칸이 비어 있음("5000" 안 보임), "기사 몫 기준" 고르기가 보임.
2. 비율 기사 차량은 지금처럼 저장된 값("10")으로 열림.
3. 기사 차량을 저장하면 `commType`은 언제나 `'percent'`(창에서 무엇이 오든).
4. 월급제·메인 차량, 거래처 수수료는 그대로.

### 건드릴 파일
- `src/features/cars/ui/CarListPage.jsx` — 창 열 때 `commType: 'percent'`, 수수료는 옛 건당이면 빈 칸. 주석 최종 문구: `// 옛 건당 자료는 수수료 빈 칸으로 연다.`
- `src/domain/carDriverFields.ts` — 52줄 지우고 저장 값 `commType: 'percent'`.
- `src/features/cars/ui/CarFormModal.jsx` — 168줄 `draft.commType !== 'direct' &&` 지움.
- 테스트:
  - `features/cars/ui/CarFormModal.test.js:122-136` — 건당 경우를 빼고 이름을 "월급제·메인 차량이면 드롭다운 숨김"으로.
  - `features/cars/ui/CarListPage.flows.test.js` — 새 테스트: 옛 건당 차량(수수료 5000)을 [수정]으로 열면 수수료 칸 빈 칸·"기사 몫 기준" 보임, 비율 차량(10)은 "10".
  - `domain/cars.test.js` — 새 테스트: `upsertCar`에 `commType: 'direct'`를 넣어도 `'percent'`로 저장.

### 안 건드릴 것
- 불러오기·저장 형식(`hydrateMergeCars.ts`·`hydrateEmployedDriverCar.ts`·`cloudStorage.ts`·`persistDomainRecords.ts`의 `COMM_TYPES`) — 거래처와 같이 쓰는 값이라 그대로, 서버 `comm_type` 칸도 그대로. 옛 자료는 계산에서 이미 수수료 미설정(138-A).
- 창 안의 `commType` 칸 자체(`CarFormModal.jsx` 자료 설명·`setRevenueMode`, `CarListPage.jsx` 빈 창 기본값, `vehicleMutations.ts` 형식) — 늘 `'percent'`라 그대로 둠, 칸을 없애면 저장 형식까지 바뀜.
- 거래처 수수료 창(`ClientTradeFields.jsx`), 운비서 거래처 등록.

### 검증
- `npm test`, 타입 검사, 코드 검사, 의존 검사, 빌드(CI).
- 새 테스트(빈 칸으로 열기, 저장은 비율)가 고치기 전 코드에서 실패하는지 확인(revert-check). 비율 차량 열기는 안전 확인.
- 로컬 비회원 화면: 기사 차량(비율 10%) 등록 → [수정] → 수수료 "10"·기사 몫 기준 보임 → 저장 → 목록 "수수료 10%" → 시험 차량 지움.
- 배포본: 아래 확인 방법.
- [x] 때 sot §4-23의 "차량 창·저장에서 건당 빼기는 로드맵 138-B" 문장을 결과로 바꿈, 로드맵 138 전체 `[x]`.

### 진행 기록
- 2026-10-30 보리 "응"(이대로 진행) → 데스크톱 AI가 코드. react-app `264d576`(push 안 함).
- 로컬: 테스트 950·926 모두 통과, 타입 검사·코드 검사·의존 검사(위반 0)·빌드 통과. 파일 길이 193·130·66.
- 고치기 전 실패 확인 — 새 테스트 2개(옛 건당 차량 빈 칸으로 열기, 건당 값이 와도 비율로 저장) 모두 고치기 전 코드에서 실패. 바꾼 차량 창 테스트(월급제·메인 숨김)는 안전 확인. 되돌린 파일 3개 원래대로 복구 확인.
- 브라우저(로컬 5175, 비회원): 기사 차량 12가3456(5톤, 매출제 10%) 등록 → [수정] → 수수료 "10 %"·"기사 몫 기준" 보임 → 그대로 [저장] → 목록 "(5톤), 수수료 10%" → 시험 차량 지움, 콘솔 오류 없음.
- push 뒤 CI verify·deploy 초록(run 41026274009).
- 2026-10-30 보리 "응"(확정) → `[x]`, sot §4-23 결과 반영, 로드맵 138 전체 `[x]`.
- 배포본(getdrivelog.com, 로그인 탭): 차량 관리 22가2222 [수정] → 매출제·수수료 "20 %"·"기사 몫 기준" 보임 → [취소] → 목록 "(25톤), 수수료 20%" 그대로.
- §5 리뷰: 바뀐 파일 6개 = "건드릴 파일"과 같음, 새 파일·저장 키·대비 장치·타입 꼼수 없음, 새 주석은 지시서 문구 그대로("로드맵 61"은 원래 있던 테스트 이름에서 "건당"만 뺀 것), 지운 테스트 없음, 파일 길이 193·130·66, 목표 1~4 충족(1·3은 새 테스트, 2는 테스트·로컬·배포본, 4는 기존 테스트 통과).

### 파일 길이
- `CarFormModal.jsx` 193 → 193(200줄 감시 목록, 줄 안에서만 지움), `CarListPage.jsx` 129 → 약 130, `carDriverFields.ts` 67 → 약 66. 모두 200 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 창 열기·저장 값만 고침. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법
1. 배포본 차량 관리에서 비율 수수료 기사 차량(예: 22가2222) [수정] → 수수료 칸 "20", "기사 몫 기준" 고르기 보임.
2. 아무것도 바꾸지 않고 [취소] → 목록 "(25톤), 수수료 20%" 그대로.
3. (옛 건당 차량은 서버에 없어 화면에서 볼 일이 없음 — 빈 칸으로 열리는 것은 자동 테스트로 확인.)
