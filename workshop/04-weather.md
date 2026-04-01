# Hands-on Lab: Build a Weather Agent (MCP)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Weather Agent**. This agent uses the **MCP (Model Context Protocol)** pattern: an agent leverages an external tool server to interact with the real world. The Weather Agent uses **Playwright MCP** to fetch weather data from public weather services and compile a useful weather briefing.

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
   I want to build a GitHub Copilot custom agent called "Weather Agent"
   that uses the MCP pattern with a Playwright MCP server.

   Its purpose:
   - The user provides a city or location
   - The agent uses Playwright MCP to fetch weather data from public
     weather services:
     1. BBC Weather (bbc.co.uk/weather) — for UK locations
     2. Weather.gov — for US locations
     3. wttr.in — for any global location (fallback)
   - The agent compiles a weather briefing with:
     1. Current conditions (temperature, feels like, wind, humidity)
     2. Today's forecast (high/low, precipitation chance, description)
     3. 3-day outlook (brief summary per day)
     4. What to wear recommendation based on conditions
     5. Activity suggestions (indoor/outdoor based on weather)
   - For multiple locations, compare them side by side
   - Save to ./output/weather-briefing.md

   I want a requirements markdown file for this agent.
   Include:
   - Description, target user
   - MCP tools required
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `weather-requirements.md`.

### 4.2 — Review and refine

```
Add a "travel weather" mode where the user provides a destination and
travel dates, and the agent shows the typical weather for that period
plus a packing recommendation (umbrella, sunscreen, layers, etc.).
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`weather.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:weather-requirements.md generate a "Weather Agent"
   Model: Claude Sonnet 4.5.
   Use a 3-phase workflow: Locate → Fetch → Brief.
   The agent must use MCP Playwright tools for all web interactions.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **MCP usage**       | Uses `browser_navigate`, `browser_snapshot` correctly.     |
| **Workflow**        | Three phases: Locate, Fetch, Brief.                        |
| **Data sources**    | Multiple weather sites with fallback logic.                |

> [!IMPORTANT]
> `tools` must include MCP Playwright tools.

## Step 6 — Test the Weather Agent

### 6.1 — Invoke the agent

1. Select **Weather** from the agent dropdown.
2. Ask about the weather:

   ```
   What's the weather like in London today? Include what I should wear
   and whether it's a good day for an outdoor run.
   ```

3. Watch the agent navigate to weather sites and compile a briefing!

### 6.2 — Try more scenarios

```
Compare the weather in Manchester and Edinburgh for this weekend.
I'm deciding where to go hiking.
```

```
I'm flying to Barcelona next Tuesday. What weather should I expect
and what should I pack?
```

```
Is it going to rain in Birmingham in the next 3 days? I have an
outdoor event on Saturday.
```

## Step 7 — Refine and Iterate

### 7.1 — Add severe weather alerts

```
Update the Weather Agent to check for severe weather warnings (Met
Office for UK, NWS for US) and prominently display them at the top
of the briefing with appropriate urgency levels.
```

### 7.2 — Add historical comparison

```
Update the agent to compare today's weather with the historical
average for this date (e.g., "Today is 5°C above the average for
early December in London") to give context for unusual conditions.
```

---

## Summary

| Artifact                         | Location           | Purpose                                      |
| -------------------------------- | ------------------ | -------------------------------------------- |
| `weather-requirements.md`        | Repository root    | Describes what the Weather Agent does.       |
| `weather.agent.md`               | `.github/agents/`  | The agent definition with MCP tools.         |
| `.vscode/mcp.json`              | `.vscode/`         | MCP server configuration for Playwright.     |
| `output/weather-briefing.md`     | Created at runtime | Compiled weather briefing.                   |

### Key takeaways

1. **MCP tools** extend agents beyond text generation into real-world interaction.
2. **Playwright MCP** enables agents to fetch live data from any website.
3. **Utility agents** demonstrate value through practical daily use.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
