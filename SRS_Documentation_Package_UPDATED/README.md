# SRS Documentation Index
## Project Management Web Application

### Document Status
Documentation Package Index  
Updated at: 2026-06-11 03:19:05 UTC

---

## 1. Purpose of This Index
Dokumen ini berfungsi sebagai panduan resmi untuk membaca paket dokumentasi SRS secara berurutan dan konsisten. Tujuannya adalah agar AI/coder, backend engineer, frontend engineer, QA, dan analyst memahami:
- dokumen mana yang menjadi sumber kebenaran utama,
- urutan baca yang disarankan,
- fungsi masing-masing dokumen,
- hubungan antar dokumen,
- cara menggunakan dokumen ini untuk implementasi.

---

## 2. Source of Truth Hierarchy

Urutan prioritas dokumen adalah sebagai berikut:

1. **`docs/01_SRS_Main.md`**  
   Menjadi sumber kebenaran utama untuk:
   - business scope,
   - business rules,
   - role & permission,
   - workflow,
   - data model konseptual,
   - functional requirements.

2. **`docs/02_UI_UX_Specification_Per_Screen.md`**  
   Menjadi sumber kebenaran utama untuk:
   - screen purpose,
   - layout dan section,
   - komponen UI,
   - state layar,
   - interaksi pengguna,
   - validation UX,
   - navigation behavior.

3. **`docs/03_SRS_Pseudo_SQL_Schema_Draft.md`**  
   Menjadi referensi utama untuk:
   - draft relational schema,
   - tabel database,
   - constraint,
   - indexing,
   - service-layer guard yang berkaitan dengan persistence.

4. **`docs/04_SRS_Sample_API_Request_Response_Payload.md`**  
   Menjadi referensi utama untuk:
   - struktur endpoint,
   - bentuk request,
   - bentuk response,
   - pola response envelope,
   - contoh payload approval/rejection.

5. **`docs/05_SRS_Acceptance_Criteria_Test_Scenarios.md`**  
   Menjadi referensi utama untuk:
   - acceptance criteria,
   - skenario uji,
   - validasi perilaku sistem,
   - baseline QA/UAT.

6. **`docs/06_Implementation_Task_Breakdown.md`**  
   Menjadi referensi utama untuk:
   - urutan implementasi,
   - phase breakdown,
   - dependency antar task,
   - sprint suggestion,
   - definition of done per modul.

---

## 3. Recommended Reading Order

### 3.1 For AI/Coder
Urutan baca yang direkomendasikan:
1. `01_SRS_Main.md`
2. `02_UI_UX_Specification_Per_Screen.md`
3. `03_SRS_Pseudo_SQL_Schema_Draft.md`
4. `04_SRS_Sample_API_Request_Response_Payload.md`
5. `05_SRS_Acceptance_Criteria_Test_Scenarios.md`
6. `06_Implementation_Task_Breakdown.md`

### 3.2 For Backend Engineer
Urutan baca yang direkomendasikan:
1. `01_SRS_Main.md`
2. `03_SRS_Pseudo_SQL_Schema_Draft.md`
3. `04_SRS_Sample_API_Request_Response_Payload.md`
4. `05_SRS_Acceptance_Criteria_Test_Scenarios.md`
5. `06_Implementation_Task_Breakdown.md`
6. `02_UI_UX_Specification_Per_Screen.md`

### 3.3 For Frontend Engineer
Urutan baca yang direkomendasikan:
1. `01_SRS_Main.md`
2. `02_UI_UX_Specification_Per_Screen.md`
3. `04_SRS_Sample_API_Request_Response_Payload.md`
4. `05_SRS_Acceptance_Criteria_Test_Scenarios.md`
5. `06_Implementation_Task_Breakdown.md`
6. `03_SRS_Pseudo_SQL_Schema_Draft.md`

### 3.4 For QA / Tester
Urutan baca yang direkomendasikan:
1. `01_SRS_Main.md`
2. `05_SRS_Acceptance_Criteria_Test_Scenarios.md`
3. `02_UI_UX_Specification_Per_Screen.md`
4. `04_SRS_Sample_API_Request_Response_Payload.md`
5. `06_Implementation_Task_Breakdown.md`
6. `03_SRS_Pseudo_SQL_Schema_Draft.md`

---

## 4. Document Directory

### 4.1 `docs/01_SRS_Main.md`
**Role in package:** Dokumen utama  
**Use this for:** memahami aturan bisnis dan requirement sistem secara keseluruhan

### 4.2 `docs/02_UI_UX_Specification_Per_Screen.md`
**Role in package:** Spesifikasi implementasi layar  
**Use this for:** merancang dan membangun halaman frontend secara konsisten

### 4.3 `docs/03_SRS_Pseudo_SQL_Schema_Draft.md`
**Role in package:** Draft struktur database  
**Use this for:** mendesain schema awal backend dan persistence layer

### 4.4 `docs/04_SRS_Sample_API_Request_Response_Payload.md`
**Role in package:** Contoh kontrak API  
**Use this for:** membangun endpoint dan menyelaraskan response dengan frontend

### 4.5 `docs/05_SRS_Acceptance_Criteria_Test_Scenarios.md`
**Role in package:** Baseline QA/UAT  
**Use this for:** memastikan implementasi sesuai behavior yang diharapkan

### 4.6 `docs/06_Implementation_Task_Breakdown.md`
**Role in package:** Panduan implementasi terstruktur  
**Use this for:** memecah pekerjaan build menjadi fase, task, dependency, dan deliverable

---

## 5. How to Use This Package in Development

### 5.1 Before Coding
Pastikan implementer sudah memahami:
- model akses project,
- role dan approval flow,
- WBD versioning,
- pemisahan progress dan actual cost,
- aturan approved vs pending data.

### 5.2 During Backend Development
Gunakan:
- `01_SRS_Main.md`
- `03_SRS_Pseudo_SQL_Schema_Draft.md`
- `04_SRS_Sample_API_Request_Response_Payload.md`
- `06_Implementation_Task_Breakdown.md`

### 5.3 During Frontend Development
Gunakan:
- `01_SRS_Main.md`
- `02_UI_UX_Specification_Per_Screen.md`
- `04_SRS_Sample_API_Request_Response_Payload.md`
- `06_Implementation_Task_Breakdown.md`

### 5.4 During Testing
Gunakan:
- `05_SRS_Acceptance_Criteria_Test_Scenarios.md`
sebagai baseline utama,
dengan referensi silang ke:
- `01_SRS_Main.md`
- `02_UI_UX_Specification_Per_Screen.md`
- `06_Implementation_Task_Breakdown.md`

---

## 6. Conflict Resolution Rule

Jika terjadi konflik antar dokumen, gunakan aturan berikut:

1. **SRS Main** mengalahkan dokumen lain untuk aturan bisnis dan requirement inti.
2. **UI/UX Specification** mengalahkan interpretasi frontend umum untuk behavior layar.
3. **Pseudo-SQL Schema Draft** adalah rekomendasi teknis, tetapi tidak boleh melanggar SRS Main.
4. **API Payload Samples** boleh disesuaikan secara teknis selama tetap konsisten dengan SRS Main dan UI/UX needs.
5. **Acceptance Criteria** harus dipakai untuk memverifikasi bahwa hasil implementasi tetap sesuai requirement.
6. **Implementation Task Breakdown** membantu urutan delivery, tetapi tidak boleh menggantikan aturan bisnis di SRS Main.

---

## 7. Recommended Working Method for AI/Coder

Disarankan AI/coder bekerja dengan urutan berikut:
1. baca `01_SRS_Main.md` sampai paham domain dan workflow,
2. baca `02_UI_UX_Specification_Per_Screen.md` untuk memahami kebutuhan layar,
3. baca `03_SRS_Pseudo_SQL_Schema_Draft.md` untuk model database,
4. baca `04_SRS_Sample_API_Request_Response_Payload.md` untuk kontrak interface,
5. gunakan `05_SRS_Acceptance_Criteria_Test_Scenarios.md` sebagai checklist verifikasi implementasi,
6. gunakan `06_Implementation_Task_Breakdown.md` untuk menentukan urutan build yang aman.

AI/coder sebaiknya tidak langsung memulai coding hanya dari mockup tanpa membaca dokumen-dokumen di atas.

---

## 8. Recommended Next Documents (Optional)
Dokumen tambahan yang bisa disiapkan berikutnya bila project ingin langsung masuk fase build yang lebih terstruktur:
- sprint backlog
- owner assignment matrix
- SQL migration draft final
- test case spreadsheet
- deployment and environment setup guide

---

## 9. Package File List
- `README.md` ← dokumen index ini
- `docs/01_SRS_Main.md`
- `docs/02_UI_UX_Specification_Per_Screen.md`
- `docs/03_SRS_Pseudo_SQL_Schema_Draft.md`
- `docs/04_SRS_Sample_API_Request_Response_Payload.md`
- `docs/05_SRS_Acceptance_Criteria_Test_Scenarios.md`
- `docs/06_Implementation_Task_Breakdown.md`

---

## 10. Final Note
Paket ini dirancang sebagai satu set referensi yang saling melengkapi:
- SRS Main menjelaskan **apa** yang harus dibangun,
- UI/UX Spec menjelaskan **bagaimana layar berperilaku**,
- Schema Draft menjelaskan **bagaimana data disimpan**,
- API Samples menjelaskan **bagaimana modul saling berbicara**,
- Acceptance Criteria menjelaskan **bagaimana hasilnya dinilai benar**,
- Implementation Task Breakdown menjelaskan **dalam urutan apa sistem sebaiknya dibangun**.
