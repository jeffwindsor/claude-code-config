# Universal Coding Standards (MANDATORY)

This file defines **non-optional** coding standards that apply to ALL projects. These preferences **override default Claude behavior**. Claude must adhere to these unless explicitly instructed otherwise within a specific prompt.

---

## CODE STYLE STANDARDS

### Core Principles

- Favor **composition** over inheritance
- Use **builders** for complex data creation
- Use **immutable** structures where practical
- Keep methods **short, focused, expressive**
- Variable and method names must express **business intent**

### Error Handling

- Use **functional constructs** (`Either`, `Optional`) where applicable
    - Prefer Structured Enums over String as Left type
    - Enums should not be mixed between concerns or layers
- Do not throw exceptions for expected cases
- All error messages must be **clear and actionable**

### Dependency Guidelines

- Minimize 3rd-party dependencies
- Prefer **standard library** and idiomatic patterns
- Always use **dependency injection**
- Avoid static accessors, global state, or hidden dependencies

### Code Documentation

- Code must be **self-documenting** by naming
- Comments should explain **why**, not **what**
- Public APIs must include JavaDoc
- Keep documentation close to the relevant code

---

## CLAUDE MUST FOLLOW THESE PREFERENCES

These coding standards override general best practices. **Do not suggest or apply alternate conventions** unless explicitly instructed per project or file.
