---
globs: [".claude/skills/**/*"]
---

# Skill Development Rules

When working with files in `.claude/skills/`:

## SKILL.md Structure (2.1.0+)

```yaml
---
name: skill-name
description: Brief description
allowed-tools: [Bash, Read]           # Optional: restrict tools
allowed-tools: [Bash(npm *), Read]    # Wildcards supported (2.1.0+)

# NEW in 2.1.0:
skills: [skill-1, skill-2]            # Skills inherited when spawned as subagent
context: fork                         # Run in isolated context
hooks:
  PreToolUse: |
    echo "Before tool: $TOOL_NAME"
  PostToolUse: |
    echo "After tool"
  Stop: |
    echo "Skill complete"
---
```

## DO
- Keep SKILL.md concise (< 200 lines)
- Include clear "When to Use" section
- Provide copy-paste bash commands
- Reference scripts/ for MCP operations
- Add triggers to skill-rules.json
- Use `skills:` to declare dependencies for agent-type skills
- Use `context: fork` for skills with large outputs

## DON'T
- Include implementation details in SKILL.md
- Duplicate content across skills
- Create skills without corresponding trigger in skill-rules.json
- Use allowed-tools that aren't needed
- Over-engineer hooks - use only when validation is needed

## MCP Wrapper Skills
For skills that wrap MCP scripts:
- Use `allowed-tools: [Bash, Read]` to restrict capabilities
- Point to the script in scripts/ directory
- Include parameter documentation

## Agent-Type Skills
For skills that run as subagents:
- Add `skills:` to declare skill inheritance
- Consider `context: fork` to prevent context pollution
- Use `hooks: Stop:` to verify completion requirements
