Status: Accepted Date: 2024-11-20 Context: The project requires a rapid development cycle to build a Proof of Concept validation of the architecture. The choice of technology must support the Layered Architecture and Relational Data model selected in ADR 001 and ADR 002. Decision:
Backend: PHP 8.2 (Native session support, widely available).
Database: MySQL (Relational integrity for Foreign Keys).
Server: Apache (XAMPP). 
Consequences:
Positive: Zero configuration overhead for the development environment; native support for the request-response model used in the diagrams.
Negative: PHP's shared-nothing architecture makes WebSocket/Real-time features harder, but these are out of scope for the current PoC. References:
PHP Documentation. (2024). Session Handling. php.net.
