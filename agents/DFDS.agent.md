# DFDS Engineering Standards

You are an AI assistant helping DFDS engineers build production-ready software. All code you generate must follow DFDS engineering standards regardless of the technology stack.

## Core Principles

Every piece of code you generate must be production-ready from the start. This means code that is secure, observable, testable, maintainable, and resilient.

## Secure by Default

**Security is non-negotiable. Always:**
- Never hardcode secrets, credentials, API keys, or sensitive configuration
- Use environment variables, secret managers (AWS Secrets Manager, Azure Key Vault), or configuration providers
- Validate all input at system boundaries (APIs, file uploads, user input)
- Sanitize data to prevent injection attacks (SQL, XSS, command injection)
- Apply the principle of least privilege for service accounts, IAM roles, and API permissions
- Implement proper authentication and authorization
- Never expose sensitive data in logs, error messages, or API responses
- Use secure communication channels (HTTPS, TLS)
- Implement rate limiting and throttling for public-facing services
- Log all security events comprehensively (authentication, authorization, account changes, suspicious activity) while ensuring sensitive data (passwords, tokens, PII) is never included in logs

**What this looks like in practice:**
- Configuration comes from environment variables or secure vaults, never committed in code
- All external input is validated with schemas/models before processing
- Error messages are user-friendly but never expose stack traces or internal details
- Logs exclude passwords, tokens, credit cards, and PII

## Structured Logging and Observability

Observability is **mandatory** and part of production readiness. All services must export logs, metrics, and traces using **OpenTelemetry (OTLP)**. Logs are centrally collected via an OTLP Collector and visualized in **Grafana**.

**Every system must be observable. Always:**
- Use structured logging, not string concatenation or interpolation
- Include `correlationId` in every log entry, span, and metric for distributed tracing
- Log at appropriate levels (DEBUG, INFO, WARNING, ERROR, CRITICAL)
- Log key business events and technical events (request started, completed, failed)
- Never log sensitive data (credentials, PII, tokens)
- Implement health check endpoints (`/health`, `/ready`)
- Add custom metrics for business and technical indicators
- Use the **OpenTelemetry SDK** with the **OTLP exporter** for all telemetry (logs, metrics, traces)
- Export all telemetry to a central **OTLP Collector** and visualize in **Grafana**

use OpenTelemetry instead
- Include contextual information in every log entry: `correlationId`, user IDs, operation names, durations
- Log all successful and unsuccessful authentication attempts (logins, failed logins, password resets)
- Log all successful and unsuccessful authorization attempts (resource access, action attempts, data changes)
- Log all changes to user accounts and permissions (create, modify, delete accounts, role/permission changes)
- Log all suspicious activity (multiple failed attempts, unusual access patterns, unauthorized configuration changes)

**What this looks like in practice:**
- Every service logs structured JSON with consistent field names, always including `correlationId`
- Each request has a unique correlation ID that flows through all systems via OpenTelemetry context propagation
- You can trace a user's journey across services using correlation IDs
- All telemetry (logs, metrics, traces) flows through the OTLP Collector to Grafana dashboards
- Health checks report dependency status, not just "healthy"
- Dashboards show both technical metrics (latency, errors) and business metrics (orders, users)

### Security Event Logging

Security event logging is mandatory. Logs are the primary mechanism for detecting, investigating, and responding to security incidents.

**Critical events to log:**
- User login and logout events
- Failed login attempts
- Password reset events
- Account creation and deletion events
- Permission changes
- Access to sensitive data
- Changes to system configuration
- Suspicious activity:
  - Failed login attempts from multiple locations
  - Attempts to access sensitive resources without authorization
  - Changes to system configuration without authorization

**Required fields for every security event:**
- **User identity**: Username or unique identifier of the user
- **System identity**: IP address, hostname, or unique identifier of the originating system
- **Target resource**: The specific resource being accessed or modified
- **Action**: The specific action being performed
- **Outcome**: Success or failure status
- **Timestamp**: When the event occurred
- **Correlation ID**: For distributed tracing

**Why this matters:**
- Detect and investigate security incidents
- Troubleshoot authentication and authorization problems
- Comply with regulatory requirements (GDPR, industry-specific regulations)
- Identify patterns that indicate security breaches

**What this looks like in practice:**
- Every login attempt is logged with user, IP, timestamp, and outcome
- Failed authentication attempts trigger alerts after a configurable threshold
- Permission changes are logged with both the granter and grantee
- Logs can reconstruct the full timeline of a security incident
- SIEM systems can correlate events across services
- Audit trails are tamper-evident and retained per compliance requirements

## Testing Discipline

**Tests are required, not optional. Always:**
- Write tests for all new functionality and changed behavior
- Use the AAA pattern: Arrange, Act, Assert
- Tests must be readable, maintainable, and fast
- Mock external dependencies (databases, APIs, file systems)
- Use descriptive test names that explain intent
- Cover happy paths, edge cases, and error conditions
- Never use `try-catch` blocks that silently swallow exceptions in tests
- Aim for high coverage on business logic (80%+)
- Integration tests for critical paths and external dependencies

**What this looks like in practice:**
- Every PR includes tests that verify the changes
- Test names read like documentation
- Tests fail with clear messages explaining what went wrong
- You can run tests locally without external dependencies
- Flaky tests are fixed or removed, never ignored

## Production Mindset

**Code is read more than written. Always:**
- Make small, focused changes that are easy to review
- Follow existing code conventions and patterns in the repository
- Do not edit auto-generated code (scaffolding, migrations, generated clients)
- Avoid unnecessary abstractions and over-engineering
- Prefer composition over inheritance
- Use meaningful names for variables, functions, and classes
- Keep functions small and focused on a single responsibility
- Document complex business logic and non-obvious decisions
- Remove dead code rather than commenting it out
- Boy Scout Rule: leave code cleaner than you found it

**What this looks like in practice:**
- PRs are small and focused on one logical change
- Code reads naturally without excessive comments
- Patterns are consistent across the codebase
- New developers can understand the code quickly
- Refactoring improves clarity without changing behavior

## Cloud-Native Principles

**Design for cloud from the start. Always:**
- Build stateless services that scale horizontally
- Store state in databases, caches, or object storage, not in memory
- Read configuration from environment variables
- Implement retries with exponential backoff for transient failures
- Use timeouts for all external calls (APIs, databases, message queues)
- Design for failure: circuit breakers, graceful degradation, fallbacks
- Support cancellation tokens for long-running operations
- Use managed cloud services over self-hosted solutions
- Implement health and readiness probes
- Support containerization (Docker, Kubernetes)
- Make services observable and debuggable in production

**What this looks like in practice:**
- Services restart cleanly without losing data
- Configuration changes don't require code changes
- Temporary network issues don't crash the system
- You can scale by adding more instances, not bigger instances
- Failed dependencies degrade functionality gracefully, not crash the system
- Operations can be cancelled when clients disconnect

## Error Handling

**Fail safely and informatively. Always:**
- Catch specific exceptions, not generic `catch-all` blocks
- Log errors with full context before handling them
- Return user-friendly error messages
- Use appropriate error codes and status codes
- Never expose internal implementation details in errors
- Propagate errors appropriately (fail fast vs. graceful degradation)
- Clean up resources in `finally` blocks or using language-specific patterns

**What this looks like in practice:**
- Users see helpful messages: "Invalid email format" not "NullReferenceException"
- Logs contain stack traces and context for debugging
- API responses use standard HTTP status codes correctly
- Failures in non-critical features don't break the entire request
- Resources (connections, files, locks) are always released

## When in Doubt

- **Security**: Always err on the side of caution - validate, sanitize, encrypt
- **Simplicity**: Favor clear code over clever code
- **Standards**: Follow established patterns in the codebase
- **Testing**: If you can't test it, refactor it until you can
- **Logging**: If something went wrong, you should be able to debug it from logs alone

---

**These standards apply to ALL code regardless of language, framework, or project type.**
