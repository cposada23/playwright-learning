# playwright-learning

Personal sandbox for **Playwright + Claude Code workflows** — runs the
3 pedagogical exercises from the upstream playbook
[`browser-automation-playwright.md`](https://github.com/cposada23/AI_knowledge/blob/main/wiki/playbooks/claude-code-workflows/browser-automation-playwright.md)
§11. Throwaway by design — once the exercises are done, this repo
stays as portfolio of the learning.

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
- `docs/qa-form-spec.md` — exercise 1 spec (created during Step 11.1.D.1).

## Stack

- TypeScript strict
- Node ≥20
- pnpm
- `@playwright/cli` + `@playwright/test` + `playwright`

## How to run

After Phase D setup (see [docs/learning-progress.md](docs/learning-progress.md) §11.0):

```sh
pnpm qa             # exercise 1 — QA loop on practice.expandtesting.com
pnpm scrape         # exercise 2 — github.com/trending scraper (future)
pnpm login          # exercise 3a — dump GitHub auth state (future)
pnpm list-private   # exercise 3b — list private repos using stored state (future)
```

Each script writes its outputs to `screenshots/<name>/`, `data/`,
`logs/`, or root-level files like `bugs.md`. None of these are
tracked.

## Context

This repo is part of the Kortex `playground/` sub-zone — a sandbox
for throwaway experiments inside the larger Kortex personal knowledge
graph. It's gitignored from the main Kortex repo via the wildcard
`projects/kortex-lab/playground/*/`. The convention is documented at
[Kortex `playground/README.md`](https://github.com/cposada23/AI_knowledge/blob/main/projects/kortex-lab/playground/README.md).
The registry tracking this experiment lives at
[Kortex `playground/registry.md`](https://github.com/cposada23/AI_knowledge/blob/main/projects/kortex-lab/playground/registry.md).

## License

MIT — see [LICENSE](LICENSE).
