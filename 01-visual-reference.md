# HRGA IMAJIN - Visual Reference & Quick Lookup

## 🏗️ ARCHITECTURE OVERVIEW

```
┌─────────────────────────────────────────────────────────────────┐
│                     VERCEL HOSTING                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌────────────────────────┐    ┌────────────────────────────┐  │
│  │   NEXT.JS 14 (App      │    │   TAILWIND CSS             │  │
│  │   Router + SSR/SSG)    │    │   + Custom Components      │  │
│  ├────────────────────────┤    ├────────────────────────────┤  │
│  │  Pages                 │    │  Responsive Design         │  │
│  │  ├─ (auth)            │    │  └─ Mobile/Tablet/Desktop │  │
│  │  ├─ (dashboard)       │    │                            │  │
│  │  │  ├─ calendar       │    │  Accessibility             │  │
│  │  │  ├─ assets         │    │  └─ WCAG 2.1 AA            │  │
│  │  │  ├─ users          │    │                            │  │
│  │  │  └─ organization   │    │  Dark Mode Ready           │  │
│  │  └─ api/              │    │  └─ CSS Variables          │  │
│  │                       │    │                            │  │
│  │  TypeScript           │    │  Performance               │  │
│  │  └─ Type Safe         │    │  └─ Optimized Bundle       │  │
│  │                       │    │                            │  │
│  │  React 18             │    │                            │  │
│  │  └─ Server Components │    │                            │  │
│  └────────────────────────┘    └────────────────────────────┘  │
│                    │                      │                     │
└────────────────────┼──────────────────────┼─────────────────────┘
                     │                      │
         ┌───────────▼──────────┐  ┌────────▼─────────────┐
         │  SUPABASE            │  │  GITHUB              │
         ├──────────────────────┤  ├──────────────────────┤
         │                      │  │                      │
         │  PostgreSQL Database │  │  Version Control     │
         │  ├─ Users            │  │  ├─ Source Code      │
         │  ├─ Assets           │  │  ├─ CI/CD            │
         │  ├─ Requests         │  │  └─ Collaboration    │
         │  └─ Calendar         │  │                      │
         │                      │  │  GitHub Actions      │
         │  Authentication      │  │  └─ Automated Tests  │
         │  ├─ Auth.JS Support  │  │                      │
         │  ├─ JWT Tokens       │  │                      │
         │  └─ OAuth Ready      │  │                      │
         │                      │  │                      │
         │  Row Level Security  │  │                      │
         │  └─ Data Protection  │  │                      │
         │                      │  │                      │
         │  Storage             │  │                      │
         │  └─ File Uploads     │  │                      │
         │                      │  │                      │
         │  Realtime (Optional) │  │                      │
         │  └─ Live Updates     │  │                      │
         └──────────────────────┘  └──────────────────────┘
```

---

## 📁 FOLDER STRUCTURE (Tree View)

```
hrga-imajin/
│
├── 📄 Configuration Files
│   ├── package.json              ← All dependencies
│   ├── tsconfig.json             ← TypeScript config
│   ├── next.config.js            ← Next.js settings
│   ├── tailwind.config.ts        ← Tailwind tokens
│   ├── .eslintrc.json            ← Code quality
│   ├── .prettierrc.json          ← Code formatting
│   ├── jest.config.ts            ← Unit tests
│   ├── playwright.config.ts      ← E2E tests
│   └── .env.example              ← Environment template
│
├── 📁 src/
│   │
│   ├── app/                      (Next.js App Router)
│   │   ├── layout.tsx            ← Root layout
│   │   ├── globals.css           ← Global styles
│   │   ├── page.tsx              ← Home/redirect
│   │   │
│   │   ├── (auth)/               (Grouped, no prefix)
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   ├── register/
│   │   │   │   └── page.tsx
│   │   │   ├── reset-password/
│   │   │   │   └── page.tsx
│   │   │   └── callback/
│   │   │       └── route.ts      (OAuth)
│   │   │
│   │   ├── (dashboard)/          (Protected routes)
│   │   │   ├── layout.tsx        ← Dashboard layout
│   │   │   │
│   │   │   ├── dashboard/        ← Main dashboard
│   │   │   │   └── page.tsx
│   │   │   │
│   │   │   ├── calendar/         ← Calendar module
│   │   │   │   ├── page.tsx
│   │   │   │   └── [id]/
│   │   │   │
│   │   │   ├── assets/           ← Asset management
│   │   │   │   ├── page.tsx      (Inventory)
│   │   │   │   ├── requests/     (Requests)
│   │   │   │   ├── checkout/     (Checkout)
│   │   │   │   ├── checkin/      (Checkin)
│   │   │   │   ├── mutations/    (Movements)
│   │   │   │   └── maintenance/  (Maintenance)
│   │   │   │
│   │   │   ├── users/            ← User management
│   │   │   │   ├── page.tsx
│   │   │   │   └── [id]/
│   │   │   │
│   │   │   ├── organization/     ← Org structure
│   │   │   │   ├── sites/
│   │   │   │   ├── departments/
│   │   │   │   └── settings/
│   │   │   │
│   │   │   └── settings/         ← User settings
│   │   │       └── page.tsx
│   │   │
│   │   └── api/                  (API routes)
│   │       ├── auth/
│   │       │   ├── login/
│   │       │   ├── logout/
│   │       │   ├── refresh/
│   │       │   └── session/
│   │       │
│   │       ├── assets/
│   │       │   ├── route.ts      (CRUD)
│   │       │   └── [id]/
│   │       │
│   │       ├── asset-requests/
│   │       │   ├── route.ts
│   │       │   └── [id]/
│   │       │
│   │       ├── calendar/
│   │       │   ├── route.ts
│   │       │   └── [id]/
│   │       │
│   │       ├── users/
│   │       │   ├── route.ts
│   │       │   └── [id]/
│   │       │
│   │       ├── organization/
│   │       │   ├── sites/
│   │       │   └── departments/
│   │       │
│   │       └── webhooks/
│   │           └── supabase/
│   │
│   ├── components/               (React components)
│   │   ├── layout/
│   │   │   ├── sidebar.tsx
│   │   │   ├── header.tsx
│   │   │   ├── nav-menu.tsx
│   │   │   └── user-avatar.tsx
│   │   │
│   │   ├── workspace/
│   │   │   ├── workspace-header.tsx
│   │   │   ├── workspace-toolbar.tsx
│   │   │   ├── view-toggle.tsx
│   │   │   └── table-wrapper.tsx
│   │   │
│   │   ├── drawers/
│   │   │   ├── base-drawer.tsx
│   │   │   ├── asset-request-drawer.tsx
│   │   │   ├── checkout-drawer.tsx
│   │   │   └── ...
│   │   │
│   │   ├── forms/
│   │   │   ├── asset-form.tsx
│   │   │   ├── asset-request-form.tsx
│   │   │   └── ...
│   │   │
│   │   ├── tables/
│   │   │   ├── asset-table.tsx
│   │   │   ├── user-table.tsx
│   │   │   └── data-table.tsx
│   │   │
│   │   ├── cards/
│   │   │   ├── user-card.tsx
│   │   │   ├── stat-card.tsx
│   │   │   └── action-card.tsx
│   │   │
│   │   ├── status/
│   │   │   ├── status-badge.tsx
│   │   │   └── approval-progress.tsx
│   │   │
│   │   ├── common/
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── select.tsx
│   │   │   └── ...
│   │   │
│   │   └── icons/
│   │       └── index.tsx
│   │
│   ├── lib/                      (Utilities & helpers)
│   │   ├── supabase/
│   │   │   ├── client.ts         (Browser client)
│   │   │   ├── server.ts         (Server client)
│   │   │   ├── admin.ts          (Admin client)
│   │   │   └── types.ts          (Generated types)
│   │   │
│   │   ├── api/
│   │   │   ├── fetch.ts          (Custom fetch)
│   │   │   ├── assets.ts         (Asset calls)
│   │   │   ├── calendar.ts
│   │   │   ├── users.ts
│   │   │   └── organization.ts
│   │   │
│   │   ├── utils/
│   │   │   ├── format.ts         (Formatting)
│   │   │   ├── validation.ts     (Zod schemas)
│   │   │   ├── permissions.ts    (RBAC)
│   │   │   ├── date.ts           (Date utils)
│   │   │   ├── constants.ts      (App constants)
│   │   │   └── helpers.ts        (General helpers)
│   │   │
│   │   └── hooks/
│   │       ├── useAuth.ts        (Auth hook)
│   │       ├── useUser.ts        (User hook)
│   │       ├── useAssets.ts      (Asset data)
│   │       ├── useDrawer.ts      (Drawer state)
│   │       └── ...
│   │
│   ├── styles/
│   │   ├── globals.css
│   │   ├── tailwind.css
│   │   └── animations.css
│   │
│   └── types/
│       ├── index.ts
│       ├── database.ts           (DB types)
│       ├── api.ts                (API types)
│       ├── auth.ts               (Auth types)
│       ├── forms.ts              (Form types)
│       └── ui.ts                 (UI types)
│
├── 📁 public/
│   ├── images/
│   ├── icons/
│   └── fonts/
│
├── 📁 tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
└── 📁 docs/
    ├── SETUP.md
    ├── API.md
    ├── DATABASE.md
    └── DEPLOYMENT.md
```

---

## 🔄 DATA FLOW DIAGRAMS

### User Creation Flow
```
Admin Panel
    ↓
Create User Form
    ↓
Validation (Zod)
    ↓
POST /api/users
    ↓
Supabase
    ├─ Create auth user
    └─ Insert user record + department
    ↓
Response → Success Toast
```

### Asset Request Flow
```
Employee
    ↓
Create Asset Request Form
    ↓
POST /api/asset-requests
    ↓
Supabase
    ├─ Create request record
    └─ Set approval_stage = 'team_leader'
    ↓
Team Leader Notification
    ↓
Review & Approve
    ↓
POST /api/asset-requests/{id}/approve
    ↓
Move to next approval stage
    ↓
... (repeat for HRGA, Finance, Purchasing)
    ↓
Final Approval → Request Status = 'approved'
```

### Asset Checkout Flow
```
Employee selects Asset
    ↓
Create Checkout Request
    ↓
POST /api/assets/{id}/checkout
    ↓
Status: waiting_confirmation
    ↓
Admin Confirms → Status: ready_for_handover
    ↓
Employee Receives → Status: checked_out
    ↓
Asset status = 'in_checkout'
    ↓
Due date approaches → Reminder notification
    ↓
Employee Returns
    ↓
POST /api/assets/{id}/checkin
    ↓
Inspection → Condition checked
    ↓
Status: closed → Asset status = 'active'
```

---

## 📊 DATABASE RELATIONSHIP DIAGRAM

```
users ◄──────────────┐
  │                  │
  ├── 1:N ──────► departments
  │                  │
  │            1:N   ├──► sites
  │            
  ├── 1:N ──────► assets
  │                  │
  │                  ├── 1:N ──► checkout_requests
  │                  │                │
  │                  │                └── N:1 ──► users
  │                  │
  │                  ├── 1:N ──► checkin_requests
  │                  │
  │                  ├── 1:N ──► mutations
  │                  │
  │                  └── 1:N ──► maintenance_requests
  │
  ├── 1:N ──────► asset_requests
  │                  │
  │                  └── 1:N ──► approval_history
  │                                  │
  │                                  └── N:1 ──► users
  │
  └── 1:N ──────► calendar_events
                      │
                      └── N:N ──► users (participants)
```

---

## 🎯 State Management Strategy

```
┌────────────────────────────────────┐
│   Application State Management     │
├────────────────────────────────────┤
│                                    │
│  ┌────────────────────────────┐   │
│  │  Server State              │   │
│  │  (TanStack React Query)    │   │
│  │                            │   │
│  │  ✓ Assets list             │   │
│  │  ✓ Asset requests          │   │
│  │  ✓ Users                   │   │
│  │  ✓ Calendar events         │   │
│  │  ✓ Approval history        │   │
│  │                            │   │
│  │  Stale time: 5 minutes     │   │
│  │  Cache time: 10 minutes    │   │
│  └────────────────────────────┘   │
│                                    │
│  ┌────────────────────────────┐   │
│  │  Client State              │   │
│  │  (Zustand)                 │   │
│  │                            │   │
│  │  ✓ UI state                │   │
│  │    - Drawer open/close     │   │
│  │    - Sidebar expanded      │   │
│  │  ✓ Form state              │   │
│  │    - Loading states        │   │
│  │  ✓ Notifications           │   │
│  │    - Toast messages        │   │
│  │                            │   │
│  │  Persisted (localStorage)  │   │
│  │  - User preferences        │   │
│  │  - UI layout               │   │
│  └────────────────────────────┘   │
│                                    │
│  ┌────────────────────────────┐   │
│  │  Authentication State      │   │
│  │  (Supabase Auth)           │   │
│  │                            │   │
│  │  ✓ User session            │   │
│  │  ✓ Auth tokens             │   │
│  │  ✓ Permissions             │   │
│  │  ✓ User role               │   │
│  └────────────────────────────┘   │
│                                    │
└────────────────────────────────────┘
```

---

## 🔐 Authentication & Authorization Flow

```
┌─────────────────────────────────────┐
│   LOGIN PAGE                        │
├─────────────────────────────────────┤
│                                     │
│  Email + Password                   │
│    ↓                                │
│  POST /api/auth/login               │
│    ↓                                │
│  Supabase.auth.signInWithPassword   │
│    ↓                                │
│  Generate JWT tokens                │
│    ↓                                │
│  Store in HTTP-only cookie          │
│    ↓                                │
│  Redirect to /dashboard             │
│                                     │
└─────────────────────────────────────┘
           ↓
┌─────────────────────────────────────┐
│   AUTHENTICATED SESSION             │
├─────────────────────────────────────┤
│                                     │
│  On each API request:               │
│    ↓                                │
│  Check auth middleware              │
│    ↓                                │
│  Verify JWT token                   │
│    ↓                                │
│  Load user from Supabase            │
│    ↓                                │
│  Check user permissions             │
│    ↓                                │
│  Apply Row Level Security           │
│    ↓                                │
│  Process request                    │
│                                     │
│  Permissions checked for:           │
│  • Can view asset?                  │
│  • Can approve request?             │
│  • Can delete user?                 │
│  • Can access site?                 │
│                                     │
└─────────────────────────────────────┘
```

---

## 🎨 Component Hierarchy

```
RootLayout
├── Sidebar
│   ├── Logo
│   ├── NavRail
│   │   ├── NavIcon (Dashboard)
│   │   ├── NavIcon (Assets)
│   │   ├── NavIcon (Calendar)
│   │   ├── NavIcon (Users)
│   │   └── UserAvatar
│   └── NavPanel (expanded)
│       ├── AppTitle
│       ├── NavSection (Overview)
│       ├── NavSection (Transactions)
│       └── NavSection (Inventory)
│
├── DashboardLayout
│   ├── Header
│   │   ├── Breadcrumb
│   │   └── UserMenu
│   │
│   └── ContentStage
│       ├── PageContent
│       │   ├── WorkspaceHeader
│       │   ├── WorkspaceToolbar
│       │   └── MainContent
│       │       ├── Table / List / Grid
│       │       └── Pagination
│       │
│       ├── Drawer (CreateDrawer)
│       │   ├── DrawerHeader
│       │   ├── DrawerBody
│       │   │   ├── FormSection
│       │   │   └── FormFields
│       │   └── DrawerFooter
│       │       └── Actions
│       │
│       ├── Drawer (DetailDrawer)
│       │   └── ...
│       │
│       └── Toast Notifications
│
└── ErrorBoundary
```

---

## 🔌 API Response Pattern

### Success Response
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Value",
    ...
  },
  "meta": {
    "timestamp": "2026-03-25T14:30:00Z",
    "version": "1.0"
  }
}
```

### List Response
```json
{
  "success": true,
  "data": [
    { "id": "uuid", ... },
    { "id": "uuid", ... }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 10,
    "total": 100,
    "totalPages": 10,
    "hasNextPage": true
  }
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      {
        "field": "email",
        "message": "Email is required"
      }
    ]
  }
}
```

---

## 📊 Approval Workflow Stages

```
Request Created
    ↓
PENDING
├─ Stage: team_leader
├─ Status: pending
└─ Waiting for: Team Lead Review
    ↓
APPROVED by Team Lead
    ↓
TEAM_LEADER (Completed)
├─ Stage: hrga
├─ Status: pending
└─ Waiting for: HRGA Review
    ↓
APPROVED by HRGA
    ↓
HRGA (Completed)
├─ Stage: finance
├─ Status: pending
└─ Waiting for: Finance Review
    ↓
APPROVED by Finance
    ↓
FINANCE (Completed)
├─ Stage: purchasing
├─ Status: pending
└─ Waiting for: Purchasing Review
    ↓
APPROVED by Purchasing
    ↓
PURCHASING (Completed)
├─ Stage: approved
├─ Status: approved
└─ All approvals done! ✓

OR at any stage:
REJECTED → Status: rejected
REVISION_REQUESTED → Status: revision_requested
```

---

## 🎨 Color Palette (Tailwind)

```
Primary Accent (Purple)
└─ accent-500: #9d60e6
└─ accent-600: #8b4ae8
└─ accent-700: #7935e8

Status Colors
├─ Success (Green): #10b981
├─ Warning (Orange): #f59e0b
├─ Danger (Red): #ef4444
└─ Info (Blue): #3b82f6

Neutral
├─ Gray-50: #fafafa
├─ Gray-100: #f5f5f5
├─ Gray-500: #6b7280
└─ Gray-900: #111827
```

---

## 🚀 Deployment Checklist

```
PRE-DEPLOYMENT
├─ [ ] All tests passing
├─ [ ] Code reviewed
├─ [ ] Database migrations tested
├─ [ ] Environment variables set
├─ [ ] Secrets configured in Vercel
└─ [ ] Performance optimized

DEPLOYMENT
├─ [ ] Push to main branch
├─ [ ] GitHub Actions run
├─ [ ] Build succeeds
├─ [ ] Tests pass
└─ [ ] Deploy to Vercel (automatic)

POST-DEPLOYMENT
├─ [ ] Verify in production
├─ [ ] Check database migrations
├─ [ ] Test critical workflows
├─ [ ] Monitor error logs
└─ [ ] Smoke tests passed
```

---

## 🔍 Monitoring & Debugging

```
Vercel Dashboard
├─ Deployment logs
├─ Performance metrics
├─ Error tracking
└─ API routes analytics

Supabase Dashboard
├─ Database size
├─ Auth users
├─ API usage
├─ Realtime connections
└─ Backup status

Browser DevTools
├─ Network tab (API calls)
├─ Console (errors/warnings)
├─ Performance (Core Web Vitals)
└─ Application (LocalStorage/Cookies)
```

---

## 📱 Responsive Breakpoints

```
Mobile (sm): < 640px
├─ Single column layout
├─ Full-width cards
├─ Bottom navigation
└─ Stacked forms

Tablet (md): 640px - 1024px
├─ 2 column layout
├─ Sidebar collapses
├─ Side navigation
└─ Optimized spacing

Desktop (lg+): > 1024px
├─ Full sidebar expanded
├─ Multi-column layouts
├─ Optimized for mouse
└─ Full feature set
```

---

## ✨ Performance Optimization

```
Frontend
├─ Code splitting (dynamic imports)
├─ Image optimization (next/image)
├─ CSS-in-JS vs Tailwind
├─ Lazy loading components
└─ Caching strategy

Backend
├─ Database indexes
├─ Query optimization
├─ Connection pooling
├─ Rate limiting
└─ Compression

Deployment
├─ CDN caching
├─ Edge functions
├─ Server compression
├─ HTTP/2 push
└─ Bundle analysis
```

---

**Version**: 1.0  
**Last Updated**: March 2026  
**Status**: ✅ Complete
