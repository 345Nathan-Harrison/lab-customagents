# Hands-on Lab: Build a Meeting Notes Agent

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Meeting Notes Agent**. When a user invokes this agent, it takes rough, unstructured notes from a meeting and produces a polished summary with action items, decisions, and key discussion points.

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

4. Confirm the clone:

```powershell
dir .github\instructions
```

## Step 2 — Open the Repository in VS Code

1. Run:

```powershell
code .
```

2. Confirm the folder structure includes `.github/agents/` and `.github/instructions/custom-agent.instructions.md`.

3. Open **Copilot Chat** and confirm it is active.

## Step 3 — Read the Instruction File (Optional but Recommended)

1. Open `.github/instructions/custom-agent.instructions.md` and skim through.
2. Note the Agent Archetypes, Frontmatter, Body Sections, and State Files conventions.

> [!NOTE]
> Copilot will automatically follow these rules for any `.agent.md` file.

## Step 4 — Use Copilot to Write the Requirements Document

### 4.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat** in **Plan** mode.
2. Type:

   ```
   I want to build a GitHub Copilot custom agent called "Meeting Notes Agent".
   Its purpose:
   - The user pastes in rough meeting notes (bullet points, fragments,
     stream-of-consciousness notes taken during a meeting).
   - The agent produces a polished meeting summary with:
     1. Meeting title (inferred from context)
     2. Date (today's date if not specified)
     3. Attendees (extracted from notes if mentioned)
     4. Key Discussion Points — the main topics discussed
     5. Decisions Made — any commitments or choices agreed upon
     6. Action Items — who needs to do what, with deadlines where mentioned
     7. Open Questions — unresolved items flagged for follow-up
   - Save the output to ./output/meeting-notes.md

   I want a requirements markdown file for this agent.
   Include:
   - A short description of the agent
   - The target user
   - Functional requirements (what it does)
   - Non-functional requirements (tone, length, format)
   - Example input and output
   ```

3. Review and save to `meeting-notes-requirements.md`.

### 4.2 — Review and refine

Suggested refinements:

```
Add a constraint that the agent should never invent attendees or action items not present in the original notes — it should flag gaps instead.
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`meeting-notes.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

1. Open the file and type in Copilot Chat:

   ```
   Using #file:meeting-notes-requirements.md generate a "Meeting Notes Agent"
   Model: Claude Sonnet 4.5.
   Use a 3-phase workflow: Extract → Organise → Present.
   ```

2. Select **[Keep]** and **Save**.

### 5.3 — Review the generated agent

| Element                  | What to check                                                       |
| ------------------------ | ------------------------------------------------------------------- |
| **Frontmatter**          | `name`, `description`, `model`, `tools` fields present.             |
| **Workflow**             | Three phases: Extract, Organise, Present.                           |
| **Action Items**         | Agent extracts who/what/when from notes.                            |
| **State file**           | Writes to `./output/state/00-meeting-notes-agent.md`.               |

> [!IMPORTANT]
> `tools` must be set to `["read", "edit"]`.

## Step 6 — Test the Meeting Notes Agent

### 6.1 — Invoke the agent

1. Select **Meeting Notes** from the agent dropdown.
2. Paste rough notes:

   ```
   Sprint planning with Sarah, Tom, and Priya
   - discussed the auth migration, Sarah says 2 more sprints needed
   - decided to postpone the dashboard redesign until Q3
   - Tom needs to update the API docs by Friday
   - Priya will set up the new staging environment
   - question: do we need a separate database for the analytics service?
   - budget approved for the new monitoring tool
   - Tom raised concern about test coverage dropping below 80%
   ```

3. Watch the agent produce a structured summary!

### 6.2 — Verify the output

1. Check `output/meeting-notes.md` — should have all 7 sections.
2. Action items should clearly state who/what/when.

### 6.3 — Try more scenarios

```
Quick sync with dev team about the release
- release is on track for Thursday
- need to freeze PRs by Wednesday 5pm
- Alex found a bug in checkout flow, fixing now
- discussed whether to include the new search feature — decided yes
```

```
Architecture review meeting
attendees: James, Sophie, Amara, DevOps team
- current system hitting 10k req/s limit
- Sophie proposed event-driven architecture using Kafka
- James concerned about operational complexity
- agreed to do a 2-week spike
- Amara will write the RFC by next Monday
- open question: self-hosted vs managed Kafka?
```

## Step 7 — Refine and Iterate

### 7.1 — Add priority tagging

```
Update the Meeting Notes Agent to tag each action item with priority:
🔴 Urgent / 🟡 Normal / 🟢 Low — based on language cues in the notes.
```

### 7.2 — Add follow-up reminder format

```
Update the agent to include a "Follow-up Email Draft" section at the bottom — a brief email summarising decisions and action items that could be sent to attendees.
```

### 7.3 — Change the model

| Model               | Expected result                                     |
| ------------------- | --------------------------------------------------- |
| `Claude Opus 4.6`   | Best at inferring context from messy notes          |
| `Claude Sonnet 4.5` | Good balance of speed and accuracy                  |
| `Claude Haiku 4.5`  | Fastest, but may miss nuance in complex discussions |

---

## Summary

| Artifact                         | Location           | Purpose                                               |
| -------------------------------- | ------------------ | ----------------------------------------------------- |
| `meeting-notes-requirements.md`  | Repository root    | Describes what the Meeting Notes Agent should do.     |
| `meeting-notes.agent.md`         | `.github/agents/`  | The agent definition file that GitHub Copilot loads.  |
| `output/state/`                  | Created at runtime | State tracking files.                                 |

### Key takeaways

1. **Custom agents are just Markdown files** — no servers, no APIs, no deployments.
2. **Instruction files supercharge Copilot** — conventions are handled automatically.
3. **Start with requirements** — gives Copilot better context.
4. **Iterate quickly** — agents are Markdown, edits take seconds.

---
