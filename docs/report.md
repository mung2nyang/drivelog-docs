# docs/report.md — 현재 슬라이스 착수지시서

135-B: drivelog-docs `b15f2f8`. 135-A: drivelog-docs `56ca240`. 134: drivelog-docs `baee1ea`. 133: drivelog-docs `e049969`. 132-B: drivelog-docs `e3b7b60`. 132-A: drivelog-docs `0d32ec4`. 131-B: drivelog-docs `bac091a`. 131-A: drivelog-docs `448deb1`. 130: drivelog-docs `83e93be`. 129-B: drivelog-docs `fc2b077`. 129-A: drivelog-docs `867b653`. 128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

## 로드맵 136 세금계산서에서 휴무일 콜·고정 노선 빼기 `[x]`

전수점검(2026-10-26) B1, 로드맵 ②(돈·세무 금액). 근거 sot §4-13 "휴무일 콜은 모든 계산에서 제외 — … 세금계산서 … 전부"(보리 결정 2026-10-15, react-app `7395b21`). 그때 세금계산서 묶음 함수 한 곳이 빠졌음. DB 변경 없음.

### 쉬운 요약
- 휴무로 바꾼 날에 콜이나 고정 노선 횟수가 남아 있으면, 지금은 세금계산서 금액에 그 운행이 들어갑니다. 달력·매출·미수금·내역서는 이미 빼고 있어서 세금계산서만 금액이 큽니다.
- 세금계산서도 휴무일을 빼서 다른 화면과 같은 금액으로 맞춥니다. 운비서에게 "○○ 거래처 매출"을 물었을 때의 답도 같은 계산을 써서 함께 맞춰집니다.

### 현재 상태 (코드 확인)
- `domain/financeTaxInvoiceGroups.ts:51-84` `getTaxInvoiceSourceGroups` — 날마다 콜(`:52-62`)·고정 횟수(`:64` `record.fixedCount`를 그대로 읽음)·파렛트(`:77`)를 더하는데 `isOff`를 보지 않음. 파렛트만 `getPalletCount`(`day-record.ts:17-20`, 휴무면 0)를 거쳐 이미 빠지고, 콜과 고정 노선은 들어감.
- 다른 합계는 모두 휴무 날을 통째로 건너뜀: `financeCore.ts:91`·`:141`, `driverShareBase.ts:28`, 같은 파일 `flattenLinkedDriverTrips`(`:106`, 기사 관리 거래처 계산서 카드), 내역서(`features/documents/api/report.test.js:141` "isOff 레코드는 완전히 제외된다").
- 부르는 곳(grep `getTaxInvoiceSourceGroups`, 운영 코드 2곳): 서류 발급 세금계산서 목록 `financeTaxInvoiceEntries.ts:100`, 운비서 거래처 매출 `features/home/api/unbiseoNumbers.ts:47`.
- 화면 금액은 열 때마다 다시 계산함(`financeTaxInvoiceEntries.ts:75-76` — 저장값 위에 계산값을 덮음).

### 목표 상태 (기대 동작)
1. 휴무 날의 콜·고정 노선·파렛트는 세금계산서 묶음(공급가액·부가세·건수)에 들어가지 않음. 메인 차량·기사 차량 모두.
2. 휴무 날 콜만 있던 거래처는 그달 세금계산서 목록에서 빠짐(묶음이 생기지 않음).
3. 휴무를 끄면 다시 들어감(콜은 지우지 않으므로 — sot §4-13).
4. 휴무가 아닌 날 계산은 그대로(기존 테스트 그대로 통과).
5. 운비서 거래처 매출 답이 같은 기준으로 바뀜(같은 함수라 코드 변경 없음).
6. 이미 발급 완료로 저장한 계산서: 화면은 열 때 다시 계산한 금액, 서버 저장값은 다시 저장할 때 바뀜(sot §4-14와 같은 방식). 휴무 날 콜만 있던 거래처의 발급 완료분은 지금처럼 저장된 것으로 남음(`financeTaxInvoiceEntries.ts:102-107`).

### 건드릴 파일
- `src/domain/financeTaxInvoiceGroups.ts` — `:51` 날마다 돌 때 휴무 날은 바로 건너뜀(`if (record?.isOff) return`). 주석 최종 문구: `// 휴무 날은 콜·고정 노선·파렛트 모두 빼기(다른 합계와 같음).`
- `src/domain/finance.test.js` — 새 테스트: 휴무 날 콜·고정 노선이 빠짐, 휴무만 있던 거래처는 묶음 없음, 휴무를 끄면 다시 들어감, 기사 차량도 같음, 달력 정산(`monthSettlementSummary`)과 같은 금액.

### 안 건드릴 것
- `financeTaxInvoiceEntries.ts`·`unbiseoNumbers.ts` — 같은 함수를 불러 따라 바뀜(위 grep).
- `flattenLinkedDriverTrips`·`getLinkedDriverClientInvoiceGroups`(같은 파일 `:96-178`) — 이미 휴무를 뺌(`:106`).
- 서버에 저장된 계산서 값, DB. 휴무 확인 창(`OffToggle.jsx`).

### 검증
- `npm test`, 타입 검사, 코드 검사, 빌드(CI).
- 새 테스트가 고치기 전 코드에서 실패하는지 확인(revert-check).
- 배포본: 아래 확인 방법.

### 진행 기록
- 2026-10-30 보리 "확정, 진행" → 데스크톱 AI가 코드. react-app `fd8b2bd`(push 안 함).
- 로컬: 테스트 940·922 모두 통과, 타입 검사·코드 검사·빌드 통과. 고치기 전 실패 확인 — 새 테스트 3개 중 2개(메인·기사 차량, 달력 정산과 같은 금액)는 고치기 전 코드에서 실패, "휴무를 끄면 다시 들어간다"는 안전 확인.
- 브라우저: 로컬 탭은 비회원이라 세금계산서 자료 없음 — 배포본은 push 뒤 확인.
- push(보리) 뒤 CI verify 빨강(run 40996846024) — 새 테스트 "메인·기사 차량 모두 휴무 날은 빠지고…" 1개 실패. 금액·건수는 모두 맞고 줄 순서만 다름.

### 수정 착수지시서 1 — 새 테스트의 줄 세우기가 컴퓨터 언어 설정에 따라 달라짐
- **무엇이 왜 실패했나**: 테스트 `rows`가 묶음 이름을 `localeCompare`로 줄 세움. 이 함수는 컴퓨터 언어 설정을 따라, 한국어 설정(로컬 Windows)에서는 한글이 영어보다 앞("대한__서울99가1111" → "대한__main"), CI(리눅스, 영어 설정)에서는 영어가 앞("대한__main" → "대한__서울99가1111"). 앱 코드 문제 아님(앱은 이 순서를 쓰지 않음, 테스트 안에서만 줄 세움).
- **고칠 것**: `src/domain/finance.test.js` 새 describe의 `rows`만 — 줄 세우기를 글자 번호 비교(`String(a[0]) < String(b[0]) ? -1 : 1`)로 바꾸고, 첫 테스트 기대 순서를 그 기준(영어가 한글보다 앞: `대한__main`, `대한__서울99가1111`, `한진__main`, `한진__서울99가1111`)으로. 두 번째 테스트(대한·한진·휴무만)는 어느 설정에서도 같은 순서라 그대로.
- **안 건드릴 것**: `financeTaxInvoiceGroups.ts`(앱 코드), 다른 테스트.
- **검증**: 로컬 `finance.test.js`·전체 테스트, 수정 커밋 하나 더(이력 되감기 없음) → 보리 push → CI 초록.
- **실패 시 처리**: 새 장치 없음, 다시 실패하면 수정 착수지시서 2.
- 진행: 보리 "확정, 진행" → react-app `e2b7058`(테스트만, push 안 함). 로컬 `finance.test.js`가 한국어·영어(`LC_ALL=en_US.UTF-8`) 설정 모두 통과, 전체 테스트 940·922·타입·코드 검사 통과.
- push(보리) 뒤 CI verify·deploy 초록(run 40998140026).
- 배포본(getdrivelog.com, 로그인 계정, 데스크톱 AI — 보리 지시): 10월 세금계산서 테스터거래처 공급가액 2,373,456원·22건 → 10-13(콜 1건 823,456원, 고정·파렛트 0이라 휴무를 켜도 지워지는 값 없음) 휴무로(확인 창) → 1,550,000원·21건·세액 155,000원 → 휴무 끄기 → 2,373,456원으로 돌아옴, 콘솔 오류 없음.

### 파일 길이
- `financeTaxInvoiceGroups.ts` 178 → 180. 200줄 아래.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음. 계산만 바꿈. 검증이 실패하면 수정 착수지시서를 써서 다시 승인받음.

### 사용자 확인 방법 (배포본, 로그인 계정)
1. 서류 발급 → [세금계산서] 탭에서 10월 "테스터거래처" 공급가액을 적어 둠.
2. 10월 13일 일지 → 휴무로 바꿈(콜이 있어 확인 창 → 확인).
3. 세금계산서 탭으로 돌아가면 그 거래처 공급가액이 13일 콜 금액만큼 줄어 있음(달력 10월 정산과 같은 방향).
4. 13일 휴무를 다시 끄면 금액이 원래대로.

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
