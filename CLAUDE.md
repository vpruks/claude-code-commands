# CLAUDE.md - Global Claude Code Configuration

## Session Management

### Session Display Format

Display before EVERY action (tools, responses, commands):

```text
🌿 Branch: [branch] | 🌲 Worktree: [path] | 🆔 [sessionId] | 📌 claude-xxxx | 🤖 [model]
```

- **sessionId**: Real Claude session ID for `-r, --resume` commands
- **claude-xxxx**: Visual identifier for tmux sessions

## Development Workflow

### Core Workflow: Explore-Plan-Code-Commit

1. **Explore**: Understand codebase (use Gemini for large codebases)
2. **Plan**: Design approach (TodoWrite + appropriate subagent)
3. **Code**: Implement incrementally (language-specific subagent)
4. **Commit**: Verify and commit at logical points

### Todo-Driven Development

- `TodoRead` → View current state
- `TodoWrite` → Plan tasks
- Execute → Update status in real-time
- States: `pending` → `in_progress` (one at a time) → `completed`

### GitHub Issue Synchronization

**Sync TodoWrite → GitHub issue regularly:**

```shell
# After major task completion:
/user:gw-iss-sync [issue_number]

# Manual fallback if needed:
ISSUE=$(git branch --show-current | grep -oE '[0-9]+' | head -1)
gh issue edit $ISSUE --body "$(gh issue view $ISSUE --json body -q .body | sed 's/- \[ \] TASK_NAME/- [x] TASK_NAME/')"
```

**When to sync:**

- After major task completion
- Before creating PR (mandatory)
- Every 30-60 minutes during long sessions
- When switching context

## Git Practices

### Commit Messages

Include session ID for traceability:

```shell
git commit -m "feat: implement feature

Session: claude -r [sessionId]"
```

### Worktree Convention

- Location: `./worktrees/`
- Purpose: Parallel development
- Remember: Add `/worktrees/` to `.gitignore`

### No AI Signatures

**Never include in commits/PRs/issues:**

- ❌ AI/bot attribution
- ❌ "Generated with Claude"
- ❌ Co-Authored-By headers
- ❌ Robot emojis

## Command Reference

### gw Commands

Execute after displaying session info. Read `~/.claude/commands/gw-xxx.md` for details.

#### Issue Management

- `gw-iss-create`: Create new issue
- `gw-iss-edit`: Edit existing issue
- `gw-iss-context`: Load issue context
- `gw-iss-run`: Issue → PR workflow
- `gw-iss-implement`: Issue → local commits
- `gw-iss-sync`: Sync todos → issue checkboxes
- `gw-iss-status`: Check progress

#### PR Management

- `gw-pr-create`: Create PR with description
- `gw-pr-fix`: Fix CI failures
- `gw-pr-merge`: Squash merge and cleanup
- `gw-pr-sync`: Sync with main branch

#### Development

- `gw-commit`: Smart commit with message generation
- `gw-push`: Add, commit, push, create PR
- `gw-yolo`: Full feature implementation (requires issue first)
- `gw-editor`: Open in Cursor/VSCode

### Gemini Integration

Use prompt-engineer subagent to improve prompts before executing:

- `/user:gemini-query [question]`: Ask questions
- `/user:gemini-analyze [code]`: Analyze code
- `/user:gemini-brainstorm [topic]`: Brainstorm ideas
- `/user:gemini-summarize [text]`: Summarize content

## Testing & Verification

### Pre-commit Checks

Always run language-specific checks before committing:

**TypeScript**: `tsc && npm run lint && npm test`
**Rust**: `cargo check && cargo clippy && cargo test`
**Python**: `mypy . && ruff check && pytest`

If checks fail, fix issues before committing.

## Advanced Features

### Test-Driven Development

When requested by user:

1. Write tests first
2. Confirm tests fail
3. Implement code
4. Verify tests pass

### Visual Development

- Analyze screenshots provided by user
- Iterate based on visual feedback
- Work toward clear targets

### Context Management

- Use git worktrees for parallel work
- Use Gemini Integration for large codebases
- Keep focus on current objective
