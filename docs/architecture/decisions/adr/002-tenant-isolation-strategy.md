ADR #2 – Tenant Isolation Strategy (Updated)
Date: 22 Oct 2025  Status: Accepted

Context and Problem Statement
ABC Limited’s Complaint Management System (CMS) must support multiple client organisations (tenants) within a single solution. Each tenant has distinct users, branding, and data.
 The architecture must:
Prevent cross-tenant data access (privacy / regulatory compliance).


Scale as tenant and complaint volumes grow (~ +10 % per year).


Remain maintainable for a small development team.


Align with the Layered Architecture (ADR #1) and upcoming C4 L2 design.



Considered Options
Option
Description
Pros
Cons
1. Shared Schema + Tenant ID
One database; all tables include tenant_id.
• Fast to implement • Low cost • Simplest for PoC
• Stronger discipline needed to avoid cross-tenant leaks • Harder per-tenant backups
2. Schema-per-Tenant (Chosen)
Each tenant has its own schema in one DB instance.
• Good isolation • Simple per-tenant backup • Scales moderately well
• Requires provisioning/migration tooling • Schema management overhead
3. Database-per-Tenant
Separate DB instance per tenant.
• Strongest isolation
• High operational cost • Complex DevOps


Decision Outcome
Chosen Option: ✅ Option 2 – Schema-per-Tenant (Isolated Schema within Shared DB Instance)
Justification:
Balances security and maintainability for a medium-scale multi-tenant web system.


Meets data-privacy / compliance obligations while avoiding excessive operational cost.


Supports expected +10 % annual growth.


Aligns with the Layered Architecture where tenant logic sits in the Business layer and data isolation in the Data layer.


Provides a clear migration path from a shared schema to full isolation.



Phased Implementation Approach
Phase 1 (PoC): use a shared schema with tenant_id column to demonstrate functionality quickly.


Keeps implementation simple for Task 2.


Every query filters by tenant_id and access control enforces tenant boundaries.


Phase 2 (Final Product): migrate to schema-per-tenant model once provisioning tooling is available.


Business and Data layers are designed so this change requires minimal code impact.


This phased plan keeps Task 1 documentation aligned with best practice while keeping the Task 2 PoC feasible.

Consequences
Positive Impacts
Clear logical separation per tenant → stronger security and trust.


Straightforward per-tenant backup / customisation.


Efficient resource usage (shared hardware).


Smooth scaling (add new schema rather than new DB server).


Negative Impacts
Schema-creation scripts required for onboarding.


Version control across schemas must stay consistent.


In Phase 1, stricter testing needed to prevent cross-tenant access.


Mitigation
Build a Tenant Manager component to resolve tenant context per request.


Automate schema creation / migration scripts.


Add integration tests for tenant isolation.


Document the tenant-context logic at C4 Level 3.



References
SHU Assessment Brief 25-26 – Solution Architecture & NFRs (security, scalability, data design).


ABC Limited Case Study – Multi-tenant and privacy requirements.


ISO/IEC 27001 (Information Security Management Systems).


Fowler (2012) Patterns of Enterprise Application Architecture – Shared Database vs Isolated Schema patterns.
