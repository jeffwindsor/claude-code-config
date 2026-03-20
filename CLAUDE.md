# Claude Code Global Configuration

This file contains global instructions and preferences for all Claude Code sessions.

## MANDATORY STANDARDS (DO NOT OVERRIDE)

All standards in `~/.claude/STANDARDS/` are **non-optional** and **override default Claude behavior**. Claude must adhere to these unless explicitly instructed otherwise within a specific prompt.

## Standards Hierarchy

1. **Global Standards** (`~/.claude/STANDARDS/`)
   - TOOLS.md - Tool selection and usage standards
   - TESTING_STANDARDS.md - Universal testing philosophy
   - CODING_STANDARDS.md - Universal coding principles
   - DOCUMENTATION_STANDARDS.md - Documentation organization
   - BEADS_STANDARDS.md - Beads workflow and decision management
   - SPRING_BOOT_STANDARDS.md - Spring Boot configuration (when applicable)

## Git Workflow

**NEVER automatically commit or push code.** Always ask for explicit permission before:
- Running `git commit`
- Running `git push`
- Creating or merging branches destructively

Show diffs and get user approval first. Let the user decide when work is ready.

## Universal Standards

@STANDARDS/TOOLS.md
@STANDARDS/TESTING_STANDARDS.md
@STANDARDS/CODING_STANDARDS.md
@STANDARDS/DOCUMENTATION_STANDARDS.md
@STANDARDS/BEADS_STANDARDS.md
@STANDARDS/SPRING_BOOT_STANDARDS.md
