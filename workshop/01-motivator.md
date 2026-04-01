# Hands-on Lab: Build a Motivator Agent

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Motivator Agent**. When a user describes what they're struggling with or working on, this agent generates a personalised motivational message, complete with an action plan and an inspirational quote.

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
   I want to build a GitHub Copilot custom agent called "Motivator Agent".
   Its purpose:
   - The user describes what they're working on or struggling with
     (e.g. "I can't get this bug fixed", "I'm overwhelmed with deadlines",
     "I'm learning a new language and it's hard").
   - The agent responds with:
     1. Acknowledgement — validates the user's feelings or situation
     2. Reframe — puts the challenge in a positive, growth-oriented context
     3. Micro-action plan — 3 small, concrete steps to move forward
     4. Inspirational quote — relevant to the situation (attributed)
     5. Power phrase — a short, punchy motivational line to remember
   - Tone: warm, encouraging, authentic — NOT cheesy or condescending
   - Save the output to ./output/motivation.md

   I want a requirements markdown file for this agent.
   Include:
   - A short description of the agent
   - The target user
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `motivator-requirements.md`.

### 4.2 — Review and refine

```
Add a persona option. The user can optionally choose a motivational style:
- Coach (direct, action-focused)
- Mentor (wise, reflective)
- Cheerleader (energetic, enthusiastic)
- Stoic (calm, philosophical)
Default to Coach if not specified.
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`motivator.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:motivator-requirements.md generate a "Motivator Agent"
   Model: Claude Sonnet 4.5.
   Use a 3-phase workflow: Listen → Inspire → Empower.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **Workflow**        | Three phases: Listen, Inspire, Empower.                    |
| **Tone**            | Warm and genuine — not over-the-top.                       |
| **Output**          | All 5 sections present (Acknowledge, Reframe, Plan, Quote, Phrase). |

> [!IMPORTANT]
> `tools` must be set to `["read", "edit"]`.

## Step 6 — Test the Motivator Agent

### 6.1 — Invoke the agent

1. Select **Motivator** from the agent dropdown.
2. Tell it what's going on:

   ```
   I've been trying to learn Rust for three weeks and I still can't
   wrap my head around the borrow checker. I feel like I'm not smart
   enough for this language.
   ```

3. Watch the agent respond with encouragement!

### 6.2 — Try more scenarios

```
I have a presentation to give tomorrow to 200 people and I'm terrified
of public speaking.
```

```
I've been job hunting for 2 months with no callbacks. Starting to lose
hope. Style: Stoic.
```

```
I just shipped a feature that broke production. I feel terrible.
Style: Mentor.
```

## Step 7 — Refine and Iterate

### 7.1 — Add progress tracking

```
Update the Motivator Agent so it saves the user's challenge and date to
a state file. If the user comes back later, it can reference their previous
challenge and celebrate progress.
```

### 7.2 — Add a daily affirmation mode

```
Add a mode where the user can just say "daily affirmation" and the agent
generates a short morning motivation without needing a specific problem.
```

---

## Summary

| Artifact                     | Location           | Purpose                                          |
| ---------------------------- | ------------------ | ------------------------------------------------ |
| `motivator-requirements.md`  | Repository root    | Describes what the Motivator Agent should do.    |
| `motivator.agent.md`         | `.github/agents/`  | The agent definition file that GitHub Copilot loads. |
| `output/state/`              | Created at runtime | State tracking files.                            |

### Key takeaways

1. **Custom agents are just Markdown files** — no servers, no APIs, no deployments.
2. **Empathy-driven agents** show AI doesn't have to be purely technical.
3. **Start with requirements** — gives Copilot better context to scaffold.
4. **Iterate quickly** — agents are Markdown, edits take seconds.

---
