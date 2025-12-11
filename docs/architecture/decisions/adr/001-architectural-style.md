ADR 001: Selection of Layered Monolithic Architecture

Status: Accepted | Date: 20-11-2024

Context: The CMS needs to handle complaints, data storage, and different user views (Agent vs Consumer). I initially considered splitting these into different services to make them scalable. However, I am working as a single developer with a strict submission deadline.

Decision: I decided to build a Layered Monolith. The code is organized into layers (Views, Services, Repositories) but runs as one single application.

Alternatives Considered:

Microservices: Rejected. I realized that setting up separate services for a simple PoC would require too much configuration (like API Gateways) and would distract me from the core security requirements.

Serverless: Rejected because debugging "cloud functions" locally is difficult without advanced tools, and I wanted to stick to a standard XAMPP environment.

Justification: This approach is much easier to test and debug. If something breaks, I can see the error in one place rather than tracing it across multiple services. It fits the "Vertical Slice" scope perfectly.

Consequences: If the system grows huge later, it might be harder to split up, but for this project, it is the most efficient choice.
