# Hands-on Lab 2: Build a Multi-Agent Incident Report Factory

## Overview

In this lab you will create a **multi-agent pipeline** using GitHub Copilot Custom Agents. The pipeline is called the **Incident Agent** — an **Orchestrator** that coordinates two **Subagents**:

| Agent           | Archetype    | Job                                                                    |
| --------------- | ------------ | ---------------------------------------------------------------------- |
| Incident Agent  | Orchestrator | Receives the user's raw incident notes and drives the two-step pipeline |
| Analyser Agent  | Subagent     | Structures the raw notes into a timeline and root-cause analysis        |
| Reporter Agent  | Subagent     | Takes the analysis and produces a formal incident report as HTML        |

The user gives the Incident Agent a dump of raw notes about an incident (e.g. "Database went down at 14:32, team noticed at 14:45, Jane restarted the pod, back up at 15:10"). The Orchestrator passes the notes to the Analyser subagent, which produces a structured timeline and root-cause analysis. Then the Reporter subagent takes that analysis and produces a self-contained HTML incident report with professional formatting.

### What you will learn

- How to design a **multi-agent Orchestrator + Subagent pipeline**.
- How to write a **requirements document** for a multi-agent system.
- How to create an **Orchestrator `.agent.md`** file that delegates work to subagents.
- How to create **Subagent `.agent.md`** files that each handle one pipeline step.
- How subagents communicate through **state files** in `./output/state/`.
- How to test the entire pipeline end-to-end inside VS Code.

### Prerequisites

| Requirement                    | Details                                                   |
| ------------------------------ | --------------------------------------------------------- |
| **Completed Lab 1 (Bio Agent)** | You should be comfortable creating a single custom agent. |
| **GitHub account**             | Must have Copilot enabled.                                |
| **GitHub Copilot**             | Active subscription.                                      |
| **VS Code**                    | Always use the latest.                                    |

To read more about Subagents in VS Code — check [https://code.visualstudio.com/docs/copilot/agents/subagents](https://code.visualstudio.com/docs/copilot/agents/subagents)

## Step 1 — Open the Starter Repository in VS Code

You will use the same starter repository from Lab 1. If you no longer have it, re-clone it now (see Lab 1, Step 1).

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
   │   │   ├── _subagents/
   │   │   │   └── ...
   │   │   └── ...
   │   └── instructions/
   │       └── custom-agent.instructions.md
   ├── README.md
   └── ...
```

4. Open **Copilot Chat** and confirm it is active.

---

## Step 2 — Understand the Architecture

Before writing any files, take a moment to understand how a multi-agent pipeline works.

### How Orchestrator + Subagent Pipelines Work

```diagram
┌─────────────────────────────────────────┐
│            USER PROMPT                  │
│  "Here are my raw incident notes..."    │
└──────────────────┬──────────────────────┘
                   │
                   ▼
┌──────────────────────────────────┐
│         INCIDENT AGENT           │
│         (Orchestrator)           │
│                                  │
│  Step 1 → Analyser Agent         │
│  Step 2 → Reporter Agent         │
└──────────────────┬───────────────┘
                   │
        ┌──────────┴───────────┐
        ▼                      ▼
┌──────────────────┐   ┌──────────────────┐
│  ANALYSER AGENT  │   │  REPORTER AGENT  │
│   (Subagent)     │   │   (Subagent)     │
│                  │   │                  │
│  Structures the  │──▶│  Reads analysis  │
│  notes into a    │   │  and builds a    │
│  timeline + RCA  │   │  formal HTML     │
│  state file      │   │  incident report │
└──────────────────┘   └──────────────────┘
```

Key concepts:

- **Orchestrator** — The Incident Agent never writes the analysis or the report itself. It only _delegates_ work to its subagents and manages the pipeline flow.
- **Subagents** — Each subagent does exactly one job. The Analyser structures the raw data; the Reporter builds the formatted report.
- **State files** — Subagents communicate through state files in `./output/state/`. The Analyser writes `01-analyser.md` containing the structured analysis. The Reporter reads that file, then writes `02-reporter.md` and the HTML deliverable.
- **Gates** — Between each step the orchestrator can pause for approval or auto-continue.

> [!TIP]
> Delete any existing content in ./output/state/ that is carried over from Lab 1.

---

## Step 3 — Use Copilot to Write the Requirements Document

Just like in Lab 1, you will start by writing a requirements document. This time it describes the full multi-agent system.

### 3.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat**.
2. Set the mode to **Plan** — click the mode selector at the bottom of the chat panel and choose **Plan**.

3. Type the following prompt:

   ```
   I want to build a multi-agent GitHub Copilot custom agent system called "Incident Agent".
   Architecture:
   - An Orchestrator called "Incident Agent" that coordinates a 2-step pipeline.
   - Subagent 1: "Analyser Agent" — receives raw incident notes from the user and
     produces a structured analysis with:
     - Incident title
     - Severity level (P1–P4)
     - Timeline of events (chronological)
     - Root cause analysis
     - Impact assessment
     - Remediation actions taken
     - Recommended follow-ups
   - Subagent 2: "Reporter Agent" — reads the structured analysis from the
     Analyser Agent's state file and creates a self-contained HTML incident
     report with professional formatting, colour-coded severity, and a
     clear timeline visualisation.

   Flow:
   1. User gives the Incident Agent raw notes about an incident.
   2. Incident Agent delegates to Analyser Agent → analysis is written to
      ./output/state/01-analyser.md
   3. Incident Agent delegates to Reporter Agent → HTML report is saved to
      ./output/incident-report.html
   4. Incident Agent presents the result to the user.

   I want a requirements markdown file for these agents.
   Include:
   - A short description of the system
   - Architecture overview (orchestrator + 2 subagents)
   - Functional requirements for each agent
   - Non-functional requirements (tone, format, constraints)
   - Example input and expected outputs
   ```

4. Review the generated requirements. You should see sections covering:

   | Section                            | What to look for                                                                 |
   | ---------------------------------- | -------------------------------------------------------------------------------- |
   | Description                        | One-liner explaining the Incident Agent pipeline.                                |
   | Architecture Overview              | Diagram or table showing Orchestrator → Analyser → Reporter flow.                |
   | Functional Requirements (Analyser) | Structures raw notes into timeline, RCA, severity, and remediation.              |
   | Functional Requirements (Reporter) | Reads analysis, produces styled HTML report with colour-coded severity.          |
   | Non-functional Requirements        | Professional tone, factual, no invented details, responsive HTML.                |
   | Example                            | Input: raw incident notes → Structured analysis → HTML incident report.          |

5. In Plan mode — the agent cannot write to the file. You have two options depending on how the agent responds. Either:

- Copy the suggested requirements to the `incident-requirements.md` file and save it
- If given the option 'Open in Editor' select that and then save to `incident-requirements.md`

### 3.2 — Review and refine

Read through the requirements.

Suggested refinements you can ask Copilot:

```
Add a constraint that the HTML report must be fully self-contained — no external
CSS frameworks, no JavaScript dependencies, just inline CSS and HTML.
```

```
Add a constraint that the agent must never invent details not present in the
original notes — it should flag gaps rather than fill them.
```

Save the file when you are satisfied.

---

## Step 4 — Use Copilot to Create the Agent Definition Files

Now you will ask Copilot to create all three agents — the orchestrator and both subagents — based on the requirements document. The `custom-agent.instructions.md` file will guide Copilot to produce well-structured agent definitions.

### 4.1 — Ask Copilot to scaffold the agents

1. Open **Copilot Chat**.

2. Make sure you are in **Agent** mode (not Ask or Plan) — Copilot needs to be able to create files.

Create a new Chat session to start with a clean slate.

3. Type the following prompt:

```
   Using #file:incident-requirements.md and #file:custom-agent.instructions.md,
   create all the agents for the Incident Agent pipeline —
   the orchestrator and both subagents.
```

4. Copilot will create the three files. Review each one as it appears — accept the changes and save.

### 4.2 — Verify the generated agents

Walk through each file and spot-check these key elements:

**Incident Agent** (`.github/agents/incident.agent.md`) — Orchestrator:

| Element                 | What to check                                                                   |
| ----------------------- | ------------------------------------------------------------------------------- |
| **Frontmatter**         | `name: "Incident Agent"`, `tools: ["read", "edit", "agent"]`, `agents: ["*"]`   |
| **Pipeline Definition** | Step table with Analyser Agent (Step 1) and Reporter Agent (Step 2)             |
| **State Management**    | Describes scanning `./output/state/` and resuming from first unchecked step     |

**Analyser Agent** (`.github/agents/_subagents/analyser.agent.md`) — Subagent:

| Element         | What to check                                                                  |
| --------------- | ------------------------------------------------------------------------------ |
| **Frontmatter** | `name: "Analyser Agent"`, `user-invocable: false`, `tools: ["read", "edit"]`   |
| **Workflow**    | Multi-phase workflow ending with analysis written to the state file             |
| **State file**  | Writes to `./output/state/01-analyser.md` with structured headings             |

**Reporter Agent** (`.github/agents/_subagents/reporter.agent.md`) — Subagent:

| Element                 | What to check                                                                  |
| ----------------------- | ------------------------------------------------------------------------------ |
| **Frontmatter**         | `name: "Reporter Agent"`, `user-invocable: false`, `tools: ["read", "edit"]`   |
| **Prerequisites Check** | Checks for `./output/state/01-analyser.md` — stops if missing                 |
| **Output Files**        | State: `./output/state/02-reporter.md`, Deliverable: `./output/incident-report.html` |

> [!TIP]
> If anything is missing or looks off, just tell Copilot what to fix in the chat.

---

## Step 5 — Verify the Complete File Structure

Before testing, confirm all files are in place.

1. In the Explorer panel, verify:

```diagram
   .github/
   ├── agents/
   │   ├── _subagents/
   │   │   ├── analyser.agent.md     ← Subagent: structures raw notes
   │   │   └── reporter.agent.md     ← Subagent: builds HTML report
   │   ├── incident.agent.md         ← Orchestrator: runs the pipeline
   │   └── bio.agent.md              ← from Lab 1
   └── instructions/
       └── custom-agent.instructions.md
```

2. Make sure all three new files are saved.

---

## Step 6 — Test the Incident Agent Pipeline

Time to run the full pipeline!

Create a new Chat session to start with a clean slate.

### 6.1 — Open Copilot Chat

1. Open **Copilot Chat**.

### 6.2 — Invoke the Incident Agent

1. Select **Incident Agent** from the agent dropdown (where it normally says Agent | Ask | Plan).

2. Type a realistic incident scenario:

   ```
   Raw incident notes:
   - 14:32 UTC: Monitoring alerts fired for API latency > 5s on prod-eu-west
   - 14:35: On-call engineer Sarah pinged in Slack
   - 14:38: Sarah confirmed database connection pool exhausted
   - 14:42: Checked recent deployments — v2.4.1 deployed at 14:15 with new
     query joining 3 tables without index
   - 14:45: Sarah rolled back to v2.4.0
   - 14:48: Connection pool recovering, latency dropping
   - 14:55: All metrics back to normal
   - 15:10: Post-incident Slack thread started
   - Impact: ~25 minutes of degraded API performance, 12% of requests
     returned 503, affected approx 2,400 users
   - No data loss confirmed
   ```

3. Press Enter and watch the orchestrator work through the pipeline.

### 6.3 — Observe the pipeline execution

As the Incident Agent runs, you should see it:

1. **Parse the raw notes** — validates the input is incident-related
2. **Invoke the Analyser Agent** — structures the notes into a timeline, root cause, and severity assessment in `./output/state/01-analyser.md`
3. **Invoke the Reporter Agent** — reads the analysis and creates `./output/incident-report.html`
4. **Present the result** — shows you a summary and points you to the HTML report

> [!TIP]
> Keep the Explorer panel open so you can watch the `output/` folder populate in real time as each subagent does its work.

### 6.4 — Check the output files

1. In the Explorer panel, expand the `output/` folder. You should see:

```diagram
   output/
   ├── state/
   │   ├── 00-incident-agent.md     ← Orchestrator pipeline state
   │   ├── 01-analyser.md           ← Analyser's state (structured analysis)
   │   └── 02-reporter.md           ← Reporter's state
   └── incident-report.html         ← The deliverable — your incident report!
```

2. Open `01-analyser.md` — confirm it contains a timeline, severity level, root cause analysis, and remediation actions.

3. Open `02-reporter.md` — confirm it shows all phases checked off.

4. Open `00-incident-agent.md` — confirm both pipeline steps are marked complete.

### 6.5 — View the HTML report

1. Right-click on `output/incident-report.html` in the Explorer panel.
2. Select **Open with Live Server** (if you have the Live Server extension) or **Reveal in File Explorer** and open the file in your browser.
3. You should see a professionally styled incident report with colour-coded severity, a clear timeline, and action items!

### 6.6 — Try more incidents

Run the pipeline a few more times with different scenarios:

```
   Notes: Website was defaced at 09:00 this morning. Security team noticed
   at 09:15. Traced to compromised CMS credentials. Password reset at 09:20,
   site restored from backup at 09:45. Affected pages: homepage and about page.
   No customer data exposed.
```

```
   Payment processing failed for 40 minutes starting 11:22. Stripe webhook
   endpoint returning 500. Root cause: expired TLS certificate on the webhook
   receiver. Renewed cert at 12:01, payments resumed. ~160 transactions queued
   and processed successfully after recovery. No revenue loss.
```

> [!TIP]
> Good practice for each new run is to delete previous output files — but you will find they get overwritten if this isn't done.

---

## Step 7 — Refine and Iterate

### 7.1 — Improve the analysis quality

Open `analyser.agent.md` and ask Copilot:

```
Update the Analyser Agent so it also assesses:
- Time to detection (how long between incident start and first alert)
- Time to resolution (total duration)
- A severity recommendation with justification
Include a "Gaps in Notes" section that flags any missing information.
```

### 7.2 — Improve the HTML report

Open `reporter.agent.md` and ask Copilot:

```
Update the Reporter Agent so the HTML report includes:
- A severity badge with colour coding (P1=red, P2=orange, P3=yellow, P4=green)
- A visual timeline using CSS (not just a list)
- An "Action Items" section with checkboxes
- A "Print-friendly" CSS media query
```

### 7.3 — Change the model

Experiment with different models:

| Model               | Expected result                                            |
| ------------------- | ---------------------------------------------------------- |
| `Claude Opus 4.6`   | Most thorough analysis, best-structured reports            |
| `Claude Sonnet 4.5` | Good balance of speed and quality (recommended)            |
| `Claude Haiku 4.5`  | Fastest response, may miss nuances in complex incidents    |

---

## Summary

Congratulations! 🎉 You have completed Lab 2. Here is what you built:

| Artifact                  | Location                    | Purpose                                                          |
| ------------------------- | --------------------------- | ---------------------------------------------------------------- |
| `incident-requirements.md`| Repository root             | Describes the multi-agent system requirements.                   |
| `incident.agent.md`       | `.github/agents/`           | The Orchestrator — manages the analysis → report pipeline.       |
| `analyser.agent.md`       | `.github/agents/_subagents/`| Subagent that structures raw notes into a formal analysis.       |
| `reporter.agent.md`       | `.github/agents/_subagents/`| Subagent that builds a styled HTML incident report.              |
| `output/state/`           | Created at runtime          | State files for pipeline tracking and inter-agent communication. |

### Key takeaways

1. **Orchestrators delegate, they don't do the work.** The Incident Agent never analyses data or writes HTML itself — it coordinates the pipeline and tracks progress.
2. **Subagents are specialists.** Each subagent has one clear job, which makes them easier to build, test, and improve independently.
3. **State files are the communication channel.** Subagents read and write state files in `./output/state/` to pass work between pipeline steps.
4. **Real-world use cases shine with multi-agent architectures.** Incident management is a natural fit — analysis and reporting are distinct skills that benefit from separation.

---
