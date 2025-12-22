# Universal Testing Standards (MANDATORY)

This file defines **non-optional** testing standards that apply to ALL projects. These preferences **override default Claude behavior**. Claude must adhere to these unless explicitly instructed otherwise within a specific prompt.

---

## TESTING STANDARDS

### Test Architecture

- Prefer **stubs**, not mock frameworks.
- Drive **non-determinism to the edges** of the system (e.g., time, DB, I/O). Inject such dependencies.
- Write **deep tests** through public interfaces; stub only edge dependencies.
- Prefer **builders over fixtures** to create test data.
- Each test must be:
  - **Self-contained**
  - **Minimal** (setup only what is needed)
  - **Deterministic** and independent
  - **Focused on intent**, not implementation details

### Test Performance

- Ideal: **< 10ms**
- Warning: **> 20ms**
- Failure: **> 100ms**
- Disallowed: File system, network, DB, or global resources (time, env vars)
- Allow only: CPU, heap, stack

### Test Reliability

- Tests must run in **any order**
- Disallow **shared state**
- Duplicate logic goes into **test-only stubs/fakes**
- Never reuse stub instances
- Tests must not use system time or env vars directly

### Test Readability

- Inputs/outputs must be **human-understandable**
- Prefer strings, ints, bools – avoid paths, byte arrays
- Keep test data small, relevant, clear
- Delegate complexity to test utility classes

### Test Types – Correct Usage Only

| Type            | Allowed Use                                                                                          |
|-----------------|------------------------------------------------------------------------------------------------------|
| **Unit**        | For all logic, prefer tests at the outer boundary of code (the input to storage and non-determinism) |
| **Integration** | Only for cross-boundary checks (e.g., DB)                                                            |
| **End-to-End**  | Only for smoke/perf on deploy; not in build                                                          |
| **Regression**  | For large critical datasets; must document                                                           |
| **Manual**      | Only if result requires human visual check                                                           |

### Test Naming & Structure

- Use `@Nested` classes like `WhenX`, `GivenY`, `WithZ` for scenario grouping
- Method names: `shouldDoXWhenY`
- Use `@DisplayName` for business-readable titles
- Structure tests with clear sections: `// Setup`, `// Execute`, `// Verify`
- Use **dedicated, reusable assertion helpers** (`assertSuccess()`, `assertOutput()`)

### Test-Driven Development (TDD) Process

**RED-GREEN-REFACTOR cycle definitions:**

**RED State (Proper)**:
- All code **compiles successfully** (no compilation errors)
- Test code and production code structure exists with correct signatures
- Tests **run but fail** with assertion errors
- Production code has minimal/stub implementation that returns wrong values
- This proves the test is actually testing something

**RED State (Improper - Do Not Create)**:
- Compilation errors
- Missing classes, methods, or imports
- Tests cannot execute

**GREEN State**:
- All code compiles successfully
- Tests run and **pass** with correct assertions
- Production code implements the actual logic to satisfy requirements
- No test failures

**TDD Workflow (MANDATORY - ONE TEST AT A TIME)**:

1. **Write ONE test** (may have compilation errors initially)
2. **Create minimal structure** so everything compiles (proper RED state)
3. **STOP - User runs tests** and confirms RED state
4. **Implement minimal production code** to make ONLY this one test pass (GREEN state)
5. **STOP - User runs tests** and confirms GREEN state
6. **Provide commit message and description** for this one test cycle
7. **STOP - Wait for user confirmation** of commit before proceeding
8. **Refactor if needed** while keeping tests GREEN (optional)
9. **Repeat from step 1** for the next test

**Critical TDD Rules**:
- **NEVER write multiple tests at once** - always one test at a time
- **NEVER implement code before user confirms RED** - must see failing test first
- **NEVER move to next test before user confirms commit** - each test gets its own commit
- **User controls the pace** - Claude waits for explicit confirmation at each step
- **Commit after every GREEN** - maintains clean history and rollback points

**Commit Message Format**:
```
test: add test for [specific behavior]

- Added test: [test name]
- Implements: [what production code was added/changed]
- Scenario: [which scenario this supports]
```

---

## CLAUDE MUST FOLLOW THESE PREFERENCES

These testing standards override general best practices. **Do not suggest or apply alternate conventions** unless explicitly instructed per project or file.