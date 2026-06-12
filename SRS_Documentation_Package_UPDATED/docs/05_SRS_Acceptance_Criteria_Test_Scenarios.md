# Acceptance Criteria and Test Scenarios
## Project Management Web Application

> Dokumen ini berisi acceptance criteria dan skenario uji utama per modul. Format ditulis agar mudah dipakai oleh QA, BA, dan developer.

---

# 1. General Acceptance Criteria

## 1.1 Authentication and Authorization
- User hanya dapat mengakses sistem jika berhasil login
- Hak aksi user harus mengikuti role
- User tanpa hak yang sesuai harus mendapat response forbidden / UI action disabled

## 1.2 Approved vs Pending Data
- Data pending tidak boleh masuk analytics resmi
- Data approved harus tampil di dashboard, S-curve, cost analysis, dan report final
- Data rejected harus tetap tersimpan untuk audit

## 1.3 Baseline Dependency
- Progress tidak boleh dibuat jika belum ada active baseline WBD final approved
- Gantt harus selalu menampilkan baseline aktif

---

# 2. Project Module

## 2.1 Acceptance Criteria
1. Sistem dapat menampilkan daftar project
2. Setiap project menampilkan `project_name`, `client_name`, periode, dan status
3. User dari semua role dapat melihat project list
4. Project detail menampilkan baseline aktif bila tersedia

## 2.2 Test Scenarios

### Scenario PM-PRJ-001
**Title:** User melihat daftar project  
**Given** user sudah login  
**When** user membuka halaman project list  
**Then** sistem menampilkan daftar project yang tersedia  
**And** setiap row minimal menampilkan nama project, client, periode, dan status

### Scenario PM-PRJ-002
**Title:** Project detail menampilkan baseline aktif  
**Given** project memiliki baseline aktif  
**When** user membuka detail project  
**Then** sistem menampilkan nomor versi baseline aktif dan statusnya

---

# 3. WBD Module

## 3.1 Acceptance Criteria
1. Project Manager dan Admin Proyek dapat membuat draft WBD
2. WBD mendukung struktur multi-level hierarchy
3. Sistem menghitung planned cost otomatis dari volume × rate
4. Sistem menghitung end date dari start date + duration - 1
5. WBD final approved tidak dapat diedit langsung
6. Revisi WBD menghasilkan versi baru
7. Hanya Direksi yang dapat approve/reject WBD
8. Hanya satu baseline aktif per project

## 3.2 Test Scenarios

### Scenario PM-WBD-001
**Title:** PM membuat draft WBD baru  
**Given** user berrole Project Manager  
**When** user membuat WBD draft untuk project  
**Then** sistem membuat versi WBD dengan status `DRAFT`

### Scenario PM-WBD-002
**Title:** Admin Proyek menambah item WBD operasional  
**Given** user berrole Admin Proyek  
**And** user membuka WBD draft  
**When** user menambah item dengan volume, rate, start date, dan duration  
**Then** sistem menyimpan item baru  
**And** sistem menghitung planned cost otomatis  
**And** sistem menghitung end date otomatis

### Scenario PM-WBD-003
**Title:** Direksi menyetujui revisi WBD  
**Given** terdapat WBD version dengan status `PENDING_DIRECTOR_APPROVAL`  
**When** user berrole Direksi memilih approve  
**Then** status WBD berubah menjadi `FINAL_APPROVED`  
**And** versi itu menjadi baseline aktif  
**And** baseline aktif sebelumnya berubah menjadi `SUPERSEDED`

### Scenario PM-WBD-004
**Title:** User mencoba edit WBD final approved  
**Given** WBD version sudah `FINAL_APPROVED`  
**When** PM atau Admin Proyek mencoba mengubah node langsung  
**Then** sistem menolak perubahan langsung  
**And** sistem mengarahkan user untuk membuat revisi versi baru

### Scenario PM-WBD-005
**Title:** Hanya satu baseline aktif  
**Given** sudah ada satu WBD version aktif  
**When** versi baru disetujui  
**Then** hanya versi baru yang berstatus aktif  
**And** versi lama tidak lagi aktif

---

# 4. Gantt Module

## 4.1 Acceptance Criteria
1. Gantt mengambil data dari baseline aktif
2. Gantt bersifat read-only
3. User semua role dapat melihat Gantt
4. Perubahan pada baseline aktif tercermin di Gantt

## 4.2 Test Scenarios

### Scenario PM-GNT-001
**Title:** User melihat Gantt dari baseline aktif  
**Given** project memiliki WBD baseline aktif  
**When** user membuka halaman Gantt  
**Then** sistem menampilkan task berdasarkan baseline aktif

### Scenario PM-GNT-002
**Title:** User tidak dapat edit Gantt langsung  
**Given** user membuka halaman Gantt  
**When** user mencoba drag bar atau edit tanggal dari Gantt  
**Then** sistem tidak mengizinkan perubahan langsung

---

# 5. Progress Module

## 5.1 Acceptance Criteria
1. Progress hanya dapat dibuat jika ada baseline aktif
2. Progress harus terkait ke WBD item operasional
3. Progress dari Admin Proyek harus masuk status `PENDING_PM_APPROVAL`
4. Progress dari Project Manager harus masuk status `AUTO_APPROVED`
5. Hanya progress `APPROVED` dan `AUTO_APPROVED` yang memengaruhi analytics resmi
6. Progress approved tidak boleh diedit langsung

## 5.2 Test Scenarios

### Scenario PM-PRG-001
**Title:** Admin Proyek input progress saat baseline aktif tersedia  
**Given** project memiliki baseline aktif  
**And** user berrole Admin Proyek  
**When** user submit progress untuk item operasional  
**Then** sistem menyimpan progress  
**And** status progress adalah `PENDING_PM_APPROVAL`

### Scenario PM-PRG-002
**Title:** PM input progress dan auto-approved  
**Given** project memiliki baseline aktif  
**And** user berrole Project Manager  
**When** user submit progress  
**Then** sistem menyimpan progress  
**And** status progress adalah `AUTO_APPROVED`

### Scenario PM-PRG-003
**Title:** Progress tidak boleh dibuat tanpa baseline aktif  
**Given** project belum memiliki baseline aktif  
**When** user mencoba submit progress  
**Then** sistem menolak transaksi  
**And** menampilkan pesan bahwa baseline aktif wajib tersedia

### Scenario PM-PRG-004
**Title:** PM approve progress Admin Proyek  
**Given** terdapat progress dengan status `PENDING_PM_APPROVAL`  
**When** PM memilih approve  
**Then** status berubah menjadi `APPROVED`

### Scenario PM-PRG-005
**Title:** PM reject progress Admin Proyek  
**Given** terdapat progress dengan status `PENDING_PM_APPROVAL`  
**When** PM memilih reject dengan alasan  
**Then** status berubah menjadi `REJECTED`  
**And** alasan reject tersimpan

### Scenario PM-PRG-006
**Title:** Progress pending tidak masuk dashboard resmi  
**Given** ada progress `PENDING_PM_APPROVAL`  
**When** user membuka dashboard atau S-curve  
**Then** progress tersebut tidak dihitung dalam angka resmi

---

# 6. Actual Cost Module

## 6.1 Acceptance Criteria
1. Actual cost harus selalu terkait ke satu progress entry
2. Admin Proyek dapat input actual cost
3. Project Manager tidak dapat input actual cost
4. Finance dapat input actual cost
5. Biaya dari Admin Proyek harus masuk status `REVIEW`
6. Finance dapat approve/reject actual cost
7. Hanya actual cost `APPROVED` yang masuk analytics resmi

## 6.2 Test Scenarios

### Scenario PM-CST-001
**Title:** Admin Proyek input actual cost  
**Given** user berrole Admin Proyek  
**And** progress entry valid tersedia  
**When** user input actual cost  
**Then** sistem menyimpan transaksi biaya  
**And** status transaksi adalah `REVIEW`

### Scenario PM-CST-002
**Title:** Finance input actual cost  
**Given** user berrole Finance  
**And** progress entry valid tersedia  
**When** user input actual cost  
**Then** sistem menyimpan transaksi biaya  
**And** transaksi mengikuti policy status implementasi yang berlaku

### Scenario PM-CST-003
**Title:** PM tidak boleh input biaya  
**Given** user berrole Project Manager  
**When** user mencoba membuka atau submit form input actual cost  
**Then** sistem menolak akses atau action tersebut

### Scenario PM-CST-004
**Title:** Finance approve biaya  
**Given** terdapat transaksi biaya dengan status `REVIEW`  
**When** Finance memilih approve  
**Then** status berubah menjadi `APPROVED`  
**And** biaya masuk ke angka resmi analytics

### Scenario PM-CST-005
**Title:** Finance reject biaya  
**Given** terdapat transaksi biaya dengan status `REVIEW`  
**When** Finance memilih reject dengan alasan  
**Then** status berubah menjadi `REJECTED`  
**And** alasan reject tersimpan

### Scenario PM-CST-006
**Title:** Actual cost harus punya progress entry  
**Given** user mengisi form actual cost  
**When** progress entry tidak dipilih  
**Then** sistem menolak submit  
**And** menampilkan pesan validasi wajib pilih progress entry

---

# 7. File Repository Module

## 7.1 Acceptance Criteria
1. Sistem mendukung upload document dan image
2. File dapat dikaitkan ke WBD item atau progress entry
3. File image wajib memiliki caption dan photo_date
4. Semua role dapat melihat file
5. Hanya PM dan Admin Proyek yang dapat upload file operasional
6. Administrator Sistem dapat mengelola master kategori file

## 7.2 Test Scenarios

### Scenario PM-FIL-001
**Title:** Admin Proyek upload dokumen  
**Given** user berrole Admin Proyek  
**When** user upload file bertipe document dengan category valid  
**Then** sistem menyimpan file dan metadata

### Scenario PM-FIL-002
**Title:** PM upload foto lapangan  
**Given** user berrole Project Manager  
**When** user upload file bertipe image dengan caption dan photo_date  
**Then** sistem menyimpan file image  
**And** file dapat dipreview

### Scenario PM-FIL-003
**Title:** Upload foto tanpa caption ditolak  
**Given** user memilih file_type image  
**When** user submit tanpa caption  
**Then** sistem menolak submit  
**And** menampilkan validasi bahwa caption wajib

### Scenario PM-FIL-004
**Title:** Upload foto tanpa photo_date ditolak  
**Given** user memilih file_type image  
**When** user submit tanpa photo_date  
**Then** sistem menolak submit  
**And** menampilkan validasi bahwa tanggal foto wajib

### Scenario PM-FIL-005
**Title:** Administrator Sistem kelola kategori file  
**Given** user berrole Administrator Sistem  
**When** user menambah kategori file baru  
**Then** sistem menyimpan kategori baru  
**And** kategori bisa digunakan pada form upload berikutnya

---

# 8. Dashboard Module

## 8.1 Acceptance Criteria
1. Dashboard hanya menampilkan data resmi
2. Dashboard menampilkan baseline cost, actual cost approved, progress resmi, item aktif, item terlambat
3. Semua role dapat melihat dashboard
4. Dashboard mengikuti baseline aktif pada project

## 8.2 Test Scenarios

### Scenario PM-DSH-001
**Title:** Dashboard hanya menggunakan data approved  
**Given** ada mix data approved dan pending  
**When** user membuka dashboard  
**Then** hanya data approved/resmi yang dihitung

### Scenario PM-DSH-002
**Title:** Semua role dapat melihat dashboard  
**Given** user login dengan role apa pun  
**When** user membuka dashboard  
**Then** sistem menampilkan dashboard sesuai project yang dipilih

---

# 9. S-Curve Module

## 9.1 Acceptance Criteria
1. S-Curve menampilkan plan vs actual kumulatif
2. Actual pada S-Curve hanya memakai progress approved/auto-approved dan cost approved
3. Semua role dapat melihat S-Curve

## 9.2 Test Scenarios

### Scenario PM-SCV-001
**Title:** S-Curve volume menggunakan progress resmi  
**Given** ada progress pending dan approved  
**When** user membuka S-Curve  
**Then** hanya progress approved/auto-approved yang dihitung ke actual volume

### Scenario PM-SCV-002
**Title:** S-Curve biaya menggunakan actual cost approved  
**Given** ada biaya review dan approved  
**When** user membuka S-Curve  
**Then** hanya biaya approved yang dihitung ke actual cost cumulative

---

# 10. Cost Analysis Module

## 10.1 Acceptance Criteria
1. Cost analysis menampilkan baseline cost, actual cost approved, deviasi, dan weighted progress
2. Semua role dapat melihat cost analysis
3. Nilai actual pada layar ini hanya berasal dari biaya approved

## 10.2 Test Scenarios

### Scenario PM-CAN-001
**Title:** Cost analysis menghitung deviasi biaya  
**Given** baseline cost dan actual cost approved tersedia  
**When** user membuka cost analysis  
**Then** sistem menampilkan deviasi biaya secara benar

### Scenario PM-CAN-002
**Title:** Cost analysis mengabaikan biaya rejected/pending  
**Given** terdapat transaksi biaya dengan status review atau rejected  
**When** user membuka cost analysis  
**Then** transaksi tersebut tidak dihitung sebagai actual resmi

---

# 11. Report Module

## 11.1 Acceptance Criteria
1. PM dan Admin Proyek dapat generate final report
2. Report tidak memiliki draft workflow
3. Report history menyimpan hasil generate
4. Semua role dapat melihat hasil report yang tersedia

## 11.2 Test Scenarios

### Scenario PM-RPT-001
**Title:** PM generate final report  
**Given** user berrole Project Manager  
**When** user memilih report type dan periode lalu generate  
**Then** sistem menghasilkan file report final  
**And** menyimpan record report history

### Scenario PM-RPT-002
**Title:** Admin Proyek generate final report  
**Given** user berrole Admin Proyek  
**When** user generate report  
**Then** sistem menghasilkan report final tanpa status draft

### Scenario PM-RPT-003
**Title:** User lihat report history  
**Given** report telah digenerate  
**When** user membuka history report  
**Then** sistem menampilkan type, period, generated_by, generated_at, dan file output

---

# 12. System Administration Module

## 12.1 Acceptance Criteria
1. Administrator Sistem dapat mengelola user dan role
2. Administrator Sistem dapat mengelola master kategori file
3. Administrator Sistem tidak dapat mengelola data operasional proyek

## 12.2 Test Scenarios

### Scenario PM-ADM-001
**Title:** Administrator Sistem tambah user  
**Given** user berrole Administrator Sistem  
**When** user menambah akun baru  
**Then** sistem menyimpan user baru dengan role yang dipilih

### Scenario PM-ADM-002
**Title:** Administrator Sistem ubah role user  
**Given** user berrole Administrator Sistem  
**When** user mengubah role akun tertentu  
**Then** sistem menyimpan role baru

### Scenario PM-ADM-003
**Title:** Administrator Sistem tidak boleh input progress  
**Given** user berrole Administrator Sistem  
**When** user mencoba membuka form progress entry  
**Then** sistem menolak akses

### Scenario PM-ADM-004
**Title:** Administrator Sistem tidak boleh input actual cost  
**Given** user berrole Administrator Sistem  
**When** user mencoba membuka form actual cost  
**Then** sistem menolak akses

---

# 13. Cross-Module Integrity Scenarios

### Scenario PM-INT-001
**Title:** Revisi WBD pending tidak menghentikan progress  
**Given** project punya baseline aktif versi 3  
**And** versi 4 sedang `PENDING_DIRECTOR_APPROVAL`  
**When** Admin Proyek input progress  
**Then** sistem tetap mengizinkan progress menggunakan baseline aktif versi 3

### Scenario PM-INT-002
**Title:** Baseline baru aktif mengubah Gantt  
**Given** versi WBD baru telah `FINAL_APPROVED` dan aktif  
**When** user membuka Gantt  
**Then** Gantt menampilkan task dari baseline baru

### Scenario PM-INT-003
**Title:** Data resmi konsisten antar layar  
**Given** ada progress approved dan actual cost approved  
**When** user membuka dashboard, S-Curve, dan cost analysis  
**Then** ketiga layar memakai angka resmi yang konsisten

---

# 14. Exit Criteria for MVP UAT
MVP dianggap memenuhi UAT minimum bila:
- semua role utama dapat login dan melihat seluruh project
- workflow approval WBD berjalan end-to-end
- workflow approval progress berjalan end-to-end
- workflow review/approval biaya berjalan end-to-end
- file document dan photo dapat diupload dengan validasi benar
- dashboard, Gantt, S-Curve, dan cost analysis memakai data resmi
- report final dapat digenerate dan diunduh
- audit trail dasar tersedia untuk perubahan penting
