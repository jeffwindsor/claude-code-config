---
description: Record a deferred task into the current feature's tasks.md without breaking your flow
argument-hint: description of the deferred work
---

# speckit.todo

Record a deferred task into the current feature's `tasks.md`, keeping you in flow without losing the thought.

## Invocation

`/speckit.todo <description of deferred work>`

`$ARGUMENTS` is the task description.

## Procedure

### Step 1: Validate arguments

If `$ARGUMENTS` is empty, ask the user: "What deferred task would you like to record?" and wait for their response before proceeding.

### Step 2: Get feature context

Run the prerequisites check to locate the active feature:

```bash
.specify/scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks
```

Parse the JSON output to extract:
- `FEATURE_DIR` — path to the current feature directory
- `TASKS` — path to `tasks.md`
- Confirm `tasks.md` exists

**If `tasks.md` is not found or the script fails:** Fall through to backlog behavior — treat this as a `/speckit.backlog` call instead. Notify the user: "No tasks.md found for an active feature — recording in project backlog instead." Then follow the `/speckit.backlog` procedure with the same `$ARGUMENTS`.

### Step 3: Assign the next task ID

Read `tasks.md` and scan for all existing task IDs in the format `T` followed by digits (e.g., `T001`, `T042`). Find the highest numeric value and increment by 1. Zero-pad to match the existing format (e.g., if highest is `T042`, next is `T043`; if no tasks exist yet, start at `T001`).

### Step 4: Find the insertion point

Scan `tasks.md` for an appropriate section to append the deferred item:

1. **Preferred:** Find the `## Polish & Cross-Cutting Concerns` section (or any section whose heading contains "Polish" or "Deferred"). Append the new task at the end of that section.
2. **Fallback:** If no such section exists, look for `## Dependencies` and insert a new `## Deferred Work` section immediately before it.
3. **Last resort:** If neither section exists, append a new `## Deferred Work` section at the very end of the file.

### Step 5: Append the deferred task

Add the following line at the chosen insertion point:

```
- [ ] TXXX [DEFERRED] <description>
```

Where:
- `TXXX` is the assigned task ID from Step 3
- `<description>` is the trimmed value of `$ARGUMENTS`
- No `[P]` marker (deferred items are not parallelized)
- No `[USx]` story label (not tied to a specific user story)

### Step 6: Confirm to user

Report back:
- The task ID assigned (e.g., `T043`)
- The full line appended
- The file path where it was recorded

**Example output:**
```
Recorded deferred task T043 in tasks.md:
  - [ ] T043 [DEFERRED] Fix null check in PaymentService.java

File: .specify/features/payments/tasks.md
```

## Edge Cases

| Situation | Behavior |
|-----------|----------|
| No `$ARGUMENTS` | Prompt user for description before proceeding |
| No active feature / no `tasks.md` | Record in `backlog.md` at repo root (backlog fallback) |
| No Polish or Deferred section in `tasks.md` | Create `## Deferred Work` section before `## Dependencies` (or at end) |
| No existing task IDs | Start at `T001` |
| Mixed zero-padding in file | Match the padding format of the majority of existing IDs |
