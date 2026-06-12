# Implementation Task Breakdown
## Project Management Web Application

### Document Status
Draft v0.1

### Purpose of Document
Dokumen ini memecah kebutuhan implementasi menjadi task-task yang lebih operasional agar AI/coder, backend engineer, frontend engineer, QA, dan technical lead dapat memulai build secara terstruktur.

Dokumen ini dirancang untuk menjawab:
- apa yang harus dibangun dulu,
- task mana yang menjadi dependency untuk task lain,
- deliverable apa yang diharapkan dari tiap fase,
- urutan implementasi yang paling efisien untuk MVP.

---

# 1. Implementation Strategy

## 1.1 Recommended Delivery Principle
Implementasi sebaiknya dilakukan dengan urutan berikut:
1. **foundation first**
2. **core workflow next**
3. **analytics after official data flow is stable**
4. **reporting after data consistency is proven**
5. **administration and polish in parallel or near-final stage**

## 1.2 Main Delivery Streams
Pekerjaan dibagi ke dalam stream berikut:
1. Project Setup & Architecture
2. Authentication & Authorization
3. Master Data & Administration
4. Project Core
5. WBD & Baseline Versioning
6. Progress Workflow
7. Actual Cost Workflow
8. File Repository
9. Analytics
10. Reporting
11. Testing & QA
12. Deployment Preparation

---

# 2. Phase Breakdown

# Phase 0 — Project Setup and Alignment

## Objective
Menyiapkan fondasi teknis dan menyamakan pemahaman implementasi.

## Tasks
### 0.1 Review documentation package
- baca `SRS_Main`
- baca `UI/UX Specification`
- baca schema draft
- baca API payload samples
- baca acceptance criteria

### 0.2 Confirm implementation stack
- pilih backend framework
- pilih frontend framework
- pilih database
- pilih object/file storage
- pilih auth approach
- pilih deployment target

### 0.3 Define repository structure
- setup mono repo atau multi repo
- tentukan folder backend/frontend/docs
- setup coding convention
- setup branch strategy

### 0.4 Setup engineering standards
- linting
- formatting
- commit convention
- environment variable strategy
- migration strategy
- API versioning rule

## Deliverables
- implementation stack decision
- repo initialized
- engineering standards agreed

## Dependencies
None

---

# Phase 1 — Foundation and Access Control

## Objective
Membangun fondasi autentikasi, role access, dan entitas master inti.

## Tasks
### 1.1 Implement database core tables
- roles
- users
- file_categories

### 1.2 Seed master roles
- Administrator Sistem
- Project Manager
- Direksi
- Finance
- Admin Proyek

### 1.3 Implement authentication module
- login endpoint
- current user endpoint
- auth middleware
- session/token strategy

### 1.4 Implement role-based authorization
- permission guard for API
- frontend route guard
- role-based action visibility

### 1.5 Build user administration module
- list users
- create user
- update role
- activate/deactivate user

### 1.6 Build file category master
- list categories
- create category
- update category
- activate/deactivate category

## Deliverables
- login working
- role guard working
- user master working
- file category master working

## Dependencies
- Phase 0 completed

---

# Phase 2 — Project Core

## Objective
Membangun konteks project sebagai root untuk seluruh modul.

## Tasks
### 2.1 Implement project table and migration
- projects table
- project status master or enum
- project FK to creator user

### 2.2 Build project APIs
- list projects
- project detail
- optional create/update project (if included in implementation scope)

### 2.3 Build frontend project screens
- project list screen
- project detail screen
- project context selector in topbar

### 2.4 Implement project access rule
- all registered users can view all projects
- no user-project mapping

## Deliverables
- project list working
- project detail working
- project selector working

## Dependencies
- Phase 1 completed

---

# Phase 3 — WBD and Baseline Versioning

## Objective
Membangun modul paling inti: WBD hierarchy, versioning, dan approval baseline.

## Tasks
### 3.1 Implement WBD database layer
- wbd_versions table
- wbd_nodes table
- active_wbd_version relation in project

### 3.2 Implement WBD calculation engine
- planned_cost = volume × rate
- end_date = start_date + duration - 1
- subtotal aggregation
- total project aggregation
- component percent
- total percent

### 3.3 Implement WBD tree CRUD
- create WBD draft version
- create group node
- create item node
- edit node in draft
- delete node in draft
- reorder/sort nodes

### 3.4 Implement WBD version workflow
- create revision from active baseline
- submit for Direksi approval
- approve version
- reject version
- supersede old active version
- enforce single active baseline

### 3.5 Build frontend WBD screens
- WBD tree/grid screen
- version list/panel
- WBD approval screen for Direksi

### 3.6 Enforce read-only on final approved WBD
- disable edit direct on final baseline
- enable create revision instead

## Deliverables
- WBD versioning working end-to-end
- Direksi approval flow working
- one active baseline rule enforced

## Dependencies
- Phase 2 completed

---

# Phase 4 — Gantt Read-Only Module

## Objective
Menampilkan jadwal dari baseline aktif.

## Tasks
### 4.1 Build gantt data transformation logic
- map WBD items to timeline tasks
- derive date ranges from baseline active version

### 4.2 Build gantt endpoint
- read-only gantt data
- filtering by group/status if needed

### 4.3 Build frontend gantt screen
- left task panel
- timeline panel
- legend
- filters
- read-only interactions

### 4.4 Enforce no direct edit
- disable drag
- disable inline date update
- route user to WBD for schedule changes

## Deliverables
- gantt screen working from active baseline
- read-only rule enforced

## Dependencies
- Phase 3 completed

---

# Phase 5 — Progress Workflow

## Objective
Membangun transaksi progress resmi dan approval PM.

## Tasks
### 5.1 Implement progress_entries table
- migration
- enum/status support
- approval fields

### 5.2 Implement progress business guards
- baseline active required before create
- only operasional WBD node may receive progress
- status assignment by role
- approved data filtering

### 5.3 Build progress APIs
- create progress
- list progress
- detail progress
- approve progress
- reject progress

### 5.4 Build progress frontend
- progress list screen
- progress entry form/modal
- PM approval screen

### 5.5 Implement audit logging for progress actions
- create
- approve
- reject

## Deliverables
- Admin Proyek progress flow working
- PM approval flow working
- approved-only logic available for downstream analytics

## Dependencies
- Phase 3 completed

---

# Phase 6 — Actual Cost Workflow

## Objective
Membangun transaksi biaya aktual terpisah namun terkait progress.

## Tasks
### 6.1 Implement actual_cost_transactions table
- migration
- relation to progress_entry
- review fields
- lifecycle statuses

### 6.2 Implement cost business guards
- progress_entry_id mandatory
- PM cannot input cost
- Admin Proyek cost goes to REVIEW
- Finance approval required for official cost
- project consistency check between cost and progress

### 6.3 Build actual cost APIs
- create actual cost
- list actual cost
- detail actual cost
- approve actual cost
- reject actual cost

### 6.4 Build actual cost frontend
- transaction list screen
- cost entry form/modal
- Finance review screen

### 6.5 Implement baseline cost edit handling
- Finance input baseline cost through WBD revision flow
- no direct edit on active final baseline

## Deliverables
- actual cost workflow working
- Finance approval flow working
- baseline cost governance respected

## Dependencies
- Phase 5 completed
- Phase 3 completed

---

# Phase 7 — File Repository

## Objective
Membangun upload dokumen dan foto yang bisa dikaitkan ke WBD item atau progress.

## Tasks
### 7.1 Implement file storage strategy
- local/private object storage decision
- file path scheme
- signed access or protected download if needed

### 7.2 Implement project_files table
- migration
- file type rules
- image metadata rules
- polymorphic relation fields

### 7.3 Implement upload APIs
- upload file
- list files
- file detail
- update metadata if needed

### 7.4 Implement file validation rules
- image requires caption
- image requires photo_date
- category required
- related entity pair consistency

### 7.5 Build frontend file repository
- file list screen
- upload form/modal
- image preview
- document download
- filters by category/type/related entity

### 7.6 Connect files to context screens
- open files from progress context
- open files from WBD node context

## Deliverables
- document upload working
- image upload with metadata validation working
- file repository screen working

## Dependencies
- Phase 1 file categories completed
- Phase 3 and/or Phase 5 for relation context

---

# Phase 8 — Dashboard and Analytics

## Objective
Membangun output monitoring resmi berbasis baseline aktif dan transaksi approved.

## Tasks
### 8.1 Define official-data query rules
- approved progress only
- approved actual cost only
- active baseline only

### 8.2 Build dashboard aggregation service
- official progress percent
- baseline cost total
- approved actual cost total
- active items
- delayed items
- deviation summary

### 8.3 Build S-Curve calculation service
- cumulative plan volume
- cumulative actual volume
- cumulative plan cost
- cumulative actual cost
- period grouping strategy

### 8.4 Build cost analysis service
- baseline vs approved actual
- weighted progress
- group/item deviation
- health indicators

### 8.5 Build analytics APIs
- dashboard
- gantt data
- s-curve
- cost analysis

### 8.6 Build frontend analytics screens
- dashboard
- s-curve
- cost analysis

## Deliverables
- analytics screens working with official data only
- dashboard numbers consistent with approved transactions

## Dependencies
- Phase 3 completed
- Phase 5 completed
- Phase 6 completed

---

# Phase 9 — Reporting

## Objective
Menghasilkan report final berbasis data resmi.

## Tasks
### 9.1 Implement report_records table
- migration
- report type
- period
- file path
- generated_by
- status

### 9.2 Build report generation service
- choose report type
- choose project
- choose period
- optional filter set
- generate final output file

### 9.3 Build report APIs
- generate report
- list report history
- get report detail/download metadata

### 9.4 Build frontend report screens
- report generation form
- report history table

## Deliverables
- final report generation working
- report history available

## Dependencies
- Phase 8 completed

---

# Phase 10 — Audit, Integrity, and Correction Support

## Objective
Memastikan jejak audit dan dasar correction strategy tersedia.

## Tasks
### 10.1 Implement audit_logs table
- migration
- helper/service for logging important actions

### 10.2 Log critical actions
- WBD submit/approve/reject
- progress create/approve/reject
- cost create/approve/reject
- user/role changes
- file uploads if needed

### 10.3 Implement integrity checks
- one active baseline only
- no progress without baseline
- no cost without progress
- image metadata completeness

### 10.4 Prepare correction support policy
- prevent direct edit of approved progress
- prevent direct edit of approved actual cost
- prevent direct edit of final approved WBD
- prepare future adjustment mechanism hooks

## Deliverables
- audit trail basic coverage
- integrity guard stable

## Dependencies
- Phases 3, 5, 6 completed

---

# Phase 11 — QA, UAT, and Stabilization

## Objective
Memvalidasi seluruh MVP berdasarkan acceptance criteria.

## Tasks
### 11.1 Build test checklist from acceptance criteria
- per module
- per role
- cross-module scenarios

### 11.2 Execute functional testing
- WBD workflow
- progress workflow
- cost workflow
- file workflow
- analytics
- report generation

### 11.3 Execute role/permission testing
- Administrator restrictions
- PM permissions
- Direksi approval access
- Finance approval access
- Admin Proyek restrictions and permissions

### 11.4 Execute negative scenario testing
- progress without active baseline
- PM trying to input cost
- image upload without caption
- actual cost without progress entry

### 11.5 Fix defects and regression test
- retest impacted modules
- verify analytics consistency after bug fixes

## Deliverables
- passed acceptance criteria for MVP
- defect log resolved or acknowledged
- UAT-ready build

## Dependencies
- Phases 1–10 completed enough for end-to-end flow

---

# Phase 12 — Deployment Preparation

## Objective
Menyiapkan sistem untuk staging/production deployment.

## Tasks
### 12.1 Environment setup
- environment variables
- database connection
- storage config
- auth secrets

### 12.2 Migration and seed scripts
- roles
- file categories
- optional demo data for staging

### 12.3 Logging and monitoring
- backend app logs
- error monitoring
- audit log review access

### 12.4 Backup and restore basics
- DB backup policy
- uploaded file storage backup policy

### 12.5 Release checklist
- migrations tested
- seed data validated
- permissions validated
- report generation validated
- storage validated

## Deliverables
- deployable MVP package
- release readiness checklist

## Dependencies
- MVP modules stable

---

# 3. Work Breakdown by Team / Stream

# 3.1 Backend Stream
## Priority Order
1. Auth & role guard
2. Project
3. WBD versioning
4. Progress
5. Actual cost
6. Files
7. Analytics
8. Reporting
9. Audit

## Backend Key Outputs
- database schema
- migrations
- services
- APIs
- authorization guards
- business rule enforcement

---

# 3.2 Frontend Stream
## Priority Order
1. App shell and navigation
2. Project screens
3. WBD screens
4. Progress screens
5. Cost screens
6. File repository
7. Dashboard / analytics
8. Report screens
9. Administration screens

## Frontend Key Outputs
- reusable layout components
- page screens
- forms
- tables
- modals
- role-based action visibility
- status visual states

---

# 3.3 QA Stream
## Priority Order
1. Build test matrix from acceptance criteria
2. Verify role access
3. Verify WBD approval flow
4. Verify progress approval flow
5. Verify cost approval flow
6. Verify analytics official-data rule
7. Verify report generation
8. Regression cycle

---

# 4. Suggested Sprint / Iteration Breakdown

## Sprint 1
- setup
- auth
- roles
- user admin
- file category master
- project list/detail basic

## Sprint 2
- WBD schema
- WBD CRUD
- WBD versioning
- WBD approval

## Sprint 3
- gantt
- progress entry
- progress approval
- audit log baseline

## Sprint 4
- actual cost
- finance review
- file repository

## Sprint 5
- dashboard
- s-curve
- cost analysis

## Sprint 6
- reporting
- stabilization
- UAT
- deployment preparation

---

# 5. Critical Dependencies

## 5.1 Business-Critical Dependencies
- WBD baseline approval must exist before progress workflow is meaningful
- approved progress must exist before analytics can be trusted
- approved actual cost must exist before cost analysis and report become accurate

## 5.2 Technical Dependencies
- auth and role guard must exist before role-sensitive modules
- DB schema foundation must exist before service implementation
- file storage must be chosen before file repository is completed
- report generation depends on stable official data queries

---

# 6. High-Risk Areas

## 6.1 WBD Versioning and Historic Consistency
Risk:
- progress references can become confusing when baseline changes

Mitigation:
- lock progress to the relevant WBD node at transaction time
- maintain audit trail
- consider stable business code in later phase

## 6.2 Approved vs Pending Data in Analytics
Risk:
- dashboard and analytics accidentally include pending transactions

Mitigation:
- centralize official-data query logic
- test approved/pending filters explicitly

## 6.3 Baseline Cost Governance
Risk:
- Finance changes baseline cost outside WBD revision workflow

Mitigation:
- route all baseline cost changes through WBD version APIs only

## 6.4 Polymorphic File Relation
Risk:
- file linked to invalid related entity

Mitigation:
- strict service validation
- integration test file association scenarios

---

# 7. Definition of Done by Module

## 7.1 WBD Module Done
- version draft can be created
- nodes can be managed in draft
- submit/approve/reject works
- one active baseline rule enforced
- final approved WBD is read-only

## 7.2 Progress Module Done
- progress can be created only with active baseline
- Admin Proyek progress goes to PM approval
- PM progress auto-approved
- official progress query excludes pending/rejected

## 7.3 Actual Cost Module Done
- cost always tied to progress entry
- Admin Proyek cost goes to Finance review
- PM cannot input cost
- approved cost only used in analytics

## 7.4 File Module Done
- document upload works
- image upload requires caption and photo_date
- files can link to WBD node or progress entry
- image preview/document download works

## 7.5 Analytics Done
- dashboard consistent with approved transactions
- S-Curve uses approved official data
- cost analysis uses approved actual cost

## 7.6 Report Done
- report final can be generated
- report history available
- generated file downloadable

---

# 8. Recommended Next Management Artifact
Setelah dokumen ini, artefak yang paling berguna biasanya:
1. **Sprint Backlog**
2. **Task Board / Kanban Breakdown**
3. **Owner Assignment Matrix**
4. **Estimation Sheet**
5. **Release Checklist**

---

# 9. Final Note for AI/Coder
AI/coder sebaiknya mengerjakan implementasi berdasarkan urutan dependency, bukan berdasarkan urutan menu di sidebar saja.

Urutan build yang paling aman:
1. auth + roles
2. project context
3. WBD + baseline versioning
4. progress
5. actual cost
6. files
7. analytics
8. reports
9. audit and hardening
