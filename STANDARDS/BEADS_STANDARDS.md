# Universal Beads Workflow Standards (MANDATORY)

This file defines **non-optional** beads workflow standards that apply to ALL projects using beads. These preferences **override default Claude behavior**. Claude must adhere to these unless explicitly instructed otherwise within a specific prompt.

---

## BEADS WORKFLOW STANDARDS

### Decision Impact Review (MANDATORY)

When closing any decision-type bead, you MUST perform a full impact review following this checklist:

#### 1. Document the Decision

```bash
bd update <decision-bead> --notes="DECISION: [what was decided]
Rationale: [why this was chosen]
Implications: [what this means for implementation]"
```

Record the decision context so future sessions understand the choice.

#### 2. Review Blocked Tasks

```bash
bd show <decision-bead>  # See what tasks this decision blocks
```

For each blocked task, ask:
- **Still relevant?** Does this task still make sense given the decision?
- **Needs modification?** Should the task description or approach change?
- **Creates new work?** Does the decision require additional tasks?

#### 3. Identify Gaps

Compare the decision's implications against existing beads:

**Critical questions:**
- What new tasks does this decision require?
- Do any existing tasks become obsolete?
- Have dependencies or priorities changed?
- Is the overall plan still coherent?

**Example patterns:**
- Decision: "Use shell scripts instead of library" → May need: script creation, validation, documentation tasks
- Decision: "Use hybrid approach" → May need: both original tasks AND new bridging tasks
- Decision: "Change architecture" → May need: refactor existing tasks, add migration tasks

#### 4. Create Missing Beads

```bash
# Create new tasks revealed by the decision
bd create --title="..." --type=task --priority=N --description="..."

# Update tasks that need modification
bd update <bead> --description="Updated based on decision: [explanation]"

# Close tasks that are no longer relevant
bd close <bead> --reason="Obsolete after decision: [explanation]"
```

#### 5. Update Dependencies

```bash
# Add new dependencies
bd dep add <dependent-task> <blocking-task>

# Verify dependency graph makes sense
bd blocked  # Check for unintended blocks
bd ready    # Confirm logical next steps are available
```

#### 6. Verify Plan Completeness

```bash
bd ready   # Should show logical next steps
bd stats   # Sanity check: does task count make sense?
```

Ask yourself:
- Can someone start working immediately on ready tasks?
- Is there a clear path from current state to completion?
- Are there obvious gaps in the workflow?

#### 7. Close Decision Bead

Only after completing steps 1-6:

```bash
bd close <decision-bead> --reason="Decision implemented: [summary]"
```

---

### When to Trigger Decision Impact Review

**Always trigger for:**
- Closing decision-type beads (explicitly labeled as decisions)
- After presenting options to user and user selects one
- When approach changes mid-implementation (e.g., "actually, let's use X instead of Y")

**Consider triggering for:**
- Discovering technical constraints that change scope
- Scope changes (expansion or contraction)
- User feedback that invalidates prior assumptions
- Implementation revealing unexpected complexity

**Red flags - Stop and Review:**
If you observe any of these, pause and perform impact review:
- Major architectural decision made
- Technology choice changed (library → scripts, REST → gRPC, etc.)
- User selected between multiple approaches
- "This is more complex than expected"
- "We need to do X before Y" (new dependency discovered)

---

### Plan-Reality Synchronization Principles

**Core principle:** Beads must reflect reality, not aspirations.

- **Plans are living documents** - Decisions invalidate previous assumptions
- **Never close a decision bead** without checking downstream impact
- **Prevent divergence** between what was decided and what beads track
- **When creating initial beads from a plan**, link them to the decision bead as blockers so you're forced to review them when the decision is made

**Anti-patterns to avoid:**
- ✗ Closing decision bead immediately without impact review
- ✗ Creating one new bead but missing others the decision requires
- ✗ Assuming existing beads are still valid without verification
- ✗ Not updating priorities when decision changes importance
- ✗ Forgetting to adjust dependencies when workflow changes

**Correct patterns:**
- ✓ Decision → Full impact review → Update all affected beads → Close decision
- ✓ Treat decision beads as checkpoints that force plan review
- ✓ Ask "What else?" after creating obvious new beads
- ✓ Verify ready tasks make sense as next steps

---

### Recovery When Gaps Are Discovered

If you discover beads don't match reality (e.g., user asks "are there other tasks without beads?"):

1. **Acknowledge the gap** - Don't defend incomplete planning
2. **Perform retroactive impact review** - Go through the checklist above
3. **Create missing beads** - Fill the gaps immediately
4. **Learn the pattern** - What should have triggered the review earlier?
5. **Update this document** - If pattern isn't covered, propose addition

**Example recovery:**
```bash
# User points out missing tasks
bd search "decision"  # Find the decision point
bd show <decision-bead>  # Review what was decided
# Ask: "What tasks should this decision have created?"
bd create ...  # Fill gaps
bd dep add ...  # Fix dependencies
bd ready  # Verify completeness
```

---

### Optional: Project-Specific Workflow Documentation

For complex projects, consider creating `docs/beads-workflow.md` with:
- **Project-specific examples** - Real beads from the project showing decisions and impact
- **Lessons learned** - Specific gaps encountered and how they were fixed
- **Recovery strategies** - Project-specific patterns for staying synchronized
- **Team guidelines** - Multi-person workflow considerations

This project doc is **optional** - the global standard above is sufficient for all projects. Create project docs when:
- Project is long-running with multiple decision points
- Multiple people work with the beads
- Workflow patterns specific to this project emerge
- You want to document specific anti-patterns encountered

---

## CLAUDE MUST FOLLOW THESE PREFERENCES

These beads workflow standards override general best practices. **Do not skip the decision impact review process** unless explicitly instructed per project or file.
