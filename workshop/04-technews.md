# Hands-on Lab 4: Build a Tech News Agent with MCP Integration

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Tech News Agent** that gathers and summarises the latest technology news from multiple sources. This lab introduces a powerful new concept: **Model Context Protocol (MCP) servers** — external tools that dramatically extend what your custom agents can do.

The Tech News Agent will use the **Playwright MCP server** to visit real news websites, extract headlines and summaries, deduplicate stories, and generate a comprehensive daily briefing report.

### What you will learn

- What **MCP** is and why it matters for custom agents.
- How to install and configure an **MCP server** (Playwright) in VS Code.
- How to declare **MCP tools** in agent frontmatter so the agent can use them.
- How to build an agent that orchestrates multiple web scraping tasks.
- How to generate structured **markdown reports** from live web data.

### Prerequisites

| Requirement                  | Details                                                                    |
| ---------------------------- | -------------------------------------------------------------------------- |
| **Completed Lab 1, 2 and 3** | You should be comfortable creating custom agents and understand workflows. |
| **GitHub account**           | Must have Copilot enabled.                                                 |
| **GitHub Copilot**           | Active subscription.                                                       |
| **VS Code**                  | Always use the latest.                                                     |
| **Node.js**                  | Required for installing MCP servers. Version 18+ recommended.              |
| **npx**                      | Comes with Node.js — used to run Playwright MCP.                           |

---

## What is MCP and Why Does It Matter?

**Model Context Protocol (MCP)** is an open standard that allows AI agents to connect to external tools and data sources in a standardised way. Instead of building custom integrations for every tool, MCP provides a universal connector.

### MCP in this lab

```diagram
┌────────────────────────────────┐
│       TECH NEWS AGENT          │
│   (GitHub Copilot Custom)      │
└──────────────┬─────────────────┘
               │
               │ Uses mcp_playwright_* tools
               │
               ▼
┌────────────────────────────────┐
│     PLAYWRIGHT MCP SERVER      │
│  (Runs in VS Code background)  │
│                                │
│  • navigate(url)               │
│  • screenshot()                │
│  • evaluate(JavaScript)        │
└──────────────┬─────────────────┘
               │
      ┌────────┼────────┐
      ▼        ▼        ▼
┌────────┐ ┌────────┐ ┌──────────┐
│  BBC   │ │  Ars   │ │ Hacker   │  ... and more
│  Tech  │ │Technica│ │  News    │
└────────┘ └────────┘ └──────────┘
```

Key benefits:

- **Reusability**: One Playwright MCP server can be used by many agents.
- **Safety**: MCP servers run in a controlled environment, not directly in the agent.
- **Extensibility**: You can add new MCP servers (databases, APIs, tools) without changing your agent code.

> [!NOTE]
> In this lab, you'll see how custom agents and MCP servers work together seamlessly. The agent declares `playwright/*` in its `tools` array, and VS Code handles the rest.

---

## Step 1 — Open the Starter Repository in VS Code

You will use the same repository from Labs 1, 2, and 3. If you no longer have it, re-clone it now (see Lab 1, Step 1).

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
   │   └── instructions/
   │       └── custom-agent.instructions.md
   ├── .vscode/
   │   └── mcp.json
   ├── README.md
   └── ...
```

4. Open **Copilot Chat** and confirm it is active.

---

## Step 2 — Install and Configure the Playwright MCP Server

**Playwright** is an open-source framework from Microsoft, widely used for **functional testing of web applications**. Developers write automated tests that launch a real browser, navigate to pages, fill in forms, click buttons, and assert that the application behaves correctly — all without a human touching the keyboard.

In this lab, we repurpose that same browser-automation power for a completely different goal: **live web scraping by an AI agent**. Instead of a test script driving the browser, the Tech News Agent calls the Playwright MCP server to navigate to news websites, read headlines, and extract article summaries.

### Why this matters

Before your agent can use Playwright, you need to install the Playwright MCP server and configure VS Code to run it.

### 2.1 — Understand MCP server configuration

MCP servers are configured per-project using a `.vscode/mcp.json` file in the workspace root. This keeps MCP configuration scoped to the project and version-controlled alongside your code. Each server entry includes:

- **command**: The executable to run (e.g., `npx`)
- **args**: Arguments to pass (e.g., the MCP server package name)
- **env**: Optional environment variables

### 2.2 — Verify the MCP configuration file

1. In the Explorer panel, expand the `.vscode` folder.
2. Open the **`mcp.json`** file — it is already included in the lab repository.
3. Look for the `playwright` entry in the `"servers"` section:

```json
    "playwright": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    },
```

This tells VS Code that a Playwright MCP server is available for this workspace.

### 2.3 — Start the Playwright MCP server

There are two ways to start the Playwright MCP server:

#### Option A — Start from mcp.json (quickest)

1. Open the `.vscode/mcp.json` file in the editor.
2. You will see a **`▷ Start`** link displayed inline above the `"playwright"` server entry.
3. Click **`Start`** — the server will launch in the background.
4. Once running, the link changes to **`Stop`** — confirming the server is active. You will also see a tool count (e.g., `22 tools`) indicating the available Playwright tools.

#### Option B — Start from the MCP Servers panel

1. Open the **Extensions** sidebar (`Ctrl+Shift+X`).
2. At the bottom of the sidebar, look for the **MCP Servers** section.
3. Right-click on **playwright** → **Start Server**.

> [!TIP]
> The MCP Servers panel is useful for managing multiple servers at once. You can start, stop, and restart servers, view their output logs, and inspect their configuration.

### 2.4 — Verify the MCP server is running

1. Confirm the server is running using either method:
   - In `mcp.json` — you should see **`Stop | 22 tools | More...`** next to the playwright entry.
   - In the MCP Servers panel — the playwright entry should show a running status.

2. You can also click the **Tools** button (the wrench/spanner icon at the bottom of the Copilot Chat panel, next to the model selector) to see all available tools — the Playwright MCP tools should be listed there.

> [!NOTE]
> **Understanding the Tools selector:** The Tools button lets you **enable or disable individual tools** for a conversation. Copilot has a **limit of 128 tools** that can be active at once. Every active tool adds to the context sent to the model, consuming tokens. Use the Tools selector to **uncheck tools you don't need** for the current task — this keeps the context lean and helps the agent perform better. For this lab, make sure the `playwright` tools are enabled and consider disabling any unrelated tools.

> [!IMPORTANT]
> Troubleshooting: If you don't see Playwright tools, check:
>
> - Node.js is installed (`node --version` in terminal)
> - The `.vscode/mcp.json` configuration is correct (no JSON syntax errors)
> - The server is started (click **Start** in `mcp.json` or the MCP Servers panel)

---

## Step 3 — Use Copilot to Write the Requirements Document

Just like in the previous labs, you will start by describing what the agent should do in a requirements document.

### 3.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat**.

2. Set the mode to **Plan** — click the mode selector at the bottom of the chat panel and choose **Plan**.

3. Type the following prompt into the chat:

   ```
   I want a custom agent that gathers and summarises the latest technology
   news from multiple websites.

   The Workflow:

   1. User prompts with a topic or "general" for broad tech news
   2. Agent visits the following news sources via Playwright MCP tool:
      - BBC Technology (https://www.bbc.co.uk/news/technology)
      - Ars Technica (https://arstechnica.com/)
      - Hacker News (https://news.ycombinator.com/)
      - The Verge (https://www.theverge.com/tech)
      - TechCrunch (https://techcrunch.com/)
   3. A markdown briefing report is written to ./output/, with a filename
      reflecting the date and topic

   Report Contents to include:

   - Timestamp of the briefing and topic searched
   - Top 5 stories (deduplicated across sources), each with:
     - Headline
     - One-paragraph summary
     - Source(s) covering the story
     - Link to the original article
   - A "Quick Takes" section with 3 emerging trends spotted across sources
   - A "Worth Watching" section for 2-3 stories that aren't top news yet
     but look interesting

   Technical Requirements

   - Playwright functionality must be accessed via an MCP tool `playwright/*`
   - The agent should handle sites that fail to load gracefully — skip and note
   - Total scraping should complete within a reasonable time

   Create a requirements document for this "Tech News Agent".
   ```

4. In Plan mode — the agent cannot write to the file. You have two options:
   - Copy the suggested requirements to the `technews-requirements.md` file and save it
   - If given the option 'Open in Editor' select that and then save to `technews-requirements.md`

5. Review the output — it should contain sections similar to:

| Section                     | What to look for                                                                          |
| --------------------------- | ----------------------------------------------------------------------------------------- |
| Description                 | A one-liner explaining the Tech News Agent's purpose.                                     |
| Target User                 | Developers, tech leads, anyone wanting a daily tech briefing.                             |
| Functional Requirements     | Visits 5 news sites, extracts headlines, deduplicates, generates briefing.                |
| Non-functional Requirements | Must use Playwright MCP tools, handle failures gracefully, complete in reasonable time.   |
| Output Format               | Markdown report in `./output/{topic}-briefing-{date}.md`                                  |
| Example                     | Input: "AI" → Briefing with top 5 AI stories from across 5 sources.                      |

### 3.2 — Review and refine

Read through the requirements. Feel free to edit them by hand or ask Copilot follow-up questions.

Save the file when you are satisfied.

---

## Step 4 — Use Copilot to Create the Agent Definition File

Now you will create the actual `.agent.md` file that defines the Tech News Agent.

### 4.1 — Create the file in the correct location

Custom agents live in `.github/agents/`.

1. In the Explorer panel, right-click on the `.github/agents/` folder → **New File**.
2. Name the file: **`technews.agent.md`**
3. Press Enter to create the empty file.

> [!IMPORTANT]
> The file name **must** end in `.agent.md`. This is what tells VS Code (and Copilot) that it is a custom agent definition.

Create a new Chat session to start with a clean slate.

### 4.2 — Ask Copilot to scaffold the agent

1. Make sure the file `technews.agent.md` is open and active in the editor (click on its tab).
2. Open **Copilot Chat** and make sure you are in **Agent** mode (not Ask or Plan) — Copilot needs to be able to create files.
3. Type the following prompt:

   ```
   Use #file:technews-requirements.md and #file:custom-agent.instructions.md to
   generate a "Tech News Agent" using Claude Sonnet 4.5.
   ```

4. Copilot will generate a complete `technews.agent.md` file.

5. Select **[Keep]** and **Save** the file (`Ctrl+S` / `Cmd+S`).

### 4.3 — Verify the generated agent

Walk through the file and spot-check these key elements:

**Tech News Agent** (`.github/agents/technews.agent.md`) — Agent:

| Element              | What to check                                                                                    |
| -------------------- | ------------------------------------------------------------------------------------------------ |
| **Frontmatter**      | `name: "Tech News Agent"`, `tools` includes `read`, `edit`, and `playwright_*` tools             |
| **Source List**       | Mentions all 5 sources: BBC Tech, Ars Technica, Hacker News, The Verge, TechCrunch              |
| **Input Validation** | Accepts a topic or "general" for broad news                                                      |
| **Scraping Workflow** | Describes using Playwright to navigate, extract headlines, and handle failures                   |
| **Report Structure** | Top 5 stories, Quick Takes, Worth Watching sections                                              |
| **Deduplication**    | Agent identifies the same story across multiple sources and merges them                          |
| **Output File**      | Report saved to `./output/{topic}-briefing-{date}.md`                                            |
| **State File**       | Writes progress to `./output/state/00-technews-agent.md`                                         |

> [!TIP]
> If anything is missing or looks off, just tell Copilot what to fix in the chat. The most critical element is that the `playwright/*` tools are declared in the frontmatter — without them, the agent won't be able to control browsers.

---

## Step 5 — Verify the Complete File Structure

Before testing, confirm all files are in place.

1. In the Explorer panel, verify:

```diagram
   .github/
   ├── agents/
   │   ├── _subagents/
   │   │   └── ...
   │   └── technews.agent.md        ← NEW — your Tech News Agent
   └── instructions/
       └── custom-agent.instructions.md
```

2. Make sure the tech news agent file is saved.

---

## Step 6 — Test the Tech News Agent

Time to run the Tech News Agent and see it scrape real news websites!

Create a new Chat session to start with a clean slate.

Make sure Playwright MCP is running.

### 6.1 — Open Copilot Chat

1. Open **Copilot Chat**.

### 6.2 — Invoke the Tech News Agent

1. Select **Tech News Agent** from the agent dropdown (where it normally says Agent | Ask | Plan).

2. Type the test prompt:

   ```
   Give me today's top tech news about AI
   ```

3. Press Enter and watch the agent work.

### 6.3 — Observe the agent execution

1. If there are any prompts asking for permission — if you are happy then Accept. After a time, the Agent may ask if it should "Continue to Iterate", again Accept for it to proceed.

> [!TIP]
> Keep the Explorer panel open so you can watch the `output/` folder populate in real time. This search may take a few minutes depending on website response times — the agent is visiting 5 live websites!

2. Eventually the Agent will complete its work.

As the Tech News Agent runs, you should see it:

1. **Parse the topic** — confirms "AI" is a valid news topic
2. **Visit each source** — uses Playwright MCP to navigate to each news site and extract headlines
3. **Handle failed sources** — if a site is down or blocks scraping, the agent notes it and continues
4. **Deduplicate stories** — identifies the same story reported by multiple sources
5. **Generate the briefing** — creates a markdown report with top stories, trends, and emerging stories
6. **Present results** — shows you a summary and points you to the report file

> [!NOTE]
> **What's happening behind the scenes:** The agent is calling `mcp_playwright_navigate` to visit each news site, `mcp_playwright_evaluate` to run JavaScript that extracts headlines and article data from the page, and optionally `mcp_playwright_screenshot` to capture evidence. All of this happens through the MCP protocol — the agent never directly controls the browser; it asks the Playwright MCP server to do it.

### 6.4 — Check the output files

1. In the Explorer panel, expand the `output/` folder. You should see:

   ```diagram
   output/
   ├── state/
   │   └── 00-technews-agent.md                ← Progress tracking
   └── ai-briefing-2026-03-31.md               ← The deliverable!
   ```

2. Open the state file — confirm it shows all phases checked off.

### 6.5 — View the briefing report

3. Open the briefing report — it should contain:
   - A timestamp and the topic searched
   - Top 5 stories with headlines, summaries, sources, and links
   - Quick Takes section with 3 emerging trends
   - Worth Watching section with 2–3 interesting stories
   - Notes on any sources that failed to load

### 6.6 — Try more topics

Run the agent a few more times with different topics:

```
Give me today's top tech news — general overview
```

```
What's the latest news about cybersecurity?
```

```
Any news about cloud computing and infrastructure?
```

> [!TIP]
> Good practice for each new run is to delete previous output files — but you will find they get overwritten if this isn't done.

---

## Step 7 — Refine and Iterate

### 7.1 — Things to try

- Add more sources — perhaps region-specific tech news or niche publications.

- Improved error handling — e.g. timeout after 30 seconds if a site is slow.

- Add visual evidence — capture screenshots of the news pages being scraped.

- Add historical tracking — save each briefing to a `briefing-history/` folder so you can compare news over time.

- Filter by relevance — if the user specifies a topic, only include stories that are genuinely relevant (not just any story from the tech section).

### 7.2 — Change the model

Experiment with different models for the Tech News Agent:

| Model               | Expected result                                              |
| ------------------- | ------------------------------------------------------------ |
| `Claude Opus 4.6`   | More sophisticated scraping, better deduplication            |
| `Claude Sonnet 4.5` | Good balance of speed and reliability (recommended)          |
| `Claude Haiku 4.5`  | Faster but may struggle with complex page structures         |

---

## Summary

Congratulations! 🎉 You have completed Lab 4. Here is what you built:

| Artifact                                        | Location           | Purpose                                                             |
| ----------------------------------------------- | ------------------ | ------------------------------------------------------------------- |
| `technews-requirements.md`                      | Repository root    | Describes the Tech News Agent's requirements and behaviour.         |
| `technews.agent.md`                             | `.github/agents/`  | The Tech News Agent — gathers and summarises tech news via Playwright MCP. |
| Playwright MCP server configuration             | `.vscode/mcp.json` | Tells VS Code to run the Playwright MCP server.                     |
| `output/state/00-technews-agent.md`             | Created at runtime | State file for progress tracking.                                   |
| `output/{topic}-briefing-{date}.md`             | Created at runtime | The deliverable — your tech news briefing!                          |

### Key takeaways

1. **MCP extends agents with real-world capabilities.** By connecting to the Playwright MCP server, your agent can control web browsers, scrape websites, and extract data — all through a standardised protocol.

2. **MCP tools are declared in frontmatter.** The agent specifies which tools it needs in its `tools` array, and VS Code ensures the corresponding MCP servers are running and accessible.

3. **Agents orchestrate, MCP servers execute.** The agent plans the workflow ("visit BBC Tech, then Ars Technica, then ..."), but the Playwright MCP server does the actual browser automation. This separation keeps agents simple and MCP servers reusable.

4. **MCP is composable.** You could add a **Database MCP server** to store historical briefings, a **Notification MCP server** to send daily email digests, or a **Summarisation MCP server** to provide deeper analysis — all without changing the Tech News Agent's core logic.

5. **Structured output makes data actionable.** The markdown briefing format is human-readable but also machine-parseable — you could build another agent that reads these briefings and generates a weekly summary or trend report.

---

### What's next?

Ideas to extend the lab on your own:

- **Add a Digest Agent** — Create a second agent that reads all your briefing files and generates a weekly summary with the biggest stories.
- **Track trending topics** — Modify the agent to tag each story with categories and track which topics appear most frequently over time.
- **Add RSS fallback** — If Playwright fails on a site, fall back to parsing the site's RSS feed instead.
- **Slack integration** — Use a Notification MCP server to post the daily briefing to a Slack channel automatically.

---

### Learn more about MCP

- **Official MCP Specification:** [https://modelcontextprotocol.io/](https://modelcontextprotocol.io/)
- **VS Code MCP Documentation:** [https://code.visualstudio.com/docs/copilot/customization/mcp-servers](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)
- **Playwright Documentation:** [https://playwright.dev/docs/intro](https://playwright.dev/docs/intro)
- **Other MCP Servers:** Browse the community catalog at [https://github.com/mcp](https://github.com/mcp)

---
