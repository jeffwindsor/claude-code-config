# Spring Boot Standards (MANDATORY for Spring Projects)

This file defines **non-optional** Spring Boot configuration standards that apply to ALL Spring Boot projects. These preferences **override default Claude behavior**. Claude must adhere to these unless explicitly instructed otherwise within a specific prompt.

---

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

---

## CLAUDE MUST FOLLOW THESE PREFERENCES

These Spring Boot standards override general best practices. **Do not suggest or apply alternate conventions** unless explicitly instructed per project or file.