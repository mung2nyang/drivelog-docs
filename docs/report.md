# docs/report.md — 현재 슬라이스 착수지시서

103 지시서·기록은 drivelog-docs `27aa8b3`.

## 로드맵 23-A 문의 관리자 — 서버 명단·관리자 함수·문의 표 권한 `[x]` `[확인: 2026-10-22 보리 — "모바일 다른계정으로 확인완료 승인"]`

보리 지시(2026-10-22): 23번을 관리자 화면 방식으로 바꿈 `[확인: 2026-10-22 보리 — "응 내계정으로 붙혀, 순서도 좋은데"]`, "좋아 작성해". 로드맵 23번 순서 A(서버) → B(관리자 화면) → C(사용자 답변 알림) → D(보리 메일 알림) 중 **이번엔 A만**. 앱 코드는 안 바뀜(B에서 씀). 관리자 계정은 ya01na111@gmail.com `[확인: 2026-10-22 보리 — "응 맞아"]`.

### 쉬운 요약
- 서버에 "관리자 명단"을 만들고 보리 계정을 한 줄 넣습니다. 명단에 있는 계정만 모든 문의를 보고 답변을 저장할 수 있게 서버가 막습니다.
- 조회해 보니 **지금은 사용자가 자기 문의의 답변 칸을 직접 채울 수 있게 서버가 열려 있습니다**(앱엔 그런 화면이 없지만 서버는 허용). 이것도 이번에 막습니다.
- 일반 사용자는 자기 문의를 쓰고 읽기만 합니다. 화면은 이번에 안 바뀝니다(관리자 화면은 23-B).

### 현재 상태 (확인 근거)
- 앱: `features/support/api/supportInquiryMutations.js`가 insert(`user_id·type·title·content·status:'open'`)와 본인 조회(`select('*')`)만(같은 파일 grep `.update`·`.delete` 0). "답변 완료"는 `answer`에 글이 있으면(`MyInquiriesList.jsx:41`). 관리자 개념은 어디에도 없음(react-app grep `admin` 0, sot grep `관리자` 0). 로그인은 구글뿐(`supabaseClient.ts:23`).
- 서버 — 0번 읽기 전용 조회(2026-10-22, 데스크톱 AI가 보리 허락으로 SQL Editor에서 실행. 이관 기록 0001~0024엔 없음):
  - 칸: `id uuid`(기본 `gen_random_uuid()`, 기본키)·`user_id uuid`(→ `auth.users`, 계정 지우면 같이 지움)·`type`·`title`·`content`·`status`(기본 `'open'`, 허용 값 제한 없음)·`answer`·`answered_at timestamptz`·`raw jsonb`·`created_at timestamptz`(기본 `now()`). 트리거 0. **줄 0개**(아직 문의 없음).
  - RLS 켜짐. 정책 3개(모두 `public` 대상): "본인 문의만 작성" INSERT `auth.uid() = user_id` / "본인 문의만 조회" SELECT 같음 / **"본인 문의만 수정" UPDATE `auth.uid() = user_id`** — 칸 제한이 없어 본인 줄의 `answer`·`status`·`answered_at`을 사용자가 채울 수 있음.
  - 권한: `anon`·`authenticated` 둘 다 표 전체 권한(INSERT·SELECT·UPDATE·DELETE·TRUNCATE 등, Supabase 기본값). 지우기 정책이 없어 DELETE는 막혀 있음.
  - 그 밖: `admin_users` 없음, 같은 이름 함수 없음, `profiles`에 `id uuid`·`name text` 있음, `auth.users`에 ya01na111@gmail.com 계정 1개.

### 목표 상태 (기대 동작)
1. 새 표 `admin_users`(`user_id` 기본키 → `auth.users`, 계정 지우면 같이 지움 / `created_at`). RLS 켜고 정책 없음, `anon`·`authenticated` 권한 없음 → 앱에서 아무도 직접 못 읽고 못 고침.
2. 서버 함수 3개, 모두 `security definer` + `set search_path = public`, `anon` 실행 불가·`authenticated`만:
   - `is_admin()` → 지금 계정이 명단에 있으면 true(23-B 화면 문에 씀).
   - `admin_list_support_inquiries()` → 관리자가 아니면 오류. 관리자면 모든 문의(id·유형·제목·내용·상태·답변·답변 시각·접수 시각) + 문의한 사람 이름(`profiles.name`만), 답변 대기 먼저·최근 먼저.
   - `admin_answer_support_inquiry(inquiry_id, answer_text)` → 관리자가 아니면 오류, 빈 답변·없는 문의도 오류. 관리자면 `answer`·`status = 'answered'`·`answered_at = now()`만 바꿈(답변을 고치면 시각도 새로).
3. 문의 표 권한: "본인 문의만 수정" 정책 지움. `anon` 권한 전부 뺌. `authenticated`는 읽기 + **넣기는 `user_id·type·title·content·status` 칸만**(답변 칸을 넣을 때부터 채우는 것도 막음). 넣기·읽기 정책은 같은 조건으로 대상만 `authenticated`로. 지금 앱 동작(넣기·본인 조회)은 그대로 됨(앱이 넣는 칸이 정확히 이 5개).
4. 문의 표 모양을 이관 기록에 담음: `create table if not exists`로 위 조회 결과 그대로(이미 있으니 실제로는 안 바뀜).
5. 보리 계정 1줄을 명단에 넣음 — SQL Editor에서 따로, 파일에는 메일 주소를 안 남김.

### 건드릴 파일
- 새 파일 `react-app/supabase/migrations/0025_support_admin.sql` — 아래 SQL 완성본 그대로 + 맨 아래 사후검증 주석(0021과 같은 형식).
- 서버(Supabase SQL Editor): 그 파일 실행 + 명단 1줄 넣기. 실행은 데스크톱 AI(보리 로그인·허락) 또는 보리.
- 문서(`[x]` 때): `docs/sot.md` §8-2 문의 줄에 권한 한 줄(사용자 본인 넣기·읽기 / 관리자 전체 읽기·답변, 관리자는 서버 명단).

### 안 건드릴 것
- `src/` 전부(관리자 화면·메뉴·`is_admin` 부르기는 23-B, 답변 알림은 23-C), 메일 알림(23-D), 처리방침 문구, `profiles` 권한(함수가 이름만 꺼내 줌), `raw` 칸(앱이 안 씀 — `supportInquiryMutations.js` insert에 없음, 그대로 둠).
- 데이터: 지우거나 고치지 않음(`DROP TABLE`·`TRUNCATE`·`DELETE` 없음, AGENTS §9). 지우는 건 정책 1개("본인 문의만 수정")뿐.

### SQL 완성본 (`0025_support_admin.sql`, 멱등)
```sql
-- 0025: 고객센터 문의 관리자(로드맵 23-A). 관리자는 admin_users 명단으로만 정하고, 관리자 함수로만 모든 문의를 읽고 답변을 저장한다. 일반 사용자는 본인 문의 넣기·읽기만.
-- 명단 넣기는 이 파일에 없다 — SQL Editor에서 계정 id로 한 줄 넣는다(메일 주소를 저장소에 남기지 않으려고).
begin;

-- 문의 표는 이관 기록 없이 만들어져 있었다. 2026-10-22 조회 결과 그대로 적는다(이미 있으면 안 바뀜).
create table if not exists public.support_inquiries (
  id uuid primary key default gen_random_uuid(),
  user_id uuid references auth.users(id) on delete cascade,
  type text,
  title text,
  content text,
  status text default 'open',
  answer text,
  answered_at timestamptz,
  raw jsonb,
  created_at timestamptz default now()
);
alter table public.support_inquiries enable row level security;

-- 사용자가 본인 줄의 답변 칸을 채울 수 있던 수정 정책을 없애고, 넣기 칸을 앱이 쓰는 5개로 묶는다.
drop policy if exists "본인 문의만 수정" on public.support_inquiries;
drop policy if exists "본인 문의만 작성" on public.support_inquiries;
create policy "본인 문의만 작성" on public.support_inquiries
  for insert to authenticated
  with check (auth.uid() = user_id);
drop policy if exists "본인 문의만 조회" on public.support_inquiries;
create policy "본인 문의만 조회" on public.support_inquiries
  for select to authenticated
  using (auth.uid() = user_id);

revoke all on public.support_inquiries from anon;
revoke all on public.support_inquiries from authenticated;
grant select on public.support_inquiries to authenticated;
grant insert (user_id, type, title, content, status) on public.support_inquiries to authenticated;

create table if not exists public.admin_users (
  user_id uuid primary key references auth.users(id) on delete cascade,
  created_at timestamptz not null default now()
);
alter table public.admin_users enable row level security;
revoke all on public.admin_users from anon;
revoke all on public.admin_users from authenticated;

create or replace function public.is_admin()
returns boolean
language sql
stable
security definer
set search_path = public
as $$
  select exists (select 1 from public.admin_users where user_id = auth.uid());
$$;

create or replace function public.admin_list_support_inquiries()
returns table (
  id uuid,
  type text,
  title text,
  content text,
  status text,
  answer text,
  answered_at timestamptz,
  created_at timestamptz,
  user_name text
)
language plpgsql
stable
security definer
set search_path = public
as $$
begin
  if not public.is_admin() then
    raise exception '관리자만 볼 수 있습니다.' using errcode = '42501';
  end if;
  return query
    select q.id, q.type, q.title, q.content, q.status, q.answer, q.answered_at, q.created_at, p.name
    from public.support_inquiries q
    left join public.profiles p on p.id = q.user_id
    order by (q.answer is null or btrim(q.answer) = '') desc, q.created_at desc;
end;
$$;

create or replace function public.admin_answer_support_inquiry(inquiry_id uuid, answer_text text)
returns void
language plpgsql
security definer
set search_path = public
as $$
begin
  if not public.is_admin() then
    raise exception '관리자만 답변할 수 있습니다.' using errcode = '42501';
  end if;
  if answer_text is null or btrim(answer_text) = '' then
    raise exception '답변 내용이 비어 있습니다.' using errcode = '22023';
  end if;
  update public.support_inquiries
     set answer = answer_text, status = 'answered', answered_at = now()
   where id = inquiry_id;
  if not found then
    raise exception '없는 문의입니다.' using errcode = 'P0002';
  end if;
end;
$$;

revoke all on function public.is_admin() from public;
revoke execute on function public.is_admin() from anon;
grant execute on function public.is_admin() to authenticated;
revoke all on function public.admin_list_support_inquiries() from public;
revoke execute on function public.admin_list_support_inquiries() from anon;
grant execute on function public.admin_list_support_inquiries() to authenticated;
revoke all on function public.admin_answer_support_inquiry(uuid, text) from public;
revoke execute on function public.admin_answer_support_inquiry(uuid, text) from anon;
grant execute on function public.admin_answer_support_inquiry(uuid, text) to authenticated;

commit;
```

명단 넣기(파일 밖, SQL Editor에서 한 번):
```sql
insert into public.admin_users (user_id)
select id from auth.users where email = 'ya01na111@gmail.com'
on conflict (user_id) do nothing;
```

### 검증
- 사후검증(파일 끝 주석, 기대 출력):
  - `select policyname, cmd, roles::text from pg_policies where tablename = 'support_inquiries';` → "본인 문의만 작성" INSERT `{authenticated}`, "본인 문의만 조회" SELECT `{authenticated}` 2개만.
  - `select grantee, privilege_type from information_schema.role_table_grants where table_name in ('support_inquiries', 'admin_users') and grantee in ('anon', 'authenticated');` → `authenticated SELECT` 1줄만(칸 단위 INSERT는 여기 안 나옴).
  - `select column_name from information_schema.column_privileges where table_name = 'support_inquiries' and grantee = 'authenticated' and privilege_type = 'INSERT';` → content·status·title·type·user_id 5개.
  - `select relrowsecurity from pg_class where oid = 'public.admin_users'::regclass;` → true, `select count(*) from pg_policies where tablename = 'admin_users';` → 0, `select count(*) from public.admin_users;` → 1.
  - `select proname, prosecdef, has_function_privilege('anon', oid, 'execute') anon_exec, has_function_privilege('authenticated', oid, 'execute') auth_exec from pg_proc where proname in ('is_admin', 'admin_list_support_inquiries', 'admin_answer_support_inquiry');` → 3줄 모두 prosecdef true·anon_exec false·auth_exec true.
- 흉내 확인(SQL Editor, `begin … rollback`이라 남는 것 없음, `set local role authenticated` + `request.jwt.claims`에 계정 id): 아무 계정 id → `is_admin()` false, 두 관리자 함수 오류. 보리 계정 id → `is_admin()` true, 목록 함수 0줄(문의 없음). 일반 계정으로 `update support_inquiries set answer = 'x'` → 권한 오류.
- 앱 CI 영향 없음(앱 코드 안 바뀜, 마이그레이션 파일은 CI 대상 밖). 앱에서 문의 넣기·나의 문의 보기가 그대로 되는지 브라우저로 한 번.

### 파일 길이
- `0025_support_admin.sql` 새 파일 약 135줄(위 완성본 약 120줄 + 사후검증 주석, 200 이하). 다른 파일 변화 없음.

### 코드에 넣을 주석(최종 문구)
- 위 완성본의 주석 그대로(머리 2줄, 문의 표 기록 1줄, 권한 정리 1줄).

### 실패 시 처리
- 새 저장 장치·재시도 없음(AGENTS §7). 실행이 오류로 멈추면 `begin…commit`이라 아무것도 안 바뀜 — 그대로 멈추고 보고. 사후검증·흉내 확인이 기대와 다르면 이 파일에 수정 착수지시서를 써서 다시 승인받음. 되돌리기가 필요하면 그때 되돌리는 SQL(함수 3개·`admin_users` 지우기, 정책·권한 원래대로)을 함께 제시.

### 사용자 확인 방법
- 이번엔 화면 변화 없음. 앱(로그인) → 마이페이지 → 고객센터 → 1:1 문의/건의에서 시험 문의 1건 보내기 → "나의 문의/건의 확인"에 "답변 대기"로 보이면 그대로 잘 됨. 관리자 확인은 위 흉내 확인 결과로 보고, 화면은 23-B에서.

### 진행 기록
- 2026-10-22 보리 "착수지시서 확정,작업진행해". 교차검증: 앱 넣기 칸 5개·본인 조회·`admin` 0·0021/0024 형식 모두 지시서와 같음. `0025_support_admin.sql` 127줄 — 완성본 그대로 + 사후검증 주석 4묶음.
- 서버 실행 전 미리 시험(저장소 밖 시험용 Postgres, PGlite 0.3.7 — 옛 정책 3개·전체 권한을 조회 결과대로 꾸민 뒤 실행): 두 번 실행 오류 없음, 사후검증 전부 기대값. 흉내: 사용자 5칸 넣기 됨·답변 칸 같이 넣기·남 대신 넣기·답변 고치기·지우기·명단 보기 막힘, 본인 조회 됨. 사용자 `is_admin` false·관리자 함수 2개 오류. 관리자 `is_admin` true·목록 1줄(이름 포함)·빈 답변·없는 문의 오류·답변 저장 뒤 사용자 쪽 "answered". 진짜 서버 실행·명단 1줄은 아직(데스크톱 AI 또는 보리).
- 코드 커밋 react-app `24afd74`(2026-10-22, 파일 1개, push 안 함). 남은 것: 서버 실행·명단 1줄 → 사후검증·흉내 확인 → 앱 문의 1건 확인 → 보리 push → CI → §5 리뷰 → 최종 승인.
- 보리 push 뒤 CI verify·deploy 초록(run 39759243299). 데스크톱 AI 확인: `0025` 파일이 지시서 완성본과 같음(`commit;`까지 diff 0). 진짜 서버는 아직 적용 전(2026-10-22 조회: 옛 정책 3개·전체 권한 그대로, `admin_users`·함수 없음, 문의 0줄).
- 2026-10-22 보리 "0025 서버 실행하고 명단 1줄 넣고 사후검증·흉내 확인까지 해" → 데스크톱 AI가 SQL Editor에서 실행(파일과 들여쓰기 빼고 같음 확인 뒤, "파괴적 작업" 경고 확인) — 성공. 명단 1줄 넣음. 사후검증 4묶음 모두 기대값(정책 2개 `{authenticated}`, 표 권한 `authenticated SELECT`만, 넣기 칸 5개, `anon` 칸 권한 0, `admin_users` RLS true·정책 0·1줄, 함수 3개 definer·anon 불가·authenticated 가능·`search_path=public`).
- 흉내 확인(진짜 서버, 끝에 오류를 내 전부 되돌림 — 뒤 조회로 문의 0줄·명단 1줄 확인): 일반 계정 `is_admin` false, 목록·답변 함수 막힘, 명단 읽기 막힘, 5칸 넣기 됨, 답변 칸 같이 넣기·남 대신 넣기·답변 고치기·지우기 막힘, 본인 줄만 보임. 관리자 `is_admin` true, 목록 1줄(이름 있음), 빈 답변·없는 문의 막힘, 답변 저장 됨 → 일반 계정에서 `answered`와 답변 글이 보임.
- 보리 확인: 휴대폰 다른 계정으로 앱에서 문의 넣기 됨(넣기 칸 5개로 묶은 뒤에도 앱 그대로).
- §5 리뷰: ① 범위 — 바뀐 파일 `0025_support_admin.sql` 1개(지시서 "건드릴 파일"과 같음, `src/` 변화 0) ② 몰래 증설 — 새 표 `admin_users`는 지시서 목표 1(승인), 다른 저장 장치·재시도 없음 ③ 타입 꼼수 — SQL뿐, 해당 없음 ④ 200줄 — 127줄 ⑤ 테스트 — 앱 테스트 변화 없음, 서버 동작은 흉내 확인으로 ⑥ 공용 영향 — 문의 표를 쓰는 앱 코드는 `supportInquiryMutations.js` 하나(넣기 5칸·본인 조회, 그대로 됨) ⑦ 목표 1~5 모두 충족.
- sot 기록 위치: 지시서엔 §8-2라 했으나 §8-2는 "원본과 다른 점" 표라 맞지 않아 새 절 §4-15로 적음.

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
