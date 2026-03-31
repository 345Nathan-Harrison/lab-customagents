# Hands-on Lab 3: Build a Proposal Generator Agent with Skills

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Proposal Agent**. When a user invokes this agent, it takes a project brief and generates a structured business proposal, then produces a set of deliverables — either as a **PowerPoint** presentation or an **HTML document** — depending on the user's choice.

This lab introduces two new concepts:

- **Skills** — reusable capabilities that agents can leverage (in this case, skills for creating PowerPoint files and HTML documents).
- **Interactive prompts** — using the VS Code `vscode/askQuestions` tool to ask the user which output format they prefer.

> [!NOTE]
> Custom agents declare the tools they need in the `tools:` array in their YAML frontmatter. This is the same mechanism used for both **built-in VS Code tools** (like `read`, `edit`, `vscode/askQuestions`, `runInTerminal`) and **MCP tools** (like `mcp_serverName_toolName`). From the agent's perspective, all tools are invoked the same way — the only difference is that built-in tools are always available, while MCP tools require an external MCP server to be configured and running.

### What you will learn

- How to install and configure **skills** for use with custom agents.
- How to use the VS Code **`vscode/askQuestions`** tool to prompt the user for input during agent execution.
- How to create an agent that generates structured business content with professional formatting.
- How to produce multiple output formats (PowerPoint and HTML) from the same agent.

### Prerequisites

| Requirement                   | Details                                                                                                                                                     |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Completed Lab 1 and Lab 2** | You should be comfortable creating custom agents.                                                                                                           |
| **GitHub account**            | Must have Copilot enabled.                                                                                                                                  |
| **GitHub Copilot**            | Active subscription.                                                                                                                                        |
| **VS Code**                   | Always use the latest.                                                                                                                                      |
| **Node.js**                   | Required for installing skills via `npx` and for creating PowerPoint files (via `pptxgenjs`).                                                               |
| **Python** _(optional)_       | Only needed if you want advanced PowerPoint QA features (thumbnail generation, text extraction). Not required for HTML output or basic PowerPoint creation.  |

---

## Step 1 — Open the Starter Repository in VS Code

You will use the same repository from Labs 1 and 2. If you no longer have it, re-clone it now (see Lab 1, Step 1).

1. Open a **terminal** and navigate to the `lab-customagents-init` folder — for example:

```powershell
   cd c:\dev\lab-customagents-init
```

2. Open the folder in VS Code:

```powershell
   code .
```

3. In the **Explorer** panel, confirm the folder structure includes:

```diagram
   ./
   ├── .github/
   │   ├── agents/
   │   │   ├── _subagents/
   │   │   │   └── ...
   │   │   └── ...
   │   └── instructions/
   │       └── custom-agent.instructions.md
   ├── README.md
   └── ...
```

4. Open **Copilot Chat** and confirm it is active.

---

## Step 2 — Install the Skills

This lab uses two external **skills** that give the Proposal Agent the ability to create PowerPoint presentations and styled HTML documents. Skills are reusable packages of domain knowledge that agents can leverage to produce high-quality output.

> [!NOTE]
> **Agents vs Skills:** A **custom agent** is an autonomous persona that the user invokes directly — it has its own name, model, tools, and workflow, and it drives a conversation to produce output. A **skill** is a passive package of domain knowledge (typically a `SKILL.md` file) that an agent can _read_ to learn how to do something it wouldn't otherwise know — like how to create a PowerPoint file or build a styled HTML document. Skills don't run on their own; they only take effect when an agent reads and follows their instructions. Think of an agent as the chef and a skill as the recipe.

You can browse a growing catalog of community skills at [https://skills.sh](https://skills.sh) — it's a good place to discover skills for common tasks before writing your own.

In this lab we use **two** skills because the Proposal Agent supports two different output formats — and each format requires specialist knowledge that the agent doesn't have on its own:

| Skill               | What it teaches the agent                                                                                 |
| ------------------- | --------------------------------------------------------------------------------------------------------- |
| **pptx**            | How to create well-structured PowerPoint `.pptx` files — slide layouts, text positioning, and formatting. |
| **frontend-slides** | How to build HTML slide decks — responsive layouts, navigation between slides, and CSS styling.           |

The Proposal Agent generates the same content regardless of format. The skill it reads determines _how_ that content gets turned into a deliverable. Without these skills, the agent would have no idea how to produce a valid `.pptx` file or a professionally styled HTML document.

### 2.1 — Install the skills

1. Open a **terminal** in VS Code (`` Ctrl+` `` or **Terminal → New Terminal**).

2. Run the following commands to install both skills:

```powershell
npx skills add https://github.com/anthropics/skills --skill pptx --agent github-copilot -y

npx skills add https://github.com/zarazhangrui/frontend-slides --skill frontend-slides --agent github-copilot -y
```

3. Move the installed skills into the `.github/skills` folder (our project convention). For PowerShell:

```powershell
   robocopy .\.agents\skills .\.github\skills /E /MOVE /NFL /NDL
   Remove-Item -Recurse -Force .\.agents
```

### 2.2 — Verify the skills are installed

1. In the **Explorer** panel, expand `.github/skills/`. You should see the skills have been added:

```diagram
   ./
   ├── .github/
   │   ├── agents/
   │   │   ├── _subagents/
   │   │   │   └── ...
   │   │   └── ...
   │   ├── instructions/
   │   │   └── custom-agent.instructions.md
   │   └── skills/
   │       ├── pptx/
   │       │   └── ...
   │       └── frontend-slides/
   │           └── ...
   ├── README.md
   └── ...
```

2. Take a minute to browse the skill files. Each skill folder contains:
   - A **`SKILL.md`** file — the main instructions that tell the agent what to do and how to do it.
   - **Supporting documents** — additional markdown guides with detailed techniques.
   - **CSS/code files** — reusable assets the agent can include in its output.
   - **Scripts** — helper scripts the agent can run during execution.

   The agent reads these files at runtime and follows the instructions to produce high-quality output — without these files, it would be guessing at file formats and best practices.

---

## Step 3 — Use Copilot to Write the Requirements Document

Just like in the previous labs, you will start by describing _what_ the agent should do in a requirements document.

### 3.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat**.
2. Set the mode to **Plan** — click the mode selector at the bottom of the chat panel and choose **Plan**.

3. Type the following prompt into the chat:

   ```
   I want to build a GitHub Copilot custom agent called "Proposal Agent".
   Its purpose:
   - The user provides a project brief (what the project is, who the
     client is, goals, rough scope, timeline, and budget constraints).
   - The agent generates a structured business proposal with these sections:
     1. Executive Summary
     2. Problem Statement
     3. Proposed Solution
     4. Scope & Deliverables
     5. Timeline & Milestones
     6. Investment / Pricing
     7. Why Us / Team
     8. Next Steps
   - The proposal can be produced as either PowerPoint (.pptx) or
     HTML slides:
     - For PowerPoint output, the agent uses the pptx skill
       (in .github/skills/pptx/)
     - For HTML output, the agent uses the frontend-slides skill
       (in .github/skills/frontend-slides/)
   - The agent should ask the user which format they prefer using
     the VS Code 'vscode/askQuestions' tool
   - If the user provides an incomplete brief, the agent should ask
     clarifying questions before proceeding

   I want a requirements markdown file for this agent.
   Include:
   - A short description of the agent
   - The target user
   - Functional requirements (what it does)
   - Non-functional requirements (tone, format, constraints)
   - Output format options
   - Example input and output
   ```

4. Copilot will generate a markdown document in the chat. **Review the output** — it should contain sections similar to:

   | Section                     | What to look for                                                                 |
   | --------------------------- | -------------------------------------------------------------------------------- |
   | Description                 | A one-liner explaining the Proposal Agent's purpose.                             |
   | Target User                 | Consultants, sales teams, project managers who need quick proposals.             |
   | Functional Requirements     | 8-section proposal structure, dual output format, clarifying questions.          |
   | Non-functional Requirements | Professional tone, concise language, no fabricated statistics.                   |
   | Output Format Options       | PowerPoint (.pptx) via pptx skill or HTML slides via frontend-slides skill.      |
   | Example                     | Input: project brief → 8-section proposal → PowerPoint or HTML.                 |

5. In Plan mode — the agent cannot write to the file. You have two options depending on how the agent responds. Either:

- Copy the suggested requirements to the `proposal-requirements.md` file and save it
- If given the option 'Open in Editor' select that and then save to `proposal-requirements.md`

### 3.2 — Review and refine

Read through the requirements. Feel free to edit them by hand or ask Copilot follow-up questions.

Save the file when you are satisfied.

---

## Step 4 — Use Copilot to Create the Agent Definition File

Now you will create the actual `.agent.md` file that defines the Proposal Agent.

### 4.1 — Create the file in the correct location

Custom agents live in `.github/agents/`.

1. In the Explorer panel, right-click on the `.github/agents/` folder → **New File**.
2. Name the file: **`proposal.agent.md`**
3. Press Enter to create the empty file.

> [!IMPORTANT]
> The file name **must** end in `.agent.md`. This is what tells VS Code (and Copilot) that it is a custom agent definition.

Create a new Chat session to start with a clean slate.

### 4.2 — Ask Copilot to scaffold the agent

1. Make sure the file `proposal.agent.md` is **open and active** in the editor (click on its tab).
2. Open **Copilot Chat** and make sure you are in **Agent** mode (not Ask or Plan) — Copilot needs to be able to create files.
3. Type the following prompt:

   ```
   Using #file:proposal-requirements.md and #file:custom-agent.instructions.md
   generate a "Proposal Agent" using Claude Sonnet 4.5.
   ```

4. Copilot will generate a complete `proposal.agent.md` file. It uses the `custom-agent.instructions.md` file to help scaffold the agent.

5. Select **[Keep]** and **Save** the file (`Ctrl+S` / `Cmd+S`).

### 4.3 — Verify the generated agent

Walk through the file and spot-check these key elements:

**Proposal Agent** (`.github/agents/proposal.agent.md`) — Agent:

| Element                 | What to check                                                                          |
| ----------------------- | -------------------------------------------------------------------------------------- |
| **Frontmatter**         | `name: "Proposal Agent"`, `tools` includes `read`, `edit`, and `vscode/askQuestions`   |
| **Skills Reference**    | References both `.github/skills/pptx/` and `.github/skills/frontend-slides/`           |
| **Input Validation**    | Describes asking clarifying questions if the brief is incomplete                        |
| **Proposal Sections**   | All 8 sections defined (Executive Summary through Next Steps)                          |
| **Format Prompt**       | Uses `vscode/askQuestions` to ask user: PowerPoint or HTML                              |
| **Output Files**        | Deliverables saved to `./output/` (e.g. `./output/proposal.pptx` or `./output/proposal.html`) |

> [!TIP]
> If anything is missing or looks off, just tell Copilot what to fix in the chat.

---

## Step 5 — Verify the Complete File Structure

Before testing, confirm all files are in place.

1. In the Explorer panel, verify:

```diagram
   .github/
   ├── agents/
   │   ├── _subagents/
   │   │   └── ...
   │   ├── proposal.agent.md        ← NEW — your Proposal Agent
   │   └── ...
   ├── instructions/
   │   └── custom-agent.instructions.md
   └── skills/
       ├── pptx/                    ← NEW — PowerPoint skill
       └── frontend-slides/         ← NEW — HTML slides skill
```

2. Make sure the proposal agent file is saved.

---

## Step 6 — Test the Proposal Agent

Time to run the Proposal Agent!

Create a new Chat session to start with a clean slate.

### 6.1 — Open Copilot Chat

1. Open **Copilot Chat**.

### 6.2 — Invoke the Proposal Agent

1. Select **Proposal Agent** from the agent dropdown (where it normally says Agent | Ask | Plan).

2. Type a project brief:

   ```
   Project: Cloud Migration for Northwind Traders
   Client: Northwind Traders — mid-size e-commerce company, ~200 employees
   Goals: Migrate on-premise .NET monolith to Azure microservices
   Scope: Assessment, architecture design, migration of 3 core services,
          CI/CD pipeline setup, team training
   Timeline: 6 months
   Budget: Approx £180,000
   Our team: 2 architects, 4 developers, 1 DevOps engineer
   ```

3. Press Enter and watch the agent work.

> [!TIP]
> Keep the Explorer panel open so you can watch the `output/` folder populate in real time.

### 6.3 — Respond to the format prompt

The Proposal Agent should ask you which output format you prefer — **PowerPoint** or **HTML slides**. Select / enter your preferred option.

You may get prompted to approve commands — if happy to proceed then allow.

### 6.4 — Observe the agent execution

As the Proposal Agent runs, you should see it:

1. **Validate the brief** — confirms enough detail is provided
2. **Generate all 8 proposal sections** — Executive Summary through Next Steps
3. **Ask for output format** — prompts you to choose PowerPoint or HTML
4. **Create the deliverable** — produces the proposal in your chosen format
5. **Present the result** — shows you a summary and points you to the output file

### 6.5 — Check the output files

1. In the Explorer panel, expand the `output/` folder. You should see:

```diagram
   output/
   ├── state/
   │   └── 00-proposal-agent.md         ← Agent state file
   └── proposal.pptx (or proposal.html) ← The deliverable — your proposal!
```

2. Open the state file — confirm it shows all phases checked off.

### 6.6 — View the proposal

**If you chose PowerPoint:**

1. Open `output/proposal.pptx` in PowerPoint or a compatible viewer.
2. You should see slides covering all 8 proposal sections with professional formatting.

**If you chose HTML slides:**

1. Right-click on `output/proposal.html` in the Explorer panel.
2. Select **Open with Live Server** or open the file in your browser.
3. You should see a navigable slide deck covering the full proposal.

### 6.7 — Test with an incomplete brief

Try giving the agent an incomplete brief to verify it asks clarifying questions:

```
   I need a proposal for a mobile app project
```

The agent should ask for more details — client name, goals, timeline, budget, etc.

### 6.8 — Try more project briefs

Run the agent a few more times with different scenarios:

```
   Project: AI Chatbot for Adatum Healthcare
   Client: Adatum Healthcare — NHS-contracted clinic chain
   Goals: Build a patient triage chatbot using Azure OpenAI
   Scope: Requirements gathering, bot development, NHS data compliance,
          integration with existing booking system, pilot rollout
   Timeline: 4 months
   Budget: £95,000
```

```
   Project: Internal Dashboard Redesign
   Client: Internal — our own engineering team
   Goals: Replace legacy Grafana dashboards with a modern React dashboard
   Scope: Design system, 5 dashboard views, real-time data via WebSockets
   Timeline: 8 weeks
   Budget: Internal project — no external budget
```

> [!TIP]
> Good practice for each new run is to delete previous output files — but you will find they get overwritten if this isn't done.

---

## Step 7 — Refine and Iterate

### 7.1 — Improve the proposal quality

Open `proposal.agent.md` and ask Copilot:

```
Update the Proposal Agent so it generates two alternative approaches
in the "Proposed Solution" section and recommends one with a brief
justification.
```

### 7.2 — Change the model

Experiment with different models for the Proposal Agent:

| Model               | Expected result                                     |
| ------------------- | --------------------------------------------------- |
| `Claude Opus 4.6`   | Most polished prose, best-structured proposals      |
| `Claude Sonnet 4.5` | Good balance of quality and speed (recommended)     |
| `Claude Haiku 4.5`  | Faster generation but simpler proposals             |

---

## Summary

Congratulations! 🎉 You have completed Lab 3. Here is what you built:

| Artifact                               | Location                          | Purpose                                                           |
| -------------------------------------- | --------------------------------- | ----------------------------------------------------------------- |
| `proposal-requirements.md`            | Repository root                   | Describes the Proposal Agent's requirements and behaviour.        |
| `proposal.agent.md`                   | `.github/agents/`                 | The Proposal Agent — generates business proposals from briefs.    |
| `pptx` skill                          | `.github/skills/pptx/`            | Skill for creating PowerPoint presentations.                      |
| `frontend-slides` skill               | `.github/skills/frontend-slides/` | Skill for creating HTML slide decks.                              |
| `output/state/`                        | Created at runtime                | State file written by the agent for progress tracking.            |
| `output/proposal.pptx` or `.html`      | Created at runtime                | The deliverable — your proposal in the chosen format.             |

### Key takeaways

1. **Skills extend agent capabilities.** By installing the `pptx` and `frontend-slides` skills, the Proposal Agent gained the ability to produce professional output without you having to teach it the file formats.
2. **Interactive prompts improve the user experience.** Using the `vscode/askQuestions` tool, the agent can gather preferences at runtime rather than requiring everything upfront in the prompt.
3. **Input validation makes agents robust.** By checking for incomplete briefs and asking clarifying questions, the Proposal Agent avoids generating vague or incomplete proposals.
4. **A single agent can produce multiple output formats.** The same proposal content drives both PowerPoint and HTML output — the skill handles the format-specific details.
5. **The instruction file continues to guide quality.** Just like in Labs 1 and 2, `custom-agent.instructions.md` ensured the agent followed all the conventions for frontmatter, state management, and workflow structure.

---

### What's next?

Ideas to extend the lab on your own:

- **Add a competitor analysis section** — Use web search to research the client's industry and add competitive context.
- **Add pricing tiers** — Let the user specify Bronze/Silver/Gold pricing options and generate a comparison table.
- **Add a cover page** — Include a branded cover slide with the client's name, date, and your company logo.
- **Create a template library** — Save successful proposals as templates that the agent can reference for similar future projects.

---
