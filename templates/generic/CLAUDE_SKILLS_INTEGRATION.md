## Agent Skill System (Powered by Claude Skills)

You have access to a powerful, extendable skill system to enhance your capabilities. These skills are defined in external files and provide specialized, pre-tested instructions, workflows, and scripts.

Your primary directive is to use this skill system whenever possible.

### Core Principle: The Skill-First Mandate

Before attempting *any* user task, you **must** follow this logic:

1.  **Analyze Intent**: Identify the user's goal (e.g., "process a file," "write code," "draft an email").
2.  **Check Registry**: Review your internal skill registry (in working memory) for a skill whose name or description matches the user's intent.
3.  **Execute Priority Rule**:
      * **IF** a matching skill is found in your registry -> You **must** load and follow that skill's instructions (see "Skill Execution" below).
      * **IF** no matching skill is found, **and your skill registry is not yet built** -> You **must** perform the one-time "Skill Discovery" process (see below) to build the registry, then re-check for a match.
      * **ONLY IF** no matching skill exists *after* discovery ->Proceed using your general capabilities.

**Why this is critical**: Skills contain specialized knowledge and optimized workflows. Using them ensures higher-quality results, consistency, and adherence to proven best practices.

### Skill Discovery (Comprehensive Two-Scope System)

Skill discovery is the process of building your internal "skill registry." This operation is **performed exactly once per session**.

This process is **triggered automatically** the *first time* one of these conditions is met:

1.  A user's request implies a task (e.g., "summarize this PDF," "create a new server"), and your skill registry is currently empty.
2.  The user explicitly asks, "What skills do you have?"

**Discovery Process - Comprehensive Two-Scope Scanning:**

The discovery process **simultaneously** scans both project scope and user scope directories to build a complete skill registry. This ensures you discover skills from **both sides** from the start:

1.  **Scan All Skill Locations** (performed in parallel for efficiency):

      **Project Scope (Local to this project):**
      * `./.agent/skills/` (Project-local skills)
      * `./.claude/skills/` (Project-local, Claude Code compatible)
      
      **User Scope (Global to user across all projects):**
      * `~/.agent/skills/` (User-global skills)
      * `~/.claude/skills/` (User-global, Claude Code compatible)
      * `~/.claude/plugins/marketplaces/` (User-global marketplace skills)
      
      **Important**: You must check **both** project and user directories every time skill discovery is triggered. This example output shows the result of such comprehensive discovery:

2.  **Parse Metadata**: For each skill directory found, read **only** the YAML frontmatter from its `SKILL.md` file to extract skill name, description, and scope information.

3.  **Build Comprehensive Registry**: Store all discovered skills organized by scope (project vs user) in your working memory for the rest of the session. The example output above demonstrates what you might find when you search both scopes thoroughly.

**Scope Priority**: When the same skill name exists in both project and user scopes, **project scope takes precedence** for execution, but both are catalogued for transparency.

### Responding to "What skills do you have?"

If the user explicitly asks about your available skills, you must:

1.  Ensure your skill registry is built. (Perform the "Skill Discovery" process if you have not already done so in this session).

2.  If no skills are found, inform the user.

3.  If skills are found, you **must** list them for the user, grouped by scope, using a format similar to this example:

    ```markdown
    Here are the skills I have available:

    **Project Skills** (from ./.agent/ or ./.claude/)
    * `skill-name-1`: Brief description of what this project skill does
    * `skill-name-2`: Brief description of what this project skill does
    * `skill-name-3`: Brief description of what this project skill does

    **User Skills** (from ~/.agent/, ~/.claude/, or marketplace)
    * `skill-name-a`: Brief description of what this user skill does
    * `skill-name-b`: Brief description of what this user skill does
    * `skill-name-c`: Brief description of what this user skill does
    ```

    **Note**: The actual skills discovered will vary based on what's installed in both project and user directories. The key is to demonstrate that you search **both scopes** and organize results accordingly.

### Skill Execution (Progressive Loading)

To manage your context window efficiently, you **must** load skills progressively.

  * **Level 1 (Always in Registry)**: Skill `name`, `description`, and `scope`. This metadata is built during the one-time discovery.
  * **Level 2 (Loaded on Trigger)**: When a skill is matched, load its full `SKILL.md` body from the appropriate scope location.
      * `ReadFile(path="./.agent/skills/{skill-name}/SKILL.md")` for project skills
      * `ReadFile(path="~/.claude/plugins/marketplaces/anthropic-agent-skills/{skill-name}/SKILL.md")` for user skills
  * **Level 3 (Executed on Demand)**: Follow the instructions in the `SKILL.md` body. **Only** execute scripts or load reference files *when and if* the skill's instructions tell you to.
      * `Bash(command="python ./.agent/skills/{skill-name}/scripts/script.py")` for project skills
      * `Bash(command="python ~/.claude/plugins/marketplaces/anthropic-agent-skills/{skill-name}/scripts/script.py")` for user skills
      * `ReadFile(path="./.agent/skills/{skill-name}/references/ref.md")` for project skills

### Best Practices: Skill Usage Transparency

To ensure the user trusts the skill system and understands your actions, you **must** be transparent about skill usage.

1.  **State Your Intent**: When you successfully match a user's task to a skill from your registry, you **must** inform the user which skill you are about to use *before* you execute its instructions.
2.  **Confirm Execution**: This confirms to the user that you are correctly following the "Skill-First Mandate" and have selected the appropriate tool.
3.  **Scope Transparency**: When using skills, mention whether you're using a project-specific or user-global skill to provide context.

### Example Interactions

**User Request:**
"Please extract the text from this quarterly report PDF."

**Correct Agent Response:**
"Understood. I will use the `pdf` skill from user scope to extract the text for you."
*(...Agent then proceeds to load and execute the skill...)*

**User Request:**
"Help me implement this feature using TDD."

**Correct Agent Response:**
"I'll use the `test-driven-development` skill from project scope to guide our implementation process."
*(...Agent then proceeds to load and execute the skill...)*

**Incorrect Agent Response:**
*(...Agent performs task without mentioning the skill or its scope...)*