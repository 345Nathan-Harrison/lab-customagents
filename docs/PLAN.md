# Workshop Customisation Plan

## How Mark's Version Delivers Content

The site is a **static GitHub Pages site** with a clever content-fetching trick:

```
GitHub Repo
├── docs/          ← The website (HTML/CSS/JS) — deployed to GitHub Pages
│   ├── index.html          Landing page (hero + lab card list)
│   ├── step.html           Single-page step viewer (sidebar nav + content area)
│   ├── 404.html            Error page
│   ├── css/                Stylesheets
│   └── js/
│       ├── stepcfg.js      Step definitions + REPO_OWNER / REPO_NAME
│       ├── step.js         Fetches markdown at runtime, renders via marked.js
│       └── theme-toggle.js Light/dark mode
│
├── workshop/      ← The actual lab content (markdown + images)
│   ├── 00-overview.md
│   ├── 01-poet.md
│   ├── ...
│   └── images/
│
└── .github/workflows/deploy.yml  ← Copies docs/ → _site/, deploys to Pages
```

### The key mechanism (step.js)

1. `stepcfg.js` exports `REPO_OWNER` and `REPO_NAME` — currently **`markharrison`** / **`lab-customagents`**.
2. `step.js` builds the content URL:
   - **Local dev** (`localhost` / `file://`): fetches from `../workshop/<file>.md`
   - **Production**: fetches from `https://raw.githubusercontent.com/{OWNER}/{REPO}/main/workshop/<file>.md`
3. The markdown is parsed client-side with **marked.js**, mermaid diagrams are rendered, images are rebased to the raw GitHub URL, and GitHub-flavour admonitions (`[!NOTE]`, `[!TIP]`) are transformed.
4. The **deploy.yml** workflow only copies `docs/` to `_site/` — the `workshop/` folder is **never deployed**. Content is always fetched at runtime from the GitHub raw API.

### What this means for you

- The `workshop/*.md` files are the **only content you need to change** — the website framework can stay as-is.
- `stepcfg.js` must be updated to point to **your** repo owner/name.
- `stepcfg.js` also defines the step list (sidebar nav + ordering) — update this when you change labs.
- `index.html` has the lab cards hardcoded — must be updated to match your new labs.

---

## What You Need To Do

### 1. Fix `stepcfg.js` — Point to your repo

```js
// Change FROM:
export const REPO_OWNER = "markharrison";
export const REPO_NAME = "lab-customagents";

// Change TO:
export const REPO_OWNER = "345Nathan-Harrison";
export const REPO_NAME = "lab-customagents";
```

Without this, the live site fetches markdown from Mark's repo, not yours.

### 2. Replace the workshop content

Replace the 4 labs (01–04) with your own unique examples. See the next section for ideas.

### 3. Update `stepcfg.js` step definitions

Update the `steps` array to match your new lab filenames and titles.

### 4. Update `index.html` lab cards

The hero section and part cards are hardcoded HTML — update titles, descriptions, and `href` links to match your new steps.

### 5. Update `05-complete.md`

Personalise the completion page (your name, your links, your feedback contact).

### 6. Verify `deploy.yml` exists in your repo

You already have it. Confirm GitHub Pages is enabled (Settings → Pages → Source: GitHub Actions).

---

## New Workshop Ideas (Different From Mark's)

The teaching structure stays the same — each lab progressively introduces a concept:

| # | Concept Taught            | Mark's Example        | Your New Example (Proposal)         |
|---|---------------------------|-----------------------|-------------------------------------|
| 01 | Single Agent basics      | Poet Agent (limericks)| **Recipe Agent** — give it ingredients, it creates a recipe with method + timing |
| 02 | Orchestrator + Subagents | Funny Agent (jokes → HTML) | **Travel Agent** — Orchestrator delegates to a "Planner" subagent (builds itinerary) then a "Designer" subagent (creates a styled HTML travel card) |
| 03 | Skills                   | Quiz Agent (quiz → PPT/HTML slides) | **Flashcard Agent** — generates study flashcards on a topic, uses skills to output as either HTML flip-cards or a printable PDF-ready page |
| 04 | MCP Servers              | Shopper Agent (grocery price comparison) | **Weather Agent** — uses Playwright MCP to scrape weather forecasts from multiple sites and generates a comparison report with a 5-day outlook |

### Alternative ideas if the above don't suit

| Concept | Alternative 1 | Alternative 2 |
|---------|--------------|---------------|
| Single Agent | **Motivator Agent** — daily motivational quote + ASCII art | **Translator Agent** — translates text and explains cultural nuances |
| Orchestrator | **Blog Agent** — Writer subagent drafts post, Editor subagent reviews & formats | **Presentation Agent** — Research subagent gathers info, Slide subagent builds deck |
| Skills | **Email Agent** — drafts professional emails using tone/format skills | **CV Agent** — generates a CV/resume using layout skills |
| MCP | **News Agent** — scrapes headlines from multiple news sites | **Stock Agent** — pulls live stock data and generates a summary dashboard |

---

## File Checklist

| File | Action | Priority |
|------|--------|----------|
| `docs/js/stepcfg.js` | Update `REPO_OWNER`, `REPO_NAME`, and `steps` array | **Must do first** |
| `docs/index.html` | Update lab card titles, descriptions, step count, duration | Must do |
| `workshop/00-overview.md` | Update checklist, clone URL, intro text | Must do |
| `workshop/01-*.md` | Write new Lab 1 content | Must do |
| `workshop/02-*.md` | Write new Lab 2 content | Must do |
| `workshop/03-*.md` | Write new Lab 3 content | Must do |
| `workshop/04-*.md` | Write new Lab 4 content | Must do |
| `workshop/05-complete.md` | Update with your name/links | Must do |
| `workshop/images/` | Replace screenshots with your own | Must do |
| `docs/css/styles.css` | Already customised (Pyrenees background) | Done |
| `docs/css/step.css` | Already customised (Pyrenees background) | Done |
| `.github/workflows/deploy.yml` | Verify it exists and Pages is enabled | Verify |

---

## Suggested Order of Work

1. **Fix `stepcfg.js`** — point to your repo so the site works
2. **Pick your 4 lab themes** — decide on the replacement examples
3. **Write Lab 01** — get one end-to-end lab working first
4. **Update `index.html`** and `00-overview.md` to match
5. **Write Labs 02–04** one at a time, testing each
6. **Take fresh screenshots** for `workshop/images/`
7. **Update `05-complete.md`** with your details
8. **Push and verify** the live GitHub Pages site
