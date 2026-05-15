---
name: ux-edits
description: |
  Auto-loaded when working on the {{PROJECT_DESC}} codebase.
  Use for any UX, design, copy, color, layout, spacing, or visual change.
  Also use when the user asks "what do I do", "where do I start", "what's
  next", or hands off a design from claude.ai/design to be applied to the
  real codebase. This skill encodes the git workflow, design conventions,
  and the "do not touch" list for this repo.
---

# UX edits — {{REPO_NAME}}

You are helping the user iterate on the {{PROJECT_DESC}}. Changes will be
described in design-oriented language ("tighten the header", "make it
pop", "use the green from the sidebar"). Execute them directly in the
code.

Common workflow:

1. Design exploration may happen in `claude.ai/design` (web).
2. The handoff arrives in this session as pasted markup, a description,
   a screenshot, or a Claude Design "share to Code" handoff.
3. You apply it to the real codebase, commit, push to `{{BRANCH}}`.
4. The user previews their changes (see "Previewing changes locally"
   below). {{REVIEWER}} (the team's tech lead) reviews the PR and merges
   to `{{MAIN_BRANCH}}` on their own cadence. Deploy timing is up to
   them.

---

## On every session — orient first

When a fresh Claude Code session opens in this repo, or when the user
asks "what do I do now?" / "where do I start?" / "what's next?":

1. **Check git state.** Run `git status` and `git branch --show-current`.
2. **Make sure you're on `{{BRANCH}}`.** If not, switch: `git checkout
   {{BRANCH}}`. If there are uncommitted changes on the wrong branch,
   confirm before stashing or discarding them.
3. **Pull latest from origin/{{BRANCH}}** so you're not editing stale code.
4. **Report state in one short sentence** and prompt for the next change.

---

## When the user requests a change

1. **Confirm understanding** in one short sentence. Visual descriptions
   need restating as concrete edits before applying.
2. **Make the edit** using the `Edit` tool. Prefer the smallest surgical change.
3. **Show the diff in plain English** before committing. Don't paste raw
   diff output. Describe what changed.
4. **Sanity-check the edit before committing.** If the edit touched HTML,
   JS, or CSS in any allowed-to-edit path:
   - Grep for obvious breakage (unclosed tags, unmatched braces, broken
     script blocks, missing mount targets).
   - If anything looks suspect, open the file in the preview (per
     "Previewing changes locally" below) or invoke `webapp-testing` for
     a quick load check.
   - **Don't commit broken code.** A broken push to `{{BRANCH}}` wastes
     the user's time when they open the preview.
5. **Commit + push to `{{BRANCH}}`** with a clear, descriptive message:
   ```
   git add -A
   git commit -m "ui: <short description>"
   git push origin {{BRANCH}}
   ```
6. **Report how to see it.** See "Previewing changes locally" below.
7. **When the user says "I'm done"** / "ship this" / "ready to review":
   - Push any pending commits to `{{BRANCH}}`.
   - Open a pull request:
     ```
     gh pr create --base {{MAIN_BRANCH}} --head {{BRANCH}} --title "<short summary>" --body "<bulleted change list>"
     ```
   - **Do not merge.** Only {{REVIEWER}} can merge to `{{MAIN_BRANCH}}`.
     Explain briefly: *"PR is up. {{REVIEWER}} gets notified and reviews.
     They'll merge and deploy on their own schedule."*

---

## Intent mapping — workflow phrases → actions

Common natural-language phrases map to specific actions automatically.
**Don't ask permission on these mappings. Execute and report back in one
short sentence.** Only ask if something is ambiguous or destructive.

### Starting a session — from scratch

- *"Let's get started"* / *"Let's begin"* / *"Starting a new session"* /
  *"Ready to work"* / *"I'm back"* → Pull the most recent code:
  ```
  git fetch origin
  git checkout {{BRANCH}}
  git pull origin {{BRANCH}}
  ```
  Then check if `{{MAIN_BRANCH}}` has moved ahead of `{{BRANCH}}`.
  If so, merge `{{MAIN_BRANCH}}` into `{{BRANCH}}`:
  ```
  git merge origin/{{MAIN_BRANCH}} --no-edit
  ```
  Report in one sentence: *"You're on `{{BRANCH}}`, up to date with
  everything {{REVIEWER}} has merged. What do you want to change?"*

- *"What did I work on last?"* / *"What did we change last time?"* →
  `git log --oneline {{BRANCH}} ^{{MAIN_BRANCH}} -10` and summarize.

### Starting a session — from a Claude Design handoff

The user designs in `claude.ai/design` (web) and clicks **"Share handoff
to Claude Code"**. The handoff arrives as pasted markup, a screenshot,
a structured handoff message, or a description with assets.

**Recognize the entry** by any of these signals:

- A handoff preamble identifying the source (Claude Design)
- Pasted HTML/CSS markup with design-y structure
- A pasted screenshot + brief description
- Phrases like *"Here's what I sketched"* / *"Here's my Claude Design
  mockup"* / *"Match this design"* / *"Build what I made in Claude Design"*

**How to handle, every time:**

1. **Orient the repo first** if not already done this session.
2. **Treat the handoff as a visual reference, not literal code to drop in.**
3. **Read the structural intent.** New section? Restyled card? Redesigned
   modal? Layout overhaul of an existing surface?
4. **Find the closest existing element** that maps to it. Don't add
   brand-new components if an existing one can be tweaked.
5. **Translate tokens to ours.** (See "Design conventions" below.)
6. **Restate the plan in one short sentence** before editing.
7. **Apply and commit** per the normal flow.

**If the handoff is just a screenshot with no markup**, ask one
clarifying question: *"Want me to match exact spacing/colors, or take the
structural direction and use our tokens?"*

**If the handoff describes a brand-new component**, flag it: *"This
looks like a new component pattern. Want me to build it inside the
existing surface, or as a reusable piece in our design system?"*

**If the URL pasted isn't `claude.ai/design`** (e.g., Figma share link,
Dribbble shot, screenshot URL): treat the visual as reference only. The
token translation guidance above doesn't apply (you don't know their
design system). Ask one clarifying question: *"What's the takeaway from
this? Layout structure, specific colors, a font treatment? I'll extract
that piece and apply our tokens."* Don't try to import outside tokens.

**If the user reports Claude Design rendering issues** (e.g., *"the
page looks unstyled"*, *"file not found"*, *"nothing's loading"*, or
shows a screenshot with broken styles after a Claude Design GitHub
import):

- **Likely cause:** Claude Design flattened the folder hierarchy on
  GitHub import. HTML/CSS/JS that uses relative paths to find sibling
  folders (e.g., `../../design-system/...`) now points nowhere.
- **Fix:** suggest the user re-import with folder structure preserved.
  The exact phrase that works: *"Import the whole repo with the full
  folder structure preserved."*
- **If she's also asking how to refresh** Claude Design between sessions:
  *"Pull the latest from GitHub"* gets her current `{{MAIN_BRANCH}}`.

### During a session

- *"Save this"* / *"Save what we have"* → commit + push to `{{BRANCH}}`.
  Don't open a PR yet.
- *"Undo that"* / *"Revert that change"* / *"Go back"* / *"Never mind"* →
  if uncommitted, `git checkout -- <file>`. If already committed and
  pushed, create a revert commit and push. Always confirm scope first.
- *"Show me what's changed"* / *"What did we change?"* / *"Diff this"* →
  run `git diff` and summarize each hunk in plain English.
- *"Try a different approach"* → revert the current attempt, then make
  the new attempt.
- *"Pause here"* / *"Done for the day"* → commit + push everything
  pending to `{{BRANCH}}`. **Don't** open a PR.
- *"Scrap everything"* / *"Start over"* / *"Forget all that"* / *"Trash
  this"* → **Always confirm scope first.** Ask: *"To make sure I
  understand, do you want to undo:*
  - *just the last change (one edit), or*
  - *the last commit, or*
  - *today's work (since the last session start), or*
  - *everything on `{{BRANCH}}` that hasn't been merged to `{{MAIN_BRANCH}}` yet?"*

  Default to the **smallest** scope if the user is vague. Never reset to
  `{{MAIN_BRANCH}}` (that wipes unmerged work). For "everything unmerged"
  scope, push back: *"That would wipe everything on `{{BRANCH}}`. Are
  you sure? If so, ping {{REVIEWER}} to do it manually."*
- *"Bring back the X we removed"* / *"Restore that section we deleted"*
  → Find the removal in history:
  ```
  git log --oneline --all -- <relevant-file>
  git show <commit-before-removal>:<file>
  ```
  Extract the relevant chunk and **splice it back into the current
  file**. Don't `git revert` the removal commit blindly — that commit
  may have other changes the user wants to keep.

### Finishing a session

- *"I'm finished"* / *"I'm done"* / *"Send to {{REVIEWER}}"* / *"Ready
  for review"* / *"Ship it"* →
  1. Push pending commits to `{{BRANCH}}`.
  2. Open a PR:
     ```
     gh pr create --base {{MAIN_BRANCH}} --head {{BRANCH}} \
       --title "<short summary of the batch>" \
       --body "<bulleted list of changes from this session>"
     ```
     Pull body bullets from `git log origin/{{MAIN_BRANCH}}..{{BRANCH}} --oneline`.
  3. **Don't merge.** Report: *"Done. Sent to {{REVIEWER}} as PR #N.
     They'll merge and deploy on their own schedule."*
  4. If a PR already exists from a prior session, new pushes auto-update
     it. Don't open a duplicate.

### Previewing changes locally

- *"Open a preview"* / *"Show me the page"* / *"Let me see"* / *"Preview
  this"* → use this team's preview flow:

{{PREVIEW_FLOW}}

  After more edits in the same session, the user just refreshes the open
  tab (or the dev server hot-reloads). Don't re-trigger the preview
  unless explicitly asked.

### When the user wants to add something new (not edit existing)

If the request is to ADD a section, component, or page rather than tweak
an existing one:

1. **Check for similar existing first.** Grep the codebase for the
   pattern (a card, a modal, a banner). If something close already
   exists, ask: *"We have a similar card on [page]. Want me to clone-
   and-modify that, or add a totally new one?"*
2. **Ask which surface/path it belongs to** if not specified.
3. **Apply per normal flow** — edit, show diff, sanity-check, commit.

### Status checks

- *"Where am I?"* / *"What's my status?"* → branch + clean/dirty +
  whether ahead/behind origin.
- *"Has {{REVIEWER}} seen my PR?"* / *"Did they merge yet?"* →
  `gh pr view --json state,reviews,mergedAt` and translate to plain English.
- *"What's on the live site right now?"* → `gh pr list --state merged
  --limit 3` to see recent merges; the live site reflects `{{MAIN_BRANCH}}`.

### Edge cases worth handling gracefully

- **A change is made but the user never says "I'm done."** That's fine.
  The work is safe on `{{BRANCH}}`. Don't auto-open a PR. Wait.
- **User says "I'm done" but the diff is empty.** Don't open a PR for
  nothing. Report: *"Nothing's changed since the last push. What did you
  want to send?"*
- **User says "delete this page" or "remove this section".** Clarify
  first. Default to commenting out, not deleting.
- **User says "save" while on the wrong branch.** Switch to `{{BRANCH}}`
  first, then commit. Report what happened.
- **User asks to undo something already merged to `{{MAIN_BRANCH}}`.**
  Clarify: *"That's already on the live site. Want me to propose a revert
  PR for {{REVIEWER}} to approve?"*

---

## Design conventions — never deviate

Fill in your team's actual conventions. The structure below is a starting
point; replace each value with your design system's real tokens, fonts,
and rules.

- **Color tokens** — only use CSS variables from `{{DESIGN_SYSTEM_PATH}}`.
  Never hard-code a hex. If a named color doesn't exist as a token, push
  back: *"That's not in our token set. Closest is X."*
- **Typography** — display font for hero/page titles, body font for
  everything else. Sizes via tokens only. Never set raw px font sizes.
- **Voice** — fill in your team's voice rules.
- **Domain constants** — fill in any never-change numbers (capacity
  defaults, business-logic constants, format conventions).

## Designer vocabulary — common translations

- *"Make it pop"* → bump font-weight one notch, raise contrast against
  bg, or add an elevation shadow. Offer two options.
- *"Tighten"* → reduce vertical padding/margin. One step down on your
  spacing scale.
- *"Loosen"* / *"breathing room"* → one step up the spacing scale.
- *"Make it match my design"* → pasted markup or screenshot from
  Claude Design. Integrate structure but preserve our tokens.
- *"Use the [color name from sidebar/header/etc]"* → translate to the
  corresponding design token.
- *"Make this title bigger"* → bump one step on the type scale.
- *"Center it"* → check the parent's display + alignment first; offer
  `text-align: center` for inline or `justify-content: center` /
  `margin: auto` for block contexts.

---

## Files Claude is allowed to edit freely

Fill in for your repo. Example:

- Anything in `src/` (or your equivalent)
- `design-system/` styling files (if extending tokens, push back first)
- `README.md` (only if explicitly asked)

## Files Claude must NOT edit unless the user explicitly says so

Fill in for your repo. Example:

- Data files / fixtures (changes break flows)
- Routing config
- `.gitignore`, `.npmrc`, `package.json` — repo plumbing
- `.github/CODEOWNERS` — branch protection
- `.claude/skills/`, `.claude/settings.json` — meta-config
- Test specs

If one of these is requested, explicitly confirm:
*"That file is one of the protected ones. Are you sure? Mind explaining
what you're trying to achieve? There may be a safer path."*

## Commands Claude must NOT run, ever

- `git push origin {{MAIN_BRANCH}}` — only {{REVIEWER}} merges to main
- `git push --force` or `git push -f` anything — risk of losing history
- `git reset --hard` anything — risk of losing uncommitted work
- `git branch -D` anything — risk of deleting work
- `rm -rf` anything — risk of losing files
- `gh pr merge` — only {{REVIEWER}} merges
- Anything that touches `.npmrc` (credential file)

If one of these is requested, refuse and explain:
*"I'm not allowed to do that from this skill. {{REVIEWER}} owns merges
to `{{MAIN_BRANCH}}`. Your changes go to `{{BRANCH}}`, then they review."*

## What's safe to do without asking

- `git status`, `git diff`, `git log`, `git branch` — read-only inspection
- `git checkout {{BRANCH}}` — switch to the working branch
- `git pull origin {{BRANCH}}` — get latest
- `git add`, `git commit`, `git push origin {{BRANCH}}`
- `gh pr create --base {{MAIN_BRANCH}} --head {{BRANCH}}` — open a PR
- `gh pr view`, `gh pr list` — read PR status
- Editing files in the allowed list above

---

## If something looks broken

Fill in for your stack. Examples:

- For build-based stacks: run the dev server and check the terminal for
  errors. If it doesn't compile, the error message points at the file.
- For static stacks: open the browser console (DevTools) to see runtime
  errors.
- For test failures: run your team's test suite and surface the relevant
  failure.

## Where things live (map for quick lookups)

Fill in the canonical paths/folders the user might ask about. Use this
map when they say *"where's the X page?"* / *"what file controls the
sidebar?"* / *"where do I find the colors?"*.

Example structure (replace with your team's actual paths):

- `src/pages/<feature>/` — the main user-facing surfaces
- `src/components/` — shared UI primitives
- `design-system/` — tokens, fonts, base styles
- `tests/` — test specs (protected)
- `mock-data/` or `fixtures/` — sample data (protected)

If the user asks for something not in this map, grep first and report
back rather than guessing.

## Cheat sheet (paste back when asked)

> Welcome. You're working on the {{REPO_NAME}} repo.
>
> **What I can do:** any visual / design / copy change. Just describe it
> in your own words ("tighten the header", "use the green from the
> sidebar", "make this title bigger").
>
> **What I won't do:** push to `{{MAIN_BRANCH}}`, force-push, delete
> files, or edit data/config files. Your changes go to a branch called
> `{{BRANCH}}`. {{REVIEWER}} reviews and merges.
>
> **How to see your changes:** see the preview steps in `CONTRIBUTING.md`.
> To see them on the live URL, open a PR; {{REVIEWER}} reviews and
> deploys on their own schedule.
>
> Ready when you are.
