# Hands-on Lab: Build a Newsletter Agent (Skills)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Newsletter Agent**. This agent uses the **skills pattern**: a single agent is enhanced with reusable skill files that give it specialised capabilities. The Newsletter Agent takes a list of topics or bullet points and produces a formatted newsletter using an **editorial style skill** and a **newsletter layout skill**.

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
   I want to build a GitHub Copilot custom agent called "Newsletter Agent"
   that uses the skills pattern.

   The agent:
   - Receives a list of topics, bullet points, links, or raw notes for
     this week's/month's newsletter
   - Produces a polished, ready-to-send newsletter in Markdown format

   It uses 2 skill files:
   1. **Editorial Style Skill** (.github/instructions/editorial-style.instructions.md)
      - Defines voice and tone: conversational yet authoritative
      - Rules for headlines (punchy, benefit-driven, 6-10 words)
      - Rules for article summaries (2-3 sentences, hook + value)
      - Transition phrases between sections
      - How to write calls-to-action that don't feel sales-y
      - Grammar and style preferences (Oxford comma, active voice)
   2. **Newsletter Layout Skill** (.github/instructions/newsletter-layout.instructions.md)
      - Standard sections: Header/banner, intro/greeting, featured article,
        secondary articles (2-3), quick links/roundup, tip of the week,
        closing/sign-off
      - Emoji usage rules (1 per section heading, relevant, not excessive)
      - Length guidelines (total: 500-800 words, intro: 2-3 sentences)
      - Visual separator between sections (---)
      - Footer with unsubscribe placeholder and social links placeholder

   Output saved to ./output/newsletter.md

   I want a requirements markdown file for this agent.
   ```

3. Save to `newsletter-requirements.md`.

### 4.2 — Review and refine

```
Add a "series" feature where the newsletter auto-numbers itself
(Issue #1, Issue #2, etc.) by checking for previous newsletters in the
output folder. The agent should also include a "Previously..." section
that references the last issue's featured article.
```

## Step 5 — Use Copilot to Create the Agent and Skill Files

### 5.1 — Create the Editorial Style skill

1. Create `.github/instructions/editorial-style.instructions.md`
2. Ask Copilot:

   ```
   Using #file:newsletter-requirements.md generate the "Editorial Style
   Skill" instruction file. Include voice, tone, headline rules, and
   writing guidelines.
   ```

### 5.2 — Create the Newsletter Layout skill

1. Create `.github/instructions/newsletter-layout.instructions.md`
2. Ask Copilot:

   ```
   Using #file:newsletter-requirements.md generate the "Newsletter Layout
   Skill" instruction file. Include section order, length guidelines,
   and formatting rules.
   ```

### 5.3 — Create the agent

1. Create `.github/agents/newsletter.agent.md`
2. Ask Copilot:

   ```
   Using #file:newsletter-requirements.md generate a "Newsletter Agent"
   that references the editorial-style and newsletter-layout instruction
   files. Model: Claude Sonnet 4.5.
   Workflow: Curate → Write → Format.
   ```

### 5.4 — Review the agent and skills

| Element          | What to check                                              |
| ---------------- | ---------------------------------------------------------- |
| **Agent**        | References both skill files.                               |
| **Editorial**    | Voice, headline rules, CTA guidelines defined.             |
| **Layout**       | All standard sections present, length limits set.          |
| **Tools**        | Must be `["read", "edit"]`.                                |

## Step 6 — Test the Newsletter Agent

### 6.1 — Invoke the agent

1. Select **Newsletter** from the agent dropdown.
2. Provide this week's content:

   ```
   Create a developer newsletter with these items:

   Featured: GitHub Copilot now supports custom agents — you can build
   your own AI-powered tools using just Markdown files. It's a game-changer
   for developer productivity.

   Secondary items:
   - TypeScript 5.7 released with new "using" declarations for resource
     management
   - VS Code got a major performance update — 40% faster startup on Windows
   - New study shows teams using AI coding tools ship 25% more features

   Quick links:
   - Node.js 22 LTS release notes
   - Rust 2024 survey results
   - "Clean Code is Dead" blog post (controversial take on code quality)

   Tip of the week: Use `git stash --include-untracked` to stash ALL
   files, not just tracked ones.
   ```

3. Watch the agent produce a polished newsletter!

### 6.2 — Try a different type

```
Create a team internal newsletter:

Featured: We shipped the v3 API this week! 🎉 3 months of work, zero
downtime during migration. Huge thanks to the platform team.

Updates:
- Sprint retrospective findings: we need better code review turnaround
- New hire: Welcome Priya Sharma to the frontend team starting Monday
- Office: The 3rd floor kitchen renovation is complete

Reminder: Submit your conference talk proposals by end of this month.

Fun: Friday quiz winner was Alex with 18/20. This week's theme: 90s movies.
```

## Step 7 — Refine and Iterate

### 7.1 — Add audience segmentation

```
Create a new skill: .github/instructions/audience-segment.instructions.md
It teaches the agent to adjust content for different audiences:
- Developer newsletter (technical, code-focused)
- Manager newsletter (metrics, people, strategy)
- All-hands newsletter (accessible, company-wide, inclusive)
```

### 7.2 — Add HTML output

```
Update the Newsletter Agent so it can also output an HTML version with
basic styling (inline CSS) suitable for email clients. The user can
request "markdown" or "html" format.
```

---

## Summary

| Artifact                                        | Location                  | Purpose                                   |
| ----------------------------------------------- | ------------------------- | ----------------------------------------- |
| `newsletter-requirements.md`                    | Repository root           | Describes all requirements.               |
| `newsletter.agent.md`                           | `.github/agents/`         | The main Newsletter agent.                |
| `editorial-style.instructions.md`               | `.github/instructions/`   | Skill — voice, tone, writing rules.       |
| `newsletter-layout.instructions.md`             | `.github/instructions/`   | Skill — section order and formatting.     |
| `output/newsletter.md`                          | Created at runtime        | Final polished newsletter.                |

### Key takeaways

1. **Skills are reusable** — the editorial style skill could power any writing agent.
2. **Instruction files** encode editorial expertise without bloating the agent.
3. **Content agents** save hours of newsletter production time.
4. **Iterate quickly** — all files are Markdown, edits take seconds.

---
