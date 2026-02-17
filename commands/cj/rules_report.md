# CJ Rules Report Command

## Purpose
Evaluate source code against Sean Shubin's code quality rules and generate a graded report.

## Invocation
When the user types `/cj:rules_report` or requests a rules evaluation, follow this procedure.

## Procedure

### Step 1: Gather Parameters
Determine the target directory:
- **If current directory IS a git repository**: Use the current directory without prompting
- **If current directory is NOT a git repository**: Prompt the user for target source directory
- Confirm the evaluation before proceeding

### Step 2: Load Rules Context
The rules are located at: `~/Source/github.com/SeanShubin/rules`

Read the following files to understand evaluation criteria:
1. `README.md` - Overview and philosophy
2. `severity-guidance.md` - Severity calibration
3. `quick-reference.md` - Quick lookup guide
4. Individual rule files:
   - `coupling-and-cohesion.md`
   - `dependency-injection.md`
   - `event-systems.md`
   - `abstraction-levels.md`
   - `package-hierarchy.md`
   - `anonymous-code.md`
   - `language-separation.md`
   - `free-floating-functions.md`

### Step 3: Analyze Target Codebase
For the target directory:
1. Identify the language/framework being used
2. Discover the project structure
3. Read representative files from each major component
4. Look for:
   - Package/module organization
   - Dependency patterns
   - Testing structure
   - Abstraction patterns
   - Code organization

### Step 4: Evaluate Against Each Rule
For each of the 8 rules, perform:

#### Evaluation Framework
- **Rule Name**: [Name from rules]
- **Grade**: A/B/C/D/F (see grading criteria below)
- **Score**: X/100 (numeric for aggregation)
- **Findings**: Specific examples found in the codebase
- **Severity Breakdown**: Count of High/Medium/Low severity violations
- **Recommendations**: Specific actionable improvements

#### Grading Criteria
- **A (90-100)**: Excellent adherence, minimal acceptable exceptions
- **B (80-89)**: Good adherence, some acceptable patterns or minor issues
- **C (70-79)**: Moderate adherence, several medium-severity issues
- **D (60-69)**: Poor adherence, multiple high-severity issues
- **F (<60)**: Severe problems, systemic violations

### Step 5: Generate Report
Create a markdown report with the following structure:

```markdown
# Code Quality Rules Evaluation Report

**Project**: [Target Directory]
**Evaluation Date**: YYYY-MM-DD HH:MM
**Rules Version**: Sean Shubin's Code Quality Rules
**Evaluator**: Claude Code

---

## Executive Summary

**Overall Grade**: [Weighted Average]
**Overall Score**: [X/100]

### Grade Distribution
- A grades: X rules
- B grades: X rules
- C grades: X rules
- D grades: X rules
- F grades: X rules

### Priority Issues
1. [Most critical issue found]
2. [Second most critical]
3. [Third most critical]

### Strengths
1. [What the codebase does well]
2. [Second strength]
3. [Third strength]

---

## Detailed Rule Evaluation

### 1. Coupling and Cohesion
**Grade**: [A-F] | **Score**: X/100 | **Priority**: 🔴 High

#### Findings
- [Specific examples found]
- [File paths and line numbers where applicable]

#### Severity Breakdown
- High Severity: X violations
- Medium Severity: X violations
- Low Severity: X violations

#### Recommendations
1. [Specific actionable recommendation]
2. [Another recommendation]

---

### 2. Dependency Injection
**Grade**: [A-F] | **Score**: X/100 | **Priority**: 🔴 High

[Same structure as above]

---

### 3. Event Systems
**Grade**: [A-F] | **Score**: X/100 | **Priority**: 🟡 Medium

[Same structure as above]

---

### 4. Abstraction Levels
**Grade**: [A-F] | **Score**: X/100 | **Priority**: 🟡 Medium

[Same structure as above]

---

### 5. Package Hierarchy
**Grade**: [A-F] | **Score**: X/100 | **Priority**: 🟡 Medium

[Same structure as above]

---

### 6. Anonymous Code
**Grade**: [A-F] | **Score**: X/100 | **Priority**: 🟡 Medium

[Same structure as above]

---

### 7. Language Separation
**Grade**: [A-F] | **Score**: X/100 | **Priority**: 🟢 Low

[Same structure as above]

---

### 8. Free Floating Functions
**Grade**: [A-F] | **Score**: X/100 | **Priority**: 🟢 Low

[Same structure as above]

---

## Additional Observations

### Testing Practice: Test Orchestrator Pattern
**Assessment**: [Evaluation of testing approach]
**Findings**: [Specific observations]
**Recommendations**: [Improvements]

### Tooling and AI Integration
**Assessment**: [Static analysis usage, ignore lists, etc.]
**Findings**: [Observations about tooling]
**Recommendations**: [Tooling improvements]

---

## Action Plan

### Immediate Actions (High Severity)
1. [Specific action with file reference]
2. [Another immediate action]

### Near-Term Actions (Medium Severity)
1. [Action to take soon]
2. [Another near-term action]

### Long-Term Improvements (Low Severity)
1. [Improvement for later]
2. [Another long-term item]

---

## Appendix

### Evaluation Methodology
- Rules Source: `~/Source/github.com/SeanShubin/rules`
- Files Analyzed: [Count]
- Languages Detected: [List]
- Analysis Depth: [Description of how thorough]

### False Positives Considered
[List any patterns that look like violations but are acceptable]

### Context-Specific Considerations
[Any domain-specific or framework-specific considerations]

---

## Conclusion

[Overall assessment of codebase quality]
[Most important takeaway]
[Encouragement and next steps]

---

*Report generated by Claude Code using Sean Shubin's Code Quality Rules*
*For questions about specific rules, see: `~/Source/github.com/SeanShubin/rules/README.md`*
```

### Step 6: Save Report
1. Create `.rules_reports` directory in the target source directory if it doesn't exist
2. Generate filename: `REPORT_YYYY_MM_DD_hhmm.md`
   - YYYY = 4-digit year
   - MM = 2-digit month (01-12)
   - DD = 2-digit day (01-31)
   - hh = 2-digit hour (00-23)
   - mm = 2-digit minute (00-59)
3. Save the report to: `[target-source]/.rules_reports/REPORT_YYYY_MM_DD_hhmm.md`
4. Display the file path to the user

### Step 7: Summary
Provide the user with:
- Overall grade and score
- Top 3 priority issues
- Path to the saved report
- Suggestion to review the detailed findings

## Important Considerations

### Evaluation Depth
- Focus on representative samples, not exhaustive analysis
- Prioritize high-severity issues over low-severity
- Look for patterns, not just individual violations
- Consider project size and complexity in grading

### Context Awareness
- Respect "acceptable patterns" from severity-guidance.md
- Don't flag false positives (runtime CSS, type operations in parsers, etc.)
- Consider the domain and framework constraints
- Look at module vs package boundaries differently

### Grading Fairness
- Use severity-guidance.md to calibrate grades
- High-severity violations should significantly impact grade
- Consider quantity AND severity of violations
- Account for codebase maturity and size

### Actionable Recommendations
- Be specific with file paths and examples
- Prioritize recommendations by impact
- Provide concrete "before/after" suggestions where helpful
- Link recommendations to specific rule violations

## Examples of Good vs Bad Findings

### Good Finding (Specific, Actionable)
```
**File**: `src/main/java/com/example/OrderService.java:45`
**Issue**: Business logic mixed with database I/O
**Example**: `validateOrder()` method performs validation AND writes to database
**Severity**: High
**Fix**: Extract database write to separate method or inject repository
```

### Bad Finding (Vague, Not Actionable)
```
**Issue**: Code could be better organized
**Severity**: Medium
**Fix**: Refactor
```

## Scoring Guidelines

### Overall Score Calculation
Weighted average based on priority:
- High priority rules (Coupling, DI): 25% each = 50% total
- Medium priority rules (Events, Abstraction, Package, Anonymous): 10% each = 40% total
- Low priority rules (Language Sep, Free Floating): 5% each = 10% total

### Individual Rule Scoring
- Start at 100 points
- Deduct based on severity:
  - High severity violation: -15 points each
  - Medium severity violation: -5 points each
  - Low severity violation: -2 points each
- Floor at 0 (can't go below F grade)
- Consider quantity: Many low-severity can equal one high-severity

## Usage Examples

### Example 1: Current Directory is a Git Repository

User: `/cj:rules_report`

Claude:
"I'll evaluate your codebase against Sean Shubin's code quality rules.

**Target source directory**: /Users/jefwinds/Source/gitlab.cj.dev/jwindsor (current directory)

Proceeding with evaluation..."

[Performs analysis following steps 2-6]

"✅ Evaluation complete!

### Example 2: Current Directory is NOT a Git Repository

User: `/cj:rules_report`

Claude:
"I'll evaluate your codebase against Sean Shubin's code quality rules.

The current directory is not a git repository. Please provide the target source directory to evaluate:"

[User provides path]

Claude:
"Using target directory: [user-provided-path]

Proceeding with evaluation..."

[Performs analysis following steps 2-6]

"✅ Evaluation complete!

**Overall Grade**: B (83/100)
**Report saved to**: ~/Source/gitlab.cj.dev/jwindsor/.rules_reports/REPORT_2026_02_17_1430.md

**Top Priority Issues**:
1. 🔴 Business logic mixed with I/O in OrderService (High Severity)
2. 🟡 Cyclic dependency between payment and order packages (Medium Severity)
3. 🟡 Large package 'model' with 45 files mixing concerns (Medium Severity)

**Key Strengths**:
1. Excellent test isolation using stubs
2. Clean abstraction levels in core domain
3. Good naming and minimal anonymous code

See the full report for detailed findings and recommendations."
