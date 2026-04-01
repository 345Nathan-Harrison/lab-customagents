# Hands-on Lab: Build an Email Agent (Skills)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Email Agent**. This agent uses the **skills pattern**: a single agent is enhanced with reusable skill files that give it specialised capabilities. The Email Agent takes a rough brief and produces professional emails using a **tone calibration skill** and an **email structure skill**.

### What you will learn

- How to build an agent that uses **reusable skill files** (`.md` files in a skills folder).
- How to write a **requirements document** for a skills-based agent.
- How to create an **`.agent.md`** file that references skill files.
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

Skim `.github/instructions/custom-agent.instructions.md`. Pay special attention to the **Skills pattern** section.

## Step 4 — Use Copilot to Write the Requirements Document

### 4.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat** in **Plan** mode.
2. Type:

   ```
   I want to build a GitHub Copilot custom agent called "Email Agent"
   that uses the skills pattern.

   The agent:
   - Receives a brief: who the email is to, what it's about, the desired
     outcome, and tone (formal/friendly/apologetic/urgent/grateful)
   - Produces a ready-to-send email in Markdown format

   It uses 2 skill files:
   1. **Tone Calibration Skill** (.github/instructions/tone-calibration.instructions.md)
      - Defines 5 tone profiles: Formal, Friendly, Apologetic, Urgent, Grateful
      - For each tone: vocabulary choices, sentence structure, opening
        and closing phrases, level of directness
      - Rules for when each tone is appropriate
      - How to blend tones (e.g., "urgent but friendly")
   2. **Email Structure Skill** (.github/instructions/email-structure.instructions.md)
      - Subject line best practices (clear, actionable, under 60 chars)
      - Opening line rules (context before request)
      - Body structure (one idea per paragraph, shortest paragraph first)
      - Call-to-action (specific, with deadline if relevant)
      - Closing and sign-off selection based on tone and relationship
      - Email length guidelines (aim for 5 sentences or fewer when possible)

   Output format:
   **Subject:** [subject line]
   **To:** [recipient]

   [email body]

   [sign-off]

   Save to ./output/email.md

   I want a requirements markdown file for this agent.
   ```

3. Save to `email-requirements.md`.

### 4.2 — Review and refine

```
Add a "reply mode" where the user pastes an incoming email and the agent
drafts a response. The agent should:
- Match the formality level of the incoming email
- Address all questions/points raised
- Keep the response shorter than the original where possible
```

## Step 5 — Use Copilot to Create the Agent and Skill Files

### 5.1 — Create the Tone Calibration skill

1. Create `.github/instructions/tone-calibration.instructions.md`
2. Ask Copilot:

   ```
   Using #file:email-requirements.md generate the "Tone Calibration Skill"
   instruction file. Include detailed tone profiles for all 5 tones with
   example phrases.
   ```

### 5.2 — Create the Email Structure skill

1. Create `.github/instructions/email-structure.instructions.md`
2. Ask Copilot:

   ```
   Using #file:email-requirements.md generate the "Email Structure Skill"
   instruction file. Include subject line rules, body structure, and
   sign-off selection logic.
   ```

### 5.3 — Create the agent

1. Create `.github/agents/email.agent.md`
2. Ask Copilot:

   ```
   Using #file:email-requirements.md generate an "Email Agent" that
   references the tone-calibration and email-structure instruction files.
   Model: Claude Sonnet 4.5.
   Workflow: Understand → Draft → Refine.
   ```

### 5.4 — Review the agent and skills

| Element            | What to check                                              |
| ------------------ | ---------------------------------------------------------- |
| **Agent**          | References both skill files.                               |
| **Tone profiles**  | All 5 tones defined with vocabulary and phrases.           |
| **Structure**      | Subject line, opening, body, CTA, closing rules.           |
| **Tools**          | Must be `["read", "edit"]`.                                |

## Step 6 — Test the Email Agent

### 6.1 — Invoke the agent

1. Select **Email** from the agent dropdown.
2. Provide a brief:

   ```
   To: Sarah Chen (engineering manager at a partner company)
   About: We need to reschedule next week's integration testing from
   Wednesday to Friday because our API team needs more time to fix
   a critical bug.
   Desired outcome: She agrees to Friday and confirms her team's availability
   Tone: Friendly but professional
   ```

3. Watch the agent produce a polished email!

### 6.2 — Try more scenarios

```
To: My direct report, James
About: He missed the sprint demo for the second time. I need to address
this without being harsh.
Desired outcome: He understands it's important and commits to attending
Tone: Friendly but firm
```

```
To: Customer support
About: I was charged twice for my subscription this month
Desired outcome: Refund for the duplicate charge
Tone: Polite but clear
```

```
Reply to this email:
"Hi, thanks for submitting your proposal. We've reviewed it internally
and have a few questions: 1) Can you clarify the timeline for Phase 2?
2) Is the £50k budget fixed or flexible? 3) Who would be the day-to-day
contact? Looking forward to hearing from you. Best, Maria"
```

## Step 7 — Refine and Iterate

### 7.1 — Add a follow-up generator

```
Create a new skill: .github/instructions/follow-up.instructions.md
It teaches the agent to generate a follow-up email for when someone
hasn't responded. It should be shorter than the original, reference the
key point, and include a soft deadline. Tone softens on each follow-up
(follow-up 1 vs 2 vs 3).
```

### 7.2 — Add email thread summariser

```
Add a mode where the user pastes a long email thread and the agent
summarises it: key decisions, open questions, action items, and who
owns each one.
```

---

## Summary

| Artifact                                       | Location                  | Purpose                                   |
| ---------------------------------------------- | ------------------------- | ----------------------------------------- |
| `email-requirements.md`                        | Repository root           | Describes all requirements.               |
| `email.agent.md`                               | `.github/agents/`         | The main Email agent.                     |
| `tone-calibration.instructions.md`             | `.github/instructions/`   | Skill — tone profiles and vocabulary.     |
| `email-structure.instructions.md`              | `.github/instructions/`   | Skill — layout and formatting rules.      |
| `output/email.md`                              | Created at runtime        | Final ready-to-send email.                |

### Key takeaways

1. **Skills are reusable** — the tone calibration skill could power any communication agent.
2. **Instruction files** encode writing expertise without bloating the agent.
3. **Email agents** save time on a task people do dozens of times per day.
4. **Iterate quickly** — all files are Markdown, edits take seconds.

---
