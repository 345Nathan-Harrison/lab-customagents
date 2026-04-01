# Hands-on Lab: Build a Job Search Agent (MCP)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Job Search Agent**. This agent uses the **MCP (Model Context Protocol)** pattern: an agent leverages an external tool server to interact with the real world. The Job Search Agent uses **Playwright MCP** to scrape job listings from popular job boards and compile a personalised job search report.

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
3. Verify the Playwright MCP server appears in the MCP panel (look for a green status indicator).

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
   I want to build a GitHub Copilot custom agent called "Job Search Agent"
   that uses the MCP pattern with a Playwright MCP server.

   Its purpose:
   - The user provides: job title, location, experience level, and any
     specific requirements (remote, salary range, industry)
   - The agent uses Playwright MCP to navigate to job boards and scrape
     relevant listings
   - Target sites:
     1. LinkedIn Jobs (linkedin.com/jobs) — search by title and location
     2. Indeed (indeed.co.uk) — search by keyword and location
     3. Stack Overflow Jobs / other tech job boards
   - For each listing found, extract:
     - Job title
     - Company name
     - Location (remote/hybrid/onsite)
     - Salary (if displayed)
     - Posted date
     - Brief description (first 2-3 sentences)
     - URL to the full listing
   - Compile findings into a structured report with:
     - Summary stats (total found, salary range, remote %)
     - Top 10 listings ranked by relevance
     - Comparison table
   - Save to ./output/job-search-report.md

   I want a requirements markdown file for this agent.
   Include:
   - Description, target user
   - MCP tools required
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `job-search-requirements.md`.

### 4.2 — Review and refine

```
Add a requirement to deduplicate listings (same job posted on multiple
boards) and flag roles that have been open for more than 30 days as
potentially less desirable.
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`job-search.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:job-search-requirements.md generate a "Job Search Agent"
   Model: Claude Sonnet 4.5.
   Use a 4-phase workflow: Configure → Scrape → Analyse → Report.
   The agent must use MCP Playwright tools for all web interactions.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **MCP usage**       | Uses `browser_navigate`, `browser_snapshot` correctly.     |
| **Workflow**        | Four phases: Configure, Scrape, Analyse, Report.           |
| **Deduplication**   | Logic to detect same job across boards.                    |

> [!IMPORTANT]
> `tools` must include MCP Playwright tools.

## Step 6 — Test the Job Search Agent

### 6.1 — Invoke the agent

1. Select **Job Search** from the agent dropdown.
2. Describe your search criteria:

   ```
   Search for: Senior Software Engineer
   Location: Remote (UK)
   Experience: 5+ years
   Requirements: Must mention Kubernetes or Docker, prefer Go or TypeScript
   Salary: £70,000+
   ```

3. Watch the agent navigate to job boards, scrape listings, and compile a report!

### 6.2 — Try another search

```
Search for: Data Analyst
Location: London (hybrid OK)
Experience: 2-4 years
Requirements: SQL required, Python preferred, any industry
Salary: Market rate (don't filter)
```

## Step 7 — Refine and Iterate

### 7.1 — Add application tracking

```
Update the Job Search Agent to save a state file that tracks which jobs
the user has already seen. On subsequent runs, only show NEW listings
and highlight "New since last search" vs "Still open".
```

### 7.2 — Add company research

```
Update the agent so that for the top 5 listings, it also visits the
company website and extracts: company size, industry, recent news,
and Glassdoor-style sentiment if available.
```

---

## Summary

| Artifact                            | Location           | Purpose                                      |
| ----------------------------------- | ------------------ | -------------------------------------------- |
| `job-search-requirements.md`       | Repository root    | Describes what the Job Search Agent does.    |
| `job-search.agent.md`              | `.github/agents/`  | The agent definition with MCP tools.         |
| `.vscode/mcp.json`                 | `.vscode/`         | MCP server configuration for Playwright.     |
| `output/job-search-report.md`      | Created at runtime | Compiled job search results.                 |

### Key takeaways

1. **MCP tools** extend agents beyond text generation into real-world interaction.
2. **Playwright MCP** enables agents to navigate, read, and interact with websites.
3. **Web scraping agents** demonstrate practical automation with immediate value.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
