# Contributing — for design and product

For non-engineering contributors (typically UX and PM). You make design /
copy / layout changes by talking to **Claude Desktop's Code tab** in
plain English; Claude makes the actual code changes for you.

{{REVIEWER}} (the tech lead) reviews your PRs and merges to
`{{MAIN_BRANCH}}`.

## One-time setup (~15 min, ping {{REVIEWER}} if you get stuck)

You already have Claude Desktop installed. The next few steps happen in
Terminal so your Mac can talk to GitHub. After this you won't open
Terminal again.

### 1. Open Terminal

⌘ + space → type `terminal` → Enter.

### 2. Install Homebrew (if you don't already have it)

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

### 3. Install GitHub CLI

Still in Terminal:

```
brew install gh
```

### 4. Sign in to GitHub

Still in Terminal:

```
gh auth login
```

Answer the prompts (use arrow keys to highlight, Enter to select):
`GitHub.com` → `HTTPS` → `Y` → `Login with a web browser`. Copy the code
Terminal shows, paste it into the browser tab that opens, click Authorize.

> Ping {{REVIEWER}} if you haven't been added to the {{GH_ORG}} org.

### 5. Clone the project

Still in Terminal. Paste this whole line and press Enter:

```
cd ~ && mkdir -p {{LOCAL_FOLDER}} && cd {{LOCAL_FOLDER}} && gh repo clone {{GH_REPO}}
```

This drops the project into a `{{LOCAL_FOLDER}}/` folder inside your home
directory. You can close Terminal now; you won't need it again.

### 6. Point Claude Desktop at the project

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

### Working

Tell Claude what you want, in plain English. Examples:

- *"Tighten the header on the [your page] page."*
- *"Make the active sidebar pill teal."*
- *"Match my Claude Design."* — see **Coming from Claude Design** below.
- *"Open a preview."* — see preview flow below.
- *"Save this."* — commits your progress so far (a checkpoint, no PR yet).
- *"Undo that."* — reverts the last change.
- *"I'm done."* / *"Send to {{REVIEWER}}."* — pushes everything and opens
  a Pull Request (PR) for review.

### Previewing changes

{{PREVIEW_FLOW}}

### Coming from Claude Design

Designs you sketched in [claude.ai/design](https://claude.ai/design) can
come into your Code session two ways. Both work:

- **Easiest:** click **Share handoff to Claude Code** in claude.ai/design.
  The design lands in your session as context.
- **Or:** paste the Claude Design URL into the chat. Claude fetches the
  design and applies it.

Either way, Claude turns it into real edits in the codebase.

### Ending your day (or finishing a batch)

- *"I'm done."* / *"Send to {{REVIEWER}}."* — Claude pushes
  `{{BRANCH}}` and opens a PR.
- *"Pause here."* — Claude saves your work to `{{BRANCH}}` without
  opening a PR.

{{REVIEWER}} reviews the PR and deploys to the live URL
(<{{LIVE_URL}}>) on their own schedule.

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
