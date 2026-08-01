# Production Readiness Checklist

## The 6 Domains

### 1. Observability
- Structured logging with correlation IDs
- Metrics for key operations (latency, error rate, throughput)
- Distributed tracing where requests cross service boundaries

### 2. Health & Lifecycle
- Readiness and liveness endpoints
- Graceful startup (report ready only when dependencies are reachable)
- Graceful shutdown (drain in-flight work, close connections, report shutdown-complete)

### 3. Resilience
- Timeouts on all outbound calls
- Retries with backoff where safe; circuit breakers for flapping dependencies
- Bounded queues / backpressure

### 4. Security
- Authentication and authorization on every entry point
- Secrets sourced from a secret store, never hardcoded
- Input validation at trust boundaries

### 5. Configuration
- Config via environment/config file, not code
- Sane defaults; fail fast on missing required config
- No secrets in config committed to the repo

### 6. Deployment
- Container / artifact build defined
- Rollback path
- Resource requests/limits, or explicitly N/A for CLI tools and libraries

## Disposition Rule

Do NOT silently skip a domain. For each of the six, the plan must record one of:
- **Covered by Task N**, an existing task addresses it
- **New task added**, a task was added specifically for this domain
- **N/A**, with a one-line reason (e.g., "CLI tool, no container required")
