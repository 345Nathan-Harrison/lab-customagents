# Hands-on Lab: Build a Blog Agent (Orchestrator)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Blog Agent**. This agent uses the **orchestrator + subagent** pattern: a master agent delegates to a **Research Analyst** subagent (to outline and structure the content) and a **Content Writer** subagent (to produce the polished blog post). The result is a publish-ready blog article.

### What you will learn

- How to build a multi-agent system with an **orchestrator** and **subagents**.
- How to write a **requirements document** for a composite agent system.
- How to create an **`.agent.md`** file for the orchestrator and each subagent.
- How to test the whole system inside VS Code.

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

Skim `.github/instructions/custom-agent.instructions.md`. Pay special attention to the **Orchestrator pattern** section.

## Step 4 — Use Copilot to Write the Requirements Document

### 4.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat** in **Plan** mode.
2. Type:

   ```
   I want to build a multi-agent system called "Blog Agent" using the
   orchestrator pattern.

   The system has 3 agents:
   1. **Blog Orchestrator** (main agent the user talks to)
      - Receives: topic, target audience, tone (technical/casual/formal),
        approximate word count, key points to cover
      - Delegates to subagents and assembles the final blog post
   2. **Research Analyst** (subagent)
      - Takes the topic and audience, produces: a detailed outline with
        H2/H3 headings, key arguments and supporting points, potential
        code examples or data to include, SEO keyword suggestions,
        suggested title options (5 alternatives)
      - Saves research to ./output/state/blog-research.md
   3. **Content Writer** (subagent)
      - Takes the research/outline and produces: a fully written blog
        post in Markdown, with engaging introduction (hook), well-structured
        body sections, code blocks where relevant, a compelling conclusion
        with call-to-action, meta description for SEO
      - Saves the post to ./output/blog-post.md

   I want a requirements markdown file for this system.
   Include:
   - Agent descriptions and responsibilities
   - Data flow between agents
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `blog-requirements.md`.

### 4.2 — Review and refine

```
Add a requirement that the Content Writer must include:
- A "TL;DR" section at the top (3-4 bullet points)
- Subheadings every 200-300 words
- At least one analogy or metaphor per major section
- A "Further Reading" section with 3 suggested resource topics
```

## Step 5 — Use Copilot to Create the Agent Definition Files

### 5.1 — Create the orchestrator

1. Create `.github/agents/blog.agent.md`
2. Ask Copilot:

   ```
   Using #file:blog-requirements.md generate the main "Blog Orchestrator"
   agent. It should delegate to @research-analyst and @content-writer
   subagents. Model: Claude Sonnet 4.5. Workflow:
   Brief → Research → Write → Polish.
   ```

### 5.2 — Create the Research Analyst subagent

1. Create `.github/agents/research-analyst.agent.md`
2. Ask Copilot:

   ```
   Using #file:blog-requirements.md generate the "Research Analyst"
   subagent. It produces structured outlines and SEO research.
   Model: Claude Sonnet 4.5.
   ```

### 5.3 — Create the Content Writer subagent

1. Create `.github/agents/content-writer.agent.md`
2. Ask Copilot:

   ```
   Using #file:blog-requirements.md generate the "Content Writer"
   subagent. It turns outlines into polished blog posts. Model: Claude Sonnet 4.5.
   ```

### 5.4 — Review all three agents

| Element             | Orchestrator | Research Analyst     | Content Writer       |
| ------------------- | ------------ | -------------------- | -------------------- |
| `tools`             | `["read", "edit"]` | `["read", "edit"]` | `["read", "edit"]` |
| Delegates to        | Both subagents | N/A                | N/A                  |
| Reads state from    | Both outputs | Raw input            | Blog research        |
| Writes to           | Final output | `blog-research.md`   | `blog-post.md`       |

## Step 6 — Test the Blog Agent

### 6.1 — Invoke the orchestrator

1. Select **Blog** from the agent dropdown.
2. Provide blog details:

   ```
   Topic: Why every developer should learn to write custom GitHub Copilot agents
   Audience: Mid-level developers who use Copilot but haven't customised it
   Tone: Technical but approachable, with humour
   Word count: ~1500 words
   Key points: agents are just Markdown, no deployment needed, practical
   examples save time, the agent ecosystem is growing fast
   ```

3. Watch the orchestrator delegate and produce a full blog post!

### 6.2 — Try another topic

```
Topic: The hidden costs of microservices architecture
Audience: Engineering managers considering breaking up a monolith
Tone: Formal, data-driven, balanced
Word count: ~2000 words
Key points: operational complexity, debugging difficulty, team structure
implications, when a monolith is actually better
```

## Step 7 — Refine and Iterate

### 7.1 — Add a social media promoter

```
Add a third subagent called "Social Promoter" that takes the finished
blog post and generates promotional content:
- 3 tweet-length posts (under 280 characters each)
- 1 LinkedIn post (300-500 words)
- 1 newsletter teaser paragraph
```

### 7.2 — Add series support

```
Update the Blog Orchestrator so the user can say "this is part 2 of a
series" and reference a previous blog post file. The Research Analyst
should then ensure continuity and avoid repeating content from part 1.
```

---

## Summary

| Artifact                        | Location           | Purpose                                        |
| ------------------------------- | ------------------ | ---------------------------------------------- |
| `blog-requirements.md`          | Repository root    | System requirements for all 3 agents.          |
| `blog.agent.md`                 | `.github/agents/`  | Orchestrator — delegates and assembles.        |
| `research-analyst.agent.md`     | `.github/agents/`  | Subagent — outlines and researches the topic.  |
| `content-writer.agent.md`       | `.github/agents/`  | Subagent — writes the polished blog post.      |
| `output/state/blog-research.md` | Created at runtime | Intermediate data between subagents.           |
| `output/blog-post.md`           | Created at runtime | Final publish-ready blog article.              |

### Key takeaways

1. **The orchestrator pattern** breaks complex tasks into specialised roles.
2. **State files** are the glue — subagents communicate through Markdown files.
3. **Content creation agents** demonstrate real-world productivity gains.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
