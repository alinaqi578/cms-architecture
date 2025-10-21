ADR #1 – Architectural Style for the Complaint Management System (CMS)
Date: 21 Oct 2025  Status: Accepted

Context and Problem Statement
ABC Limited requires a scalable, secure, and accessible Complaint Management System (CMS) that serves multiple client organisations (tenants) and supports web, telephone, and future chatbot channels.
 The chosen architecture must:
Provide clear separation between presentation, business logic, and data management.


Support 24 / 7 availability and growth of 10 % per year.


Enforce multi-tenant data isolation, security, and auditability.


Allow straightforward integration with external authentication (SSO) and notification services.


Be maintainable and understandable by a small development team within the module timeframe.



Considered Options
Option
Description
Pros
Cons
1. Layered Architecture (Monolithic deployment)
Application structured into Presentation, Business/Service, and Data Access layers within a single system.
• Simple and fast to develop.
• Well suited to small teams.
• Supports clear separation of concerns.
• Easy to deploy and test as one unit.
• Limited independent scalability.
• Potential coupling between modules if interfaces not enforced.
2. Service-Oriented Architecture (SOA / Microservices)
Split the system into discrete services (Complaint, User, Notification, Analytics) communicating via REST APIs.
• High scalability and fault isolation.
• Flexible technology choices.
• Too complex for the project scale.
• Requires additional DevOps, API gateway, and monitoring infrastructure.
3. Event-Driven Architecture
Components communicate asynchronously using message queues or event brokers.
• Excellent decoupling and resilience.
• Ideal for high throughput.
• Adds unnecessary infrastructure and conceptual overhead for a moderate PoC system.


Decision Outcome
Chosen option: ✅ Layered Architecture (Monolithic deployment)
The CMS will adopt a three-layer architectural pattern consisting of:
Presentation layer – user interface for all roles and channels.


Business layer – complaint workflows, tenant rules, validation, notifications.


Data access layer – persistent storage, security, and audit logging.


Justification:
Best fit for a medium-sized multi-tenant web system requiring clear separation of concerns.


Meets the case-study goal of complete complaint lifecycle within one bounded context (reflected in C4 Level 1 diagram).


Minimises complexity for a university-level project while keeping the design scalable and extensible (individual layers can later evolve into services).


Directly supports traceability between requirements, C4 L2 containers (Web App / Backend / DB), and non-functional requirements such as security and availability.



Consequences
Positive Impacts
Rapid development and low operational overhead.


High cohesion within layers and ease of testing.


Simplified deployment on standard web hosting environments.


Consistent mapping between architecture and C4 models.


Negative Impacts
Scaling requires replicating the entire application instance rather than individual modules.


Future integration of chatbot or IVR channels will require new interfaces.


Potential tight coupling if layer boundaries are not maintained.


Mitigation
Enforce strict layer interfaces and data access policies.


Document APIs and module contracts to simplify future refactoring into services.


Complement with ADR #2 (Tenant Isolation) and ADR #3 (Authentication & Security) to manage risks.



References
SHU Assessment Brief 25-26 – Task 1 Solution Architecture Criteria.


ABC Limited Case Study – Multi-tenant requirement and system channels.


ISO/IEC 42010:2011 — Architecture Description Standard.


Bass, Clements & Kazman (2013). Software Architecture in Practice. Addison-Wesley.
