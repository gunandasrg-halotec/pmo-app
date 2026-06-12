# Software Requirements Specification (SRS)
## Project Management Web Application

### Document Status
Draft v0.1

### Purpose of Document
Dokumen ini menjelaskan kebutuhan perangkat lunak untuk aplikasi web manajemen proyek multi-project yang digunakan untuk perencanaan, pengendalian, pelaporan, dan monitoring proyek. Dokumen ini disusun agar dapat dipahami oleh AI developer, software engineer, system analyst, QA, dan stakeholder bisnis.

---

# 1. Introduction

## 1.1 Purpose
Tujuan sistem adalah menyediakan aplikasi web manajemen proyek yang mendukung:
- pengelolaan banyak proyek dalam satu aplikasi,
- penyusunan baseline WBD,
- pengelolaan progress proyek,
- pengelolaan biaya baseline dan actual,
- penyimpanan dokumen dan foto proyek,
- monitoring Gantt, dashboard, S-curve, dan analisis biaya,
- pembuatan laporan final.

## 1.2 Product Scope
Aplikasi ini adalah sistem manajemen proyek berbasis web untuk kebutuhan operasional dan manajerial. Sistem dirancang untuk mendukung alur kerja berikut:
1. pembuatan dan pengelolaan project,
2. penyusunan WBD baseline,
3. approval WBD oleh Direksi,
4. input progress proyek,
5. input actual cost yang terkait ke progress,
6. upload dokumen dan foto pendukung,
7. monitoring performa proyek melalui dashboard, gantt, S-curve, dan cost analysis,
8. generate report final.

Sistem mendukung multi-project, namun tidak menerapkan pembatasan akses project per user. Semua user terdaftar dapat melihat seluruh project. Pembatasan dilakukan berdasarkan role dan aksi yang diperbolehkan.

## 1.3 Intended Audience
Dokumen ini ditujukan untuk:
- Product Owner / Business Stakeholder
- System Analyst
- Software Engineer / AI Coder
- UI/UX Developer
- QA / Tester
- Project Implementer

## 1.4 Definitions and Terms
- **Project**: entitas utama yang mewakili satu pekerjaan/proyek.
- **WBD**: Work Breakdown Detail / struktur rincian pekerjaan dan biaya proyek.
- **Baseline WBD**: versi WBD yang telah final approved dan menjadi acuan operasional.
- **Progress Entry**: catatan transaksi progress pekerjaan.
- **Actual Cost Transaction**: transaksi biaya aktual yang selalu terkait ke satu progress entry.
- **Final Approved**: status persetujuan final yang membuat data resmi digunakan oleh sistem.
- **Role-Based Action Control**: pengendalian hak akses berdasarkan role dan aksi, bukan berdasarkan project ownership.


## 1.5 Related Documents
Dokumen ini harus dibaca bersama dengan dokumen pendukung berikut:
- `UI_UX_Specification_Per_Screen.md` — spesifikasi UI/UX detail per screen
- `SRS_Pseudo_SQL_Schema_Draft.md` — draft pseudo-SQL schema
- `SRS_Sample_API_Request_Response_Payload.md` — contoh request/response API
- `SRS_Acceptance_Criteria_Test_Scenarios.md` — acceptance criteria dan test scenarios


---

# 2. Overall Description

## 2.1 Product Perspective
Sistem adalah aplikasi web terpusat dengan modul utama berikut:
- Dashboard
- Project Management
- WBD Management
- Gantt View
- Progress Management
- Cost Management
- File Repository (Documents and Photos)
- S-Curve Analytics
- Cost Analysis
- Report Generation
- System Administration

Sistem menggunakan WBD sebagai sumber baseline utama untuk jadwal dan biaya, sementara Gantt menjadi tampilan turunan read-only dari WBD.

## 2.2 User Classes and Roles
Sistem memiliki 5 jenis user:
1. **Administrator Sistem**
2. **Project Manager**
3. **Direksi**
4. **Finance**
5. **Admin Proyek**

Seluruh jenis user memiliki hak baca terhadap semua project.

## 2.3 Access Model
Sistem harus memenuhi aturan berikut:
- Semua user terdaftar dapat melihat semua project.
- Tidak ada mapping user ke project.
- Akses edit, submit, review, dan approval diatur melalui role.
- Approval dipisahkan dari hak edit.

## 2.4 Assumptions and Constraints
- Sistem berbasis web.
- Hanya ada satu baseline aktif pada setiap project pada satu waktu.
- Gantt tidak dapat diedit langsung.
- Semua perubahan WBD final harus melalui approval Direksi.
- Actual cost wajib terkait ke satu progress entry.
- Report hanya dihasilkan sebagai output final, tanpa draft workflow.

---

# 3. Business Rules

## 3.1 Project Rules
1. Sistem harus mendukung multiple projects.
2. Setiap project wajib memiliki field `client_name`.
3. Semua user dapat melihat semua project.

## 3.2 WBD Rules
1. WBD harus mendukung struktur multi-level hierarchy.
2. WBD harus menggunakan versioning.
3. Setiap project hanya boleh memiliki satu baseline aktif.
4. WBD harus memiliki status final approved sebelum progress dapat diinput.
5. Semua perubahan terhadap WBD final harus melalui approval Direksi.
6. Perubahan volume dan tarif dianggap sebagai perubahan material terkait biaya.
7. Baseline cost merupakan bagian dari versi WBD.
8. Revisi WBD tidak langsung aktif sebelum Direksi menyetujui.
9. Selama revisi pending approval, baseline aktif sebelumnya tetap digunakan.

## 3.3 Progress Rules
1. Progress yang diinput oleh Admin Proyek harus di-approve oleh Project Manager.
2. Progress yang diinput oleh Project Manager tidak memerlukan approval tambahan.
3. Hanya progress berstatus approved atau auto-approved yang memengaruhi dashboard, analytics, dan report resmi.
4. Progress tetap boleh berjalan ketika revisi WBD sedang pending, selama baseline aktif masih tersedia.

## 3.4 Cost Rules
1. Actual cost disimpan sebagai transaksi terpisah.
2. Setiap actual cost wajib terkait tepat ke satu progress entry.
3. Admin Proyek boleh input biaya.
4. Finance boleh input baseline cost dan actual cost.
5. Project Manager tidak boleh input biaya.
6. Biaya yang diinput Admin Proyek harus direview oleh Finance sebelum dianggap resmi.
7. Review oleh Finance berarti final approval.
8. Lifecycle biaya adalah: Draft -> Review -> Approved / Rejected.

## 3.5 File Rules
1. Modul file harus mendukung upload dokumen dan foto.
2. File/foto dapat dikaitkan dengan WBD item.
3. File/foto dapat dikaitkan dengan progress entry.
4. Untuk file berjenis foto, caption wajib diisi.
5. Untuk file berjenis foto, tanggal foto wajib diisi.
6. Metadata foto lain bersifat optional.
7. Sistem harus menyediakan kategori file default berikut:
   - Kontrak
   - Berita Acara
   - Invoice
   - Foto Lapangan
   - Approval
   - Bukti Pembayaran
   - Lainnya
8. Kategori file harus dapat ditambah kemudian melalui master data.

## 3.6 Gantt Rules
1. Gantt harus bersifat read-only.
2. Gantt hanya boleh menampilkan data turunan dari WBD.
3. Perubahan jadwal hanya dapat dilakukan melalui perubahan WBD.

## 3.7 Report Rules
1. Report dihasilkan sebagai output final.
2. Report tidak memiliki status draft.

---

# 4. Role and Permission Matrix

## 4.1 Administrator Sistem
Hak akses:
- mengelola master data,
- mengelola konfigurasi sistem,
- mengelola role dan parameter sistem.

Batasan:
- tidak boleh mengelola data operasional proyek,
- tidak boleh mengelola WBD,
- tidak boleh menginput progress,
- tidak boleh menginput biaya,
- tidak boleh mengelola file proyek operasional.

## 4.2 Project Manager
Hak akses:
- melihat semua project,
- membuat dan mengelola WBD,
- membuat draft revisi WBD,
- mengelola progress,
- mengelola dokumen/foto,
- mengelola report,
- meng-approve progress dari Admin Proyek.

Batasan:
- tidak boleh input biaya.

## 4.3 Direksi
Hak akses:
- melihat semua project,
- meng-approve WBD,
- meng-approve revisi WBD.

## 4.4 Finance
Hak akses:
- melihat semua project,
- input baseline cost,
- input actual cost,
- review biaya,
- final approval biaya.

## 4.5 Admin Proyek
Hak akses:
- melihat semua project,
- membuat dan mengelola WBD,
- membuat draft revisi WBD,
- input progress,
- input biaya,
- mengelola dokumen/foto,
- mengelola report.

Batasan:
- progress yang diinput harus menunggu approval Project Manager,
- biaya yang diinput harus direview Finance untuk menjadi resmi.

---

# 5. Functional Requirements

## 5.1 Project Module
### 5.1.1 Project Master
Sistem harus menyediakan modul pengelolaan project.

Setiap project minimal memiliki atribut berikut:
- project_code
- project_name
- client_name
- location / estate
- start_date
- end_date
- status
- description

### 5.1.2 Project Listing
Sistem harus dapat menampilkan daftar project dengan informasi minimum:
- project_name
- client_name
- periode project
- status

---

## 5.2 WBD Module
### 5.2.1 WBD Structure
Sistem harus menyediakan WBD dengan struktur multi-level hierarchy.

Setiap WBD harus dapat menyimpan:
- group
- item
- sub-item bertingkat
- volume
- unit
- rate
- planned cost
- start date
- duration
- end date
- status
- notes

### 5.2.2 WBD Calculation
Sistem harus mendukung perhitungan otomatis berikut:
- total biaya = volume × tarif
- tanggal akhir = tanggal mulai + durasi - 1 hari
- subtotal grup
- total proyek
- persentase komponen
- persentase total

### 5.2.3 WBD Versioning
Sistem harus mendukung pembuatan versi WBD.

Setiap revisi atas WBD final harus menghasilkan versi baru dan tidak boleh menimpa baseline aktif secara langsung.

### 5.2.4 WBD Approval
Sistem harus memungkinkan Direksi melakukan approval atas WBD.

Sistem harus memastikan:
- progress tidak dapat diinput bila tidak ada baseline WBD final approved,
- revisi WBD final tidak langsung berlaku sebelum approval Direksi,
- hanya satu versi WBD yang berstatus aktif pada satu waktu.

---

## 5.3 Progress Module
### 5.3.1 Progress Entry
Sistem harus menyediakan form input progress per item pekerjaan.

Setiap progress entry minimal memiliki data berikut:
- project
- WBD item
- tanggal progress
- volume progress
- catatan
- input_by
- status approval

### 5.3.2 Progress Approval
Sistem harus menerapkan aturan approval sebagai berikut:
- progress dari Admin Proyek masuk ke status menunggu approval Project Manager,
- progress dari Project Manager langsung dianggap resmi,
- hanya progress approved/auto-approved yang memengaruhi data resmi.

---

## 5.4 Cost Module
### 5.4.1 Baseline Cost
Sistem harus memungkinkan input baseline cost sebagai bagian dari WBD version.

### 5.4.2 Actual Cost Transaction
Sistem harus menyimpan actual cost sebagai transaksi terpisah.

Setiap actual cost transaction wajib memiliki relasi ke tepat satu progress entry.

Setiap actual cost minimal memiliki:
- progress_entry_id
- amount
- input_date
- input_by
- notes
- status

### 5.4.3 Cost Review Workflow
Sistem harus menerapkan lifecycle biaya berikut:
- Draft
- Review
- Approved
- Rejected

Sistem harus memastikan biaya dari Admin Proyek tidak menjadi angka resmi sebelum disetujui Finance.

---

## 5.5 File Repository Module
### 5.5.1 File Upload
Sistem harus mendukung upload file berupa:
- dokumen
- foto

### 5.5.2 File Association
Sistem harus memungkinkan file dikaitkan ke:
- WBD item
- progress entry

### 5.5.3 Photo Metadata
Untuk file foto, sistem harus mewajibkan field berikut:
- caption
- photo_date

Field optional dapat mencakup:
- location / GPS
- uploader note
- tag tambahan

### 5.5.4 File Category
Sistem harus memiliki kategori file default yang dapat dikelola melalui master data.

---

## 5.6 Gantt Module
### 5.6.1 Gantt View
Sistem harus menyediakan tampilan Gantt yang berasal dari data WBD.

### 5.6.2 Gantt Limitation
Gantt bersifat read-only dan tidak boleh menjadi sumber perubahan jadwal.

---

## 5.7 Dashboard Module
Sistem harus menyediakan dashboard yang menampilkan data resmi proyek berdasarkan baseline aktif dan transaksi approved.

Dashboard minimal harus dapat menampilkan:
- total biaya proyek
- total progress
- item aktif
- item terlambat
- KPI utama
- ringkasan status proyek

---

## 5.8 S-Curve Module
Sistem harus menampilkan perbandingan plan vs actual secara kumulatif.

S-Curve minimal harus dapat menampilkan:
- progress volume plan vs actual
- progress biaya plan vs actual
- deviasi progress
- deviasi biaya

Data actual pada modul ini hanya boleh menggunakan progress approved dan biaya approved.

---

## 5.9 Cost Analysis Module
Sistem harus menampilkan analisis biaya minimum berupa:
- biaya baseline
- biaya actual
- deviasi biaya
- bobot grup
- progress berbobot
- indikator item/grup bermasalah

---

## 5.10 Report Module
Sistem harus dapat menghasilkan report final.

Report minimal harus mendukung:
- generate report,
- export report,
- menggunakan data resmi yang telah approved.

---

# 6. Workflow Summary

## 6.1 WBD Workflow
1. User membuat draft WBD.
2. WBD diajukan untuk approval Direksi.
3. Jika disetujui, WBD menjadi baseline aktif.
4. Jika ada perubahan, user membuat revisi versi baru.
5. Versi revisi menunggu approval Direksi.
6. Sampai disetujui, baseline aktif lama tetap dipakai.

## 6.2 Progress Workflow
1. Admin Proyek atau Project Manager membuat progress entry.
2. Jika dibuat Admin Proyek, status menunggu approval Project Manager.
3. Jika dibuat Project Manager, status langsung resmi.
4. Progress resmi masuk ke dashboard dan analytics.

## 6.3 Cost Workflow
1. Admin Proyek atau Finance membuat actual cost transaction.
2. Actual cost wajib terkait ke satu progress entry.
3. Jika biaya berasal dari Admin Proyek, biaya masuk proses review Finance.
4. Finance memberi keputusan Approved atau Rejected.
5. Hanya biaya approved yang dipakai sebagai angka resmi.

---

# 7. Non-Functional Requirements

## 7.1 General
- Sistem harus berbasis web.
- Sistem harus mendukung banyak project dalam satu instalasi.
- Sistem harus memiliki audit trail untuk approval dan perubahan data penting.

## 7.2 Security
- Sistem harus menerapkan autentikasi user.
- Sistem harus menerapkan otorisasi berbasis role.
- Sistem harus membatasi aksi sesuai role yang ditentukan.

## 7.3 Data Integrity
- Sistem harus menjaga konsistensi antara WBD, progress, actual cost, Gantt, dan analytics.
- Sistem tidak boleh mengizinkan actual cost tanpa progress entry terkait.
- Sistem tidak boleh mengizinkan progress bila tidak ada baseline aktif yang final approved.

---

# 8. Initial Out of Scope
Fitur berikut belum menjadi ruang lingkup wajib pada draft awal ini:
- user-project assignment restriction,
- editing Gantt secara langsung,
- report draft workflow,
- mobile native application,
- workflow approval dokumen,
- workflow approval report,
- advanced geotagging mandatory.

---

# 9. Data Model and Entity Relationships

## 9.1 Core Entities
Sistem minimal harus memiliki entitas berikut:

1. **User**
2. **Role**
3. **Project**
4. **WBD Version**
5. **WBD Node**
6. **Progress Entry**
7. **Actual Cost Transaction**
8. **Project File**
9. **File Category**
10. **Audit Log**
11. **Report Record**

## 9.2 Entity Description

### 9.2.1 User
Menyimpan data akun pengguna aplikasi.

Field minimum:
- id
- full_name
- email
- password_hash
- role_id
- is_active
- created_at
- updated_at

### 9.2.2 Role
Menyimpan master role sistem.

Field minimum:
- id
- role_name
- description

Nilai awal role:
- Administrator Sistem
- Project Manager
- Direksi
- Finance
- Admin Proyek

### 9.2.3 Project
Menyimpan data project utama.

Field minimum:
- id
- project_code
- project_name
- client_name
- location
- start_date
- end_date
- status
- description
- active_wbd_version_id
- created_by
- created_at
- updated_at

### 9.2.4 WBD Version
Menyimpan versi WBD pada satu project.

Field minimum:
- id
- project_id
- version_number
- status
- based_on_version_id
- submitted_by
- submitted_at
- approved_by
- approved_at
- rejected_by
- rejected_at
- rejection_reason
- is_active
- created_at
- updated_at

Keterangan:
- satu project dapat memiliki banyak versi WBD,
- hanya satu versi yang boleh aktif pada satu waktu,
- perubahan WBD final harus menghasilkan versi baru.

### 9.2.5 WBD Node
Menyimpan struktur WBD multi-level, baik group, item, maupun sub-item.

Field minimum:
- id
- wbd_version_id
- parent_node_id
- node_type
- code
- name
- description
- unit
- volume
- rate
- planned_cost
- component_percent
- total_percent
- start_date
- duration_days
- end_date
- status
- sort_order
- created_at
- updated_at

Keterangan:
- `parent_node_id` bernilai null untuk root/group level,
- `node_type` dapat membedakan group/header dan item operasional,
- struktur harus mendukung multi-level hierarchy.

### 9.2.6 Progress Entry
Menyimpan transaksi progress pekerjaan.

Field minimum:
- id
- project_id
- wbd_node_id
- progress_date
- progress_volume
- note
- entered_by
- status
- approved_by
- approved_at
- rejected_by
- rejected_at
- rejection_reason
- created_at
- updated_at

Keterangan:
- progress hanya boleh dibuat untuk node WBD yang relevan secara operasional,
- progress dari Admin Proyek harus melalui approval Project Manager,
- progress dari Project Manager dapat langsung berstatus resmi.

### 9.2.7 Actual Cost Transaction
Menyimpan transaksi biaya aktual.

Field minimum:
- id
- project_id
- progress_entry_id
- amount
- transaction_date
- description
- entered_by
- status
- reviewed_by
- reviewed_at
- rejected_by
- rejected_at
- rejection_reason
- created_at
- updated_at

Keterangan:
- setiap actual cost wajib terkait ke satu progress entry,
- biaya dari Admin Proyek harus direview Finance,
- biaya yang telah approved menjadi biaya resmi.

### 9.2.8 Project File
Menyimpan file dokumen dan foto proyek.

Field minimum:
- id
- project_id
- related_entity_type
- related_entity_id
- file_category_id
- file_type
- original_file_name
- storage_path
- mime_type
- caption
- photo_date
- note
- uploaded_by
- uploaded_at
- created_at
- updated_at

Keterangan:
- `related_entity_type` dapat bernilai `wbd_node` atau `progress_entry`,
- `related_entity_id` berisi ID entitas yang dirujuk,
- untuk file dokumen, `caption` dan `photo_date` boleh kosong,
- untuk file foto, `caption` dan `photo_date` wajib diisi.

### 9.2.9 File Category
Menyimpan master kategori file.

Field minimum:
- id
- category_name
- description
- is_active
- created_at
- updated_at

Nilai default:
- Kontrak
- Berita Acara
- Invoice
- Foto Lapangan
- Approval
- Bukti Pembayaran
- Lainnya

### 9.2.10 Audit Log
Menyimpan jejak perubahan dan approval data penting.

Field minimum:
- id
- entity_type
- entity_id
- action_type
- action_by
- action_at
- old_value_json
- new_value_json
- remarks

### 9.2.11 Report Record
Menyimpan histori hasil generate report final.

Field minimum:
- id
- project_id
- report_type
- period_start
- period_end
- file_path
- generated_by
- generated_at
- status

Keterangan:
- status report cukup merepresentasikan hasil final generate,
- report tidak memiliki workflow draft.

## 9.3 Entity Relationships

Relasi minimum antar entitas:
- satu **Role** memiliki banyak **User**,
- satu **Project** memiliki banyak **WBD Version**,
- satu **Project** memiliki satu `active_wbd_version_id`,
- satu **WBD Version** memiliki banyak **WBD Node**,
- satu **WBD Node** dapat memiliki banyak child **WBD Node**,
- satu **Project** memiliki banyak **Progress Entry**,
- satu **WBD Node** dapat memiliki banyak **Progress Entry**,
- satu **Progress Entry** dapat memiliki banyak **Actual Cost Transaction**,
- satu **Project** memiliki banyak **Project File**,
- satu **Project File** dapat terkait ke satu **WBD Node** atau satu **Progress Entry**,
- satu **Project** memiliki banyak **Report Record**,
- semua entitas penting dapat memiliki banyak **Audit Log**.

## 9.4 Suggested Integrity Rules
1. `Project.active_wbd_version_id` harus merujuk ke versi WBD dengan status final approved dan `is_active = true`.
2. Pada satu project, hanya satu WBD Version yang boleh `is_active = true`.
3. Progress Entry tidak boleh dibuat jika project belum memiliki active baseline WBD.
4. Actual Cost Transaction tidak boleh dibuat tanpa `progress_entry_id` yang valid.
5. Project File berjenis `image` harus memiliki `caption` dan `photo_date`.
6. WBD Node child harus memiliki `wbd_version_id` yang sama dengan parent-nya.
7. WBD Node yang menerima Progress Entry harus merupakan node operasional, bukan sekadar header/group dekoratif.

---

# 10. Lifecycle Status Definitions

## 10.1 WBD Version Lifecycle
Status minimum WBD Version:
- `DRAFT`
- `PENDING_DIRECTOR_APPROVAL`
- `FINAL_APPROVED`
- `REJECTED`
- `SUPERSEDED`

Deskripsi:
- **DRAFT**: versi sedang disusun atau direvisi.
- **PENDING_DIRECTOR_APPROVAL**: versi sudah diajukan dan menunggu keputusan Direksi.
- **FINAL_APPROVED**: versi disetujui Direksi dan dapat menjadi baseline aktif.
- **REJECTED**: versi ditolak Direksi.
- **SUPERSEDED**: versi lama yang sudah tidak aktif karena digantikan baseline baru.

Aturan:
1. Hanya status `FINAL_APPROVED` yang boleh menjadi baseline aktif.
2. Saat versi baru disetujui, versi aktif sebelumnya harus berubah menjadi `SUPERSEDED`.
3. Versi `REJECTED` tidak boleh menjadi aktif.

## 10.2 Progress Entry Lifecycle
Status minimum Progress Entry:
- `DRAFT`
- `PENDING_PM_APPROVAL`
- `AUTO_APPROVED`
- `APPROVED`
- `REJECTED`

Deskripsi:
- **DRAFT**: progress masih disiapkan dan belum diajukan.
- **PENDING_PM_APPROVAL**: progress dari Admin Proyek menunggu approval Project Manager.
- **AUTO_APPROVED**: progress diinput langsung oleh Project Manager dan langsung resmi.
- **APPROVED**: progress dari Admin Proyek telah disetujui Project Manager.
- **REJECTED**: progress ditolak Project Manager.

Aturan:
1. Progress yang dibuat Admin Proyek tidak boleh langsung memengaruhi analytics resmi.
2. Hanya `APPROVED` dan `AUTO_APPROVED` yang menjadi data resmi.
3. Progress `REJECTED` harus tetap tersimpan untuk audit trail.

## 10.3 Actual Cost Transaction Lifecycle
Status minimum Actual Cost Transaction:
- `DRAFT`
- `REVIEW`
- `APPROVED`
- `REJECTED`

Deskripsi:
- **DRAFT**: transaksi biaya sedang disiapkan.
- **REVIEW**: transaksi biaya menunggu review Finance.
- **APPROVED**: transaksi biaya telah disetujui Finance dan menjadi angka resmi.
- **REJECTED**: transaksi biaya ditolak Finance.

Aturan:
1. Hanya transaksi biaya `APPROVED` yang memengaruhi cost analysis dan report resmi.
2. Actual cost dari Admin Proyek harus masuk ke `REVIEW`.
3. Actual cost dari Finance dapat mengikuti aturan bisnis implementasi, namun tetap harus berstatus resmi sebelum dipakai dalam analytics final.

## 10.4 Project File Lifecycle
Status file pada tahap awal bersifat sederhana dan minimal dapat menggunakan:
- `ACTIVE`
- `ARCHIVED`

Deskripsi:
- **ACTIVE**: file aktif dan tersedia untuk digunakan.
- **ARCHIVED**: file tidak lagi digunakan aktif tetapi tetap disimpan.

## 10.5 Report Record Lifecycle
Status report pada tahap awal dapat menggunakan:
- `FINAL`
- `DELETED`

Deskripsi:
- **FINAL**: report berhasil digenerate dan menjadi output final.
- **DELETED**: record report dihapus secara logis bila dibutuhkan kebijakan arsip.

---

# 11. Use Case Summary by Role

## 11.1 Administrator Sistem
- mengelola role
- mengelola master kategori file
- mengelola konfigurasi sistem
- mengelola parameter master lain yang dibutuhkan aplikasi

## 11.2 Project Manager
- membuat WBD draft
- merevisi WBD
- mengajukan WBD untuk approval
- menginput progress
- meng-approve progress Admin Proyek
- mengelola file proyek
- menghasilkan report final

## 11.3 Direksi
- meninjau WBD
- menyetujui atau menolak WBD
- menyetujui atau menolak revisi WBD

## 11.4 Finance
- menginput baseline cost sesuai kebijakan sistem
- menginput actual cost
- mereview actual cost
- menyetujui atau menolak actual cost

## 11.5 Admin Proyek
- membuat WBD draft
- merevisi WBD
- menginput progress
- menginput actual cost
- mengelola file proyek
- menghasilkan report final

---

# 12. Field-Level Specification

## 12.1 User

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key user | System generated |
| full_name | string | Yes | Nama lengkap user | 3-150 chars |
| email | string | Yes | Email login user | Unique, valid email format |
| password_hash | string | Yes | Password terenkripsi | Tidak disimpan dalam plain text |
| role_id | FK | Yes | Relasi ke role | Harus valid ke tabel role |
| is_active | boolean | Yes | Status aktif user | Default true |
| created_at | datetime | Yes | Waktu pembuatan data | System generated |
| updated_at | datetime | Yes | Waktu perubahan data | System generated |

## 12.2 Role

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key role | System generated |
| role_name | string | Yes | Nama role | Unique |
| description | string | No | Deskripsi role | Optional |

## 12.3 Project

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key project | System generated |
| project_code | string | Yes | Kode unik project | Unique |
| project_name | string | Yes | Nama project | 3-200 chars |
| client_name | string | Yes | Nama client project | 2-200 chars |
| location | string | Yes | Lokasi / estate / kebun | 2-200 chars |
| start_date | date | Yes | Tanggal mulai project | Must be <= end_date |
| end_date | date | Yes | Tanggal akhir project | Must be >= start_date |
| status | enum | Yes | Status project | See master status project |
| description | text | No | Deskripsi project | Optional |
| active_wbd_version_id | FK | No | WBD aktif saat ini | Nullable saat project baru belum ada baseline |
| created_by | FK | Yes | User pembuat | Valid user |
| created_at | datetime | Yes | Waktu pembuatan | System generated |
| updated_at | datetime | Yes | Waktu perubahan | System generated |

## 12.4 WBD Version

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key WBD version | System generated |
| project_id | FK | Yes | Relasi ke project | Valid project |
| version_number | integer / string | Yes | Nomor versi WBD | Unique per project |
| status | enum | Yes | Status lifecycle WBD | See WBD lifecycle |
| based_on_version_id | FK | No | Referensi versi asal | Null untuk versi pertama |
| submitted_by | FK | No | Pengaju approval | Required saat submit |
| submitted_at | datetime | No | Waktu submit | Required saat submit |
| approved_by | FK | No | Approver Direksi | Required jika approved |
| approved_at | datetime | No | Waktu approval | Required jika approved |
| rejected_by | FK | No | User penolak | Required jika rejected |
| rejected_at | datetime | No | Waktu reject | Required jika rejected |
| rejection_reason | text | No | Alasan penolakan | Recommended if rejected |
| is_active | boolean | Yes | Penanda baseline aktif | Hanya satu per project |
| created_at | datetime | Yes | Waktu pembuatan | System generated |
| updated_at | datetime | Yes | Waktu perubahan | System generated |

## 12.5 WBD Node

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key WBD node | System generated |
| wbd_version_id | FK | Yes | Relasi ke versi WBD | Valid WBD version |
| parent_node_id | FK | No | Parent node | Null untuk node root/group |
| node_type | enum | Yes | Jenis node | GROUP / ITEM |
| code | string | Yes | Kode node | Unique within version or parent scope |
| name | string | Yes | Nama node / pekerjaan | 2-255 chars |
| description | text | No | Deskripsi node | Optional |
| unit | string | No* | Satuan pekerjaan | Mandatory untuk ITEM operasional |
| volume | decimal | No* | Volume rencana | Mandatory untuk ITEM operasional, >= 0 |
| rate | decimal | No* | Tarif rencana | Mandatory untuk ITEM operasional, >= 0 |
| planned_cost | decimal | Yes | Nilai biaya rencana | Derived from volume x rate for item |
| component_percent | decimal | No | Persentase terhadap subtotal parent/grup | Calculated field |
| total_percent | decimal | No | Persentase terhadap total proyek | Calculated field |
| start_date | date | No* | Tanggal mulai | Mandatory untuk node operasional |
| duration_days | integer | No* | Durasi | Mandatory untuk node operasional, >= 1 |
| end_date | date | Yes | Tanggal akhir | Calculated or derived |
| status | enum | Yes | Status pekerjaan | See node status master |
| sort_order | integer | Yes | Urutan tampilan | >= 0 |
| created_at | datetime | Yes | Waktu pembuatan | System generated |
| updated_at | datetime | Yes | Waktu perubahan | System generated |

Catatan:
- Field bertanda `No*` menjadi mandatory untuk node ITEM yang dapat dieksekusi.
- Untuk node GROUP/header, volume, rate, dan unit dapat kosong.

## 12.6 Progress Entry

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key progress entry | System generated |
| project_id | FK | Yes | Relasi ke project | Valid project |
| wbd_node_id | FK | Yes | Relasi ke node WBD | Harus node operasional |
| progress_date | date | Yes | Tanggal progress | Must be within project period unless override policy |
| progress_volume | decimal | Yes | Volume progress yang diinput | > 0 |
| note | text | No | Catatan progress | Optional |
| entered_by | FK | Yes | User input | Valid user |
| status | enum | Yes | Status progress | See progress lifecycle |
| approved_by | FK | No | PM approver | Required for APPROVED |
| approved_at | datetime | No | Waktu approval | Required for APPROVED |
| rejected_by | FK | No | PM rejector | Required for REJECTED |
| rejected_at | datetime | No | Waktu reject | Required for REJECTED |
| rejection_reason | text | No | Alasan reject | Recommended if rejected |
| created_at | datetime | Yes | Waktu pembuatan | System generated |
| updated_at | datetime | Yes | Waktu perubahan | System generated |

## 12.7 Actual Cost Transaction

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key cost transaction | System generated |
| project_id | FK | Yes | Relasi ke project | Valid project |
| progress_entry_id | FK | Yes | Relasi ke progress entry | Wajib valid |
| amount | decimal | Yes | Nilai biaya aktual | > 0 |
| transaction_date | date | Yes | Tanggal transaksi biaya | Required |
| description | text | No | Deskripsi biaya | Optional |
| entered_by | FK | Yes | User input biaya | Valid user |
| status | enum | Yes | Status biaya | See cost lifecycle |
| reviewed_by | FK | No | Finance reviewer | Required if approved/rejected |
| reviewed_at | datetime | No | Waktu review | Required if approved/rejected |
| rejected_by | FK | No | Finance rejector | Optional if same as reviewed_by model not reused |
| rejected_at | datetime | No | Waktu reject | Required if rejected |
| rejection_reason | text | No | Alasan reject | Recommended if rejected |
| created_at | datetime | Yes | Waktu pembuatan | System generated |
| updated_at | datetime | Yes | Waktu perubahan | System generated |

## 12.8 Project File

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key file | System generated |
| project_id | FK | Yes | Relasi ke project | Valid project |
| related_entity_type | enum | No | Entitas terkait | WBD_NODE / PROGRESS_ENTRY |
| related_entity_id | FK / generic id | No | ID entitas terkait | Mandatory jika related_entity_type diisi |
| file_category_id | FK | Yes | Kategori file | Valid category |
| file_type | enum | Yes | Jenis file | DOCUMENT / IMAGE |
| original_file_name | string | Yes | Nama file asli | Required |
| storage_path | string | Yes | Lokasi file di storage | Required |
| mime_type | string | Yes | MIME type file | Required |
| caption | string | No* | Caption file/foto | Mandatory untuk IMAGE |
| photo_date | date | No* | Tanggal pengambilan foto | Mandatory untuk IMAGE |
| note | text | No | Catatan uploader | Optional |
| uploaded_by | FK | Yes | User uploader | Valid user |
| uploaded_at | datetime | Yes | Waktu upload | System generated |
| created_at | datetime | Yes | Waktu pembuatan record | System generated |
| updated_at | datetime | Yes | Waktu perubahan record | System generated |

## 12.9 File Category

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key category | System generated |
| category_name | string | Yes | Nama kategori | Unique |
| description | string | No | Deskripsi kategori | Optional |
| is_active | boolean | Yes | Status aktif | Default true |
| created_at | datetime | Yes | Waktu pembuatan | System generated |
| updated_at | datetime | Yes | Waktu perubahan | System generated |

## 12.10 Audit Log

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key audit log | System generated |
| entity_type | string | Yes | Jenis entitas | Required |
| entity_id | string / FK-like | Yes | ID entitas | Required |
| action_type | string | Yes | Jenis aksi | CREATE / UPDATE / APPROVE / REJECT / DELETE / ARCHIVE |
| action_by | FK | Yes | User pelaku aksi | Valid user |
| action_at | datetime | Yes | Waktu aksi | System generated |
| old_value_json | json | No | Snapshot lama | Optional |
| new_value_json | json | No | Snapshot baru | Optional |
| remarks | text | No | Keterangan audit | Optional |

## 12.11 Report Record

| Field | Type | Mandatory | Description | Validation / Notes |
|---|---|---:|---|---|
| id | UUID / bigint | Yes | Primary key report record | System generated |
| project_id | FK | Yes | Relasi ke project | Valid project |
| report_type | enum/string | Yes | Jenis report | Weekly / Monthly / Cost / etc |
| period_start | date | Yes | Awal periode report | Required |
| period_end | date | Yes | Akhir periode report | Must be >= period_start |
| file_path | string | Yes | Lokasi hasil generate | Required |
| generated_by | FK | Yes | User generator | Valid user |
| generated_at | datetime | Yes | Waktu generate | System generated |
| status | enum | Yes | Status report | FINAL / DELETED |

---

# 13. Role-Permission Matrix

Legend:
- **V** = View
- **C** = Create / Input
- **E** = Edit / Manage
- **S** = Submit for review / approval
- **A** = Approve / Final review
- **N** = Not allowed

| Module / Action | Administrator Sistem | Project Manager | Direksi | Finance | Admin Proyek |
|---|---:|---:|---:|---:|---:|
| View all projects | V | V | V | V | V |
| Create/edit project master | N* | N | N | N | N |
| Manage system master data | E | N | N | N | N |
| Manage system configuration | E | N | N | N | N |
| View WBD | V | V | V | V | V |
| Create WBD draft | N | C/E | N | N | C/E |
| Edit WBD draft | N | C/E | N | N | C/E |
| Submit WBD for Direksi approval | N | S | N | N | S |
| Approve / reject WBD | N | N | A | N | N |
| Create WBD revision | N | C/E | N | N | C/E |
| View Gantt | V | V | V | V | V |
| Edit Gantt directly | N | N | N | N | N |
| View progress | V | V | V | V | V |
| Create progress entry | N | C | N | N | C |
| Edit draft/pending own progress** | N | E | N | N | E |
| Approve / reject progress | N | A | N | N | N |
| View cost transactions | V | V | V | V | V |
| Input baseline cost*** | N | N | N | C/E | N |
| Input actual cost | N | N | N | C/E | C |
| Review / approve actual cost | N | N | N | A | N |
| View files | V | V | V | V | V |
| Upload documents/photos | N | C/E | N | N | C/E |
| Manage file categories | E | N | N | N | N |
| View dashboard | V | V | V | V | V |
| View S-Curve | V | V | V | V | V |
| View cost analysis | V | V | V | V | V |
| Generate final report | N | C | N | N | C |
| View generated reports | V | V | V | V | V |
| Manage users / roles | E | N | N | N | N |

Catatan:
- `N*`: Project master pada draft saat ini diasumsikan bukan data operasional yang diedit oleh Administrator Sistem kecuali bila bisnis nanti ingin mengklasifikasikan project master sebagai master data. Saat ini keputusan aman adalah Administrator Sistem fokus ke master sistem, bukan master proyek operasional.
- `**`: Implementasi detail edit progress setelah status tertentu perlu mengikuti audit policy. Minimal, progress yang sudah APPROVED/AUTO_APPROVED tidak boleh diubah langsung tanpa mekanisme koreksi baru.
- `***`: Karena baseline cost adalah bagian dari WBD version, input baseline cost oleh Finance harus tetap tunduk pada versioning dan approval Direksi.

---

# 14. Detailed Functional Requirements by Module and Screen

## 14.1 Project Module

### 14.1.1 Project List Screen
Sistem harus menyediakan layar daftar project yang menampilkan minimal:
- project code,
- project name,
- client name,
- location,
- start date,
- end date,
- status,
- active baseline version.

Sistem harus mendukung:
- pencarian project,
- filter berdasarkan status,
- sort berdasarkan nama atau periode.

### 14.1.2 Project Detail Screen
Sistem harus menyediakan layar detail project yang menampilkan ringkasan project dan akses ke seluruh modul terkait project.

Sistem harus menampilkan minimal:
- informasi dasar project,
- status baseline aktif,
- KPI ringkas,
- shortcut ke WBD, Gantt, Progress, Cost, Files, dan Report.

## 14.2 WBD Module

### 14.2.1 WBD Tree/Grid Screen
Sistem harus menyediakan layar WBD berbentuk tree table multi-level.

Sistem harus menampilkan minimal kolom:
- code,
- name,
- unit,
- volume,
- rate,
- planned cost,
- component percent,
- total percent,
- start date,
- duration,
- end date,
- status,
- actions.

Sistem harus mendukung:
- expand/collapse hierarchy,
- tambah node root/group,
- tambah item/sub-item,
- edit node,
- urutkan node sesuai sort order,
- hitung subtotal dan total otomatis,
- validasi field wajib item operasional,
- tampilkan versi WBD yang sedang dibuka.

### 14.2.2 WBD Version Screen
Sistem harus menyediakan layar atau panel yang menampilkan daftar versi WBD untuk satu project.

Sistem harus menampilkan:
- version number,
- status,
- submitter,
- submission date,
- approver,
- approval date,
- baseline aktif / tidak.

Sistem harus mendukung:
- membuat draft versi baru dari baseline aktif,
- submit versi ke Direksi,
- melihat alasan reject,
- menandai baseline aktif.

### 14.2.3 WBD Approval Screen (Direksi)
Sistem harus menyediakan layar review WBD untuk Direksi.

Sistem harus memungkinkan Direksi:
- melihat ringkasan perubahan versi,
- melihat struktur WBD yang diajukan,
- approve versi,
- reject versi dengan alasan.

### 14.2.4 WBD Business Logic
Sistem harus menerapkan logika berikut:
- end date dihitung dari start date dan duration,
- planned cost item dihitung dari volume × rate,
- subtotal parent/grup dihitung dari child,
- total project dihitung dari seluruh node relevan,
- baseline cost perubahan mengikuti versioning,
- WBD final approved tidak boleh diedit langsung.

## 14.3 Gantt Module

### 14.3.1 Gantt Screen
Sistem harus menyediakan layar Gantt read-only berdasarkan baseline WBD aktif.

Sistem harus menampilkan:
- daftar pekerjaan di sisi kiri,
- skala waktu di sisi kanan,
- bar jadwal berdasarkan start date dan end date,
- indikator status progress,
- filter grup/status bila dibutuhkan.

Sistem tidak boleh mengizinkan:
- drag-and-drop pengubahan jadwal langsung,
- edit langsung pada bar Gantt.

Sistem harus memperbarui tampilan Gantt saat baseline aktif berubah.

## 14.4 Progress Module

### 14.4.1 Progress List Screen
Sistem harus menyediakan layar daftar progress per project.

Sistem harus menampilkan minimal:
- item pekerjaan,
- grup / parent WBD,
- progress date,
- progress volume,
- entered by,
- status,
- approval info,
- related actual cost summary.

Sistem harus mendukung:
- filter berdasarkan status,
- filter berdasarkan periode,
- filter berdasarkan grup atau item,
- pencarian item,
- drill-down ke detail progress.

### 14.4.2 Progress Entry Form Screen
Sistem harus menyediakan form input progress.

Field minimum yang harus tersedia:
- project,
- WBD item,
- progress date,
- progress volume,
- note,
- attachment upload optional.

Sistem harus memvalidasi:
- baseline aktif harus tersedia,
- item harus merupakan node operasional,
- user harus punya hak input,
- progress date valid,
- progress volume > 0.

### 14.4.3 Progress Approval Screen
Sistem harus menyediakan layar review progress untuk Project Manager.

Sistem harus memungkinkan Project Manager:
- melihat progress pending,
- melihat detail input Admin Proyek,
- approve progress,
- reject progress dengan alasan.

### 14.4.4 Progress Business Logic
Sistem harus memastikan:
- progress Admin Proyek masuk ke `PENDING_PM_APPROVAL`,
- progress Project Manager masuk ke `AUTO_APPROVED`,
- hanya progress resmi yang dipakai dalam analytics,
- progress approved tidak boleh diedit langsung; koreksi harus melalui entry baru atau workflow koreksi yang terdokumentasi.

## 14.5 Cost Module

### 14.5.1 Cost Transaction List Screen
Sistem harus menyediakan layar daftar actual cost transaction.

Sistem harus menampilkan minimal:
- progress reference,
- related WBD item,
- transaction date,
- amount,
- entered by,
- status,
- reviewed by,
- review date.

Sistem harus mendukung:
- filter status,
- filter periode,
- filter item/progress,
- pencarian transaksi.

### 14.5.2 Actual Cost Entry Form Screen
Sistem harus menyediakan form input biaya aktual.

Field minimum:
- progress entry,
- transaction date,
- amount,
- description.

Sistem harus memvalidasi:
- progress entry harus valid,
- amount > 0,
- transaction date wajib,
- user memiliki hak input biaya.

### 14.5.3 Cost Review Screen (Finance)
Sistem harus menyediakan layar review biaya untuk Finance.

Sistem harus memungkinkan Finance:
- melihat transaksi pada status REVIEW,
- melihat relasi ke progress,
- melihat konteks item WBD,
- approve biaya,
- reject biaya dengan alasan.

### 14.5.4 Cost Business Logic
Sistem harus memastikan:
- setiap actual cost terkait ke tepat satu progress entry,
- biaya dari Admin Proyek harus masuk status REVIEW,
- biaya APPROVED menjadi angka resmi,
- biaya REJECTED tetap tersimpan untuk audit,
- baseline cost edit oleh Finance harus melalui revisi WBD version.

## 14.6 File Repository Module

### 14.6.1 File List Screen
Sistem harus menyediakan layar daftar file proyek.

Sistem harus menampilkan minimal:
- file name,
- file type,
- category,
- related entity,
- uploaded by,
- uploaded at,
- caption untuk foto bila ada,
- photo date untuk foto bila ada.

Sistem harus mendukung:
- filter berdasarkan category,
- filter berdasarkan file type,
- filter berdasarkan WBD item / progress,
- search by file name,
- preview image,
- download document.

### 14.6.2 File Upload Form Screen
Sistem harus menyediakan form upload file.

Field minimum:
- file upload,
- category,
- file type,
- related entity type optional,
- related entity optional,
- caption,
- photo date,
- note.

Sistem harus memvalidasi:
- category wajib,
- file wajib,
- jika file_type = IMAGE maka caption dan photo_date wajib,
- jika related_entity_type diisi maka related_entity_id wajib.

### 14.6.3 File Category Master Screen
Sistem harus menyediakan master kategori file yang hanya dapat dikelola oleh Administrator Sistem.

Sistem harus mendukung:
- tambah kategori,
- ubah kategori,
- aktif/nonaktif kategori.

## 14.7 Dashboard Module

### 14.7.1 Dashboard Screen
Sistem harus menyediakan dashboard project dengan data resmi.

Dashboard minimal harus menampilkan:
- total baseline cost,
- total actual cost approved,
- total progress resmi,
- item aktif,
- item terlambat,
- deviasi utama,
- status baseline aktif.

Sistem harus mendukung filter minimal:
- project,
- periode,
- grup pekerjaan bila diperlukan.

## 14.8 S-Curve Module

### 14.8.1 S-Curve Screen
Sistem harus menyediakan layar S-Curve dengan perbandingan plan vs actual.

Sistem harus menampilkan minimal:
- cumulative plan volume,
- cumulative actual volume,
- cumulative plan cost,
- cumulative actual cost,
- deviasi progress,
- deviasi biaya.

Sistem hanya boleh memakai:
- progress APPROVED / AUTO_APPROVED,
- actual cost APPROVED,
- baseline aktif.

## 14.9 Cost Analysis Module

### 14.9.1 Cost Analysis Screen
Sistem harus menyediakan layar analisis biaya dan bobot.

Sistem harus menampilkan minimal:
- baseline cost per grup/item,
- actual cost approved,
- cost deviation,
- weighted progress,
- status kesehatan item/grup.

Sistem harus mendukung filter:
- project,
- periode,
- grup,
- status.

## 14.10 Report Module

### 14.10.1 Report Generation Screen
Sistem harus menyediakan layar generate report final.

Sistem harus memungkinkan user berhak untuk:
- memilih tipe report,
- memilih periode,
- memilih project,
- memilih filter pendukung bila ada,
- generate file report,
- mengunduh hasil report final.

### 14.10.2 Report History Screen
Sistem harus menyediakan daftar histori report yang telah digenerate.

Sistem harus menampilkan minimal:
- report type,
- project,
- period,
- generated by,
- generated at,
- status,
- file output.

## 14.11 System Administration Module

### 14.11.1 User and Role Administration Screen
Sistem harus menyediakan layar pengelolaan user dan role untuk Administrator Sistem.

Sistem harus mendukung:
- tambah user,
- aktif/nonaktif user,
- ubah role user,
- melihat daftar user.

### 14.11.2 Master Data and Configuration Screen
Sistem harus menyediakan layar pengelolaan master data dan konfigurasi.

Minimum meliputi:
- file categories,
- status master yang diperlukan,
- parameter konfigurasi sistem.

---

# 15. ERD and Database Schema Draft

## 15.1 Conceptual ERD Summary
Relasi inti sistem adalah sebagai berikut:

- **Role** 1 --- N **User**
- **Project** 1 --- N **WBD Version**
- **Project** 1 --- 0..1 **Active WBD Version**
- **WBD Version** 1 --- N **WBD Node**
- **WBD Node** 1 --- N **WBD Node** (self hierarchy)
- **Project** 1 --- N **Progress Entry**
- **WBD Node** 1 --- N **Progress Entry**
- **Progress Entry** 1 --- N **Actual Cost Transaction**
- **Project** 1 --- N **Project File**
- **File Category** 1 --- N **Project File**
- **Project File** N --- 0..1 **WBD Node**
- **Project File** N --- 0..1 **Progress Entry**
- **Project** 1 --- N **Report Record**
- Semua entitas utama --- N **Audit Log**

## 15.2 Recommended Relational Tables

### 15.2.1 `roles`
Suggested columns:
- id (PK)
- role_name (unique)
- description
- created_at
- updated_at

### 15.2.2 `users`
Suggested columns:
- id (PK)
- role_id (FK -> roles.id)
- full_name
- email (unique)
- password_hash
- is_active
- created_at
- updated_at

### 15.2.3 `projects`
Suggested columns:
- id (PK)
- project_code (unique)
- project_name
- client_name
- location
- start_date
- end_date
- status
- description
- active_wbd_version_id (nullable FK -> wbd_versions.id)
- created_by (FK -> users.id)
- created_at
- updated_at

Constraint notes:
- `active_wbd_version_id` harus mengarah ke versi WBD milik project yang sama.
- `start_date <= end_date`.

### 15.2.4 `wbd_versions`
Suggested columns:
- id (PK)
- project_id (FK -> projects.id)
- version_number
- status
- based_on_version_id (nullable FK -> wbd_versions.id)
- submitted_by (nullable FK -> users.id)
- submitted_at
- approved_by (nullable FK -> users.id)
- approved_at
- rejected_by (nullable FK -> users.id)
- rejected_at
- rejection_reason
- is_active
- created_at
- updated_at

Recommended constraints:
- unique(project_id, version_number)
- partial unique index for `(project_id)` where `is_active = true`
- if `is_active = true`, `status` must be `FINAL_APPROVED`

### 15.2.5 `wbd_nodes`
Suggested columns:
- id (PK)
- wbd_version_id (FK -> wbd_versions.id)
- parent_node_id (nullable FK -> wbd_nodes.id)
- node_type
- code
- name
- description
- unit
- volume
- rate
- planned_cost
- component_percent
- total_percent
- start_date
- duration_days
- end_date
- status
- sort_order
- created_at
- updated_at

Recommended constraints:
- unique(wbd_version_id, code)
- parent node must belong to the same `wbd_version_id`
- `duration_days >= 1` for operasional item
- `volume >= 0`
- `rate >= 0`
- `planned_cost >= 0`

Implementation note:
- untuk mendukung multi-level hierarchy yang efisien, implementasi dapat memakai adjacency list (`parent_node_id`) pada tahap awal.
- bila performa tree traversal menjadi isu, dapat dipertimbangkan nested set, materialized path, atau closure table pada fase lanjutan.

### 15.2.6 `progress_entries`
Suggested columns:
- id (PK)
- project_id (FK -> projects.id)
- wbd_node_id (FK -> wbd_nodes.id)
- progress_date
- progress_volume
- note
- entered_by (FK -> users.id)
- status
- approved_by (nullable FK -> users.id)
- approved_at
- rejected_by (nullable FK -> users.id)
- rejected_at
- rejection_reason
- created_at
- updated_at

Recommended constraints:
- `progress_volume > 0`
- `progress_date` required
- `wbd_node_id` must belong to the active baseline or recognized baseline context used at time of entry

Important design note:
- karena revisi WBD menggunakan versioning, sistem implementasi perlu memutuskan apakah progress selalu mengarah ke `wbd_node_id` versi aktif saat transaksi dibuat, atau menggunakan stable business key tambahan. Untuk tahap awal, disarankan tetap memakai `wbd_node_id` aktual dari baseline aktif ketika progress dibuat, dan menyimpan audit yang cukup.

### 15.2.7 `actual_cost_transactions`
Suggested columns:
- id (PK)
- project_id (FK -> projects.id)
- progress_entry_id (FK -> progress_entries.id)
- amount
- transaction_date
- description
- entered_by (FK -> users.id)
- status
- reviewed_by (nullable FK -> users.id)
- reviewed_at
- rejected_by (nullable FK -> users.id)
- rejected_at
- rejection_reason
- created_at
- updated_at

Recommended constraints:
- `amount > 0`
- `progress_entry_id` mandatory
- `transaction_date` mandatory

### 15.2.8 `file_categories`
Suggested columns:
- id (PK)
- category_name (unique)
- description
- is_active
- created_at
- updated_at

Seed data:
- Kontrak
- Berita Acara
- Invoice
- Foto Lapangan
- Approval
- Bukti Pembayaran
- Lainnya

### 15.2.9 `project_files`
Suggested columns:
- id (PK)
- project_id (FK -> projects.id)
- related_entity_type
- related_entity_id
- file_category_id (FK -> file_categories.id)
- file_type
- original_file_name
- storage_path
- mime_type
- caption
- photo_date
- note
- uploaded_by (FK -> users.id)
- uploaded_at
- created_at
- updated_at

Recommended constraints:
- if `file_type = IMAGE` then `caption` is not null and `photo_date` is not null
- if `related_entity_type` is not null then `related_entity_id` is not null

Implementation note:
- karena `related_entity_id` bersifat polymorphic, validasi referensial penuh biasanya ditangani di application layer kecuali dipisah ke tabel junction terpisah.

### 15.2.10 `report_records`
Suggested columns:
- id (PK)
- project_id (FK -> projects.id)
- report_type
- period_start
- period_end
- file_path
- generated_by (FK -> users.id)
- generated_at
- status

Recommended constraints:
- `period_end >= period_start`
- status limited to FINAL / DELETED

### 15.2.11 `audit_logs`
Suggested columns:
- id (PK)
- entity_type
- entity_id
- action_type
- action_by (FK -> users.id)
- action_at
- old_value_json
- new_value_json
- remarks

Recommended indexing:
- index(entity_type, entity_id)
- index(action_by)
- index(action_at)

## 15.3 Recommended Indexing Strategy
Minimal index recommendation:

### `projects`
- unique index on `project_code`
- index on `status`
- index on `client_name`

### `wbd_versions`
- unique index on `(project_id, version_number)`
- index on `(project_id, status)`
- partial unique index on active version per project

### `wbd_nodes`
- unique index on `(wbd_version_id, code)`
- index on `parent_node_id`
- index on `(wbd_version_id, node_type)`
- index on `(wbd_version_id, sort_order)`

### `progress_entries`
- index on `project_id`
- index on `wbd_node_id`
- index on `progress_date`
- index on `status`
- composite index on `(project_id, status, progress_date)`

### `actual_cost_transactions`
- index on `project_id`
- index on `progress_entry_id`
- index on `transaction_date`
- index on `status`

### `project_files`
- index on `project_id`
- index on `file_category_id`
- index on `file_type`
- composite index on `(related_entity_type, related_entity_id)`

### `report_records`
- index on `project_id`
- index on `(project_id, report_type)`
- index on `(period_start, period_end)`

## 15.4 Recommended Database-Level Checks
Berikut check constraints yang ideal bila DB mendukung:

1. `projects.end_date >= projects.start_date`
2. `wbd_versions.is_active = true` hanya jika `status = 'FINAL_APPROVED'`
3. `wbd_nodes.duration_days >= 1` untuk item operasional
4. `wbd_nodes.volume >= 0`
5. `wbd_nodes.rate >= 0`
6. `wbd_nodes.planned_cost >= 0`
7. `progress_entries.progress_volume > 0`
8. `actual_cost_transactions.amount > 0`
9. `project_files.file_type = 'IMAGE'` mewajibkan `caption` dan `photo_date`
10. `report_records.period_end >= period_start`

## 15.5 Schema Design Notes and Trade-Offs

### 15.5.1 WBD Node Versioning Trade-Off
Karena WBD menggunakan versioning, setiap revisi besar kemungkinan akan menghasilkan salinan struktur node baru. Konsekuensinya:
- analytics historis harus jelas memakai baseline mana,
- relasi progress ke node perlu dijaga agar tidak ambigu.

Saran implementasi tahap awal:
- gunakan `wbd_node_id` pada saat progress dibuat,
- simpan audit trail yang kuat,
- pertimbangkan tambahan `business_code` stabil bila nanti dibutuhkan pelacakan lintas versi.

### 15.5.2 Polymorphic File Association Trade-Off
Relasi file ke WBD Node atau Progress Entry lebih fleksibel bila memakai pasangan:
- `related_entity_type`
- `related_entity_id`

Kelebihan:
- sederhana dan fleksibel.

Kekurangan:
- foreign key penuh sulit ditegakkan di database.

Alternatif fase lanjutan:
- pisahkan menjadi tabel junction seperti `project_file_wbd_nodes` dan `project_file_progress_entries`.

### 15.5.3 Cost and Progress Separation Trade-Off
Memisahkan actual cost dari progress entry memberi keuntungan:
- approval cost dan progress dapat berjalan independen,
- audit lebih jelas,
- satu progress bisa punya banyak transaksi biaya.

Namun konsekuensinya:
- dashboard dan analytics harus tegas hanya memakai transaksi APPROVED,
- query agregasi menjadi sedikit lebih kompleks.

---

# 16. API and Service Contract Draft

## 16.1 API Design Principles
Disarankan API mengikuti prinsip berikut:
- RESTful atau service-oriented API yang konsisten
- semua operasi approval memiliki endpoint eksplisit
- semua list screen mendukung filter, sort, dan pagination
- semua perubahan material tercatat dalam audit log
- response harus membedakan data resmi vs data pending bila relevan

## 16.2 Suggested Service Domains
Service domain minimum:
- Auth Service
- User and Role Service
- Project Service
- WBD Service
- Progress Service
- Cost Service
- File Service
- Analytics Service
- Report Service
- Audit Service

## 16.3 Suggested Endpoint Groups

### 16.3.1 Auth
- `POST /auth/login`
- `POST /auth/logout`
- `GET /auth/me`

### 16.3.2 Users and Roles
- `GET /users`
- `POST /users`
- `PATCH /users/{id}`
- `GET /roles`

### 16.3.3 Projects
- `GET /projects`
- `POST /projects`
- `GET /projects/{id}`
- `PATCH /projects/{id}`

Note:
- implementasi create/edit project harus mengikuti keputusan bisnis final tentang siapa yang boleh mengelolanya.

### 16.3.4 WBD Versions
- `GET /projects/{projectId}/wbd-versions`
- `POST /projects/{projectId}/wbd-versions`
- `GET /wbd-versions/{id}`
- `POST /wbd-versions/{id}/submit`
- `POST /wbd-versions/{id}/approve`
- `POST /wbd-versions/{id}/reject`

### 16.3.5 WBD Nodes
- `GET /wbd-versions/{wbdVersionId}/nodes`
- `POST /wbd-versions/{wbdVersionId}/nodes`
- `PATCH /wbd-nodes/{id}`
- `DELETE /wbd-nodes/{id}`

### 16.3.6 Progress
- `GET /projects/{projectId}/progress-entries`
- `POST /projects/{projectId}/progress-entries`
- `GET /progress-entries/{id}`
- `PATCH /progress-entries/{id}`
- `POST /progress-entries/{id}/approve`
- `POST /progress-entries/{id}/reject`

### 16.3.7 Actual Cost
- `GET /projects/{projectId}/actual-cost-transactions`
- `POST /projects/{projectId}/actual-cost-transactions`
- `GET /actual-cost-transactions/{id}`
- `PATCH /actual-cost-transactions/{id}`
- `POST /actual-cost-transactions/{id}/approve`
- `POST /actual-cost-transactions/{id}/reject`

### 16.3.8 Files
- `GET /projects/{projectId}/files`
- `POST /projects/{projectId}/files`
- `GET /files/{id}`
- `PATCH /files/{id}`
- `GET /file-categories`
- `POST /file-categories`
- `PATCH /file-categories/{id}`

### 16.3.9 Analytics
- `GET /projects/{projectId}/dashboard`
- `GET /projects/{projectId}/gantt`
- `GET /projects/{projectId}/s-curve`
- `GET /projects/{projectId}/cost-analysis`

### 16.3.10 Reports
- `GET /projects/{projectId}/reports`
- `POST /projects/{projectId}/reports/generate`
- `GET /reports/{id}`

### 16.3.11 Audit
- `GET /audit-logs`
- `GET /audit-logs/{entityType}/{entityId}`

## 16.4 Suggested Response Patterns
Minimal response envelope disarankan konsisten:

```json
{
  "success": true,
  "message": "Operation completed successfully",
  "data": {},
  "meta": {}
}
```

Untuk validation error:

```json
{
  "success": false,
  "message": "Validation failed",
  "errors": {
    "field_name": ["Error message"]
  }
}
```

## 16.5 Suggested Approval Actions Payload
Contoh payload approval/rejection yang konsisten:

### Approve
```json
{
  "note": "Approved"
}
```

### Reject
```json
{
  "reason": "Data tidak sesuai dokumen pendukung"
}
```

## 16.6 Service-Level Business Guards
Aplikasi/service layer minimal harus memeriksa:
1. project memiliki baseline aktif sebelum create progress,
2. only authorized role may approve,
3. actual cost selalu punya `progress_entry_id`,
4. perubahan baseline cost dilakukan melalui WBD version, bukan update langsung baseline aktif,
5. analytics hanya memakai data resmi yang approved,
6. file image wajib memiliki caption dan photo_date,
7. Gantt endpoint hanya menyajikan data read-only.

---

# 17. Approved Data Correction Strategy (Draft)

## 17.1 Correction Principle
Data yang sudah approved tidak disarankan diedit langsung. Koreksi sebaiknya dilakukan melalui mekanisme transaksi baru atau versi baru untuk menjaga audit trail.

## 17.2 WBD Correction
- WBD final approved tidak boleh diedit langsung.
- Koreksi dilakukan dengan membuat WBD version baru.
- Setelah disetujui Direksi, versi baru menggantikan baseline aktif lama.

## 17.3 Progress Correction
- Progress dengan status APPROVED atau AUTO_APPROVED tidak boleh diubah langsung.
- Koreksi progress sebaiknya dilakukan melalui:
  - progress adjustment entry baru, atau
  - reversal + re-entry policy sesuai desain lanjutan.

## 17.4 Actual Cost Correction
- Actual cost APPROVED tidak boleh diubah langsung.
- Koreksi cost dilakukan melalui transaksi penyesuaian baru dengan jejak audit.

## 17.5 File Correction
- Metadata file dapat diperbarui sesuai kebijakan implementasi.
- File fisik yang salah sebaiknya diarsipkan dan diganti dengan upload baru, bukan diam-diam ditimpa.

---

# 18. MVP Scope vs Future Scope

## 18.1 Recommended MVP Scope
Fitur yang disarankan masuk MVP:
- authentication dan role-based access
- project list dan project detail
- WBD versioning dasar
- WBD tree CRUD
- approval WBD oleh Direksi
- Gantt read-only dari WBD
- progress entry dan approval PM
- actual cost entry dan approval Finance
- file upload document/photo dengan relasi ke progress atau WBD item
- dashboard dasar
- S-Curve dasar
- cost analysis dasar
- generate final report
- audit log dasar

## 18.2 Recommended Future Scope
Fitur yang dapat masuk fase lanjutan:
- correction workflow formal yang lebih detail
- baseline change comparison view
- notification center dan reminder
- advanced analytics dan forecasting
- geotagging otomatis untuk foto
- mobile-optimized field app / PWA / native app
- document approval workflow
- report distribution workflow
- more advanced hierarchy performance model

---

# 19. Next Detailing Sections
Bagian yang perlu dirinci pada iterasi berikutnya:
1. pseudo-SQL schema draft atau migration draft,
2. sample API payload per endpoint,
3. detailed dashboard formula catalog,
4. S-Curve calculation method by period,
5. report template catalog,
6. test scenario / acceptance criteria per module.
