# Claude Code Configuration

Opinionated Claude Code configuration emphasizing engineering rigor, test-driven development, and maintainable code architecture.

## Structure

```
.
├── CLAUDE.md                          # Main config - loads all standards
├── STANDARDS/                         # Mandatory engineering standards
│   ├── PRINCIPLES.md                  # SOLID, systems thinking, quality philosophy
│   ├── TESTING_STANDARDS.md           # TDD workflow, test architecture, performance targets
│   ├── CODING_STANDARDS.md            # Composition, error handling, dependency guidelines
│   ├── DOCUMENTATION_STANDARDS.md     # Navigation, organization, content structure
│   └── SPRING_BOOT_STANDARDS.md       # Spring Boot conventions (when applicable)
├── commands/beads/autonomous.md       # EXPERIEMENTAL: Custom command for autonomous task execution for beads
├── settings.json                      # Enabled plugins configuration
└── settings.local.json                # Permission settings
```

## Core Standards

### PRINCIPLES.md
- SOLID principles, DRY/KISS/YAGNI
- Evidence-based decision making
- Trade-off analysis and risk management
- Quality quadrants (functional, structural, performance, security)

### TESTING_STANDARDS.md
**Key requirements:**
- Stubs over mocks, drive non-determinism to edges
- Performance targets: <10ms ideal, >100ms failure
- Strict TDD: RED-GREEN-REFACTOR, one test at a time
- Each test self-contained, deterministic, <20ms
- Deep tests through public interfaces

**TDD Workflow:**
1. Write ONE failing test (proper RED: compiles but fails)
2. User confirms RED state
3. Implement minimal code to pass (GREEN)
4. User confirms GREEN state
5. Commit with message
6. Repeat

### CODING_STANDARDS.md
- Composition over inheritance
- Functional error handling (`Either`, `Optional`)
- Immutable structures, dependency injection
- Self-documenting code, minimal dependencies

### DOCUMENTATION_STANDARDS.md
- Break large docs (>200-300 lines) into focused files
- Breadcrumb navigation on all sub-pages
- Comprehensive TOCs with deep links
- Cross-reference related sections

## Usage

Place in a `.claude/` directory. Under `~/` for global scope or `{repo root}` for project scope.  Claude Code automatically loads `CLAUDE.md`, which imports all standards via `@STANDARDS/*.md` references.

**These are mandatory standards.** They override default Claude behavior. Project-specific standards can supplement but not contradict these global rules.

## Plugins

Configured plugins (in `settings.json`):
- context7 - up-to-date library documentation
- feature-dev - guided feature development
- code-review - PR review automation
- jdtls-lsp, lua-lsp, pyright-lsp - language servers
- beads - git-backed issue tracking

## Permissions

`settings.local.json` configures allowlists for common operations (git, grep, file reads, web fetches) to streamline workflows.

## Not Included

Session data, caches, history, and credentials are excluded (see `.gitignore`). Only configuration and standards are version-controlled.

## Philosophy

Evidence over assumptions. Code over documentation. Efficiency over verbosity. Test-first, always.
