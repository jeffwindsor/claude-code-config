---
description: Launch autonomous agent to complete all ready beads tasks
---

Launch the autonomous beads task-agent to complete all ready tasks in the project.

# Agent Launch

Use the Task tool with the following configuration:
- **subagent_type**: `beads:task-agent`
- **run_in_background**: `true`
- **description**: "Complete beads tasks autonomously"
- **prompt**: "Find and complete all ready beads tasks. For each task: (1) claim it by setting status to in_progress, (2) complete the work following project standards, (3) run tests if applicable, (4) close the task with completion message, (5) continue to next ready task. Repeat until no ready tasks remain. Report progress clearly."

# Monitoring

After launching:
1. The agent runs in background
2. You'll receive an output_file path
3. Monitor progress with: `tail -f <output_file>` or periodic reads
4. Agent will continue autonomously until all ready tasks are complete

# Important Notes

- Agent only works on **ready** (unblocked) tasks
- Follows priority order (P0 > P1 > P2 > P3 > P4)
- Creates new issues for discovered bugs/TODOs
- Updates task status automatically
- Stops when no ready tasks remain

# Before Running

Ensure project has ready tasks:
```bash
bd ready
```

If no ready tasks, check blocked issues or create new tasks first.
