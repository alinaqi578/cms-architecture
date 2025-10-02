# ADR 002 — Technology Choices for Complaint Management System (CMS)

**Status:** Accepted  
**Date:** 2 October 2025  
**Decision ID:** ADR-002

## Context and Problem Statement
Following ADR-001 (Layered Architecture), we must select concrete technologies for the Presentation, Business/Application, and Data Access layers, as well as supporting tools for testing, deployment, logging, and observability. Choices must be pragmatic, lightweight, and aligned with the assessment’s functional requirements (multi‑role CMS, notifications, status tracking) and quality attributes (maintainability, testability, evolvability).

## Decision Drivers
- Keep the solution simple and teachable, with minimal operational overhead.
- Fit a traditional **web-first** delivery (accessible via browsers) while leaving a path to expose APIs for future mobile apps.
- Use well-known, well-documented tools that run reliably on typical university/SME hosting (LAMP/Ubuntu).
- Support unit testing and CI, with clear folder separation per ADR‑001.
- Enable quick deployment and small-team maintainability.

## Considered Options (by area)

### A. Front End
1. **Server-rendered HTML + Bootstrap 5 + Vanilla JS (fetch/AJAX)**
2. Single Page App (**React**) + REST API
3. **Server-rendered HTML + Alpine.js** for small interactivity

### B. Server-Side Application
1. **PHP 8.2 (MVC style) with Composer packages (lightweight)** — e.g., Slim microframework or custom minimal routing
2. **Laravel 10 (PHP 8.2)**
3. Node.js (Express) or Python (Django/Flask)

### C. Database
1. **MySQL 8.0**
2. PostgreSQL 15+
3. SQLite for single-user/dev only

### D. Deployment & Ops
1. **LAMP on Ubuntu (Apache + PHP 8.2 + MySQL)** — managed VM/shared hosting
2. Docker Compose (php-apache, mysql, phpmyadmin) on a VM
3. PaaS (e.g., Render/Heroku-like) with managed DB

### E. Testing, Tooling, and Support
- **PHPUnit** for unit tests (Business/Application layer)
- **PHPStan** (level 6) for static analysis
- **Monolog** for structured logging
- **Dotenv** for configuration management
- **Phinx** (or Laravel migrations if using Laravel) for DB migrations
- **GitHub Actions** for CI (lint + tests) and optional deploy
- **PHPMailer** for SMTP notifications
- **Redis (optional)** for caching sessions/queues if needed later

## Decision Outcome
**Chosen stack:**

- **Front End:** Server-rendered HTML with **Bootstrap 5**, **Vanilla JS** (fetch/AJAX).  
  _Reason:_ Fast to implement, accessible by default, small bundle size, no SPA complexity. Enhancements can use **Alpine.js** for light interactivity where helpful.

- **Server-Side Application:** **PHP 8.2**, MVC-style layering (controllers → application services/use-cases → repositories).  
  - **Framework:** Lightweight (**Slim** microframework) or custom minimal router with Composer autoload.  
  - **Key packages:** `phpmailer/phpmailer`, `monolog/monolog`, `vlucas/phpdotenv`, `robmorgan/phinx` (migrations), `phpunit/phpunit`, `phpstan/phpstan`.

- **Database:** **MySQL 8.0** with a normalized schema for complaints, users, roles, assignments, status history, and notifications.  
  - Access via repository interfaces (PDO); prevent SQL injection with prepared statements.  
  - Migrations managed by **Phinx**.

- **Authentication & Authorization:** **PHP sessions** + `password_hash()`/`password_verify()` for credentials; **RBAC** roles (`CONSUMER`, `AGENT`, `SUPERVISOR`, `ADMIN`). (Detailed in ADR‑003.)

- **Deployment:** **Ubuntu LAMP** (Apache + PHP-FPM 8.2 + MySQL).  
  - Environment config via `.env`.  
  - **GitHub Actions** to run tests/static analysis on push.  
  - Manual or rsync/FTP deploy for simplicity; optional Docker Compose for parity in dev.

- **Observability & Logging:** **Monolog** (file handlers per environment).  
  - Error pages and centralized error handler at the Presentation layer.

- **APIs (Future-proofing):** Expose REST endpoints (JSON) for key use-cases (create complaint, get status, post update). Version under `/api/v1/`.

## Consequences

### Positive
- **Low complexity**: Server-rendered pages + light JS means less build tooling and fewer moving parts.
- **Maintainable**: Clear layering maps to folders/modules; Business logic remains testable.
- **Deployable anywhere**: Works on common LAMP environments and commodity VMs/hosting.
- **Future-ready**: REST endpoints allow mobile client integration later without rewrites.

### Negative
- **Not a SPA**: Real-time UX is limited to AJAX/polling unless we add WebSockets or SSE later.
- **Slim/custom MVC**: A lighter approach means fewer batteries-included helpers vs Laravel; requires conventions and documentation.
- **MySQL-specific features**: Ties to MySQL unless repositories are abstracted fully.

## Alternatives and Rationale

### Front End
- **SPA (React)** offers rich UX and client-side routing, but adds complexity (bundlers, API auth, CORS, hydration). The CMS’s core flows (submit complaint, view status, agent triage, updates) do not require SPA-level interactivity at this stage.

### Server-Side
- **Laravel** is productive but heavier for the current scope; Slim/custom MVC keeps learning curve and infra smaller while preserving good structure.  
- **Node/Django** are strong options but add language/runtime switches that don’t align with a simple LAMP deployment target.

### Database
- **PostgreSQL** is excellent, but **MySQL** is ubiquitous in shared and small VM hosting and is fully adequate for relational CMS needs.  
- **SQLite** is for local/dev only, not multi-user production.

### Deployment
- **Docker** is great for parity; we may add it later. For now, a straightforward **LAMP** stack reduces operational load and aligns with common hosting environments.

## Implementation Notes (initial folder map)
```
/app
  /Controllers        # Presentation layer: controllers, request/response mapping
  /Views              # Templates (server-rendered)
  /Services           # Application services/use-cases (business orchestration)
  /Domain             # Entities, value objects, policies
  /Repositories       # Interfaces + PDO implementations (Data Access)
/public               # index.php, assets, front controller
/config               # env/bootstrap, container wiring
/database/migrations  # Phinx migrations
/tests                # PHPUnit tests (focus on Services/Domain)
```

## Related Decisions
- ADR‑001 — Architectural Style (Layered Architecture)
- ADR‑003 — Authentication & Authorization approach (details and flows)
- ADR‑004 — Integration mechanisms (email/SMS notifications, webhooks)

## References
- Bootstrap: https://getbootstrap.com/
- Slim Framework: https://www.slimframework.com/
- PHPUnit: https://phpunit.de/
- PHPStan: https://phpstan.org/
- Phinx: https://book.cakephp.org/phinx/
- Monolog: https://github.com/Seldaek/monolog
- PHPMailer: https://github.com/PHPMailer/PHPMailer
