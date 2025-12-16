# CLAUDE.md - AI Assistant Guide

This document provides comprehensive guidance for AI assistants working with this repository. It covers codebase structure, development workflows, and key conventions.

## Repository Overview

**Repository:** jasonballuck/claude-code
**Primary Branch:** main (or master)
**Current State:** Early stage - minimal codebase
**Last Updated:** 2025-12-16

## Repository Structure

```
claude-code/
├── .git/              # Git version control
├── README.md          # Project documentation
└── CLAUDE.md          # This file - AI assistant guide
```

### Current Status
This is a newly initialized repository with minimal content. The structure will evolve as the project develops.

## Development Workflow

### Branch Strategy

**Feature Branches:**
- All development work should be done on feature branches
- Branch naming convention: `claude/claude-md-<session-id>`
- Example: `claude/claude-md-mj8sl7hfamkg8392-wsa6D`
- **CRITICAL:** Branch names must start with `claude/` and end with the matching session ID

**Working with Branches:**
1. Check current branch: `git branch --show-current`
2. Create new branch if needed: `git checkout -b claude/claude-md-<session-id>`
3. Make your changes and commit regularly
4. Push to the designated feature branch

### Git Operations Best Practices

**Committing Changes:**
```bash
# Stage changes
git add <files>

# Commit with descriptive message
git commit -m "$(cat <<'EOF'
<Type>: <Brief description>

<Detailed explanation if needed>
EOF
)"
```

**Commit Message Convention:**
- Use clear, descriptive commit messages
- Focus on the "why" rather than the "what"
- Types: feat, fix, docs, refactor, test, chore, style

**Pushing Changes:**
```bash
# Always push with -u flag for feature branches
git push -u origin <branch-name>
```

**CRITICAL:** Branch must start with 'claude/' and end with matching session ID, otherwise push will fail with 403 HTTP code.

**Retry Logic for Network Issues:**
- Retry failed push/fetch/pull operations up to 4 times
- Use exponential backoff: 2s, 4s, 8s, 16s
- Example:
  ```bash
  git push -u origin <branch> || sleep 2 && git push -u origin <branch> || sleep 4 && git push -u origin <branch>
  ```

### Pull Request Workflow

1. **Develop** on your designated feature branch
2. **Commit** changes with clear messages
3. **Push** to the feature branch
4. **Create PR** when ready:
   ```bash
   gh pr create --title "Description" --body "$(cat <<'EOF'
   ## Summary
   - Change 1
   - Change 2

   ## Test Plan
   - [ ] Test 1
   - [ ] Test 2
   EOF
   )"
   ```

## Code Style and Conventions

### General Principles

1. **Simplicity First:** Avoid over-engineering
2. **Direct Changes:** Only modify what's necessary
3. **No Premature Optimization:** Don't add features not requested
4. **Clean Deletions:** Remove unused code completely (no comments like "// removed")

### Security Considerations

- Always validate user input at system boundaries
- Be aware of OWASP Top 10 vulnerabilities:
  - Command injection
  - XSS (Cross-Site Scripting)
  - SQL injection
  - Insecure deserialization
  - etc.
- Review code for security issues before committing

### File Operations

**Prefer specialized tools:**
- **Read files:** Use `Read` tool (not `cat`)
- **Edit files:** Use `Edit` tool (not `sed`/`awk`)
- **Write files:** Use `Write` tool (not `echo >` or heredocs)
- **Search files:** Use `Glob` tool (not `find`/`ls`)
- **Search content:** Use `Grep` tool (not `grep`/`rg`)

**File Modification Guidelines:**
- Always read a file before editing it
- Prefer editing existing files over creating new ones
- Never create documentation files unless explicitly requested
- Match existing code style and formatting

## Task Management

### Using TodoWrite Tool

The TodoWrite tool is CRITICAL for tracking progress on complex tasks.

**When to use:**
- Complex multi-step tasks (3+ steps)
- Non-trivial implementations
- Multiple user requests
- Any time you need to track progress

**Task States:**
- `pending`: Not started
- `in_progress`: Currently working (ONLY ONE at a time)
- `completed`: Finished successfully

**Best Practices:**
1. Create todos at the start of complex tasks
2. Mark ONE task as `in_progress` before starting
3. Complete tasks immediately when done (don't batch)
4. Update todos in real-time as you work

**Example:**
```javascript
// Planning phase
todos: [
  { content: "Research existing patterns", status: "pending", activeForm: "Researching existing patterns" },
  { content: "Implement feature", status: "pending", activeForm: "Implementing feature" },
  { content: "Write tests", status: "pending", activeForm: "Writing tests" }
]

// Working on first task
todos: [
  { content: "Research existing patterns", status: "in_progress", activeForm: "Researching existing patterns" },
  { content: "Implement feature", status: "pending", activeForm: "Implementing feature" },
  { content: "Write tests", status: "pending", activeForm: "Writing tests" }
]
```

## Codebase Exploration

### Finding Code

**For specific files/patterns:**
```bash
# Use Glob tool
pattern: "**/*.js"
pattern: "src/**/*.ts"
```

**For content search:**
```bash
# Use Grep tool
pattern: "functionName"
output_mode: "content"
```

**For complex exploration:**
- Use Task tool with `subagent_type='Explore'`
- Thoroughness levels: "quick", "medium", "very thorough"
- Use when you need to understand architecture or find multiple related files

**Example scenarios:**
- "Where are errors handled?" → Use Explore agent
- "What's the codebase structure?" → Use Explore agent
- "Find class Foo" → Use Glob tool directly

## Common Tasks

### Adding a New Feature

1. Read relevant existing code
2. Use TodoWrite to plan the implementation
3. Implement changes incrementally
4. Test as you go
5. Commit with clear messages
6. Push to feature branch

### Fixing a Bug

1. Reproduce and understand the issue
2. Read the relevant code
3. Make minimal fix
4. Verify the fix works
5. Commit and push

### Refactoring Code

1. Understand existing implementation first
2. Only refactor what's requested
3. Don't add extra "improvements"
4. Ensure functionality remains unchanged
5. Commit and push

### Code Review

1. Read the code thoroughly
2. Check for:
   - Security vulnerabilities
   - Logic errors
   - Code style consistency
   - Over-engineering
   - Missing error handling (at boundaries only)
3. Provide specific, actionable feedback

## AI Assistant Specific Guidelines

### Communication Style

- **Concise:** Keep responses short and to the point
- **No emojis:** Unless explicitly requested
- **Technical:** Focus on facts and solutions
- **Objective:** Prioritize accuracy over validation
- **Markdown:** Use GitHub-flavored markdown for formatting

### Tool Usage

**Parallel Execution:**
- Make independent tool calls in parallel when possible
- Example: Run `git status`, `git diff`, and `git log` together
- Never use placeholders or guess parameters

**Sequential Execution:**
- Use for dependent operations
- Example: `git add . && git commit -m "message" && git push`

**Task Tool (Subagents):**
- `Explore`: Fast codebase exploration ("quick", "medium", "very thorough")
- `Plan`: Design implementation plans for complex tasks
- `general-purpose`: Multi-step research and coding tasks

### Code References

When referencing code, use the pattern: `file_path:line_number`

Example: "The error is handled in src/utils/error.ts:42"

### Error Handling

1. Read error messages carefully
2. Check recent changes that might have caused issues
3. Look for common issues (typos, missing imports, etc.)
4. Test fixes incrementally
5. Don't mark tasks as complete if errors remain

### When to Ask for Clarification

Ask the user when:
- Requirements are ambiguous
- Multiple valid approaches exist
- Architectural decisions are needed
- Destructive operations are requested
- You need authorization context for security tools

### What NOT to Do

❌ Don't create files unless necessary
❌ Don't add features not requested
❌ Don't over-engineer solutions
❌ Don't add comments to unchanged code
❌ Don't use backward-compatibility hacks
❌ Don't add error handling for impossible scenarios
❌ Don't create abstractions for one-time operations
❌ Don't commit without explicit user request
❌ Don't push to wrong branches
❌ Don't use bash for file operations

### What TO Do

✅ Read files before editing
✅ Use specialized tools (Read, Edit, Write)
✅ Make minimal, focused changes
✅ Commit with clear messages
✅ Push to designated branches
✅ Track complex tasks with TodoWrite
✅ Explore thoroughly before major changes
✅ Test incrementally
✅ Ask for clarification when needed
✅ Focus on security at system boundaries

## Environment Information

**Platform:** Linux
**OS Version:** Linux 4.4.0
**Git Config:** See `.git/config`
**Remote:** origin at `http://local_proxy@127.0.0.1:53175/git/jasonballuck/claude-code`

## Troubleshooting

### Git Push Fails with 403

**Cause:** Branch name doesn't match required pattern
**Solution:** Ensure branch starts with `claude/` and ends with matching session ID

### Cannot Read File

**Cause:** Using wrong tool or incorrect path
**Solution:** Use `Read` tool with absolute path

### Tests Failing

**Cause:** Recent changes introduced issues
**Solution:**
1. Review recent commits
2. Read test output carefully
3. Fix issues incrementally
4. Don't mark tasks complete until tests pass

### Merge Conflicts

**Solution:**
1. Fetch latest changes: `git fetch origin`
2. Check differences: `git diff origin/main`
3. Resolve conflicts manually
4. Test thoroughly
5. Commit resolution

## Resources

- GitHub CLI: `gh` commands for PR management
- Git Documentation: Standard git operations
- Repository Issues: https://github.com/jasonballuck/claude-code/issues

## Updating This Document

This document should be updated when:
- Repository structure changes significantly
- New conventions are established
- Common issues are discovered
- Development workflow evolves

To update: Edit this file and commit with message type `docs`.

---

**Last Updated:** 2025-12-16
**Document Version:** 1.0.0
**Maintained by:** AI Assistants working with this repository
