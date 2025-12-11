ADR 005: Technology Stack Selection

Status: Accepted | Date: 20-11-2024

Context: I needed a stack that is easy to set up locally and supports Sessions and SQL natively.

Decision:

Backend: PHP 8.2

Database: MySQL

Server: Apache (XAMPP)

Alternatives Considered:

Node.js/MongoDB: Rejected. I looked into this, but enforcing strict relations (Foreign Keys) for the Multi-tenancy requirement is much harder in NoSQL (MongoDB) than in SQL.

Python/Django: Rejected. Django does too much "magic" behind the scenes. For this assignment, I wanted to show the marker the raw SQL queries to prove I understand the security logic.

Justification: PHP is built for the web. It handles the Request-Response cycle naturally, which made drawing the Sequence Diagrams much clearer.
