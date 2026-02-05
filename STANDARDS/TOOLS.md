# Universal Tool Usage Standards (MANDATORY)

This file defines **non-optional** tool usage standards that apply to ALL projects. These preferences **override default Claude behavior**. Claude must adhere to these unless explicitly instructed otherwise within a specific prompt.

---

## TOOL SELECTION STANDARDS

### Core Principle

**Use specialized tools instead of bash commands whenever possible**. Specialized tools provide:
- Better error handling and validation
- More efficient execution
- Clearer intent and maintainability
- Reduced permission prompts
- Better integration with Claude Code

---

## FILE OPERATIONS

### Reading Files

**ALWAYS USE**: `Read` tool
**NEVER USE**: `cat`, `head`, `tail`, `less`, `more`

```bash
# ✗ WRONG
cat src/main/java/MyClass.java
head -n 20 config.properties
tail -f application.log

# ✓ CORRECT
Read tool with file_path parameter
- Supports line ranges (offset, limit)
- Handles images, PDFs, Jupyter notebooks
- Provides proper error handling
```

### Writing Files

**ALWAYS USE**: `Write` tool
**NEVER USE**: `echo >`, `cat > file <<EOF`, `printf >`, `tee`

```bash
# ✗ WRONG
echo "content" > file.txt
cat > config.yaml <<EOF
key: value
EOF

# ✓ CORRECT
Write tool with file_path and content parameters
- Handles multiline content properly
- Validates paths
- Proper error handling
```

### Editing Files

**ALWAYS USE**: `Edit` tool
**NEVER USE**: `sed`, `awk`, `perl -pi`, `ex`

```bash
# ✗ WRONG
sed -i 's/old/new/g' file.txt
awk '{print $1}' file.txt
perl -pi -e 's/pattern/replacement/' file.txt

# ✓ CORRECT
Edit tool with old_string and new_string parameters
- Exact string replacement
- Validation that string exists
- Option for replace_all
```

---

## FILE DISCOVERY

### Finding Files

**ALWAYS USE**: `Glob` tool
**NEVER USE**: `find`, `ls` (for pattern matching)

```bash
# ✗ WRONG
find . -name "*.java"
ls src/**/*.properties

# ✓ CORRECT
Glob tool with pattern parameter
- Supports glob patterns like "**/*.java"
- Fast for any codebase size
- Returns sorted results
```

### Searching File Contents

**ALWAYS USE**: `Grep` tool
**NEVER USE**: `grep`, `rg`, `ag`, `ack`

```bash
# ✗ WRONG
grep -r "pattern" src/
rg "class.*Test" --type java

# ✓ CORRECT
Grep tool with pattern parameter
- Full regex support
- Output modes: content, files_with_matches, count
- Supports -A/-B/-C context
- Built on ripgrep (optimized)
```

---

## WHEN TO USE BASH

### Allowed Bash Use Cases

Bash tool is **ONLY** for:

1. **Git operations**: `git status`, `git commit`, `git push`, `git log`, etc.
2. **Package managers**: `npm install`, `mvn clean package`, `pip install`, etc.
3. **Process management**: `ps`, `kill`, `lsof`, `netstat`, etc.
4. **System commands**: `docker`, `kubectl`, `systemctl`, etc.
5. **Build tools**: `make`, `gradle`, `cargo`, etc.
6. **Test runners**: `pytest`, `jest`, `mvn test`, etc.
7. **Shell-specific operations**: `cd`, `export`, piping commands (`|`), chaining (`&&`, `;`)

### Disallowed Bash Use Cases

**DO NOT use Bash for**:

1. **File reading**: Use `Read` tool
2. **File writing**: Use `Write` tool
3. **File editing**: Use `Edit` tool
4. **Finding files**: Use `Glob` tool
5. **Searching content**: Use `Grep` tool
6. **Communication**: Output text directly, don't use `echo` to communicate

---

## COMPLEX OPERATIONS

### Open-Ended Search/Research

**USE**: `Task` tool with appropriate subagent

```bash
# ✗ WRONG - Multiple grep/find commands trying different approaches
grep -r "error handler" src/
find . -name "*Error*.java"
rg "class.*Exception"

# ✓ CORRECT - Let an agent handle the exploration
Task tool with subagent_type=Explore
- Thorough multi-round search
- Handles uncertainty
- Returns comprehensive results
```

### Multi-Step Implementation

**USE**: Specialized agents from `Task` tool

- `general-purpose`: Complex multi-step tasks
- `Explore`: Codebase exploration and analysis
- `Plan`: Implementation planning
- `Bash`: Git operations, builds, system commands

---

## COMMAND TRANSLATION GUIDE

| Bash Command | Use This Instead | Example |
|--------------|------------------|---------|
| `cat file.txt` | Read tool | `Read(file_path="file.txt")` |
| `echo "content" > file` | Write tool | `Write(file_path="file", content="content")` |
| `sed 's/old/new/' file` | Edit tool | `Edit(file_path="file", old_string="old", new_string="new")` |
| `find . -name "*.java"` | Glob tool | `Glob(pattern="**/*.java")` |
| `grep "pattern" file` | Grep tool | `Grep(pattern="pattern", path="file", output_mode="content")` |
| `git status` | Bash tool | `Bash(command="git status")` ✓ ALLOWED |
| `mvn clean install` | Bash tool | `Bash(command="mvn clean install")` ✓ ALLOWED |
| `echo "message"` | Direct output | Just output text directly |

---

## COMMUNICATION STANDARDS

### Never Use Echo for User Communication

**WRONG**:
```bash
echo "Starting the build process..."
echo "✓ Tests passed"
echo "Next, we'll deploy to production"
```

**CORRECT**:
```
Starting the build process...

[Use Bash tool to run build]

✓ Tests passed

Next, we'll deploy to production
```

Output all explanations, status updates, and messages **directly as text**. Only use tools to perform actions.

---

## PARALLEL OPERATIONS

### When to Call Multiple Tools in One Response

**DO call multiple tools in parallel when**:
- Operations are independent (no dependencies)
- Reading multiple files that don't depend on each other
- Searching for different patterns simultaneously
- Checking multiple conditions in parallel

**Example - CORRECT parallel usage**:
```
Read(file_path="src/main/java/App.java")
Read(file_path="src/main/resources/application.properties")
Read(file_path="pom.xml")
```

**DON'T call in parallel when**:
- Second operation depends on first operation's result
- Need to verify something before proceeding
- Order matters for correctness

---

## TOOL USAGE CHECKLIST

Before using Bash, ask:
- [ ] Is this for git operations? → Use Bash ✓
- [ ] Is this for package management (npm, mvn, pip)? → Use Bash ✓
- [ ] Is this for running tests or builds? → Use Bash ✓
- [ ] Is this for Docker/Kubernetes? → Use Bash ✓
- [ ] Is this for reading files? → Use **Read tool** ✗
- [ ] Is this for writing files? → Use **Write tool** ✗
- [ ] Is this for editing files? → Use **Edit tool** ✗
- [ ] Is this for finding files? → Use **Glob tool** ✗
- [ ] Is this for searching content? → Use **Grep tool** ✗
- [ ] Is this for communicating with user? → Output text directly ✗

---

## EXAMPLES OF CORRECT TOOL USAGE

### Example 1: Creating a New Java Class

**WRONG**:
```bash
cat > src/main/java/MyClass.java <<EOF
public class MyClass {
    // implementation
}
EOF
```

**CORRECT**:
```
Write tool:
file_path: src/main/java/MyClass.java
content: public class MyClass {
    // implementation
}
```

### Example 2: Updating Configuration

**WRONG**:
```bash
sed -i 's/port=8080/port=9090/' application.properties
```

**CORRECT**:
```
Edit tool:
file_path: application.properties
old_string: port=8080
new_string: port=9090
```

### Example 3: Finding Test Files

**WRONG**:
```bash
find src/test -name "*Test.java"
```

**CORRECT**:
```
Glob tool:
pattern: src/test/**/*Test.java
```

### Example 4: Searching for a Class Definition

**WRONG**:
```bash
grep -r "class MyService" src/
```

**CORRECT**:
```
Grep tool:
pattern: class MyService
path: src/
output_mode: files_with_matches
```

### Example 5: Complex Search (Use Agent)

**WRONG**:
```bash
# Multiple grep attempts trying different patterns
grep -r "error" src/
grep -r "exception" src/
grep -r "failure" src/
```

**CORRECT**:
```
Task tool:
subagent_type: Explore
prompt: Find all error handling code in the src/ directory
```

---

## PERFORMANCE CONSIDERATIONS

### Why Specialized Tools Are Better

1. **Read tool** vs `cat`:
   - Handles large files with offset/limit
   - Supports images and PDFs
   - Automatic line numbering
   - Better error messages

2. **Write tool** vs `echo >`:
   - Validates directory exists
   - Handles special characters properly
   - Atomic writes (safer)
   - Clear error reporting

3. **Edit tool** vs `sed`:
   - Validates old_string exists (prevents silent failures)
   - Exact string matching (no regex ambiguity)
   - Can target specific occurrences
   - Better for reviewability

4. **Glob tool** vs `find`:
   - Optimized for speed
   - Consistent cross-platform behavior
   - Sorted output
   - No shell escaping issues

5. **Grep tool** vs `grep`:
   - Built on ripgrep (extremely fast)
   - Multiple output modes
   - Context support
   - File type filtering

---

## EXCEPTION HANDLING

### When Bash IS Appropriate Even for File Operations

1. **Complex shell pipelines** that would be awkward with tools:
   ```bash
   git log --oneline | head -5
   ps aux | grep java | awk '{print $2}'
   ```

2. **File operations as side effects** of other commands:
   ```bash
   git diff > changes.patch
   mvn clean install 2>&1 | tee build.log
   ```

3. **System integration** that requires shell features:
   ```bash
   export VAR=value && some-command
   source .env && npm start
   ```

But even then, prefer specialized tools when possible!

---

## CLAUDE MUST FOLLOW THESE PREFERENCES

These tool usage standards override general best practices. **Do not use bash commands for file operations** unless explicitly instructed per project or there's a compelling technical reason.

**Permission prompts are a signal**: If Claude Code asks for permission to run a bash command, it's usually a sign you should be using a specialized tool instead.
