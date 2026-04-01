# Hands-on Lab: Build a Competitor Agent (MCP)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Competitor Agent**. This agent uses the **MCP (Model Context Protocol)** pattern: an agent leverages an external tool server to interact with the real world. The Competitor Agent uses **Playwright MCP** to research competitor websites and compile a competitive analysis report.

### What you will learn

- How to set up and configure an **MCP server** (Playwright) for browser automation.
- How to write a **requirements document** for an MCP-powered agent.
- How to create an **`.agent.md`** file that uses MCP tools.
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

## Step 3 — Configure the Playwright MCP Server

### 3.1 — Create the MCP configuration

1. Create `.vscode/mcp.json` with the following content:

```json
{
  "servers": {
    "playwright": {
      "command": "npx",
      "args": [
        "@anthropic-ai/mcp-playwright@latest"
      ]
    }
  }
}
```

2. **Restart VS Code** to activate the MCP server.
3. Verify the Playwright MCP server appears in the MCP panel.

### 3.2 — Understanding MCP tools

The Playwright MCP gives your agent these capabilities:

| Tool               | What it does                                |
| ------------------ | ------------------------------------------- |
| `browser_navigate` | Opens a URL in a headless browser           |
| `browser_snapshot` | Captures the page content and accessibility |
| `browser_click`    | Clicks an element on the page               |
| `browser_type`     | Types text into form fields                 |

## Step 4 — Use Copilot to Write the Requirements Document

### 4.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat** in **Plan** mode.
2. Type:

   ```
   I want to build a GitHub Copilot custom agent called "Competitor Agent"
   that uses the MCP pattern with a Playwright MCP server.

   Its purpose:
   - The user provides: their product/company name, a brief description,
     and 3-5 competitor URLs
   - The agent uses Playwright MCP to visit each competitor website and
     extract:
     1. Company tagline/value proposition (from homepage)
     2. Key product features (from features/product page)
     3. Pricing tiers (from pricing page)
     4. Target audience signals (from copy and messaging)
     5. Technology stack hints (from job listings or tech blog)
   - The agent compiles findings into a competitive analysis:
     - Feature comparison matrix
     - Pricing comparison table
     - Positioning map (where each competitor sits on 2 axes)
     - SWOT analysis for the user's product vs each competitor
     - Strategic recommendations (gaps to exploit, threats to address)
   - Save to ./output/competitor-analysis.md

   I want a requirements markdown file for this agent.
   Include:
   - Description, target user
   - MCP tools required
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `competitor-requirements.md`.

### 4.2 — Review and refine

```
Add a "monitoring mode" requirement where the agent saves a snapshot of
competitor data. On subsequent runs, it can diff against the previous
snapshot and highlight what changed (new features, price changes, new
messaging).
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`competitor.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:competitor-requirements.md generate a "Competitor Agent"
   Model: Claude Sonnet 4.5.
   Use a 4-phase workflow: Brief → Research → Analyse → Recommend.
   The agent must use MCP Playwright tools for all web interactions.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **MCP usage**       | Uses `browser_navigate`, `browser_snapshot` correctly.     |
| **Workflow**        | Four phases: Brief, Research, Analyse, Recommend.          |
| **Output sections** | Feature matrix, pricing table, SWOT, recommendations.      |

> [!IMPORTANT]
> `tools` must include MCP Playwright tools.

## Step 6 — Test the Competitor Agent

### 6.1 — Invoke the agent

1. Select **Competitor** from the agent dropdown.
2. Provide your competitive landscape:

   ```
   My product: CodeBuddy — a VS Code extension that auto-generates unit
   tests from code analysis. TypeScript, Python, Go support. £9/month.

   Competitors to research:
   1. https://www.codium.ai — CodiumAI/Qodo
   2. https://www.tabnine.com — Tabnine
   3. https://github.com/features/copilot — GitHub Copilot

   Focus on: testing features, pricing, developer experience, supported
   languages.
   ```

3. Watch the agent visit each site and compile a competitive analysis!

### 6.2 — Try another industry

```
My product: FreshBox — a UK meal kit delivery service for busy
professionals. £7.99/meal, 3-5 meals/week.

Competitors:
1. https://www.gousto.co.uk
2. https://www.hellofresh.co.uk
3. https://www.mindfulchef.com

Focus on: pricing, meal variety, dietary options, delivery flexibility.
```

## Step 7 — Refine and Iterate

### 7.1 — Add social media analysis

```
Update the Competitor Agent to also visit competitor social media pages
(Twitter/X, LinkedIn) and extract: follower count, posting frequency,
engagement levels, and common topics they talk about.
```

### 7.2 — Add battle card generation

```
Update the agent to generate "battle cards" — one-page comparisons of
your product vs each competitor, formatted for sales teams to use in
competitive deals. Include "Why us" and "Common objections" sections.
```

---

## Summary

| Artifact                            | Location           | Purpose                                        |
| ----------------------------------- | ------------------ | ---------------------------------------------- |
| `competitor-requirements.md`        | Repository root    | Describes what the Competitor Agent does.      |
| `competitor.agent.md`              | `.github/agents/`  | The agent definition with MCP tools.           |
| `.vscode/mcp.json`                 | `.vscode/`         | MCP server configuration for Playwright.       |
| `output/competitor-analysis.md`    | Created at runtime | Full competitive analysis report.              |

### Key takeaways

1. **MCP tools** extend agents beyond text generation into real-world interaction.
2. **Playwright MCP** enables agents to navigate and extract data from any website.
3. **Competitive intelligence agents** demonstrate high-value business automation.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
