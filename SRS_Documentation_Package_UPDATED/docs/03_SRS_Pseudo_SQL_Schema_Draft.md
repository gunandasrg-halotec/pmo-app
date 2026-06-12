# Pseudo-SQL Schema Draft
## Project Management Web Application

> Draft pseudo-SQL ini bersifat vendor-neutral, tetapi gaya penulisannya paling dekat ke PostgreSQL.

---

# 1. Notes and Conventions

- PK menggunakan `UUID`
- Semua tabel utama memakai `created_at` dan `updated_at`
- Soft delete hanya diterapkan bila diperlukan di fase lanjutan
- Constraint approval material tetap harus dijaga juga di application/service layer
- Karena ada polymorphic relation pada file, sebagian validasi referensial dilakukan di aplikasi

---

# 2. Master Tables

## 2.1 roles
```sql
CREATE TABLE roles (
    id UUID PRIMARY KEY,
    role_name VARCHAR(100) NOT NULL UNIQUE,
    description TEXT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

## 2.2 users
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    role_id UUID NOT NULL REFERENCES roles(id),
    full_name VARCHAR(150) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password_hash TEXT NOT NULL,
    is_active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

## 2.3 file_categories
```sql
CREATE TABLE file_categories (
    id UUID PRIMARY KEY,
    category_name VARCHAR(100) NOT NULL UNIQUE,
    description TEXT NULL,
    is_active BOOLEAN NOT NULL DEFAULT TRUE,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

Seed data yang direkomendasikan:
- Kontrak
- Berita Acara
- Invoice
- Foto Lapangan
- Approval
- Bukti Pembayaran
- Lainnya

---

# 3. Project Tables

## 3.1 projects
```sql
CREATE TABLE projects (
    id UUID PRIMARY KEY,
    project_code VARCHAR(100) NOT NULL UNIQUE,
    project_name VARCHAR(200) NOT NULL,
    client_name VARCHAR(200) NOT NULL,
    location VARCHAR(200) NOT NULL,
    start_date DATE NOT NULL,
    end_date DATE NOT NULL,
    status VARCHAR(50) NOT NULL,
    description TEXT NULL,
    active_wbd_version_id UUID NULL,
    created_by UUID NOT NULL REFERENCES users(id),
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT chk_projects_date CHECK (end_date >= start_date)
);
```

> `active_wbd_version_id` akan ditambahkan setelah tabel `wbd_versions` dibuat agar tidak terjadi circular dependency saat migration awal.

---

# 4. WBD Versioning and Structure

## 4.1 wbd_versions
```sql
CREATE TABLE wbd_versions (
    id UUID PRIMARY KEY,
    project_id UUID NOT NULL REFERENCES projects(id),
    version_number INTEGER NOT NULL,
    status VARCHAR(50) NOT NULL,
    based_on_version_id UUID NULL REFERENCES wbd_versions(id),
    submitted_by UUID NULL REFERENCES users(id),
    submitted_at TIMESTAMP NULL,
    approved_by UUID NULL REFERENCES users(id),
    approved_at TIMESTAMP NULL,
    rejected_by UUID NULL REFERENCES users(id),
    rejected_at TIMESTAMP NULL,
    rejection_reason TEXT NULL,
    is_active BOOLEAN NOT NULL DEFAULT FALSE,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_wbd_versions_project_version UNIQUE (project_id, version_number),
    CONSTRAINT chk_wbd_versions_active_status CHECK (
        (is_active = FALSE) OR (status = 'FINAL_APPROVED')
    )
);
```

## 4.2 Add FK from projects to active WBD version
```sql
ALTER TABLE projects
ADD CONSTRAINT fk_projects_active_wbd_version
FOREIGN KEY (active_wbd_version_id) REFERENCES wbd_versions(id);
```

## 4.3 Recommended partial unique index for one active baseline
```sql
CREATE UNIQUE INDEX uq_wbd_versions_one_active_per_project
ON wbd_versions(project_id)
WHERE is_active = TRUE;
```

## 4.4 wbd_nodes
```sql
CREATE TABLE wbd_nodes (
    id UUID PRIMARY KEY,
    wbd_version_id UUID NOT NULL REFERENCES wbd_versions(id),
    parent_node_id UUID NULL REFERENCES wbd_nodes(id),
    node_type VARCHAR(20) NOT NULL,
    code VARCHAR(100) NOT NULL,
    name VARCHAR(255) NOT NULL,
    description TEXT NULL,
    unit VARCHAR(50) NULL,
    volume NUMERIC(18,4) NULL,
    rate NUMERIC(18,2) NULL,
    planned_cost NUMERIC(18,2) NOT NULL DEFAULT 0,
    component_percent NUMERIC(10,4) NULL,
    total_percent NUMERIC(10,4) NULL,
    start_date DATE NULL,
    duration_days INTEGER NULL,
    end_date DATE NOT NULL,
    status VARCHAR(50) NOT NULL,
    sort_order INTEGER NOT NULL DEFAULT 0,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT uq_wbd_nodes_version_code UNIQUE (wbd_version_id, code),
    CONSTRAINT chk_wbd_nodes_node_type CHECK (node_type IN ('GROUP', 'ITEM')),
    CONSTRAINT chk_wbd_nodes_volume CHECK (volume IS NULL OR volume >= 0),
    CONSTRAINT chk_wbd_nodes_rate CHECK (rate IS NULL OR rate >= 0),
    CONSTRAINT chk_wbd_nodes_planned_cost CHECK (planned_cost >= 0),
    CONSTRAINT chk_wbd_nodes_duration CHECK (duration_days IS NULL OR duration_days >= 1)
);
```

### Optional trigger / service rule
Pastikan `parent_node_id` bila terisi berasal dari `wbd_version_id` yang sama.

Pseudo-check di service layer:
```sql
-- pseudo logic
IF parent_node_id IS NOT NULL THEN
    ASSERT parent_node.wbd_version_id = current_row.wbd_version_id;
END IF;
```

---

# 5. Progress and Cost Transactions

## 5.1 progress_entries
```sql
CREATE TABLE progress_entries (
    id UUID PRIMARY KEY,
    project_id UUID NOT NULL REFERENCES projects(id),
    wbd_node_id UUID NOT NULL REFERENCES wbd_nodes(id),
    progress_date DATE NOT NULL,
    progress_volume NUMERIC(18,4) NOT NULL,
    note TEXT NULL,
    entered_by UUID NOT NULL REFERENCES users(id),
    status VARCHAR(50) NOT NULL,
    approved_by UUID NULL REFERENCES users(id),
    approved_at TIMESTAMP NULL,
    rejected_by UUID NULL REFERENCES users(id),
    rejected_at TIMESTAMP NULL,
    rejection_reason TEXT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT chk_progress_entries_volume CHECK (progress_volume > 0),
    CONSTRAINT chk_progress_entries_status CHECK (
        status IN ('DRAFT', 'PENDING_PM_APPROVAL', 'AUTO_APPROVED', 'APPROVED', 'REJECTED')
    )
);
```

## 5.2 actual_cost_transactions
```sql
CREATE TABLE actual_cost_transactions (
    id UUID PRIMARY KEY,
    project_id UUID NOT NULL REFERENCES projects(id),
    progress_entry_id UUID NOT NULL REFERENCES progress_entries(id),
    amount NUMERIC(18,2) NOT NULL,
    transaction_date DATE NOT NULL,
    description TEXT NULL,
    entered_by UUID NOT NULL REFERENCES users(id),
    status VARCHAR(50) NOT NULL,
    reviewed_by UUID NULL REFERENCES users(id),
    reviewed_at TIMESTAMP NULL,
    rejected_by UUID NULL REFERENCES users(id),
    rejected_at TIMESTAMP NULL,
    rejection_reason TEXT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT chk_actual_cost_amount CHECK (amount > 0),
    CONSTRAINT chk_actual_cost_status CHECK (
        status IN ('DRAFT', 'REVIEW', 'APPROVED', 'REJECTED')
    )
);
```

### Integrity rule recommendation
`actual_cost_transactions.project_id` harus sama dengan `progress_entries.project_id`.

Pseudo-validation:
```sql
-- service layer validation
ASSERT actual_cost_transactions.project_id = progress_entries.project_id;
```

---

# 6. Files and Reports

## 6.1 project_files
```sql
CREATE TABLE project_files (
    id UUID PRIMARY KEY,
    project_id UUID NOT NULL REFERENCES projects(id),
    related_entity_type VARCHAR(50) NULL,
    related_entity_id UUID NULL,
    file_category_id UUID NOT NULL REFERENCES file_categories(id),
    file_type VARCHAR(20) NOT NULL,
    original_file_name VARCHAR(255) NOT NULL,
    storage_path TEXT NOT NULL,
    mime_type VARCHAR(150) NOT NULL,
    caption VARCHAR(255) NULL,
    photo_date DATE NULL,
    note TEXT NULL,
    uploaded_by UUID NOT NULL REFERENCES users(id),
    uploaded_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT chk_project_files_type CHECK (file_type IN ('DOCUMENT', 'IMAGE')),
    CONSTRAINT chk_project_files_related_type CHECK (
        related_entity_type IS NULL OR related_entity_type IN ('WBD_NODE', 'PROGRESS_ENTRY')
    ),
    CONSTRAINT chk_project_files_image_fields CHECK (
        (file_type <> 'IMAGE') OR (caption IS NOT NULL AND photo_date IS NOT NULL)
    ),
    CONSTRAINT chk_project_files_related_pair CHECK (
        (related_entity_type IS NULL AND related_entity_id IS NULL)
        OR
        (related_entity_type IS NOT NULL AND related_entity_id IS NOT NULL)
    )
);
```

> Karena `related_entity_id` polymorphic, FK eksplisit ke `wbd_nodes` / `progress_entries` tidak dibuat di level DB dasar. Validasi dilakukan di service layer.

## 6.2 report_records
```sql
CREATE TABLE report_records (
    id UUID PRIMARY KEY,
    project_id UUID NOT NULL REFERENCES projects(id),
    report_type VARCHAR(100) NOT NULL,
    period_start DATE NOT NULL,
    period_end DATE NOT NULL,
    file_path TEXT NOT NULL,
    generated_by UUID NOT NULL REFERENCES users(id),
    generated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    status VARCHAR(20) NOT NULL,
    CONSTRAINT chk_report_records_period CHECK (period_end >= period_start),
    CONSTRAINT chk_report_records_status CHECK (status IN ('FINAL', 'DELETED'))
);
```

---

# 7. Audit Log

## 7.1 audit_logs
```sql
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY,
    entity_type VARCHAR(100) NOT NULL,
    entity_id UUID NOT NULL,
    action_type VARCHAR(50) NOT NULL,
    action_by UUID NOT NULL REFERENCES users(id),
    action_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    old_value_json JSONB NULL,
    new_value_json JSONB NULL,
    remarks TEXT NULL
);
```

---

# 8. Recommended Indexes

```sql
CREATE INDEX idx_projects_status ON projects(status);
CREATE INDEX idx_projects_client_name ON projects(client_name);

CREATE INDEX idx_wbd_versions_project_status ON wbd_versions(project_id, status);

CREATE INDEX idx_wbd_nodes_parent ON wbd_nodes(parent_node_id);
CREATE INDEX idx_wbd_nodes_version_type ON wbd_nodes(wbd_version_id, node_type);
CREATE INDEX idx_wbd_nodes_version_sort ON wbd_nodes(wbd_version_id, sort_order);

CREATE INDEX idx_progress_entries_project ON progress_entries(project_id);
CREATE INDEX idx_progress_entries_node ON progress_entries(wbd_node_id);
CREATE INDEX idx_progress_entries_date ON progress_entries(progress_date);
CREATE INDEX idx_progress_entries_status ON progress_entries(status);
CREATE INDEX idx_progress_entries_project_status_date
    ON progress_entries(project_id, status, progress_date);

CREATE INDEX idx_actual_cost_project ON actual_cost_transactions(project_id);
CREATE INDEX idx_actual_cost_progress ON actual_cost_transactions(progress_entry_id);
CREATE INDEX idx_actual_cost_date ON actual_cost_transactions(transaction_date);
CREATE INDEX idx_actual_cost_status ON actual_cost_transactions(status);

CREATE INDEX idx_project_files_project ON project_files(project_id);
CREATE INDEX idx_project_files_category ON project_files(file_category_id);
CREATE INDEX idx_project_files_type ON project_files(file_type);
CREATE INDEX idx_project_files_related ON project_files(related_entity_type, related_entity_id);

CREATE INDEX idx_report_records_project ON report_records(project_id);
CREATE INDEX idx_report_records_project_type ON report_records(project_id, report_type);

CREATE INDEX idx_audit_logs_entity ON audit_logs(entity_type, entity_id);
CREATE INDEX idx_audit_logs_action_by ON audit_logs(action_by);
CREATE INDEX idx_audit_logs_action_at ON audit_logs(action_at);
```

---

# 9. Important Service-Layer Rules

## 9.1 WBD
- Hanya satu `wbd_versions.is_active = true` per project
- Hanya `FINAL_APPROVED` yang boleh aktif
- Perubahan baseline cost harus dilakukan lewat versi WBD baru

## 9.2 Progress
- Progress tidak boleh dibuat jika project belum punya baseline aktif
- Progress Admin Proyek harus berstatus `PENDING_PM_APPROVAL`
- Progress Project Manager langsung `AUTO_APPROVED`

## 9.3 Actual Cost
- Actual cost wajib terkait ke satu progress entry
- Actual cost dari Admin Proyek harus masuk status `REVIEW`
- Hanya actual cost `APPROVED` yang masuk analytics resmi

## 9.4 Files
- File image wajib punya `caption` dan `photo_date`
- File dapat dikaitkan ke `WBD_NODE` atau `PROGRESS_ENTRY`

---

# 10. Suggested Future Enhancements
- Tambahkan stable business key di `wbd_nodes` untuk tracking lintas versi
- Pertimbangkan junction tables untuk file association agar FK lebih ketat
- Tambahkan correction tables untuk progress adjustment dan cost adjustment
- Tambahkan materialized aggregate tables untuk dashboard dan S-curve bila volume data membesar
