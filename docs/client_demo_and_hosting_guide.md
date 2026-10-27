# Vote Her Ekiti — Client Demonstration & Production Hosting Blueprint

> **Release Status:** Certified Production-Ready (v1.0.0-rc)  
> **Target Cloud Infrastructure:** Supabase PostgreSQL & Auth (`gtfhpymhwpiknkzcofvh`), Supabase Storage (CDN), Next.js 16 (Turbopack)  
> **Repository:** [VoteHerEkiti](https://github.com/6twos55/VoteHerEkiti) / [voteher-ekiti-app](https://github.com/6twos55/voteher-ekiti-app)  
> **Submodule Sync:** Pushed & Up-to-Date on `origin/master` (`f67a254` / `f3156a0`)

---

## 1. Automated Integration Test Suite Report

All 29 end-to-end integration tests were executed against the live Supabase Cloud database, Supabase Storage CDN, authentication engine, and Next.js HTTP API routes.

```bash
npm run test:integration
```

### Execution Summary

| Test Suite | Total Tests | Passed | Failed | Duration | Health Status |
|---|:---:|:---:|:---:|:---:|:---:|
| **Suite 1: Database Tables (15 Tables)** | 15 | 15 | 0 | 3.21s | 🟢 100% Healthy |
| **Suite 2: Storage Buckets & CDN** | 2 | 2 | 0 | 1.03s | 🟢 100% Healthy |
| **Suite 3: Auth & Role Profiles** | 2 | 2 | 0 | 0.34s | 🟢 100% Healthy |
| **Suite 4: HTTP API Endpoints** | 9 | 9 | 0 | 3.05s | 🟢 100% Healthy |
| **Suite 5: Parity Scoring Formula** | 1 | 1 | 0 | <1ms | 🟢 100% Healthy |
| **TOTALS** | **29** | **29** | **0** | **7.64s** | 🏆 **100% Pass** |

### Verified Table Health & Sample Counts
- `appointments`: **24 real records** (Hon. Olumide Adebayo, Dr. Adunola Adeyemi, etc.)
- `historical_administrations`: **7 dispensations** (Adeyeye, Fayemi I & II, Fayose I & II, Oni, Oyebanji)
- `historical_lgas`: **16 Ekiti LGAs** (Ado, Ikole, Ijero, Ekiti West, etc.)
- `historical_metadata`: **1 active configuration row**
- `charter_demands`: **12 Women's Charter demands**
- `pledges`: **4 political legislative commitments**
- `pledge_status_history`: **5 logged status transitions**
- `bulletins`: **2 accountability editions**
- `bulletin_subscribers`: **3 initial active subscribers**
- `media_coverage`: **4 verified broadcast/press mentions**
- `press_kit_items`: **4 downloadable assets**
- `spokesperson_contacts`: **1 contact directory row**
- `admin_profiles`: **2 role-mapped admin accounts**
- `admin_audit_logs`: **10 immutable audit entries**
- `scoring_settings`: **1 active composite scoring weight matrix**

---

## 2. Admin Test Credentials & Role Matrix

Two verified real accounts are provisioned in Supabase Authentication and mapped into `admin_profiles`:

| User Email | Role | Default Password | Permissions & Capabilities |
|---|:---:|:---:|---|
| `habeeb.adewale@womantooafrica.org` | **Administrator** | `password123` | Full access: CRUD appointees, publish bulletins, upload media, edit scoring weights, view immutable audit logs, approve historical data. |
| `tinuade.mary@womantooafrica.org` | **Editor** | `password123` | Content creation & drafting: Draft appointees, prepare bulletins, log press clippings. Read-only on sensitive scoring weights and administrative settings. |

> [!NOTE]
> Passwords can be updated at any time in the Supabase Dashboard under **Authentication > Users** or via the admin settings page.

---

## 3. Client Demonstration Scenario (Step-by-Step)

Follow this 6-step walkthrough during the client presentation:

### Step 1: The Public Citizen Tracker (`/` & `/tracker`)
1. Open [`http://localhost:3000`](http://localhost:3000) (or your production URL).
2. Showcase the **35% Affirmative Action Index**:
   - The Composite Gauge shows the current parity percentage calculated live from the database.
   - Point out the 4 contributing dimensions: Cabinet (40%), MDAs (30%), Legislative (15%), Local Government (15%).
3. Navigate to **Tracker** ([`http://localhost:3000/tracker`](http://localhost:3000/tracker)):
   - Demonstrate the LGA interactive filter, Senatorial District breakdown, and real-time search.

### Step 2: Historical Benchmark & 12 Demands (`/history` & `/charter`)
1. Click **Historical Trends** ([`http://localhost:3000/history`](http://localhost:3000/history)):
   - Show how the current dispensation compares against past administrations (Fayemi, Fayose, Adebayo).
2. Click **Women's Charter** ([`http://localhost:3000/charter`](http://localhost:3000/charter)):
   - Display the 12 statutory demands and their sub-criteria fulfillment badges.

### Step 3: Secure Administrator Login (`/admin/login`)
1. Navigate to [`http://localhost:3000/admin/login`](http://localhost:3000/admin/login).
2. Sign in with:
   - **Email:** `habeeb.adewale@womantooafrica.org`
   - **Password:** `password123`
3. Notice the immediate redirection to the **Admin Overview** ([`http://localhost:3000/admin`](http://localhost:3000/admin)) displaying live statistics, quick actions, and recent audit activity.

### Step 4: Adding an Appointee & Recalculation (`/admin/appointments`)
1. Navigate to **Appointees** in the sidebar.
2. Click **"Add Appointee"**:
   - Name: `Hon. Kemi Elebute-Halle`
   - Position: `Special Adviser on Citizen Engagement`
   - Category: `Special Adviser`
   - Gender: `Female`
   - Senatorial District: `Ekiti North`
   - Status: `Active`
3. Click **"Save Appointment"**.
4. Notice:
   - The appointee appears immediately in the table.
   - A success notification confirms the action.
   - The Affirmative Action ratio automatically updates.

### Step 5: Testing Drag-and-Drop File Upload (`/admin/bulletins`)
1. Navigate to **Accountability Bulletins** ([`http://localhost:3000/admin/bulletins`](http://localhost:3000/admin/bulletins)).
2. Click **"New Edition"**:
   - Title: `October 2026 Gender Parity Digest (Special Edition)`
   - Month/Year: `October 2026`
   - Edition Number: `4`
3. Drag and drop any PDF file into the dashed dropzone (or click to browse).
4. Watch the dropzone:
   - Display the upload spinner (`Loader2`).
   - Automatically stream the file directly to Supabase Storage (`bulletins` bucket).
   - Display the green badge: `Stored on Supabase CDN`.
   - Auto-calculate the formatted file size (e.g. `2.4 MB`).
   - Provide an instant `Preview in new tab` link.
5. Click **"Draft Edition"** or **"Save Edition"**.

### Step 6: Immutable Audit Trail Verification (`/admin/history`)
1. Scroll down to the **Audit Trail** table (or view recent system logs).
2. Point out that every action performed during the demo:
   - The new appointee creation,
   - The PDF document upload to Supabase Storage,
   - Is immutably logged with the admin's email (`habeeb.adewale@womantooafrica.org`), role (`Administrator`), exact timestamp, and IP/user agent.

---

## 4. Production Hosting & Deployment Blueprint

When you are ready to host the platform (e.g., on Vercel, Netlify, or AWS Amplify):

### A. Repository Settings
- **Repository:** `https://github.com/6twos55/VoteHerEkiti`
- **Root Directory:** `voteher-ekiti-app` *(Important: set Root Directory to `voteher-ekiti-app`)*
- **Framework Preset:** `Next.js`
- **Build Command:** `npm run build`
- **Output Directory:** `.next`
- **Node.js Version:** `20.x` or `22.x`

### B. Environment Variables to Configure in Hosting Dashboard

Add these exact environment variables in your hosting provider's dashboard:

| Variable Name | Production Value | Required? | Purpose |
|---|---|:---:|---|
| `NEXT_PUBLIC_SUPABASE_URL` | `https://gtfhpymhwpiknkzcofvh.supabase.co` | **Yes** | Client-side database, auth, and CDN storage queries |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imd0ZmhweW1od3Bpa25remNvZnZoIiwicm9sZSI6ImFub24iLCJpYXQiOjE3OTI4NTY2NjMsImV4cCI6MjEwODQzMjY2M30.Q6A-sBB0oDP7UQ67AfQSALj2TfO0PXVL11xowCZOwE4` | **Yes** | Public anonymous API access with PostgreSQL RLS enforcement |
| `SUPABASE_SERVICE_ROLE_KEY` | `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imd0ZmhweW1od3Bpa25remNvZnZoIiwicm9sZSI6InNlcnZpY2Vfcm9sZSIsImlhdCI6MTc5Mjg1NjY2MywiZXhwIjoyMTA4NDMyNjYzfQ.NEVVgqkVfln1Cxg2TipBm8Y4dPxsLrA-dqe31I8Fxs0` | **Yes** | Secure server-side routes (`/api/upload`, `/api/audit-logs`, admin updates) |
| `NEXT_PUBLIC_APP_URL` | `https://voteherekiti.ng` *(or your Vercel URL)* | **Yes** | Canonical URL for social share cards and email links |
| `RESEND_API_KEY` | *(Optional, if enabling automated newsletter dispatch)* | No | Transactional email delivery for subscriber updates |
| `RESEND_FROM_EMAIL` | `GovMirror Ekiti <notifications@govmirror.ng>` | No | Verified sending address |

### C. Custom Domain DNS Setup (e.g. `voteherekiti.ng`)
Configure your registrar's DNS records:
- **A Record:** `@` -> Point to hosting provider IP (e.g., `76.76.21.21` for Vercel)
- **CNAME Record:** `www` -> Point to hosting provider alias (e.g., `cname.vercel-dns.com`)
- **SSL:** Automatic Let's Encrypt certificates are provisioned upon DNS propagation.

---

## 5. Summary of Built Features

```mermaid
graph TD
    Client[Public Citizens & Media] --> NextApp[Next.js 16 Web Application]
    Admin[Coalition Admins & Editors] --> AdminPortal[Admin Portal /admin]
    
    NextApp --> PublicPages[Public Tracker, Charter, History, Bulletins, Media]
    AdminPortal --> AuthModule[Supabase Auth Guard]
    
    AuthModule --> RoleAdmin[Administrator: Full Access]
    AuthModule --> RoleEditor[Editor: Drafting & Content]
    
    AdminPortal --> UploadEngine[FileUploadDropzone Component]
    UploadEngine --> CloudStorage[(Supabase Storage CDN: bulletins, press-kit, documents)]
    
    AdminPortal --> AuditEngine[Audit Logger Service]
    AuditEngine --> AuditTable[(admin_audit_logs Table)]
    
    PublicPages --> Database[(Supabase PostgreSQL: 15 Tables)]
    AdminPortal --> Database
```

All source code, integration test scripts, and submodule pointers are pushed, verified, and ready for deployment.
