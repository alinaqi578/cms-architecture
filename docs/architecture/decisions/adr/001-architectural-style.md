# ADR 001 — Architectural Style for Complaint Management System (CMS)

**Status:** Accepted  
**Date:** 2 October 2025  
**Decision ID:** ADR-001

## Context and Problem Statement
ABC Limited requires a web-based Complaint Management System (CMS) that supports multiple actor roles (consumer, help desk agent, supervisor/manager), provides timely status updates and notifications, and can evolve as new channels and integrations are added (e.g., mobile app, chatbot, analytics).

We need an architectural style that promotes clear separation of concerns, maintainability, and testability, while allowing straightforward deployment today and a path to scale later. This ADR focuses only on the architectural style; technology choices (frameworks, libraries, databases) are documented in separate ADRs.

## Decision Drivers
- Separation of concerns between UI, business rules, and data access
- Maintainability and ease of onboarding for future contributors
- Testability of domain logic independent of UI or storage
- Straightforward initial deployment with a path to scale (e.g., evolve to 3‑tier or services later)
- Alignment with assessment brief, case study, user stories, use cases, and the C4 System Context

## Considered Options
1. **Layered Architecture** (Presentation, Business Logic/Application, Data Access)
2. **Three-Tier Architecture** (UI tier, Application tier, Data tier on separate nodes)
3. **Client–Server Architecture** (thin client / thick server without strict layering)
4. **Service-Oriented Architecture (SOA) / Microservices**
5. **Generic monolithic web application** (no explicit layering)

## Decision Outcome
**Chosen:** **Layered Architecture** (logical 3-layer model: Presentation, Business Logic/Application, Data Access).

### Rationale
- Provides the clearest separation of concerns and supports modular growth of CMS features such as complaint intake, triage, assignment, resolution, status updates, and customer feedback.
- Keeps domain and application rules independent from UI and persistence details, improving testability.
- Enables a clean evolution path: the logical layers can later map to physical tiers (e.g., separate app and DB servers) or be extracted into services where justified.
- Aligns with the C4 System Context interactions and role boundaries defined in the assessment materials.

## Consequences

### Positive
- **Maintainability & testability:** Domain logic can be unit-tested without UI or database.
- **Modularity:** Features and cross‑cutting concerns (auth, logging, validation) can be added at appropriate layers.
- **Evolution path:** Can scale to 3‑tier or services in future without redesigning the whole system.

### Negative
- **Discipline required:** Team must respect layer boundaries to avoid logic in the UI or leaky data access concerns.
- **Initial refactoring effort:** Some code may need reorganizing into clear layer modules/packages.

## Pros and Cons of the Options

### 1. Layered Architecture
- **Pros:** Strong separation of concerns; testable; familiar to most web dev teams; easy to onboard; clear mapping to C4 containers/components.
- **Cons:** Requires governance to prevent layer violations; adds some structural overhead.

### 2. Three-Tier Architecture
- **Pros:** Operational scalability; can allocate resources independently per tier.
- **Cons:** Higher infra/ops complexity for the current scope; not necessary until load justifies it.

### 3. Client–Server Architecture
- **Pros:** Simple to implement and deploy; low ceremony.
- **Cons:** Weaker separation of concerns; harder to maintain/test as complexity grows.

### 4. SOA / Microservices
- **Pros:** Strong autonomy and independent scaling of services; tech heterogeneity.
- **Cons:** Operational overhead, distributed complexity, and premature for current scope.

### 5. Generic Monolith (no explicit layering)
- **Pros:** Fast to start.
- **Cons:** Becomes brittle as features and team size grow; poor testability and maintainability.

## Decision
Adopt **Layered Architecture** with three logical layers:
- **Presentation Layer:** Web UI/controllers, request/response mapping, DTOs/view models.
- **Business/Application Layer:** Use-cases, domain services, validation, orchestration, policies.
- **Data Access Layer:** Repositories/DAOs, persistence mapping, external data gateways.

## Compliance & Next Steps
- Establish folders/modules for each layer and define interface contracts between layers.
- Introduce automated tests focused on the Business/Application layer.
- Document cross‑cutting patterns (logging, error handling, validation) and where they live.
- Reflect this decision in the C4 Container/Component diagrams (L2/L3).

## Related Decisions
- ADR-002 — Authentication & Authorization approach (planned)
- ADR-003 — Technology stack selection (planned)
- ADR-004 — Integration mechanisms for notifications and external services (planned)

## References
- About MADR: https://adr.github.io/madr/  
- MADR templates: https://github.com/adr/madr/tree/4.0.0/template
