# MediReg Connect

Build a full-stack, production-grade web application called MediReg — 

a professional patient registration system with biometric support, 

cloud sync, staff account management, and enterprise-level security. 

The app must be built Electron-ready so it can be packaged as a 

Windows desktop installer (.exe) after export. Use React, Tailwind CSS, 

Shadcn/UI components, and Supabase for auth, database, and storage.

---

APP NAME: MediReg

TAGLINE: "Smart Patient Registration, Simplified."

COLOR SCHEME: Deep blue (#1E3A5F) primary, white background, 

green (#22C55E) accents for success states, red (#EF4444) for errors.

---

## ELECTRON / WINDOWS DESKTOP READINESS

- Build the entire app as a Single Page Application (SPA) with 

  React Router using hash-based routing (#/) so it works correctly 

  when loaded as a local file inside Electron (file:// protocol)

- Do NOT use server-side rendering or any Next.js features — 

  pure client-side React only

- All API calls must use absolute URLs pointing to Supabase so 

  they work both in browser and inside Electron wrapper

- Avoid any browser-only APIs that Electron does not support

- All assets (fonts, icons, images) must be bundled locally, 

  no external CDN dependencies at runtime

- The app must work fully offline for data entry and sync to 

  Supabase when internet is available

- Include a clear README.md with instructions on how to wrap 

  this React app in Electron using electron-builder to produce 

  a Windows .exe installer

- Window dimensions should be optimized for 1280x800 minimum 

  desktop resolution

---

## SECURITY — ENTERPRISE GRADE

Implement the following security measures throughout the entire app:

### Authentication Security

- Supabase Auth with email and password login only

- Enforce strong password policy: minimum 10 characters, 

  must include uppercase, lowercase, number, and special character

- Implement rate limiting on login: lock account for 15 minutes 

  after 5 failed login attempts, show countdown timer

- Auto logout after 15 minutes of inactivity with a 60-second 

  warning modal before logout

- All sessions use short-lived JWT tokens (1 hour) with 

  automatic refresh

- Never store passwords or tokens in localStorage — use 

  httpOnly cookies or Supabase session management only

- Two-Factor Authentication (2FA) option for Admin accounts

### Data Security

- All data transmitted to Supabase over HTTPS/TLS only — 

  never plain HTTP

- Enable Supabase Row Level Security (RLS) on every single 

  table so users can only access data they are authorized to see

- Registrars can only read/write patient records — cannot 

  access staff management or system settings

- Supervisors can read all records but cannot edit or delete

- Admins have full access

- Patient photos and files stored in Supabase Storage with 

  private bucket — never publicly accessible URLs

- All file URLs must be signed URLs that expire after 1 hour

- Encrypt all sensitive fields (ID numbers, phone numbers) 

  at the application level before storing in the database

- Never log sensitive patient data to the browser console

- Implement Content Security Policy (CSP) headers

### Input Validation & Attack Prevention

- Sanitize ALL user inputs on both client and server side 

  before any database operation

- Protect against SQL injection — use Supabase parameterized 

  queries only, never raw SQL string concatenation

- Protect against XSS (Cross-Site Scripting) — escape all 

  rendered user-generated content, never use dangerouslySetInnerHTML

- Protect against CSRF attacks — validate request origins

- Validate all file uploads: only allow image formats 

  (jpg, png, webp), maximum file size 5MB, check MIME type 

  not just extension

- Reject any input containing script tags, SQL keywords, 

  or executable code patterns

- All form fields have maximum character limits enforced

### Audit & Monitoring

- Log every action to an audit_logs table: who did what, 

  when, from which session, on which record

- Log all login attempts (successful and failed) with 

  timestamp and session info

- Admins can view the full audit log in the Staff Management section

- Flag and alert if the same patient ID number is registered twice

- Flag suspicious activity: more than 50 registrations in 

  one hour by one user

### Data Backup & Recovery

- All data stored in Supabase with automatic daily backups

- Show last sync timestamp in the app footer

- If Supabase connection fails, show a clear banner: 

  "Working offline — data will sync when connection is restored"

- Cache form progress in encrypted sessionStorage so data 

  is not lost if app crashes mid-registration

---

## AUTHENTICATION & USER ACCOUNTS

- Login page with MediReg logo, email and password fields, 

  show/hide password toggle

- Staff roles: Admin, Registrar, Supervisor

- Admin can create, deactivate, and manage staff accounts

- Each staff account has: Full Name, Email, Role, Phone, 

  Profile Photo, Date Created, Last Login, Status

- Password reset via email link

- First-time login forces password change

- On login show a dashboard with total patients registered 

  today, total patients overall, recent registrations, and 

  logged-in staff info in the top right corner with avatar

---

## DASHBOARD

- Top stats cards: Total Patients, Registered Today, 

  Pending Reviews, Active Staff

- Recent Registrations table: Patient ID, Full Name, 

  Date Registered, Registered By, Status

- Quick action button: Register New Patient

- Sidebar navigation: Dashboard, Register Patient, Patient 

  Records, Staff Management (Admin only), Reports, 

  Audit Logs (Admin only), Settings, Logout

- MediReg logo displayed on sidebar top

- License status badge in sidebar bottom: shows days remaining

---

## PATIENT REGISTRATION — MULTI-STEP FORM

Broken into clearly labeled steps with a progress bar at top. 

Every required field validated before moving to next step. 

If a required field is empty or invalid, block progression 

and show a red error message under that specific field. 

Optional fields clearly labeled "(optional)".

---

### STEP 1 — PERSONAL INFORMATION

All required unless stated:

- Title (dropdown): Mr, Mrs, Miss, Dr, Prof, Rev, Other

- First Name (text, max 50 chars)

- Middle Name (text, max 50 chars) (optional)

- Last Name (text, max 50 chars)

- Date of Birth (date picker) — must not be future date, 

  patient must be at least 1 day old, maximum age 120 years

- Age (auto-calculated from DOB, read-only display)

- Gender (dropdown): Male, Female, Other

- Marital Status (dropdown): Single, Married, Divorced, 

  Widowed, Separated

- Religion (dropdown): Christianity, Islam, Hinduism, 

  Buddhism, Traditional, Other, Prefer not to say

- Blood Group (dropdown): A+, A-, B+, B-, AB+, AB-, O+, O-

- Occupation (text, max 100 chars)

- Employer or School Name (text) (optional)

---

### STEP 2 — CONTACT DETAILS

All required unless stated:

- Primary Phone Number — validate: numbers only, 

  minimum 10 digits, maximum 15 digits

- Secondary Phone Number (optional)

- Email Address (optional) — validate format if entered

- Residential Address / Street (text, max 200 chars)

- City or Town (text)

- Region / County / State (text)

- Country of Residence (dropdown — full list of all countries)

- Postal Code (optional)

---

### STEP 3 — IDENTITY & NATIONALITY

All required unless stated:

- Nationality (dropdown — full list of nationalities)

- Country of Birth (dropdown — full list of countries)

- Place of Birth / City (text)

- ID Document Type (dropdown): National ID, Passport, 

  Driver's License, Birth Certificate, Alien ID, Other

- ID Number — validate: no spaces, min 5 chars, max 30 chars, 

  check for duplicate ID number in database before proceeding, 

  show error if duplicate found: "This ID number is already 

  registered in the system"

- ID Issue Date (date picker)

- ID Expiry Date (date picker) (optional)

- Ethnicity or Tribe (text) (optional)

- Disability Status (dropdown): None, Physical, Visual, 

  Hearing, Cognitive, Other (optional)

- Any Known Allergies (textarea, max 500 chars) (optional)

---

### STEP 4 — NEXT OF KIN

All required unless stated:

- Next of Kin Full Name

- Relationship to Patient (dropdown): Spouse, Parent, 

  Child, Sibling, Guardian, Friend, Other

- Next of Kin Primary Phone — validate format

- Next of Kin Secondary Phone (optional)

- Next of Kin Email (optional)

- Next of Kin Residential Address

- Next of Kin City

- Next of Kin Country (dropdown)

Secondary Next of Kin — collapsible section, all optional:

- Full Name, Relationship, Phone, Email

---

### STEP 5 — MEDICAL HISTORY

Label entire section as optional:

- Primary Doctor / Physician Name (optional)

- Referring Institution (optional)

- Existing Medical Conditions (textarea) (optional)

- Current Medications (textarea) (optional)

- Past Surgeries (textarea) (optional)

- Smoking Status (dropdown): Non-smoker, Former smoker, 

  Current smoker (optional)

- Alcohol Use (dropdown): None, Occasional, Regular (optional)

- Insurance Provider (optional)

- Insurance Policy Number (optional)

---

### STEP 6 — PHOTO & BIOMETRICS

- Patient Photo: webcam capture component with 

  "Capture Photo" button and "Retake" option. Also allow 

  file upload as alternative. REQUIRED — block progression 

  if no photo. Validate file type and size on upload.

- Fingerprint: placeholder scanner UI with "Scan Fingerprint" 

  button, success animation when complete. REQUIRED.

- Signature Pad: digital signature capture with "Clear" 

  button. REQUIRED.

---

### STEP 7 — REVIEW & SUBMIT

- Full summary of all entered data grouped by section

- Each section has an "Edit" button to return to that step

- Patient photo displayed prominently

- Checkbox: "I confirm all information entered is correct 

  and complete" — must be checked before submitting

- On submit: loading spinner then success screen with:

  - Auto-generated Patient ID (format: MR-YYYY-XXXXXX)

  - Patient full name and photo

  - Date and time of registration

  - Name of registrar (from logged-in session)

  - Buttons: "Print Registration Card", 

    "Register Another Patient", "View Patient Record"

---

## PATIENT RECORDS PAGE

- Searchable and filterable table of all patients

- Search by: Name, Patient ID, Phone, ID Number

- Filter by: Gender, Date Range, Registered By, Nationality

- Columns: Photo thumbnail, Patient ID, Full Name, DOB, 

  Gender, Phone, Nationality, Date Registered, Actions

- Actions: View, Edit, Print, Deactivate

- Patient detail view in clean card layout grouped by sections

- Pagination: 20 records per page

- Export to CSV button (Admin only)

---

## PRINTING

On "Print Registration Card" generate a clean printable 

PDF/print layout:

- MediReg logo top left

- Facility name and date top right

- Patient photo top right of card

- All bio data in labeled sections

- Patient ID with QR code

- Footer: Registered by (staff name), Date, 

  "Powered by MediReg"

- Auto-trigger browser print dialog on button click

---

## STAFF MANAGEMENT (Admin only)

- Table: Name, Email, Role, Status, Date Created, Last Login

- Add New Staff: Full Name, Email, Role, Phone, Temp Password

- Deactivate / Reactivate accounts

- Edit roles

- View activity log per staff member

- View full audit log table with filters by date, user, action

---

## LICENSING SYSTEM

- App checks a license key stored in Supabase settings table 

  on every launch

- License has: key, expiry date, facility name, max users

- If license is valid: app runs normally, show green badge 

  "License Active — X days remaining" in sidebar

- If license expires in 7 days: show yellow warning banner 

  on dashboard daily

- If license is expired: lock the entire app except the 

  settings page, show a full-screen modal: "Your MediReg 

  license has expired. Please contact your administrator 

  to renew." with a contact support button

- License keys validated against a remote endpoint 

  (use a Supabase Edge Function for this)

- Admin can enter and update the license key in Settings

---

## SETTINGS PAGE

- Facility Name (editable)

- Facility Logo upload (used in printed cards)

- Facility Address

- License Key input and status

- Theme toggle: Light / Dark mode

- Change password for logged-in user

- Data retention policy settings (Admin only)

---

## GENERAL UI REQUIREMENTS

- Fully responsive, optimized for 1280x800 desktop minimum

- Deep blue (#1E3A5F) and white with green (#22C55E) accents

- Smooth step transitions with animated progress bar

- Loading spinners on all async operations

- Toast notifications for success, error, and warning events

- Empty state illustrations on tables with no data

- Consistent card-based layout throughout

- MediReg branding on all major pages

- Sidebar collapses to icons on smaller screens

- All modals have proper focus trapping for accessibility

- Keyboard navigable throughout

This project was built with [Lovable](https://lovable.dev).

## Build with Lovable

Continue developing this project in the [Lovable editor](https://lovable.dev/projects/9249fa33-13f2-4ab0-ad9c-32bde53dc125).

- **Ship faster**: describe what you want to build and Lovable handles the code.
- **Stay in sync**: every change made in Lovable is committed straight to this repository.
- **Full ownership**: this code is yours. Push to `main` on GitHub and your changes sync back into Lovable, ready for your next prompt.

## Development

Prefer working locally? You need Node.js and npm — [install with nvm](https://github.com/nvm-sh/nvm#installing-and-updating).

```sh
git clone <this-repository-url>
cd <repository-name>
npm i
npm run dev
```
