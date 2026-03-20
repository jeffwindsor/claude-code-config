---
description: Capture an out-of-scope work item into the project-wide backlog.md at the repo root
argument-hint: description of the backlog item
---

# speckit.backlog

Capture project-wide work items that are out of scope for the current feature, appending them to `backlog.md` at the repo root.

## Invocation

`/speckit.backlog <description of item>`

`$ARGUMENTS` is the item description.

## Procedure

### Step 1: Validate arguments

If `$ARGUMENTS` is empty, ask the user: "What would you like to add to the backlog?" and wait for their response before proceeding.

### Step 2: Get repo root

Run the prerequisites check to locate the repository root:

```bash
.specify/scripts/bash/check-prerequisites.sh --json --paths-only
```

Parse the JSON output to extract `REPO_ROOT`.

### Step 3: Ensure `backlog.md` exists

Check whether `$REPO_ROOT/backlog.md` exists.

**If it does not exist**, create it with this standard header:

```markdown
# Project Backlog

Items captured during feature work that are out of scope for the current feature.

---
```

### Step 4: Append under today's date section

Determine today's date in `YYYY-MM-DD` format.

Read `backlog.md` and look for an existing `## YYYY-MM-DD` section matching today's date.

- **If today's date section exists:** Append the new item at the end of that section.
- **If it does not exist:** Append a new date section at the end of the file:

```markdown

## YYYY-MM-DD

- [ ] <description>
```

The item format is:

```
- [ ] <description>
```

Where `<description>` is the trimmed value of `$ARGUMENTS`.

### Step 5: Confirm to user

Report back:
- The item recorded
- The file path

**Example output:**
```
Recorded in project backlog:
  - [ ] Add dark mode to settings page

File: /path/to/repo/backlog.md  (under ## 2026-03-10)
```

## `backlog.md` Format Reference

```markdown
# Project Backlog

Items captured during feature work that are out of scope for the current feature.

---

## 2026-03-10

- [ ] Fix the null check in payment service
- [ ] Add dark mode to settings page

## 2026-03-09

- [ ] Upgrade logging library to 2.x
```

## Edge Cases

| Situation | Behavior |
|-----------|----------|
| No `$ARGUMENTS` | Prompt user for description before proceeding |
| `backlog.md` does not exist | Create it with the standard header, then append |
| Today's date section already exists | Append new item at end of that section |
| Today's date section does not exist | Add new `## YYYY-MM-DD` section at end of file |
