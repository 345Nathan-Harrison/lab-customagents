# Hands-on Lab: Build a Job Ad Agent (Orchestrator)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Job Ad Agent**. This agent uses the **orchestrator + subagent** pattern: a master agent delegates to a **Researcher** subagent (to analyse the role) and a **Copywriter** subagent (to craft the listing). The result is a polished, inclusive job advertisement.

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
   I want to build a multi-agent system called "Job Ad Agent" using the
   orchestrator pattern.

   The system has 3 agents:
   1. **Job Ad Orchestrator** (main agent the user talks to)
      - Receives: job title, department, key responsibilities, required
        skills, nice-to-haves, salary range (optional), location/remote
      - Delegates to subagents and assembles the final output
   2. **Role Researcher** (subagent)
      - Takes the raw input and produces: a structured role profile,
        industry-standard title suggestions, skill categorisation
        (must-have vs nice-to-have), and seniority level assessment
      - Saves analysis to ./output/state/role-analysis.md
   3. **Ad Copywriter** (subagent)
      - Takes the role analysis and produces: an engaging job ad with
        company intro placeholder, role summary, responsibilities,
        requirements, benefits placeholder, and inclusive language
      - Uses gender-neutral language throughout
      - Saves the final ad to ./output/job-ad.md

   I want a requirements markdown file for this system.
   Include:
   - Agent descriptions and responsibilities
   - Data flow between agents
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `job-ad-requirements.md`.

### 4.2 — Review and refine

```
Add a requirement that the Copywriter must score the ad for inclusivity
using a simple checklist:
- No gendered language
- No unnecessary jargon
- No "rockstar/ninja/guru" terms
- Clear distinction between required and preferred qualifications
- Salary transparency (flag if missing)
```

## Step 5 — Use Copilot to Create the Agent Definition Files

### 5.1 — Create the orchestrator

1. Create `.github/agents/job-ad.agent.md`
2. Ask Copilot:

   ```
   Using #file:job-ad-requirements.md generate the main "Job Ad
   Orchestrator" agent. It should delegate to @role-researcher and
   @ad-copywriter subagents. Model: Claude Sonnet 4.5. Workflow:
   Gather → Research → Write → Review.
   ```

### 5.2 — Create the Role Researcher subagent

1. Create `.github/agents/role-researcher.agent.md`
2. Ask Copilot:

   ```
   Using #file:job-ad-requirements.md generate the "Role Researcher"
   subagent. It analyses raw job input and produces a structured role
   profile. Model: Claude Sonnet 4.5.
   ```

### 5.3 — Create the Ad Copywriter subagent

1. Create `.github/agents/ad-copywriter.agent.md`
2. Ask Copilot:

   ```
   Using #file:job-ad-requirements.md generate the "Ad Copywriter"
   subagent. It takes a role analysis and produces a polished, inclusive
   job ad. Model: Claude Sonnet 4.5.
   ```

### 5.4 — Review all three agents

| Element             | Orchestrator | Researcher       | Copywriter       |
| ------------------- | ------------ | ---------------- | ---------------- |
| `tools`             | `["read", "edit"]` | `["read", "edit"]` | `["read", "edit"]` |
| Delegates to        | Both subagents | N/A             | N/A              |
| Reads state from    | Both outputs | Raw input        | Role analysis    |
| Writes to           | Final output | `role-analysis.md` | `job-ad.md`    |

## Step 6 — Test the Job Ad Agent

### 6.1 — Invoke the orchestrator

1. Select **Job Ad** from the agent dropdown.
2. Provide job details:

   ```
   I need a job ad for:
   - Title: Senior Backend Engineer
   - Department: Platform Engineering
   - Location: Remote (UK timezone)
   - Responsibilities: Design and build microservices, mentor junior devs,
     participate in architecture decisions, on-call rotation
   - Required: 5+ years backend experience, Go or Rust, PostgreSQL,
     Kubernetes, CI/CD
   - Nice to have: gRPC, event-driven architecture, OpenTelemetry
   - Salary: £75,000 - £95,000
   ```

3. Watch the orchestrator delegate to both subagents and assemble the result!

### 6.2 — Try another role

```
Title: Junior Data Analyst
Department: Business Intelligence
Location: Hybrid (London, 2 days in office)
Responsibilities: Build dashboards, write SQL queries, present findings
Required: SQL, Excel, basic Python, degree in any analytical field
Nice to have: Tableau, dbt, statistics background
Salary: not specified
```

## Step 7 — Refine and Iterate

### 7.1 — Add an interview question generator

```
Add a third subagent called "Interview Designer" that generates 5
structured interview questions based on the role analysis. Each question
should target a specific required skill and include what a good answer
looks like.
```

### 7.2 — Add format options

```
Update the Copywriter to ask the user what format they want:
- LinkedIn post style (shorter, punchier)
- Traditional job board style (comprehensive)
- Internal posting style (less sales-y, more factual)
```

---

## Summary

| Artifact                       | Location           | Purpose                                        |
| ------------------------------ | ------------------ | ---------------------------------------------- |
| `job-ad-requirements.md`       | Repository root    | System requirements for all 3 agents.          |
| `job-ad.agent.md`              | `.github/agents/`  | Orchestrator — delegates and assembles.        |
| `role-researcher.agent.md`     | `.github/agents/`  | Subagent — analyses the role.                  |
| `ad-copywriter.agent.md`       | `.github/agents/`  | Subagent — writes the job ad.                  |
| `output/state/role-analysis.md`| Created at runtime | Intermediate data between subagents.           |
| `output/job-ad.md`             | Created at runtime | Final polished job advertisement.              |

### Key takeaways

1. **The orchestrator pattern** breaks complex tasks into specialised roles.
2. **State files** are the glue — subagents communicate through Markdown files.
3. **Agent chaining** produces higher quality than a single monolithic prompt.
4. **Iterate quickly** — all agents are Markdown, edits take seconds.

---
