ADR 004: Audit Trail for Complaint Lifecycle

Status: Accepted | Date: 2024-11-23

Context: The requirement says I need to track the history of a complaint (NFR-04). If I just update the status from "Open" to "Resolved," I lose the record of when it was Open.

Decision: I used an Event Log approach. I created a separate table called complaint_updates that records who changed what and when.

Alternatives Considered:

Database Triggers: Rejected. I find SQL triggers hard to debug. I preferred writing the logic in PHP so I can see exactly what is happening in the code.

Justification: This makes the system compliant with the audit requirements. It also allows me to easily show a "Timeline" of the complaint to the user.
