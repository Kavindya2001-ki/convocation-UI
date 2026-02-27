# General Convocation Management System
### University of Peradeniya

A web-based management system for handling the end-to-end workflow of the **General Convocation** at the University of Peradeniya, Sri Lanka. The system manages student registration, payment processing, convocation photo distribution, certificate tracking, and comprehensive reporting — serving administrators, students, and support staff.

---

## Table of Contents

- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [System Architecture](#-system-architecture)
- [User Roles](#-user-roles)
- [Application Workflow](#-application-workflow)
- [Supported Faculties](#-supported-faculties)
- [Project Structure](#-project-structure)

---

## Features

### Student Portal
- **Secure Login** — Email-based authentication with password toggle and form validation
- **Registration Form** — First-time registration with contact details, attendance preference (present/absent), companion details (up to 2 persons with NIC), prize details, and special needs
- **Edit Submission** — Students can edit registration details within the admin-configured deadline window
- **Application PDF Download** — Generate and download convocation application as a PDF (powered by Dompdf)
- **Payment Voucher Upload** — Upload payment voucher images (JPG/PNG/GIF) with cancel & re-upload support
- **Online Payment** — Direct link to University of Peradeniya's IPG payment gateway
- **Convocation Photo Download** — Download official convocation photos (available after payment confirmation and faculty-level enablement)
- **Print Application** — Browser-based print functionality for the submitted application form

### Admin Dashboard
- **Student Management** — View, search, filter, edit, and delete student records with dynamic column selection
- **Bulk Upload** — CSV-based bulk upload of student data with automatic sync to the photo management table
- **Manual Entry** — Add individual student records through a form interface
- **Summary Reports** — Faculty-wise summary showing present/absent/not-confirmed counts with auto-refresh
- **Report Generation** — Filterable reports with Excel export capability (includes NIC, C&G, Signature columns)
- **Certificate Management** — CSV bulk upload and individual toggle for certificate status per student
- **Deadline Management** — Configure registration start/end dates with countdown timer display
- **Update Login Details** — Manage student login credentials
- **Data Statistics** — Modal-based quick stats overview
- **Bulk Delete** — Transaction-safe bulk deletion (removes from both student and photo tables)

### Photo Admin Dashboard
- **Quick Stats Overview** — Total students, photos uploaded, pending/completed payments at a glance
- **Payment Management** — Mark individual or all payments as paid via AJAX
- **Seat Number Assignment** — Individual or bulk (CSV/Excel) seat number upload with 4-digit formatting and automatic physical file rename
- **Faculty Status Toggle** — Enable/disable photo downloads per faculty
- **Photo File Management** — Organized storage by year and faculty folder codes

### Service Admin Dashboard
- **Arrears Management** — CSV bulk upload or AJAX individual toggle for student arrears status
- **Report Access** — Access to report generation and filtered views

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| **Backend** | PHP 8.1 (Procedural) |
| **Database** | MariaDB 10.4 / MySQL (via `mysqli`) |
| **Frontend** | Bootstrap 5.3, Font Awesome 6.4 |
| **JavaScript** | jQuery 3.6, Vanilla JS |
| **PDF Generation** | [Dompdf](https://github.com/dompdf/dompdf) v3.1 |
| **Server** | XAMPP (Apache + MySQL + PHP) |
| **Payment Gateway** | University of Peradeniya IPG |

---

## System Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                     Web Browser (Client)                     │
│         Bootstrap 5 + Font Awesome + jQuery + JS             │
└──────────────────┬───────────────────────────────────────────┘
                   │ HTTP Requests
┌──────────────────▼───────────────────────────────────────────┐
│                     Apache (XAMPP)                            │
│              PHP 8.1 - Procedural Architecture               │
│                                                              │
│  ┌─────────┐ ┌──────────┐ ┌────────────┐ ┌──────────────┐  │
│  │  index   │ │  home    │ │   admin    │ │  adminphotos │  │
│  │  (Login) │ │  (Reg.)  │ │ (Dashboard)│ │  (Photos)    │  │
│  └────┬─────┘ └────┬─────┘ └─────┬──────┘ └──────┬───────┘  │
│       │            │             │               │           │
│  ┌────▼────────────▼─────────────▼───────────────▼────────┐  │
│  │              Shared Components                          │  │
│  │  dbconfig.php | header.php | footer.php | year.php      │  │
│  │  session_status_check.php | sync_helper.php             │  │
│  └────────────────────┬───────────────────────────────────┘  │
└───────────────────────┼──────────────────────────────────────┘
                        │ mysqli
┌───────────────────────▼──────────────────────────────────────┐
│                  MariaDB / MySQL                             │
│                                                              │
│  ┌──────────────────┐  ┌────────────┐  ┌────────────────┐   │
│  │ convocation_2023 │  │  con_photo  │  │ uopfaculties   │   │
│  │ (Students/Users) │  │  (Photos)   │  │ (Faculty Conf) │   │
│  └──────────────────┘  └────────────┘  └────────────────┘   │
│  ┌──────────────────┐  ┌────────────────────┐               │
│  │    deadlines     │  │   conv_deadlines   │               │
│  │ (Reg. Periods)   │  │ (Extended Periods) │               │
│  └──────────────────┘  └────────────────────┘               │
└──────────────────────────────────────────────────────────────┘

File System:
┌──────────────────────────────────────────────────────────────┐
│  convocation images/{year}/{faculty_code}/{seatno}.{ext}     │
│  general convocation/uploads/{student_id}.{ext}              │
└──────────────────────────────────────────────────────────────┘
```

---

## User Roles

| Role | Description | Key Capabilities |
|------|-------------|-----------------|
| **Admin** | System administrator | Full CRUD on student records, bulk upload/delete, report generation, deadline management, certificate management, login credential management |
| **Student** | Graduating student | Self-registration, edit submission (within deadline), upload payment voucher, download application PDF, download convocation photo |
| **Photo Admin** | Photo & payment coordinator | Payment status management, seat number assignment, faculty-level photo download toggle, quick statistics |
| **Service Admin** | Service department staff | Arrears management, report access |

---

## Application Workflow

```
 ┌─────────────────────────────────────────────────────────────────────┐
 │ 1. SETUP                                                           │
 │    Admin uploads student data (CSV) → convocation_2023 + con_photo │
 │    Admin configures registration deadlines                         │
 └──────────────────────────┬──────────────────────────────────────────┘
                            ▼
 ┌─────────────────────────────────────────────────────────────────────┐
 │ 2. STUDENT REGISTRATION                                            │
 │    Student logs in → Fills registration form                       │
 │    (Contact, Address, Attendance, Companions, Prizes, Needs)       │
 │    Status changes: new → success                                   │
 └──────────────────────────┬──────────────────────────────────────────┘
                            ▼
 ┌─────────────────────────────────────────────────────────────────────┐
 │ 3. STUDENT SELF-SERVICE                                            │
 │    ├── Print / Download Application PDF                            │
 │    ├── Upload Payment Voucher                                      │
 │    ├── Edit Registration (within deadline window)                  │
 │    └── Download Convocation Photo (after payment + faculty enable) │
 └──────────────────────────┬──────────────────────────────────────────┘
                            ▼
 ┌─────────────────────────────────────────────────────────────────────┐
 │ 4. ADMIN PROCESSING                                                │
 │    ├── Service Admin: Manage arrears (CSV or individual toggle)    │
 │    ├── Photo Admin: Verify payments, assign seat numbers           │
 │    │   Enable/disable photo downloads per faculty                  │
 │    ├── Admin: Manage certificates (CSV or individual toggle)       │
 │    └── Admin: Generate reports (summary, detailed, Excel export)   │
 └─────────────────────────────────────────────────────────────────────┘
```

---

## 🏛 Supported Faculties

| # | Faculty | Photo Folder Code |
|---|---------|-------------------|
| 1 | Faculty of Agriculture | `AGR` |
| 2 | Faculty of Allied Health Sciences | `AHS` |
| 3 | Faculty of Arts | `ART` |
| 4 | Faculty of Dental Sciences | `DEN` |
| 5 | Faculty of Engineering | `ENG` |
| 6 | Faculty of Management | `MGT` |
| 7 | Faculty of Medicine | `MED` |
| 8 | Faculty of Science | `SCI` |
| 9 | Faculty of Veterinary Medicine & Animal Science | `VET` |
| 10 | Postgraduate Institute of Agriculture | `PGIA` |
| 11 | Postgraduate Institute of Humanities and Social Sciences | `PGIHS` |
| 12 | Postgraduate Institute of Science | `PGIS` |

---

## Project Structure

```
general-convocation-master/
│
├── composer.json                    # PHP dependencies (Dompdf)
├── composer-setup.php               # Composer installer
│
├── convocation images/              # Convocation photo storage
│   ├── 2022/                        #   Organized by year
│   ├── 2023/                        #   └── Faculty folder codes
│   └── 2024/                        #       └── {seatno}.{ext}
│       ├── AGR/
│       ├── AHS/
│       ├── ART/
│       ├── MGT/
│       ├── SCI/
│       └── VET/
│
├── screenshots/                     # Application screenshots
│
└── general convocation/             # Main application
    │
    ├── index.php                    # Login page
    ├── logincheck.php               # Authentication handler
    ├── logout.php                   # Session destruction
    ├── dbconfig.php                 # Database connection config
    ├── year.php                     # Dynamic year resolver
    ├── session_status_check.php     # Session validation
    │
    ├── home.php                     # Student registration / edit form
    ├── stud.php                     # Student dashboard (post-registration)
    ├── fetch_saved.php              # Fetch student + seat data
    ├── edit_student.php             # Student self-edit handler
    │
    ├── admin.php                    # Admin dashboard
    ├── stdReport.php                # Student report (search/filter)
    ├── export_stdReport.php         # Excel export
    ├── summaryReport.php            # Faculty-wise summary
    ├── reportgeneration.php         # Filterable report generation
    ├── uploadci&a.php               # CSV bulk upload + manual entry
    ├── deletestudentrow.php         # Single/bulk student deletion
    ├── edit.php                     # Admin edit student
    ├── certificate.php              # Certificate status management
    │
    ├── manage_deadlines.php         # Deadline configuration UI
    ├── save_deadlines.php           # Deadline persistence
    ├── registration_deadline.php    # Deadline helper functions
    ├── check_registration.php       # Registration diagnostic tool
    │
    ├── payslip.php                  # Payment voucher upload
    ├── paymentstatus.php            # Payment management (Photo Admin)
    ├── upload_payment_details.php   # Bulk payment CSV upload
    ├── paidvoucher.php              # Legacy paid voucher view
    │
    ├── adminphotos.php              # Photo admin dashboard
    ├── download_photo.php           # Secure photo download handler
    ├── photo_download.php           # Legacy photo download page
    ├── upload_seat_numbers.php      # Bulk seat number upload
    │
    ├── serviceadmin.php             # Service admin dashboard
    ├── arreas.php                   # Arrears management
    │
    ├── download_application_pdf.php # PDF generation (Dompdf)
    ├── sync_helper.php              # Data sync utilities
    │
    ├── header.php                   # Shared header / navbar
    ├── footer.php                   # Shared footer
    │
    ├── css/
    │   └── style.css                # Custom styles (maroon theme)
    ├── img/                         # Logo & assets
    ├── db/
    │   └── convocation_2023.sql     # Legacy SQL dump
    └── photoadmin/
        └── stdphotos.php            # Photo admin sub-page
```

---
