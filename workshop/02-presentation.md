# Hands-on Lab: Build a Presentation Agent (Orchestrator)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Presentation Agent**. This agent uses the **orchestrator + subagent** pattern: a master agent delegates to a **Storyline Architect** subagent (to structure the narrative) and a **Slide Designer** subagent (to produce slide-by-slide content). The result is a complete presentation deck in Markdown format.

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
   I want to build a multi-agent system called "Presentation Agent"
   using the orchestrator pattern.

   The system has 3 agents:
   1. **Presentation Orchestrator** (main agent the user talks to)
      - Receives: topic, audience, duration (in minutes), key message,
        presentation style (educational/persuasive/informational/pitch)
      - Delegates to subagents and assembles the final deck
   2. **Storyline Architect** (subagent)
      - Takes the topic, audience, and style, produces: a narrative arc 
        (opening hook, problem, solution, evidence, call-to-action),
        recommended number of slides, section breakdown with timing,
        audience engagement points (questions, demos, polls)
      - Saves structure to ./output/state/story-structure.md
   3. **Slide Designer** (subagent)
      - Takes the story structure and produces: slide-by-slide content
        in Markdown (one H2 per slide), speaker notes for each slide,
        suggested visuals/diagrams (described in text), transition cues
      - Follows the "one idea per slide" rule
      - Saves the deck to ./output/presentation.md

   I want a requirements markdown file for this system.
   Include:
   - Agent descriptions and responsibilities
   - Data flow between agents
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `presentation-requirements.md`.

### 4.2 — Review and refine

```
Add presentation best practices:
- Maximum 6 bullet points per slide, max 6 words per bullet (6x6 rule)
- Every 3rd slide should be a "breathing" slide (image, quote, or question)
- Include a "Questions?" slide before the closing slide
- Speaker notes should include timing cues and transition phrases
```

## Step 5 — Use Copilot to Create the Agent Definition Files

### 5.1 — Create the orchestrator

1. Create `.github/agents/presentation.agent.md`
2. Ask Copilot:

   ```
   Using #file:presentation-requirements.md generate the main
   "Presentation Orchestrator" agent. It should delegate to
   @storyline-architect and @slide-designer subagents. Model:
   Claude Sonnet 4.5. Workflow: Brief → Structure → Design → Refine.
   ```

### 5.2 — Create the Storyline Architect subagent

1. Create `.github/agents/storyline-architect.agent.md`
2. Ask Copilot:

   ```
   Using #file:presentation-requirements.md generate the "Storyline
   Architect" subagent. It designs the narrative flow and timing.
   Model: Claude Sonnet 4.5.
   ```

### 5.3 — Create the Slide Designer subagent

1. Create `.github/agents/slide-designer.agent.md`
2. Ask Copilot:

   ```
   Using #file:presentation-requirements.md generate the "Slide Designer"
   subagent. It creates slide-by-slide content with speaker notes.
   Model: Claude Sonnet 4.5.
   ```

### 5.4 — Review all three agents

| Element             | Orchestrator | Storyline Architect  | Slide Designer       |
| ------------------- | ------------ | -------------------- | -------------------- |
| `tools`             | `["read", "edit"]` | `["read", "edit"]` | `["read", "edit"]` |
| Delegates to        | Both subagents | N/A                | N/A                  |
| Reads state from    | Both outputs | Raw input            | Story structure      |
| Writes to           | Final output | `story-structure.md`  | `presentation.md`   |

## Step 6 — Test the Presentation Agent

### 6.1 — Invoke the orchestrator

1. Select **Presentation** from the agent dropdown.
2. Provide presentation details:

   ```
   Topic: Introduction to GitHub Copilot Custom Agents
   Audience: Software engineers at a team meeting (15 people)
   Duration: 20 minutes
   Key message: Custom agents are easy to build and immediately useful
   Style: Educational with a persuasive ending (convince them to try it)
   ```

3. Watch the orchestrator delegate and produce a complete slide deck!

### 6.2 — Try another presentation

```
Topic: Q3 Engineering Team OKR Review
Audience: Engineering leadership (VP + directors)
Duration: 10 minutes
Key message: We hit 4 of 5 OKRs, the missed one needs more resources
Style: Informational with data-driven evidence
```

## Step 7 — Refine and Iterate

### 7.1 — Add a Mermaid diagram generator

```
Update the Slide Designer so that when a slide calls for a diagram,
it generates Mermaid syntax instead of just describing the visual.
Include flowcharts, sequence diagrams, and architecture diagrams.
```

### 7.2 — Add rehearsal mode

```
Add a feature where the user says "rehearsal" and the orchestrator
outputs a teleprompter-style script: speaker notes in reading order
with slide transition cues and timing markers.
```

---

## Summary

| Artifact                             | Location           | Purpose                                         |
| ------------------------------------ | ------------------ | ----------------------------------------------- |
| `presentation-requirements.md`       | Repository root    | System requirements for all 3 agents.           |
| `presentation.agent.md`             | `.github/agents/`  | Orchestrator — delegates and assembles.         |
| `storyline-architect.agent.md`       | `.github/agents/`  | Subagent — designs the narrative structure.     |
| `slide-designer.agent.md`           | `.github/agents/`  | Subagent — creates slide content and notes.     |
| `output/state/story-structure.md`    | Created at runtime | Intermediate data between subagents.            |
| `output/presentation.md`            | Created at runtime | Final complete presentation deck.               |

### Key takeaways

1. **The orchestrator pattern** breaks complex tasks into specialised roles.
2. **State files** are the glue — subagents communicate through Markdown files.
3. **Presentation agents** save hours of slide creation time.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
