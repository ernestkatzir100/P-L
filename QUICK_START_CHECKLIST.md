# Quick Start Guide - Claude Code Execution

## For Ernest: Copy-Paste This to Claude Code

---

### 🚀 Immediate Action Required

**Repository**: `https://github.com/ernestkatzir100/P-L` (musical-space-enigma)  
**Goal**: Build complete business management system in Hebrew  
**Timeline**: 2-3 weeks for V1  
**Status**: Empty repo, ready to build

---

### 📋 Pre-Flight Checklist

Before starting Claude Code, ensure:

- [ ] GitHub Codespaces is running
- [ ] You have `CLAUDE_CODE_PROMPT.md` open
- [ ] You have `EXCEL_MIGRATION_GUIDE.md` ready
- [ ] Excel file `Trustegy_PnL23.xlsx` is accessible
- [ ] You know your PostgreSQL credentials (or will use Docker)

---

### 💬 Exact Prompt for Claude Code

```
I need you to build a professional services business management system based on the detailed specifications in CLAUDE_CODE_PROMPT.md.

This is a production system for a consulting firm (Entropy Group / Trustegy) that currently uses Excel for P&L tracking. The system must:
- Support Hebrew (RTL) UI
- Handle multi-entity accounting with consolidation
- Track projects, revenue recognition, invoicing, expenses, and banking
- Provide real-time dashboards and P&L reports
- Support role-based access (Admin/PM/Finance/View-only)

CRITICAL REQUIREMENTS:
1. Read CLAUDE_CODE_PROMPT.md in full before starting
2. Use the exact tech stack specified (React + TypeScript + Prisma + PostgreSQL)
3. Follow the phased approach (V1 → V2 → V3)
4. Build V1 MVP first: Core CRUD + Auth + Basic Dashboard
5. Hebrew RTL support from Day 1 (not an afterthought)
6. After V1 works, use EXCEL_MIGRATION_GUIDE.md to import historical data

Start by confirming you've read the requirements, then initialize the project structure.

Files to review:
- CLAUDE_CODE_PROMPT.md (main spec)
- EXCEL_MIGRATION_GUIDE.md (data import)
- Trustegy_PnL23.xlsx (current Excel system)

Begin development now.
```

---

### 🎯 Success Criteria for V1

You'll know V1 is complete when you can:

1. **Login** as Admin user
2. **Create** a new project with client name, budget, dates
3. **Allocate** monthly revenue across 12 months (drag-and-drop preferred)
4. **Create** an invoice for the project
5. **Mark** invoice as paid
6. **View** dashboard showing:
   - Total revenue (MTD/YTD)
   - Outstanding invoices
   - Active projects count
   - Simple chart (revenue trend)
7. **Generate** P&L report for date range
8. **Export** report to Excel
9. **Switch** between companies (if multi-entity)
10. **See** UI in Hebrew with proper RTL layout

---

### ⚠️ Critical Reminders for Claude Code

#### Hebrew RTL Issues
```
Common bugs to watch:
- Tables: Columns reverse order → Use dir="rtl" on <table>
- Forms: Labels appear on wrong side → Use Tailwind's rtl: prefix
- Dates: Show in wrong format → Use date-fns with Hebrew locale
- Numbers: Comma separators wrong → Use Intl.NumberFormat('he-IL')
- Dropdowns: Arrows point wrong way → Check icon direction
```

#### Database Precision
```typescript
// ❌ WRONG: Will lose cents
amount: number

// ✅ CORRECT: Prisma Decimal for currency
amount: Decimal  @db.Decimal(12, 2)

// In code:
import { Decimal } from '@prisma/client/runtime';
const total = new Decimal(100.50).plus(new Decimal(200.25));
```

#### Security Checklist
```typescript
// ✅ Must have:
- Password hashing (bcrypt, min 10 rounds)
- JWT in httpOnly cookies (not localStorage)
- Input validation (zod schemas)
- SQL injection prevention (Prisma handles this)
- CORS configured properly
- Rate limiting on auth endpoints

// ❌ Never:
- Store passwords in plain text
- Use client-side auth only
- Trust user input without validation
- Expose stack traces in production
```

---

### 🧪 Testing Checklist

After V1 is built, test these scenarios:

#### Happy Path
```
1. Admin login
2. Create company "Trustegy"
3. Create project "Project A" with ₪100,000 budget
4. Allocate ₪10,000/month for 10 months
5. Create invoice for ₪10,000
6. Mark as paid
7. Check dashboard shows ₪10,000 revenue
8. Generate P&L report → See ₪10,000 income
9. Export to Excel → Open file, verify data
```

#### Edge Cases
```
1. Try to allocate ₪200,000 (exceeds ₪100,000 budget) → Should block
2. Delete project with invoices → Should warn or cascade delete
3. Create invoice without project → Should reject
4. PM user tries to delete project → Should get 403 Forbidden
5. Invalid date ranges in reports → Should show error
6. Search for "לקוח" (Hebrew) → Should work
```

#### RTL Visual Check
```
Open app in Chrome:
1. Right-click → Inspect → Toggle device toolbar
2. Check: Sidebar on right side? ✓
3. Check: Form labels on right? ✓
4. Check: Table text aligned right? ✓
5. Check: Calendar shows Hebrew month names? ✓
6. Check: Currency symbol before/after number correctly? ✓
```

---

### 📊 Data Import After V1

Once V1 is working:

```bash
# 1. Copy Excel file to backend
cp Trustegy_PnL23.xlsx backend/data/

# 2. Run import script
cd backend
npm install xlsx
ts-node scripts/import-excel.ts

# 3. Verify in Prisma Studio
npx prisma studio
# Check: Projects, Invoices, RevenueLines tables populated

# 4. Test in UI
# - Login
# - Go to Projects → See imported projects
# - Click on "ניהול קרן פיינאפל - 2023"
# - Check revenue tab → See monthly allocations from 2023
# - Go to Dashboard → See revenue chart with historical data
```

---

### 🐛 Troubleshooting Guide

#### "Claude Code can't connect to GitHub"
```bash
# In Codespaces terminal:
gh auth status
gh auth login
```

#### "Database connection failed"
```bash
# Check PostgreSQL is running:
docker ps  # Should see postgres container

# Or start it:
docker-compose up -d db

# Verify connection string:
echo $DATABASE_URL
```

#### "Hebrew text shows as ?????"
```bash
# In Vite config, ensure UTF-8:
export default defineConfig({
  build: {
    charset: 'utf8'
  }
})

# In HTML:
<meta charset="UTF-8">
```

#### "Prisma migrations fail"
```bash
# Reset database (WARNING: Deletes all data):
npx prisma migrate reset

# Or force new migration:
npx prisma migrate dev --create-only
# Edit migration file manually if needed
npx prisma migrate dev
```

---

### 🎨 UI/UX Polish Checklist

Before showing to team:

- [ ] Loading states (spinners) on all async operations
- [ ] Success/error toasts after actions
- [ ] Empty states with helpful text ("אין פרויקטים. צור פרויקט חדש")
- [ ] Confirmation dialogs for deletes
- [ ] Responsive design (works on laptop, not mobile yet)
- [ ] Keyboard shortcuts (Ctrl+K for search, etc.)
- [ ] Proper error messages in Hebrew
- [ ] Consistent spacing/padding (use Tailwind scale)
- [ ] Professional color scheme (not default shadcn colors)
- [ ] Loading skeletons (not just spinners)

---

### 📈 V1 → V2 Upgrade Path

Once V1 is stable, enhance with:

```
Priority 1 (Next Sprint):
- Consolidated P&L across all entities
- Bank reconciliation workflow
- Expense categories and budgets
- Email notifications (overdue invoices)

Priority 2 (Month 2):
- File uploads (contracts, SOWs)
- Advanced search/filtering
- Audit log for all changes
- Multi-currency conversion rates (live API)

Priority 3 (Month 3):
- Recurring invoices (automatic generation)
- Cash flow forecasting
- API integration (QuickBooks/Xero)
- Advanced analytics
```

---

### 🚢 Deployment Guide (100% FREE)

When ready for production:

**Option 1: Self-Hosted (Recommended for Control)**
```bash
# Any Linux machine works (even old laptop)

# 1. Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 2. Clone repo
git clone https://github.com/ernestkatzir100/P-L
cd P-L

# 3. Set environment
cp .env.example .env.production
nano .env.production
# Edit: DATABASE_URL, JWT_SECRET

# 4. Build and run
docker-compose -f docker-compose.prod.yml up -d

# 5. Setup SSL (Let's Encrypt - FREE)
sudo apt install certbot nginx
sudo certbot --nginx -d yourdomain.com

# Done! Access at https://yourdomain.com
```

**Option 2: Oracle Cloud (Forever Free Tier)**
```bash
# Sign up: https://cloud.oracle.com/free
# Create Ubuntu VM (ARM Ampere A1, 4 cores, 24GB RAM - FREE!)
# SSH into VM
# Follow Option 1 steps above
# Use DuckDNS.org for free domain
```

**Option 3: Keep in GitHub Codespaces**
```bash
# Already running!
# Just keep Codespace active
# Share URL with team
# Cost: Included in your GitHub Pro
```

**Free Domain Options:**
- DuckDNS.org (free subdomain)
- Freenom.com (free .tk/.ml/.ga domains)
- No-IP.com (free dynamic DNS)

**Cost Breakdown:**
- Software: ₪0 (all open-source)
- Hosting: ₪0 (self-hosted or free tier)
- SSL: ₪0 (Let's Encrypt)
- Domain: ₪0 (free options above)
- **Total: ₪0/month**

---

### 📞 Getting Help

If Claude Code gets stuck:

1. **Check logs**: `docker-compose logs -f` or `npm run dev` output
2. **Simplify**: Break complex feature into smaller pieces
3. **Reset**: Sometimes `rm -rf node_modules && npm install` fixes issues
4. **Ask specific**: "The login endpoint returns 401 even with correct password. Here's the code: [paste]. What's wrong?"

---

### ✅ Final Checklist Before Handoff

- [ ] All V1 features work
- [ ] Data import successful
- [ ] Tests pass (at least manual testing)
- [ ] README.md written with setup instructions
- [ ] .env.example provided
- [ ] No secrets committed to git
- [ ] Database migrations clean
- [ ] UI works in Hebrew RTL
- [ ] Role-based access tested
- [ ] Performance acceptable (<3s page loads)

---

## 🎉 You're Ready!

Copy the "Exact Prompt for Claude Code" section above and paste it into Claude Code. Watch the magic happen!

**Expected timeline:**
- Day 1-2: Project setup, auth, database
- Day 3-5: Project CRUD, revenue allocation
- Day 6-8: Invoices, dashboard
- Day 9-10: Reports, polish
- Day 11-14: Testing, bug fixes, data import

Good luck! 🚀

---

## Post-Build: Showing to Team

When presenting V1:

1. **Start with dashboard**: Visual impact
2. **Create project live**: Show ease of use
3. **Import historical data**: "Look, all 2023-2024 data is here"
4. **Generate report**: Export Excel, show it matches old format
5. **Show mobile/tablet**: Even if rough, shows forward-thinking
6. **Highlight Hebrew**: "Built for Israeli market from Day 1"

**Key selling points:**
- ✅ Replaces Excel (no more broken formulas)
- ✅ Real-time collaboration (multiple users)
- ✅ Role-based security (finance data protected)
- ✅ Automatic calculations (no human error)
- ✅ Audit trail (who changed what)
- ✅ Professional UI (clients can see reports)
- ✅ Scalable (can add entities, users, features)

**Expected questions:**
Q: "Can we export to Excel?"  
A: "Yes, all reports export to Excel matching current format."

Q: "What if internet goes down?"  
A: "Data is backed up daily. Can host on local server if needed."

Q: "How long to add a new feature?"  
A: "Depends, but system is modular. Simple features: days. Complex: weeks."

Q: "Who can access what?"  
A: "Four roles: Admin (full access), PM (projects only), Finance (money only), View (read-only)."

Now go build! 🛠️
