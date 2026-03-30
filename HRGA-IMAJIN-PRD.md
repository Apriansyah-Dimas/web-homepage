# HRGA IMAJIN - Product Requirements Document (PRD)
## Next.js + Supabase Implementation Guide

**Version:** 1.0  
**Last Updated:** March 2026  
**Status:** Ready for Development

---

## TABLE OF CONTENTS
1. [Project Overview](#1-project-overview)
2. [Tech Stack](#2-tech-stack)
3. [Project Structure](#3-project-structure)
4. [Database Schema](#4-database-schema)
5. [API Layer](#5-api-layer)
6. [Authentication & Authorization](#6-authentication--authorization)
7. [Component Architecture](#7-component-architecture)
8. [Pages & Routing](#8-pages--routing)
9. [State Management](#9-state-management)
10. [Styling Strategy](#10-styling-strategy)
11. [Development Setup](#11-development-setup)
12. [Deployment](#12-deployment)
13. [Performance Optimization](#13-performance-optimization)
14. [Testing Strategy](#14-testing-strategy)
15. [Error Handling](#15-error-handling)

---

## 1. PROJECT OVERVIEW

### 1.1 Application Name & Purpose
**HRGA IMAJIN** - Enterprise HR & General Affairs Management System with Asset Lifecycle Management

### 1.2 Core Features
- **Calendar Module**: Event scheduling and management
- **Asset Management**: Complete asset lifecycle tracking
- **Asset Requests**: Approval workflow for asset requests
- **Asset Operations**: Check-out, check-in, mutations, maintenance
- **User Management**: Employee and organizational structure
- **Organization Settings**: Sites, departments, and access control

### 1.3 Key Objectives
1. Streamline HR and asset management workflows
2. Provide transparent approval pipelines
3. Track asset lifecycle from purchase to retirement
4. Enable role-based access control
5. Generate actionable reports and analytics

### 1.4 Target Users
- HR Managers
- Asset Managers
- Department Heads
- Finance Team
- Purchasing Department
- Employees

---

## 2. TECH STACK

### 2.1 Frontend
| Technology | Version | Purpose |
|------------|---------|---------|
| **Next.js** | 14.x+ | React framework with SSR/SSG |
| **React** | 18.x+ | UI library |
| **TypeScript** | 5.x+ | Type safety |
| **Tailwind CSS** | 3.4+ | Utility-first CSS |
| **Shadcn/UI** | Latest | Pre-built accessible components |
| **React Hook Form** | 7.x+ | Form state management |
| **Zod** | Latest | Schema validation |
| **TanStack Query** | 5.x+ | Server state management |
| **Zustand** | 4.x+ | Client state management |
| **date-fns** | 3.x+ | Date utilities |

### 2.2 Backend
| Technology | Version | Purpose |
|------------|---------|---------|
| **Supabase** | Latest | BaaS (PostgreSQL, Auth, Realtime) |
| **PostgreSQL** | 15+ | Database |
| **PostgREST** | Auto | Auto-generated REST API |
| **Supabase Realtime** | Latest | Real-time subscriptions |
| **Next.js API Routes** | 14.x+ | Custom API endpoints |

### 2.3 DevOps & Deployment
| Technology | Purpose |
|------------|---------|
| **Vercel** | Hosting & deployment |
| **GitHub** | Version control |
| **Environment Variables** | Configuration management |
| **GitHub Actions** | CI/CD (optional) |

### 2.4 Development Tools
| Tool | Purpose |
|------|---------|
| **pnpm** | Package manager |
| **ESLint** | Code quality |
| **Prettier** | Code formatting |
| **TypeScript** | Type checking |
| **Jest** | Unit testing |
| **Playwright** | E2E testing |

---

## 3. PROJECT STRUCTURE

### 3.1 Directory Layout
```
hrga-imajin/
├── .env.local                    # Environment variables (git ignored)
├── .env.example                  # Example environment variables
├── .gitignore
├── .eslintrc.json               # ESLint configuration
├── .prettierrc.json             # Prettier configuration
├── tsconfig.json                # TypeScript configuration
├── next.config.js               # Next.js configuration
├── tailwind.config.ts           # Tailwind configuration
├── package.json
├── pnpm-lock.yaml
│
├── src/
│   ├── app/                     # Next.js App Router
│   │   ├── layout.tsx           # Root layout
│   │   ├── globals.css          # Global styles
│   │   ├── page.tsx             # Home page / Dashboard redirect
│   │   │
│   │   ├── (auth)/              # Auth routes (grouped, no prefix)
│   │   │   ├── login/
│   │   │   │   └── page.tsx
│   │   │   ├── register/
│   │   │   │   └── page.tsx
│   │   │   ├── reset-password/
│   │   │   │   └── page.tsx
│   │   │   └── callback/        # OAuth callback
│   │   │       └── route.ts
│   │   │
│   │   ├── (dashboard)/         # Protected routes (grouped, no prefix)
│   │   │   ├── layout.tsx       # Dashboard layout with sidebar
│   │   │   │
│   │   │   ├── dashboard/
│   │   │   │   └── page.tsx     # Main dashboard
│   │   │   │
│   │   │   ├── calendar/
│   │   │   │   ├── page.tsx     # Calendar list view
│   │   │   │   └── [id]/
│   │   │   │       └── page.tsx # Event detail view
│   │   │   │
│   │   │   ├── assets/
│   │   │   │   ├── page.tsx     # Asset inventory
│   │   │   │   ├── requests/    # Asset request workflow
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   └── [id]/
│   │   │   │   │       └── page.tsx
│   │   │   │   ├── checkout/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   └── [id]/
│   │   │   │   │       └── page.tsx
│   │   │   │   ├── checkin/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   └── [id]/
│   │   │   │   │       └── page.tsx
│   │   │   │   ├── mutations/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   └── [id]/
│   │   │   │   │       └── page.tsx
│   │   │   │   └── maintenance/
│   │   │   │       ├── page.tsx
│   │   │   │       └── [id]/
│   │   │   │           └── page.tsx
│   │   │   │
│   │   │   ├── users/
│   │   │   │   ├── page.tsx     # User management list
│   │   │   │   └── [id]/
│   │   │   │       └── page.tsx # User detail/edit
│   │   │   │
│   │   │   ├── organization/
│   │   │   │   ├── sites/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   └── [id]/
│   │   │   │   │       └── page.tsx
│   │   │   │   ├── departments/
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   └── [id]/
│   │   │   │   │       └── page.tsx
│   │   │   │   └── settings/
│   │   │   │       └── page.tsx # Organization settings
│   │   │   │
│   │   │   └── settings/
│   │   │       └── page.tsx     # User settings
│   │   │
│   │   └── api/                 # API routes
│   │       ├── auth/
│   │       │   ├── login/route.ts
│   │       │   ├── logout/route.ts
│   │       │   ├── refresh/route.ts
│   │       │   └── session/route.ts
│   │       │
│   │       ├── assets/
│   │       │   ├── route.ts          # GET /api/assets, POST
│   │       │   ├── [id]/route.ts     # GET, PATCH, DELETE
│   │       │   ├── [id]/checkout/route.ts
│   │       │   ├── [id]/checkin/route.ts
│   │       │   ├── [id]/mutation/route.ts
│   │       │   ├── [id]/maintenance/route.ts
│   │       │   └── export/route.ts   # CSV/Excel export
│   │       │
│   │       ├── calendar/
│   │       │   ├── route.ts          # GET /api/calendar, POST
│   │       │   └── [id]/route.ts     # GET, PATCH, DELETE
│   │       │
│   │       ├── users/
│   │       │   ├── route.ts          # GET /api/users, POST
│   │       │   └── [id]/route.ts     # GET, PATCH, DELETE
│   │       │
│   │       ├── organization/
│   │       │   ├── sites/
│   │       │   │   ├── route.ts
│   │       │   │   └── [id]/route.ts
│   │       │   └── departments/
│   │       │       ├── route.ts
│   │       │       └── [id]/route.ts
│   │       │
│   │       └── webhooks/
│   │           └── supabase/route.ts # Supabase webhooks
│   │
│   ├── components/               # Reusable components
│   │   ├── layout/
│   │   │   ├── sidebar.tsx
│   │   │   ├── header.tsx
│   │   │   ├── nav-menu.tsx
│   │   │   └── user-avatar.tsx
│   │   │
│   │   ├── workspace/            # Workspace-specific components
│   │   │   ├── workspace-header.tsx
│   │   │   ├── workspace-toolbar.tsx
│   │   │   ├── view-toggle.tsx
│   │   │   └── table-wrapper.tsx
│   │   │
│   │   ├── drawers/              # Drawer components
│   │   │   ├── base-drawer.tsx
│   │   │   ├── asset-request-drawer.tsx
│   │   │   ├── asset-detail-drawer.tsx
│   │   │   ├── checkout-drawer.tsx
│   │   │   ├── checkin-drawer.tsx
│   │   │   ├── event-drawer.tsx
│   │   │   └── user-drawer.tsx
│   │   │
│   │   ├── forms/                # Form components
│   │   │   ├── asset-form.tsx
│   │   │   ├── asset-request-form.tsx
│   │   │   ├── event-form.tsx
│   │   │   ├── user-form.tsx
│   │   │   └── org-form.tsx
│   │   │
│   │   ├── tables/               # Table components
│   │   │   ├── asset-table.tsx
│   │   │   ├── asset-request-table.tsx
│   │   │   ├── checkout-table.tsx
│   │   │   ├── checkin-table.tsx
│   │   │   ├── user-table.tsx
│   │   │   └── data-table.tsx    # Generic data table
│   │   │
│   │   ├── cards/                # Card components
│   │   │   ├── user-card.tsx
│   │   │   ├── stat-card.tsx
│   │   │   └── action-card.tsx
│   │   │
│   │   ├── status/               # Status display
│   │   │   ├── status-badge.tsx
│   │   │   ├── approval-progress.tsx
│   │   │   └── status-indicator.tsx
│   │   │
│   │   ├── common/               # Common UI elements
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── select.tsx
│   │   │   ├── textarea.tsx
│   │   │   ├── checkbox.tsx
│   │   │   ├── radio.tsx
│   │   │   ├── dialog.tsx
│   │   │   ├── toast.tsx
│   │   │   └── loading.tsx
│   │   │
│   │   └── icons/
│   │       └── index.tsx         # Icon components
│   │
│   ├── lib/                      # Utility functions & helpers
│   │   ├── supabase/
│   │   │   ├── client.ts         # Supabase client
│   │   │   ├── server.ts         # Server-side Supabase
│   │   │   ├── admin.ts          # Admin client for sensitive ops
│   │   │   └── types.ts          # Database types (generated)
│   │   │
│   │   ├── api/                  # API client
│   │   │   ├── fetch.ts          # Custom fetch wrapper
│   │   │   ├── assets.ts         # Asset API calls
│   │   │   ├── calendar.ts       # Calendar API calls
│   │   │   ├── users.ts          # User API calls
│   │   │   └── organization.ts   # Org API calls
│   │   │
│   │   ├── utils/
│   │   │   ├── format.ts         # Formatting utilities
│   │   │   ├── validation.ts     # Validation schemas
│   │   │   ├── permissions.ts    # Permission checking
│   │   │   ├── date.ts           # Date utilities
│   │   │   ├── constants.ts      # App constants
│   │   │   └── helpers.ts        # General helpers
│   │   │
│   │   └── hooks/                # Custom React hooks
│   │       ├── useAuth.ts        # Authentication hook
│   │       ├── useUser.ts        # Current user hook
│   │       ├── useAssets.ts      # Asset data hook
│   │       ├── useCalendar.ts    # Calendar data hook
│   │       ├── useDrawer.ts      # Drawer state hook
│   │       ├── useNotification.ts # Notification hook
│   │       └── usePagination.ts  # Pagination hook
│   │
│   ├── styles/                   # Global styles
│   │   ├── globals.css
│   │   ├── tailwind.css
│   │   └── animations.css
│   │
│   └── types/                    # TypeScript type definitions
│       ├── index.ts              # Barrel file
│       ├── database.ts           # Database types
│       ├── api.ts                # API response types
│       ├── auth.ts               # Auth types
│       ├── forms.ts              # Form input types
│       └── ui.ts                 # UI component types
│
├── public/                       # Static assets
│   ├── images/
│   ├── icons/
│   └── fonts/
│
├── tests/                        # Test files
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
└── docs/                         # Documentation
    ├── SETUP.md
    ├── API.md
    ├── DATABASE.md
    └── DEPLOYMENT.md
```

### 3.2 File Naming Conventions
- **React Components**: PascalCase (e.g., `AssetTable.tsx`)
- **Functions/Utils**: camelCase (e.g., `formatDate.ts`)
- **Types/Interfaces**: PascalCase (e.g., `AssetRecord`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `DEFAULT_PAGE_SIZE`)
- **API Routes**: kebab-case (e.g., `/api/asset-requests`)

---

## 4. DATABASE SCHEMA

### 4.1 Core Tables

#### Users Table
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255) NOT NULL,
  phone VARCHAR(20),
  avatar_url VARCHAR(500),
  department_id UUID REFERENCES departments(id),
  position VARCHAR(100),
  role VARCHAR(50) NOT NULL, -- 'admin', 'manager', 'employee'
  status VARCHAR(20) DEFAULT 'active', -- 'active', 'inactive'
  permissions TEXT[] DEFAULT '{}', -- Array of permission codes
  join_date DATE,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now(),
  deleted_at TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_department_id ON users(department_id);
CREATE INDEX idx_users_role ON users(role);
CREATE INDEX idx_users_status ON users(status);
```

#### Departments Table
```sql
CREATE TABLE departments (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  code VARCHAR(50) UNIQUE NOT NULL,
  site_id UUID NOT NULL REFERENCES sites(id),
  head_id UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now(),
  deleted_at TIMESTAMP
);

CREATE INDEX idx_departments_site_id ON departments(site_id);
CREATE INDEX idx_departments_code ON departments(code);
```

#### Sites Table
```sql
CREATE TABLE sites (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  code VARCHAR(50) UNIQUE NOT NULL,
  address VARCHAR(500),
  city VARCHAR(100),
  country VARCHAR(100),
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now(),
  deleted_at TIMESTAMP
);

CREATE INDEX idx_sites_code ON sites(code);
```

#### Assets Table
```sql
CREATE TABLE assets (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  asset_number VARCHAR(100) UNIQUE NOT NULL,
  category VARCHAR(100) NOT NULL, -- 'Electronics', 'Furniture', etc.
  description TEXT,
  site_id UUID NOT NULL REFERENCES sites(id),
  current_location VARCHAR(255),
  assigned_to UUID REFERENCES users(id),
  
  -- Status & Lifecycle
  status VARCHAR(50) DEFAULT 'active', -- 'active', 'maintenance', 'retired', 'lost'
  purchase_date DATE,
  purchase_price DECIMAL(15, 2),
  warranty_expiry DATE,
  
  -- Media
  image_url VARCHAR(500),
  documents TEXT[], -- Array of document URLs
  
  -- Audit
  created_by UUID NOT NULL REFERENCES users(id),
  updated_by UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now(),
  deleted_at TIMESTAMP
);

CREATE INDEX idx_assets_asset_number ON assets(asset_number);
CREATE INDEX idx_assets_category ON assets(category);
CREATE INDEX idx_assets_site_id ON assets(site_id);
CREATE INDEX idx_assets_assigned_to ON assets(assigned_to);
CREATE INDEX idx_assets_status ON assets(status);
```

#### Asset Requests Table
```sql
CREATE TABLE asset_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  request_number VARCHAR(50) UNIQUE NOT NULL,
  requester_id UUID NOT NULL REFERENCES users(id),
  requested_asset_name VARCHAR(255) NOT NULL,
  asset_category VARCHAR(100),
  site_asset_location VARCHAR(255),
  asset_price DECIMAL(15, 2),
  purchase_link VARCHAR(500),
  purpose TEXT NOT NULL,
  estimated_duration VARCHAR(50),
  deadline_date DATE,
  
  -- Approval workflow
  approval_stage VARCHAR(50) DEFAULT 'pending', 
  -- 'pending', 'team_leader', 'hrga', 'finance', 'purchasing', 'approved', 'rejected'
  approval_status VARCHAR(50) DEFAULT 'pending',
  
  -- Timestamps
  submitted_at TIMESTAMP DEFAULT now(),
  approved_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now(),
  deleted_at TIMESTAMP
);

CREATE INDEX idx_asset_requests_requester_id ON asset_requests(requester_id);
CREATE INDEX idx_asset_requests_approval_stage ON asset_requests(approval_stage);
CREATE INDEX idx_asset_requests_request_number ON asset_requests(request_number);
```

#### Approval History Table
```sql
CREATE TABLE approval_history (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  asset_request_id UUID NOT NULL REFERENCES asset_requests(id) ON DELETE CASCADE,
  stage VARCHAR(50) NOT NULL, -- 'team_leader', 'hrga', 'finance', 'purchasing'
  action VARCHAR(20) NOT NULL, -- 'approved', 'rejected', 'revised'
  approved_by UUID NOT NULL REFERENCES users(id),
  notes TEXT,
  created_at TIMESTAMP DEFAULT now()
);

CREATE INDEX idx_approval_history_request_id ON approval_history(asset_request_id);
CREATE INDEX idx_approval_history_stage ON approval_history(stage);
```

#### CheckOut Requests Table
```sql
CREATE TABLE checkout_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  checkout_number VARCHAR(50) UNIQUE NOT NULL,
  asset_id UUID NOT NULL REFERENCES assets(id),
  requester_id UUID NOT NULL REFERENCES users(id),
  pickup_location VARCHAR(255) NOT NULL,
  pickup_date DATE NOT NULL,
  planned_return_date DATE NOT NULL,
  
  status VARCHAR(50) DEFAULT 'waiting_confirmation',
  -- 'waiting_confirmation', 'ready_for_handover', 'checked_out', 'returned'
  
  checkout_date TIMESTAMP,
  checkedout_by UUID REFERENCES users(id),
  notes TEXT,
  
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now(),
  deleted_at TIMESTAMP
);

CREATE INDEX idx_checkout_requests_asset_id ON checkout_requests(asset_id);
CREATE INDEX idx_checkout_requests_requester_id ON checkout_requests(requester_id);
CREATE INDEX idx_checkout_requests_status ON checkout_requests(status);
```

#### CheckIn Requests Table
```sql
CREATE TABLE checkin_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  checkin_number VARCHAR(50) UNIQUE NOT NULL,
  asset_id UUID NOT NULL REFERENCES assets(id),
  returned_by UUID NOT NULL REFERENCES users(id),
  received_at_location VARCHAR(255),
  
  condition VARCHAR(50) NOT NULL,
  -- 'good', 'minor_repair', 'lens_cleaning', 'damaged'
  
  status VARCHAR(50) DEFAULT 'inspection_needed',
  -- 'inspection_needed', 'closed'
  
  inspection_date TIMESTAMP,
  inspected_by UUID REFERENCES users(id),
  notes TEXT,
  
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);

CREATE INDEX idx_checkin_requests_asset_id ON checkin_requests(asset_id);
CREATE INDEX idx_checkin_requests_returned_by ON checkin_requests(returned_by);
```

#### Mutations Table
```sql
CREATE TABLE mutations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  mutation_number VARCHAR(50) UNIQUE NOT NULL,
  asset_id UUID NOT NULL REFERENCES assets(id),
  from_location VARCHAR(255) NOT NULL,
  to_location VARCHAR(255) NOT NULL,
  initiated_by UUID NOT NULL REFERENCES users(id),
  scheduled_date TIMESTAMP,
  
  status VARCHAR(50) DEFAULT 'queued',
  -- 'queued', 'in_transit', 'completed'
  
  completed_date TIMESTAMP,
  notes TEXT,
  
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);

CREATE INDEX idx_mutations_asset_id ON mutations(asset_id);
CREATE INDEX idx_mutations_status ON mutations(status);
```

#### Maintenance Requests Table
```sql
CREATE TABLE maintenance_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  maintenance_number VARCHAR(50) UNIQUE NOT NULL,
  asset_id UUID NOT NULL REFERENCES assets(id),
  issue_description TEXT NOT NULL,
  requester_id UUID NOT NULL REFERENCES users(id),
  
  priority VARCHAR(20) NOT NULL, -- 'high', 'medium', 'low'
  status VARCHAR(50) DEFAULT 'pending_review',
  -- 'pending_review', 'in_progress', 'scheduled', 'completed'
  
  assigned_to UUID REFERENCES users(id),
  scheduled_date TIMESTAMP,
  completed_date TIMESTAMP,
  notes TEXT,
  
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now()
);

CREATE INDEX idx_maintenance_requests_asset_id ON maintenance_requests(asset_id);
CREATE INDEX idx_maintenance_requests_status ON maintenance_requests(status);
CREATE INDEX idx_maintenance_requests_priority ON maintenance_requests(priority);
```

#### Calendar Events Table
```sql
CREATE TABLE calendar_events (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title VARCHAR(255) NOT NULL,
  description TEXT,
  event_type VARCHAR(50), -- 'meeting', 'holiday', 'deadline', 'event'
  
  start_date TIMESTAMP NOT NULL,
  end_date TIMESTAMP NOT NULL,
  location VARCHAR(255),
  
  created_by UUID NOT NULL REFERENCES users(id),
  participants UUID[] DEFAULT '{}',
  
  is_all_day BOOLEAN DEFAULT false,
  is_recurring BOOLEAN DEFAULT false,
  recurring_pattern VARCHAR(50), -- 'daily', 'weekly', 'monthly', 'yearly'
  
  attachments TEXT[] DEFAULT '{}',
  reminders TEXT[] DEFAULT '{}',
  tags TEXT[] DEFAULT '{}',
  
  created_at TIMESTAMP DEFAULT now(),
  updated_at TIMESTAMP DEFAULT now(),
  deleted_at TIMESTAMP
);

CREATE INDEX idx_calendar_events_created_by ON calendar_events(created_by);
CREATE INDEX idx_calendar_events_start_date ON calendar_events(start_date);
CREATE INDEX idx_calendar_events_participants ON calendar_events USING gin(participants);
```

### 4.2 Indexes & Performance
```sql
-- Composite indexes for common queries
CREATE INDEX idx_assets_status_site ON assets(status, site_id);
CREATE INDEX idx_checkout_status_date ON checkout_requests(status, created_at);
CREATE INDEX idx_approval_request_stage ON approval_history(asset_request_id, stage);

-- Enable RLS
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE assets ENABLE ROW LEVEL SECURITY;
ALTER TABLE asset_requests ENABLE ROW LEVEL SECURITY;
ALTER TABLE calendar_events ENABLE ROW LEVEL SECURITY;

-- Audit triggers
CREATE TRIGGER set_updated_at
BEFORE UPDATE ON assets
FOR EACH ROW
EXECUTE FUNCTION update_updated_at_column();
```

### 4.3 Row Level Security (RLS) Policies

#### Users Table
```sql
-- Users can see their own profile
CREATE POLICY "Users can view own profile" ON users
  FOR SELECT USING (auth.uid() = id);

-- Admins and managers can see all users
CREATE POLICY "Managers can view all users" ON users
  FOR SELECT USING (
    auth.uid() IN (
      SELECT id FROM users WHERE role IN ('admin', 'manager')
    )
  );
```

#### Assets Table
```sql
-- Employees can view assets at their site
CREATE POLICY "Users can view assets" ON assets
  FOR SELECT USING (
    site_id IN (
      SELECT site_id FROM departments 
      WHERE id = (SELECT department_id FROM users WHERE id = auth.uid())
    )
  );

-- Only admins can create assets
CREATE POLICY "Only admins can create assets" ON assets
  FOR INSERT WITH CHECK (
    auth.uid() IN (SELECT id FROM users WHERE role = 'admin')
  );
```

#### Asset Requests Table
```sql
-- Users can view their own requests
CREATE POLICY "Users can view own requests" ON asset_requests
  FOR SELECT USING (requester_id = auth.uid());

-- Approvers can see requests at their stage
CREATE POLICY "Approvers can view requests" ON asset_requests
  FOR SELECT USING (
    approval_stage = 'team_leader' OR -- logic for each approval stage
    approval_stage = 'hrga' OR
    approval_stage = 'finance' OR
    approval_stage = 'purchasing'
  );
```

---

## 5. API LAYER

### 5.1 API Routes Structure

#### Authentication Routes
```
POST   /api/auth/login              # User login
POST   /api/auth/register           # User registration
POST   /api/auth/logout             # User logout
POST   /api/auth/refresh            # Refresh session
GET    /api/auth/session            # Get current session
POST   /api/auth/reset-password     # Password reset
```

#### Assets Routes
```
GET    /api/assets                  # List assets (paginated)
POST   /api/assets                  # Create asset
GET    /api/assets/[id]             # Get asset detail
PATCH  /api/assets/[id]             # Update asset
DELETE /api/assets/[id]             # Delete asset
POST   /api/assets/[id]/checkout    # Create checkout
POST   /api/assets/[id]/checkin     # Create checkin
POST   /api/assets/[id]/mutation    # Create mutation
POST   /api/assets/[id]/maintenance # Create maintenance
GET    /api/assets/export           # Export assets (CSV/Excel)
POST   /api/assets/import           # Import assets
```

#### Asset Requests Routes
```
GET    /api/asset-requests          # List requests
POST   /api/asset-requests          # Create request
GET    /api/asset-requests/[id]     # Get request detail
PATCH  /api/asset-requests/[id]     # Update request
DELETE /api/asset-requests/[id]     # Cancel request
POST   /api/asset-requests/[id]/approve     # Approve
POST   /api/asset-requests/[id]/reject      # Reject
POST   /api/asset-requests/[id]/revise      # Request revision
```

#### Calendar Routes
```
GET    /api/calendar                # List events
POST   /api/calendar                # Create event
GET    /api/calendar/[id]           # Get event detail
PATCH  /api/calendar/[id]           # Update event
DELETE /api/calendar/[id]           # Delete event
```

#### Users Routes
```
GET    /api/users                   # List users
POST   /api/users                   # Create user
GET    /api/users/[id]              # Get user detail
PATCH  /api/users/[id]              # Update user
DELETE /api/users/[id]              # Delete user
GET    /api/users/[id]/permissions  # Get user permissions
PATCH  /api/users/[id]/permissions  # Update permissions
```

#### Organization Routes
```
GET    /api/organization/sites      # List sites
POST   /api/organization/sites      # Create site
GET    /api/organization/sites/[id] # Get site detail
PATCH  /api/organization/sites/[id] # Update site
DELETE /api/organization/sites/[id] # Delete site

GET    /api/organization/departments      # List departments
POST   /api/organization/departments      # Create department
GET    /api/organization/departments/[id] # Get department detail
PATCH  /api/organization/departments/[id] # Update department
DELETE /api/organization/departments/[id] # Delete department
```

### 5.2 API Response Format

#### Success Response
```typescript
{
  success: true,
  data: {
    id: "uuid",
    name: "Asset Name",
    ...
  },
  meta: {
    timestamp: "2026-03-25T14:30:00Z",
    version: "1.0"
  }
}
```

#### Error Response
```typescript
{
  success: false,
  error: {
    code: "VALIDATION_ERROR",
    message: "Validation failed",
    details: [
      {
        field: "name",
        message: "Name is required"
      }
    ]
  },
  meta: {
    timestamp: "2026-03-25T14:30:00Z",
    requestId: "req_xyz123"
  }
}
```

#### Paginated Response
```typescript
{
  success: true,
  data: [
    { id: "1", name: "Asset 1" },
    { id: "2", name: "Asset 2" }
  ],
  pagination: {
    page: 1,
    pageSize: 10,
    total: 25,
    totalPages: 3,
    hasNextPage: true,
    hasPreviousPage: false
  },
  meta: {
    timestamp: "2026-03-25T14:30:00Z"
  }
}
```

### 5.3 API Client Pattern

```typescript
// lib/api/fetch.ts
type ApiMethod = 'GET' | 'POST' | 'PATCH' | 'DELETE' | 'PUT';

interface FetchOptions {
  method?: ApiMethod;
  body?: any;
  params?: Record<string, any>;
  headers?: Record<string, string>;
  tags?: string[];
}

export async function apiFetch<T = any>(
  endpoint: string,
  options?: FetchOptions
): Promise<T> {
  const url = new URL(endpoint, process.env.NEXT_PUBLIC_APP_URL);
  
  // Add query params
  if (options?.params) {
    Object.entries(options.params).forEach(([key, value]) => {
      url.searchParams.append(key, String(value));
    });
  }

  const response = await fetch(url.toString(), {
    method: options?.method || 'GET',
    headers: {
      'Content-Type': 'application/json',
      ...options?.headers,
    },
    body: options?.body ? JSON.stringify(options.body) : undefined,
    next: { tags: options?.tags },
  });

  if (!response.ok) {
    const error = await response.json();
    throw new Error(error.error?.message || 'API request failed');
  }

  const result = await response.json();
  return result.data;
}
```

---

## 6. AUTHENTICATION & AUTHORIZATION

### 6.1 Authentication Flow

#### Supabase Auth Setup
```typescript
// lib/supabase/client.ts
import { createBrowserClient } from '@supabase/ssr';

export const supabase = createBrowserClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
);

// lib/supabase/server.ts
import { createServerClient, type CookieOptions } from '@supabase/ssr';
import { cookies } from 'next/headers';

export const supabaseServer = () => {
  const cookieStore = cookies();

  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() {
          return cookieStore.getAll();
        },
        setAll(cookiesToSet) {
          try {
            cookiesToSet.forEach(({ name, value, options }) =>
              cookieStore.set(name, value, options as CookieOptions)
            );
          } catch {
            // Handle error
          }
        },
      },
    }
  );
};
```

#### Login Flow
```typescript
// app/api/auth/login/route.ts
import { supabaseServer } from '@/lib/supabase/server';
import { NextRequest, NextResponse } from 'next/server';

export async function POST(request: NextRequest) {
  const { email, password } = await request.json();
  const supabase = supabaseServer();

  const { data, error } = await supabase.auth.signInWithPassword({
    email,
    password,
  });

  if (error) {
    return NextResponse.json(
      { success: false, error: { message: error.message } },
      { status: 400 }
    );
  }

  return NextResponse.json({
    success: true,
    data: {
      user: data.user,
      session: data.session,
    },
  });
}
```

### 6.2 Authorization & Permissions

#### Permission System
```typescript
// lib/utils/permissions.ts
export const PERMISSIONS = {
  // Calendar
  CALENDAR_VIEW: 'calendar_view',
  CALENDAR_CREATE: 'calendar_create',
  CALENDAR_EDIT: 'calendar_edit',
  CALENDAR_DELETE: 'calendar_delete',

  // Asset Management
  ASSETS_VIEW: 'assets_view',
  ASSETS_CREATE: 'assets_create',
  ASSETS_EDIT: 'assets_edit',
  ASSETS_DELETE: 'assets_delete',

  // Approvals
  APPROVE_TEAM_LEADER: 'approve_team_leader',
  APPROVE_HRGA: 'approve_hrga',
  APPROVE_FINANCE: 'approve_finance',
  APPROVE_PURCHASING: 'approve_purchasing',

  // User Management
  USERS_VIEW: 'users_view',
  USERS_MANAGE: 'users_manage',
} as const;

export function hasPermission(
  userPermissions: string[],
  requiredPermission: string
): boolean {
  return userPermissions.includes(requiredPermission);
}

export function hasAnyPermission(
  userPermissions: string[],
  requiredPermissions: string[]
): boolean {
  return requiredPermissions.some(p => userPermissions.includes(p));
}

export function hasAllPermissions(
  userPermissions: string[],
  requiredPermissions: string[]
): boolean {
  return requiredPermissions.every(p => userPermissions.includes(p));
}
```

#### Protected Routes
```typescript
// lib/supabase/middleware.ts
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';
import { supabaseServer } from './server';

export async function middleware(request: NextRequest) {
  const supabase = supabaseServer();
  const { data: { session } } = await supabase.auth.getSession();

  if (!session) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/dashboard/:path*'],
};
```

### 6.3 Custom User Hook
```typescript
// lib/hooks/useAuth.ts
import { useCallback, useEffect, useState } from 'react';
import { supabase } from '@/lib/supabase/client';
import type { User } from '@supabase/supabase-js';

export function useAuth() {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    // Get initial session
    supabase.auth.getSession().then(({ data: { session } }) => {
      setUser(session?.user ?? null);
      setLoading(false);
    });

    // Listen for auth changes
    const { data: { subscription } } = supabase.auth.onAuthStateChange(
      (_event, session) => {
        setUser(session?.user ?? null);
      }
    );

    return () => subscription?.unsubscribe();
  }, []);

  const logout = useCallback(async () => {
    try {
      await supabase.auth.signOut();
      setUser(null);
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Logout failed');
    }
  }, []);

  return { user, loading, error, logout };
}
```

---

## 7. COMPONENT ARCHITECTURE

### 7.1 Component Hierarchy

```
App
├── Layout (RootLayout)
│   ├── Sidebar
│   │   ├── Rail (icons)
│   │   └── Panel (navigation)
│   ├── ContentStage
│   │   ├── DashboardLayout
│   │   │   ├── Header
│   │   │   ├── Page Content
│   │   │   └── Drawers
```

### 7.2 Key Components

#### Layout Components
```typescript
// components/layout/Sidebar.tsx
interface SidebarProps {
  isExpanded: boolean;
  onToggle: () => void;
}

export function Sidebar({ isExpanded, onToggle }: SidebarProps) {
  // Sidebar implementation
}

// components/layout/DashboardLayout.tsx
interface DashboardLayoutProps {
  children: React.ReactNode;
}

export function DashboardLayout({ children }: DashboardLayoutProps) {
  // Dashboard layout with sidebar
}
```

#### Workspace Components
```typescript
// components/workspace/WorkspaceHeader.tsx
interface WorkspaceHeaderProps {
  eyebrow: string;
  title: string;
  description?: string;
  actions?: React.ReactNode;
}

export function WorkspaceHeader({
  eyebrow,
  title,
  description,
  actions,
}: WorkspaceHeaderProps) {
  // Header implementation
}
```

#### Drawer Components
```typescript
// components/drawers/BaseDrawer.tsx
interface BaseDrawerProps {
  isOpen: boolean;
  title: string;
  onClose: () => void;
  footer?: React.ReactNode;
  children: React.ReactNode;
}

export function BaseDrawer({
  isOpen,
  title,
  onClose,
  footer,
  children,
}: BaseDrawerProps) {
  // Drawer implementation
}
```

### 7.3 Form Components with React Hook Form

```typescript
// components/forms/AssetRequestForm.tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
  requestedAssetName: z.string().min(1, 'Asset name is required'),
  assetCategory: z.string().min(1, 'Category is required'),
  purpose: z.string().min(10, 'Purpose must be at least 10 characters'),
  estimatedDuration: z.string(),
  deadlineDate: z.date(),
});

type FormData = z.infer<typeof schema>;

interface AssetRequestFormProps {
  onSubmit: (data: FormData) => Promise<void>;
  isLoading?: boolean;
}

export function AssetRequestForm({ onSubmit, isLoading }: AssetRequestFormProps) {
  const form = useForm<FormData>({
    resolver: zodResolver(schema),
    defaultValues: {
      requestedAssetName: '',
      assetCategory: '',
      purpose: '',
      estimatedDuration: '',
    },
  });

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      {/* Form fields */}
    </form>
  );
}
```

### 7.4 Table Components with TanStack Query

```typescript
// components/tables/AssetTable.tsx
import { useQuery } from '@tanstack/react-query';
import { getAssets } from '@/lib/api/assets';

interface AssetTableProps {
  siteId?: string;
  status?: string;
  onRowClick: (asset: Asset) => void;
}

export function AssetTable({ siteId, status, onRowClick }: AssetTableProps) {
  const { data, isLoading, error } = useQuery({
    queryKey: ['assets', { siteId, status }],
    queryFn: () => getAssets({ siteId, status }),
  });

  if (isLoading) return <LoadingSkeleton />;
  if (error) return <ErrorState error={error} />;

  return (
    <div className="overflow-x-auto">
      <table className="w-full">
        {/* Table implementation */}
      </table>
    </div>
  );
}
```

---

## 8. PAGES & ROUTING

### 8.1 Route Map

#### Authentication Routes
| Route | Component | Auth Required |
|-------|-----------|---------------|
| `/` | Redirect to dashboard | No |
| `/login` | Login page | No |
| `/register` | Register page | No |
| `/reset-password` | Reset password | No |
| `/auth/callback` | OAuth callback | No |

#### Dashboard Routes
| Route | Component | Purpose |
|-------|-----------|---------|
| `/dashboard` | Dashboard/Home | Main dashboard |
| `/dashboard/calendar` | Calendar events list | Event management |
| `/dashboard/calendar/[id]` | Event detail | Event detail view |
| `/dashboard/assets` | Asset inventory | Asset listing |
| `/dashboard/assets/requests` | Asset request list | Request management |
| `/dashboard/assets/requests/[id]` | Request detail | Request approval |
| `/dashboard/assets/checkout` | Checkout list | Checkout management |
| `/dashboard/assets/checkin` | Checkin list | Checkin management |
| `/dashboard/assets/mutations` | Mutation list | Asset movement |
| `/dashboard/assets/maintenance` | Maintenance list | Maintenance tracking |
| `/dashboard/users` | User management | User listing & management |
| `/dashboard/users/[id]` | User detail | User profile/edit |
| `/dashboard/organization/sites` | Sites | Site management |
| `/dashboard/organization/departments` | Departments | Department management |
| `/dashboard/organization/settings` | Settings | Organization settings |
| `/dashboard/settings` | User settings | Account settings |

### 8.2 Page Implementation Example

```typescript
// app/(dashboard)/assets/page.tsx
import { Suspense } from 'react';
import { WorkspaceHeader } from '@/components/workspace/WorkspaceHeader';
import { AssetTable } from '@/components/tables/AssetTable';
import { getAssets } from '@/lib/api/assets';

export const metadata = {
  title: 'Asset Inventory | HRGA IMAJIN',
  description: 'Manage organization assets',
};

interface AssetPageProps {
  searchParams: Record<string, string | string[] | undefined>;
}

export default async function AssetPage({ searchParams }: AssetPageProps) {
  const page = Number(searchParams.page) || 1;
  const search = searchParams.q as string | undefined;
  const status = searchParams.status as string | undefined;

  return (
    <div className="space-y-6">
      <WorkspaceHeader
        eyebrow="Asset Management"
        title="Asset Inventory"
        description="Manage and track all organization assets"
        actions={<CreateAssetButton />}
      />

      <Suspense fallback={<LoadingSkeleton />}>
        <AssetTable
          page={page}
          search={search}
          status={status}
          onRowClick={handleAssetClick}
        />
      </Suspense>
    </div>
  );
}
```

---

## 9. STATE MANAGEMENT

### 9.1 Client State (Zustand)

```typescript
// lib/hooks/useDrawerStore.ts
import { create } from 'zustand';

interface DrawerState {
  activeDrawer: string | null;
  drawerData: any;
  openDrawer: (name: string, data?: any) => void;
  closeDrawer: () => void;
}

export const useDrawerStore = create<DrawerState>((set) => ({
  activeDrawer: null,
  drawerData: null,
  openDrawer: (name, data) => set({ activeDrawer: name, drawerData: data }),
  closeDrawer: () => set({ activeDrawer: null, drawerData: null }),
}));
```

### 9.2 Server State (TanStack Query)

```typescript
// lib/hooks/useAssets.ts
import { useQuery, useMutation } from '@tanstack/react-query';
import { getAssets, createAsset, updateAsset } from '@/lib/api/assets';

export function useAssets(filters: AssetFilters) {
  return useQuery({
    queryKey: ['assets', filters],
    queryFn: () => getAssets(filters),
  });
}

export function useCreateAsset(queryClient: QueryClient) {
  return useMutation({
    mutationFn: createAsset,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['assets'] });
    },
  });
}
```

### 9.3 Query Client Setup

```typescript
// app/providers.tsx
'use client';

import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactNode } from 'react';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5, // 5 minutes
      gcTime: 1000 * 60 * 10, // 10 minutes
    },
  },
});

export function Providers({ children }: { children: ReactNode }) {
  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  );
}
```

---

## 10. STYLING STRATEGY

### 10.1 Tailwind Configuration

```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss';

const config: Config = {
  content: [
    './src/app/**/*.{js,ts,jsx,tsx}',
    './src/components/**/*.{js,ts,jsx,tsx}',
  ],
  theme: {
    extend: {
      colors: {
        // Custom OKLCH-based colors
        accent: {
          50: '#f8f6fe',
          100: '#f0ebfc',
          500: '#6f74d8',
          600: '#5d66d0',
          700: '#4b5ec8',
          900: '#2d32a0',
        },
        surface: {
          50: '#fafafa',
          100: '#f5f5f5',
          200: '#e7e5e4',
        },
      },
      fontFamily: {
        body: ['Plus Jakarta Sans', 'system-ui'],
        heading: ['Outfit', 'system-ui'],
      },
      fontSize: {
        xs: ['0.75rem', { lineHeight: '1rem' }],
        sm: ['0.875rem', { lineHeight: '1.25rem' }],
        base: ['1rem', { lineHeight: '1.5rem' }],
      },
      borderRadius: {
        md: '0.5rem',
        lg: '0.75rem',
      },
    },
  },
  plugins: [],
};

export default config;
```

### 10.2 CSS Variables Integration

```css
/* src/app/globals.css */
@layer base {
  :root {
    /* Colors */
    --accent: oklch(0.62 0.19 265);
    --accent-hover: oklch(0.55 0.22 265);
    --bg: oklch(0.94 0.005 255);
    --surface: oklch(0.98 0.005 255);
    
    /* Typography */
    --font-body: 'Plus Jakarta Sans', system-ui;
    --font-heading: 'Outfit', system-ui;
    
    /* Spacing */
    --spacing-px: 1px;
    --spacing-xs: 0.25rem;
    --spacing-sm: 0.5rem;
    --spacing-md: 1rem;
    --spacing-lg: 1.5rem;
    
    /* Animations */
    --duration-fast: 120ms;
    --duration-normal: 240ms;
    --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
  }

  html {
    @apply scroll-smooth;
  }

  body {
    @apply bg-neutral-100 text-neutral-900 font-body;
  }
}

@layer components {
  .btn-primary {
    @apply inline-flex items-center justify-center gap-2 h-9 px-4 bg-accent text-white rounded-md font-medium text-sm hover:bg-accent-600 transition-colors;
  }

  .btn-ghost {
    @apply inline-flex items-center justify-center gap-2 h-9 px-4 bg-transparent border border-neutral-200 rounded-md font-medium text-sm hover:bg-neutral-50 transition-colors;
  }
}
```

### 10.3 Responsive Breakpoints

```typescript
// lib/utils/constants.ts
export const BREAKPOINTS = {
  mobile: 0,
  tablet: 640,
  desktop: 1024,
  wide: 1280,
} as const;

// Usage in Tailwind
// sm: 640px, md: 768px, lg: 1024px, xl: 1280px, 2xl: 1536px
```

---

## 11. DEVELOPMENT SETUP

### 11.1 Prerequisites
- Node.js 18.17 or later
- pnpm 8.0 or later
- Supabase account
- Vercel account

### 11.2 Initial Setup

```bash
# Clone repository
git clone https://github.com/your-org/hrga-imajin.git
cd hrga-imajin

# Install dependencies
pnpm install

# Setup environment variables
cp .env.example .env.local

# Configure Supabase
# 1. Create Supabase project
# 2. Get API keys from project settings
# 3. Add to .env.local

# Run database migrations
pnpm run db:migrate

# Seed initial data (optional)
pnpm run db:seed

# Start development server
pnpm dev
```

### 11.3 Environment Variables

```env
# .env.local

# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key

# App Config
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_API_BASE_URL=http://localhost:3000/api

# Optional: Third-party integrations
NEXT_PUBLIC_ENABLE_ANALYTICS=true
```

### 11.4 Scripts

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "type-check": "tsc --noEmit",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "db:migrate": "supabase migration up",
    "db:seed": "node scripts/seed.js",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:e2e": "playwright test",
    "generate:types": "supabase gen types typescript --project-id your-project-id > lib/supabase/types.ts"
  }
}
```

---

## 12. DEPLOYMENT

### 12.1 Vercel Deployment

#### Connect Repository
1. Push code to GitHub
2. Connect GitHub to Vercel
3. Select repository and branch
4. Configure environment variables in Vercel dashboard

#### Environment Variables in Vercel
```
NEXT_PUBLIC_SUPABASE_URL=<value>
NEXT_PUBLIC_SUPABASE_ANON_KEY=<value>
SUPABASE_SERVICE_ROLE_KEY=<value>
NEXT_PUBLIC_APP_URL=https://your-domain.com
```

#### Deployment Checklist
- [ ] Environment variables configured
- [ ] Database migrations run
- [ ] Tests passing
- [ ] Build successful
- [ ] Preview deployment verified

### 12.2 Production Considerations

```typescript
// next.config.js
const nextConfig = {
  // Security headers
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          {
            key: 'X-Content-Type-Options',
            value: 'nosniff',
          },
          {
            key: 'X-Frame-Options',
            value: 'DENY',
          },
          {
            key: 'X-XSS-Protection',
            value: '1; mode=block',
          },
        ],
      },
    ];
  },

  // Redirects & rewrites
  async redirects() {
    return [
      {
        source: '/',
        destination: '/dashboard',
        permanent: false,
      },
    ];
  },

  // Image optimization
  images: {
    formats: ['image/avif', 'image/webp'],
  },
};

export default nextConfig;
```

---

## 13. PERFORMANCE OPTIMIZATION

### 13.1 Code Splitting & Lazy Loading

```typescript
// Dynamic imports for heavy components
import dynamic from 'next/dynamic';

const AssetTable = dynamic(() => import('@/components/tables/AssetTable'), {
  loading: () => <LoadingSkeleton />,
  ssr: false,
});

// Usage in page
export default function AssetsPage() {
  return <AssetTable />;
}
```

### 13.2 Image Optimization

```typescript
// components/AssetImage.tsx
import Image from 'next/image';

interface AssetImageProps {
  src: string;
  alt: string;
  width?: number;
  height?: number;
}

export function AssetImage({ src, alt, width = 200, height = 200 }: AssetImageProps) {
  return (
    <Image
      src={src}
      alt={alt}
      width={width}
      height={height}
      priority={false}
      sizes="(max-width: 640px) 100vw, (max-width: 1024px) 50vw, 33vw"
    />
  );
}
```

### 13.3 Data Fetching Optimization

```typescript
// Use ISR (Incremental Static Regeneration)
export const revalidate = 3600; // Revalidate every hour

// Use Server Components for data fetching
async function AssetList() {
  const assets = await getAssets();
  
  return (
    <div>
      {assets.map(asset => (
        <div key={asset.id}>{asset.name}</div>
      ))}
    </div>
  );
}
```

### 13.4 Bundle Analysis

```bash
# Analyze bundle size
pnpm add --save-dev @next/bundle-analyzer

# Check bundle size
ANALYZE=true pnpm build
```

---

## 14. TESTING STRATEGY

### 14.1 Unit Tests (Jest)

```typescript
// __tests__/lib/utils/permissions.test.ts
import { hasPermission, hasAllPermissions } from '@/lib/utils/permissions';

describe('Permission utilities', () => {
  describe('hasPermission', () => {
    it('should return true if user has permission', () => {
      const permissions = ['assets_view', 'assets_create'];
      expect(hasPermission(permissions, 'assets_view')).toBe(true);
    });

    it('should return false if user lacks permission', () => {
      const permissions = ['assets_view'];
      expect(hasPermission(permissions, 'assets_delete')).toBe(false);
    });
  });

  describe('hasAllPermissions', () => {
    it('should return true if user has all permissions', () => {
      const permissions = ['assets_view', 'assets_create', 'assets_edit'];
      expect(hasAllPermissions(permissions, ['assets_view', 'assets_create'])).toBe(true);
    });

    it('should return false if user lacks any permission', () => {
      const permissions = ['assets_view'];
      expect(hasAllPermissions(permissions, ['assets_view', 'assets_delete'])).toBe(false);
    });
  });
});
```

### 14.2 Component Tests

```typescript
// __tests__/components/AssetTable.test.tsx
import { render, screen, waitFor } from '@testing-library/react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { AssetTable } from '@/components/tables/AssetTable';

describe('AssetTable', () => {
  const queryClient = new QueryClient({
    defaultOptions: {
      queries: { retry: false },
    },
  });

  it('should render asset table with data', async () => {
    render(
      <QueryClientProvider client={queryClient}>
        <AssetTable />
      </QueryClientProvider>
    );

    await waitFor(() => {
      expect(screen.getByText('Asset Name')).toBeInTheDocument();
    });
  });

  it('should handle loading state', () => {
    render(
      <QueryClientProvider client={queryClient}>
        <AssetTable />
      </QueryClientProvider>
    );

    expect(screen.getByRole('status')).toBeInTheDocument();
  });
});
```

### 14.3 E2E Tests (Playwright)

```typescript
// tests/e2e/auth.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Authentication', () => {
  test('should login successfully', async ({ page }) => {
    await page.goto('/login');

    await page.fill('input[name="email"]', 'user@example.com');
    await page.fill('input[name="password"]', 'password123');
    await page.click('button[type="submit"]');

    await expect(page).toHaveURL('/dashboard');
  });

  test('should show error on invalid credentials', async ({ page }) => {
    await page.goto('/login');

    await page.fill('input[name="email"]', 'user@example.com');
    await page.fill('input[name="password"]', 'wrongpassword');
    await page.click('button[type="submit"]');

    await expect(page.locator('text=Invalid credentials')).toBeVisible();
  });
});
```

---

## 15. ERROR HANDLING

### 15.1 Error Boundary

```typescript
// components/ErrorBoundary.tsx
'use client';

import { ReactNode, useEffect } from 'react';

interface ErrorBoundaryProps {
  children: ReactNode;
}

interface ErrorState {
  hasError: boolean;
  error?: Error;
}

export function ErrorBoundary({ children }: ErrorBoundaryProps) {
  const [errorState, setErrorState] = useErrorHandler();

  useEffect(() => {
    const handleError = (error: ErrorEvent) => {
      setErrorState({
        hasError: true,
        error: error.error,
      });
    };

    window.addEventListener('error', handleError);
    return () => window.removeEventListener('error', handleError);
  }, []);

  if (errorState.hasError) {
    return (
      <div className="flex flex-col items-center justify-center min-h-screen gap-4">
        <h1 className="text-2xl font-bold">Something went wrong</h1>
        <p className="text-gray-600">{errorState.error?.message}</p>
        <button
          onClick={() => window.location.reload()}
          className="btn-primary"
        >
          Reload Page
        </button>
      </div>
    );
  }

  return <>{children}</>;
}
```

### 15.2 API Error Handling

```typescript
// lib/api/fetch.ts
export class ApiError extends Error {
  constructor(
    public status: number,
    public code: string,
    message: string,
    public details?: any
  ) {
    super(message);
    this.name = 'ApiError';
  }
}

export async function apiFetch<T>(
  endpoint: string,
  options?: FetchOptions
): Promise<T> {
  try {
    const response = await fetch(endpoint, {
      ...options,
      headers: {
        'Content-Type': 'application/json',
        ...options?.headers,
      },
    });

    const data = await response.json();

    if (!response.ok) {
      throw new ApiError(
        response.status,
        data.error?.code || 'UNKNOWN_ERROR',
        data.error?.message || 'An error occurred',
        data.error?.details
      );
    }

    return data.data;
  } catch (error) {
    if (error instanceof ApiError) {
      throw error;
    }
    throw new ApiError(500, 'NETWORK_ERROR', 'Network request failed');
  }
}
```

### 15.3 Form Error Handling

```typescript
// components/forms/AssetForm.tsx
export function AssetForm() {
  const form = useForm<AssetFormData>({
    resolver: zodResolver(assetSchema),
  });

  const onError = (error: FieldValues) => {
    // Handle form errors
    console.error('Form errors:', error);
  };

  return (
    <form onSubmit={form.handleSubmit(onSubmit, onError)}>
      {/* Form fields with error display */}
      {form.formState.errors.name && (
        <span className="text-red-500 text-sm">
          {form.formState.errors.name.message}
        </span>
      )}
    </form>
  );
}
```

---

## SUMMARY

This PRD provides a comprehensive guide for building HRGA IMAJIN with:

✅ **Modern tech stack**: Next.js 14, React 18, TypeScript, Tailwind CSS  
✅ **Scalable backend**: Supabase with PostgreSQL  
✅ **Clear architecture**: Component-based, API routes, custom hooks  
✅ **Type safety**: Full TypeScript coverage  
✅ **Performance optimized**: Code splitting, SSR/SSG, image optimization  
✅ **Security focused**: Row-level security, authentication, authorization  
✅ **Testing ready**: Unit, component, and E2E test strategies  
✅ **Production ready**: Deployment on Vercel, error handling, monitoring  

---

**Next Steps:**
1. Set up Supabase project
2. Initialize Next.js project with this structure
3. Create database schema based on section 4
4. Build API routes according to section 5
5. Develop components following section 7
6. Deploy to Vercel
