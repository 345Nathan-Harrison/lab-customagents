# Hands-on Lab: Build a Stock Agent (MCP)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Stock Agent**. This agent uses the **MCP (Model Context Protocol)** pattern: an agent leverages an external tool server to interact with the real world. The Stock Agent uses **Playwright MCP** to fetch stock market data from public financial sites and compile an investment research briefing.

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
   I want to build a GitHub Copilot custom agent called "Stock Agent"
   that uses the MCP pattern with a Playwright MCP server.

   Its purpose:
   - The user provides one or more stock ticker symbols (e.g., AAPL,
     MSFT, GOOGL) or company names
   - The agent uses Playwright MCP to fetch data from public financial
     sites:
     1. Google Finance (google.com/finance) — current price, daily change
     2. Yahoo Finance (finance.yahoo.com) — detailed stats, key metrics
     3. MarketWatch (marketwatch.com) — news headlines
   - For each stock, extract:
     1. Current price and daily change (% and absolute)
     2. Market cap
     3. P/E ratio
     4. 52-week high/low
     5. Volume vs average volume
     6. Dividend yield (if applicable)
     7. Latest 3 news headlines
   - Compile findings into a research briefing with:
     - Summary dashboard (all stocks at a glance)
     - Individual stock profiles
     - Comparison table (if multiple stocks)
     - Simple sentiment indicator based on recent news (Bullish/Neutral/Bearish)
   - Save to ./output/stock-briefing.md

   DISCLAIMER: This agent provides information only, not financial advice.

   I want a requirements markdown file for this agent.
   ```

3. Save to `stock-requirements.md`.

### 4.2 — Review and refine

```
Add a mandatory disclaimer at the top and bottom of every output:
"This is for informational purposes only and does not constitute
financial advice. Always consult a qualified financial advisor before
making investment decisions."
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`stock.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:stock-requirements.md generate a "Stock Agent"
   Model: Claude Sonnet 4.5.
   Use a 4-phase workflow: Identify → Fetch → Analyse → Report.
   The agent must use MCP Playwright tools for all web interactions.
   Include a mandatory financial disclaimer in every output.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **MCP usage**       | Uses `browser_navigate`, `browser_snapshot` correctly.     |
| **Workflow**        | Four phases: Identify, Fetch, Analyse, Report.             |
| **Disclaimer**      | Present at top and bottom of every output.                 |

> [!IMPORTANT]
> `tools` must include MCP Playwright tools. The financial disclaimer is mandatory.

## Step 6 — Test the Stock Agent

### 6.1 — Invoke the agent

1. Select **Stock** from the agent dropdown.
2. Request a stock briefing:

   ```
   Give me a briefing on AAPL, MSFT, and GOOGL.
   Compare their current valuations and recent performance.
   ```

3. Watch the agent fetch live data from financial sites and compile a report!

### 6.2 — Try more scenarios

```
Research Tesla (TSLA) — I want to understand the current valuation,
recent news sentiment, and how it compares to its 52-week range.
```

```
Compare the UK banks: Barclays (BARC.L), Lloyds (LLOY.L), and
NatWest (NWG.L). Focus on dividend yields and P/E ratios.
```

```
What's happening with NVIDIA (NVDA) today? Give me a quick snapshot
with the latest news.
```

## Step 7 — Refine and Iterate

### 7.1 — Add a watchlist feature

```
Update the Stock Agent to save a watchlist file in output/state/watchlist.md.
When the user says "check my watchlist", the agent fetches all saved
tickers and compares current prices to the prices from the last run.
Show which went up and which went down.
```

### 7.2 — Add sector analysis

```
Update the agent so the user can say "tech sector overview" and it
fetches the top 5 tech stocks by market cap, plus the Nasdaq composite
index performance, and summarises the sector's direction.
```

---

## Summary

| Artifact                       | Location           | Purpose                                        |
| ------------------------------ | ------------------ | ---------------------------------------------- |
| `stock-requirements.md`        | Repository root    | Describes what the Stock Agent does.           |
| `stock.agent.md`               | `.github/agents/`  | The agent definition with MCP tools.           |
| `.vscode/mcp.json`            | `.vscode/`         | MCP server configuration for Playwright.       |
| `output/stock-briefing.md`     | Created at runtime | Compiled stock research briefing.              |

### Key takeaways

1. **MCP tools** extend agents beyond text generation into real-world interaction.
2. **Playwright MCP** enables agents to fetch live financial data from any site.
3. **Financial agents** demonstrate high-value data aggregation automation.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
