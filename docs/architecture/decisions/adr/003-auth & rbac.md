ADR #3 – Authentication and Role-Based Access Control (RBAC)
Date: 28 Oct 2025  Status: Accepted
Context and Problem Statement
 The CMS must be used by different types of users:
Customer: raises complaints and views only their own cases.


Support Agent: monitors incoming complaints, updates status, and communicates with the customer.


Supervisor / Manager: oversees case handling quality, escalations, and SLA compliance.


The assessment brief and case study both require security, controlled access to sensitive data, and accountability for actions taken on complaints.
 The system therefore needs:
Secure login for all roles.


A way to ensure that one user cannot access another user’s complaints without authorisation.


A way to distinguish operational staff (agents) from oversight (supervisor/manager).


Considered Options
Option 1. Single “user” role for everyone
 Description: All authenticated users are treated the same.
 Pros:
Very easy to build.
 Cons:


Fails confidentiality requirements.


A normal customer could view another customer’s complaint.


No way to separate day-to-day handling (agent) from governance/escalation (supervisor).


Not acceptable in a complaints context.


Option 2. Role field + session-based access checks (Chosen)
 Description:
Each account record stores a role value such as customer, agent, or supervisor.


After successful login, the system stores the user’s ID and role in session.


Every protected page and every business action checks:


Is the user logged in?


Is the user allowed to perform this action for this specific complaint ID?
 Rules include:


Customers can only view complaints where submitted_by = this_user_id.


Agents can view and update complaints assigned to them or in their queue.


Supervisors can view all complaints and escalation data across the tenant.
 Pros:


Straightforward to implement in a layered monolithic system.


Supports separation of duties (customer vs agent vs supervisor).


Directly enforces confidentiality and accountability.
 Cons:


Requires consistent enforcement across all controller/business-layer actions.


Requires storing and checking secure sessions.


Option 3. External identity provider / SSO
 Description: Delegate authentication and role claims to an enterprise identity service.
 Pros:
Enterprise-grade security and auditability.
 Cons:


Higher integration effort.


Out of scope for this coursework delivery timeline.


Decision Outcome
 Chosen option: ✅ Option 2 – Role field + session-based access checks.
The CMS will implement:
A users table that stores:


user_id (PK)


name


email / username


password_hash


role (customer / agent / supervisor)


tenant reference


A login process that:


verifies credentials,


sets session variables for user_id, role, and tenant.


Access control logic that:


filters database queries based on both role and ownership,


prevents a customer from viewing other customers’ complaints,


allows supervisors to view cross-complaint information for escalation/SLA analysis within their tenant.


Justification
Meets the non-functional requirements around confidentiality, security, and accountability.


Supports the complaint journey shown in the prototype (customer should only see their own timeline).


Enables operational workflows (agents) and compliance/oversight workflows (supervisors) without needing separate applications.


Fits within the Layered Architecture defined in ADR #1:


Presentation layer: enforces which pages a role can see.


Business layer: enforces which actions a role can perform.


Data layer: enforces query filtering by user/tenant.


Consequences
 Positive impacts:
Clear protection of sensitive complaint data.


Ability to trace which user/role performed each update on a complaint.


Supports escalation and SLA monitoring described in the case study.


Negative impacts:
Every controller/action must consistently check both login state and role permissions.


Password handling and session management must be implemented securely.


Mitigation:
Centralise authorisation checks instead of duplicating logic.


Store passwords using a secure hash.


Invalidate and regenerate sessions appropriately on login/logout.


References
Assessment brief: security, privacy, and oversight requirements.


Case study: multi-role environment (customer, handler, supervisor).


Internal non-functional requirements: confidentiality, integrity, accountability.









