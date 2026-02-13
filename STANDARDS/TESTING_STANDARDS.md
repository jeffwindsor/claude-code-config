# Universal Testing Standards

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

**TDD States:**
- **RED (Proper)**: Code compiles, tests run but fail, production code has stub implementation
- **RED (Improper)**: Compilation errors, missing structure, tests cannot execute
- **GREEN**: Code compiles, tests pass, production code implements actual logic

**TDD Workflow (ONE TEST AT A TIME):**
1. Write ONE test → Create minimal structure (proper RED)
2. STOP - User confirms RED
3. Implement minimal code for ONLY this test (GREEN)
4. STOP - User confirms GREEN
5. Provide commit message
6. STOP - Wait for commit confirmation
7. Refactor if needed, repeat

**Critical Rules**: Never write multiple tests at once | Never implement before RED confirmed | Never proceed before commit confirmed | User controls pace | Commit after every GREEN

**Commit Message Format**:
```
test: add test for [specific behavior]

- Added test: [test name]
- Implements: [what production code was added/changed]
- Scenario: [which scenario this supports]
```

### Project-Specific TDD Workflows

Some projects may define detailed multi-agent TDD workflows. Check for:
- **docs/TDD_WORKFLOW.md** - Project-specific TDD process (e.g., 3-agent cross-validation)
- **AGENTS.md** - Agent roles and workflow guidance

When these exist, they **override/extend** the general TDD standards above.