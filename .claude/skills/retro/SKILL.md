---
name: retro
description: Capture debugging learnings before PR merge - creates retro doc, inline comments, PR comment, and updates CLAUDE.md
---

# Debugging Retrospective

Capture learnings from a debugging session before the PR merges.

## When to Use

- After fixing a non-trivial bug
- After resolving a tricky issue that required investigation
- When you've learned something that would help future debugging
- Before merging a PR that contains hard-won fixes

## Process

### Step 1: Gather Context

```bash
# Get current PR info
gh pr view --json number,title,body,headRefName,files

# Get modified files
git diff --name-only origin/main...HEAD

# Get recent commits on this branch
git log --oneline origin/main..HEAD
```

Read the modified files to understand what was changed.

### Step 2: Generate Retro Document

Create file: `thoughts/retros/YYYY-MM-DD-<slug>.md`

Use this template:

```markdown
# Retro: <Title>
Date: YYYY-MM-DD
PR: #<number>
Branch: <branch-name>

## Problem

<What broke? What were the symptoms? How was it discovered?>

## Root Causes

1. **<Root Cause 1>**: <explanation>
2. **<Root Cause 2>**: <explanation>
   (List all root causes, not just the primary one)

## Solution

### Code Changes

<Before/after code samples showing the key fixes>

```python
# Before (broken)
<code>

# After (fixed)
<code>
```

### Why This Works

<Explanation of why the fix addresses the root causes>

## External References

- <Link to documentation that helped>
- <Link to GitHub issue or Stack Overflow>
- <Link to any other resources consulted>

## Key Learnings

1. **<Learning 1>**: <transferable insight>
2. **<Learning 2>**: <transferable insight>

## Files Modified

- `path/to/file.py:123` - <what changed>
- `path/to/other.py:45-67` - <what changed>
```

### Step 3: Apply Inline Comments

For each file with a non-obvious "why", add a comment explaining the gotcha.

**Comment format:**
```python
# GOTCHA: <brief explanation of why this is needed>
# See: thoughts/retros/YYYY-MM-DD-<slug>.md
```

**Examples of comments to add:**

```python
# GOTCHA: @logfire.no_auto_trace required - without it, middleware becomes
# root span and breaks trace hierarchy. See: thoughts/retros/2026-01-06-logfire-tracing.md
@logfire.no_auto_trace
class CorrelationMiddleware:
```

```python
# GOTCHA: Must use .all() explicitly - Juno's query wrapper doesn't support
# implicit iteration like raw SQLAlchemy. See: thoughts/retros/2026-01-08-juno-queries.md
results = await ctx.db.query(User).filter_by(active=True).all()
```

**When to add comments:**
- The fix is non-obvious (someone would ask "why?")
- The pattern differs from typical usage in the framework/library
- The issue took significant debugging to find
- Similar code elsewhere doesn't need this pattern

**When NOT to add comments:**
- Standard patterns documented in CLAUDE.md
- Self-explanatory fixes (typos, obvious bugs)
- Already has adequate documentation

### Step 4: Index to Nia

Index the retros folder so it's searchable:

```bash
# Use Nia MCP to index the retros folder
```

Call the Nia MCP tool:
```
mcp__nia__index
  folder_path: "<project_dir>/thoughts/retros"
  folder_name: "<project-name>-retros"
```

If already indexed, the folder will be updated.

### Step 5: Post PR Comment

```bash
gh pr comment <PR_NUMBER> --body "$(cat <<'EOF'
## Debugging Retrospective

### Root Causes
1. <Root cause 1>
2. <Root cause 2>

### Key Learnings
- <Learning 1>
- <Learning 2>

### Full Retro
See: `thoughts/retros/YYYY-MM-DD-<slug>.md`

---
*Generated with /retro*
EOF
)"
```

### Step 6: Update CLAUDE.md (If Warranted)

**Only update CLAUDE.md if:**
- The learning is generalizable (will recur)
- It's a pattern, not a one-off fix
- It would help someone unfamiliar with the codebase

**Section structure in CLAUDE.md:**

```markdown
## Gotchas & Patterns

### Logfire / Tracing
- <terse pattern description>

### Pydantic / Serialization
- <terse pattern description>

### Juno SDK
- <terse pattern description>

### Infrastructure
- <terse pattern description>
```

**Update logic:**

1. **Parse** existing CLAUDE.md to find "Gotchas & Patterns" section
2. **Classify** the new learning - which subsection?
3. **Check for conflicts:**
   - Does new learning contradict existing entry?
   - If yes: Ask user which is correct
4. **Check for staleness:**
   - Does new learning supersede existing entry?
   - If yes: Replace old with new
5. **Integrate:**
   - Add to appropriate subsection
   - Keep entries terse (1-2 lines)
   - Full context lives in retro file

**Example CLAUDE.md entry:**
```markdown
### Logfire / Tracing
- `@logfire.no_auto_trace` on middleware prevents root span hijacking
- `excluded_urls` is regex - bare `/` matches ALL urls
```

## Output Checklist

When complete, confirm:
- [ ] Retro document created in `thoughts/retros/`
- [ ] Inline comments added to code where needed
- [ ] Retros folder indexed in Nia
- [ ] PR comment posted with summary
- [ ] CLAUDE.md updated (if learning is generalizable)

## Example Invocation

```
User: /retro

Claude: Let me gather context from the current PR and create a retrospective.

[Gathers PR info, reads modified files]

I'll create the retro document for the logfire tracing fix...

[Creates thoughts/retros/2026-01-06-logfire-tracing.md]

Adding inline comments to explain the gotchas...

[Adds GOTCHA comments to correlation.py and main.py]

Indexing retros to Nia...

[Calls mcp__nia__index]

Posting summary to PR #145...

[Posts gh pr comment]

This learning is generalizable - updating CLAUDE.md...

[Updates Gotchas & Patterns section]

Done! Retrospective complete:
- Retro: thoughts/retros/2026-01-06-logfire-tracing.md
- 2 inline comments added
- PR comment posted
- CLAUDE.md updated with 2 new patterns
```
