Status: Accepted Date: 2024-11-22 Context: The system has distinct user types: Consumers (submitters) and Agents (resolvers). A Consumer must never access Agent functionality. Decision: We will implement Session-Based RBAC.
User roles (agent, consumer) are stored in the database and loaded into the PHP $_SESSION upon login.
Every controller script (e.g., agent_dashboard.php) will check $_SESSION['role'] before execution. 
Consequences:
Positive: Simple to implement in PHP without external libraries; meets NFR-03 (Security).
Negative: Session management requires secure handling (e.g., regeneration ID) to prevent hijacking. References:
NIST. (2016). Role-Based Access Control (RBAC). National Institute of Standards and Technology.
