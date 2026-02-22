# Multi-Tenant Online Examination Platform

## 1. Product Overview

The system is a multi-tenant SaaS-based online examination platform designed for coaching institutes and training centers.

Institutes can create and schedule exams. Students can attempt time-bound tests with auto-save functionality. The system evaluates submissions automatically and provides performance analytics and leaderboard functionality.

The platform must support up to 500 concurrent students during peak exam windows and be designed for future scalability.

---

## 2. Functional Requirements

### 2.1 Institute/Admin Features

- Institute registration and onboarding
- Admin authentication (JWT-based)
- Create, update, delete exams
- Define exam:
    - Title
    - Duration
    - Total marks
    - Start time
    - End time
- Add questions to exam (MCQ initially)
- Define correct answers and marks
- View student submissions
- View exam results
- Download results (CSV/PDF)
- View leaderboard

---

### 2.2 Student Features

- Student registration
- Student login (JWT-based)
- Join an institute
- View active/upcoming exams
- Start exam within scheduled window
- Fetch exam questions
- Auto-save answers periodically
- Submit exam manually
- Auto-submit on exam timeout
- View results (if enabled by institute)

---

## 3. Non-Functional Requirements

### 3.1 Scalability

- Support 500 concurrent students
- Design should allow horizontal scaling
- Services must be stateless

### 3.2 Performance

- Question fetch latency < 300ms
- Auto-save latency < 300ms
- Submission processing under 2 seconds (excluding async evaluation)

### 3.3 Reliability

- No data loss during server crash
- Auto-save mechanism to prevent answer loss
- Idempotent submission handling
- Retry mechanism for failed processing

### 3.4 Security

- JWT-based authentication
- Role-based access (Admin, Student)
- Tenant-level data isolation
- Password hashing (BCrypt)
- Secure API endpoints

### 3.5 Availability

- 99% availability during exam windows
- Graceful degradation in case of service failure

---

## 4. Traffic Assumptions

- Maximum 500 concurrent students
- Each student:
    - Fetches questions once at exam start
    - Auto-saves answers every 20 seconds
    - Submits exam once
- Peak traffic occurs:
    - At exam start (question fetch spike)
    - At exam end (submission spike)

---

## 5. Multi-Tenancy Strategy

- Shared database architecture
- Each table contains a `tenant_id` column
- All queries must filter by `tenant_id`
- Composite index on `(tenant_id, primary_key)`
- No cross-tenant data access
- Tenant isolation enforced at service layer

---

## 6. Exam Lifecycle Flow

1. Institute admin creates exam
2. Exam scheduled with start and end time
3. Student logs in
4. Student starts exam session
5. System validates exam window
6. Questions fetched
7. Auto-save triggered every 20 seconds
8. Student submits exam OR auto-submit at timeout
9. Submission event triggered
10. Evaluation service processes answers
11. Score calculated
12. Leaderboard updated
13. Results made available

---

## 7. Future Enhancements (Not in MVP)

- Descriptive answer support
- Anti-cheating monitoring
- Real-time proctoring
- Analytics dashboard
- Multi-language support
- Payment integration
- Institute custom branding
- CDN-based question delivery

---

## 8. Out of Scope (Initial Version)

- Live video proctoring
- AI-based cheating detection
- Distributed exam centers
- Offline exam mode
