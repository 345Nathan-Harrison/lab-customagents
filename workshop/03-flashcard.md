# Hands-on Lab: Build a Flashcard Agent (Skills)

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Flashcard Agent**. This agent uses the **skills pattern**: a single agent is enhanced with reusable skill files that give it specialised capabilities. The Flashcard Agent takes any topic or study material and generates structured flashcard decks using a **question design skill** and a **spaced repetition skill**.

### What you will learn

- How to build an agent that uses **reusable skill files** (`.md` files in a skills folder).
- How to write a **requirements document** for a skills-based agent.
- How to create an **`.agent.md`** file that references skill files.
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

## Step 3 — Read the Instruction File (Optional but Recommended)

Skim `.github/instructions/custom-agent.instructions.md`. Pay special attention to the **Skills pattern** section.

## Step 4 — Use Copilot to Write the Requirements Document

### 4.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat** in **Plan** mode.
2. Type:

   ```
   I want to build a GitHub Copilot custom agent called "Flashcard Agent"
   that uses the skills pattern.

   The agent:
   - Receives a topic, a set of notes, or a document to study
   - Produces a deck of flashcards in Markdown format

   It uses 2 skill files:
   1. **Question Design Skill** (.github/instructions/question-design.instructions.md)
      - Knows how to create effective flashcard questions:
        - Factual recall (What is...?)
        - Conceptual understanding (Why does...?)
        - Application (How would you...?)
        - Comparison (What's the difference between...?)
      - Ensures questions are atomic (one concept per card)
      - Answers are concise but complete
      - Includes memory aids (mnemonics, analogies) where helpful
   2. **Spaced Repetition Skill** (.github/instructions/spaced-repetition.instructions.md)
      - Tags each card with difficulty: Easy / Medium / Hard
      - Groups cards into study sessions (10-15 cards each)
      - Suggests review schedule (Day 1, Day 3, Day 7, Day 14, Day 30)
      - Orders cards within a session for optimal learning (interleaving)

   Output format per card:
   ---
   **Q:** [question]
   **A:** [answer]
   **Difficulty:** [Easy/Medium/Hard]
   **Category:** [topic area]
   **Memory aid:** [optional mnemonic or analogy]
   ---

   Save the deck to ./output/flashcards.md

   I want a requirements markdown file for this agent.
   ```

3. Save to `flashcard-requirements.md`.

### 4.2 — Review and refine

```
Add a "quiz mode" where the flashcard deck is output as a self-test:
questions first (numbered), then answers at the bottom (also numbered)
so the user can cover the answers and test themselves.
```

## Step 5 — Use Copilot to Create the Agent and Skill Files

### 5.1 — Create the Question Design skill

1. Create `.github/instructions/question-design.instructions.md`
2. Ask Copilot:

   ```
   Using #file:flashcard-requirements.md generate the "Question Design
   Skill" instruction file. This should contain rules for creating
   effective flashcard questions across all Bloom's taxonomy levels.
   ```

### 5.2 — Create the Spaced Repetition skill

1. Create `.github/instructions/spaced-repetition.instructions.md`
2. Ask Copilot:

   ```
   Using #file:flashcard-requirements.md generate the "Spaced Repetition
   Skill" instruction file. This should contain the tagging, grouping,
   and scheduling rules.
   ```

### 5.3 — Create the agent

1. Create `.github/agents/flashcard.agent.md`
2. Ask Copilot:

   ```
   Using #file:flashcard-requirements.md generate a "Flashcard Agent"
   that references the question-design and spaced-repetition instruction
   files. Model: Claude Sonnet 4.5.
   Workflow: Parse → Generate → Organise.
   ```

### 5.4 — Review the agent and skills

| Element            | What to check                                              |
| ------------------ | ---------------------------------------------------------- |
| **Agent**          | References both skill files.                               |
| **Question types** | All 4 types (recall, conceptual, application, comparison). |
| **Card format**    | Q/A/Difficulty/Category/Memory aid structure.              |
| **Tools**          | Must be `["read", "edit"]`.                                |

## Step 6 — Test the Flashcard Agent

### 6.1 — Invoke the agent

1. Select **Flashcard** from the agent dropdown.
2. Provide a topic:

   ```
   Create a flashcard deck for: Kubernetes fundamentals

   Cover these topics:
   - Pods, Deployments, Services, Ingress
   - ConfigMaps and Secrets
   - Namespaces and RBAC
   - kubectl essential commands
   - Difference between StatefulSet and Deployment
   - Liveness vs readiness probes

   Generate 20 cards across all difficulty levels.
   ```

3. Watch the agent generate a complete flashcard deck!

### 6.2 — Generate from notes

```
Here are my rough study notes. Turn them into flashcards:

HTTP status codes:
- 200 OK, 201 Created, 204 No Content
- 301 Moved Permanently, 302 Found, 304 Not Modified
- 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found
- 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable

REST principles:
- Stateless, client-server, cacheable, uniform interface, layered system
- Resources identified by URIs
- CRUD maps to HTTP methods: POST, GET, PUT/PATCH, DELETE
```

## Step 7 — Refine and Iterate

### 7.1 — Add Anki export format

```
Create a new skill file: .github/instructions/anki-export.instructions.md
It should teach the agent how to output flashcards in Anki-compatible
format (tab-separated: front<TAB>back<TAB>tags) so users can import
directly into the Anki app.
```

### 7.2 — Add source material linking

```
Update the Flashcard Agent so that when the user provides a document file,
each flashcard includes a "Source" field referencing the section of the
document the question came from.
```

---

## Summary

| Artifact                                           | Location                  | Purpose                               |
| -------------------------------------------------- | ------------------------- | ------------------------------------- |
| `flashcard-requirements.md`                        | Repository root           | Describes all requirements.           |
| `flashcard.agent.md`                               | `.github/agents/`         | The main Flashcard agent.             |
| `question-design.instructions.md`                  | `.github/instructions/`   | Skill — question creation rules.      |
| `spaced-repetition.instructions.md`                | `.github/instructions/`   | Skill — difficulty tagging & scheduling. |
| `output/flashcards.md`                             | Created at runtime        | Final flashcard deck.                 |

### Key takeaways

1. **Skills are reusable** — the question design skill could enhance any educational agent.
2. **Instruction files** encode pedagogical best practices without bloating the agent.
3. **Learning agents** demonstrate immediate personal value for anyone studying.
4. **Iterate quickly** — all files are Markdown, edits take seconds.

---
