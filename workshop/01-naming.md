# Hands-on Lab: Build a Naming Agent

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Naming Agent**. When a user describes a project, product, or company, this agent brainstorms creative names, checks them against common naming conventions, and presents ranked options with reasoning.

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
   I want to build a GitHub Copilot custom agent called "Naming Agent".
   Its purpose:
   - The user describes what they're naming (a project, product, company,
     feature, API, or open-source library).
   - The user provides keywords, themes, or vibes they want the name to
     evoke.
   - The agent generates 10 name suggestions across 3 categories:
     1. Descriptive names (clearly say what it does)
     2. Abstract/creative names (evocative, memorable)
     3. Acronym/portmanteau names (clever wordplay)
   - For each name, the agent provides:
     - The name itself
     - A one-line rationale
     - A "vibe check" score (1-5) for: Memorable, Professional, Unique
   - The agent recommends its top 3 picks with explanations.
   - Save the output to ./output/names.md

   I want a requirements markdown file for this agent.
   Include:
   - A short description of the agent
   - The target user
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `naming-requirements.md`.

### 4.2 — Review and refine

```
Add a constraint that the agent should also check each name for:
- Easy to spell and pronounce
- Domain-name friendly (no special characters, reasonable length)
- Not an obvious trademark conflict with major tech companies
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`naming.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:naming-requirements.md generate a "Naming Agent"
   Model: Claude Sonnet 4.5.
   Use a 3-phase workflow: Discover → Generate → Evaluate.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **Workflow**        | Three phases: Discover, Generate, Evaluate.                |
| **Categories**      | Descriptive, Abstract, Acronym sections.                   |
| **Scoring**         | Vibe check table with Memorable/Professional/Unique.       |

> [!IMPORTANT]
> `tools` must be set to `["read", "edit"]`.

## Step 6 — Test the Naming Agent

### 6.1 — Invoke the agent

1. Select **Naming** from the agent dropdown.
2. Describe what you're naming:

   ```
   I'm building a developer tool that automatically generates API
   documentation from code comments. It supports TypeScript, Python, and
   Go. I want the name to feel modern, developer-friendly, and slightly
   playful. Keywords: docs, auto, code, clarity.
   ```

3. Watch the agent brainstorm names!

### 6.2 — Try more scenarios

```
I need a name for a startup that delivers healthy meal kits for busy
professionals. Vibes: fresh, convenient, premium but not pretentious.
```

```
Name an open-source CLI tool that lint-checks Markdown files for
accessibility issues (alt text, heading levels, colour contrast descriptions).
```

```
I need a name for a team hackathon project — it's a browser extension
that summarises long email threads. Keep it fun and nerdy.
```

## Step 7 — Refine and Iterate

### 7.1 — Add domain availability hint

```
Update the Naming Agent so it appends ".com", ".io", and ".dev" to each
name and flags which ones look like they'd be available (no actual lookup,
just heuristic based on name uniqueness and length).
```

### 7.2 — Add logo concept suggestions

```
Update the agent to suggest a simple logo concept (icon + colour palette)
for its top 3 name recommendations.
```

---

## Summary

| Artifact                  | Location           | Purpose                                          |
| ------------------------- | ------------------ | ------------------------------------------------ |
| `naming-requirements.md`  | Repository root    | Describes what the Naming Agent should do.       |
| `naming.agent.md`         | `.github/agents/`  | The agent definition file that GitHub Copilot loads. |
| `output/state/`           | Created at runtime | State tracking files.                            |

### Key takeaways

1. **Custom agents are just Markdown files** — no servers, no APIs, no deployments.
2. **Creative brainstorming agents** showcase AI's generative strength.
3. **Start with requirements** — gives Copilot better context to scaffold.
4. **Iterate quickly** — agents are Markdown, edits take seconds.

---
