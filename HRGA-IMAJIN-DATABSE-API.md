# HRGA IMAJIN - Database & API Documentation

---

## 1. COMPLETE DATABASE SCHEMA

### 1.1 Users Table - Detailed Definition

```sql
CREATE TABLE users (
  -- Identifiers
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  auth_id UUID UNIQUE NOT NULL, -- Link to Supabase Auth
  
  -- Personal Information
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255) NOT NULL,
  phone VARCHAR(20),
  avatar_url VARCHAR(500),
  
  -- Organization
  department_id UUID REFERENCES departments(id) ON DELETE SET NULL,
  position VARCHAR(100),
  
  -- Access Control
  role VARCHAR(50) NOT NULL DEFAULT 'employee',
  -- Roles: 'admin', 'manager', 'team_lead', 'employee'
  
  permissions TEXT[] DEFAULT '{}',
  -- Array of permission codes from PERMISSIONS enum
  
  -- Status
  status VARCHAR(20) DEFAULT 'active',
  -- Status: 'active', 'inactive', 'suspended'
  
  -- Dates
  join_date DATE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  deleted_at TIMESTAMP WITH TIME ZONE,
  
  -- Metadata
  metadata JSONB DEFAULT '{}'
  -- For storing additional user data
);

-- Indexes for performance
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_auth_id ON users(auth_id);
CREATE INDEX idx_users_department_id ON users(department_id);
CREATE INDEX idx_users_role ON users(role);
CREATE INDEX idx_users_status ON users(status);
CREATE INDEX idx_users_deleted_at ON users(deleted_at);
```

### 1.2 Assets Table - Detailed Definition

```sql
CREATE TABLE assets (
  -- Identifiers
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  asset_number VARCHAR(100) UNIQUE NOT NULL,
  -- Format: SITE-CATEGORY-NUMBER (e.g., HQ-ELC-001)
  
  -- Classification
  category VARCHAR(100) NOT NULL,
  -- Categories: 'Electronics', 'Furniture', 'Office Equipment', 'Vehicles', 'Other'
  
  -- Details
  description TEXT,
  
  -- Location & Assignment
  site_id UUID NOT NULL REFERENCES sites(id) ON DELETE RESTRICT,
  current_location VARCHAR(255),
  assigned_to UUID REFERENCES users(id) ON DELETE SET NULL,
  
  -- Status Tracking
  status VARCHAR(50) DEFAULT 'active',
  -- Status: 'active', 'maintenance', 'retired', 'lost', 'in_checkout'
  
  -- Financial & Warranty
  purchase_date DATE,
  purchase_price DECIMAL(15, 2),
  warranty_expiry DATE,
  depreciation_value DECIMAL(15, 2),
  
  -- Media & Documents
  image_url VARCHAR(500),
  qr_code_url VARCHAR(500),
  documents TEXT[] DEFAULT '{}',
  -- Array of document URLs (receipts, warranty, etc)
  
  -- Audit Trail
  created_by UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  updated_by UUID REFERENCES users(id) ON DELETE SET NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  deleted_at TIMESTAMP WITH TIME ZONE,
  
  -- Metadata
  metadata JSONB DEFAULT '{}',
  -- For storing custom attributes
  
  -- Search optimization
  search_text TSVECTOR GENERATED ALWAYS AS (
    to_tsvector('english', name || ' ' || asset_number || ' ' || category)
  ) STORED
);

-- Indexes
CREATE INDEX idx_assets_asset_number ON assets(asset_number);
CREATE INDEX idx_assets_category ON assets(category);
CREATE INDEX idx_assets_site_id ON assets(site_id);
CREATE INDEX idx_assets_assigned_to ON assets(assigned_to);
CREATE INDEX idx_assets_status ON assets(status);
CREATE INDEX idx_assets_status_site ON assets(status, site_id);
CREATE INDEX idx_assets_created_at ON assets(created_at DESC);
CREATE INDEX idx_assets_search ON assets USING GIN(search_text);
```

### 1.3 Asset Requests Table

```sql
CREATE TABLE asset_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  request_number VARCHAR(50) UNIQUE NOT NULL,
  -- Format: REQ-YYYY-MM-NNN (e.g., REQ-2026-03-001)
  
  -- Requester Information
  requester_id UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  
  -- Requested Item Details
  requested_asset_name VARCHAR(255) NOT NULL,
  asset_category VARCHAR(100),
  site_asset_location VARCHAR(255),
  asset_price DECIMAL(15, 2),
  purchase_link VARCHAR(500),
  
  -- Request Details
  purpose TEXT NOT NULL,
  estimated_duration VARCHAR(50),
  -- Format: "5 days", "2 weeks", "1 month"
  
  deadline_date DATE,
  
  -- Approval Workflow
  approval_stage VARCHAR(50) DEFAULT 'pending',
  -- Stages: 'pending', 'team_leader', 'hrga', 'finance', 'purchasing', 'approved', 'rejected'
  
  approval_status VARCHAR(50) DEFAULT 'pending',
  -- Status: 'pending', 'approved', 'rejected', 'revision_requested'
  
  revision_reason TEXT,
  max_revisions INT DEFAULT 0,
  
  -- Timestamps
  submitted_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  approved_at TIMESTAMP WITH TIME ZONE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  deleted_at TIMESTAMP WITH TIME ZONE
);

-- Indexes
CREATE INDEX idx_asset_requests_requester_id ON asset_requests(requester_id);
CREATE INDEX idx_asset_requests_approval_stage ON asset_requests(approval_stage);
CREATE INDEX idx_asset_requests_request_number ON asset_requests(request_number);
CREATE INDEX idx_asset_requests_submitted_at ON asset_requests(submitted_at DESC);
```

### 1.4 Approval History Table

```sql
CREATE TABLE approval_history (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  asset_request_id UUID NOT NULL REFERENCES asset_requests(id) ON DELETE CASCADE,
  
  -- Approval Stage & Action
  stage VARCHAR(50) NOT NULL,
  action VARCHAR(20) NOT NULL,
  -- Actions: 'approved', 'rejected', 'revision_requested'
  
  -- Approver Info
  approved_by UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  approval_date TIMESTAMP WITH TIME ZONE DEFAULT now(),
  
  -- Notes & Feedback
  notes TEXT,
  attachment_urls TEXT[] DEFAULT '{}',
  
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

-- Indexes
CREATE INDEX idx_approval_history_request_id ON approval_history(asset_request_id);
CREATE INDEX idx_approval_history_stage ON approval_history(stage);
CREATE INDEX idx_approval_history_approved_by ON approval_history(approved_by);
```

### 1.5 Checkout Requests Table

```sql
CREATE TABLE checkout_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  checkout_number VARCHAR(50) UNIQUE NOT NULL,
  -- Format: CHO-YYYY-MM-NNN
  
  -- Asset & Requester
  asset_id UUID NOT NULL REFERENCES assets(id) ON DELETE RESTRICT,
  requester_id UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  
  -- Location & Schedule
  pickup_location VARCHAR(255) NOT NULL,
  pickup_date DATE NOT NULL,
  planned_return_date DATE NOT NULL,
  
  -- Status Tracking
  status VARCHAR(50) DEFAULT 'waiting_confirmation',
  -- Status: 'waiting_confirmation', 'ready_for_handover', 'checked_out', 'returned', 'overdue'
  
  -- Checkout Details
  checkout_date TIMESTAMP WITH TIME ZONE,
  checkedout_by UUID REFERENCES users(id) ON DELETE SET NULL,
  
  actual_return_date DATE,
  returned_by UUID REFERENCES users(id) ON DELETE SET NULL,
  received_by UUID REFERENCES users(id) ON DELETE SET NULL,
  
  -- Additional Info
  condition_on_checkout VARCHAR(50),
  condition_on_return VARCHAR(50),
  -- Conditions: 'good', 'minor_damage', 'major_damage', 'lost'
  
  notes TEXT,
  attachment_urls TEXT[] DEFAULT '{}',
  
  -- Audit
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  deleted_at TIMESTAMP WITH TIME ZONE
);

-- Indexes
CREATE INDEX idx_checkout_requests_asset_id ON checkout_requests(asset_id);
CREATE INDEX idx_checkout_requests_requester_id ON checkout_requests(requester_id);
CREATE INDEX idx_checkout_requests_status ON checkout_requests(status);
CREATE INDEX idx_checkout_requests_planned_return ON checkout_requests(planned_return_date);
```

### 1.6 Checkin Requests Table

```sql
CREATE TABLE checkin_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  checkin_number VARCHAR(50) UNIQUE NOT NULL,
  checkout_request_id UUID REFERENCES checkout_requests(id) ON DELETE SET NULL,
  
  -- Asset & Return Details
  asset_id UUID NOT NULL REFERENCES assets(id) ON DELETE RESTRICT,
  returned_by UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  received_at_location VARCHAR(255),
  
  -- Condition Assessment
  condition VARCHAR(50) NOT NULL,
  -- Conditions: 'good', 'minor_repair', 'lens_cleaning', 'damaged'
  
  damage_description TEXT,
  repair_estimate DECIMAL(15, 2),
  
  -- Status
  status VARCHAR(50) DEFAULT 'inspection_needed',
  -- Status: 'inspection_needed', 'inspected', 'repaired', 'closed'
  
  -- Inspection
  inspection_date TIMESTAMP WITH TIME ZONE,
  inspected_by UUID REFERENCES users(id) ON DELETE SET NULL,
  inspection_notes TEXT,
  
  -- Photos/Evidence
  photos TEXT[] DEFAULT '{}',
  
  notes TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

-- Indexes
CREATE INDEX idx_checkin_requests_asset_id ON checkin_requests(asset_id);
CREATE INDEX idx_checkin_requests_returned_by ON checkin_requests(returned_by);
CREATE INDEX idx_checkin_requests_status ON checkin_requests(status);
```

### 1.7 Mutations Table

```sql
CREATE TABLE mutations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  mutation_number VARCHAR(50) UNIQUE NOT NULL,
  -- Format: MUT-YYYY-MM-NNN
  
  asset_id UUID NOT NULL REFERENCES assets(id) ON DELETE RESTRICT,
  
  -- Movement Details
  from_location VARCHAR(255) NOT NULL,
  to_location VARCHAR(255) NOT NULL,
  from_site_id UUID REFERENCES sites(id) ON DELETE RESTRICT,
  to_site_id UUID REFERENCES sites(id) ON DELETE RESTRICT,
  
  -- Initiation
  initiated_by UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  initiated_date TIMESTAMP WITH TIME ZONE DEFAULT now(),
  
  -- Scheduling
  scheduled_date TIMESTAMP WITH TIME ZONE,
  expected_arrival_date DATE,
  
  -- Execution
  status VARCHAR(50) DEFAULT 'queued',
  -- Status: 'queued', 'in_transit', 'completed', 'cancelled'
  
  started_date TIMESTAMP WITH TIME ZONE,
  completed_date TIMESTAMP WITH TIME ZONE,
  
  -- Handler Info
  handled_by UUID REFERENCES users(id) ON DELETE SET NULL,
  received_by UUID REFERENCES users(id) ON DELETE SET NULL,
  
  -- Details
  carrier VARCHAR(255),
  tracking_number VARCHAR(100),
  notes TEXT,
  
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

-- Indexes
CREATE INDEX idx_mutations_asset_id ON mutations(asset_id);
CREATE INDEX idx_mutations_status ON mutations(status);
CREATE INDEX idx_mutations_scheduled_date ON mutations(scheduled_date);
```

### 1.8 Maintenance Requests Table

```sql
CREATE TABLE maintenance_requests (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  maintenance_number VARCHAR(50) UNIQUE NOT NULL,
  -- Format: MNT-YYYY-MM-NNN
  
  asset_id UUID NOT NULL REFERENCES assets(id) ON DELETE RESTRICT,
  
  -- Issue Details
  issue_description TEXT NOT NULL,
  issue_category VARCHAR(100),
  -- Categories: 'preventive', 'corrective', 'predictive', 'cleaning'
  
  requester_id UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  request_location VARCHAR(255),
  
  -- Prioritization
  priority VARCHAR(20) NOT NULL DEFAULT 'medium',
  -- Priority: 'high', 'medium', 'low'
  
  -- Status Tracking
  status VARCHAR(50) DEFAULT 'pending_review',
  -- Status: 'pending_review', 'approved', 'scheduled', 'in_progress', 'completed', 'closed'
  
  -- Assignment
  assigned_to UUID REFERENCES users(id) ON DELETE SET NULL,
  vendor_id VARCHAR(100),
  vendor_name VARCHAR(255),
  
  -- Scheduling
  requested_date TIMESTAMP WITH TIME ZONE DEFAULT now(),
  scheduled_date TIMESTAMP WITH TIME ZONE,
  estimated_duration VARCHAR(50),
  
  -- Execution
  started_date TIMESTAMP WITH TIME ZONE,
  completed_date TIMESTAMP WITH TIME ZONE,
  
  -- Cost
  estimated_cost DECIMAL(15, 2),
  actual_cost DECIMAL(15, 2),
  
  -- Details
  work_done TEXT,
  photos TEXT[] DEFAULT '{}',
  warranty_affected BOOLEAN DEFAULT false,
  notes TEXT,
  
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

-- Indexes
CREATE INDEX idx_maintenance_requests_asset_id ON maintenance_requests(asset_id);
CREATE INDEX idx_maintenance_requests_status ON maintenance_requests(status);
CREATE INDEX idx_maintenance_requests_priority ON maintenance_requests(priority);
CREATE INDEX idx_maintenance_requests_assigned_to ON maintenance_requests(assigned_to);
```

### 1.9 Calendar Events Table

```sql
CREATE TABLE calendar_events (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  
  -- Event Details
  title VARCHAR(255) NOT NULL,
  description TEXT,
  event_type VARCHAR(50),
  -- Types: 'meeting', 'holiday', 'deadline', 'maintenance', 'event', 'training'
  
  -- Time
  start_date TIMESTAMP WITH TIME ZONE NOT NULL,
  end_date TIMESTAMP WITH TIME ZONE NOT NULL,
  is_all_day BOOLEAN DEFAULT false,
  timezone VARCHAR(50) DEFAULT 'UTC',
  
  -- Location & Participants
  location VARCHAR(255),
  created_by UUID NOT NULL REFERENCES users(id) ON DELETE RESTRICT,
  participants UUID[] DEFAULT '{}',
  optional_participants UUID[] DEFAULT '{}',
  
  -- Recurrence
  is_recurring BOOLEAN DEFAULT false,
  recurring_pattern VARCHAR(50),
  -- Patterns: 'daily', 'weekly', 'biweekly', 'monthly', 'yearly'
  
  recurring_end_date DATE,
  recurring_except_dates DATE[] DEFAULT '{}',
  
  -- Attachments & Notes
  attachments TEXT[] DEFAULT '{}',
  reminders TEXT[] DEFAULT '{}',
  -- Reminder formats: '5 minutes before', '1 day before'
  
  tags TEXT[] DEFAULT '{}',
  color VARCHAR(7) DEFAULT '#6f74d8',
  
  -- Status
  is_cancelled BOOLEAN DEFAULT false,
  cancellation_reason TEXT,
  
  -- Audit
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  deleted_at TIMESTAMP WITH TIME ZONE
);

-- Indexes
CREATE INDEX idx_calendar_events_created_by ON calendar_events(created_by);
CREATE INDEX idx_calendar_events_start_date ON calendar_events(start_date);
CREATE INDEX idx_calendar_events_participants ON calendar_events USING GIN(participants);
CREATE INDEX idx_calendar_events_event_type ON calendar_events(event_type);
```

### 1.10 Sites & Departments Tables

```sql
CREATE TABLE sites (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  code VARCHAR(50) UNIQUE NOT NULL,
  
  -- Address
  address VARCHAR(500),
  city VARCHAR(100),
  province VARCHAR(100),
  postal_code VARCHAR(20),
  country VARCHAR(100),
  
  -- Contact
  phone VARCHAR(20),
  email VARCHAR(255),
  
  -- Status
  status VARCHAR(20) DEFAULT 'active',
  
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  deleted_at TIMESTAMP WITH TIME ZONE
);

CREATE INDEX idx_sites_code ON sites(code);
CREATE INDEX idx_sites_status ON sites(status);

CREATE TABLE departments (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  code VARCHAR(50) UNIQUE NOT NULL,
  
  site_id UUID NOT NULL REFERENCES sites(id) ON DELETE RESTRICT,
  head_id UUID REFERENCES users(id) ON DELETE SET NULL,
  
  budget DECIMAL(15, 2),
  budget_year INT,
  
  status VARCHAR(20) DEFAULT 'active',
  
  created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
  deleted_at TIMESTAMP WITH TIME ZONE
);

CREATE INDEX idx_departments_site_id ON departments(site_id);
CREATE INDEX idx_departments_code ON departments(code);
CREATE INDEX idx_departments_head_id ON departments(head_id);
```

---

## 2. API ENDPOINTS DOCUMENTATION

### 2.1 Authentication Endpoints

#### POST /api/auth/login
**Login user**

Request:
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

Response (Success):
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "name": "User Name",
      "role": "employee"
    },
    "session": {
      "access_token": "token",
      "refresh_token": "token",
      "expires_in": 3600
    }
  }
}
```

#### POST /api/auth/logout
**Logout user**

Response:
```json
{
  "success": true,
  "message": "Logged out successfully"
}
```

#### POST /api/auth/register
**Register new user**

Request:
```json
{
  "email": "newuser@example.com",
  "password": "password123",
  "name": "New User",
  "phone": "081234567890"
}
```

#### GET /api/auth/session
**Get current user session**

Response:
```json
{
  "success": true,
  "data": {
    "user": { /* user object */ },
    "session": { /* session object */ }
  }
}
```

### 2.2 Assets Endpoints

#### GET /api/assets
**Get paginated assets list**

Query Parameters:
```
page=1&pageSize=10&status=active&siteId=uuid&search=query&sortBy=name&sortOrder=asc
```

Response:
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "name": "Asset Name",
      "assetNumber": "HQ-ELC-001",
      "category": "Electronics",
      "status": "active",
      "assignedTo": "user-name",
      "siteId": "uuid",
      "createdAt": "2026-03-25T14:30:00Z"
    }
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

#### POST /api/assets
**Create new asset**

Request:
```json
{
  "name": "MacBook Pro 16\"",
  "assetNumber": "HQ-ELC-001",
  "category": "Electronics",
  "description": "Development laptop",
  "siteId": "uuid",
  "currentLocation": "Office 3",
  "purchaseDate": "2026-01-15",
  "purchasePrice": 45000000,
  "warrantyExpiry": "2027-01-15"
}
```

#### GET /api/assets/[id]
**Get asset detail**

Response:
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "MacBook Pro 16\"",
    "assetNumber": "HQ-ELC-001",
    "category": "Electronics",
    "description": "Development laptop",
    "status": "active",
    "purchaseDate": "2026-01-15",
    "purchasePrice": 45000000,
    "warrantyExpiry": "2027-01-15",
    "assignedTo": {
      "id": "uuid",
      "name": "John Doe"
    },
    "site": {
      "id": "uuid",
      "name": "Headquarters"
    },
    "createdBy": {
      "id": "uuid",
      "name": "Admin"
    },
    "createdAt": "2026-01-15T10:00:00Z",
    "updatedAt": "2026-03-25T14:30:00Z"
  }
}
```

#### PATCH /api/assets/[id]
**Update asset**

Request:
```json
{
  "status": "maintenance",
  "assignedTo": "new-user-id",
  "currentLocation": "Service Center"
}
```

#### DELETE /api/assets/[id]
**Delete asset (soft delete)**

Response:
```json
{
  "success": true,
  "message": "Asset deleted successfully"
}
```

### 2.3 Asset Requests Endpoints

#### GET /api/asset-requests
**Get paginated asset requests**

Query Parameters:
```
page=1&pageSize=10&approvalStage=pending&status=pending&view=requester
```

Response:
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "requestNumber": "REQ-2026-03-001",
      "requester": {
        "id": "uuid",
        "name": "Employee Name"
      },
      "requestedAsset": "MacBook Pro",
      "category": "Electronics",
      "purpose": "Development work",
      "approvalStage": "team_leader",
      "status": "pending",
      "submittedAt": "2026-03-25T10:00:00Z"
    }
  ],
  "pagination": { /* ... */ }
}
```

#### POST /api/asset-requests
**Create asset request**

Request:
```json
{
  "requestedAssetName": "MacBook Pro 16\"",
  "assetCategory": "Electronics",
  "siteAssetLocation": "Headquarters",
  "assetPrice": 45000000,
  "purchaseLink": "https://example.com",
  "purpose": "Development and design work",
  "estimatedDuration": "2 weeks",
  "deadlineDate": "2026-04-30"
}
```

#### POST /api/asset-requests/[id]/approve
**Approve asset request at current stage**

Request:
```json
{
  "notes": "Approval notes"
}
```

#### POST /api/asset-requests/[id]/reject
**Reject asset request**

Request:
```json
{
  "reason": "Budget constraint",
  "notes": "Rejection notes"
}
```

#### POST /api/asset-requests/[id]/revise
**Request revision**

Request:
```json
{
  "revisionReason": "Please provide more details about the usage",
  "suggestedChanges": "Clarify the business justification"
}
```

### 2.4 Checkout Endpoints

#### POST /api/assets/[id]/checkout
**Create checkout request**

Request:
```json
{
  "requesterName": "Employee Name",
  "pickupLocation": "Office 3",
  "pickupDate": "2026-03-27",
  "plannedReturnDate": "2026-04-03",
  "notes": "Needed for client meeting"
}
```

Response:
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "checkoutNumber": "CHO-2026-03-001",
    "asset": { /* asset data */ },
    "status": "waiting_confirmation",
    "pickupDate": "2026-03-27",
    "plannedReturnDate": "2026-04-03"
  }
}
```

#### GET /api/assets/checkout
**Get all checkout requests**

#### PATCH /api/assets/checkout/[id]
**Update checkout status**

Request:
```json
{
  "status": "checked_out",
  "notes": "Handed over to user"
}
```

### 2.5 Checkin Endpoints

#### POST /api/assets/[id]/checkin
**Create checkin request**

Request:
```json
{
  "returnedByName": "Employee Name",
  "condition": "good",
  "damageDescription": null,
  "notes": "Asset returned in good condition"
}
```

#### GET /api/assets/checkin
**Get all checkin requests**

#### PATCH /api/assets/checkin/[id]
**Update checkin (inspection)**

Request:
```json
{
  "status": "inspected",
  "condition": "minor_repair",
  "damageDescription": "Screen has minor scratch",
  "repairEstimate": 500000
}
```

### 2.6 Users Endpoints

#### GET /api/users
**Get users list**

Query Parameters:
```
page=1&pageSize=10&departmentId=uuid&status=active&search=query&role=employee
```

Response:
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "email": "user@example.com",
      "name": "User Name",
      "position": "Developer",
      "department": {
        "id": "uuid",
        "name": "IT"
      },
      "role": "employee",
      "status": "active",
      "joinDate": "2025-01-15",
      "permissions": ["assets_view", "assets_create"]
    }
  ],
  "pagination": { /* ... */ }
}
```

#### POST /api/users
**Create user**

Request:
```json
{
  "email": "newuser@example.com",
  "name": "New User",
  "phone": "081234567890",
  "position": "Developer",
  "departmentId": "uuid",
  "role": "employee",
  "joinDate": "2026-03-25"
}
```

#### GET /api/users/[id]
**Get user detail**

#### PATCH /api/users/[id]
**Update user**

Request:
```json
{
  "name": "Updated Name",
  "position": "Senior Developer",
  "departmentId": "new-uuid",
  "status": "inactive"
}
```

#### PATCH /api/users/[id]/permissions
**Update user permissions**

Request:
```json
{
  "permissions": [
    "assets_view",
    "assets_create",
    "assets_edit",
    "approve_team_leader"
  ]
}
```

### 2.7 Organization Endpoints

#### GET /api/organization/sites
**Get sites list**

#### POST /api/organization/sites
**Create site**

Request:
```json
{
  "name": "New Branch",
  "code": "NB",
  "address": "Jl. New Street 1",
  "city": "Bandung",
  "country": "Indonesia"
}
```

#### GET /api/organization/departments
**Get departments list**

Query Parameters:
```
siteId=uuid&search=query
```

#### POST /api/organization/departments
**Create department**

Request:
```json
{
  "name": "Finance",
  "code": "FIN",
  "siteId": "uuid",
  "headId": "user-uuid"
}
```

### 2.8 Calendar Endpoints

#### GET /api/calendar
**Get events list**

Query Parameters:
```
startDate=2026-03-01&endDate=2026-03-31&participantId=uuid&eventType=meeting
```

Response:
```json
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "title": "Team Meeting",
      "eventType": "meeting",
      "startDate": "2026-03-25T14:00:00Z",
      "endDate": "2026-03-25T15:00:00Z",
      "location": "Conference Room 1",
      "createdBy": {
        "id": "uuid",
        "name": "Manager Name"
      },
      "participants": [
        { "id": "uuid", "name": "Employee 1" }
      ],
      "isAllDay": false,
      "tags": ["meeting", "team"]
    }
  ]
}
```

#### POST /api/calendar
**Create event**

Request:
```json
{
  "title": "Team Standup",
  "eventType": "meeting",
  "startDate": "2026-03-26T09:00:00Z",
  "endDate": "2026-03-26T09:30:00Z",
  "location": "Office",
  "description": "Daily standup",
  "participants": ["user-id-1", "user-id-2"],
  "isAllDay": false,
  "reminders": ["5 minutes before"],
  "tags": ["standup", "daily"]
}
```

#### PATCH /api/calendar/[id]
**Update event**

#### DELETE /api/calendar/[id]
**Delete event**

---

## 3. ERROR RESPONSE CODES

### Standard HTTP Status Codes

| Status | Meaning | Use Case |
|--------|---------|----------|
| 200 | OK | Successful request |
| 201 | Created | Resource created |
| 204 | No Content | Successful deletion |
| 400 | Bad Request | Invalid input data |
| 401 | Unauthorized | Missing authentication |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource already exists |
| 422 | Unprocessable Entity | Validation failed |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Server Error | Unexpected server error |

### Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      {
        "field": "email",
        "message": "Email is already in use"
      },
      {
        "field": "password",
        "message": "Password must be at least 8 characters"
      }
    ]
  },
  "meta": {
    "timestamp": "2026-03-25T14:30:00Z",
    "requestId": "req_abc123xyz"
  }
}
```

---

## 4. DATA VALIDATION SCHEMAS

### 4.1 Asset Request Schema

```typescript
import { z } from 'zod';

export const assetRequestSchema = z.object({
  requestedAssetName: z.string()
    .min(3, 'Asset name must be at least 3 characters')
    .max(255, 'Asset name must not exceed 255 characters'),
  
  assetCategory: z.enum([
    'Electronics',
    'Furniture',
    'Office Equipment',
    'Vehicles',
    'Other'
  ]),
  
  siteAssetLocation: z.string()
    .max(255, 'Location must not exceed 255 characters')
    .optional(),
  
  assetPrice: z.number()
    .positive('Asset price must be positive')
    .finite('Asset price must be a valid number'),
  
  purchaseLink: z.string()
    .url('Must be a valid URL')
    .optional(),
  
  purpose: z.string()
    .min(10, 'Purpose must be at least 10 characters')
    .max(1000, 'Purpose must not exceed 1000 characters'),
  
  estimatedDuration: z.enum([
    '1 day',
    '3 days',
    '1 week',
    '2 weeks',
    '1 month',
    '3 months',
    'ongoing'
  ]),
  
  deadlineDate: z.date()
    .refine(
      (date) => date > new Date(),
      'Deadline date must be in the future'
    )
});
```

### 4.2 User Schema

```typescript
export const userSchema = z.object({
  email: z.string()
    .email('Invalid email format'),
  
  name: z.string()
    .min(3, 'Name must be at least 3 characters')
    .max(255, 'Name must not exceed 255 characters'),
  
  phone: z.string()
    .regex(/^(\+62|0)[0-9]{9,12}$/, 'Invalid Indonesian phone number')
    .optional(),
  
  position: z.string()
    .max(100, 'Position must not exceed 100 characters')
    .optional(),
  
  departmentId: z.string().uuid('Invalid department ID'),
  
  role: z.enum(['admin', 'manager', 'team_lead', 'employee']),
  
  joinDate: z.date(),
  
  permissions: z.array(z.string()).default([])
});
```

---

## 5. RATE LIMITING

### Rate Limits by Endpoint Type

| Endpoint Type | Requests | Window |
|---------------|----------|--------|
| Authentication | 5 | 15 minutes |
| Read (GET) | 100 | 1 minute |
| Write (POST/PATCH) | 30 | 1 minute |
| Delete (DELETE) | 10 | 1 minute |

### Rate Limit Headers

```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-RateLimit-Reset: 1648230600
```

---

## 6. SAMPLE IMPLEMENTATION CODE

### 6.1 API Route with Validation

```typescript
// app/api/asset-requests/route.ts

import { NextRequest, NextResponse } from 'next/server';
import { supabaseServer } from '@/lib/supabase/server';
import { assetRequestSchema } from '@/lib/utils/validation';
import { generateRequestNumber } from '@/lib/utils/helpers';

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();
    
    // Validate input
    const validated = assetRequestSchema.parse(body);
    
    // Get current user
    const supabase = supabaseServer();
    const { data: { user } } = await supabase.auth.getUser();
    
    if (!user) {
      return NextResponse.json(
        { success: false, error: { message: 'Unauthorized' } },
        { status: 401 }
      );
    }
    
    // Generate request number
    const requestNumber = await generateRequestNumber('REQ');
    
    // Create request
    const { data, error } = await supabase
      .from('asset_requests')
      .insert([
        {
          request_number: requestNumber,
          requester_id: user.id,
          requested_asset_name: validated.requestedAssetName,
          asset_category: validated.assetCategory,
          site_asset_location: validated.siteAssetLocation,
          asset_price: validated.assetPrice,
          purchase_link: validated.purchaseLink,
          purpose: validated.purpose,
          estimated_duration: validated.estimatedDuration,
          deadline_date: validated.deadlineDate,
          approval_stage: 'team_leader',
          approval_status: 'pending',
        }
      ])
      .select()
      .single();
    
    if (error) throw error;
    
    return NextResponse.json({
      success: true,
      data,
    }, { status: 201 });
    
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json({
        success: false,
        error: {
          code: 'VALIDATION_ERROR',
          message: 'Validation failed',
          details: error.errors.map(e => ({
            field: e.path.join('.'),
            message: e.message
          }))
        }
      }, { status: 422 });
    }
    
    return NextResponse.json({
      success: false,
      error: { message: error.message || 'Internal server error' }
    }, { status: 500 });
  }
}
```

### 6.2 Custom Hook Implementation

```typescript
// lib/hooks/useAssetRequests.ts

import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { apiFetch } from '@/lib/api/fetch';

interface AssetRequestFilters {
  page?: number;
  pageSize?: number;
  status?: string;
  approvalStage?: string;
  view?: 'requester' | 'approver';
}

export function useAssetRequests(filters: AssetRequestFilters) {
  return useQuery({
    queryKey: ['asset-requests', filters],
    queryFn: async () => {
      const params = new URLSearchParams(
        Object.entries(filters)
          .filter(([, v]) => v != null)
          .map(([k, v]) => [k, String(v)])
      );
      return apiFetch(`/api/asset-requests?${params}`);
    },
    staleTime: 1000 * 60 * 5,
  });
}

export function useApproveAssetRequest(queryClient: QueryClient) {
  return useMutation({
    mutationFn: async ({ id, notes }: { id: string; notes?: string }) => {
      return apiFetch(`/api/asset-requests/${id}/approve`, {
        method: 'POST',
        body: { notes }
      });
    },
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['asset-requests'] });
    }
  });
}
```

---

**Documentation Version**: 1.0  
**Last Updated**: March 2026
