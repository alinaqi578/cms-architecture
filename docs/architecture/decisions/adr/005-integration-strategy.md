ADR #5 – Integration Strategy for Multiple Channels
Date: 28 Oct 2025  Status: Accepted
Context and Problem Statement
 The CMS is intended to be used through more than one channel:
Web portal where customers submit and track complaints.


Internal/agent-facing interface where support staff update status, assign ownership, and respond.


Supervisor view for oversight and escalation.


Potential future channels, e.g. chatbot or telephone operator logging a complaint on behalf of the customer.


The architecture must allow these channels to interact with the same complaint lifecycle and the same audit trail without each channel bypassing rules or corrupting data.
We need an approach that:
Keeps one source of truth for complaint records.


Enforces RBAC and tenant isolation.


Ensures that every action still generates audit trail entries.


Does not introduce unnecessary complexity.


Considered Options
Option 1. Let every channel write directly to the database
 Description: Each UI/channel runs its own SQL operations.
 Pros:
Simple in theory.
 Cons:


Inconsistent validation and security between channels.


Easy to skip RBAC or forget to write an audit entry.


High risk of data corruption or privacy breach across tenants.


Option 2. Centralised business layer operations (Chosen)
 Description:
All complaint actions (create complaint, add update, change status, assign to agent, close, escalate) are implemented as controlled operations in the CMS business layer.


Every channel (customer web front end, agent dashboard, supervisor dashboard, and any future chatbot/phone intake) must call those same operations instead of doing ad-hoc direct database writes.
 Pros:


Guarantees consistent enforcement of RBAC (ADR #3), tenant isolation (ADR #2), and audit trail logging (ADR #4).


Limits duplication: complaint logic is defined once.


Keeps the architecture monolithic (ADR #1), so we do not introduce microservice overhead.
 Cons:


Requires documenting and reusing those shared operations instead of quick one-off SQL in each UI.


Option 3. Full public REST API for all operations from day one
 Description: Immediately expose a formal external API and force all channels to talk to it.
 Pros:
Clean boundary for future integration with chatbots and IVR.
 Cons:


Adds design, security, and versioning complexity that may be beyond what is needed to evidence the architecture in Task 1 and implement a PoC in Task 2.


Decision Outcome
 Chosen option: ✅ Option 2 – Centralised business layer operations.
The CMS will define a small set of controlled complaint operations in the application’s business layer, for example:
createComplaint(customer_id, tenant, category, description, evidence…)


addCustomerMessage(complaint_id, customer_id, message…)


addAgentUpdate(complaint_id, agent_id, new_status / note / assignment…)


escalateComplaint(complaint_id, supervisor_id, escalation_reason…)


resolveComplaint(complaint_id, agent_id, resolution_note…)


All channels must call these operations. Those operations are responsible for:
enforcing role/tenant checks (ADR #3 and ADR #2),


updating the current snapshot in complaints,


inserting an immutable event row into complaint_updates (ADR #4).


Justification
Matches the Layered Architecture from ADR #1: Presentation layer (different UIs/channels) talks to a shared Business layer, which talks to the Data layer.


Ensures consistent behaviour: audit logging and permissions are never “forgotten” by a side channel.


Keeps implementation realistic for coursework: still a single deployed application, not microservices.


Consequences
 Positive impacts:
All complaint activity goes through one controlled path.


Easier to prove compliance, escalation handling, and SLA tracking to supervisors.


Future channels (chatbot, call centre operator interface) can be added by calling the same operations without redesigning storage.


Negative impacts:
Requires discipline in code structure to avoid “quick hacks” that bypass the business layer.


Slightly more upfront design effort to define those shared operations.


Mitigation:
Clearly document the core business operations and reference them in the C4 Level 3 component model (e.g. ComplaintController / ComplaintService / TimelineService / DataAccess).


Ensure UI components never perform direct SQL writes.


References
Assessment brief: requirement for multi-channel access while preserving control, auditability and security.


Case study: different personas (customer, agent, supervisor) acting on the same complaint data.


Non-functional requirements: consistency, maintainability, accountability, scalability across tenants.
