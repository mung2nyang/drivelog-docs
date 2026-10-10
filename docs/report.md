# docs/report.md — 현재 슬라이스 착수지시서

## 군더더기 정리 3 — 안 쓰는 CSS 클래스 지우기 `[x]`

보리 요청 2026-10-10(로드맵 밖). 근거: `docs/code-cleanup-review.md` B3. 앞 슬라이스 2-B `[x]`(react-app `b644be8`).

### 현재 상태 (코드 확인)
화면 코드(js·jsx·`index.html`) 어디에도 이름이 안 나오는 클래스 26개. 근거: 클래스마다 `grep -rl <이름> src index.html`(CSS 제외) → 0건, 이름을 조립해 만드는 코드(`` `auth-${…}` `` 등)도 없음(2026-10-10 재확인).

| 파일 | 지울 클래스 |
|---|---|
| `account-flow.css`(573줄) | `auth-question-text`, `auth-secondary-btn`, `auth-role-tabs`, `auth-role-tab`(+`.active`), `auth-role-tab-icon`(+`svg`), `auth-role-subtitle`, `auth-link-text`, `auth-invite-row`(+좁은 화면 규칙 571줄), `auth-invite-text`, `auth-code-box`, `onboarding-sub-toggle-card`, `onboarding-sub-copy`, `practice-home`(+`button`) |
| `revenue/revenue.css`(272줄) | `revenue-summary-card`·`-label`·`-amount`·`-sub`, `revenue-vehicle-list`, `revenue-vehicle-row`(+`:first-child`), `revenue-vehicle-meta`, `revenue-driver-select-chevron`(+`svg`) |
| `calendar/calendar.css` | `main-practice-note`, `main-practice-back` |
| `clients/client-management.css` | `client-inline-field`(+`> label`) |
| `day-log/call-detail-form.css` | `work-log-call-modal` |
| `customer-center.css`, `notice.css` | `support-card-label` (두 파일에 같은 규칙) |

### 목표 상태 (기대 동작)
1. 위 클래스의 규칙 삭제.
2. **다른 클래스와 한 줄로 묶인 규칙은 안 쓰는 이름만 빼고 규칙은 남김** — `revenue.css:33` `.revenue-vehicle-list, .revenue-year-list`, `:41` `.revenue-vehicle-row, .revenue-year-row`, `:52` `...:first-child` 세 곳은 `revenue-year-*`가 계속 씀.
3. `customer-center.css:1` 머리 주석의 클래스 목록에서 `.support-card-label`만 뺌.
4. **화면 변화 없음.** 쓰는 클래스의 값은 하나도 안 바꿈.

### 건드릴 파일 (CSS 7개)
`src/account-flow.css`, `src/components/revenue/revenue.css`, `src/components/calendar/calendar.css`, `src/components/clients/client-management.css`, `src/components/day-log/call-detail-form.css`, `src/components/customer-center.css`, `src/components/notice.css`
- 슬라이스 크기 기준(1~3개)보다 많지만 전부 지우기만이라 한 번에 묶음(2-B와 같은 판단).

### 안 건드릴 것
- 쓰는 클래스 규칙 전부. §5 6번(공용 클래스 영향범위): 바꾸는 속성 없음 — 안 쓰는 클래스 규칙만 없어지므로 다른 화면 영향 없음.
- 200줄 넘는 CSS(`account-flow.css`·`revenue.css`·`call-detail-form.css`·`calendar.css`)의 나누기 — 이번엔 지우기만.

### 테스트
- 지울 클래스 이름을 검사하는 테스트 없음(근거: 26개 이름으로 `*.test.js` 검색 → 0건). 새 테스트 없음. 로컬 `npm test`·`typecheck`·`build` 통과로 확인.
- 플레이북 트리거 해당 없음(CSS만).

### §6 200줄
- 삭제만 있음. 200줄 넘는 4개 파일은 줄어듦(이번에 200 이하로 내리진 않음).

### 실패 시 처리
- 새 저장·대비 장치 없음(§7 해당 없음). 실패 시 수정 착수지시서 작성 후 대기.

### 브라우저 확인 (보리, 휴대폰 — 같은 와이파이 개발 서버 http://192.168.219.105:5173)
- 로그인 화면(로그아웃 상태)·처음 설정 화면 모양이 전과 같은지.
- 매출 화면(연도별 목록 포함)·달력·거래처 추가 창·일지 운행 추가 창·고객센터·공지사항 모양이 전과 같은지.

### 진행 결과
- 7개 CSS에서 26개 클래스 규칙 삭제(224줄 삭제·3줄 수정). 남은 클래스 검색 0건.
- 묶인 규칙 4곳은 안 쓰는 이름만 빼고 남김: `revenue.css`의 3곳(지시서대로) + **`account-flow.css` `.auth-primary-btn, .auth-secondary-btn` 1곳**(지시서에 빠졌던 것 — `.auth-primary-btn` 규칙 그대로 남음).
- 주석 정리: `customer-center.css:1` 목록에서 `.support-card-label` 뺌, `revenue.css:2` 줄 수 숫자 265→211.
- 줄 수: `account-flow.css` 573→443, `revenue.css` 272→211, `calendar.css` 231→213, `call-detail-form.css` 312→308.
- 로컬 `npm test` 전부 통과(단위 832 / 화면 365), `npm run build` 성공(JS 묶음 크기 경고 1건 — CSS만 지운 이번 변경과 무관), 개발 서버 오류 0.
- react-app `3fbac44`(보리 지시로 커밋·push AI가 함), CI verify·deploy 초록, 보리 휴대폰 확인 "괜찮아"·최종 `[x]` 승인(2026-10-10).
