# Hands-on Lab: Build a Translator Agent

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Translator Agent**. When a user provides text and a target language, this agent translates the text while preserving tone, formality, and context — then provides alternatives and cultural notes.

### What you will learn

- How to set up a repository that supports custom GitHub Copilot agents.
- How to write a **requirements document** that describes what an agent should do.
- How to create an **`.agent.md`** file that defines the agent's personality, rules, and workflow.
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

Skim `.github/instructions/custom-agent.instructions.md`. Note the Agent Archetypes, Frontmatter, and State Files conventions.

## Step 4 — Use Copilot to Write the Requirements Document

### 4.1 — Ask Copilot to draft the requirements

1. Open **Copilot Chat** in **Plan** mode.
2. Type:

   ```
   I want to build a GitHub Copilot custom agent called "Translator Agent".
   Its purpose:
   - The user provides text in any language and specifies a target language.
   - The agent outputs:
     1. Detected source language
     2. Primary translation (natural-sounding, not literal)
     3. Literal translation (word-for-word, for learning)
     4. Formality variants (casual / neutral / formal)
     5. Cultural notes — idioms, phrases that don't translate directly
     6. Pronunciation guide (phonetic) for non-Latin scripts
   - The agent should handle:
     - Common languages: English, Spanish, French, German, Italian,
       Portuguese, Japanese, Korean, Mandarin, Arabic, Hindi, Dutch
     - Technical and business terminology
     - Slang and colloquialisms
   - Save the output to ./output/translation.md

   I want a requirements markdown file for this agent.
   Include:
   - A short description of the agent
   - The target user
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `translator-requirements.md`.

### 4.2 — Review and refine

```
Add a "context mode" where the user can specify the context (business email,
casual chat, academic paper, tourist phrase) and the agent adjusts
formality and vocabulary accordingly.
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`translator.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:translator-requirements.md generate a "Translator Agent"
   Model: Claude Sonnet 4.5.
   Use a 3-phase workflow: Detect → Translate → Enrich.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **Workflow**        | Three phases: Detect, Translate, Enrich.                   |
| **Variants**        | Casual, neutral, and formal translations.                  |
| **Cultural notes**  | Section for idioms and untranslatable concepts.            |

> [!IMPORTANT]
> `tools` must be set to `["read", "edit"]`.

## Step 6 — Test the Translator Agent

### 6.1 — Invoke the agent

1. Select **Translator** from the agent dropdown.
2. Provide text to translate:

   ```
   Translate to Japanese (business email context):
   "Thank you for your prompt response. We would be delighted to
   schedule a meeting at your earliest convenience to discuss the
   partnership proposal."
   ```

3. Watch the agent produce a rich translation!

### 6.2 — Try more scenarios

```
Translate to Spanish (casual chat):
"Hey, are you up for grabbing coffee later? My treat!"
```

```
Translate to French (academic paper):
"The results demonstrate a statistically significant correlation
between screen time and sleep quality in adolescents."
```

```
Translate to German:
"It's raining cats and dogs outside."
```

## Step 7 — Refine and Iterate

### 7.1 — Add batch translation

```
Update the Translator Agent so it can accept multiple phrases separated
by "---" and translate them all in one go, producing a numbered list.
```

### 7.2 — Add a "teach me" mode

```
Add a mode where the user says "teach me" and the agent breaks down the
translation word by word, explaining grammar rules and sentence structure.
```

---

## Summary

| Artifact                      | Location           | Purpose                                          |
| ----------------------------- | ------------------ | ------------------------------------------------ |
| `translator-requirements.md`  | Repository root    | Describes what the Translator Agent should do.   |
| `translator.agent.md`         | `.github/agents/`  | The agent definition file that GitHub Copilot loads. |
| `output/state/`               | Created at runtime | State tracking files.                            |

### Key takeaways

1. **Custom agents are just Markdown files** — no servers, no APIs, no deployments.
2. **Language agents** highlight AI's multilingual capabilities.
3. **Start with requirements** — gives Copilot better context to scaffold.
4. **Iterate quickly** — agents are Markdown, edits take seconds.

---
