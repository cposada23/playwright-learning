# PRD — playwright-learning

> Lightweight PRD for a learning sandbox. Not a production deliverable —
> this repo exists to execute 3 pedagogical exercises with Playwright
> + Claude Code, calibrate the workflows empirically, and harvest
> gotchas while doing so.

## What this is

Personal sandbox to learn **Playwright + Claude Code workflows** by
running 3 self-contained pedagogical exercises (QA loop, scraping,
storage state). The repo is throwaway: when the 3 exercises are done,
keep what's worth keeping (the public GitHub repo as a portfolio of
the learning) and move on.

## Goals

1. Calibrate empirically how long the QA loop / scraping /
   storage-state workflows take when Claude Code generates the
   Playwright TypeScript.
2. Identify gotchas worth recording — version mismatches, brittle
   selectors, timeouts that need tuning, surprising costs.
3. Validate the generic setup (TypeScript + Playwright + tsx + ESM)
   works end-to-end on a fresh repo with no missing steps.

## Non-goals

- Build a production framework or library.
- Run automated scripts against any commercial site (only public
  practice sites + GitHub.com are used here).
- Maintain this repo long-term — it stays as portfolio of the
  learning, not as actively maintained code.

## Scope: 3 exercises

### Exercise 1 — QA Loop (Form Validator)

**Target:** [https://practice.expandtesting.com/notes/app/register](https://practice.expandtesting.com/notes/app/register)
**Output:** `scripts/qa-form.ts` + `bugs.md` + `screenshots/qa-form/`
**Demonstrates:** semantic selectors (`getByRole` / `getByLabel`), edge case validation, fix-loop pattern.
**Success criterion:** `bugs.md` ends empty after ≤3 fix iterations.

### Exercise 2 — Scraping with Learning Loop

**Target:** [https://github.com/trending](https://github.com/trending)
**Output:** `scripts/scrape-trending.ts` + `data/trending-YYYY-MM-DD.csv`
**Demonstrates:** initial scrape → schema discovery → refine → CSV output.
**Success criterion:** CSV with ≥10 rows, schema captures repo name + author + stars + language.

### Exercise 3 — Storage State (Authenticated Session)

**Target:** GitHub.com authenticated session (list private repos via UI)
**Output:** `scripts/login-and-dump-state.ts` + `scripts/list-private-repos.ts` + `playwright/.auth/github.json` (gitignored)
**Demonstrates:** persistent auth pattern (storage state JSON), reuse across runs without re-login.
**Success criterion:** second run consumes `.auth/github.json`, lists ≥1 private repo without browser prompt.

## Tech stack

- TypeScript strict
- Node ≥20
- pnpm
- `@playwright/cli` (scripts) + `@playwright/test` (assertions in QA loop) + `playwright` (programmatic)
- Chromium browser via `pnpm exec playwright install chromium`

See [docs/learning-progress.md](learning-progress.md) for the full
setup + exercise step-by-step.

## Status

_(See [docs/learning-progress.md](learning-progress.md) "Current step pointer" for the live status.
This Status block is a high-level mirror — update both together.)_

- [x] Setup Step 1 done (pnpm + Playwright + Chromium installed)
- [ ] Setup Step 2 pending (overwrite tsconfig.json) ← **NEXT**
- [ ] Setup Steps 3-6 pending (package.json scripts, .gitignore, initial commit, version verify)
- [ ] Exercise 1 pending (7 sub-steps)
- [ ] Exercise 2 pending (future)
- [ ] Exercise 3 pending (future)
- [ ] Closure: log final calibration data + decide on local clone retention

## Closure criteria

The repo is "done" when:

1. The exercises (or whichever subset is decided) run cleanly — final
   scripts + working outputs.
2. Insights observed during execution have been recorded in the
   "Closure log" section of
   [docs/learning-progress.md](learning-progress.md).
3. All checkboxes in [docs/learning-progress.md](learning-progress.md)
   are either marked done, marked skipped (with reason), or removed
   from scope explicitly.

If exercises 2 or 3 are skipped, document the skip in Status above
with the reason.
