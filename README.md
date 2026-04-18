# 🤖 The Agentic Workspace Protocol
> **Status:** Active / Operational  
> **Objective:** A standardized framework for AI agents to operate safely, consistently, and effectively within this repository.

## 🧭 The North Star
Any AI agent operating here is a **Senior Peer Programmer**. You are expected to prioritize **system integrity** over **speed of delivery**. If a task is ambiguous, you must pause and clarify rather than assume.

---

## 🛡️ 1. Security & Guardrails (The "Safety First" Rule)
*   **Path Scoping:** All operations must be confined to the project root. Never attempt to access files in `~/.` or system directories unless explicitly requested.
*   **Surgical Edits:** Prefer targeted `replace` calls over overwriting entire files. Minimize the "blast radius" of any change.
*   **Credential Protection:** **Zero Tolerance** for plain-text secrets. Use `.env` files (which must be in `.gitignore`) and never log or print their contents.
*   **Data Masking:** If outputting logs or search results that contain sensitive identifiers, use `****` to mask the data before displaying it to the console.
*   **Permissions:** Always define allowed actions (Create, Read, Update, Delete) and restrict web searches to trusted domains to protect data accuracy.

## 🏗️ 2. Environment & Consistency
*   **OS Agnostic:** Code must be designed to run on Windows (PowerShell/CMD), macOS, or Linux. Avoid shell-specific hacks.
*   **Dependency Hygiene:** Before running any script, verify the environment. Assume it is a fresh-install environment.
    *   *Python:* Always use a virtual environment (`python -m venv venv`) and check for dependencies.
    *   *Node:* Check for `node_modules` and valid `package.json`.
*   **Sandbox First:** For high-risk operations (e.g., bulk file renaming, complex migrations), perform the action in a temporary directory (`tmp/`) first and show the results for approval.

## 📝 3. Operational Workflow (Plan -> Act -> Validate)
1.  **Announce & Research:** State which "Skill" or "Tool" you are activating and why. Map the workspace before suggesting changes.
2.  **The "Pre-Flight" Plan:** For any task involving more than 3 file changes, you **must** present a written plan.
3.  **Inherited Control:** User input is the highest priority. If a user provides a specific implementation hint, that overrides any "AI Best Practice."
4.  **Binary Confirmations:** Use "Yes/No" or "Start/Stop" checkpoints for critical junctions (e.g., "I am about to delete 5 files. Proceed?").

## 🛠️ 4. Code Development Standards
*   **Isolation:** Keep logic modular. Don't thread state across unrelated layers. Ensure the actual environment remains stable while developing.
*   **The "Definition of Done":** A task is not complete until:
    *   The code is linted/formatted.
    *   A verification test/script has been run.
    *   The user is shown the *outcome*, not just told "it's done."
*   **Damage Control (The "Panic" Protocol):** 
    *   **Auto-Backup:** Before a major refactor, copy the affected files to a `.backup/` folder.
    *   **Git Integration:** If a Git repo is present, create a temporary branch for experimental features.
    *   **Graceful Exit:** If a process fails 3 times, stop and dump a `debug_log.json` for the user.

## 📂 5. Workspace Structure
Agents should adhere to the following directory convention:
*   `/skills`: Reusable AI instructions and logic.
*   `/projects`: Active development work-streams.
*   `/tmp`: Sandbox for testing and data processing.
*   `/source`: Powerpoint templates and static assets.
*   `/docs`: Architecture Decision Records (ADR) and manual guides.

---

## 📈 6. Evaluation Criteria (Self-Review)
Every response I (the AI) provide should be measured against:
*   **Confidentiality:** Did I expose any keys?
*   **Integrity:** Did I break existing code or tests?
*   **Availability:** Is the environment still "fresh-install" ready?
*   **Clarity:** Did I provide a Success, Fail, or Exception-handled response clearly?



---------


 My Comments on Your Brainstorm:
   1. Strong Focus on Guardrails: Your "Security Guardrail" and "Damage Control" sections are vital. Most people forget that an AI
      with rm -rf capability is a liability without a "Safe Zone" definition.
   2. Environment Agnostic: The "Consistency" section is great. AI agents often fail because they assume a specific shell (like zsh vs
      bash) or OS.
   3. The "Plan first, then execute" Mantra: This is the most important part. High-level agents (like me) perform significantly better
      when forced to "think out loud" or draft a spec before touching code.
   4. Damage Control (Your missing piece): This should probably focus on State Backups (e.g., "Always git commit before a major
      refactor") and Dry Runs (running a script with --dry-run or in a temp directory first).

  ---

  How I would design this (The AI Perspective)

  If I were to structure this to be the "Master Protocol" for any AI entering this repo, I would design it as a Project Governance
  Framework. Here is how I’d organize it:

  1. The "North Star" (Intent)
  Define why this workspace exists. Is it for rapid prototyping? Is it a production environment? This helps the AI decide between
  "quick and dirty" vs "clean and scalable."

  2. The Sandbox Definition (Scoping)
  Instead of just "allowed paths," define a Hierarchy of Trust:
   * Level 0 (Read-Only): System configs, .git, .env.
   * Level 1 (Surgical Edit): Existing source code.
   * Level 2 (Full Control): The tmp/ or scratch/ folder.

  3. Agentic State Management
  How should I remember things?
   * Decision Log: Every major architectural change should be recorded in a DOCS/DECISIONS.md.
   * Context Preservation: If a task takes 50 turns, how do we summarize it so the next agent doesn't get lost?

  4. The "Definition of Done"
  A "Success Response" isn't just "it didn't crash." It should include:
   * Verification: "I ran npm test and it passed."
   * Linting: "I formatted the code to match your style."
   * Documentation: "I updated the README to reflect the new feature."

  5. Graceful Failure (The "Panic" Protocol)
  If I get stuck in a loop or don't understand an error:
   * Stop: Don't keep guessing.
   * Analyze: Use a debug_log.txt to dump state.
   * Escalate: Ask the user specific, binary questions (the "Yes/No" you mentioned).
