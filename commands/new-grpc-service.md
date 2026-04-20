---
description: Scaffold a new gRPC microservice for the CJ empire domain using foreign-exchange-service as the template. Creates all three repos: service, protocol, and k8s.
argument-hint: Optional service description
---

# New gRPC Service

You are scaffolding a new gRPC microservice in the CJ empire domain. The template source is the `foreign-exchange-service` located at `/Users/jefwinds/Source/gitlab.cj.dev/empire/foreign-exchange-service/`.

The service user wants to create: $ARGUMENTS

Use `TodoWrite` to track progress through the phases below.

---

## Phase 1: Gather Inputs

Ask questions in three batches. **Do not proceed past a batch until the user has answered it.**

### Batch 1 — Identity
Ask for a single **base name** (lowercase, e.g. `settlement`).

Infer from it:
- **Service name**: `{base-name}-service` (e.g. `settlement-service`)
- **Java package suffix**: `{base-name}` (e.g. `settlement` → `com.cj.fintech.settlement`)
- **Domain class name**: PascalCase of base name (e.g. `Settlement`)

Show the user the inferred values and ask them to confirm before continuing to Batch 2.

### Batch 2 — gRPC Contract
- **Proto service name(s)** (e.g. `SettlementService`)
- **Method(s):** for each method: name, request type, response type
  - Example: `getSettlement` / `GetSettlementRequest` / `GetSettlementResponse`
  - If multiple methods, list them all

### Batch 3 — Infrastructure

Infer defaults for fields not provided, show the user the inferred values, and ask them to confirm or override each one before continuing.

| Field | Default inference rule |
|---|---|
| **gRPC port** | Suggest the next available port above 9090 (known conflicts: foreign-exchange-service=9090, financial-service=9090). Must not conflict. |
| **GitLab group path** | Infer from `foreign-exchange-service` template: `cj/finance` |
| **OCI image path** | `registry.cj.dev/{gitlab-group-path}/{service-name}` |
| **Database needed?** | Default: `yes` (Oracle + HikariCP + Flyway) |
| **Authentication required?** | Default: `yes` (Bearer token via `AuthenticationInterceptor`) |
| **Outbound gRPC clients?** | Default: `none` |

Present the inferred defaults to the user and ask them to confirm or override. Only ask for values the user has not already provided.

After all three batches are answered, confirm the full summary with the user before proceeding.

---

## Phase 2: Template Knowledge

### Three repos to create

| Repo | Template source | Purpose |
|---|---|---|
| `{service-name}-protocol` | `foreign-exchange-service-protocol` | Proto files + Maven artifact |
| `{service-name}` | `foreign-exchange-service` (service) | Spring Boot gRPC service |
| `{service-name}-k8s` | `foreign-exchange-service-k8s` | Kubernetes manifests |

### What to carry forward (rename/update only)

These components are proven and should be copied as-is, with only package/name substitutions applied:

- **All 4 interceptors** — `AuthenticationInterceptor` (if auth=yes), `ErrorHandlingInterceptor`, `SecurityHeadersInterceptor`, `StructuredLoggingInterceptor`
  - **Exception:** Use `StructuredLoggingInterceptor` from `financial-service` instead — it extracts client IP from gRPC transport attributes. Path: `/Users/jefwinds/Source/gitlab.cj.dev/empire/financial-service/financial-service/src/main/java/com/cj/fintech/interceptor/StructuredLoggingInterceptor.java`
- `GrpcRequestHandler` / `RequestPipeline` wiring
- `ErrorResponseHandler` + `ServiceError` / `GetError` enum pattern
- `@ConfigurationProperties` classes: `SecurityProperties`, `RetryProperties`, `TimeoutProperties`
- `SecurityEventLogger` (async `@Async`, risk-leveled)
- `DatabaseMetricsConfig`, `DatabaseTimingUtils`, `LoggingUtils`
- Test infrastructure: all stubs, `ControllerBuilder`, `TestDataBuilder`, `TestAssertions`
- Retry + timeout configuration properties
- `logback-spring.xml` profile structure (dev = human-readable, prod = JSON)
- Jib plugin containerization config
- Actuator + Prometheus endpoint config
- HikariCP + Oracle datasource config (if DB=yes)

### What to strip / replace

| Element | Action |
|---|---|
| `FXRate`, currency enums, all domain model classes | Replace with new domain model |
| `GetFxRateHandler`, `GetActiveCurrenciesHandler`, `GetAdvPubRatesHandler` | Replace with one handler per proto method |
| Proto dependency `foreign-exchange-service-protocol` | Replace with `{service-name}-protocol` |
| PST timezone logic, 3% fee business logic | Remove entirely |
| Oracle DB schema + Flyway migrations | Replace with new schema (remove if DB=no) |
| `AuthenticationInterceptor` + `SecurityProperties.authentication` | Remove if auth=no |
| Outbound gRPC client config in `application.properties` | Add/remove per inputs |
| `otel.service.name` | Set to `{service-name}` |
| JIB `image` tag | Set to provided OCI image path |
| `grpc.server.port` | Set to provided port |
| Package `com.cj.fintech.fx` | Replace everywhere with `com.cj.fintech.{package-suffix}` |
| App class name `ForeignExchangeServiceApplication` | Rename to `{DomainClass}ServiceApplication` |
| Proto package and service names | Set to new proto service name(s) and method(s) |
| Proto Maven artifact ID | Set to `{service-name}-protocol` |
| k8s deployment names, service names, configmap names | Replace `foreign-exchange` with `{service-name}` |
| k8s container port | Update to provided gRPC port |

### Handler pattern to follow

Each proto method gets its own handler class following this structure:

```java
@Component
public class Get{Domain}Handler {
    private final I{Domain}Repository repository;
    private final RequestValidator requestValidator;
    private final {Domain}Metrics metrics;

    public Get{Domain}Handler(I{Domain}Repository repository,
                              RequestValidator requestValidator,
                              {Domain}Metrics metrics) { ... }

    public Either<ServiceError, Get{Domain}Response> handle(Get{Domain}Request request) { ... }
}
```

### Error enum pattern to follow

```java
public enum ServiceError {
    NULL_REQUEST, EMPTY_REQUEST, VALIDATION_FAILED, REPOSITORY_ERROR
}

public enum GetError {
    NOT_FOUND, FAILED_TO_GET
}
```

Map in `ErrorResponseHandler`:
- `NULL_REQUEST`, `EMPTY_REQUEST`, `VALIDATION_FAILED` → `INVALID_ARGUMENT`
- `REPOSITORY_ERROR` → `UNAVAILABLE`

---

## Phase 3: Write Implementation Plan

Invoke `superpowers:writing-plans` to produce a detailed, step-by-step implementation plan covering all three repos. The plan should:

1. Start with the protocol repo (service depends on it)
2. Then the service repo (uses the protocol artifact)
3. Then the k8s repo (references the service image)

Within each repo, follow this order:
1. Copy template files
2. Apply package/name substitutions globally
3. Strip domain-specific logic
4. Implement new domain model and handlers
5. Update configuration (ports, image, service names)
6. Update tests (rename stubs, update builders for new domain)
7. Verify compilation and tests pass

---

## Phase 4: Execute

Use `superpowers:subagent-driven-development` to execute the plan, creating the three repos in the empire directory at `/Users/jefwinds/Source/gitlab.cj.dev/empire/`.
