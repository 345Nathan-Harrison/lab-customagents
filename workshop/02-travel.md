# Hands-on Lab: Build a Travel Agent (Orchestrator)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Travel Agent**. This agent uses the **orchestrator + subagent** pattern: a master agent delegates to a **Destination Expert** subagent (to research the location) and an **Itinerary Builder** subagent (to plan the day-by-day schedule). The result is a complete, personalised travel itinerary.

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
   I want to build a multi-agent system called "Travel Agent" using the
   orchestrator pattern.

   The system has 3 agents:
   1. **Travel Orchestrator** (main agent the user talks to)
      - Receives: destination, travel dates, number of travellers,
        budget level (budget/mid-range/luxury), interests (culture,
        food, adventure, relaxation, nightlife), any constraints
      - Delegates to subagents and assembles the final itinerary
   2. **Destination Expert** (subagent)
      - Takes the destination and traveller profile, produces:
        best neighbourhoods to stay, local customs and etiquette,
        weather expectations, transportation options, safety tips,
        must-know phrases in local language
      - Saves research to ./output/state/destination-research.md
   3. **Itinerary Builder** (subagent)
      - Takes the destination research + traveller preferences, produces:
        day-by-day schedule with morning/afternoon/evening activities,
        restaurant recommendations for each meal, estimated daily spend,
        travel time between activities, booking tips
      - Saves the itinerary to ./output/travel-itinerary.md

   I want a requirements markdown file for this system.
   Include:
   - Agent descriptions and responsibilities
   - Data flow between agents
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `travel-requirements.md`.

### 4.2 — Review and refine

```
Add a packing list feature. The Destination Expert should also generate
a packing checklist based on the destination weather, planned activities,
and trip duration.
```

## Step 5 — Use Copilot to Create the Agent Definition Files

### 5.1 — Create the orchestrator

1. Create `.github/agents/travel.agent.md`
2. Ask Copilot:

   ```
   Using #file:travel-requirements.md generate the main "Travel
   Orchestrator" agent. It should delegate to @destination-expert and
   @itinerary-builder subagents. Model: Claude Sonnet 4.5. Workflow:
   Brief → Research → Plan → Package.
   ```

### 5.2 — Create the Destination Expert subagent

1. Create `.github/agents/destination-expert.agent.md`
2. Ask Copilot:

   ```
   Using #file:travel-requirements.md generate the "Destination Expert"
   subagent. It produces comprehensive destination research.
   Model: Claude Sonnet 4.5.
   ```

### 5.3 — Create the Itinerary Builder subagent

1. Create `.github/agents/itinerary-builder.agent.md`
2. Ask Copilot:

   ```
   Using #file:travel-requirements.md generate the "Itinerary Builder"
   subagent. It creates a day-by-day travel plan. Model: Claude Sonnet 4.5.
   ```

### 5.4 — Review all three agents

| Element             | Orchestrator | Destination Expert    | Itinerary Builder    |
| ------------------- | ------------ | --------------------- | -------------------- |
| `tools`             | `["read", "edit"]` | `["read", "edit"]` | `["read", "edit"]` |
| Delegates to        | Both subagents | N/A                 | N/A                  |
| Reads state from    | Both outputs | Raw input             | Destination research |
| Writes to           | Final output | `destination-research.md` | `travel-itinerary.md` |

## Step 6 — Test the Travel Agent

### 6.1 — Invoke the orchestrator

1. Select **Travel** from the agent dropdown.
2. Provide trip details:

   ```
   Destination: Tokyo, Japan
   Dates: 7 days in April (cherry blossom season)
   Travellers: 2 adults
   Budget: Mid-range
   Interests: Food (especially street food and ramen), temples and
   shrines, quirky neighbourhoods, anime/manga culture, photography
   Constraints: One traveller is vegetarian
   ```

3. Watch the orchestrator delegate and assemble a complete itinerary!

### 6.2 — Try another trip

```
Destination: Lisbon, Portugal
Dates: 4 days in September
Travellers: Solo traveller
Budget: Budget
Interests: History, local food, live music, street art, surfing
Constraints: No car — must rely on public transport and walking
```

## Step 7 — Refine and Iterate

### 7.1 — Add a Budget Tracker subagent

```
Add a third subagent called "Budget Tracker" that takes the itinerary
and calculates a detailed budget breakdown: accommodation, food,
activities, transport, and miscellaneous. Show daily and trip totals
in both local currency and GBP.
```

### 7.2 — Add a rainy day alternative

```
Update the Itinerary Builder so each day has a "Rain Plan" — an
indoor alternative if the weather turns bad.
```

---

## Summary

| Artifact                              | Location           | Purpose                                        |
| ------------------------------------- | ------------------ | ---------------------------------------------- |
| `travel-requirements.md`              | Repository root    | System requirements for all 3 agents.          |
| `travel.agent.md`                     | `.github/agents/`  | Orchestrator — delegates and assembles.        |
| `destination-expert.agent.md`         | `.github/agents/`  | Subagent — researches the destination.         |
| `itinerary-builder.agent.md`          | `.github/agents/`  | Subagent — builds the day-by-day plan.         |
| `output/state/destination-research.md`| Created at runtime | Intermediate data between subagents.           |
| `output/travel-itinerary.md`          | Created at runtime | Final complete travel itinerary.               |

### Key takeaways

1. **The orchestrator pattern** breaks complex tasks into specialised roles.
2. **State files** are the glue — subagents communicate through Markdown files.
3. **Rich domain agents** showcase AI's ability to synthesise diverse information.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
