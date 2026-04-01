# Hands-on Lab: Build a Standup Agent

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Standup Agent**. When a user invokes this agent, it takes rough bullet points of what they worked on and produces a polished daily standup update formatted for Slack, Teams, or email.

### What you will learn

- How to set up a repository that supports custom GitHub Copilot agents.
- How to write a **requirements document** that describes what an agent should do.
- How to create an **`.agent.md`** file that defines the agent's personality, rules, and workflow.
- How to test the finished agent inside VS Code.

---

## Step 1 — Clone the Starter Repository

The starter repo already contains a `.github/instructions/custom-agent.instructions.md` file. This instruction file teaches Copilot _how_ to scaffold high-quality agent definitions for you.

1. Open a **terminal** (Command Prompt, PowerShell, or your preferred shell).
2. Navigate to the folder where you keep your projects — for example:

```powershell
cd c:\dev
```

3. Clone the lab repository:

```powershell
git clone https://github.com/345Nathan-Harrison/lab-customagents-init.git
cd lab-customagents-init
```

4. Remove the .Git folder — in PowerShell:

```powershell
Remove-Item -Recurse -Force .git
```

5. Confirm the clone succeeded:

```powershell
dir .github\instructions
```

> [!NOTE]
> The `lab-customagents-init` repo folder can be renamed if you intend to use this as the basis for creating your own custom agents.

## Step 2 — Open the Repository in VS Code

1. While still in your terminal inside the repo folder, run:

```powershell
code .
```

2. In the **Explorer** panel (left sidebar), confirm the folder structure includes:

```diagram
   ./
   ├── .github/
   │   ├── agents/          ← (empty — you will add your agent here)
   │   └── instructions/
   │       └── custom-agent.instructions.md
   ├── README.md
   └── ...
```

3. Open **Copilot Chat** and confirm it is active.

## Step 3 — Read the Instruction File (Optional but Recommended)

1. In VS Code, open `.github/instructions/custom-agent.instructions.md`.
2. Scan through the document. Key things to notice:
   - **Agent Archetypes** — Agent, Orchestrator, Subagent. For this lab you will build a simple **Agent**.
   - **Frontmatter** — Every `.agent.md` file starts with YAML frontmatter.
   - **Body Sections** — Role line, Context, DO / DON'T, Workflow, Output Files, Validation Checklist.
   - **State Files** — Agents write progress to `./output/state/`.

> [!NOTE]
> You do _not_ need to memorise any of this. Copilot will automatically follow these rules when you create a file ending in `.agent.md`.

## Step 4 — Use Copilot to Write the Requirements Document

### 4.1 — Ask Copilot to draft the requirements

1. Open the **Copilot Chat** panel.
2. Make sure you're in **Plan** mode.

3. Type the following prompt into the chat:

   ```
   I want to build a GitHub Copilot custom agent called "Standup Agent".
   Its purpose:
   - The user provides rough bullet points about what they worked on
     yesterday, what they're working on today, and any blockers.
   - The agent produces a polished standup update in three sections:
     1. ✅ Yesterday — what was completed
     2. 🎯 Today — what's planned
     3. 🚧 Blockers — any issues (or "None" if clear)
   - The output should be concise, professional, and ready to paste
     into Slack, Teams, or email.
   - Save the output to file ./output/standup.md

   I want a requirements markdown file for this agent.
   Include:
   - A short description of the agent
   - The target user
   - Functional requirements (what it does)
   - Non-functional requirements (tone, length, format)
   - Example input and output
   ```

4. Review the output — it should contain:

   | Section                     | What to look for                                                            |
   | --------------------------- | --------------------------------------------------------------------------- |
   | Description                 | A one-liner explaining the Standup Agent's purpose.                         |
   | Target User                 | Developers, team leads — anyone who does daily standups.                    |
   | Functional Requirements     | Accepts rough notes, structures into Yesterday/Today/Blockers.              |
   | Non-functional Requirements | Concise, professional, emoji-formatted, ready to paste.                     |
   | Example                     | Input: rough bullets → Output: formatted 3-section standup.                 |

5. Save to `standup-requirements.md`.

### 4.2 — Review and refine

Feel free to ask Copilot follow-up questions such as:

```
Add a constraint that each section should be 1-3 bullet points max, and the entire standup should fit in a Slack message without scrolling.
```

Save the file when you are satisfied.

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file in the correct location

1. In the Explorer panel, right-click on `.github/agents/` → **New File**.
2. Name the file: **`standup.agent.md`**

> [!IMPORTANT]
> The file name **must** end in `.agent.md`.

Create a new Chat session to start with a clean slate.

### 5.2 — Ask Copilot to scaffold the agent

1. Make sure `standup.agent.md` is open and active in the editor.
2. Open **Copilot Chat**.
3. Type:

   ```
   Using #file:standup-requirements.md generate a "Standup Agent"
   Model: Claude Sonnet 4.5.
   Use a 3-phase workflow: Parse → Structure → Present.
   ```

4. Select **[Keep]** and **Save**.

### 5.3 — Review the generated agent

| Element                  | What to check                                                       |
| ------------------------ | ------------------------------------------------------------------- |
| **Frontmatter**          | `name`, `description`, `model`, `tools` fields are all present.     |
| **Role line**            | One-liner describing the Standup Agent.                             |
| **DO / DON'T**           | At least 3 DO items and 3 DON'T items.                              |
| **Workflow**             | Three phases: Parse, Structure, Present.                            |
| **Validation Checklist** | Checklist for verifying the standup output.                         |

> [!IMPORTANT]
> `tools` must be set to `["read", "edit"]`.

## Step 6 — Test the Standup Agent

Create a new Chat session.

### 6.1 — Invoke the Standup Agent

1. Select **Standup** from the agent dropdown.
2. Type some rough notes:

   ```
   yesterday: fixed the auth bug in PR #234, reviewed janes PR for the
   new dashboard, had sprint planning meeting
   today: going to work on the API rate limiting feature, need to pair
   with tom on the database migration
   blockers: waiting on design team for the new login page mockups,
   CI pipeline is flaky again
   ```

3. Press Enter and watch the agent format your standup!

### 6.2 — Verify the output

1. Check `output/standup.md` — it should have three clean sections with emoji headers.
2. The output should be concise enough to paste directly into Slack.

### 6.3 — Try more scenarios

```
worked on documentation all day, nothing blocked, today doing code reviews
```

```
yesterday was mostly meetings - sprint retro, 1:1 with manager, arch review
today finishing the caching layer
blocked on: need AWS credentials from devops team
```

## Step 7 — Refine and Iterate

### 7.1 — Add time context

```
Update the Standup Agent to include a brief time estimate next to each "Today" item (e.g. "~2hrs").
```

### 7.2 — Add mood/energy indicator

```
Update the Standup Agent to end with a one-word energy level: 🟢 Good / 🟡 OK / 🔴 Low — based on the tone of the user's input notes.
```

### 7.3 — Change the model

| Model               | Expected result                         |
| ------------------- | --------------------------------------- |
| `Claude Opus 4.6`   | Most polished, natural-sounding updates |
| `Claude Sonnet 4.5` | Good balance of speed and quality       |
| `Claude Haiku 4.5`  | Fastest, more concise output            |

---

## Summary

| Artifact                  | Location           | Purpose                                             |
| ------------------------- | ------------------ | --------------------------------------------------- |
| `standup-requirements.md` | Repository root    | Describes what the Standup Agent should do.          |
| `standup.agent.md`        | `.github/agents/`  | The agent definition file that GitHub Copilot loads. |
| `output/state/`           | Created at runtime | State tracking files.                               |

### Key takeaways

1. **Custom agents are just Markdown files** — no servers, no APIs, no deployments.
2. **Instruction files supercharge Copilot** — the conventions are handled automatically.
3. **Start with requirements** — it gives Copilot better context to scaffold the agent.
4. **Iterate quickly** — agents are Markdown, so edits take seconds.

---
