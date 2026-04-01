# Hands-on Lab: Build a Product Launch Agent (Orchestrator)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Product Launch Agent**. This agent uses the **orchestrator + subagent** pattern: a master agent delegates to a **Market Analyst** subagent (to assess positioning) and a **Launch Planner** subagent (to create the go-to-market plan). The result is a comprehensive product launch strategy document.

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
   I want to build a multi-agent system called "Product Launch Agent"
   using the orchestrator pattern.

   The system has 3 agents:
   1. **Launch Orchestrator** (main agent the user talks to)
      - Receives: product name, description, target audience, price point,
        launch date, competitive landscape notes
      - Delegates to subagents and assembles the final launch plan
   2. **Market Analyst** (subagent)
      - Takes the raw input and produces: target persona profiles,
        competitive positioning matrix, SWOT analysis, key differentiators,
        pricing strategy assessment
      - Saves analysis to ./output/state/market-analysis.md
   3. **Launch Planner** (subagent)
      - Takes the market analysis and produces: a phased launch timeline
        (pre-launch, launch day, post-launch), channel strategy (social,
        email, PR, partnerships), key messaging and taglines, success
        metrics and KPIs
      - Saves the plan to ./output/launch-plan.md

   I want a requirements markdown file for this system.
   Include:
   - Agent descriptions and responsibilities
   - Data flow between agents
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `product-launch-requirements.md`.

### 4.2 — Review and refine

```
Add a requirement that the Launch Planner must include a risk assessment
section with:
- Top 3 risks to the launch
- Mitigation strategy for each risk
- A go/no-go checklist with 10 items
```

## Step 5 — Use Copilot to Create the Agent Definition Files

### 5.1 — Create the orchestrator

1. Create `.github/agents/product-launch.agent.md`
2. Ask Copilot:

   ```
   Using #file:product-launch-requirements.md generate the main "Launch
   Orchestrator" agent. It should delegate to @market-analyst and
   @launch-planner subagents. Model: Claude Sonnet 4.5. Workflow:
   Brief → Analyse → Plan → Deliver.
   ```

### 5.2 — Create the Market Analyst subagent

1. Create `.github/agents/market-analyst.agent.md`
2. Ask Copilot:

   ```
   Using #file:product-launch-requirements.md generate the "Market Analyst"
   subagent. It produces competitive analysis and persona profiles.
   Model: Claude Sonnet 4.5.
   ```

### 5.3 — Create the Launch Planner subagent

1. Create `.github/agents/launch-planner.agent.md`
2. Ask Copilot:

   ```
   Using #file:product-launch-requirements.md generate the "Launch Planner"
   subagent. It creates a phased go-to-market plan. Model: Claude Sonnet 4.5.
   ```

### 5.4 — Review all three agents

| Element             | Orchestrator | Market Analyst     | Launch Planner     |
| ------------------- | ------------ | ------------------ | ------------------ |
| `tools`             | `["read", "edit"]` | `["read", "edit"]` | `["read", "edit"]` |
| Delegates to        | Both subagents | N/A              | N/A                |
| Reads state from    | Both outputs | Raw input          | Market analysis    |
| Writes to           | Final output | `market-analysis.md` | `launch-plan.md` |

## Step 6 — Test the Product Launch Agent

### 6.1 — Invoke the orchestrator

1. Select **Product Launch** from the agent dropdown.
2. Provide product details:

   ```
   Product: CodeBuddy
   Description: A VS Code extension that auto-generates unit tests by
   analysing your codebase. Supports TypeScript, Python, and Go.
   Target audience: Individual developers and small dev teams (2-10 people)
   Price: Free tier (5 tests/day), Pro at £9/month, Team at £7/user/month
   Launch date: 6 weeks from today
   Competition: Copilot (built-in but not test-focused), Codium AI
   (similar but expensive), manual writing (the default)
   ```

3. Watch the orchestrator delegate to both subagents and assemble a full launch plan!

### 6.2 — Try another product

```
Product: MealMate
Description: A mobile app that scans your fridge with your camera and
suggests recipes based on what you have. Supports dietary restrictions.
Target audience: Busy professionals aged 25-40 who want to cook more
Price: Free with ads, Premium at £3.99/month (no ads + meal planning)
Launch date: 3 months from today
Competition: Supercook (web only), Yummly (recipe-focused, not fridge scanning)
```

## Step 7 — Refine and Iterate

### 7.1 — Add a Content Creator subagent

```
Add a third subagent called "Content Creator" that takes the launch plan
and generates 5 social media posts (Twitter/X, LinkedIn, Instagram caption)
for the pre-launch, launch day, and post-launch phases.
```

### 7.2 — Add investor pitch mode

```
Add a mode where the user says "investor pitch" and the orchestrator also
generates a one-page executive summary suitable for investors, including
market size, traction goals, and revenue projections.
```

---

## Summary

| Artifact                           | Location           | Purpose                                        |
| ---------------------------------- | ------------------ | ---------------------------------------------- |
| `product-launch-requirements.md`   | Repository root    | System requirements for all 3 agents.          |
| `product-launch.agent.md`          | `.github/agents/`  | Orchestrator — delegates and assembles.        |
| `market-analyst.agent.md`          | `.github/agents/`  | Subagent — analyses market and competition.    |
| `launch-planner.agent.md`          | `.github/agents/`  | Subagent — creates the go-to-market plan.      |
| `output/state/market-analysis.md`  | Created at runtime | Intermediate data between subagents.           |
| `output/launch-plan.md`            | Created at runtime | Final comprehensive launch strategy.           |

### Key takeaways

1. **The orchestrator pattern** breaks complex tasks into specialised roles.
2. **State files** are the glue — subagents communicate through Markdown files.
3. **Business agents** demonstrate AI's versatility beyond code generation.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
