# PRD — playwright-learning

> Lightweight PRD for a learning sandbox. Not a production deliverable —
> this repo exists to execute 3 pedagogical exercises from the Kortex
> playbook on browser automation, calibrate the workflows empirically,
> and harvest gotchas back to the playbook.

## What this is

Personal sandbox to learn **Playwright + Claude Code workflows** by
running the 3 pedagogical exercises defined in §11 of the upstream
playbook (`browser-automation-playwright.md`). The repo is throwaway:
when the 3 exercises are done, archive the registry entry in Kortex
and (optionally) delete the local clone. The GitHub repo stays as
public portfolio of the learning.

## Upstream reference

- **Playbook source:** [browser-automation-playwright.md §11](https://github.com/cposada23/AI_knowledge/blob/main/wiki/playbooks/claude-code-workflows/browser-automation-playwright.md) — 3 mini-PRDs of pedagogical exercises.
- **Playground convention:** [kortex-lab/playground/README.md](https://github.com/cposada23/AI_knowledge/blob/main/projects/kortex-lab/playground/README.md) (Kortex repo).
- **Source of truth for execution:** [docs/learning-progress.md](learning-progress.md) — step-by-step detailed checkboxes + verification + insights.

## Goals

1. Calibrate empirically how long the QA-loop / scraping / storage-state
   workflows take with Claude Code generating Playwright TypeScript.
2. Identify gotchas not documented in the playbook §F (Gotchas comunes).
3. Validate the generic setup §11.0 works end-to-end (no missing steps,
   no version pinning issues) before recommending it to others.

## Non-goals

- Build a production framework or library.
- Run automated tests against any commercial site (only public practice
  sites + GitHub.com used, per playbook §11.0).
- Maintain this repo long-term — it gets archived after the 3 exercises.

## Scope: 3 exercises

### Exercise 1 — §11.1 QA Loop (Form Validator)

**Target:** [https://practice.expandtesting.com/notes/app/register](https://practice.expandtesting.com/notes/app/register)
**Output:** `scripts/qa-form.ts` + `bugs.md` + `screenshots/qa-form/`
**Demonstrates:** semantic selectors, edge case validation, fix-loop pattern.
**Success criterion:** `bugs.md` ends empty after ≤3 fix iterations.

### Exercise 2 — §11.2 Scraping with Learning Loop

**Target:** [https://github.com/trending](https://github.com/trending)
**Output:** `scripts/scrape-trending.ts` + `data/trending-YYYY-MM-DD.csv`
**Demonstrates:** initial scrape → schema discovery → refine → CSV output.
**Success criterion:** CSV with ≥10 rows, schema captures repo name + author + stars + language.

### Exercise 3 — §11.3 Storage State (Authenticated Session)

**Target:** GitHub.com authenticated session (list private repos via UI)
**Output:** `scripts/login-and-dump-state.ts` + `scripts/list-private-repos.ts` + `playwright/.auth/github.json` (gitignored)
**Demonstrates:** persistent auth pattern (storage state JSON), reuse across runs without re-login.
**Success criterion:** second run consumes `.auth/github.json`, lists ≥1 private repo without browser prompt.

## Tech stack

- TypeScript strict
- Node ≥20 (per playbook §3.1)
- pnpm
- `@playwright/cli` (scripts) + `@playwright/test` (assertions in QA loop) + `playwright` (programmatic)
- Chromium browser via `pnpm exec playwright install chromium`

See [docs/learning-progress.md](learning-progress.md) §11.0.3 for the
full install step.

## Status

_(See [docs/learning-progress.md](learning-progress.md) "Current step pointer" for the live status.
This Status block is a high-level mirror — update both together.)_

- [x] Phase A done (Kortex bookkeeping for playground convention)
- [x] Phase B done (gh repo created + cloned into Kortex playground/)
- [x] Phase C done (this PRD + learning-progress.md + README + scaffold committed at `7e0f103`/`07f56d0`)
- [ ] Phase D pending (§11.0 setup base — 6 steps) ← **NEXT**
- [ ] Phase E pending (§11.1 QA Loop exercise — 7 sub-steps)
- [ ] Future: §11.2 Scraping exercise
- [ ] Future: §11.3 Storage state exercise
- [ ] Future: archive — promote insights to playbook §F + mark registry archived

## Closure criteria

The repo is "done" when:

1. The 3 exercises run cleanly (3 final scripts + 3 working outputs).
2. Insights observed during execution have been promoted to the
   upstream playbook §F (Gotchas comunes) — at least 1 insight added
   if anything tripped up during execution.
3. [docs/learning-progress.md](learning-progress.md) has all checkboxes
   marked, output sections populated, insights captured.
4. Registry entry in Kortex `playground/registry.md` is moved from
   Active → Archived with `closed:` date and `insights promoted:` paths.

If exercises 2 or 3 are skipped (e.g. cost of opportunity), document
the skip explicitly in Status above, mark archived with reason, and
still promote any insights from exercise 1 to the playbook.
