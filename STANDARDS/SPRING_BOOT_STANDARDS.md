# Spring Boot Standards

## SPRING BOOT CONFIGURATION

### Security

- All secrets must be sourced from **environment variables** only
- Use `${ENV_VAR}` in `application.properties`, **never** hardcode credentials

### Config Management

- Prefer `application.properties` over `@Configuration` classes for simple configs
- Use Spring Boot property binding over custom config classes
- Avoid scattering `@Value` injections – consolidate config
- Set default values: `${DB_HOST:localhost}`

### Spring Boot Code Hygiene

- Remove redundant `@Configuration` classes
- Favor **declarative** config (properties, annotations) over imperative logic