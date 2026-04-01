# Hands-on Lab: Build a Commit Message Agent

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Commit Message Agent**. When a user invokes this agent, it reads a `git diff` or a description of changes and produces a well-structured conventional commit message.

### What you will learn

- How to set up a repository that supports custom GitHub Copilot agents.
- How to write a **requirements document** that describes what an agent should do.
- How to create an **`.agent.md`** file that defines the agent's personality, rules, and workflow.
- How to test the finished agent inside VS Code.

---

## Step 1 — Clone the Starter Repository

1. Open a **terminal** and navigate to your projects folder:

```powershell
cd c:\dev
```

2. Clone the lab repository:

```powershell
git clone https://github.com/345Nathan-Harrison/lab-customagents-init.git
cd lab-customagents-init
```

3. Remove the .Git folder:

```powershell
Remove-Item -Recurse -Force .git
```

## Step 2 — Open the Repository in VS Code

1. Run `code .`
2. Confirm `.github/agents/` and `.github/instructions/custom-agent.instructions.md` exist.
3. Open **Copilot Chat** and confirm it is active.

## Step 3 — Read the Instruction File (Optional but Recommended)

Skim `.github/instructions/custom-agent.instructions.md`. Note the Agent Archetypes, Frontmatter, and State Files conventions.

## Step 4 — Use Copilot to Write the Requirements Document

### 4.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat** in **Plan** mode.
2. Type:

   ```
   I want to build a GitHub Copilot custom agent called "Commit Message Agent".
   Its purpose:
   - The user provides either a git diff, a list of changed files, or a
     plain-English description of what changed.
   - The agent produces a conventional commit message following the
     Conventional Commits specification (https://www.conventionalcommits.org/).
   - The output includes:
     1. A commit type (feat, fix, refactor, docs, test, chore, etc.)
     2. An optional scope in parentheses
     3. A concise subject line (≤ 72 characters)
     4. An optional body with more detail (wrapped at 72 characters)
     5. Optional footer for breaking changes or issue references
   - Save the output to ./output/commit-message.md

   I want a requirements markdown file for this agent.
   Include:
   - A short description of the agent
   - The target user
   - Functional requirements (what it does)
   - Non-functional requirements (tone, format, constraints)
   - Example input and output
   ```

3. Save to `commit-message-requirements.md`.

### 4.2 — Review and refine

```
Add a constraint that the agent should suggest up to 3 alternative commit messages ranked by specificity, and recommend the best one.
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`commit-message.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:commit-message-requirements.md generate a "Commit Message Agent"
   Model: Claude Sonnet 4.5.
   Use a 3-phase workflow: Analyse → Compose → Present.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **Workflow**        | Three phases: Analyse, Compose, Present.                   |
| **Commit format**   | Follows Conventional Commits spec.                         |
| **Character limit** | Subject line ≤ 72 characters.                              |

> [!IMPORTANT]
> `tools` must be set to `["read", "edit"]`.

## Step 6 — Test the Commit Message Agent

### 6.1 — Invoke the agent

1. Select **Commit Message** from the agent dropdown.
2. Describe your changes:

   ```
   I added input validation to the user registration form. Now it checks
   for valid email format, password strength (min 8 chars, 1 uppercase,
   1 number), and prevents duplicate usernames. Also added unit tests
   for all three validators.
   ```

3. Watch the agent produce a commit message!

### 6.2 — Try more scenarios

```
Fixed a bug where the shopping cart total was calculated incorrectly
when a discount code was applied to items that were already on sale.
The fix ensures discounts are not double-applied.
```

```
Refactored the database connection pooling logic to use a singleton
pattern. Removed 3 duplicate connection helper files. No behaviour change.
```

```
Updated the README with installation instructions for Windows and macOS.
Added a troubleshooting section for common Node.js version issues.
```

## Step 7 — Refine and Iterate

### 7.1 — Add git integration

```
Update the Commit Message Agent so it can also accept a #file reference to a diff file and parse the actual code changes to infer what changed.
```

### 7.2 — Add issue linking

```
Update the agent to ask if there's a related issue number and include it in the commit footer as "Closes #<number>".
```

---

## Summary

| Artifact                          | Location           | Purpose                                                |
| --------------------------------- | ------------------ | ------------------------------------------------------ |
| `commit-message-requirements.md`  | Repository root    | Describes what the Commit Message Agent should do.     |
| `commit-message.agent.md`         | `.github/agents/`  | The agent definition file that GitHub Copilot loads.   |
| `output/state/`                   | Created at runtime | State tracking files.                                  |

### Key takeaways

1. **Custom agents are just Markdown files** — no servers, no APIs, no deployments.
2. **Practical agents save daily time** — a commit message agent eliminates a common friction point.
3. **Start with requirements** — gives Copilot better context to scaffold.
4. **Iterate quickly** — agents are Markdown, edits take seconds.

---
