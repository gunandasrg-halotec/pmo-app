# Sample Request and Response API Payload
## Project Management Web Application

> Payload di bawah ini adalah contoh kontrak API yang konsisten dengan SRS draft. Format final masih bisa disesuaikan framework/backend yang dipakai.

---

# 1. Response Envelope Standard

## Success
```json
{
  "success": true,
  "message": "Operation completed successfully",
  "data": {},
  "meta": {}
}
```

## Validation Error
```json
{
  "success": false,
  "message": "Validation failed",
  "errors": {
    "field_name": [
      "Error message"
    ]
  }
}
```

## Business Rule Error
```json
{
  "success": false,
  "message": "Project does not have an active approved baseline",
  "code": "BASELINE_REQUIRED"
}
```

---

# 2. Auth

## 2.1 Login

### Request
`POST /auth/login`
```json
{
  "email": "pm@company.com",
  "password": "secret123"
}
```

### Response
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "token": "jwt-or-session-token",
    "user": {
      "id": "1c8b9f3d-1111-4444-9999-a11111111111",
      "full_name": "Project Manager Sosa",
      "email": "pm@company.com",
      "role": {
        "id": "r-pm",
        "name": "Project Manager"
      }
    }
  }
}
```

---

# 3. Projects

## 3.1 List Projects

### Request
`GET /projects?status=ACTIVE&search=sosa&page=1&limit=20`

### Response
```json
{
  "success": true,
  "message": "Projects fetched successfully",
  "data": [
    {
      "id": "proj-001",
      "project_code": "PRJ-SOSA-2026",
      "project_name": "Replanting Kebun Sosa 2026",
      "client_name": "PT Contoh Client",
      "location": "Kebun Sosa",
      "start_date": "2026-06-08",
      "end_date": "2026-12-31",
      "status": "ACTIVE",
      "active_wbd_version": {
        "id": "wbdv-003",
        "version_number": 3,
        "status": "FINAL_APPROVED"
      }
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 1
  }
}
```

## 3.2 Get Project Detail

### Request
`GET /projects/proj-001`

### Response
```json
{
  "success": true,
  "message": "Project detail fetched successfully",
  "data": {
    "id": "proj-001",
    "project_code": "PRJ-SOSA-2026",
    "project_name": "Replanting Kebun Sosa 2026",
    "client_name": "PT Contoh Client",
    "location": "Kebun Sosa",
    "start_date": "2026-06-08",
    "end_date": "2026-12-31",
    "status": "ACTIVE",
    "description": "Project replanting kebun",
    "active_wbd_version": {
      "id": "wbdv-003",
      "version_number": 3,
      "status": "FINAL_APPROVED"
    }
  }
}
```

---

# 4. WBD Versions

## 4.1 Create WBD Draft Version

### Request
`POST /projects/proj-001/wbd-versions`
```json
{
  "based_on_version_id": "wbdv-003"
}
```

### Response
```json
{
  "success": true,
  "message": "WBD draft version created successfully",
  "data": {
    "id": "wbdv-004",
    "project_id": "proj-001",
    "version_number": 4,
    "status": "DRAFT",
    "based_on_version_id": "wbdv-003",
    "is_active": false
  }
}
```

## 4.2 Submit WBD for Direksi Approval

### Request
`POST /wbd-versions/wbdv-004/submit`
```json
{
  "note": "Mohon review revisi volume dan tarif pada item jalan baru"
}
```

### Response
```json
{
  "success": true,
  "message": "WBD version submitted for director approval",
  "data": {
    "id": "wbdv-004",
    "status": "PENDING_DIRECTOR_APPROVAL",
    "submitted_at": "2026-07-20T10:15:00Z"
  }
}
```

## 4.3 Approve WBD Version

### Request
`POST /wbd-versions/wbdv-004/approve`
```json
{
  "note": "Approved"
}
```

### Response
```json
{
  "success": true,
  "message": "WBD version approved successfully",
  "data": {
    "id": "wbdv-004",
    "status": "FINAL_APPROVED",
    "is_active": true,
    "approved_at": "2026-07-21T08:00:00Z",
    "superseded_version_id": "wbdv-003"
  }
}
```

## 4.4 Reject WBD Version

### Request
`POST /wbd-versions/wbdv-004/reject`
```json
{
  "reason": "Perubahan tarif belum sesuai lampiran persetujuan"
}
```

### Response
```json
{
  "success": true,
  "message": "WBD version rejected",
  "data": {
    "id": "wbdv-004",
    "status": "REJECTED",
    "rejection_reason": "Perubahan tarif belum sesuai lampiran persetujuan"
  }
}
```

---

# 5. WBD Nodes

## 5.1 List WBD Nodes

### Request
`GET /wbd-versions/wbdv-003/nodes`

### Response
```json
{
  "success": true,
  "message": "WBD nodes fetched successfully",
  "data": [
    {
      "id": "node-a",
      "parent_node_id": null,
      "node_type": "GROUP",
      "code": "A",
      "name": "Persiapan Areal",
      "planned_cost": 1446200000,
      "component_percent": null,
      "total_percent": 18.40,
      "start_date": "2026-06-08",
      "duration_days": 42,
      "end_date": "2026-07-19",
      "status": "RUNNING"
    },
    {
      "id": "node-a-01",
      "parent_node_id": "node-a",
      "node_type": "ITEM",
      "code": "A-01",
      "name": "Tumbang / Bongkar / Chipping",
      "unit": "Pokok",
      "volume": 3857,
      "rate": 60000,
      "planned_cost": 231420000,
      "component_percent": 16.00,
      "total_percent": 2.95,
      "start_date": "2026-06-08",
      "duration_days": 14,
      "end_date": "2026-06-21",
      "status": "RUNNING"
    }
  ]
}
```

## 5.2 Create WBD Node

### Request
`POST /wbd-versions/wbdv-004/nodes`
```json
{
  "parent_node_id": "node-b",
  "node_type": "ITEM",
  "code": "B-03",
  "name": "Pekerjaan Drainase",
  "description": "Drainase blok timur",
  "unit": "Mtr",
  "volume": 1500,
  "rate": 25000,
  "start_date": "2026-08-01",
  "duration_days": 10,
  "status": "PLANNED",
  "sort_order": 3
}
```

### Response
```json
{
  "success": true,
  "message": "WBD node created successfully",
  "data": {
    "id": "node-b-03",
    "wbd_version_id": "wbdv-004",
    "parent_node_id": "node-b",
    "node_type": "ITEM",
    "code": "B-03",
    "name": "Pekerjaan Drainase",
    "planned_cost": 37500000,
    "end_date": "2026-08-10"
  }
}
```

---

# 6. Progress Entries

## 6.1 Create Progress Entry by Admin Proyek

### Request
`POST /projects/proj-001/progress-entries`
```json
{
  "wbd_node_id": "node-a-01",
  "progress_date": "2026-06-10",
  "progress_volume": 300,
  "note": "Cuaca baik, alat berat aktif dua unit"
}
```

### Response
```json
{
  "success": true,
  "message": "Progress entry created successfully",
  "data": {
    "id": "prog-001",
    "project_id": "proj-001",
    "wbd_node_id": "node-a-01",
    "progress_date": "2026-06-10",
    "progress_volume": 300,
    "status": "PENDING_PM_APPROVAL",
    "entered_by": {
      "id": "user-admin-proyek-01",
      "name": "Admin Proyek Sosa"
    }
  }
}
```

## 6.2 Create Progress Entry by Project Manager

### Request
`POST /projects/proj-001/progress-entries`
```json
{
  "wbd_node_id": "node-a-01",
  "progress_date": "2026-06-11",
  "progress_volume": 250,
  "note": "Verifikasi lapangan oleh PM"
}
```

### Response
```json
{
  "success": true,
  "message": "Progress entry created successfully",
  "data": {
    "id": "prog-002",
    "status": "AUTO_APPROVED",
    "approved_by": {
      "id": "user-pm-01",
      "name": "Project Manager Sosa"
    }
  }
}
```

## 6.3 Approve Progress Entry

### Request
`POST /progress-entries/prog-001/approve`
```json
{
  "note": "Sesuai bukti lapangan"
}
```

### Response
```json
{
  "success": true,
  "message": "Progress entry approved successfully",
  "data": {
    "id": "prog-001",
    "status": "APPROVED",
    "approved_at": "2026-06-10T15:00:00Z"
  }
}
```

## 6.4 Reject Progress Entry

### Request
`POST /progress-entries/prog-001/reject`
```json
{
  "reason": "Volume belum sesuai BA progress"
}
```

### Response
```json
{
  "success": true,
  "message": "Progress entry rejected",
  "data": {
    "id": "prog-001",
    "status": "REJECTED",
    "rejection_reason": "Volume belum sesuai BA progress"
  }
}
```

---

# 7. Actual Cost Transactions

## 7.1 Create Actual Cost by Admin Proyek

### Request
`POST /projects/proj-001/actual-cost-transactions`
```json
{
  "progress_entry_id": "prog-001",
  "transaction_date": "2026-06-10",
  "amount": 18000000,
  "description": "Pembayaran tahap 1 pekerjaan chipping"
}
```

### Response
```json
{
  "success": true,
  "message": "Actual cost transaction created successfully",
  "data": {
    "id": "cost-001",
    "progress_entry_id": "prog-001",
    "amount": 18000000,
    "status": "REVIEW"
  }
}
```

## 7.2 Create Actual Cost by Finance

### Request
`POST /projects/proj-001/actual-cost-transactions`
```json
{
  "progress_entry_id": "prog-002",
  "transaction_date": "2026-06-11",
  "amount": 9500000,
  "description": "Verifikasi pembayaran alat berat"
}
```

### Response
```json
{
  "success": true,
  "message": "Actual cost transaction created successfully",
  "data": {
    "id": "cost-002",
    "progress_entry_id": "prog-002",
    "amount": 9500000,
    "status": "APPROVED"
  }
}
```

> Jika implementasi akhir ingin semua input biaya, termasuk dari Finance, tetap melewati step `REVIEW`, payload response bisa diubah ke status `REVIEW`.

## 7.3 Approve Actual Cost

### Request
`POST /actual-cost-transactions/cost-001/approve`
```json
{
  "note": "Nilai sesuai invoice"
}
```

### Response
```json
{
  "success": true,
  "message": "Actual cost approved successfully",
  "data": {
    "id": "cost-001",
    "status": "APPROVED",
    "reviewed_at": "2026-06-10T17:00:00Z"
  }
}
```

## 7.4 Reject Actual Cost

### Request
`POST /actual-cost-transactions/cost-001/reject`
```json
{
  "reason": "Lampiran bukti pembayaran tidak lengkap"
}
```

### Response
```json
{
  "success": true,
  "message": "Actual cost rejected",
  "data": {
    "id": "cost-001",
    "status": "REJECTED",
    "rejection_reason": "Lampiran bukti pembayaran tidak lengkap"
  }
}
```

---

# 8. File Uploads

## 8.1 Upload Document

### Request
`POST /projects/proj-001/files`
```json
{
  "file_type": "DOCUMENT",
  "file_category_id": "cat-invoice",
  "related_entity_type": "PROGRESS_ENTRY",
  "related_entity_id": "prog-001",
  "note": "Invoice vendor pekerjaan chipping"
}
```

### Response
```json
{
  "success": true,
  "message": "File uploaded successfully",
  "data": {
    "id": "file-001",
    "file_type": "DOCUMENT",
    "original_file_name": "invoice-chipping-juni.pdf",
    "related_entity_type": "PROGRESS_ENTRY",
    "related_entity_id": "prog-001"
  }
}
```

## 8.2 Upload Photo

### Request
`POST /projects/proj-001/files`
```json
{
  "file_type": "IMAGE",
  "file_category_id": "cat-foto-lapangan",
  "related_entity_type": "WBD_NODE",
  "related_entity_id": "node-a-01",
  "caption": "Foto progres chipping blok timur",
  "photo_date": "2026-06-10",
  "note": "Diambil pagi hari"
}
```

### Response
```json
{
  "success": true,
  "message": "Photo uploaded successfully",
  "data": {
    "id": "file-002",
    "file_type": "IMAGE",
    "caption": "Foto progres chipping blok timur",
    "photo_date": "2026-06-10"
  }
}
```

## 8.3 File Validation Error Example

### Response
```json
{
  "success": false,
  "message": "Validation failed",
  "errors": {
    "caption": [
      "Caption is required for image files"
    ],
    "photo_date": [
      "Photo date is required for image files"
    ]
  }
}
```

---

# 9. Analytics

## 9.1 Dashboard

### Request
`GET /projects/proj-001/dashboard?period=2026-06`

### Response
```json
{
  "success": true,
  "message": "Dashboard data fetched successfully",
  "data": {
    "project_id": "proj-001",
    "baseline_cost_total": 7860400000,
    "actual_cost_total_approved": 5280000000,
    "official_progress_percent": 67.8,
    "active_items_count": 18,
    "delayed_items_count": 4,
    "active_wbd_version": {
      "id": "wbdv-003",
      "version_number": 3
    }
  }
}
```

## 9.2 Gantt

### Request
`GET /projects/proj-001/gantt`

### Response
```json
{
  "success": true,
  "message": "Gantt data fetched successfully",
  "data": {
    "project_id": "proj-001",
    "baseline_version_id": "wbdv-003",
    "mode": "READ_ONLY",
    "tasks": [
      {
        "wbd_node_id": "node-a-01",
        "code": "A-01",
        "name": "Tumbang / Bongkar / Chipping",
        "start_date": "2026-06-08",
        "end_date": "2026-06-21",
        "status": "RUNNING"
      }
    ]
  }
}
```

## 9.3 S-Curve

### Request
`GET /projects/proj-001/s-curve?period_start=2026-06-01&period_end=2026-12-31`

### Response
```json
{
  "success": true,
  "message": "S-Curve data fetched successfully",
  "data": {
    "project_id": "proj-001",
    "baseline_version_id": "wbdv-003",
    "points": [
      {
        "period_label": "2026-06",
        "plan_volume_cumulative": 15.2,
        "actual_volume_cumulative": 12.8,
        "plan_cost_cumulative": 890000000,
        "actual_cost_cumulative": 930000000
      }
    ]
  }
}
```

---

# 10. Reports

## 10.1 Generate Final Report

### Request
`POST /projects/proj-001/reports/generate`
```json
{
  "report_type": "WEEKLY_PROGRESS",
  "period_start": "2026-06-08",
  "period_end": "2026-06-14",
  "filters": {
    "group_codes": ["A", "B"]
  }
}
```

### Response
```json
{
  "success": true,
  "message": "Report generated successfully",
  "data": {
    "id": "report-001",
    "project_id": "proj-001",
    "report_type": "WEEKLY_PROGRESS",
    "period_start": "2026-06-08",
    "period_end": "2026-06-14",
    "status": "FINAL",
    "file_path": "/reports/proj-001/weekly-progress-2026-06-14.pdf",
    "generated_at": "2026-06-15T08:00:00Z"
  }
}
```

---

# 11. Audit Logs

## 11.1 List Audit Logs by Entity

### Request
`GET /audit-logs/WBD_VERSION/wbdv-004`

### Response
```json
{
  "success": true,
  "message": "Audit logs fetched successfully",
  "data": [
    {
      "id": "audit-001",
      "entity_type": "WBD_VERSION",
      "entity_id": "wbdv-004",
      "action_type": "SUBMIT",
      "action_by": {
        "id": "user-pm-01",
        "name": "Project Manager Sosa"
      },
      "action_at": "2026-07-20T10:15:00Z",
      "remarks": "Mohon review revisi volume dan tarif"
    }
  ]
}
```

---

# 12. Recommended API Rules Summary
- Semua endpoint list sebaiknya mendukung pagination, filter, dan sort
- Semua approve/reject action harus punya endpoint eksplisit
- Semua response analytics hanya boleh memakai data approved/resmi
- Semua validation error harus konsisten formatnya
- Semua perubahan material harus dicatat dalam audit log
