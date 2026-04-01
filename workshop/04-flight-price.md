# Hands-on Lab: Build a Flight Price Agent (MCP)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Flight Price Agent**. This agent uses the **MCP (Model Context Protocol)** pattern: an agent leverages an external tool server to interact with the real world. The Flight Price Agent uses **Playwright MCP** to search flight comparison sites and compile a price comparison report.

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
   I want to build a GitHub Copilot custom agent called "Flight Price Agent"
   that uses the MCP pattern with a Playwright MCP server.

   Its purpose:
   - The user provides: origin, destination, departure date, return date
     (optional for one-way), number of passengers, and preferences
     (direct flights only, cabin class, preferred airlines)
   - The agent uses Playwright MCP to search flight comparison sites:
     1. Google Flights (google.com/travel/flights) — for comparison
     2. Skyscanner (skyscanner.net) — for price comparison
   - For each result, extract:
     1. Airline(s) and flight number(s)
     2. Departure and arrival times
     3. Duration and number of stops
     4. Price per person and total price
     5. Baggage allowance (if shown)
   - Compile findings into a report with:
     - Cheapest option highlighted
     - Fastest option highlighted
     - Best value option (balance of price and convenience)
     - Price comparison table sorted by price
     - Tips (best day to fly, flexible dates savings)
   - Save to ./output/flight-prices.md

   I want a requirements markdown file for this agent.
   ```

3. Save to `flight-price-requirements.md`.

### 4.2 — Review and refine

```
Add a "flexible dates" mode where the agent checks prices for +/- 3 days
around the given dates and shows a date grid highlighting the cheapest
combination of outbound and return dates.
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`flight-price.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:flight-price-requirements.md generate a "Flight Price Agent"
   Model: Claude Sonnet 4.5.
   Use a 4-phase workflow: Parse → Search → Compare → Report.
   The agent must use MCP Playwright tools for all web interactions.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **MCP usage**       | Uses `browser_navigate`, `browser_snapshot`, `browser_click`. |
| **Workflow**        | Four phases: Parse, Search, Compare, Report.               |
| **Highlights**      | Cheapest, fastest, and best value clearly marked.          |

> [!IMPORTANT]
> `tools` must include MCP Playwright tools.

## Step 6 — Test the Flight Price Agent

### 6.1 — Invoke the agent

1. Select **Flight Price** from the agent dropdown.
2. Search for flights:

   ```
   Find flights from London to Barcelona
   Departing: 15th March
   Returning: 19th March
   Passengers: 2 adults
   Preferences: Direct flights preferred, economy class
   ```

3. Watch the agent search flight comparison sites and compile a price report!

### 6.2 — Try more scenarios

```
One-way flight from Manchester to Dublin
Date: Next Friday
1 passenger
Any airline, cheapest option
```

```
Find flights from London to New York
Departing: 1st June
Returning: 15th June
2 adults, 1 child
Business class preferred
Use flexible dates mode (+/- 3 days)
```

## Step 7 — Refine and Iterate

### 7.1 — Add price tracking

```
Update the Flight Price Agent to save search results to a state file
with timestamps. On repeat searches, show a price trend: "Prices have
gone UP/DOWN by X% since your last search on [date]."
```

### 7.2 — Add airport alternative suggestions

```
Update the agent to also check nearby airports. For example, if the user
says "London", also check Gatwick, Stansted, and Luton alongside Heathrow,
and show if a different airport offers significant savings.
```

---

## Summary

| Artifact                          | Location           | Purpose                                       |
| --------------------------------- | ------------------ | --------------------------------------------- |
| `flight-price-requirements.md`   | Repository root    | Describes what the Flight Price Agent does.   |
| `flight-price.agent.md`          | `.github/agents/`  | The agent definition with MCP tools.          |
| `.vscode/mcp.json`              | `.vscode/`         | MCP server configuration for Playwright.      |
| `output/flight-prices.md`        | Created at runtime | Compiled flight price comparison.             |

### Key takeaways

1. **MCP tools** extend agents beyond text generation into real-world interaction.
2. **Playwright MCP** enables agents to interact with complex web UIs like flight search.
3. **Price comparison agents** demonstrate practical automation that saves money.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
