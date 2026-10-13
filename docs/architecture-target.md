# react-app 폴더 구조 — 현재 분석과 목표 구조 (2026-10-12)

> 코드 구조 점검(로드맵 55~60) 뒤 보리 요청으로 정리. 목표 구조는 **로드맵 59번의 목표**
> `[확인: 2026-10-12 보리 — 59번을 이 구조로, 문서로 남김]`. 한 번에 옮기지 않고 기능 폴더 하나씩 옮긴다.

## 1. 현재 폴더 구조

```
src/
├─ main.jsx               앱 시작점
├─ supabaseClient.js      서버 연결 (혼자 맨 위)
├─ *.css 10개             공용 디자인 (맨 위에 흩어짐)
├─ app/        25개       앱 시작·화면 주소·전체 틀 (AppShell, 로그인 확인, 뒤로가기)
├─ components/ 138개      화면
│   ├─ (폴더 없이 바로) 41개 + CSS 14개   MyPage, RevenuePage, ReportPage, 모달 여러 개 …
│   ├─ day-log/ calendar/ home/ revenue/ receivables/ documents/
│   ├─ cars/ clients/ drivers/ notifications/ auth/ side-menu/ tax-invoice/ report/
│   └─ shared/            공용 부품 (입력칸, 드롭다운, 뼈대 화면 …)
├─ domain/     51개       계산 규칙 (돈·날짜·수수료) — 화면·서버와 무관
├─ lib/        91개       서버 저장·동기화·불러오기 + 엑셀·PDF + 재수출 껍데기 (섞임)
└─ store/      17개       앱 안 데이터 보관 + 구독 훅
```

## 2. 현재 화면 계층

```
main.jsx
└─ App ─ 오류 보호막 없음
   ├─ /auth        AuthPage
   ├─ /welcome, /onboarding   OnboardingPage
   └─ /app/*       AppShell
       ├─ SideMenu · BottomNav · HydrationRetryBanner · PullRefreshIndicator
       └─ PageLoadErrorBoundary → AppShellRoutes (화면 약 20개)
           ├─ HomePage → AppTopBar, HomeAssistantBar, HomeMonthCard, HomeTodayCard, HomeTodoCard
           ├─ MainPageRoute → CalendarPage (Header, Grid, MonthSummary, SubLogBanner)
           │               → DayLogPage → CallDetailList, CallDetailForm, PalletSection,
           │                   FixedRouteChips, DayLogExpenses, DailyInspectionNotice … (13개)
           ├─ RevenuePage → OwnerRevenueView / DriverRevenueView   ← 거의 같은 두 벌
           ├─ ReceivablesPage → 목록 / 상세
           ├─ DocumentIssuePage → ReportPage, TaxInvoiceTab, DailyInspectionMonthSheet
           ├─ CarManagementPage → (한 줄 껍데기) → cars/CarListPage
           ├─ ClientManagementPage → (한 줄 껍데기) → clients/ClientListPage
           ├─ DriverConnectionPage, LinkedDriverManagementPage, LinkedDriverClientsPage
           └─ MyPage, PersonalInfoPage, AppSettingsPage, CustomerCenterPage, NoticePage …
```

모든 화면이 `showToast`·`ownerKey`·`onBack`·`onOpenMenu`를 `AppShellRoutes`에서 손으로 받는다.

## 3. 평가 — "정리된 역 구내" (곧은 선로 쪽 약 70%)

**곧은 부분**
- 파일끼리 돌고 도는 참조 0건(의존 분석 스크립트로 전수 확인).
- 계산 규칙(`domain/`)이 화면·서버와 완전히 분리.
- 200줄 넘는 코드 파일 8개뿐, 한 파일이 다 떠안은 곳 없음.

**엉킨 분기점**

| 위치 | 문제 |
|---|---|
| `lib/` ↔ `store/` | 서로를 부름 — 저장 흐름이 양방향 |
| `lib/` 91개 | 서버 통신·저장 장치·파일 내보내기가 한 서랍, `domain/`과 같은 이름 파일 11쌍 |
| `components/` 바로 아래 41개 | 어떤 기능 소속인지 이름으로만 앎 |
| 디자인 파일 | 맨 위 10개 · `components/` 바로 아래 14개 · 기능 폴더 안, 세 군데 |
| 껍데기 파일 | `CarManagementPage.jsx` 등 한 줄 넘겨주기, 재수출 파일 5개 |
| 화면 → 앱 시작 코드 | 아래층이 위층을 부르는 곳 5군데 |

## 4. 목표 구조 — 기능별 폴더 + 아래로만 흐르는 4층

```
src/
├─ app/                 ① 시작·주소·전체 틀만
│   ├─ App.jsx           전체 오류 보호막 + 알림(토스트) 공급자
│   ├─ routes/           화면 주소 목록
│   └─ shell/            AppShell, SideMenu, BottomNav
│
├─ features/            ② 기능별 묶음 (한 기능 = 한 폴더)
│   ├─ day-log/          ui/  hooks/  api/
│   ├─ calendar/  home/  revenue/  receivables/
│   ├─ documents/        운송비 내역서·세금계산서·일상점검표
│   ├─ cars/  clients/  drivers/
│   ├─ account/          로그인, 온보딩, 마이페이지, 개인정보
│   └─ settings/  support/  notifications/
│
├─ data/                ③ 저장 엔진 (지금 store + lib의 저장 장치)
│   ├─ store/            앱 안 보관 + 구독 훅
│   ├─ sync/             대기 저장 목록, 서버 동기화
│   ├─ hydrate/          앱 켤 때 불러오기
│   └─ cloud/            supabaseClient, 로그인 세션
│
├─ domain/              ④ 계산 규칙 (지금 그대로)
│
└─ shared/              어디서나 쓰는 공용
    ├─ ui/               PageHeader, ModalShell, ConfirmModal, ClearableInput, Skeleton
    ├─ hooks/            useToast, 저장 실패 공용 훅
    ├─ styles/           variables.css 등 공용 디자인
    └─ utils/            숫자·날짜 표시
```

**흐름 규칙**

```
app → features → data → domain
          ↘   shared   ↙
```
- 아래층은 위층을 부르지 않는다.
- 기능 폴더끼리 서로 부르지 않는다. 같이 쓸 것은 `shared/`·`data/`로 내린다.
- 규칙은 의존 분석 검사로 자동 확인할 수 있다(CI에 넣을지는 착수 때 보리 결정).

**목표 화면 계층**

```
App ─ 전체 오류 보호막 · 알림 공급자
├─ 로그인 / 온보딩            (보호막 안)
└─ AppShell
    ├─ SideMenu · BottomNav
    └─ 화면별 보호막 → 기능 화면 (예: features/day-log/ui/DayLogPage)
        ├─ 기능 하위 부품 — 그리기만
        ├─ 기능 훅 — 처리 흐름
        └─ shared/ui 부품 — 모달·입력칸·제목줄
```
알림은 손으로 넘기지 않고 필요한 곳에서 `useToast()`로 꺼내 쓴다.

## 5. 옮기는 순서

한 번에 옮기지 않는다(300개 넘는 파일·테스트 경로가 바뀌어 저장 경로가 깨질 위험). 로드맵 항목이 단계 역할:

| 로드맵 | 목표 구조에서의 의미 |
|---|---|
| 55 | `app/App.jsx` 전체 오류 보호막 |
| 56 | `shared/hooks` 저장 실패 공용 훅 |
| 57 | `shared/ui` `ModalShell` |
| 58 | 화면 두 벌 합치기 |
| 60 | 기능 훅으로 처리 흐름 옮기기 |
| 59 | 폴더 이동 — 기능 폴더 하나씩(예: `cars/` 먼저), 그다음 `data/`·`shared/` 정리 |

59번은 하위 슬라이스로 나누고, 나누는 방식은 착수 때 1회 보고(AGENTS §3).
