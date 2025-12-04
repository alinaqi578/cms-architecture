Status: Accepted Date: 2024-11-21 Context: The CMS must serve multiple enterprise clients (e.g., NatWest, Vodafone). Strict data isolation is a critical Non-Functional Requirement (NFR-01) to prevent data leakage between competitors. Decision: We will implement Logical Isolation (Shared Database, Separate Tenant Context).
Every table (e.g., complaints, users) will include a mandatory tenant_id foreign key.
All data access queries must include WHERE tenant_id = ?. 
Consequences:
Positive: Highly cost-effective (one database instance), easy to back up, and simplifies the codebase compared to "Database-per-tenant".
Negative: Risk of developer error forgetting the WHERE clause. This is mitigated by implementing a centralized TenantContextResolver service (seen in C4 Level 3). References:
Chong, F., & Carraro, G. (2006). Architecture Strategies for Catching the Long Tail. Microsoft MSDN. (Defining Multi-tenant data patterns).
