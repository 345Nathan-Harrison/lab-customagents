# Hands-on Lab: Build a News Agent (MCP)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **News Agent**. This agent uses the **MCP (Model Context Protocol)** pattern: an agent leverages an external tool server to interact with the real world. The News Agent uses **Playwright MCP** to scrape headlines from multiple news sources and compile a personalised news digest — similar to the Tech News Agent but covering any topic the user chooses.

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
   I want to build a GitHub Copilot custom agent called "News Agent"
   that uses the MCP pattern with a Playwright MCP server.

   Its purpose:
   - The user specifies a topic of interest (e.g., "AI", "climate change",
     "UK politics", "space exploration", "football")
   - The agent uses Playwright MCP to scrape headlines from multiple
     generalist news sources:
     1. BBC News (bbc.co.uk/news) — search or section page
     2. The Guardian (theguardian.com) — topic page
     3. Reuters (reuters.com) — topic search
     4. Google News (news.google.com) — topic search
   - For each article found, extract:
     1. Headline
     2. Source (which news outlet)
     3. Publication date/time
     4. Brief summary or first paragraph
     5. URL to the full article
   - Compile findings into a news digest with:
     - Topic summary (3-4 sentences synthesising the key themes)
     - Top stories (up to 10, deduplicated across sources)
     - Source diversity indicator (how many different outlets covered)
     - Trending angle (what's new vs ongoing story)
   - Save to ./output/news-digest.md

   I want a requirements markdown file for this agent.
   ```

3. Save to `news-requirements.md`.

### 4.2 — Review and refine

```
Add a "daily brief" mode where the user can define favourite topics in
a config file (./topics.md), and the agent checks ALL topics and produces
a single combined morning briefing sorted by category.
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`news.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:news-requirements.md generate a "News Agent"
   Model: Claude Sonnet 4.5.
   Use a 4-phase workflow: Topic → Scrape → Synthesise → Digest.
   The agent must use MCP Playwright tools for all web interactions.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **MCP usage**       | Uses `browser_navigate`, `browser_snapshot` correctly.     |
| **Workflow**        | Four phases: Topic, Scrape, Synthesise, Digest.            |
| **Deduplication**   | Same story from multiple sources merged correctly.         |

> [!IMPORTANT]
> `tools` must include MCP Playwright tools.

## Step 6 — Test the News Agent

### 6.1 — Invoke the agent

1. Select **News** from the agent dropdown.
2. Request a news digest:

   ```
   Give me a news digest on "artificial intelligence" —
   what are the top stories today?
   ```

3. Watch the agent scrape multiple news sources and compile a digest!

### 6.2 — Try more topics

```
What's happening in UK politics today? Focus on anything from the
last 24 hours.
```

```
Get me the latest news on climate change and renewable energy.
Prioritise scientific developments over policy news.
```

```
Sports news: What happened in the Premier League this weekend?
```

## Step 7 — Refine and Iterate

### 7.1 — Add sentiment analysis

```
Update the News Agent to categorise each story's tone as Positive,
Neutral, or Negative, and include an overall sentiment summary for the
topic (e.g., "Coverage is mostly negative, focused on regulation concerns").
```

### 7.2 — Add comparison mode

```
Add a mode where the user provides 2 topics and the agent compares
their media coverage: which topic is getting more attention, what's the
sentiment difference, and how many sources are covering each.
```

---

## Summary

| Artifact                       | Location           | Purpose                                       |
| ------------------------------ | ------------------ | --------------------------------------------- |
| `news-requirements.md`         | Repository root    | Describes what the News Agent does.           |
| `news.agent.md`                | `.github/agents/`  | The agent definition with MCP tools.          |
| `.vscode/mcp.json`            | `.vscode/`         | MCP server configuration for Playwright.      |
| `output/news-digest.md`        | Created at runtime | Compiled news digest.                         |

### Key takeaways

1. **MCP tools** extend agents beyond text generation into real-world interaction.
2. **Playwright MCP** enables agents to scrape and aggregate live news data.
3. **News aggregation agents** demonstrate practical daily utility.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
