# playwright-learning

Personal sandbox for **Playwright + Claude Code workflows**. Three
self-contained pedagogical exercises, each a small disposable script
that demonstrates one workflow:

1. **QA loop** — generate a Playwright TypeScript script with Claude
   Code, run it against a public form-validation practice site, fix
   bugs in a tight loop until the validator passes.
2. **Scraping with learning loop** — initial scrape → schema
   discovery → refine. Output goes to a CSV.
3. **Storage state** — dump an authenticated browser session to a
   JSON file, reuse the file on a second run to skip re-login.

The repo is intentionally throwaway. The point is calibration: how
long does a workflow take, what gotchas show up, when is Playwright
+ Claude Code the right tool and when is it overkill.

## What's in here

- [docs/PRD.md](docs/PRD.md) — lightweight spec for the 3 exercises +
  closure criteria.
- [docs/learning-progress.md](docs/learning-progress.md) — **source
  of truth for cross-session continuity.** Step-by-step detailed
  checklist with verification + output + insights slots per step.
  Open this file to know where you stopped and what's next.
- `scripts/` — Playwright TypeScript scripts (one per exercise).
  Created during execution.
- `playwright/.auth/` — auth state JSONs (gitignored).
- `screenshots/`, `data/`, `logs/` — run output (gitignored, each run
  reproducible from scratch).
- `docs/qa-form-spec.md` — exercise 1 spec (created during Exercise 1 Step 1).

## Stack

- TypeScript strict
- Node ≥20
- pnpm
- `@playwright/cli` + `@playwright/test` + `playwright`

## How to run

After completing the setup steps in
[docs/learning-progress.md](docs/learning-progress.md):

```sh
pnpm qa             # exercise 1 — QA loop on practice.expandtesting.com
pnpm scrape         # exercise 2 — github.com/trending scraper (future)
pnpm login          # exercise 3a — dump GitHub auth state (future)
pnpm list-private   # exercise 3b — list private repos using stored state (future)
```

Each script writes its outputs to `screenshots/<name>/`, `data/`,
`logs/`, or root-level files like `bugs.md`. None of these are
tracked.

## License

MIT — see [LICENSE](LICENSE).
