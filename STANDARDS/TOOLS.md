# Universal Tool Usage Standards

## Core Principle

**Use specialized tools instead of bash commands whenever possible**. Specialized tools provide:
- Better error handling and validation
- More efficient execution
- Clearer intent and maintainability
- Reduced permission prompts
- Better integration with Claude Code

---

## Command Translation Guide (QUICK REFERENCE)

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

## File Operations

### Read
**Use**: `Read` tool | **Never**: `cat`, `head`, `tail`, `less`, `more`
- Supports line ranges (offset, limit), images, PDFs, Jupyter notebooks

### Write
**Use**: `Write` tool | **Never**: `echo >`, `cat > file <<EOF`, `printf >`, `tee`
- Handles multiline content, validates paths, proper error handling

### Edit
**Use**: `Edit` tool | **Never**: `sed`, `awk`, `perl -pi`, `ex`
- Exact string replacement, validates string exists, option for replace_all

**Examples**:
```bash
# ✗ WRONG
cat src/main/java/MyClass.java
echo "content" > file.txt
sed -i 's/old/new/g' file.txt

# ✓ CORRECT
Read(file_path="src/main/java/MyClass.java")
Write(file_path="file.txt", content="content")
Edit(file_path="file.txt", old_string="old", new_string="new")
```

---

## File Discovery

### Find Files
**Use**: `Glob` tool | **Never**: `find`, `ls` (for pattern matching)
- Supports glob patterns like `**/*.java`, fast for any codebase size, sorted results

### Search Contents
**Use**: `Grep` tool | **Never**: `grep`, `rg`, `ag`, `ack`
- Full regex support, output modes (content/files_with_matches/count), built on ripgrep

**Examples**:
```bash
# ✗ WRONG
find . -name "*.java"
grep -r "class MyService" src/

# ✓ CORRECT
Glob(pattern="**/*.java")
Grep(pattern="class MyService", path="src/", output_mode="files_with_matches")
```

---

## When to Use Bash

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

## Complex Operations

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

## Communication Standards

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

## Parallel Operations

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

## Tool Usage Checklist

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

## Exception Handling

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
