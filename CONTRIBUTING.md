# Contributing Guidelines

Thank you for your interest in contributing! This document outlines the different ways you can help, from simple prompt tuning to core architecture development.

## Core Philosophy

Our goal is to create a robust, efficient, and reliable skill system for LLM agents.

* **v1 Architecture (Current):** The templates (like `templates/generic/CLAUDE_SKILLS_INTEGRATION.md`) instruct the agent to perform an "on-demand" (lazy-loaded) directory scan *during* the session. This is flexible but can be slow and token-intensive.
* **v2 Architecture (Future Plan):** This advanced architecture provides the agent with the *tools* and *knowledge* to build and manage its own persistent skill registry, moving the "discovery" logic out of the core interaction loop.

---

## Project Roadmap: The "v2" Architecture (Agent-Managed Registry)

This is the primary direction for the project's evolution. Instead of the agent re-scanning directories every session (v1), we will provide the agent with a "bootstrapping" prompt that teaches it how to build and consult a persistent registry.

**The "v2" Vision:**

1.  **Tool Provisioning:** This repository will provide a script (e.g., `scripts/build_skill_registry.py`) that is capable of scanning all skill directories and outputting a structured file (e.g., `~/.agent/registry.json`).
2.  **Prompt as an "SOP" (Standard Operating Procedure):** The user will append the `v2` prompt template (e.g., `templates/v2/SKILLS_INTEGRATION.md`) to their agent's configuration.
3.  **Agent Self-Management:** This new prompt will **instruct the agent on a new mechanism**:
    * It will tell the agent: "Your authoritative source of skills is a local file located at `~/.agent/skill-registry.json`."
    * It will provide the *logic for managing this registry*: "If this file is missing, or if the user asks you to 'refresh your skills' or 'scan for new skills', you **must** execute the provided `python scripts/build_skill_registry.py` tool. This tool will (re)build the `skill-registry.json` file for you."
    * It will define the *loading mechanism*: "After ensuring the registry is up-to-date, you **must** then use `ReadFile(path="~/.agent/skill-registry.json")` to load the list of available skills into your working memory for this session."
4.  **Efficient Execution:** In this model, the agent only performs the expensive "scan" (by running the script) *once*, or when explicitly told to. All subsequent sessions will be near-instantaneous, as it will just `ReadFile` on the existing `skill-registry.json`.

**How You Can Help:**

We are looking for contributors to help build this v2 system. This includes:

* **Authoring the `build_skill_registry.py` Script:** Writing a robust, cross-platform Python script that can scan all known skill directories and reliably generate the `skill-registry.json`. This script *must* be runnable by the agent's `Bash` or `python` tool.
* **Designing the `skill-registry.json` Schema:** Defining a clear and concise JSON structure for the skill registry.
* **Authoring the `v2` Prompt Template:** This is the most critical part. Crafting the `templates/v2/SKILLS_INTEGRATION.md` prompt that clearly and unambiguously teaches the agent this entire self-management workflow (check file -> run script if needed -> read file).

---

## Types of Contributions

### 1. Prompt Enhancements (v1)

If you believe you can improve the *current* v1 prompt templates (like `templates/generic/CLAUDE_SKILLS_INTEGRATION.md`), we welcome your PRs.

* **Goal:** Improve reliability, reduce token count, or enhance intent recognition.
* **How:** Submit a PR with your changes to the relevant v1 template file.
* **Requirement:** In your PR, you **must** provide concrete examples (before/after) of user inputs where your new prompt performs better than the old one.

### 2. Agent-Specific Adaptations (Templates)

This is the most common contribution, as described in the `README.md`.

* **Goal:** Create a variant of the prompt snippet optimized for a *specific* agent CLI (e.g., `gemini-cli`, `kimi-cli`).
* **How:**
    1.  Create a new directory: `templates/your-cli-name/`
    2.  Add your prompt template file (e.g., `SKILLS_INTEGRATION.md`) inside it.
    3.  This prompt can leverage special features of that CLI (e.g., "Use the `[TOOL_CALL]` function" instead of generic instructions).
* **Requirement:** Your PR must include notes on *why* this specialization is necessary.

### 3. Contributing to the "v2" Roadmap

This is the most impactful way to contribute.

* **Goal:** Help build the "Persistent Registry" architecture.
* **How:** Please open an Issue first to discuss which part of the v2 plan you'd like to work on. This helps coordinate efforts.

## Submitting Your Pull Request

1.  Fork the repository and create a new feature branch.
2.  Make your changes to the template files.
3.  **Test your changes thoroughly!** Append your modified template to a *real* agent's prompt file and test it against various inputs to ensure no regressions.
4.  Submit a Pull Request with a clear description of:
    * **What** you changed (e.g., "Improved regex in generic v1 template").
    * **Why** you changed it.
    * **How** you tested it.

Thank you for helping us build a smarter agent!