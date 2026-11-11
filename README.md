<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/header-dark.svg" />
  <source media="(prefers-color-scheme: light)" srcset="assets/header-light.svg" />
  <img src="assets/header-light.svg" width="100%" alt="Griffins Shem Ondeyo, Full-Stack Software Engineer. I design the schema, the API and the pixels, and write the tests that prove they work." />
</picture>

<br/>

<a href="https://www.linkedin.com/in/griffins-shem-ondeyo/"><img src="https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn" /></a>
<a href="mailto:griffinsshem254@gmail.com"><img src="https://img.shields.io/badge/Email-Get%20in%20touch-D14836?style=for-the-badge&logo=gmail&logoColor=white" alt="Email" /></a>
<a href="https://github.com/Griffinsshem?tab=repositories"><img src="https://img.shields.io/badge/GitHub-Repositories-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub" /></a>

📍 Nairobi, Kenya &nbsp;·&nbsp; Open to **Full-Stack**, **Frontend** and **Backend** engineering roles, remote or on-site

</div>

---

## About me

I build multi-tenant web applications end to end: the database schema, the API, the security
model, the interface and the deployment. I care most about the parts users never notice until
they fail: tenant isolation, authentication, data integrity, honest error states and safe releases.

I trained in Software Engineering at **Moringa School**. Before that I worked in construction
site monitoring and network installation, which taught me to understand the real-world workflow
first, then design the system so the costly mistakes cannot happen.

**What you can expect in my repositories**

- **Production-minded architecture.** Typed Next.js frontends behind a server-side proxy, layered Flask APIs (routes → services → models), and PostgreSQL schemas that enforce rules rather than trusting application code.
- **Security by default.** httpOnly cookie sessions, Argon2id password hashing, CSRF protection, nonce-based CSP, rate limiting, timing-safe login and per-tenant data isolation.
- **Tests and CI as standard.** More than 1,600 automated tests across my featured projects (pytest, Vitest and Playwright), run by GitHub Actions with linting, type checks, dependency audits and secret scanning.
- **Written decisions.** Architecture Decision Records, documented trade-offs and an honest list of known limitations in every serious project.

---

## Featured projects

### 🏢 [PropFlow](https://github.com/Griffinsshem/propflow) · Property maintenance, from report to resolved
**[Live demo →](https://propflow-omega-tawny.vercel.app)** &nbsp;·&nbsp; a private sandbox per visitor, no signup

<img src="assets/demos/propflow.gif" width="100%" alt="PropFlow demo: an owner opens the demo, switches to the tenant view and reports a leaking ceiling as urgent, then switches to the manager view, where the new request is waiting to be triaged." />

Tenants report repairs from their phone, managers assign contractors, and every party follows one
auditable status trail until the job is closed. Each role (owner, manager, tenant, contractor) gets its own dashboard.

- **Security:** opaque server-side sessions, Argon2id, HMAC-based CSRF, nonce CSP, one central permission matrix enforced on every request, an append-only audit log, and Redis rate limits keyed by account and IP.
- **Correctness:** optimistic concurrency so simultaneous edits are surfaced instead of overwritten; requests and work orders run on explicit state machines.
- **Performance:** load-tested with 16,000 tenants and 64,000 requests. `EXPLAIN ANALYZE` profiling cut the owner dashboard from **36.8 ms to 17.8 ms**, and a 12,434-request load test finished with **0% failures**.
- **Quality:** **368 API tests**, **76 frontend unit tests** and **46 Playwright browser journeys** in CI, plus **21 Architecture Decision Records**.

<details>
<summary><b>How it's built: architecture and the maintenance state machine</b></summary>
<br/>

```mermaid
flowchart TB
  B[Browser] -->|HTTPS| V["Next.js on Vercel<br/>pages and /api proxy"]
  V -->|"shared proxy secret + visitor IP"| A["Flask API on Render<br/>gunicorn"]
  A --> N[("PostgreSQL on Neon")]
  A --> R[("Redis<br/>rate limits")]
  A --> W["Celery worker<br/>email, photo processing"]
  A -->|"signed upload links"| S[("Cloudflare R2<br/>photos, location data stripped")]
  B -->|"upload and view via signed links"| S
  G["GitHub Actions"] -->|"CI on every push, hourly clean-up"| N
```

Every status change goes through one transition table. Anything not listed is rejected with a `409`,
and each arrow requires a named permission from the central matrix:

```mermaid
stateDiagram-v2
  direction LR
  [*] --> submitted
  submitted --> triaged
  triaged --> assigned
  assigned --> triaged: contractor declines (reason required)
  assigned --> in_progress
  in_progress --> waiting: reason required
  waiting --> in_progress
  in_progress --> resolved
  resolved --> in_progress: reopened (reason required)
  resolved --> closed
  submitted --> closed: duplicate or invalid
  triaged --> closed: duplicate or invalid
  closed --> [*]
```

`Next.js 16` `TypeScript` `TanStack Query` `React Hook Form` `Zod` `Flask` `SQLAlchemy 2` `Celery` `PostgreSQL` `Redis` `Cloudflare R2` `Playwright`

</details>

---

### 🏫 [School Management System](https://github.com/Griffinsshem/school-management-system) · Multi-school SaaS for Kenyan schools
**[Staging →](https://school-management-system-one-peach.vercel.app)** &nbsp;·&nbsp; fictional data only, in active development

<img src="assets/demos/school-management.gif" width="100%" alt="School Management System demo: a teacher logs in, opens Grade 7 Blue, marks one student late and one absent for sickness, saves the register, then opens a student's report card with CBC grades and the attendance just recorded." />

One platform for administrators, teachers, finance staff, parents and students, with strict school-level data isolation.

- **Academics:** class registers, marks entry, CBC and KCSE grading, term results, class teachers' comments and printable report cards.
- **Finance:** fee structures per grade and term, bills, payments, receipts, statements and arrears tracking, all print-ready.
- **Security:** revocable server-side sessions (only SHA-256 token hashes stored), Argon2id tuned to OWASP guidance, three-layer CSRF defence, and the active school taken only from the server session, re-checked on every request.
- **Quality:** **770+ backend tests** and Playwright browser tests, with CI against a throwaway PostgreSQL 16 database.

<details>
<summary><b>How it's built: request path and least-privilege database</b></summary>
<br/>

```mermaid
flowchart LR
  B[Browser] --> N["Next.js on Vercel<br/>proxy adds shared secret + visitor IP"]
  N -->|"X-SMS-Proxy-Secret"| F["Flask API on Render"]
  F --> C{"Active membership<br/>in an active school?<br/>checked every request"}
  C -->|yes| D[("PostgreSQL 16")]
  C -->|no| X["403, identical for every school,<br/>so school IDs cannot be probed"]
  F -. "connects as sms_app:<br/>read and write rows only" .-> D
  M["Alembic migrations"] -. "sms_migrator:<br/>owns the schema" .-> D
```

The API's database user cannot create or alter tables, and a test proves it. The test harness also
refuses to run against any database whose name does not end in `_test`.

`Next.js 16` `React 19` `TypeScript` `Tailwind CSS 4` `Flask 3.1` `SQLAlchemy 2.1` `Pydantic` `PostgreSQL 16` `Alembic` `Playwright`

</details>

---

### 🩺 [ClinicFlow](https://github.com/Griffinsshem/clinicflow) · Appointments and follow-ups for small clinics
**[Live demo →](https://clinicflow-mu-nine.vercel.app)** &nbsp;·&nbsp; one click opens a private clinic with sample data

<img src="assets/demos/clinicflow.gif" width="100%" alt="ClinicFlow demo: a visitor opens the demo clinic, reviews the dashboard, goes to follow-ups, and completes an overdue follow-up while booking the patient's next appointment in one step." />

Built around one real failure in paper-based clinics: the patient told to come back in six weeks
who is never contacted again. Patients, appointments, follow-ups and a daily dashboard.

- **Isolation in the schema:** composite foreign keys on `(patient_id, clinic_id)` make cross-clinic records impossible to write; a scoping layer prevents cross-clinic reads, and foreign records return 404 so IDs cannot be probed.
- **Correct by construction:** "overdue" is computed per request in the clinic's own timezone instead of stored, so it is never stale and needs no background job.
- **Secure sessions:** the JWT lives in an httpOnly cookie behind a same-origin Next.js proxy, so an XSS bug cannot steal it. Login is timing-safe against account enumeration.
- **Quality:** **99 backend tests**, 15 of them dedicated to cross-tenant isolation. Production refuses to boot with placeholder secrets or wildcard CORS.

<details>
<summary><b>How it's built: request path and the one-transaction follow-up</b></summary>
<br/>

```mermaid
flowchart LR
  B[Browser] -->|"same-origin /api/proxy<br/>httpOnly session cookie"| N["Next.js on Vercel<br/>edge route guard + proxy"]
  N -->|"Bearer JWT, server to server"| F["Flask API on Render<br/>routes → services → models"]
  F -->|"every query scoped to the clinic"| P[("PostgreSQL on Neon<br/>FK (patient_id, clinic_id)")]
```

Completing a follow-up and booking the next visit is a single transaction, so a patient can never
end up with a closed follow-up and no appointment:

```mermaid
sequenceDiagram
  participant S as Clinic staff
  participant A as Flask API
  participant D as PostgreSQL
  S->>A: POST /follow-ups/:id/complete (book next visit)
  A->>D: BEGIN
  A->>D: mark follow-up completed
  A->>D: insert next appointment for the same clinic
  A->>D: COMMIT
  A-->>S: both saved, or neither
```

`Next.js 16` `TypeScript` `Tailwind CSS v4` `Flask` `SQLAlchemy 2` `Pydantic v2` `PostgreSQL 16` `Vercel` `Render` `Neon`

</details>

---

### ⚽ [Kitengela Marines FC](https://github.com/Griffinsshem/kitengela-marines) · Club website and CMS for a real client
**[Live site →](https://kitengela-marines.vercel.app)**

The official website and content management system for a Kenyan football club with men's and
women's teams. Every fixture, result, player, article and photo is managed by club staff through
an admin area; nothing on the public site is hardcoded.

- **Domain model:** 23 PostgreSQL tables covering teams, players, staff, competitions, fixtures, results, standings, news, galleries, sponsors and an audit log.
- **Authorisation:** capability-based permissions scoped per team, so one team's coach cannot edit another's squad. Access tokens live in memory only; refresh tokens sit in httpOnly cookies with CSRF double-submit protection.
- **Craft:** View Transitions that morph a player's photo into their profile, scroll-driven CSS motion that stops under `prefers-reduced-motion`, Zod-validated API responses, and every time shown in Nairobi time.
- **Quality:** **226 API tests** and **35 frontend tests** behind one quality gate (`ruff`, `mypy`, `pytest`, `eslint`, `tsc`, `vitest`, `next build`) run by a pre-commit hook.

<details>
<summary><b>How it's built: public site, admin area and audit trail</b></summary>
<br/>

```mermaid
flowchart LR
  V[Supporters] --> W["Public site on Vercel<br/>static pages, revalidated on a timer"]
  S[Club staff] --> AD["Admin area<br/>access token in memory only"]
  W -->|"responses validated with Zod"| API["Flask API on Render"]
  AD -->|"JWT + httpOnly refresh cookie + CSRF token"| API
  API --> PG[("PostgreSQL on Neon<br/>23 tables")]
  API --> CL[("Cloudinary<br/>images re-encoded, GPS stripped")]
  API -. "SQLAlchemy session listener:<br/>no endpoint can skip it" .-> AU["Audit log"]
```

Editors' HTML is sanitised against an allow-list on save and again on read, and deletes are refused
with an explanation rather than silently cascading.

`Next.js 16` `TypeScript` `Tailwind v4` `Zod` `Motion` `Flask 3` `SQLAlchemy 2` `Pydantic` `PostgreSQL` `Cloudinary` `mypy` `Vitest`

</details>

---

## Tech stack

**Languages**
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=flat-square&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=flat-square&logo=css3&logoColor=white)

**Frontend**
![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![TanStack Query](https://img.shields.io/badge/TanStack_Query-FF4154?style=flat-square&logo=reactquery&logoColor=white)
![React Hook Form](https://img.shields.io/badge/React_Hook_Form-EC5990?style=flat-square&logo=reacthookform&logoColor=white)
![Zod](https://img.shields.io/badge/Zod-3E67B1?style=flat-square&logo=zod&logoColor=white)

**Backend**
![Flask](https://img.shields.io/badge/Flask-000000?style=flat-square&logo=flask&logoColor=white)
![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-D71F00?style=flat-square&logo=sqlalchemy&logoColor=white)
![Pydantic](https://img.shields.io/badge/Pydantic-E92063?style=flat-square&logo=pydantic&logoColor=white)
![Celery](https://img.shields.io/badge/Celery-37814A?style=flat-square&logo=celery&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=nodedotjs&logoColor=white)

**Data and storage**
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![Cloudflare R2](https://img.shields.io/badge/Cloudflare_R2-F38020?style=flat-square&logo=cloudflare&logoColor=white)
![Cloudinary](https://img.shields.io/badge/Cloudinary-3448C5?style=flat-square&logo=cloudinary&logoColor=white)
![Firebase](https://img.shields.io/badge/Firebase-FFCA28?style=flat-square&logo=firebase&logoColor=black)

**Testing and quality**
![Pytest](https://img.shields.io/badge/Pytest-0A9EDC?style=flat-square&logo=pytest&logoColor=white)
![Playwright](https://img.shields.io/badge/Playwright-2EAD33?style=flat-square&logo=playwright&logoColor=white)
![Vitest](https://img.shields.io/badge/Vitest-6E9F18?style=flat-square&logo=vitest&logoColor=white)
![Ruff](https://img.shields.io/badge/Ruff-D7FF64?style=flat-square&logo=ruff&logoColor=black)
![ESLint](https://img.shields.io/badge/ESLint-4B32C3?style=flat-square&logo=eslint&logoColor=white)

**DevOps and hosting**
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)
![Vercel](https://img.shields.io/badge/Vercel-000000?style=flat-square&logo=vercel&logoColor=white)
![Render](https://img.shields.io/badge/Render-46E3B7?style=flat-square&logo=render&logoColor=black)
![Neon](https://img.shields.io/badge/Neon-00E599?style=flat-square&logo=neon&logoColor=black)
![Git](https://img.shields.io/badge/Git-F05032?style=flat-square&logo=git&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square&logo=linux&logoColor=black)

---

## Engineering practices I apply

| Area | How it shows up in my work |
|---|---|
| **Multi-tenancy** | Tenant boundaries enforced by composite foreign keys and server-side context, never by IDs sent from the client |
| **Authentication** | httpOnly, SameSite cookies; revocable server-side sessions; Argon2id; timing-safe login; per-account and per-IP rate limits |
| **API design** | Versioned REST (`/api/v1`), a consistent response envelope, strict Pydantic validation (`extra="forbid"`), and generic error messages to clients |
| **Data integrity** | Migrations tested up and down, CHECK constraints, explicit state machines, optimistic concurrency, and refused deletes instead of silent cascades |
| **Performance** | Aggregate queries instead of loading rows, `EXPLAIN ANALYZE` at realistic data volumes, and k6 load tests against latency budgets |
| **Frontend craft** | View Transitions, scroll-driven CSS motion with reduced-motion fallbacks, loading skeletons, and empty states that say why something is empty |
| **Delivery** | CI on every push, configuration validated at boot, migrations gated before traffic, and documented zero-cost deployments |

---

## More projects

| Project | Description | Stack |
|---|---|---|
| [SokoMtaani](https://github.com/Griffinsshem/Sokomtaani) | Full-stack marketplace connecting local buyers and sellers, with auth, listings, categories and favourites | Next.js · Flask · SQLite |
| [Gym Management Dashboard](https://github.com/Griffinsshem/gym-management-dashboard) | Admin dashboard for memberships, payments and attendance | Next.js · Tailwind CSS |
| [Cartify](https://github.com/Griffinsshem/Cartify) | E-commerce storefront with authentication and cart management | React · Firebase |
| [Smart Goal Planner](https://github.com/Griffinsshem/smart-goal-planner) | Savings goal tracker with milestones, deadlines and progress views | Next.js · JSON Server |

---

## Education

**Moringa School** · Software Engineering Program (2025)
Full-stack web development: JavaScript, React, Python, Flask, SQL, REST API design, testing and collaborative Git workflows.

---

<div align="center">

**I'm looking for a team that takes engineering quality seriously.**
If that's yours, let's talk: [LinkedIn](https://www.linkedin.com/in/griffins-shem-ondeyo/) · [griffinsshem254@gmail.com](mailto:griffinsshem254@gmail.com)

<sub>Demo recordings use each project's built-in demo or fictional test data. No real patient, tenant or student records appear.</sub>

</div>
