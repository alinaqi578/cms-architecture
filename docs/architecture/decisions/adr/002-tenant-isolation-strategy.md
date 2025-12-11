ADR 002: Multi-Tenancy via Schema-per-Tenant Strategy

Status: Accepted | Date: 21-11-2024

Context: I need to stop NatWest users from seeing Vodafone data (NFR-01). I had to choose how to separate this data in the database.

Decision: I chose Logical Isolation (Shared Database). This means everyone shares the same tables, but I added a tenant_id column to every table to filter the data.

Alternatives Considered:

Database-per-Tenant: Rejected. Creating a brand new database every time a new client signs up would be a nightmare to manage in phpMyAdmin for this assignment.

Separate Schemas: Rejected because MySQL (my chosen database) doesn't handle schemas as easily as Postgres, and I wanted to keep the SQL simple.

Justification: This is the most practical solution for a PoC. It proves I can write secure SQL queries (WHERE tenant_id = ?) without needing complex infrastructure.

Consequences: I have to be extremely careful not to forget the WHERE clause in my code, otherwise data will leak. I fixed this risk by adding the TenantContextResolver check.
