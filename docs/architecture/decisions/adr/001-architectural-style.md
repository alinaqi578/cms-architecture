Status: Accepted Date: 2024-11-20 Context: The Complaint Management System (CMS) requires a robust structure to handle core business logic (complaints workflow), data persistence, and multiple user interfaces (Web/Agent). The system needs to be developed by a single developer within a limited university module timeframe. The key driver is maintainability and simplicity while preparing for future extensibility. Decision: We will adopt a Layered Monolithic Architecture consisting of three distinct layers:
Presentation Layer: PHP views (HTML/CSS) for Consumer and Agent dashboards.
Business Logic Layer: Service classes handling RBAC and Tenant Logic.
Data Access Layer: Repository classes managing SQL interactions. 
Consequences:
Positive: Simplifies deployment (single artifact), easier to debug than microservices, and aligns with the "Golden Thread" scope of the assessment.
Negative: Scaling individual components (e.g., just the reporting engine) is harder than in microservices, but acceptable for the current user load projections. References:
Fowler, M. (2002). Patterns of Enterprise Application Architecture. Addison-Wesley. (Justification for Layered pattern).
Bass, L., Clements, P., & Kazman, R. (2012). Software Architecture in Practice. Addison-Wesley.
