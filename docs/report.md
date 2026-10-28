# docs/report.md — 현재 슬라이스 착수지시서

128: drivelog-docs `9e32174`. 127: drivelog-docs `e743872`. 118: drivelog-docs `7cf86e3`. 116: drivelog-docs `8cade9e`. 115: drivelog-docs `6539d2d`. 122: drivelog-docs `48f420c`. 126: drivelog-docs `bfd19f7`. 117: drivelog-docs `8cbdca5`. 125: drivelog-docs `d48cac8`. 114: drivelog-docs `5f55ee1`. 34-C: drivelog-docs `11ea476`. 34-B: drivelog-docs `a54be26`. 34-A: drivelog-docs `58331f0`. 113-D: drivelog-docs `0bcb19a`. 113-C: drivelog-docs `afa3f5e`. 113-B: drivelog-docs `1c95e76`. 113-A: drivelog-docs `25069f9`. 운비서 버튼 설정: drivelog-docs `d65beaf`. 운비서·+ 버튼 겹침: drivelog-docs `6fe917d`. 로그인 화면 개편 B: drivelog-docs `ae6d734`. 로그인 화면 개편 A 수정: drivelog-docs `c712994`. 로그인 화면 개편 A: drivelog-docs `d5bc380`. 문구 정리 5-B: drivelog-docs `466871a`. 문구 정리 5-A: drivelog-docs `f4a3ee8`. UI 위계화 E-2: drivelog-docs `c03dba5`. UI 위계화 E-1: drivelog-docs `4b28cf6`. UI 위계화 D-1: drivelog-docs `acff677`. UI 위계화 C-2: drivelog-docs `182b8e4`. UI 위계화 C-1: drivelog-docs `c19c294`. UI 위계화 B: drivelog-docs `d43886f`. UI 위계화 A: drivelog-docs `ba4692a`. 112: drivelog-docs `bed3293`. 24-J-2: drivelog-docs `2659849`. 24-J-1: drivelog-docs `8e025d8`. 24-I-2: drivelog-docs `e79562a`. 24-I-1: drivelog-docs `cdeb64d`. 24-H-3: drivelog-docs `57ccb7e`. 24-H-2: drivelog-docs `9ffec36`. 24-H-1: drivelog-docs `09323b9`. 24-G-2: drivelog-docs `f606644`. 24-G-1: drivelog-docs `c0f52a6`. 24-D3: drivelog-docs `9dc244b`. 111: drivelog-docs `46dc63b`. 24-E2: drivelog-docs `db74acc`. 24-F-2: drivelog-docs `c23630f`. 24-F-1: drivelog-docs `8c1ddfb`. 24-E: drivelog-docs `f8c7a9a`. 110-B: drivelog-docs `acfa71b`. 110-A: drivelog-docs `b57e0b1`. 109-B: drivelog-docs `e200af2`. 109-A: drivelog-docs `a5b3d61`. 108번: drivelog-docs `6bfaa09`. 105번: 105-B drivelog-docs `705c4ac`, 105-A `aa5c8b9`. 24번(AI 비서) 지시서·기록: 24-D-2 drivelog-docs `44a068c`, 24-D-1 `ac195e4`, 24-C-2 `455cd19`, 24-C-1 `a26815e`, 24-A2 `2ea514e`, 24-B-3-B `0548640`, 24-B-3-A `a0f2b64`, 24-B-2 `c115e2b`, 24-B-1 `18646c9`, 24-A `1dde103`. 23번: 23-D `a6ee944`, 23-C `aedd08d`, 23-B `4e692ce`, 23-A `ac81245`.

---

## 로드맵 129-A 세금계산서 저장이 중간에 실패한 뒤 다시 저장하면 계산서가 한 번 더 생김 `[x]`

폴더별 전수점검(2026-10-26) A5, 로드맵 129 `[확인: 2026-10-26 보리 — "로드맵에 올려야겠지"]`. 순서 ① 127 → 135 중 세 번째. 앞 슬라이스 128 `[x]`(react-app `077e00a`). **DB 변경 없음**(앱 코드만).
**129를 둘로 나눔(AI 판단): 이번엔 129-A(A5 중복)만.** 129-B(A6 기사 차량 번호를 바꾼 뒤 계산서 저장이 계속 실패)는 고칠 곳(번호 변경 서버 함수·차량 찾기)이 달라 따로 지시서 — 같은 파일이지만 고치는 방법이 전혀 다름.

### 쉬운 요약
- 세금계산서 저장은 목록의 계산서를 하나씩 서버에 보냅니다. 새 계산서 두 장을 저장하다 둘째에서 끊기면, 첫째는 서버에 들어갔는데 앱은 그 사실을 버립니다(화면은 실패라 그대로).
- 그래서 다시 저장하면 앱은 첫째를 또 "새 계산서"로 보내 서버에 같은 계산서가 두 장 생깁니다. 저장은 됐는데 응답만 끊긴 경우도 똑같습니다.
- 새 계산서를 넣기 전에 서버에 같은 계산서(앱이 붙인 번호가 같은 것)가 이미 있는지 먼저 보고, 있으면 그 줄을 고칩니다. 끊긴 뒤 다시 저장해도 한 장만 남습니다.

### 현재 상태 (코드 확인, 2026-10-28)
- `data/sync/syncTaxInvoicesTable.ts:28-58` `syncTaxInvoices`: 목록 전체를 차례로 — 서버 번호(`supabaseId`)가 있으면 그 줄 update(`:47-51`), 없으면 insert 후 받은 번호를 지역 목록 `next`에만 붙임(`:53-55` `applyInsertedTaxInvoiceId`).
- 중간에 오류가 나면 던져서 `next`를 버림 → `data/sync/invoicesSave.ts:25-27`도 Store에 반영 안 함(실패면 화면 그대로 — 지출·일지 저장과 같은 규칙).
- 다시 저장하면 첫째는 여전히 서버 번호가 없어 또 insert → 중복. 실패 테스트 기록 `docs/full-code-review-failing-tests.md` A5(재현됨, `2 !== 1`).
- 서버 줄에는 앱이 붙인 계산서 번호가 `raw.id`로 들어감(`domain/taxInvoices.ts:57-70` `buildTaxInvoiceRow`의 `raw: item`), 불러올 때도 그 번호로 합침(`:73-84` `mergeTaxInvoiceRecords`).
- 부르는 곳: `features/documents/ui/TaxInvoiceTab.jsx:84-86` `persist`, `features/account/api/memberRestoreRecords.js:159`(회원 데이터 불러오기) — 둘 다 `saveInvoices` 한 길(`grep saveInvoices(`).

### 목표 상태 (기대 동작)
1. 서버 번호가 없는 계산서는 넣기 전에 서버에서 내 계산서 중 `raw.id`가 같은 줄을 1개 찾는다. 있으면 그 줄을 고치고 그 번호를 붙이고, 없으면 지금처럼 넣는다.
2. 둘째 계산서에서 끊긴 뒤 같은 목록으로 다시 저장하면 첫째는 서버에 한 장만 있다(넣기 1번, 고치기 1번).
3. 실패하면 지금처럼 화면·Store 그대로, 실패 알림. 성공하면 지금처럼 Store 반영.
4. 서버 번호가 있는 계산서 고치기는 지금과 같다(찾기 없음).

### 건드릴 파일
- `react-app/src/data/sync/syncTaxInvoicesTable.ts` — 서버 번호 없는 계산서만 `tax_invoices`에서 `user_id`·`raw->>id`로 `limit(1)` 찾기 → 있으면 update + 번호 붙이기, 없으면 insert. 주석 최종 문구: `// 넣기 전에 같은 계산서가 서버에 있는지 본다 — 앞 저장이 중간에 끊겼거나 응답만 잃었으면 다시 넣지 않고 고친다.`
- 새 `react-app/src/data/sync/invoicesSave.retry.test.js` — 가짜 서버가 넣은 줄을 기억(찾기는 `raw->>id`로): ⓐ 새 계산서 2장 중 둘째 넣기 실패 → 같은 목록으로 다시 저장 → 첫째 넣기 1번·고치기 1번, 둘째 넣기 1번, Store에 두 장 모두 서버 번호 ⓑ 응답만 잃은 경우(넣기는 됐는데 오류) → 다시 저장하면 넣기 1번뿐 ⓒ 서버 번호 있는 계산서는 찾기 0번.
- 공용 스타일·공용 값 변경 없음.

### 안 건드릴 것
- 129-B(A6, 기사 차량 번호를 바꾼 뒤 계산서 저장이 계속 실패) — 다음 슬라이스. 이번 변경 뒤에도 그대로 남음.
- `invoicesSave.ts` — 실패면 Store 그대로 두는 규칙 그대로(실패한 목록을 Store에 넣는 방법은 화면이 "저장된 것처럼" 보이게 돼서 쓰지 않음).
- 서버에 이미 생긴 중복 계산서 정리 — 범위 밖(있는지 모름, 불러올 때 같은 번호면 하나로 합쳐 보임 `mergeTaxInvoiceRecords`).
- DB에 "같은 번호는 한 줄만" 제약 추가 — DB 변경이라 안 함(찾기로 충분).
- 기존 `docs/full-code-review-failing-tests.md` A5 테스트 — 그 가짜 서버는 넣은 줄을 기억하지 않아(찾기가 늘 빈 결과) 이 방법으로는 통과 못 함. 같은 상황을 서버 줄을 기억하는 새 테스트 ⓐ로 확인.

### 검증
- 착수 전 `docs/testing-playbook.md` 열람(§5 세션 — 세션 확인은 `invoicesSave.ts`가 맡음, 그대로).
- 새 테스트 ⓐ·ⓑ가 고치기 전 코드에서 실패하는지 먼저 확인(넣기 2번), 고친 뒤 통과. 기존 `cloudMemorySave.test.js` 계산서 테스트·`domain/taxInvoices.test.js` 그대로 통과. 로컬 `npm test` 전체 통과.
- 테스트·타입·빌드·코드 검사는 CI가 봄. 화면 모양 변화 없음 — 시안 필요 없음.

### 파일 길이
- `syncTaxInvoicesTable.ts` 58 → 약 72줄, 새 `invoicesSave.retry.test.js` 약 90줄.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(신규 레이어 없음 — 넣기 전에 한 번 찾아봄). 찾기가 실패하면 지금처럼 오류·실패 알림. 실패하면 수정 착수지시서를 다시 써서 승인받음.

### 사용자 확인 방법
1. 끊김은 화면에서 만들 수 없어 테스트로만 확인.
2. 배포본 로그인 테스터 계정 → 서류 발급 → 세금계산서 → 계산서 하나 저장 → 새로고침 뒤 한 장만 보이면 성공(평소 저장 회귀 확인). 확인 뒤 그 계산서를 원래대로 되돌림.

### 누가 하나
- 데스크톱 AI가 앱 코드·코드 커밋까지, push는 보리 `[확인: 2026-10-28 보리 — "확정, 너가 진행"]`.

### 진행 기록
- 코드: react-app `f25d3e0`(파일 2개, 지시서 "건드릴 파일" 그대로, 주석은 지시서 최종 문구 그대로). 줄 수 `syncTaxInvoicesTable.ts` 70, 새 `invoicesSave.retry.test.js` 101(지시서 예상 약 90).
- 고치기 전 실패 확인: `syncTaxInvoicesTable.ts`만 마지막 커밋으로 되돌려 새 테스트 실행 → 3개 중 ⓐ·ⓑ 실패(ⓒ는 "지금과 같음"이라 통과가 맞음), 복구 뒤 통과.
  ```
  ✖ 둘째 넣기가 실패한 뒤 같은 목록으로 다시 저장하면 첫째는 고치기, 서버에 한 장씩
      actual: [ 'insert:inv-1', 'insert:inv-2', 'insert:inv-1', 'insert:inv-2' ],
      expected: [ 'insert:inv-1', 'insert:inv-2', 'update:inv-1:9001', 'insert:inv-2' ],
  ✖ 넣기는 됐는데 응답만 잃은 뒤 다시 저장하면 넣기 1번뿐
      actual: 2,
      expected: 1,
  ℹ tests 3  ℹ pass 1  ℹ fail 2  (exit=1)
  ```
- 로컬: `test:unit` 922 통과·`test:app` 901 통과(실패 0), `typecheck`·`lint` 통과.
- push(보리) 뒤 CI verify·deploy 초록(run 40626590244).
- 배포본 확인 전 서버 읽기: 이 계정 `tax_invoices` 0줄. 서류 발급 → 세금계산서(새 묶음 파일 `index-CRpBnTNK.js`)는 10월 "테스터거래처" 작성 전 1건(일지에서 계산한 것, 아직 저장 안 된 계산서). [수정하기] → 저장하면 서버에 새 줄이 생기고 화면에서 지울 길이 없어, 보리 확인 전엔 저장하지 않음.
- 보리 지시("저장하고 서버에서 지워")로 확인: [수정하기] → 바꾸지 않고 [저장] → "세금계산서 작성 내용을 저장했습니다." → 서버 1줄(`raw.id = sales|2026-10|테스터거래처__main`, draft) → 새로고침 뒤 화면 1건 그대로 → 한 번 더 [저장] → 서버 여전히 1줄. 정리: 그 줄을 SQL로 지움(`delete … where id = 'a28b4876-…' and user_id = 테스터`, 1줄) → 새로고침 뒤 화면은 처음과 같음(작성 전 1건, 일지에서 계산한 것), 기기 저장소에 계산서 키 없음. 끊김(찾기로 고치는 길)은 화면에서 만들 수 없어 테스트로만 — 배포본은 평소 저장이 한 줄만 만드는지(회귀)만 확인. 요청 기록 창은 이 요청을 못 잡음.
- §5 점검: ① 범위 — 바뀐 파일 2개가 "건드릴 파일"과 같음 ② 새 저장 키·큐·대비 장치 없음(넣기 전에 한 번 찾기) ③ 새 `unknown`은 새 테스트의 `field` 도우미 1곳(`syncExpenseItems.test.js`의 같은 도우미를 그대로 씀), 소스 파일에는 없음 ④ 200줄 넘는 파일 없음 ⑤ 기존 테스트 지우거나 약화한 것 없음(테스트 diff에 지운 줄 0) ⑥ 공용 값 변경 없음 — 바뀐 건 `syncTaxInvoices` 안뿐, 부르는 곳 `saveInvoices` 한 길 ⑦ 목표 1·2(새 테스트 ⓐ·ⓑ)·3(ⓐ의 실패 뒤 Store 그대로)·4(ⓒ) 충족.

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
