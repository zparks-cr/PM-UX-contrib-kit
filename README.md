# PM/UX contrib kit

Reusable templates for setting up a Claude-Desktop-driven contribution
workflow on any team repo. Copy the three templates into your repo,
find-replace the placeholders, done.

**Files in this kit:**

- `CONTRIBUTING.template.md` → goes to your repo root as `CONTRIBUTING.md`
- `SKILL.template.md` → goes to `.claude/skills/ux-edits/SKILL.md`
- `settings.template.json` → goes to `.claude/settings.json` (the
  hard-block enforcement layer)


## What this workflow is

Non-engineering teammates (typically design and product) work in Claude
Desktop's **Code** tab. Claude applies their changes directly to the
repo, commits to a working branch, and opens a PR to `main`. The team's
**tech lead** reviews and merges to `main`, then deploys.

The skill encodes:

- Workflow phrase → git action mapping (so the UX teammate can say
  "let's get started", "save this", "send to <reviewer>")
- Claude Design → Code handoff handling
- Safety rails (no force-push, no merge-to-main, protected files)
- Translation guidance for design language ("tighten", "make it pop")

## What you need

1. **A GitHub repo** your non-engineering contributors have push access to.
2. **A working branch** they push to (e.g. `ux`, `design`, `staging`).
3. **A tech lead** with merge rights on `main` (or your default branch).
4. **A preview flow** the contributors can use to see changes
   (file-open for static repos, `npm run dev` for build repos, a QA URL,
   Storybook, whatever fits your stack).
5. **Branch protection** on `main` so only the tech lead can merge. Use
   `.github/CODEOWNERS` + a required-review rule.

## Placeholders to swap

Both templates use these placeholders. Find-replace once per file:

| Placeholder | Meaning | Example |
|---|---|---|
| `{{REPO_NAME}}` | Just the repo name | `my-app` |
| `{{GH_REPO}}` | `org/repo` | `acme/my-app` |
| `{{GH_ORG}}` | GitHub org | `acme` |
| `{{PROJECT_DESC}}` | One-liner describing the codebase | `Acme's customer-facing web app` |
| `{{LIVE_URL}}` | Production URL (if any) | `https://my-app.com` |
| `{{REVIEWER}}` | First-name of the tech lead who merges to main | `Alex` |
| `{{BRANCH}}` | Contributors' working branch | `ux` |
| `{{MAIN_BRANCH}}` | Branch they merge into | `main` |
| `{{LOCAL_FOLDER}}` | Where to clone on disk (under `~/`) | `code` |
| `{{PREVIEW_FLOW}}` | How the contributor previews changes (multi-line, see below) | see "Preview flow" section |

## Preview flow

This is the most team-specific piece. Pick whichever applies and paste it
in everywhere the template says `{{PREVIEW_FLOW}}`:

**Static HTML, no build:**

```
The prototype is static HTML. Open the file directly in the browser:
`open prototype/path/to/file.html`. After edits, refresh the tab (Cmd+R).
```

**Local dev server (most CR teams):**

```
Run `npm run dev` (or `dotnet run`, etc.) from the repo root. Visit
http://localhost:3000 (or whatever port your dev server uses). Hot-reload
picks up edits automatically.
```

**QA environment:**

```
Push to `{{BRANCH}}` and the QA environment auto-deploys. Visit
https://qa.yourteam.example to preview.
```

**Storybook:**

```
Run `npm run storybook` from the repo root. Visit
http://localhost:6006. Stories hot-reload on file save.
```

Pick one (or write your own). Substitute it into both template files.

## Design conventions

`SKILL.template.md` has a "Design conventions" section with `{{...}}`
placeholders for your team's color tokens, fonts, voice rules, and domain
constants. Fill in your team's actual values. If you have a design system
README, paste a short summary there or link out.

## Protected / allowed files

The template lists "allowed to edit freely" and "do not edit" buckets
with `{{...}}` placeholders. Fill these in based on your repo's
structure (typical protected paths: data fixtures, routing config,
test specs, CI config, etc.).

## Install steps (tech lead does these once)

1. Copy `CONTRIBUTING.template.md` → `CONTRIBUTING.md` at your repo root.
2. Copy `SKILL.template.md` → `.claude/skills/ux-edits/SKILL.md`.
3. Copy `settings.template.json` → `.claude/settings.json`.
4. Find-replace the placeholders in all three files (VS Code's "Find in
   Files" or `sed` works).
5. Set up branch protection on `{{MAIN_BRANCH}}`:
   - Add `* @your-github-handle` to `.github/CODEOWNERS`.
   - Enable "Require a pull request before merging" + "Require review
     from Code Owners" in repo settings (or via `gh api`).
6. Create the `{{BRANCH}}` branch and push it to origin.

Then point your design / PM contributors at `CONTRIBUTING.md`. It walks
them through their own one-time setup (~15 min, Terminal-based, no
hand-holding required). Be on Slack to answer questions if they get
stuck.

## What you DON'T need

- A monorepo or a build system. The skill works on any repo.
- A specific framework. The workflow logic is framework-agnostic.
- A paid Claude API key. Claude Desktop's Code tab uses the team's
  Anthropic plan.
