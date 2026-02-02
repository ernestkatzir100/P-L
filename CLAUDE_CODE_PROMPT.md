# Professional Services Business Management System - Claude Code Instructions

## Project Overview
Build a comprehensive, production-ready business management system for a professional services SMB (Entropy Group / Trustegy) in Hebrew. The system must handle the complete business lifecycle: project creation → billing milestones → P&L → liquidity management → bank reconciliation.

**Repository**: `P-L` (GitHub Codespaces ready)  
**Primary Language**: Hebrew (RTL UI)  
**Target Users**: 5-15 users across consulting/advisory firm

---

## Core Requirements

### 1. ENTITIES & DATA MODEL

#### **Companies (Legal Entities)**
```typescript
- Company ID
- Company Name (Hebrew)
- Tax ID
- Registration Number
- Entity Type (S-Corp, LLC, etc.)
- Active Status
- Consolidation Flag (include in consolidated reports?)
```

#### **Projects**
```typescript
- Project ID
- Project Name (Hebrew)
- Client Name (Hebrew)
- Company ID (which legal entity)
- Project Type: [Fixed-Price | Time & Materials | Retainer]
- Total Budget (excl VAT)
- Currency: [NIS (default) | USD]
- Start Date
- End Date
- Status: [Active | Completed | On-Hold | Cancelled]
- Notes
```

#### **Revenue Recognition (Monthly)**
```typescript
- Project ID
- Month (YYYY-MM)
- Recognized Revenue (excl VAT)
- Invoiced Amount
- Payment Status: [Not Invoiced | Invoiced | Paid]
- Invoice Number
- Invoice Date
- Payment Due Date
- Payment Received Date
- Notes
```

#### **Expenses**
```typescript
- Expense ID
- Company ID
- Category: [Salaries | Rent | Software | Contractors | Marketing | Other]
- Subcategory (user-defined)
- Amount (excl VAT)
- Currency
- Date
- Description
- Payment Status
- Bank Account ID
```

#### **Bank Accounts**
```typescript
- Account ID
- Company ID
- Bank Name (Hebrew)
- Account Number
- Currency (NIS default)
- Current Balance
- Last Updated
- Active Status
```

#### **Users & Roles**
```typescript
- User ID
- Name
- Email
- Role: [Admin | PM | Finance | View-Only]
- Active Status
- Last Login
```

---

### 2. CURRENT EXCEL LOGIC TO REPLICATE

Based on `Trustegy_PnL23.xlsx`:

#### **PnL Sheet Logic**
- Row per project with monthly columns (Jan 2023 → Dec 2026)
- VLOOKUP to BILLING sheet for client/project/budget
- Sum formulas for yearly totals
- Budget remaining calculation: `Budget - Sum(Recognized Revenue)`
- Cross-reference between PnL and BILLING sheets

#### **BILLING Sheet Logic**
- Each row = billing milestone
- Columns: Client, Project, Budget, Stage, Billing Date, Amount, Invoiced?, Payment Date, Paid?, Amount incl VAT
- Track invoice numbers and transaction numbers
- Payment tracking separate from invoicing

#### **Dashboard Logic**
- Pull from PnL sheet (rows 21, 50, 82, 54, 83)
- Calculate totals, margins, trends
- Display revenue, expenses, profit, profit %

**Key Formula Patterns to Replicate:**
```excel
Revenue Recognition = SUM(Monthly_Columns)
Remaining Budget = Total_Budget - Revenue_Recognition
Monthly Average = SUM(Year_Range)/12
Profit = Revenue - Expenses
Profit % = Profit / Revenue
```

---

### 3. UI/UX REQUIREMENTS

#### **Layout & Navigation (Hebrew RTL)**
- **Top Bar**: Logo | Company Selector | User Menu | Notifications
- **Sidebar** (right side for RTL):
  - 📊 דשבורד (Dashboard)
  - 📁 פרויקטים (Projects)
  - 💰 חשבוניות (Invoices)
  - 💳 הוצאות (Expenses)
  - 🏦 בנקים (Banks)
  - 📈 דוחות (Reports)
  - ⚙️ הגדרות (Settings)

#### **Key Screens**

**Dashboard**
- KPI Cards (RTL layout):
  - Total Revenue (MTD, YTD)
  - Outstanding Invoices
  - Cash Position (all accounts)
  - Profit Margin %
- Charts:
  - Monthly Revenue Trend (12 months)
  - Revenue by Project (pie chart)
  - Cash Flow Forecast (6 months)
  - Expense Breakdown (bar chart)

**Projects Table**
- Columns: Client | Project | Type | Budget | Recognized | Remaining | Status | Actions
- Filters: Company, Status, Project Type, Date Range
- Sort: All columns
- Search: Client/Project name
- Bulk Actions: Export, Change Status
- Drag-to-reorder rows (save order preference)

**Project Detail Page**
- **Header**: Project Name | Client | Budget | Status Badge
- **Tabs**:
  1. **Overview**: Basic info form
  2. **Revenue Recognition**: Monthly table with inline editing
     - Drag-and-drop to allocate budget across months
     - Auto-calculate remaining budget
  3. **Invoices**: Linked invoices table
  4. **Documents**: File uploads (contracts, SOWs)
  5. **Activity Log**: Audit trail

**Invoice Management**
- **Invoice Form**:
  - Project Dropdown (auto-fill client, remaining budget)
  - Milestone Description
  - Amount (excl VAT) → Auto-calc VAT (17%)
  - Invoice Date
  - Due Date (auto: +30 days)
  - Payment Status toggle
- **Invoice Table**:
  - Columns: Invoice # | Date | Client | Project | Amount | VAT | Total | Status | Days Overdue | Actions
  - Status badges with colors
  - Quick-mark-as-paid button
  - Export to PDF

**Bank Accounts**
- Multi-currency balance display
- Transaction history table
- Manual balance adjustment (with reason)
- Link to expenses

**Reports**
1. **P&L Report**
   - Company selector (single or consolidated)
   - Date range picker
   - Export: Excel, PDF
   - Drill-down by project
2. **Cash Flow Report**
   - Actual vs Forecast
   - By bank account
3. **Project Profitability**
   - Table: Project | Revenue | Costs | Margin | ROI
   - Sort by margin

---

### 4. TECHNICAL SPECIFICATIONS

#### **Tech Stack (100% Free & Open-Source)**
```yaml
Frontend:
  - React 18 + TypeScript (MIT License - FREE)
  - Vite (MIT License - FREE)
  - React Router v6 (MIT License - FREE)
  - TanStack Query (MIT License - FREE)
  - Zustand (MIT License - FREE)
  
UI Library:
  - shadcn/ui components (MIT License - FREE)
  - Tailwind CSS (MIT License - FREE)
  - Recharts (MIT License - FREE)
  - dnd-kit (MIT License - FREE)
  - date-fns (MIT License - FREE)
  
Backend:
  - Node.js 20 + Express (MIT License - FREE)
  - TypeScript (Apache 2.0 - FREE)
  
Database:
  - **PostgreSQL** (via Supabase free tier - recommended)
    - 500MB free (enough for years of data)
    - Automatic backups included
    - Scales to terabytes when needed
    - Internet-accessible from Day 1
  - Prisma ORM (Apache 2.0 - FREE, works perfectly with PostgreSQL)
  
  Alternative: Railway PostgreSQL (also free tier)
  
Auth:
  - JWT tokens (MIT License - FREE)
  - bcrypt password hashing (MIT License - FREE)
  - Custom role-based middleware (you own it)
  - Optional: Supabase Auth (pre-built, free)
  
File Storage:
  - Supabase Storage (1GB free)
  - Alternative: Cloudflare R2 (10GB free)
  - All files accessible via HTTPS URLs

Hosting (Choose ONE - all FREE with internet access):
  Option 1: **Supabase + Vercel + Railway** (Recommended for scalability)
    - Supabase: Database + Storage (₪0 for 500MB)
    - Vercel: Frontend auto-deploy from GitHub (₪0 unlimited)
    - Railway: Backend 500 CPU hours/month (₪0)
    - Total setup time: 20 minutes
    - URL: https://yourapp.vercel.app
    - Scales to 10,000+ users with same architecture
    
  Option 2: **Railway All-in-One**
    - PostgreSQL + Backend + Frontend all on Railway
    - 500 CPU hours/month free
    - Auto-deploy from GitHub
    - URL: https://yourapp.up.railway.app
    
  Option 3: **Self-hosted + Cloudflare Tunnel**
    - Run on your server/computer
    - Cloudflare Tunnel for secure internet access (free, no port forwarding)
    - URL: https://yourapp.yourdomain.com
    - Full control, ₪0 cost
```

**Total Cost: ₪0 (except Claude Code subscription you already have)**

#### **Database Schema (Prisma) - Multi-Tenant & Scalable**

**Architecture for Growth:**
- ✅ **Day 1**: 1 tenant, 1 company, 1 user
- ✅ **Year 2**: 10 tenants, 50 companies, 100 users
- ✅ **Code stays the same** - built for scale from start

```prisma
// schema.prisma
datasource db {
  provider = "postgresql"  // Scalable, internet-ready
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

// Multi-tenant support (even if starting with 1)
model Tenant {
  id              String    @id @default(cuid())
  name            String    // "Entropy Group", "Trustegy"
  slug            String    @unique  // "entropy", "trustegy"
  domain          String?   // Optional custom domain
  active          Boolean   @default(true)
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt
  
  users           User[]
  companies       Company[]
  
  @@index([slug])
}

model User {
  id              String    @id @default(cuid())
  tenantId        String    // Multi-tenant ready
  email           String
  password        String    // bcrypt hashed
  name            String
  role            UserRole  @default(VIEW_ONLY)
  active          Boolean   @default(true)
  lastLogin       DateTime?
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt
  
  tenant          Tenant    @relation(fields: [tenantId], references: [id])
  
  @@unique([tenantId, email])  // Same email can exist in different tenants
  @@index([tenantId])
  @@index([email])
}

// Core schema structure

model Company {
  id              String    @id @default(cuid())
  tenantId        String    // Multi-tenant: each company belongs to a tenant
  name            String
  taxId           String?
  consolidate     Boolean   @default(false)
  active          Boolean   @default(true)
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt
  
  tenant          Tenant    @relation(fields: [tenantId], references: [id])
  projects        Project[]
  expenses        Expense[]
  bankAccounts    BankAccount[]
  
  @@index([tenantId])
}

model Project {
  id              String    @id @default(cuid())
  companyId       String
  clientName      String
  projectName     String
  projectType     ProjectType
  budget          Decimal   @db.Decimal(12, 2)
  currency        Currency  @default(NIS)
  startDate       DateTime
  endDate         DateTime?
  status          ProjectStatus @default(ACTIVE)
  notes           String?
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt
  
  company         Company   @relation(fields: [companyId], references: [id])
  revenueLines    RevenueLine[]
  invoices        Invoice[]
  
  @@index([companyId])
  @@index([status])
}

model RevenueLine {
  id              String    @id @default(cuid())
  projectId       String
  month           DateTime  // 1st day of month
  recognizedAmt   Decimal   @db.Decimal(12, 2)
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt
  
  project         Project   @relation(fields: [projectId], references: [id], onDelete: Cascade)
  
  @@unique([projectId, month])
  @@index([projectId])
}

model Invoice {
  id              String    @id @default(cuid())
  projectId       String
  invoiceNumber   String?
  amount          Decimal   @db.Decimal(12, 2)
  vatAmount       Decimal   @db.Decimal(12, 2)
  totalAmount     Decimal   @db.Decimal(12, 2)
  invoiceDate     DateTime
  dueDate         DateTime
  paymentDate     DateTime?
  paymentStatus   PaymentStatus @default(NOT_INVOICED)
  notes           String?
  createdAt       DateTime  @default(now())
  updatedAt       DateTime  @updatedAt
  
  project         Project   @relation(fields: [projectId], references: [id])
  
  @@index([projectId])
  @@index([paymentStatus])
}

// Add: Expense, BankAccount, User, AuditLog models
// Include enums: ProjectType, Currency, ProjectStatus, PaymentStatus, UserRole, ExpenseCategory

```

#### **API Endpoints (RESTful)**
```
Auth:
POST   /api/auth/login
POST   /api/auth/logout
GET    /api/auth/me

Companies:
GET    /api/companies
POST   /api/companies
GET    /api/companies/:id
PATCH  /api/companies/:id
DELETE /api/companies/:id

Projects:
GET    /api/projects                    # ?companyId=&status=&search=
POST   /api/projects
GET    /api/projects/:id
PATCH  /api/projects/:id
DELETE /api/projects/:id
GET    /api/projects/:id/revenue        # Monthly revenue lines
POST   /api/projects/:id/revenue
PATCH  /api/projects/:id/revenue/:month
DELETE /api/projects/:id/revenue/:month

Invoices:
GET    /api/invoices                    # ?projectId=&status=&overdue=
POST   /api/invoices
GET    /api/invoices/:id
PATCH  /api/invoices/:id
DELETE /api/invoices/:id
PATCH  /api/invoices/:id/mark-paid      # Quick payment

Expenses:
GET    /api/expenses
POST   /api/expenses
PATCH  /api/expenses/:id
DELETE /api/expenses/:id

BankAccounts:
GET    /api/bank-accounts
POST   /api/bank-accounts
PATCH  /api/bank-accounts/:id/balance   # Manual adjustment

Reports:
GET    /api/reports/pnl                 # ?companyId=&startDate=&endDate=&consolidated=
GET    /api/reports/cashflow
GET    /api/reports/project-profitability

Dashboard:
GET    /api/dashboard/kpis              # Returns all KPI data
GET    /api/dashboard/charts            # Chart data
```

---

### 5. DRAG-AND-DROP FEATURES

#### **Revenue Allocation**
- **Interface**: Monthly timeline (12 months visible)
- **Drag Budget**: Drag remaining budget amount to specific month
- **Visual**: Green bars showing allocated amounts
- **Auto-calc**: Recalculate remaining budget in real-time
- **Constraints**: Can't allocate more than remaining budget

#### **Project Priority Ordering**
- **Drag Projects**: Reorder projects in table
- **Save Order**: Persist user's preferred view order
- **Per User**: Each user can have their own sort preference

---

### 6. HEBREW (RTL) CONFIGURATION

#### **Tailwind Config**
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      direction: {
        rtl: 'rtl',
      },
    },
  },
  plugins: [require('tailwindcss-rtl')],
}
```

#### **HTML Setup**
```html
<html dir="rtl" lang="he">
```

#### **Date Formatting**
```typescript
import { format } from 'date-fns';
import { he } from 'date-fns/locale';

format(new Date(), 'dd MMMM yyyy', { locale: he })
// Output: "27 ינואר 2026"
```

#### **Number Formatting (Hebrew)**
```typescript
const formatCurrency = (amount: number, currency: 'NIS' | 'USD') => {
  return new Intl.NumberFormat('he-IL', {
    style: 'currency',
    currency: currency === 'NIS' ? 'ILS' : 'USD',
  }).format(amount);
};
// Output: ‏₪123,456.78
```

---

### 7. ROLE-BASED ACCESS CONTROL

```typescript
Permissions Matrix:

Action                  | Admin | PM  | Finance | View-Only
------------------------|-------|-----|---------|----------
Create Project          |   ✓   |  ✓  |    -    |    -
Edit Project            |   ✓   |  ✓  |    -    |    -
Delete Project          |   ✓   |  -  |    -    |    -
View All Projects       |   ✓   |  ✓  |    ✓    |    ✓
Create Invoice          |   ✓   |  -  |    ✓    |    -
Edit Invoice            |   ✓   |  -  |    ✓    |    -
Mark Invoice Paid       |   ✓   |  -  |    ✓    |    -
View Financials         |   ✓   |  -  |    ✓    |    ✓
Manage Expenses         |   ✓   |  -  |    ✓    |    -
Manage Bank Accounts    |   ✓   |  -  |    ✓    |    -
User Management         |   ✓   |  -  |    -    |    -
Company Settings        |   ✓   |  -  |    -    |    -
Export Reports          |   ✓   |  ✓  |    ✓    |    -
```

Implement using middleware:
```typescript
// middleware/rbac.ts
export const requireRole = (roles: UserRole[]) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
};

// Usage:
router.delete('/projects/:id', requireRole(['ADMIN']), deleteProject);
```

---

### 8. DATA IMPORT FROM EXISTING EXCEL

**Create Import Tool (Admin Only)**

```typescript
POST /api/import/excel
- Accept: multipart/form-data
- Parse Excel file (use: xlsx library)
- Map sheets to entities:
  - BILLING → Projects + Invoices
  - PnL → RevenueLine records
  - מימון → BankAccounts + Loans (Expenses)
- Validate data
- Bulk insert with transaction
- Return: { imported: {projects: 10, invoices: 45, ...}, errors: [] }
```

**UI**: Admin → Settings → Import Data → Upload Excel → Preview Mapping → Confirm Import

---

### 9. REPORTS & EXPORTS

#### **P&L Report Format**
```
דוח רווח והפסד - [Company Name]
תקופה: [Start Date] - [End Date]

הכנסות:
  הכרה בהכנסה          ₪[amount]
  חשבוניות שהונפקו      ₪[amount]
  תשלומים שהתקבלו       ₪[amount]
                        ---------
  סה"כ הכנסות          ₪[total]

הוצאות:
  משכורות               ₪[amount]
  שכר דירה              ₪[amount]
  תוכנות                ₪[amount]
  קבלנים                ₪[amount]
  שיווק                 ₪[amount]
  אחר                   ₪[amount]
                        ---------
  סה"כ הוצאות          ₪[total]

רווח נקי                ₪[profit]
שולי רווח               [margin]%

```

#### **Export Options**
- **Excel**: Use `exceljs` library, replicate original Excel structure
- **PDF**: Use `pdfkit` or `puppeteer` for Hebrew support
- **CSV**: For external analysis

---

### 10. DEPLOYMENT & PRODUCTION READINESS (FREE OPTIONS)

#### **Environment Variables**
```env
# PostgreSQL (Supabase recommended - free tier)
DATABASE_URL=postgresql://postgres:[PASSWORD]@db.[PROJECT].supabase.co:5432/postgres

# Or Railway PostgreSQL
# DATABASE_URL=postgresql://user:pass@containers-us-west-xxx.railway.app:5432/railway

# Or self-hosted PostgreSQL
# DATABASE_URL=postgresql://trustegy:[PASSWORD]@localhost:5432/trustegy_db

JWT_SECRET=your-secret-key-min-32-chars-generate-random
PORT=5000
NODE_ENV=production
CORS_ORIGIN=https://trustegy.vercel.app
FRONTEND_URL=https://trustegy.vercel.app

# Optional: Monitoring
SENTRY_DSN=https://...
LOGTAIL_TOKEN=...
```

#### **Docker Setup (PostgreSQL for Scalability)**
```yaml
# docker-compose.yml - Production Ready
version: '3.8'
services:
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: trustegy_db
      POSTGRES_USER: trustegy
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
  
  backend:
    build: ./backend
    ports:
      - "5000:5000"
    environment:
      DATABASE_URL: postgresql://trustegy:${DB_PASSWORD}@db:5432/trustegy_db
      JWT_SECRET: ${JWT_SECRET}
      NODE_ENV: production
    depends_on:
      - db
  
  frontend:
    build: ./frontend
    ports:
      - "3000:80"
    depends_on:
      - backend

volumes:
  postgres_data:

# Access: http://localhost:3000
# Or use Cloudflare Tunnel for internet access (free)
```

**For internet access with self-hosting:**
```bash
# Install Cloudflare Tunnel (free, no port forwarding needed)
cloudflared tunnel create trustegy
cloudflared tunnel route dns trustegy app.trustegy.com

# Start tunnel
cloudflared tunnel run trustegy

# Now accessible at: https://app.trustegy.com
# Secure, no open ports, free forever!
```

#### **Free Hosting Options**

**Option 1: Self-Hosted (100% FREE)**
```bash
# Requirements: Any Linux server (even old laptop)
# 1. Install Docker + Docker Compose
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 2. Clone your repo
git clone https://github.com/ernestkatzir100/P-L
cd P-L

# 3. Set environment variables
cp .env.example .env
nano .env  # Edit DATABASE_URL, JWT_SECRET

# 4. Run
docker-compose up -d

# 5. Access at: http://your-server-ip:3000
```

**Option 2: GitHub Codespaces (Included in GitHub Pro)**
```bash
# Already running in Codespaces!
# Can keep it running 24/7 if needed
# Forward ports 3000 (frontend) and 5000 (backend)
# Access via GitHub-provided URL
```

**Option 3: Home Server + Dynamic DNS (FREE)**
```bash
# Use old computer as server
# Install Ubuntu Server (free)
# Use DuckDNS.org for free domain
# Port forward 80/443 on router
# Run Docker Compose as above
# Cost: ₪0 (just electricity)
```

**Option 4: Oracle Cloud Free Tier (Forever Free)**
```
# Oracle gives:
# - 2 AMD VMs (1 core, 1GB RAM each)
# - 200GB block storage
# - 10GB object storage
# Forever free, no credit card expiration

Steps:
1. Sign up: https://cloud.oracle.com/free
2. Create Ubuntu VM
3. Install Docker
4. Deploy your app
5. Use free domain from DuckDNS or Freenom
```

#### **Production Checklist**
- [ ] HTTPS enabled (use Let's Encrypt - FREE)
- [ ] Rate limiting (express-rate-limit - FREE)
- [ ] Input validation (zod - FREE)
- [ ] SQL injection protection (Prisma - FREE)
- [ ] XSS protection (helmet.js - FREE)
- [ ] CORS configured properly (FREE)
- [ ] Error logging (winston - FREE)
- [ ] Database backups (pg_dump script - FREE)
- [ ] Monitoring (Grafana + Prometheus - FREE)

#### **Free SSL Certificate**
```bash
# Install Certbot (Let's Encrypt)
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com

# Auto-renewal (free forever)
sudo certbot renew --dry-run
```

#### **Free Monitoring & Logging**
```bash
# Use Grafana (free) + Prometheus (free)
docker-compose -f monitoring-stack.yml up -d

# Or use built-in Node.js logging
# winston + rotating file logs (free)
```

**Total Monthly Cost: ₪0**  
*Only costs: electricity for self-hosted, or use free cloud tier*

---

### 11. TESTING REQUIREMENTS

#### **Unit Tests (Vitest)**
```typescript
// Example: tests/projects.test.ts
describe('Project Creation', () => {
  test('should create project with valid data', async () => {
    const project = await createProject({
      companyId: 'test-company',
      clientName: 'לקוח מס 1',
      projectName: 'פרויקט בדיקה',
      projectType: 'FIXED_PRICE',
      budget: 100000,
      currency: 'NIS',
      startDate: new Date(),
    });
    
    expect(project.id).toBeDefined();
    expect(project.status).toBe('ACTIVE');
  });
  
  test('should reject invalid budget', async () => {
    await expect(
      createProject({ ...validData, budget: -1000 })
    ).rejects.toThrow();
  });
});
```

#### **E2E Tests (Playwright)**
```typescript
test('complete billing flow', async ({ page }) => {
  await page.goto('/projects');
  await page.click('[data-test="create-project"]');
  await page.fill('[name="projectName"]', 'פרויקט חדש');
  await page.fill('[name="budget"]', '50000');
  await page.click('[data-test="save"]');
  
  // Navigate to invoices
  await page.click('[data-test="create-invoice"]');
  // ... continue flow
});
```

---

### 12. PHASED DEVELOPMENT (V1 → V2 → V3)

#### **V1 - Core MVP (Build This First)**
- ✅ User auth (login/logout)
- ✅ Company CRUD
- ✅ Project CRUD with revenue recognition
- ✅ Invoice CRUD with payment tracking
- ✅ Basic dashboard (KPIs + charts)
- ✅ P&L report (single company)
- ✅ Hebrew UI (RTL)
- ✅ Role-based access
- ✅ Excel export

**Timeline: 2-3 weeks**

#### **V2 - Advanced Features**
- Multi-currency conversion rates (API)
- Consolidated reports
- Bank reconciliation
- Expense categories & budgets
- File uploads (contracts)
- Advanced filtering/search
- Email notifications (invoice overdue)
- Audit log

**Timeline: +2 weeks**

#### **V3 - Automation & Scale**
- Recurring invoices (retainer automation)
- Cash flow forecasting (ML-based)
- Integration: QuickBooks, Xero
- Mobile app (React Native)
- Advanced analytics (cohorts, trends)
- Multi-language support

**Timeline: +4 weeks**

---

## CLAUDE CODE EXECUTION INSTRUCTIONS

### Step 1: Project Initialization
```bash
# Initialize monorepo
npx create-vite frontend --template react-ts
mkdir backend
cd backend && npm init -y

# Install dependencies
cd frontend && npm install react-router-dom @tanstack/react-query zustand
npm install -D tailwindcss postcss autoprefixer tailwindcss-rtl
npm install shadcn-ui recharts date-fns react-dnd
npm install zod react-hook-form @hookform/resolvers

cd ../backend
npm install express prisma @prisma/client bcrypt jsonwebtoken cors helmet express-rate-limit
npm install -D typescript @types/node @types/express ts-node-dev
```

### Step 2: Database Setup (PostgreSQL - Scalable & Internet-Ready)

**Option A: Supabase (Recommended - Easiest)**
```bash
# 1. Create free account at supabase.com
# 2. Create new project (takes 2 minutes)
# 3. Copy connection string from Settings → Database

# 4. In backend folder:
cd backend
npx prisma init

# 5. Edit .env file:
DATABASE_URL="postgresql://postgres:[YOUR-PASSWORD]@db.[PROJECT-REF].supabase.co:5432/postgres"

# 6. Edit schema.prisma (already set to postgresql)
# Add all models (Tenant, User, Company, Project, Invoice, etc.)

# 7. Create tables
npx prisma migrate dev --name init

# 8. Generate Prisma Client
npx prisma generate

# 9. View database (optional)
npx prisma studio  # Opens browser UI
```

**Option B: Railway PostgreSQL**
```bash
# 1. Connect GitHub repo to Railway
# 2. Add PostgreSQL plugin (free)
# 3. Railway provides DATABASE_URL automatically

cd backend
npx prisma migrate dev --name init
npx prisma generate
```

**Database ready! Accessible from internet, auto-backups included.**

### Step 3: Build Order
1. **Backend First**:
   - Auth system (register/login/me)
   - Company endpoints
   - Project endpoints with revenue
   - Invoice endpoints
   - Basic dashboard endpoint

2. **Frontend Second**:
   - Auth pages (login)
   - Layout with RTL sidebar
   - Projects page (table + form)
   - Project detail with revenue allocation
   - Dashboard with KPIs

3. **Reports & Polish**:
   - P&L report
   - Excel export
   - Bank accounts
   - Role enforcement
   - Testing

### Step 4: Testing Strategy
```bash
# Backend
cd backend
npm install -D jest @types/jest supertest
npm test

# Frontend
cd frontend
npm install -D vitest @testing-library/react @testing-library/user-event
npm run test

# E2E
npx playwright install
npx playwright test
```

---

## CRITICAL SUCCESS FACTORS

### For Claude Code:
1. **Start Simple**: Build V1 core first, don't over-engineer
2. **Hebrew RTL**: Test every UI component in RTL mode immediately
3. **Database First**: Get schema right before building features
4. **Type Safety**: Use TypeScript strictly, no `any` types
5. **Component Library**: Use shadcn/ui, don't build from scratch
6. **Security**: Never commit secrets, use environment variables
7. **Git Workflow**: Commit after each major feature works
8. **Testing**: Write tests as you go, not at the end

### Known Pitfalls:
- ⚠️ RTL breaks: Tables, forms, dropdowns need special attention
- ⚠️ Decimal precision: Use Prisma Decimal type for currency
- ⚠️ Date handling: Always UTC in DB, convert for display
- ⚠️ Hebrew fonts: Include Rubik or Assistant font
- ⚠️ VAT calculation: 17% in Israel, apply after amounts

---

## FINAL DELIVERABLES

When complete, the repository should have:

```
/P-L
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── lib/
│   │   ├── types/
│   │   └── App.tsx
│   ├── public/
│   ├── package.json
│   └── vite.config.ts
├── backend/
│   ├── src/
│   │   ├── controllers/
│   │   ├── routes/
│   │   ├── middleware/
│   │   ├── services/
│   │   ├── prisma/
│   │   └── server.ts
│   ├── tests/
│   ├── package.json
│   └── tsconfig.json
├── docker-compose.yml
├── README.md (deployment instructions)
└── .env.example

```

**README must include:**
- Setup instructions
- Environment variables
- Database migration steps
- Running locally
- Running tests
- Deployment guide

---

## CLAUDE: BEGIN DEVELOPMENT

You are now ready to build this system autonomously. Follow these steps:

1. **Review Prompt**: Confirm understanding
2. **Initialize**: Set up project structure
3. **Database**: Create Prisma schema
4. **Auth**: Build login system
5. **Backend API**: Build all endpoints
6. **Frontend**: Build UI components
7. **Integration**: Connect frontend to backend
8. **Testing**: Write and run tests
9. **Polish**: Fix bugs, improve UX
10. **Document**: Write README

**Start with**: "I've reviewed the requirements. Beginning with project initialization..."

Ask clarifying questions ONLY if critical information is missing. Otherwise, proceed with development.

Good luck! 🚀
