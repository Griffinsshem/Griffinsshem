<div align="center">

# Griffins Shem Ondeyo

### Full-Stack Software Engineer · Next.js · TypeScript · Python/Flask · PostgreSQL

I build multi-tenant web applications end to end: the database schema, the API, the security model,
the interface and the deployment, with the tests that prove each of them works.

📍 Nairobi, Kenya &nbsp;·&nbsp; Open to **Full-Stack**, **Backend** and **Frontend** engineering roles (remote or on-site)

<a href="https://www.linkedin.com/in/griffins-shem-ondeyo/"><img src="https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn" /></a>
<a href="mailto:griffinsshem254@gmail.com"><img src="https://img.shields.io/badge/Email-Get%20in%20touch-D14836?style=for-the-badge&logo=gmail&logoColor=white" alt="Email" /></a>
<a href="https://github.com/Griffinsshem?tab=repositories"><img src="https://img.shields.io/badge/GitHub-Repositories-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub" /></a>

</div>

---

## About me

I'm a full-stack engineer who cares about the parts of software users never see until they fail:
tenant isolation, authentication, data integrity, honest error states and safe deployments.
I trained in Software Engineering at **Moringa School**, and before that worked in construction
site monitoring and network installation. That background shapes how I build: understand the
real-world workflow first, then design the system so the costly mistakes cannot happen.

**What you can expect in my repositories**

- **Production-minded architecture.** Typed Next.js frontends behind a server-side proxy, layered Flask APIs (routes → services → models), and PostgreSQL schemas that enforce rules rather than trusting application code.
- **Security by default.** httpOnly cookie sessions, Argon2id/scrypt password hashing, CSRF protection, nonce-based CSP, rate limiting, timing-safe login and per-tenant data isolation.
- **Tests and CI as standard.** pytest, Vitest and Playwright suites, run on every push by GitHub Actions together with linting, type checks, dependency audits and secret scanning.
- **Written decisions.** Architecture Decision Records, documented trade-offs and an explicit list of known limitations in every serious project.

---

## Featured projects

### 🏢 [PropFlow](https://github.com/Griffinsshem/propflow) · Property maintenance platform
**[Live demo →](https://propflow-omega-tawny.vercel.app)** &nbsp;·&nbsp; private sandbox per visitor, no signup

Tenants report repairs from their phone, managers assign contractors, and every party follows one
auditable status trail until the job is closed. Role-based dashboards for owners, managers, tenants and contractors.

- **Security:** opaque server-side sessions, Argon2id, HMAC-based CSRF, nonce CSP, a central role-permission matrix enforced on every request, append-only audit log, and Redis rate limits keyed by account and IP.
- **Correctness:** optimistic concurrency versioning so simultaneous edits are surfaced instead of overwritten; maintenance requests and work orders run on explicit state machines.
- **Infrastructure:** Celery background workers, photo uploads to Cloudflare R2 through signed URLs with location data stripped, SMTP email with retries, and scheduled clean-up jobs on GitHub Actions.
- **Performance:** load-tested with 16,000 tenants and 64,000 requests. `EXPLAIN ANALYZE` profiling cut the owner dashboard from **36.8 ms to 17.8 ms**, and a 10-minute baseline load test of 12,434 requests finished with **0% failures**.
- **Quality:** **368 API tests**, **76 frontend unit tests** and **46 Playwright browser journeys** run in CI, backed by **21 Architecture Decision Records**.

`Next.js 16` `TypeScript` `TanStack Query` `React Hook Form` `Zod` `Flask` `SQLAlchemy 2` `Celery` `PostgreSQL` `Redis` `Cloudflare R2` `Playwright`

---

### 🏫 [School Management System](https://github.com/Griffinsshem/school-management-system) · Multi-school SaaS
**[Staging →](https://school-management-system-one-peach.vercel.app)** &nbsp;·&nbsp; fake data only, in active development

A multi-school platform for administrators, teachers, finance staff, parents and students, built
with strict school-level data isolation and security as its first priorities.

- **Academic workflows:** classes, subjects, student enrolment, attendance registers and summaries, assessment mark sheets, configurable grading and printable report cards.
- **Finance:** fee structures per grade and term, student bills, payments, receipts, statements and arrears tracking, with print-ready layouts.
- **Security:** revocable server-side sessions (only SHA-256 token hashes stored), Argon2id tuned to OWASP guidance, three-layer CSRF defence, and school context taken only from the server session, re-verified on every request.
- **Database discipline:** least-privilege PostgreSQL roles (the app user cannot alter the schema, and tests prove it), case-insensitive unique emails enforced by constraint, and a test harness that refuses to run against a non-test database.
- **Observability:** request IDs on every log line and response, with personal data deliberately excluded from logs.
- **Quality:** **770+ backend tests** across 54 test modules, run in GitHub Actions against a throwaway PostgreSQL 16 instance.

`Next.js 16` `React 19` `TypeScript` `Tailwind CSS 4` `Flask 3.1` `SQLAlchemy 2.1` `Pydantic` `PostgreSQL 16` `Alembic` `Playwright`

---

### 🩺 [ClinicFlow](https://github.com/Griffinsshem/clinicflow) · Appointments and follow-ups for small clinics
**[Live demo →](https://clinicflow-mu-nine.vercel.app)** &nbsp;·&nbsp; one click opens a private clinic with sample data

Built around one real failure in paper-based clinics: the patient who was told to come back in six
weeks and was never contacted again. Patients, appointments, follow-ups and a daily dashboard.

- **Tenant isolation in the schema:** composite foreign keys on `(patient_id, clinic_id)` make cross-clinic records impossible to write, and a query-scoping layer prevents cross-clinic reads. Foreign records return 404, not 403, so IDs cannot be probed.
- **Correct by construction:** "overdue" is derived per request in the clinic's own timezone rather than stored, so no background job is needed and the data never goes stale.
- **Secure sessions:** the JWT lives in an httpOnly cookie and the browser talks only to a same-origin Next.js proxy, so an XSS bug cannot steal the session. Login is timing-safe against account enumeration.
- **Efficient queries:** the dashboard runs nine aggregate SQL statements regardless of clinic size.
- **Quality:** **99 backend tests**, 15 dedicated to cross-tenant isolation. Production config refuses to boot with placeholder secrets or wildcard CORS.

`Next.js 16` `TypeScript` `Tailwind CSS v4` `Flask` `SQLAlchemy 2` `Pydantic v2` `PostgreSQL 16` `Vercel` `Render` `Neon`

---

### ⚽ [Kitengela Marines FC](https://github.com/Griffinsshem/kitengela-marines) · Club website and CMS (real client)
**[Live site →](https://kitengela-marines.vercel.app)**

The official website and content management system for a Kenyan football club with men's and
women's teams. Every word, fixture, result and photo supporters see is managed by club staff
through an admin area; nothing on the public site is hardcoded.

- **Domain model:** 23 PostgreSQL tables covering teams, players, staff, competitions, fixtures, results, standings, news, media galleries, sponsors and an audit log.
- **Authorisation:** capability-based permissions scoped per team, so a coach of one team cannot edit another team's squad. JWT access tokens are held in memory only, with refresh tokens in httpOnly cookies and CSRF double-submit protection.
- **Safe content:** an automatic audit trail via SQLAlchemy session listeners, editor HTML sanitised on save and on read, and uploads re-encoded to strip GPS metadata.
- **Frontend:** statically generated, revalidated pages; every API response validated with Zod; View Transitions and scroll-driven CSS motion that respects `prefers-reduced-motion`; and all times shown in Nairobi time.
- **Quality:** **226 API tests** and **35 frontend tests** behind one quality gate (`ruff`, `mypy`, `pytest`, `eslint`, `tsc`, `vitest`, `next build`) enforced by a pre-commit hook.

`Next.js 16` `TypeScript` `Tailwind v4` `Zod` `Motion` `Flask 3` `SQLAlchemy 2` `Pydantic` `PostgreSQL` `Cloudinary` `mypy` `Vitest`

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
| **Data integrity** | Alembic migrations tested up and down, CHECK constraints, explicit state machines, optimistic concurrency, refusing deletes instead of silent cascades |
| **Performance** | Aggregate queries over row loading, `EXPLAIN ANALYZE` at realistic data volumes, k6 load tests against latency budgets |
| **Delivery** | CI on every push, fail-fast configuration validation at boot, migrations gated before traffic, documented zero-cost deployments |
| **Documentation** | Architecture Decision Records, runbooks, and honest "known limitations" sections |

---

## More projects

| Project | Description | Stack |
|---|---|---|
| [SokoMtaani](https://github.com/Griffinsshem/Sokomtaani) | Full-stack marketplace connecting local buyers and sellers, with auth, listings, categories and favourites | Next.js · Flask · SQLite |
| [Gym Management Dashboard](https://github.com/Griffinsshem/gym-management-dashboard) | Admin dashboard for memberships, payments and attendance | Next.js · Tailwind CSS |
| [Cartify](https://github.com/Griffinsshem/Cartify) | E-commerce storefront with authentication and cart management | React · Firebase |
| [Smart Goal Planner](https://github.com/Griffinsshem/smart-goal-planner) | Savings goal tracker with milestones, deadlines and progress views | Next.js · JSON Server |

---

## GitHub activity

<div align="center">
  <img height="165" src="https://github-readme-stats.vercel.app/api?username=Griffinsshem&show_icons=true&hide_border=true&theme=github_dark&count_private=true" alt="GitHub stats" />
  <img height="165" src="https://github-readme-stats.vercel.app/api/top-langs/?username=Griffinsshem&layout=compact&hide_border=true&theme=github_dark&langs_count=8" alt="Top languages" />
  <br/>
  <img src="https://streak-stats.demolab.com/?user=Griffinsshem&theme=github-dark-blue&hide_border=true" alt="GitHub streak" />
</div>

---

## Education

**Moringa School** · Software Engineering Program (2025)
Full-stack web development: JavaScript, React, Python, Flask, SQL, REST API design, testing and collaborative Git workflows.

---

<div align="center">

**I'm looking for a team that takes engineering quality seriously.**
If that's yours, let's talk: [LinkedIn](https://www.linkedin.com/in/griffins-shem-ondeyo/) · [griffinsshem254@gmail.com](mailto:griffinsshem254@gmail.com)

</div>
