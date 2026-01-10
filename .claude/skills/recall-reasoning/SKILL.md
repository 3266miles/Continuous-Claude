---
description: Search past reasoning for relevant decisions and approaches
---

# Recall Past Work

Search through previous sessions to find relevant decisions, approaches that worked, and approaches that failed. Queries three sources:

1. **Debugging Retros** (Nia) - Structured retrospectives from `/retro` skill
2. **Artifact Index** - Handoffs, plans, ledgers with post-mortems (what worked/failed)
3. **Reasoning Files** - Build attempts, test failures, commit context

## When to Use

- Starting work similar to past sessions
- "What did we do last time with X?"
- Looking for patterns that worked before
- Investigating why something was done a certain way
- Debugging an issue encountered previously

## Usage

### Primary: Debugging Retros (Nia)

Use Nia MCP to search indexed retro files:

```
mcp__nia__search
  query: "<your search query>"
  local_folders: ["<project-name>-retros"]
```

This searches `thoughts/retros/` for structured debugging retrospectives with root causes, solutions, and learnings.

### Secondary: Artifact Index (rich context)

```bash
uv run python scripts/artifact_query.py "<query>" [--outcome SUCCEEDED|FAILED] [--limit N]
```

This searches handoffs with post-mortems (what worked, what failed, key decisions).

### Tertiary: Reasoning Files (build attempts)

```bash
bash .claude/scripts/search-reasoning.sh "<query>"
```

This searches `.git/claude/commits/*/reasoning.md` for build failures and fixes.

## Examples

**Nia retro search:**
```
mcp__nia__search
  query: "logfire tracing span propagation"
  local_folders: ["sumvin-api-retros"]
```

**Artifact Index:**
```bash
# Search for authentication-related work
uv run python scripts/artifact_query.py "authentication OAuth JWT"

# Find only successful approaches
uv run python scripts/artifact_query.py "implement agent" --outcome SUCCEEDED

# Find what failed (to avoid repeating mistakes)
uv run python scripts/artifact_query.py "hook implementation" --outcome FAILED
```

**Reasoning files:**
```bash
# Search build/test reasoning
bash .claude/scripts/search-reasoning.sh "TypeError"
```

## What Gets Searched

**Debugging Retros** (`thoughts/retros/`):
- Problem description and symptoms
- Root causes (numbered, detailed)
- Solution with code samples
- External references used
- Key learnings (transferable)

**Artifact Index** (handoffs, plans, ledgers):
- Task summaries and status
- **What worked** - Successful approaches
- **What failed** - Dead ends and why
- **Key decisions** - Choices with rationale
- Goal and constraints from ledgers

**Reasoning Files** (`.git/claude/`):
- Failed build attempts and error output
- Successful builds after failures
- Commit context and branch info

## Interpreting Results

**From Artifact Index:**
- `✓` = SUCCEEDED outcome (pattern to follow)
- `✗` = FAILED outcome (pattern to avoid)
- `?` = UNKNOWN outcome (not yet marked)
- Post-mortem sections show distilled learnings

**From Reasoning:**
- `build_fail` = approach that didn't work
- `build_pass` = what finally succeeded
- Multiple failures before success = non-trivial problem

## Process

1. **Search Nia retros first** - structured debugging retrospectives with root causes
2. **Read matching retros** - check root causes, solutions, key learnings
3. **If needed, search Artifact Index** - for handoffs with post-mortems
4. **If needed, search reasoning** - for specific build errors
5. **Apply learnings** - follow successful patterns, avoid failed ones

## No Results?

**Nia retros empty:**
- Run `/retro` after debugging sessions to capture learnings
- Index existing retros: `mcp__nia__index` with `folder_path: "thoughts/retros"`
- Check if `thoughts/retros/` directory exists

**Artifact Index empty:**
- Run `uv run python scripts/artifact_index.py --all` to index existing handoffs
- Create handoffs with post-mortem sections for future recall

**Reasoning files empty:**
- Use `/commit` after builds to capture reasoning
- Check if `.git/claude/` directory exists
