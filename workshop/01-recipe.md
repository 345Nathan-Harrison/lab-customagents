# Hands-on Lab: Build a Recipe Agent

## Overview

In this lab you will create a **GitHub Copilot Custom Agent** called the **Recipe Agent**. When a user provides a list of ingredients they have on hand, this agent creates a creative, well-structured recipe using only those ingredients plus common pantry staples.

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
   I want to build a GitHub Copilot custom agent called "Recipe Agent".
   Its purpose:
   - The user provides a comma-separated list of ingredients they have.
   - The agent creates a recipe that uses ONLY those ingredients plus
     common pantry staples (salt, pepper, oil, butter, garlic, onion,
     flour, sugar, eggs, milk).
   - The output includes:
     1. Recipe name (creative and appetising)
     2. Cuisine style
     3. Difficulty (Easy / Medium / Hard)
     4. Prep time and cook time
     5. Numbered ingredients list with quantities
     6. Numbered step-by-step instructions
     7. Chef's tips section
   - If fewer than 3 ingredients are provided, the agent should suggest
     additions.
   - Save the output to ./output/recipe.md

   I want a requirements markdown file for this agent.
   Include:
   - A short description of the agent
   - The target user
   - Functional and non-functional requirements
   - Example input and output
   ```

3. Save to `recipe-requirements.md`.

### 4.2 — Review and refine

```
Add dietary restriction awareness. The agent should ask once whether the
user has any dietary restrictions (vegetarian, vegan, gluten-free,
dairy-free, nut-free) and respect them when generating the recipe.
```

## Step 5 — Use Copilot to Create the Agent Definition File

### 5.1 — Create the file

1. Right-click `.github/agents/` → **New File** → **`recipe.agent.md`**

### 5.2 — Ask Copilot to scaffold the agent

   ```
   Using #file:recipe-requirements.md generate a "Recipe Agent"
   Model: Claude Sonnet 4.5.
   Use a 3-phase workflow: Gather → Create → Present.
   ```

### 5.3 — Review the generated agent

| Element             | What to check                                              |
| ------------------- | ---------------------------------------------------------- |
| **Frontmatter**     | `name`, `description`, `model`, `tools` present.           |
| **Workflow**        | Three phases: Gather, Create, Present.                     |
| **Pantry staples**  | Listed in agent instructions so it knows what's "free".    |
| **Dietary check**   | Agent asks about restrictions before generating.           |

> [!IMPORTANT]
> `tools` must be set to `["read", "edit"]`.

## Step 6 — Test the Recipe Agent

### 6.1 — Invoke the agent

1. Select **Recipe** from the agent dropdown.
2. Provide your ingredients:

   ```
   I have: chicken thighs, bell peppers, rice, soy sauce, ginger, and lime
   ```

3. Watch the agent generate a full recipe!

### 6.2 — Try more scenarios

```
I have: pasta, canned tomatoes, mozzarella, and basil
```

```
I have: sweet potatoes, black beans, and cumin. I'm vegan.
```

```
I only have eggs and cheese
```

## Step 7 — Refine and Iterate

### 7.1 — Add a scaling feature

```
Update the Recipe Agent so it asks how many servings are needed and adjusts all ingredient quantities accordingly. Default to 4 servings.
```

### 7.2 — Add a meal-prep variant

```
Update the agent so the user can say "meal prep" and it generates a version that stores well, with reheating instructions and how many days it keeps.
```

---

## Summary

| Artifact                  | Location           | Purpose                                          |
| ------------------------- | ------------------ | ------------------------------------------------ |
| `recipe-requirements.md`  | Repository root    | Describes what the Recipe Agent should do.       |
| `recipe.agent.md`         | `.github/agents/`  | The agent definition file that GitHub Copilot loads. |
| `output/state/`           | Created at runtime | State tracking files.                            |

### Key takeaways

1. **Custom agents are just Markdown files** — no servers, no APIs, no deployments.
2. **Creative agents are fun** — they demonstrate AI capability with tangible results.
3. **Start with requirements** — gives Copilot better context to scaffold.
4. **Iterate quickly** — agents are Markdown, edits take seconds.

---
