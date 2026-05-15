# Team setup kit

Reusable templates for setting up a Claude-Desktop-driven contribution
workflow on any CourtReserve team repo. Copy `CONTRIBUTING.template.md`
and `SKILL.template.md` into your repo, find-replace the placeholders,
done.

This kit is generic. The filled-in versions for the Competitions team
(tournaments-front prototype) live at:

- `../CONTRIBUTING.md`
- `../.claude/skills/ux-edits/SKILL.md`

Treat those as the worked example.

## What this workflow is

Non-engineering teammates (typically design and product) work in Claude
Desktop's **Code** tab. Claude applies their changes directly to the
repo, commits to a working branch, and opens a PR to `main`. The team's
**tech lead** reviews and merges to `main`, then deploys.

> On the Competitions team, Zak (the lead) merges because he's acting as
> both PM and eng lead. On most teams the tech lead is the merger, and
> the PM + UX are the contributors going through this workflow.

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
| `{{REPO_NAME}}` | Just the repo name | `tournaments-front` |
| `{{GH_REPO}}` | `org/repo` | `TennisRungs/tournaments-front` |
| `{{GH_ORG}}` | GitHub org | `TennisRungs` |
| `{{PROJECT_DESC}}` | One-liner describing the codebase | `CourtReserve tournaments prototype` |
| `{{LIVE_URL}}` | Production URL (if any) | `https://crtournaments.netlify.app` |
| `{{REVIEWER}}` | First-name of the tech lead who merges to main | `Zak` |
| `{{BRANCH}}` | Contributors' working branch | `ux` |
| `{{MAIN_BRANCH}}` | Branch they merge into | `main` |
| `{{LOCAL_FOLDER}}` | Where to clone on disk (under `~/`) | `competitions` |
| `{{PREVIEW_FLOW}}` | How the UX teammate previews changes (multi-line, see below) | see "Preview flow" section |

## Preview flow

This is the most team-specific piece. Pick whichever applies and paste it
in everywhere the template says `{{PREVIEW_FLOW}}`:

**Static HTML, no build (Competitions / tournaments-front uses this):**

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
https://qa.courtreserve.com to preview.
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
structure. The Competitions example has them filled in for the
prototype's `mock-data/` files, `_redirects`, etc. Adapt to whatever's
sensitive in your codebase.

## Install steps (tech lead does these once)

1. Copy `CONTRIBUTING.template.md` → `CONTRIBUTING.md` at your repo root.
2. Copy `SKILL.template.md` → `.claude/skills/ux-edits/SKILL.md`.
3. Find-replace the placeholders in both files (VS Code's "Find in
   Files" or `sed` works).
4. Set up branch protection on `{{MAIN_BRANCH}}`:
   - Add `* @your-github-handle` to `.github/CODEOWNERS`.
   - Enable "Require a pull request before merging" + "Require review
     from Code Owners" in repo settings (or via `gh api`).
5. Create the `{{BRANCH}}` branch and push it to origin.
6. Add a `.claude/settings.json` to allow safe git commands and deny
   dangerous ones. (The Competitions example at
   `../.claude/settings.json` is a good starting point.)

Then point your design / PM contributors at `CONTRIBUTING.md`. It walks
them through their own one-time setup (~15 min, Terminal-based, no
hand-holding required). Be on Slack to answer questions if they get
stuck.

## What you DON'T need

- A monorepo or a build system. The skill works on any repo.
- A specific framework. The workflow logic is framework-agnostic.
- A paid Claude API key. Claude Desktop's Code tab uses the team's
  Anthropic plan.
