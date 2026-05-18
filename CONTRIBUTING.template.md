# Contributing — for design and product

For non-engineering contributors (typically UX and PM). You make design /
copy / layout changes by talking to **Claude Desktop's Code tab** in
plain English; Claude makes the actual code changes for you.

{{REVIEWER}} (the tech lead) reviews your PRs and merges to
`{{MAIN_BRANCH}}`.

## One-time setup (~20 min, ping {{REVIEWER}} if you get stuck)

Most of this is one-time tooling so your Mac can talk to GitHub. After
this you won't open Terminal again.

### 1. Install Claude Desktop

Download from [claude.com/download](https://claude.com/download).
Open the `.dmg` and drag the Claude icon to Applications. Sign in with
your Anthropic account.

> The Code tab needs a Pro plan. Ping {{REVIEWER}} if you don't have one.

### 2. Open Terminal

⌘ + space → type `terminal` → Enter.

### 3. Install Homebrew (if you don't already have it)

Check:

```
brew --version
```

If it says `command not found`, install:

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Enter your Mac password when asked. You won't see characters as you
type; that's normal. Takes 3–5 minutes.

### 4. Install GitHub CLI

Still in Terminal:

```
brew install gh
```

### 5. Sign in to GitHub

Still in Terminal:

```
gh auth login
```

Answer the prompts (use arrow keys to highlight, Enter to select):
`GitHub.com` → `HTTPS` → `Y` → `Login with a web browser`. Copy the code
Terminal shows, paste it into the browser tab that opens, click Authorize.

> Ping {{REVIEWER}} if you haven't been added to the {{GH_ORG}} org.

### 6. Clone the project

Still in Terminal. Paste this whole line and press Enter:

```
cd ~ && mkdir -p {{LOCAL_FOLDER}} && cd {{LOCAL_FOLDER}} && gh repo clone {{GH_REPO}}
```

This drops the project into a `{{LOCAL_FOLDER}}/` folder inside your home
directory. You can close Terminal now; you won't need it again.

### 7. Point Claude Desktop at the project

Open Claude Desktop → **Code** tab → **Select folder** → navigate to
**home → {{LOCAL_FOLDER}} → {{REPO_NAME}}** → Open.

> If any of the Terminal steps errors out, screenshot the Terminal
> window and DM {{REVIEWER}}. Don't try to fix it yourself.

---

## Every working session

### Starting your day

1. Open Claude Desktop → **Code** tab → **New session**.
2. Say *"Let's get started."*

Claude pulls the latest from `{{MAIN_BRANCH}}` into the `{{BRANCH}}`
branch so you're working off current code. Then it'll ask what you want
to do.

### Two ways to iterate

The project files live on your Mac in `~/{{LOCAL_FOLDER}}/{{REPO_NAME}}/`.
Claude does all the editing; you don't open a code editor. You see your
changes via your team's preview flow (see "Previewing changes" below) —
typically by refreshing the open browser tab, since files are local.

You have two paths. Pick whichever fits the task.

#### Path A: Direct in Claude Code (default)

Talk to Claude Code in plain English. You see changes via your team's
preview flow (see below).

1. *"Open a preview."* — Claude opens the page (per your preview flow).
2. Tell Claude what you want.
3. Reload to see the change.

**Best for:** copy tweaks, color swaps, spacing, single-element changes,
cross-surface changes — anything you can describe in a sentence. This is
what you'll use most of the time.

Example phrases:

- *"Tighten the header on the [your page] page."*
- *"Make the active sidebar pill teal."*
- *"Save this."* — commits your progress (a checkpoint, no PR yet).
- *"Undo that."* — reverts the last change.
- *"I'm done."* / *"Send to {{REVIEWER}}."* — pushes everything and opens
  a Pull Request (PR) for review.

### Previewing changes (Path A)

{{PREVIEW_FLOW}}

#### Path B: Visual iteration in Claude Design, then handoff

Use Claude Design's visual tools (drag knobs, alternates, comments) to
iterate, then hand off to Claude Code to apply.

**One-time setup.** In Claude Design, click **Import → From GitHub** and
select `{{GH_REPO}}`. When asked, say *"Import the whole repo with the
full folder structure preserved."* This avoids the import flattening
folders, which would break source awareness and rendering.

**Each session:**

1. Open Claude Design. Say *"Pull the latest from GitHub"* so you're
   working off current `{{MAIN_BRANCH}}`.
2. Iterate visually with Claude Design's tools.
3. Click **Share handoff to Claude Code** when you're happy.
4. Open Claude Code's **Code** tab. Paste the handoff (or tell Claude
   *"fetch this Claude Design URL: ..."*).
5. Claude Code applies the changes, commits to `{{BRANCH}}`, and opens a PR.

**Best for:** redesigning an existing page, designing a new component
from scratch, comparing multiple visual directions side-by-side,
anything that benefits from drag-knob iteration.

If you see "file not found," unstyled pages, or broken renders after
Claude Design's import, the folders got flattened. Re-import with the
preserved-structure phrase above.

### Ending your day (or finishing a batch)

- *"I'm done."* / *"Send to {{REVIEWER}}."* — Claude pushes
  `{{BRANCH}}` and opens a PR.
- *"Pause here."* — Claude saves your work to `{{BRANCH}}` without
  opening a PR.

{{REVIEWER}} reviews the PR and merges it onto your team's path to
production (could be direct to `{{MAIN_BRANCH}}`, or through a QA /
staging stage first — depends on the team). Your changes show up at the
live URL (<{{LIVE_URL}}>) once it's gone through.

---

## What Claude won't do

By design, not a bug. Ping {{REVIEWER}} if you need one of these:

- Push directly to `{{MAIN_BRANCH}}`
- Rewrite history or delete files
- Edit plumbing files (anything starting with `.`)
- Touch the data layer or business-logic files
- Touch test specs
- Edit engineering docs (`README.md`, `CONTRIBUTING.md`, `package.json`)

The full hard-blocked list lives in `.claude/settings.json` — see the
template's `settings.template.json`.

---

## Where things live

Fill in for your repo. Example structure:

- `src/` — the pages and components you'll be editing
- `design-system/` — colors, fonts, primitives
- `docs/` — documentation
- `.claude/` — Claude's instructions (don't touch)

---

## How the full workflow runs

1. Tell Claude what to change in the Code tab.
2. Claude edits and pushes to the `{{BRANCH}}` branch.
3. *"I'm done. Send to {{REVIEWER}}."* → Claude opens a Pull Request.
4. {{REVIEWER}} gets a Slack ping, reviews, merges if good.
5. Changes go live at <{{LIVE_URL}}> after merge (or after whatever
   stages your team's pipeline runs).
6. If revising: {{REVIEWER}} comments → fix in Claude Code →
   *"push that"* → the PR auto-updates.

You never touch git, branches, PRs, or `{{MAIN_BRANCH}}` directly. Your
inputs are: **what to change** and **when it's ready for {{REVIEWER}}**.
