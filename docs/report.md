# docs/report.md — 현재 슬라이스 착수지시서

## 알림 화면 — 옆 창 → 화면 전체, 알림마다 날짜, 종류별 끄기 `[x]`

보리 요청 2026-10-10(로드맵 밖). 참고 화면(다른 앱 알림 탭)은 따라 하기가 아니라 방식만 참고.
**react-app 새 브랜치 `notifications-page`**(main `1c43c7b`에서 시작) — 비서 브랜치와 따로, 끝나면 main에 합쳐 배포 가능.

### 보리 결정 (2026-10-10, 착수 전 질문 답)
1. 열기: 홈 위쪽 🔔 → 알림 화면 전체(하단 탭은 4개 그대로).
2. 날짜: 알림은 앱을 열 때 지금 상태로 만드는 안내라 "받은 시각"이 없음 → 알림마다 **관련 날짜**를 보여 줌(아래 표).
3. 끄기: **종류별** 켜기·끄기.

### 현재 상태 (코드 확인)
- 🔔 → 오른쪽에서 나오는 창(`NotificationPanel.jsx`, `AppShell.jsx:176`)에 카드 목록 + [닫기].
- 알림 종류(`lib/notifications.js` `collectNotifications`): ① 연체 미수금(건마다) ② 기사 초대 대기 ③ 오늘 운행일지 비어 있음(오후 6시 이후) ④ 일상점검표 의무화 안내(로그인, [바로 사용하기]/[다시 보지 않기]) ⑤ 백업 권장(비회원).
- 끄기 설정 없음. 날짜 표시 없음.

### 목표 상태 (기대 동작)
1. 🔔 → `/app/notifications` **화면 전체**: 위쪽 "◀ 알림"(뒤로가기 = 이전 화면), 하단 탭 그대로(홈 탭 강조).
2. 맨 위 **"알림 받기" 카드**: 종류별 스위치 — 미수금 연체 / 오늘 운행일지 비어 있음 / 기사 초대 대기(차주만) / 백업 권장(비회원만). 그 계정에 해당 없는 종류는 안 보임.
   - 끈 종류는 목록·🔔 숫자에서 빠짐. 켜면 다시 나옴.
   - ④ 일상점검표 의무화 안내는 법령 안내라 스위치 없음(지금처럼 [다시 보지 않기]로만).
3. 그 아래 **알림 목록**: 카드마다 제목·내용 + **날짜 줄**, 누르면 해당 화면, [닫기](지금과 같음 — 그 알림만 숨김).

   | 알림 | 날짜 줄 |
   |---|---|
   | 연체 미수금 | "운행 10.03 · 입금 예정 10.06" |
   | 오늘 운행일지 비어 있음 | 오늘 "10.10" |
   | 백업 권장 | 마지막 백업 "10.01"(한 번도 안 했으면 날짜 없음) |
   | 기사 초대 대기 | 날짜 없음(초대한 날을 저장하지 않음 — 새로 저장하지 않음) |
   | 일상점검표 의무화 | 날짜 없음 |
4. 알림이 없으면 "새로운 알림이 없습니다."(지금 문구).
5. 옆 창(`NotificationPanel`)은 없어짐.

### 저장 (§7 확인 완료 — 보리 "응")
- 스위치 값은 **기존 앱 설정(settings)에 칸 1개 추가**: `notifOff`(끈 종류 이름 목록). 저장은 기존 `savePracticeSettings` 그대로(비회원 = 이 휴대폰, 로그인 = 서버 `profiles.settings`에 같이 — 서버 표 변경 없음, 이미 설정 묶음째 저장).
- 새 저장 키·큐·대체 장치 없음. 저장 실패 시 기존과 같은 안내("저장에 실패했습니다…"), 스위치는 저장 성공 후에 바뀐 값으로 보임(설정 구독).
- `docs/testing-playbook.md` 확인: 기존 저장 길 재사용·새 저장 길 없음 → 실패 시 토스트·화면 값 그대로를 테스트.

### 건드릴 파일
| 파일 | 내용 | 줄 수(현재 → 예상) |
|---|---|---|
| `react-app/src/components/notifications/NotificationsPage.jsx` (새) | 알림 화면(머리·스위치 카드·목록) | 새로 약 110 |
| `react-app/src/components/notifications/notifications-page.css` (새) | 화면 모양(공용 색 변수, 라이트·다크) | 새로 약 90 |
| `react-app/src/lib/notifications.js` | 알림마다 날짜 줄(`dateLabel`)·종류(`kind`) 추가, 끈 종류 빼기 | 133 → 약 155 |
| `react-app/src/domain/practiceSettings.js` | `notifOff` 정리(알려진 종류 이름만 남김) | 124 → 약 135 |
| `react-app/src/app/AppShell.jsx` | 🔔 → 알림 화면 이동, 옆 창·[바로 사용하기] 처리 삭제(화면으로 옮김), 알림 화면은 홈 탭 강조 | 196 → 약 180 |
| `react-app/src/app/AppShellRoutes.jsx` | `notifications` 경로 추가 | 105 → 약 106 |
| `react-app/src/components/NotificationPanel.jsx`·`notification-panel.css` | 삭제(화면으로 대체) | 삭제 |
| 테스트 | 아래 | — |

### 안 건드릴 것
- 알림을 고르는 규칙(연체 판정·오후 6시·백업 14일 등) — 그대로, 끈 종류만 뺌.
- [닫기]로 숨긴 알림 기록(`dismissedNotifications`) — 그대로 사용.
- 서버 표 — 변경 없음(설정 묶음 안의 칸 추가).

### 테스트
- 알림 화면: 목록·날짜 줄·누르면 해당 화면·[닫기], 알림 없음 문구.
- 스위치: 끄면 목록·🔔 숫자에서 빠지고 다시 켜면 나옴, 해당 없는 종류 스위치 안 보임, 저장 실패면 안내 + 값 그대로.
- `notifications.js`: 종류별 날짜 줄, `notifOff` 빼기. 기존 `notifications*.test.js` 그대로 통과.
- 옆 창 테스트(`NotificationPanel.dailyInspection.test.js`)는 같은 내용(일상점검 [바로 사용하기]/[다시 보지 않기])을 새 화면 테스트로 옮김(약화 없음).
- 🔔 → `/app/notifications` → 뒤로가기 → 홈.
- 되돌림 FAIL 확인.

### §6 200줄
- 모두 200줄 이하.

### 실패 시 처리
- 저장 실패 = 기존 안내 토스트, 신규 레이어 없음.

착수지시서 확정, 작업 진행 승인(2026-10-10).

### 수정 착수지시서 1 (2026-10-10, 착수 직후 조사에서 발견 — 코드 아직 안 고침)
- 발견: 이 휴대폰에 저장된 설정을 읽을 때 **허용된 칸 이름 목록**으로 검사함(`store/persistDomainSchema.js` `SETTINGS_KEYS`·`isPersistedSettings`). 목록에 없는 칸(`notifOff`)이 들어가면 설정 전체가 "형식 오류"가 되어, 비회원은 다음에 켤 때 **로드맵 32번 막기 화면**("기록을 불러오지 못했습니다")이 뜸 — 로드맵 25번(지출 `vehicleNumber` 누락)과 같은 종류의 사고.
- 수정 계획: `react-app/src/store/persistDomainSchema.js`(119 → 약 121)에 `notifOff`를 허용 칸으로 추가 + "글자 목록일 때만 통과" 검사 1줄. 다른 검사·저장 코드는 그대로.
- `src/store/**`라 플레이북 필수 → `docs/testing-playbook.md` 확인. 테스트 추가: `notifOff` 든 설정이 저장→다시 읽기에서 통과, 잘못된 모양(글자 목록 아님)은 지금처럼 형식 오류, 비회원 끄기 저장 뒤 다시 켜도 막기 화면 안 뜸.
- 건드릴 파일 표에 위 1개 추가. 신규 레이어 없음.
착수지시서 확정(수정 1 포함), 작업 진행 승인(2026-10-10).

### 진행 (2026-10-10)
- react-app `notifications-page` 커밋 `07d19c1`(push 안 함). 줄 수: `NotificationsPage.jsx` 103·`notifications-page.css` 119·`lib/notifications.js` 155·`practiceSettings.js` 134·`persistDomainSchema.js` 121·`AppShell.jsx` 173.
- 표에 없던 1줄 변경 2곳(알림): `domain/financeTypes.js` 설정 모양 설명에 `notifOff` 1줄(타입 검사용), `app/lazyPages.js` 새 화면 등록 1줄(다른 화면과 같은 나중 불러오기).
- typecheck·`npm test` unit 838·화면 364 통과. 되돌림 FAIL: 허용 칸에서 `notifOff` 빼면 설정 읽기 테스트 실패, 끈 종류 빼기 없애면 화면·날짜 테스트 실패, 🔔 이동 없애면 앱 테스트 실패.
- AI 브라우저 확인(비회원, 휴대폰 크기): 🔔 → 알림 화면, 스위치 3개(미수금·오늘·백업), 오늘 알림 날짜 "10.10", 백업 끄기 → 목록에서 빠짐 → **새로고침해도 꺼진 그대로·막기 화면 없음**.
- 남은 것: push(브랜치) → CI → 보리 휴대폰 확인(개발 서버는 지금 이 브랜치 코드).
- push(보리) → CI verify 초록. 보리 휴대폰 확인 후 최종 승인 `[x]`(2026-10-10).

### §5 리뷰
1. 범위: 지시서 표 + 수정 1(`persistDomainSchema.js`) + 1줄 변경 2곳(`financeTypes.js`·`lazyPages.js`, 위 진행에 적음) + 테스트.
2. 몰래 증설 없음: 새 저장 키 없음 — 기존 설정 묶음에 칸 1개(`notifOff`, §7 보리 승인), 저장은 기존 `savePracticeSettings`.
3. 타입 꼼수 없음(`any`·`@ts-ignore`·`as unknown as` 추가 0건).
4. 200줄: 모두 이하(가장 긴 `lib/notifications.js` 155, 테스트 제외).
5. 테스트: 새 11건(되돌림 FAIL 3종), 옛 옆 창 테스트는 같은 내용을 새 화면 테스트로 옮김(약화 없음).
6. 공용 CSS·상태값: 새 CSS는 `notif-` 이름만, 공용 `.switch`는 쓰기만. 공용 상태값 `settings`에 칸 추가 → 읽는 곳(`persistDomainSchema` 검사·`normalizeSettings`) 전부 맞춤, 다른 곳은 칸을 안 읽음.
7. 요구사항: 목표 1~5 + 보리 결정 3건 충족.
