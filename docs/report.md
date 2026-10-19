# docs/report.md — 현재 슬라이스 착수지시서

## 로드맵 95 문자 보내기 주소를 만드는 같은 코드 세 벌을 한 곳으로 `[x]`

> **진행(2026-10-19)**: 보리 "착수지시서 작업해줘". 코드 커밋 react-app `6c414a2`(7개 파일).
> - 교차검증(AGENTS §1): 세 곳 코드가 글자까지 같음(`/iPhone|iPad|iPod/i`, `encodeURIComponent`), 줄 번호·테스트 위치·`ReportShareModal.jsx`에 `buildShareSmsUrl` 가져오기 없음 모두 맞음. 고친 것: 테스트 수 "1,420 → 1,419"는 실제 기준과 다름 — 실제 1003 + 496 = 1,499 → **1004 + 494 = 1,498**(줄어든 수 1은 지시서와 같음).
> - 목표 2 grep(`sms:\$\{`, 운영 코드) → `domain/smsHref.js:7` **1곳**. `buildShareSmsUrl`·`buildTemplateSmsUrl` 이름 grep 0건.
> - 바뀐 파일: 새 `domain/smsHref.js` 8줄(머리 주석 지시서 문구 그대로)·`domain/smsHref.test.js`, `ShareFileModal.jsx` 113→105, `MessageTemplateSheet.jsx` 68→60(둘 다 `navigator.userAgent`를 넘김), `driverInviteSms.js` 26→26(가져오기 1줄 +, 두 줄 → 한 줄), 옛 테스트 두 개 삭제(`ReportShareModal.test` "buildShareSmsUrl: 기본 UA는 ?, iPhone UA는 &", `MessageTemplateSheet.test` "buildTemplateSmsUrl: 기본 UA는 ?, iPhone UA는 &") + 가져오기.
> - 단언 나란히(옛 두 테스트 = 새 테스트): 기본 `sms:01012345678?body=${encodeURIComponent('안녕 테스트')}` / iPhone UA `sms:01012345678&body=…` — 글자 같음. 새 테스트는 iPad 1줄 더, 기기 정보는 인자로(옛 테스트의 기본 UA는 jsdom 기본값, 새 테스트는 안드로이드 UA 문자열). revert-check 해당 없음(새 파일 검사).
> - `inviteCode.security.test.js`(기사 초대 주소) 그대로 통과. `npm test` 1004 + 494 통과, 타입 검사·빌드 통과.
> - 개발 서버(비회원): 일지 카드 문자 단추 → 문자 문구 시트 "미수금 안내 … 한진 담당자님" 열림, 서류 발급 → 운송비 내역서 → 공유 창 "문자로 보내기" 보임, 콘솔 오류 없음. **기사 초대 창은 로그인해야 열려 확인 못 함**(비회원은 "로그인 후 쓸 수 있습니다") — 주소는 위 테스트로만 확인.
> - 지시서 오류 1건(데스크톱 AI): 테스트 수를 `test(` 줄 수(1,420)로 잘못 적음 — 교차검증 정정(실행 수 1,499 → 1,498)이 맞음.
> - push 뒤 CI verify·deploy 초록(run 39209609674).
> - §5 리뷰(데스크톱 AI): ①범위 = 지시서 7개 파일 ②새 파일 2개(`domain/smsHref.js`·테스트 — 지시서에 적은 것), 새 저장 키 0 ③타입 꼼수 0 ④모두 200줄 이내 ⑤테스트 — 옛 두 벌 삭제, 새 테스트가 같은 기대 주소 + iPad(단언 3) ⑥공용 함수 영향 — `buildSmsHref` 쓰는 곳 = 세 곳 전부(grep), 운영 `sms:${` 1곳 ⑦목표 1~4 대조 완료.
> - 배포본 로그인 확인(2026-10-19, 내장 브라우저): 운송비 내역서 [공유] → "문자로 보내기" 보임, 기사 연동 관리 [수정] → "문자 보내기" 단추 보임(로그인이라 이번엔 열림), 10월 10일 일지 콜 카드 "문자 보내기" → 문자 문구 창 "테스터거래처, 010-0000-0000 … 미수금 안내·입금 요청" 열림. 세 창 모두 문구는 누르지 않고 닫음, 콘솔 오류 없음.
> - 보리 휴대폰에서 문자 앱 열림 확인, 최종 승인(2026-10-19).

보리 "승인"(2026-10-19, 93-B `[x]`). 로드맵 95 `[확인: 2026-10-19 보리 — 93-B 조사 중 발견, 로드맵에 올림]`, 순서 "93-B 바로 뒤".

### 쉬운 요약
- 문자 앱을 여는 주소(아이폰이면 `&`, 그 밖은 `?`)를 만드는 같은 두 줄이 세 곳(내역서·점검표 공유 창, 문자 문구 시트, 기사 초대 문자)에 따로 있습니다.
- 한 곳에 작은 함수 하나로 두고 세 곳이 그걸 부르게 합니다. 나중에 한 곳만 고쳐도 세 화면이 같이 맞습니다.
- 화면·문자 내용은 그대로입니다.

### 현재 상태 (코드 확인)
- `components/shared/ShareFileModal.jsx:10-13` `buildShareSmsUrl(phone, body)` — `navigator.userAgent`를 직접 읽음. 부르는 곳 `:74`(같은 파일).
- `components/day-log/MessageTemplateSheet.jsx:16-19` `buildTemplateSmsUrl(phone, body)` — 위와 글자까지 같음. 부르는 곳 `:44`(같은 파일).
- `lib/driverInviteSms.js:24-25` — 같은 두 줄을 함수 안에 직접 씀, 기기 정보는 인자 `userAgent`로 받음(`DriverFormModal.jsx:70`이 `navigator.userAgent`를 넘김).
- 테스트: 같은 내용의 테스트가 두 벌 — `ReportShareModal.test.js:235` "buildShareSmsUrl: 기본 UA는 ?, iPhone UA는 &", `MessageTemplateSheet.test.js:159` "buildTemplateSmsUrl: …"(둘 다 `navigator.userAgent`를 바꿔 끼워 검사). 기사 초대 문자는 `inviteCode.security.test.js:60·62`가 주소 전체를 봄.
- grep `sms:\$\{` 운영 코드 3곳뿐(위 세 곳).

### 목표 상태 (기대 동작)
1. 새 파일 `domain/smsHref.js` 하나에 `buildSmsHref(phone, body, userAgent)` — 화면·기기와 무관한 순수 함수(기기 정보는 부르는 쪽이 넘김, `driverInviteSms.js`가 이미 쓰는 방식).
2. 세 곳 모두 `buildSmsHref`를 부름. `buildShareSmsUrl`·`buildTemplateSmsUrl` 정의 삭제. 운영 코드 grep `sms:\$\{` → `domain/smsHref.js` 1곳.
3. 만들어지는 주소는 지금과 글자 하나까지 같음(기본 `sms:번호?body=…`, iPhone·iPad·iPod `sms:번호&body=…`, 본문은 `encodeURIComponent`).
4. 테스트: 두 벌 테스트를 새 `domain/smsHref.test.js` 하나로 옮김(같은 단언 — 기본 `?`, iPhone `&` — 에 iPad 1줄 더, 기기 정보는 인자로 넘겨 `navigator` 바꿔 끼우기 없음). 옛 두 테스트 삭제. `inviteCode.security.test.js`는 그대로 통과(기사 초대 주소 안 바뀜).

### 건드릴 파일
- 새로: `domain/smsHref.js`(약 10줄), `domain/smsHref.test.js`.
- 고침: `components/shared/ShareFileModal.jsx`(함수 삭제, `:74` 부르기), `components/day-log/MessageTemplateSheet.jsx`(함수 삭제, `:44` 부르기), `lib/driverInviteSms.js`(`:24-25` → `buildSmsHref`).
- 테스트 고침: `components/ReportShareModal.test.js`(`:32` 가져오기·`:235` 테스트 삭제), `components/day-log/MessageTemplateSheet.test.js`(`:23` 가져오기·`:159` 테스트 삭제).
- 코드 주석 최종 문구(`domain/smsHref.js` 머리): `// 기기 문자 앱을 여는 주소 — 아이폰 계열은 본문 앞에 &, 그 밖은 ?(공유 창·문자 문구·기사 초대가 같이 씀).`
- 공용 값 영향 목록(§5-6): `buildSmsHref`를 쓰는 곳 = 위 세 곳이 전부(목표 2 grep).

### 안 건드릴 것
- 문자 본문 내용(내역서 안내 문구·문자 문구 시트·기사 초대 문구) — 그대로.
- `ReportShareModal.jsx` — 93-B에서 다른 이름 줄을 이미 지워 이번엔 바뀔 것 없음(`buildShareSmsUrl` 가져오기 없음, grep 확인).
- 폴더 옮기기(`shared/utils` 등 목표 구조) — 59번. 지금 `shared/utils/` 폴더가 없어 순수 함수 자리인 `domain/`에 둠.

### 검증
- `npm test`·타입 검사·빌드 통과. 테스트 수: 두 개 지우고 하나 더함 → 1,420 → 1,419.
- 목표 2 grep 결과를 진행 기록에.
- revert-check: 새 테스트는 새 파일을 검사하므로 고치기 전 코드엔 대상이 없음 — 해당 없음. 대신 고치기 전 코드의 두 옛 테스트와 새 테스트의 단언(기대 주소 글자)이 같은지 진행 기록에 나란히.
- 개발 서버(비회원): 서류 발급 → 운송비 내역서 → [공유] → "문자로 보내기" 보임, 일지 → 문자 문구 시트 열림, 기사 초대 창 [문자 보내기] 보임 → 콘솔 오류 없음(문자 앱을 실제로 열지는 않음).

### 파일 길이
- `ShareFileModal.jsx` 113 → 약 109, `MessageTemplateSheet.jsx` 68 → 약 63, `driverInviteSms.js` 26 → 약 27(가져오기 1줄), 새 `domain/smsHref.js` 약 10. 모두 200줄 이내.

### 실패 시 처리
- 새 저장 장치·재시도·대비 장치 없음.
- `inviteCode.security.test.js` 등 기존 테스트가 하나라도 실패하면 주소 글자가 달라졌다는 뜻 — 고치지 말고 멈추고 무엇이 달라졌는지 이 파일에 적어 다시 승인받는다.

### 사용자 확인 방법 (배포 뒤, 휴대폰 — 보리)
1. 운송비 내역서 → [공유] → "문자로 보내기" → 문자 앱이 안내 문구와 함께 열림(보내지 않고 닫아도 됨).
2. 일지 → 문자 문구 하나 고르기 → 문자 앱이 그 문구로 열림.
3. (AI가 배포본에서) 위 창들이 열리고 콘솔 오류 없음 확인.
