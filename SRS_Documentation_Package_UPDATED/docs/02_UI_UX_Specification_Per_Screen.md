# UI/UX Specification per Screen
## Project Management Web Application

### Document Status
Draft v0.1

### Purpose of Document
Dokumen ini menjabarkan spesifikasi UI/UX per screen untuk melengkapi SRS utama. Fokus dokumen ini adalah membantu AI/coder, frontend engineer, dan UI implementer memahami struktur layar, komponen, states, interaksi, validasi, dan navigasi antar halaman secara lebih detail.

---

# 1. Design Principles

## 1.1 General Principles
1. Antarmuka harus mendukung penggunaan desktop sebagai prioritas utama.
2. Layout harus menekankan keterbacaan data tabel, struktur WBD, dan informasi status.
3. Interaksi harus mengutamakan efisiensi kerja operasional.
4. Data resmi dan data pending harus dibedakan secara visual.
5. Semua screen harus konsisten dalam penggunaan:
   - page title
   - filter bar
   - action bar
   - status badge
   - modal form
   - table styling
   - validation message
   - empty/loading/error state

## 1.2 Global Layout
Setiap halaman utama direkomendasikan memiliki struktur:
1. **App Shell**
   - sidebar navigation
   - topbar
   - main content area

2. **Topbar**
   - project selector
   - quick search
   - notification area
   - user profile / avatar

3. **Main Content**
   - page header
   - page action bar
   - filter area
   - content section
   - modal / drawer overlay bila diperlukan

## 1.3 Responsive Behavior
1. Desktop adalah layout utama.
2. Tablet boleh menyusun ulang panel menjadi stacked layout.
3. Mobile bukan prioritas utama MVP, tetapi komponen harus tetap tidak rusak secara layout.
4. Table lebar harus mendukung horizontal scroll.

---

# 2. Global UI Components

## 2.1 Buttons
Jenis tombol minimum:
- **Primary Button**: aksi utama seperti Save, Submit, Generate
- **Secondary Button**: aksi pendukung seperti Cancel, Filter, Export
- **Danger Button**: aksi reject, delete, archive
- **Ghost/Button Link**: aksi kecil seperti View Detail, Open History

## 2.2 Status Badge
Status harus tampil dengan badge konsisten.

Contoh status badge:
- Draft
- Pending Approval
- Approved
- Rejected
- Final Approved
- Review
- Active
- Archived
- Running
- Planned
- Delayed
- Done

## 2.3 Table Standard
Table default harus mendukung:
- sticky header bila sesuai
- sort indicator
- row action area
- empty state
- loading state
- pagination
- horizontal scroll untuk data lebar

## 2.4 Modal Standard
Modal digunakan untuk:
- create/edit form sederhana
- approval action
- reject confirmation
- file upload

Modal minimum harus memiliki:
- title
- subtitle/description singkat
- content form
- action footer
- close button
- cancel button
- submit button

## 2.5 Filter Bar Standard
Filter bar minimum dapat berisi:
- search input
- dropdown status
- dropdown date range / period
- dropdown category/group
- reset filter button

---

# 3. Navigation Structure

## 3.1 Main Navigation
Menu utama:
- Dashboard
- WBD
- Gantt
- Progress
- Documents / Files
- S-Curve
- Cost Analysis
- Reports
- User Settings / System Administration

## 3.2 Cross-Screen Navigation Rules
1. Dari dashboard, user harus bisa masuk ke modul detail terkait.
2. Dari WBD, user harus bisa menuju Gantt dan Progress context yang relevan.
3. Dari Progress, user harus bisa melihat actual cost terkait.
4. Dari File Repository, user harus bisa membuka file terkait WBD item atau progress entry.
5. Dari Reports, user harus bisa kembali ke project context.

---

# 4. Screen Specification

# 4.1 Login Screen

## Purpose
Memberikan akses masuk ke sistem.

## Accessible By
Semua user yang memiliki akun aktif.

## Main Sections
- logo / branding
- form login
- error message area

## Components
- email input
- password input
- login button

## Validation
- email wajib
- password wajib
- email harus format valid

## States
- default
- loading saat submit
- invalid credentials
- account inactive

## Interactions
- Enter key men-submit form
- tombol login disabled saat proses submit

---

# 4.2 Project List Screen

## Purpose
Menampilkan daftar semua project yang dapat dilihat user.

## Accessible By
Semua role.

## Main Sections
- page title
- project filter bar
- project table / card list

## Components
- search project
- filter status
- sort by name / period
- project table

## Table Columns
- project code
- project name
- client name
- location
- start date
- end date
- status
- active baseline version
- action

## Primary Actions
- View Project Detail

## Secondary Actions
- search
- filter
- sort

## States
- loading projects
- empty project list
- no search result
- error fetch data

## Navigation
- click row / detail button → Project Detail Screen

---

# 4.3 Project Detail Screen

## Purpose
Menampilkan ringkasan project dan pintu masuk ke semua modul project.

## Accessible By
Semua role.

## Main Sections
- project summary card
- KPI summary
- quick links to modules
- baseline status panel

## Components
- project info block
- active baseline badge
- summary metrics
- shortcut cards/buttons

## Displayed Information
- project name
- client name
- location
- period
- project status
- active baseline version
- high-level KPI

## States
- loading
- no active baseline
- active baseline available
- archived/inactive project

---

# 4.4 Dashboard Screen

## Purpose
Menampilkan ringkasan resmi kondisi project berdasarkan baseline aktif dan transaksi approved.

## Accessible By
Semua role.

## Main Sections
- page header
- dashboard filter bar
- KPI summary cards
- status breakdown section
- risk / deviation section
- quick activity / recent updates section

## Components
- period filter
- optional group filter
- KPI cards
- progress summary cards
- risk highlights
- chart placeholders / simple visuals

## Displayed Metrics
- total baseline cost
- total actual approved cost
- official progress percent
- active items count
- delayed items count
- main deviation summary
- active baseline status

## States
- loading skeleton
- empty state when no official data
- no active baseline state
- partial data warning

## Interactions
- clicking KPI can open related module
- project selector in topbar refreshes dashboard context

---

# 4.5 WBD Tree/Grid Screen

## Purpose
Menampilkan dan mengelola struktur WBD multi-level.

## Accessible By
- View: semua role
- Create/Edit/Submit: Project Manager, Admin Proyek
- Approve/Reject: Direksi via approval screen

## Main Sections
- page header with current WBD version
- version status banner
- action bar
- filter bar
- WBD tree table
- summary footer / totals
- side info panel or inline help

## Components
- version selector or version panel
- create draft version button
- add group button
- add item button
- submit for approval button
- table/tree grid
- row action buttons
- summary cards

## Table Columns
- code
- name
- unit
- volume
- rate
- planned cost
- component percent
- total percent
- start date
- duration
- end date
- status
- actions

## Row Behaviors
- group rows visually distinct
- expandable/collapsible hierarchy
- item rows indented by level
- final approved version should be read-only

## Primary Actions
- add group
- add item
- edit node
- create revision
- submit for approval

## Secondary Actions
- filter by group
- filter by status
- search node
- export

## States
- loading
- empty WBD draft
- final approved read-only state
- pending approval banner
- rejected version banner with reason

## Validation
For item node:
- name required
- unit required
- volume required
- rate required
- start date required
- duration required
- calculated end date shown
- calculated planned cost shown

## UX Notes
- auto calculation should update instantly after volume/rate/duration changes
- readonly calculated fields should be visually distinct
- if version is final approved, edit actions disabled and replaced by “Create Revision”

---

# 4.6 WBD Version Screen / Panel

## Purpose
Menampilkan histori versi WBD pada satu project.

## Accessible By
- View: semua role
- Action: PM, Admin Proyek, Direksi

## Main Sections
- version list
- version metadata
- action buttons

## Table/List Fields
- version number
- status
- based on version
- submitted by
- submitted at
- approved by
- approved at
- rejection reason
- active flag

## Actions
- open version detail
- create new draft from active version
- submit draft
- approve
- reject

## States
- no version yet
- draft exists
- pending approval exists
- rejected version exists
- active baseline exists

---

# 4.7 WBD Approval Screen (Direksi)

## Purpose
Membantu Direksi mereview dan memutuskan approval atas WBD.

## Accessible By
Direksi only.

## Main Sections
- version summary
- change summary
- WBD preview / comparison
- approval action area

## Components
- metadata card
- change notes
- version diff summary
- approve button
- reject button
- reject reason input

## States
- loading
- pending approval
- already approved
- already rejected

## Interactions
- reject requires rejection reason
- approve may allow optional note

---

# 4.8 Gantt Screen

## Purpose
Menampilkan jadwal project berbasis baseline aktif secara read-only.

## Accessible By
Semua role.

## Main Sections
- header
- filter bar
- split layout:
  - task panel
  - timeline panel
- legend

## Components
- group/status filter
- time scale selector
- read-only gantt grid
- legend chips

## Behaviors
- no drag
- no inline edit
- clicking task may open task detail or WBD detail
- gantt refreshes when active baseline changes

## States
- loading
- no active baseline
- empty task list
- filtered empty result

---

# 4.9 Progress List Screen

## Purpose
Menampilkan daftar progress entry dalam konteks project.

## Accessible By
Semua role untuk view.
PM/Admin Proyek untuk create/manage.
PM untuk approve/reject.

## Main Sections
- header
- action bar
- filter bar
- progress table
- summary cards

## Components
- add progress button
- filter by status
- filter by date range
- filter by group/item
- search item
- progress table

## Table Columns
- progress id / reference
- item name
- group
- progress date
- progress volume
- entered by
- status
- approval info
- actual cost summary
- actions

## Actions
- add progress
- open detail
- approve
- reject

## States
- loading
- no progress
- no active baseline
- no search result
- approval pending highlight

---

# 4.10 Progress Entry Form Screen / Modal

## Purpose
Memasukkan progress baru.

## Accessible By
Project Manager, Admin Proyek.

## Main Sections
- form
- related WBD item context
- preview summary
- attachment section optional

## Fields
- WBD item
- progress date
- progress volume
- note
- optional attachments

## Validation
- active baseline required
- operasional WBD item required
- progress date required
- progress volume > 0
- permission required

## Behaviors
- Admin Proyek submit → status `PENDING_PM_APPROVAL`
- PM submit → status `AUTO_APPROVED`
- after submit show success feedback and redirect/reload list

## States
- loading WBD item list
- no active baseline
- validation error
- submit success

---

# 4.11 Progress Approval Screen

## Purpose
Memudahkan PM memproses progress pending.

## Accessible By
Project Manager only.

## Main Sections
- pending list
- detail panel
- approval footer

## Components
- pending table/list
- progress detail card
- approve button
- reject button
- reject reason input

## Behaviors
- reject requires reason
- approved/rejected item should disappear from pending list or move state

---

# 4.12 Actual Cost Transaction List Screen

## Purpose
Menampilkan transaksi biaya aktual.

## Accessible By
Semua role untuk view.
Finance/Admin Proyek untuk create.
Finance untuk approve/reject.

## Main Sections
- header
- action bar
- filter bar
- transaction table
- summary strip

## Table Columns
- transaction id
- progress reference
- WBD item
- transaction date
- amount
- entered by
- status
- reviewed by
- reviewed at
- actions

## Actions
- add actual cost
- approve
- reject
- open related progress

## States
- loading
- empty transactions
- only pending transactions
- no search result

---

# 4.13 Actual Cost Entry Form Screen / Modal

## Purpose
Memasukkan actual cost yang selalu terkait ke progress entry.

## Accessible By
Finance, Admin Proyek.

## Fields
- progress entry
- transaction date
- amount
- description

## Validation
- progress entry required
- transaction date required
- amount > 0
- PM must not access this form

## Behaviors
- Admin Proyek submit → status `REVIEW`
- Finance submit → mengikuti policy implementasi akhir
- success message after submit

## States
- no eligible progress entry
- validation error
- submit success

---

# 4.14 Cost Review Screen (Finance)

## Purpose
Membantu Finance mereview transaksi biaya.

## Accessible By
Finance only.

## Main Sections
- review queue
- related progress detail
- cost detail
- action footer

## Components
- pending transaction list
- related progress info
- WBD item info
- approve button
- reject button
- rejection reason field

## Behaviors
- reject requires reason
- approved transactions move out of review queue

---

# 4.15 File Repository List Screen

## Purpose
Menampilkan dokumen dan foto project.

## Accessible By
Semua role untuk view.
PM/Admin Proyek untuk upload/manage operasional.

## Main Sections
- header
- action bar
- filter bar
- file list table or gallery hybrid

## Components
- upload file button
- filter category
- filter file type
- filter related entity
- search by file name
- file list

## Table/List Fields
- file name
- file type
- category
- related entity
- uploaded by
- uploaded at
- caption (for image)
- photo date (for image)
- actions

## Behaviors
- images support preview
- documents support download/open
- relation badges show linked entity context

## States
- loading
- empty file repository
- no result
- filter empty result

---

# 4.16 File Upload Form Screen / Modal

## Purpose
Upload dokumen atau foto.

## Accessible By
Project Manager, Admin Proyek.

## Fields
- file
- file type
- category
- related entity type
- related entity
- caption
- photo date
- note

## Validation
- file required
- category required
- if file type = IMAGE:
  - caption required
  - photo date required
- if related entity type selected:
  - related entity required

## States
- validation error
- upload in progress
- upload success
- upload failed

---

# 4.17 File Category Master Screen

## Purpose
Mengelola kategori file.

## Accessible By
Administrator Sistem only.

## Main Sections
- category list
- add/edit form
- active/inactive toggle

## Fields
- category name
- description
- is_active

## States
- loading
- empty master list
- duplicate name error

---

# 4.18 S-Curve Screen

## Purpose
Menampilkan plan vs actual kumulatif.

## Accessible By
Semua role.

## Main Sections
- header
- filter bar
- chart area
- metric summary
- deviation insight area

## Components
- period filter
- optional group filter
- curve chart
- summary cards
- insight panel

## Behaviors
- actual only from official data
- baseline follows active WBD version
- tooltips show period values

## States
- loading chart
- no official data
- no baseline
- error loading analytics

---

# 4.19 Cost Analysis Screen

## Purpose
Menampilkan analisis biaya dan progress berbobot.

## Accessible By
Semua role.

## Main Sections
- header
- filter bar
- summary cards
- analysis table
- quick insights panel

## Components
- project / period filter
- group/status filter
- analysis table
- weighted progress display
- deviation indicators

## States
- loading
- empty data
- no approved cost yet

---

# 4.20 Report Generation Screen

## Purpose
Generate report final.

## Accessible By
Project Manager, Admin Proyek.

## Main Sections
- report parameter form
- report preview summary
- generate action area

## Fields
- report type
- period start
- period end
- optional filters

## Validation
- report type required
- period start required
- period end required
- end date >= start date

## Behaviors
- generate creates final report record
- no draft stage
- show progress/loading while generate

## States
- idle
- generating
- generation success
- generation failed

---

# 4.21 Report History Screen

## Purpose
Menampilkan histori report final.

## Accessible By
Semua role.

## Main Sections
- header
- filter bar
- report history table

## Table Columns
- report type
- project
- period
- generated by
- generated at
- status
- file output

## Behaviors
- download/open report file
- filter by type and period

## States
- no report history
- loading
- no filtered results

---

# 4.22 User Administration Screen

## Purpose
Mengelola user dan role.

## Accessible By
Administrator Sistem only.

## Main Sections
- user list
- add/edit user form
- role assignment controls

## Fields
- full name
- email
- role
- is_active

## Validation
- full name required
- email required and unique
- role required

## States
- loading
- empty user list
- duplicate email error

---

# 4.23 System Configuration / Master Data Screen

## Purpose
Mengelola master dan konfigurasi sistem.

## Accessible By
Administrator Sistem only.

## Main Sections
- configuration groups
- master data lists
- edit forms

## Potential Areas
- file category master
- project status master
- node status master
- system parameters

---

# 5. State Specification Summary

## 5.1 Mandatory States for Every Data Screen
Setiap data screen minimal harus punya state:
- loading
- empty
- no search/filter result
- error
- permission restricted if applicable

## 5.2 Mandatory Form States
Setiap form minimal harus punya state:
- default
- validating
- submit loading
- submit success
- submit failed

---

# 6. UX Rules for Validation and Feedback

## 6.1 Validation Behavior
1. Validation field-level tampil dekat field terkait
2. Validation message harus jelas dan spesifik
3. Submit button disabled saat proses submit
4. Calculated fields tampil readonly

## 6.2 Success Feedback
1. Setelah create/update/approve/reject, tampilkan success message
2. Setelah success, list harus refresh atau status visual harus berubah
3. Approval action harus menghasilkan perubahan state yang langsung terlihat

## 6.3 Error Feedback
1. Error fetch data harus tampil dalam alert/panel yang jelas
2. Error bisnis seperti “baseline aktif belum tersedia” harus tampil sebagai pesan yang mudah dipahami
3. Error permission harus membedakan:
   - action hidden
   - action disabled
   - forbidden response

---

# 7. Visual Distinction Rules

## 7.1 Read-Only vs Editable
- field readonly harus tampil berbeda dari input editable
- WBD final approved harus jelas terlihat sebagai read-only mode
- Gantt read-only harus jelas dari tidak adanya affordance drag/edit

## 7.2 Pending vs Official Data
- data pending harus diberi badge/status visual berbeda
- analytics resmi tidak boleh mencampur data pending
- list screen sebaiknya memperlihatkan status tiap transaksi dengan jelas

---

# 8. Notes for AI/Coder

## 8.1 Frontend Priorities
Prioritaskan implementasi frontend dalam urutan:
1. App shell dan navigation
2. Project context selector
3. WBD module
4. Progress module
5. Cost module
6. File repository
7. Dashboard / analytics
8. Reports
9. Administration

## 8.2 UX Consistency
Pastikan komponen berikut reusable:
- status badge
- filter bar
- table wrapper
- modal form shell
- empty/loading/error state block
- summary card
- approval action footer

## 8.3 Implementation Caution
Hal-hal yang paling berisiko jika diinterpretasi sembarangan:
- WBD final tidak boleh editable langsung
- progress tanpa baseline aktif harus ditolak
- actual cost harus selalu terkait progress entry
- foto wajib punya caption dan photo date
- pending data tidak boleh masuk analytics resmi

---

# 9. Next Recommended UI/UX Documents
Dokumen lanjutan yang bisa dibuat setelah ini:
1. component library / design system lite
2. validation message catalog
3. user flow diagram
4. wireframe annotation per screen
5. frontend state management specification
