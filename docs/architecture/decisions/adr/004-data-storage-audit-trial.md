Status: Accepted Date: 2024-11-23 Context: For compliance in banking/telecom sectors, every change to a complaint's status must be traceable. Overwriting data (e.g., just changing "Open" to "Closed") loses the history of when it happened. Decision: We will use an Event Log Pattern.
A dedicated complaint_updates table will record every status change.
The main complaints table holds the current state, while complaint_updates holds the history. 
Consequences:
Positive: Provides full traceability (NFR-04) and supports SLA reporting.
Negative: Increases storage requirements, but deemed negligible for the PoC scope. References:
Richardson, C. (2018). Microservices Patterns. Manning Publications. (Reference for Event Sourcing concepts applied to Monoliths).
