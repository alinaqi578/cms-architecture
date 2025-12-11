ADR 003: Role-Based Access Control (RBAC) Implementation

Status: Accepted | Date: 2024-11-22

Context: I have Consumers and Agents logging in. I need a way to stop Consumers from guessing the URL for the Agent Dashboard (e.g., /agent_dashboard.php).

Decision: I implemented Session-Based RBAC. When a user logs in, I save their role (agent or consumer) in the PHP Session. Then, at the top of every sensitive file, I check this session variable.

Alternatives Considered:

Access Control Lists (ACLs): Rejected. Defining complex permission rules for every single button was too complicated for the time I had.

OAuth/Auth0: Rejected. Integrating a third-party login system is too big a scope for this "Vertical Slice." I wanted to demonstrate that I understand how sessions work natively.

Justification: This satisfies NFR-03 (Security) without needing external libraries. It is simple, effective, and easy to test.
