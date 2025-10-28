# Claude Skills Integration Templates - Agent Guide

## Project Overview

This project provides drop-in Markdown snippets that enable **any Agent CLI** to discover and use Claude Skills, even if you primarily use different CLIs than Claude Code. The templates teach agents how to scan skill directories and progressively load skills for efficient context management.

**Key Insight**: Skills aren't Claude-specific — you can point any model at a skills folder and say "read pdf/SKILL.md" and it will work.

## Technology Stack & Architecture

This is a **documentation/template project** rather than traditional software:

- **Primary Format**: Markdown templates and documentation
- **No Build System**: Pure documentation with copy-paste deployment
- **Architecture Evolution**:
  - **v1 (Current)**: On-demand directory scanning during agent sessions
  - **v2 (Planned)**: Agent-managed persistent skill registry with build scripts

## Project Structure

```
claude-skills-integration-templates/
├── templates/
│   └── generic/
│       ├── CLAUDE_SKILLS_INTEGRATION.md          # Main integration template
│       └── local-only/
│           └── CLAUDE_SKILLS_INTEGRATION.md      # Security-focused variant
├── sample/
│   └── skill-discovery-response/                  # Example agent responses
│       ├── kimi.md                               # Kimi CLI response format
│       └── codex.md                              # Codex CLI response format
├── scripts/                                      # Future v2 registry scripts (empty)
├── README.md                                     # Comprehensive user documentation
├── CONTRIBUTING.md                               # Development and contribution guide
├── LICENSE                                       # MIT License
└── AGENTS.md                                     # This file
```

## Core Template System

### Template Types

1. **Generic Template** (`templates/generic/CLAUDE_SKILLS_INTEGRATION.md`)
   - Works with any CLI (Codex, Gemini, Kimi, custom agents)
   - Scans both project and user scope directories
   - Comprehensive two-scope skill discovery

2. **Local-Only Template** (`templates/generic/local-only/CLAUDE_SKILLS_INTEGRATION.md`)
   - Security-focused, project-scoped only
   - Scans only `./.agent/skills/` and `./.claude/skills/`
   - Ideal for CI/CD and shared environments

### Skill Discovery Architecture

**Directory Scanning Order**:
```
Project Scope (Local):
  ./.agent/skills/                    # Project-local skills
  ./.claude/skills/                   # Project-local, Claude compatible

User Scope (Global):
  ~/.agent/skills/                    # User-global skills
  ~/.claude/skills/                   # User-global, Claude compatible
  ~/.claude/plugins/marketplaces/     # Claude marketplace skills
```

**Progressive Loading System**:
1. **Level 1**: Metadata scanning (name, description, scope)
2. **Level 2**: Full SKILL.md loading when triggered
3. **Level 3**: Script execution and resource loading on demand

## Development Conventions

### Template Standards
- **Language**: Clear, imperative instructions for AI agents
- **Structure**: YAML frontmatter + detailed skill instructions
- **Transparency**: Agents must announce skill usage before execution
- **Scope Priority**: Project skills override user skills when names conflict

### Skill Format Requirements
```markdown
---
name: skill-name
description: Brief description of what this skill does
---

# Skill Title

Detailed instructions, workflows, and scripts...
```

### Testing Strategy
- **Manual Testing**: Append templates to real agent configurations
- **Sample Responses**: Document expected agent behavior
- **No Automated Tests**: Template effectiveness verified through usage

## Deployment Process

### Method 1: Auto-Discovery
```bash
mkdir -p .agent
curl -o .agent/CLAUDE_SKILLS_INTEGRATION.md \
  https://raw.githubusercontent.com/calt13/claude-skills-integration-templates/main/templates/generic/CLAUDE_SKILLS_INTEGRATION.md
/init  # Your CLI merges this automatically
```

### Method 2: Manual Integration
```bash
curl -o /tmp/skills-integration.md \
  https://raw.githubusercontent.com/calt13/claude-skills-integration-templates/main/templates/generic/CLAUDE_SKILLS_INTEGRATION.md
cat /tmp/skills-integration.md >> AGENTS.md
```

### Method 3: Local-Only Security Setup
```bash
mkdir -p ./.agent/skills
# Optional: Link specific skills
ln -s ~/.claude/plugins/marketplaces/anthropics-skills/document-skills ./.agent/skills/pdf-tools
curl -o .agent/CLAUDE_SKILLS_INTEGRATION.md \
  https://raw.githubusercontent.com/calt13/claude-skills-integration-templates/main/templates/generic/local-only/CLAUDE_SKILLS_INTEGRATION.md
```

## Code Style Guidelines

### Template Writing
- **Imperative Voice**: "You must scan directories..."
- **Clear Logic Flow**: Numbered steps with conditional logic
- **Explicit Paths**: Full directory paths for clarity
- **Error Handling**: Instructions for when skills aren't found

### Documentation Style
- **User-Focused**: Solve real multi-CLI integration problems
- **Example-Driven**: Include concrete usage scenarios
- **Security-Conscious**: Highlight local-only options
- **Transparent**: Explain the "why" behind design decisions

## Testing Instructions

### Template Validation
1. **Copy Template**: Append to your agent's configuration file
2. **Test Discovery**: Ask "What skills do you have?"
3. **Verify Response**: Check for proper skill listing format
4. **Test Execution**: Request tasks that match available skills
5. **Confirm Transparency**: Ensure agent announces skill usage

### Expected Agent Behavior
- **Discovery**: Lists skills grouped by project/user scope
- **Transparency**: States "I will use the X skill from Y scope"
- **Progressive Loading**: Loads metadata first, full skill when needed
- **Scope Awareness**: Distinguishes between project and user skills

## Security Considerations

### Local-Only Template Benefits
- **Project Isolation**: No scanning of user home directory
- **Controlled Access**: Skills must be explicitly linked
- **CI/CD Compatible**: Works in containerized environments
- **Team Safety**: Prevents accidental personal skill access

### Best Practices
- Use local-only template for shared development environments
- Link only required skills via symbolic links
- Keep sensitive skills in project-local directories
- Regular audit of linked skills for security compliance

## Future Development (v2 Architecture)

### Planned Improvements
- **Persistent Registry**: Agent-managed skill registry files
- **Build Scripts**: Python tools for registry generation
- **Efficient Loading**: Near-instantaneous skill discovery
- **Self-Management**: Agents rebuild registry when needed

### Contributing to v2
- Author `build_skill_registry.py` script
- Design `skill-registry.json` schema
- Create v2 prompt templates
- Implement registry management logic

## Troubleshooting

### Common Issues
1. **Skills Not Found**: Check directory structure and SKILL.md files
2. **Agent Not Using Skills**: Verify template is properly appended
3. **Wrong Skill Selected**: Review skill descriptions and matching logic
4. **Security Concerns**: Use local-only template for isolation

### Verification Commands
```bash
# Check skill directories
ls -la ./.agent/skills/
ls -la ~/.claude/plugins/marketplaces/

# Verify SKILL.md files
find ./.agent/skills/ -name "SKILL.md" | head -5

# Test symbolic links
ls -la ./.agent/skills/ | grep "^l"
```

## Related Resources

- **[Claude Skills Documentation](https://docs.claude.com/en/docs/claude-code/skills)**
- **[Anthropics Skills Repository](https://github.com/anthropics/skills)**
- **[Community Skills](https://github.com/travisvn/awesome-claude-skills)**
- **[Plugin Marketplace](https://claudecodemarketplace.com/)**

---

**This project enables seamless skill sharing across all your development tools — no duplication needed!**