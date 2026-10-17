# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 57-C CSS 이름 중복 — 글자까지 같은 복사본 6개만 한 곳으로 `[x]`

> **진행(2026-10-17)**: 보리 "착수지시서 확정, 작업 진행해". 코드 커밋 react-app `42e84f1`(push 전) — 7개 파일, +6 −44줄.
> `npm test` 1,375개 통과(unit 935 + 화면 440), 테스트 파일 변경 없음.
> AI 개발 서버 비회원 375px 밝은 화면, 계산된 모양 값 전후 비교: 고객센터(FAQ 닫힘·펼침·문의 탭·내 문의 탭), 공지사항(빈 화면 + 검사용 공지 1건을 잠깐 넣어 카드·펼친 답), 달력(검사용 아이콘), 앱 설정 백업 아이콘 2개 — 전부 같음. 공지사항은 이제 `customer-center.css` 하나만 불러옴, `.inline-icon`은 `shared-controls.css`에만 있음.
> 기사 연동 화면: 처음 잰 "전" 값이 고친 뒤와 달라서, 이 화면 CSS 2개를 잠깐 커밋 전 상태로 되돌려 다시 잼 → 고친 뒤와 같은 값(처음 잰 값만 재는 조건이 달랐음). 다시 고친 상태로 돌려놓고 같은 값·옛 주석 없음 확인. 콘솔·서버 오류 없음.
> **지시서와 다른 점 1개**: `linked-driver.css` 185 → 178(지시서 177) — 앞뒤 규칙 사이 빈 줄 하나 남김.
> 리뷰 §5: 바뀐 파일 = 지시서 목록 그대로(`notice.css` 삭제 포함), 새 파일 없음, 타입 꼼수 없음(diff grep 0건), 전부 200줄 이하, 나머지 4개 이름의 파일 안 바뀜.
> 보리 push 뒤 CI verify·deploy 초록(run 38995065306). 배포본(getdrivelog.com, 비회원) 공지사항: `customer-center-*.css`를 불러와 `.support-panel` grid·gap 9px, 검사용 공지 카드 padding 17px 16px·둥근 모서리 16px·펼친 답 보임 — 따로 불러오는 화면에서도 규칙이 제대로 붙음. 보리 최종 `[x]` 승인(2026-10-17).

로드맵 57번의 남은 일(57-A `2f03f9a`·57-B `294a63d` `[x]`). 보리 지시(2026-10-17): "CSS 진짜 복사본 6개(파일 3쌍)만 정리, 나머지 4개는 그대로". 이번엔 이 6개만 다룬다.

### 쉬운 요약
- 같은 화면 꾸밈 규칙이 두 파일에 글자 하나 다르지 않게 두 번 적힌 곳이 6개(파일 3쌍)다. 지금은 한쪽만 고치면 고객센터와 공지 화면 모양이 서로 달라진다.
- 6개를 각각 한 곳에만 남기고 복사본은 지운다. 화면 모양은 하나도 안 바뀐다(같은 글자를 지우는 것뿐).
- 하고 나면 FAQ·공지 카드, 기사 연동 요약 제목, 작은 선 아이콘 모양을 바꿀 때 한 곳만 고치면 된다.

### 현재 상태 (코드 확인)
- **쌍 1 — FAQ·공지 카드 4개**(`.support-panel`·`.support-card`·`.faq-item`·`.support-detail`, 규칙 9줄): `customer-center.css:8-16` = `notice.css:3-11` 글자까지 같음. `notice.css`는 이 9줄이 전부. 두 화면이 따로 불러오는 화면(`lazyPages.js:18-19`)이라 이관 때 각자 모양이 갖춰지도록 일부러 복사함(`notice.css:1-2` 주석). 쓰는 곳: `CustomerCenterPage.jsx:193-219`, `NoticePage.jsx:27-38`.
- **쌍 2 — 기사 연동 요약 제목**(`.driver-section-heading`, 규칙 4줄): `driver-connection.css:7-10` = `linked-driver.css:160-163`. 쓰는 곳은 `DriverConnectionPage.jsx:108` 한 곳뿐이고(전체 grep), 이 화면은 `driver-connection.css`를 불러옴(`DriverConnectionPage.jsx:18`). → `linked-driver.css` 쪽은 쓰는 화면이 없음. 주석이 말하는 `ClientInvoiceGroups`는 이제 이 이름을 안 씀(grep 0건).
- **쌍 3 — 작은 선 아이콘**(`.inline-icon`·`.inline-icon.sm`): `calendar.css:37-51` = `shared-controls.css:178-191`. `shared-controls.css`는 앱 시작 때 모든 화면에 불러옴(`App.jsx:27`) → `calendar.css` 쪽은 겹친 것. 쓰는 곳: `AppSettingsBackupSection.jsx:7·15`, `CalendarMonthSummary.jsx:133·144·158`.
- 6개 이름을 다른 CSS 파일에서 덮어쓰는 규칙: `.driver-summary-card .driver-section-heading`(`driver-connection.css:18`, 그대로 남김) 외 없음(전체 grep).

### 목표 상태 (기대 동작)
1. 6개 이름이 각각 CSS 파일 한 곳에만 정의돼 있다(grep으로 확인).
2. 공지사항 화면이 `customer-center.css`를 불러와 FAQ·공지 카드 규칙을 같이 쓰고, `notice.css`는 없다.
3. 고객센터(FAQ·문의·내 문의 탭), 공지사항, 기사 연동 화면 요약 카드, 달력 월 요약 아이콘, 앱 설정 백업 버튼 아이콘의 모양이 바꾸기 전과 똑같다.
4. 나머지 4개(`.modal-overlay`·`.work-log-page`·`.mypage-shortcut-icon`·`.maint-fuel-page`)는 손대지 않는다.

### 건드릴 파일 (모두 `react-app/src/`)
- `components/notice.css` — **삭제**(9줄 전부가 `customer-center.css`와 같은 규칙).
- `components/NoticePage.jsx` — 불러오는 파일 1줄: `./notice.css` → `./customer-center.css`.
- `components/customer-center.css` — 머리 주석만 고침(규칙은 그대로). 최종 문구:
  `/* 고객센터(CustomerCenterPage.jsx)·공지사항(NoticePage.jsx) 공용 — .support-panel/.support-card/`
  `   .faq-item/.support-detail은 두 화면이 같이 쓴다. */`
- `components/drivers/linked-driver.css` — 157-164줄(주석 3줄 + `.driver-section-heading` 4줄 + 빈 줄) 삭제.
- `components/drivers/driver-connection.css` — 주석에서 "두 파일에 각각 복제" 내용만 지움: 2-3줄 → `linked-driver.css §15 슬라이스 A 분리(§6). */`, 6줄 → `문제까지 명시적으로 상쇄(§15 슬라이스 A) */`.
- `components/calendar/calendar.css` — 36-52줄(주석 + `.inline-icon`·`.inline-icon.sm` + 빈 줄) 삭제.
- `shared-controls.css` — 176-177줄 주석 → `/* 전역 인라인 아이콘(앱 설정 백업 버튼·달력 월 요약) — App.jsx가 모든 화면에 불러옴. */`
- **공용 이름 영향 목록**(AGENTS §5-6): 위 "쓰는 곳" 그대로 — 규칙 값은 하나도 안 바꾸고 같은 글자만 지우므로 모양 영향 없음. 공지 화면이 새로 같이 불러오는 `customer-center.css`의 나머지 규칙(`.customer-center-page`·`.support-tabs`·`.inquiry-form`·`.my-inquiry-*` 등)은 공지 화면에 그 이름이 없어 아무것에도 안 붙음(`NoticePage.jsx` 이름: `notice-page`·`support-panel`·`empty-state`·`faq-item`·`support-detail`).
- **슬라이스 크기**: 파일 7개(1개는 삭제)로 §3 "1~3개"보다 많지만, 전부 같은 글자 지우기·주석 고치기(바뀌는 줄 약 40줄)라 보리 지시대로 한 슬라이스.
- 다른 방법(안 씀): 공용 새 파일로 빼기 — 이미 228줄인 `CustomerCenterPage.jsx`를 고쳐야 하고 새 파일이 생김.

### 안 건드릴 것
- 나머지 4개 — 이름만 같고 내용을 나눠 맡음(복사본 아님): `.modal-overlay`(`account-flow.css:386` 모양 / `app-shell-base.css:42` 움직임), `.work-log-page`(`day-log-shell.css:4` 왼쪽 정렬 / `day-log.css:31` 스크롤 여백), `.mypage-shortcut-icon`(`mypage.css:22` 가운데 정렬·색 / `mypage-shortcuts.css:55` 크기), `.maint-fuel-page`(`management-common.css:7` 관리 화면 공통 / `maint-fuel.css:4` 이 화면 아래 여백).
- JSX의 CSS 이름(`className`)·`CustomerCenterPage.jsx`·화면 동작·저장 — 하나도 안 바뀜.

### 검증
- 로컬 `npm test` — 그대로 통과해야 함. 새 테스트는 없음: 테스트 실행기는 CSS를 빈 것으로 바꿔 읽어(`testSupport/jsxLoaderHook.mjs:14`) 모양을 못 봄 → 아래 화면 값 비교로 확인.
- AI 개발 서버(비회원, 저장 안 함): **고치기 전에** 고객센터 FAQ 카드·펼친 답·문의 탭 카드, 공지사항 빈 화면, 달력 월 요약 아이콘, 앱 설정 백업 아이콘의 계산된 모양 값(크기·여백·테두리·글자 크기·색)을 재고 → 고친 뒤 같은 값인지 비교. 비회원으로 못 여는 기사 연동 화면은 보리 휴대폰 확인으로.
- 6개 이름 정의가 CSS 한 곳씩인지 grep.
- 로드맵 57번의 "화면 모양이 바뀔 수 있어 시안 먼저"는 이번 범위엔 해당 없음(모양을 안 바꿈) → 시안 대신 위 전후 값 비교.

### 파일 길이
- `customer-center.css` 35 → 34 / `notice.css` 11 → 삭제 / `NoticePage.jsx` 44 → 44 / `linked-driver.css` 185 → 177 / `driver-connection.css` 159 → 158 / `calendar.css` 118 → 101 / `shared-controls.css` 192 → 191 — 전부 200 이하.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음(CSS 글자 지우기만). 모양이 하나라도 달라지면 멈추고 수정 착수지시서를 다시 써서 승인받는다.

### 사용자 확인 방법 (보리 휴대폰)
1. 마이페이지 → 고객센터 → FAQ 하나 눌러 펼침 → 카드 모양·펼친 답이 그대로. "1:1 문의/건의"·"나의 문의·건의 확인" 탭 위 카드도 그대로.
2. 공지사항 → "등록된 공지사항이 없습니다"가 전처럼 보임.
3. (차주 계정) 기사 연동 화면(`/app/drivers`) → 맨 위 요약 카드 제목이 전처럼 왼쪽 정렬.
4. 운행 탭 달력 → 월 요약의 정비·주유·기타 줄 아이콘 크기 그대로, 앱 설정 → 백업 버튼 아이콘 그대로.
