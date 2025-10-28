ADR #4 – Data Storage and Audit Trail
Date: 28 Oct 2025  Status: Accepted
Context and Problem Statement
 The CMS must:
Let a customer submit a complaint.


Let support staff respond, assign, update status, and eventually close the complaint.


Let supervisors review what happened, when, and by whom.


The assessment brief expects not only functionality (submit a complaint) but also evidence of auditability, traceability, and accountability.
 In other words: it is not enough to just “change status”; we must be able to prove the lifecycle.
The storage design therefore needs to:
Represent each complaint’s current state.


Preserve a full timeline of updates and communication for legal / regulatory review.


Support reporting (e.g. complaints waiting too long, complaints escalated).


Considered Options
Option 1. Single complaints record with in-place updates
 Description: Keep all complaint details and latest status in one row (complaints table), and update that row repeatedly.
 Pros:
Very easy to code.
 Cons:


Overwrites history (loses who changed it and when).


Weak for audits and SLA/reputation protection.


Hard to evidence “we responded within X hours”.


Option 2. Snapshot + append-only timeline (Chosen)
 Description:
complaints table stores the “current snapshot”: who raised it, category, description, current_status, assigned_to, timestamps.


complaint_updates (timeline table) stores every status change, assignment, or message as a new row. Each row includes timestamp, actor role, and content.
 Pros:


Full lifecycle is recorded.


Supports the “Complaint Details & Timeline” screen in the prototype.


Enables supervisor review and SLA analysis.


Makes it possible to show the customer a transparent history of activity on their own complaint.
 Cons:


Slightly more logic (each action = insert into timeline table as well as update main complaint status).


Option 3. External audit/logging infrastructure
 Description: Send every change to a dedicated log service.
 Pros:
Strong forensic story in enterprise production.
 Cons:


Too heavy for module scope.


Adds infrastructure outside assessment expectations.


Decision Outcome
 Chosen option: ✅ Option 2 – Snapshot + append-only timeline.
The CMS will persist complaint data using two related relational tables:
complaints


complaint_id (PK)


tenant reference


submitted_by (customer user_id)


category


description


current_status (Open / In Review / Resolved / Escalated / Closed)


assigned_to (agent user_id or null)


created_at


last_updated_at


complaint_updates


update_id (PK)


complaint_id (FK → complaints)


timestamp


actor_user_id


actor_role (customer / agent / supervisor / system)


event_type (status_change / message / assignment / resolution_note / escalation)


event_detail (free text or status label recorded at that time)


Justification
Satisfies accountability: every change is timestamped and linked to a role.


Satisfies transparency for the customer: they can view “who touched my complaint and what was said”.


Satisfies oversight for supervisors: they can see if response times meet expectations, and if escalations are being handled.


Aligns with non-functional requirements on auditability and traceability.


Still implementable with straightforward INSERT/SELECT statements in the chosen layered architecture.


Consequences
 Positive impacts:
Strong evidence trail for each complaint.


Enables “timeline view” with no extra services.


Supports SLA / compliance review within each tenant.


Negative impacts:
Requires discipline: every agent/supervisor action must write to complaint_updates.


More database rows over time.


Mitigation:
Treat complaint_updates rows as immutable; do not edit past events.


The business layer (not UI directly) is responsible for writing both the complaint snapshot and the corresponding update record so they don’t drift apart.


References
Assessment brief: need for auditability, escalation visibility, and management oversight.


Case study: supervisors must be able to view handling quality and timeliness.


Non-functional requirements: accountability, traceability, transparency.





