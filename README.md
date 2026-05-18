# PM/UX contrib kit

> **Define product changes by making them.** PMs ship requirements as
> code. Designers ship UI as code. Engineers ship the systems work only
> they can do. No spec docs, no ticket queues, no waiting on each other.

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
repo, commits to a working branch, and opens a PR. The team's **tech
lead** reviews the PR and merges it onto whatever branch their pipeline
expects — that might be `main` directly, or `qa` / `staging` /
`develop` if the team has multi-stage promotion. The contributor flow
is the same either way; only the tech lead's target changes.

The skill encodes:

- Workflow phrase → git action mapping (so the UX teammate can say
  "let's get started", "save this", "send to <reviewer>")
- Claude Design → Code handoff handling
- Safety rails (no force-push, no merge-to-main, protected files)
- Translation guidance for design language ("tighten", "make it pop")

## What you need

1. **A GitHub repo** your non-engineering contributors have push access to.
2. **A working branch** they push to (e.g. `ux`, `design`).
3. **A tech lead** with merge rights on whatever branch their PRs target
   (`main` for single-branch teams; `qa` / `staging` / `develop` for
   teams that promote through stages first).
4. **A preview flow** the contributors can use to see changes
   (file-open for static repos, `npm run dev` for build repos, a QA URL,
   Storybook, whatever fits your stack).
5. **Branch protection** on the contributors' merge target so only the
   tech lead can merge. Use `.github/CODEOWNERS` + a required-review rule.

## Placeholders to swap

Both templates use these placeholders. Find-replace once per file:

| Placeholder | Meaning | Example |
|---|---|---|
| `{{REPO_NAME}}` | Just the repo name | `my-app` |
| `{{GH_REPO}}` | `org/repo` | `acme/my-app` |
| `{{GH_ORG}}` | GitHub org | `acme` |
| `{{PROJECT_DESC}}` | One-liner describing the codebase | `Acme's customer-facing web app` |
| `{{LIVE_URL}}` | Production URL (if any) | `https://my-app.com` |
| `{{REVIEWER}}` | First-name of the tech lead who merges contributor PRs | `Alex` |
| `{{MAIN_BRANCH}}` | The branch the tech lead merges contributor PRs into. Depends on your team's pipeline — could be `main` for a single-branch flow, or `qa` / `staging` / `develop` for teams with multi-stage promotion. The contributor doesn't need to know what's downstream. | `main` or `qa` |

> **Branches are per-contributor, derived automatically.** The skill
> reads each contributor's `git config user.name` and creates a branch
> named after them (`"Jae Ko"` → `jae-ko`). You don't pre-create
> branches or pick a single shared `{{BRANCH}}` value. Multiple
> contributors (PM, UX, etc.) each get their own personal branch
> automatically. The skill creates branches on first session if they
> don't exist yet.
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
Push to your personal branch and the QA environment auto-deploys
(per-branch preview, if your CI is set up for it). Visit
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

### Step 0 — Get this kit

```
gh repo clone zparks-cr/PM-UX-contrib-kit ~/Desktop/PM-UX-contrib-kit
```

Or download as zip from the GitHub web view if you don't have `gh`.

### Step 1 — Decide your placeholder values before you find-replace

Pick concrete values for each of the placeholders in the table above.
The one that needs real thought:

- **`{{MAIN_BRANCH}}`** — where contributor PRs target. Often `main`,
  but for multi-stage teams it might be `qa` / `staging` / `develop`
  (whichever stage contributor changes land on first). You handle
  promotion downstream.

Contributor branches are derived per-person automatically — no choice
needed here.

### Step 2 — Copy the three template files into your repo

```
cd ~/your-repo
cp ~/Desktop/PM-UX-contrib-kit/CONTRIBUTING.template.md ./CONTRIBUTING.md
mkdir -p .claude/skills/ux-edits
cp ~/Desktop/PM-UX-contrib-kit/SKILL.template.md .claude/skills/ux-edits/SKILL.md
cp ~/Desktop/PM-UX-contrib-kit/settings.template.json .claude/settings.json
```

### Step 3 — Find-replace the placeholders

In VS Code: ⌘+Shift+F → toggle "replace" → run each one across the
three files. Or via `sed`:

```
sed -i '' 's|{{REPO_NAME}}|my-app|g' CONTRIBUTING.md .claude/skills/ux-edits/SKILL.md .claude/settings.json
sed -i '' 's|{{GH_REPO}}|acme/my-app|g' CONTRIBUTING.md .claude/skills/ux-edits/SKILL.md .claude/settings.json
# repeat for each placeholder in the table above
```

Also paste your `{{PREVIEW_FLOW}}` block (see "Preview flow" section
above) into the two `{{PREVIEW_FLOW}}` slots in CONTRIBUTING.md and
SKILL.md.

### Step 4 — Add `.github/CODEOWNERS`

```
mkdir -p .github
echo "* @your-github-handle" > .github/CODEOWNERS
```

### Step 5 — Enable branch protection on `{{MAIN_BRANCH}}`

In your repo's Settings → Branches (or via `gh api`):

- Require a pull request before merging
- Require review from Code Owners
- (Optional) Dismiss stale reviews + require conversation resolution

This is what enforces *"only the tech lead can merge"* on the GitHub side.

### Step 6 — Commit + push the kit files to `{{MAIN_BRANCH}}`

```
git add CONTRIBUTING.md .claude/ .github/CODEOWNERS
git commit -m "Add PM/UX contrib kit"
git push origin {{MAIN_BRANCH}}
```

If branch protection blocks this because you just enabled it, push via
`--admin` once or temporarily disable protection during setup.

### Step 7 (optional) — Set up Slack notifications

So you get pinged when a contributor opens a PR. Via GitHub's official
Slack app:

- **In a channel** (whole team sees PR pings):
  ```
  /github subscribe acme/my-app pulls
  ```
- **In your DM with the GitHub bot** (just you sees them):
  Open the **GitHub** app in Slack's sidebar → run the same command
  in the DM.

You'll get notified on PR opens, ready-for-review, closes, and merges.
Prerequisite: your Slack workspace admin needs to install the GitHub
app from the Slack App Directory if `/github` isn't recognized.

### Step 8 — Per-contributor onboarding

For each PM / designer joining the workflow:

1. Add them to your GitHub org with **write** access on the repo.
2. Point them at `CONTRIBUTING.md`. It walks them through their own
   one-time setup (~20 min, Terminal-based, no hand-holding required).
3. Be on Slack to answer questions if they get stuck on Homebrew, gh
   auth, or anything else.

## What you DON'T need

- A monorepo or a build system. The skill works on any repo.
- A specific framework. The workflow logic is framework-agnostic.
- A paid Claude API key. Claude Desktop's Code tab uses the team's
  Anthropic plan.
