# Learning progress — playwright-learning

> **Source of truth for cross-session continuity.** If you stop a
> session mid-step, find the first unchecked box below and resume
> there. Each step has: action (the exact command or task),
> verification (binary check — did it work?), output (what to
> paste/log), and insights (anything surprising worth promoting
> to the upstream playbook).
>
> **Cross-references:**
> - PRD: [PRD.md](PRD.md)
> - Upstream playbook: [browser-automation-playwright.md §11](https://github.com/cposada23/AI_knowledge/blob/main/wiki/playbooks/claude-code-workflows/browser-automation-playwright.md)
> - Macro phase tracker (Kortex side): [output/sessions/2026-04-27.md](https://github.com/cposada23/AI_knowledge/blob/main/output/sessions/2026-04-27.md)

## Current step pointer

**As of 2026-04-27:** Phases A-C ✅ done (Kortex bookkeeping + gh repo + scaffold). **Next:** Phase D Step 11.0.3 (initialize TypeScript + Playwright via pnpm).

_(Update this pointer at the end of each work session so the next session knows exactly where to resume.)_

---

## Phase D — §11.0 Setup base

The setup happens once at the top of the repo. After §11.0.8 verifies,
all 3 exercises share the same `node_modules`, tsconfig, and Playwright
install.

### Step 11.0.3 — Initialize TypeScript + Playwright

**Action:** run from inside this repo (subshell from Kortex parent):

```sh
( cd /Users/camiloposada/Documents/Courses/AI_knowledge/projects/kortex-lab/playground/playwright-learning && \
  pnpm init && \
  pnpm add -D typescript @types/node tsx && \
  pnpm add -D @playwright/cli @playwright/test playwright && \
  pnpm exec tsc --init && \
  pnpm exec playwright install chromium && \
  mkdir -p playwright/.auth scripts data screenshots logs )
```

**Verification:** all of these must be true after the command:

- [ ] `package.json` exists at repo root
- [ ] `pnpm-lock.yaml` exists at repo root
- [ ] `tsconfig.json` exists at repo root (created by `tsc --init`)
- [ ] `node_modules/playwright/` directory exists
- [ ] `node_modules/@playwright/cli/` directory exists
- [ ] `node_modules/@playwright/test/` directory exists
- [ ] All 5 folders exist: `playwright/.auth/`, `scripts/`, `data/`, `screenshots/`, `logs/`
- [ ] Chromium binary downloaded (check via next step's verification)

**Output (paste relevant after running):**

_(installed Playwright version, any warnings, time to install)_

**Insights:**

_(any gotcha — e.g. pnpm flagging peer deps, registry timeouts, version conflicts)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.0.4 — Configure tsconfig.json

**Action:** replace the auto-generated `tsconfig.json` content with the
playbook §11.0.4 version. Use the Edit tool from Claude Code, or open
in VS Code:

```json
{
  "compilerOptions": {
    "target": "es2022",
    "module": "esnext",
    "moduleResolution": "bundler",
    "lib": ["es2022", "dom"],
    "outDir": "./dist",
    "rootDir": "./scripts",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["scripts/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

**Verification:**

- [ ] `tsconfig.json` matches the block above exactly
- [ ] `cat tsconfig.json | head -3` shows `{` `"compilerOptions": {` `"target": "es2022",`

**Output:**

_(no command output expected — this is a file edit. Optionally paste `cat tsconfig.json` after.)_

**Insights:**

_(any tripping point — e.g. `tsc --init` already had options that conflicted with manual replacement)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.0.5 — Configure package.json

**Action:** in `package.json`:

1. Add at root level: `"type": "module"`
2. Add (or replace) the `scripts` section with:

```json
"scripts": {
  "qa": "tsx scripts/qa-form.ts",
  "scrape": "tsx scripts/scrape-trending.ts",
  "login": "tsx scripts/login-and-dump-state.ts",
  "list-private": "tsx scripts/list-private-repos.ts"
}
```

**Verification:**

- [ ] `cat package.json | grep '"type"'` shows `"type": "module"`
- [ ] `pnpm run` (with no args) lists `qa`, `scrape`, `login`, `list-private`

**Output:**

_(paste output of `pnpm run`)_

**Insights:**

_(e.g. ESM vs CJS conflicts when running `tsx`)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.0.6 — Extended .gitignore

**Action:** append the following to the existing `.gitignore` (Node
template) created by gh:

```
playwright/.auth/
.playwright-cli/
screenshots/
data/
logs/
```

**Why:** `screenshots/`, `data/`, `logs/` are run output — each run
should be reproducible from scratch, no committed artifacts. `.auth/`
is auth state JSON with valid session tokens — never commit. `.playwright-cli/`
is local cache.

**Verification:**

- [ ] `cat .gitignore | tail -10` shows the 5 new lines
- [ ] Existing Node entries (`node_modules/`, etc.) still present

**Output:**

_(paste tail of .gitignore)_

**Insights:**

_(e.g. did the Node template already cover any of these?)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.0.7 — Initial commit + push

**Action:** commit the scaffold and push to GitHub.

```sh
( cd /Users/camiloposada/Documents/Courses/AI_knowledge/projects/kortex-lab/playground/playwright-learning && \
  git add . && \
  git status && \
  git commit -m "chore: initial playwright sandbox scaffold (§11.0)" && \
  git push origin main )
```

**Verification:**

- [ ] `git status` after push shows `working tree clean`
- [ ] `git log --oneline` shows the new commit at HEAD
- [ ] [https://github.com/cposada23/playwright-learning](https://github.com/cposada23/playwright-learning) shows the new files (`package.json`, `tsconfig.json`, etc.)

**Output:**

_(paste git log --oneline + sha of the new commit)_

**Insights:**

_(any push issue — auth, permissions, line endings)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.0.8 — Verify setup

**Action:**

```sh
( cd /Users/camiloposada/Documents/Courses/AI_knowledge/projects/kortex-lab/playground/playwright-learning && \
  pnpm exec playwright-cli --version && \
  pnpm exec playwright --version && \
  node -v )
```

**Verification:** all 3 commands print versions, no errors:

- [ ] `playwright-cli --version` prints version (e.g. `1.x.x`)
- [ ] `playwright --version` prints version (e.g. `Version 1.x.x`)
- [ ] `node -v` prints `v20.x.x` or higher

**Output:**

_(paste the 3 version strings)_

**Insights:**

_(e.g. version mismatch between @playwright/cli and playwright; node < 20)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

**End of Phase D — Setup base complete. Next: Phase E §11.1 QA Loop exercise.**

---

## Phase E — §11.1 QA Loop exercise (Form Validator)

Target: [https://practice.expandtesting.com/notes/app/register](https://practice.expandtesting.com/notes/app/register)

### Step 11.1.D.1 — Create spec file

**Action:** create `docs/qa-form-spec.md` with the spec from playbook §11.1.D.1
(target URL, happy path, 3 edge cases, results format).

Full content of the spec is in the playbook — copy it verbatim into
`docs/qa-form-spec.md`. Or generate it with Claude Code via:

> "Create docs/qa-form-spec.md with the content from §11.1.D.1 of
> https://github.com/cposada23/AI_knowledge/blob/main/wiki/playbooks/claude-code-workflows/browser-automation-playwright.md"

**Verification:**

- [ ] `docs/qa-form-spec.md` exists
- [ ] File contains: target URL, happy path (5 fields), 3 edge cases, results format

**Output:**

_(paste `wc -l docs/qa-form-spec.md`)_

**Insights:**

_(e.g. did Claude Code copy the spec verbatim or reword it? Reword is fine if semantics match.)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.1.D.2 — Generate the script via Claude Code (plan mode first)

**Action:** open Claude Code from inside the repo, enter plan mode (`Shift+Tab`),
paste the request from playbook §11.1.D.2 verbatim:

```
Plan: necesito un script Playwright en TypeScript que valide el form
de signup de https://practice.expandtesting.com/notes/app/register
según el spec en docs/qa-form-spec.md.

Requirements:
- Path del script: scripts/qa-form.ts
- Headed mode (querés ver el browser).
- Generar email único cada run (usá Date.now() suffix).
- Screenshot por cada paso a screenshots/qa-form/<step>.png.
- bugs.md al final con resultados de los 4 checks (1 happy path + 3
  edge cases).
- Script que sale con exit code 0 si todo OK, 1 si hay bug.
- Usar getByRole / getByLabel (semantic selectors), no CSS classes.
- Timeout de 5s por interacción, 10s para espera de redirect.

NO ejecutar el script todavía. Mostrame el código primero.
```

Accept the plan, let Claude write `scripts/qa-form.ts`.

**Verification:**

- [ ] `scripts/qa-form.ts` exists
- [ ] First lines of the file import from `@playwright/test` or `playwright`
- [ ] No syntax errors when running `pnpm exec tsc --noEmit` (optional check)

**Output:**

_(paste `wc -l scripts/qa-form.ts` + first 5 lines)_

**Insights:**

_(e.g. did Claude pick `@playwright/test` or `playwright` programmatic? Playbook §3 prefers `@playwright/cli` style for ad-hoc; check if Claude inferred this correctly.)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.1.D.3 — Review the script (mandatory before running)

**Action:** read `scripts/qa-form.ts` end to end. Check:

- [ ] Selectors are semantic (`getByRole`, `getByLabel`, `getByPlaceholder`) — NOT `page.locator('.css-xyz')`
- [ ] Each `await` doing I/O has an explicit timeout
- [ ] Email is generated dynamically per run (uses `Date.now()` or similar; no hardcoded email)
- [ ] The 3 edge cases are wrapped in try/catch (one failure doesn't abort the next)
- [ ] `bugs.md` is written at the end, even if there are bugs (in a `finally` block or after the try/catch chain)

If any check fails, ask Claude:

> "Adjust X — [specify what]. Show me the diff before applying."

Repeat until all 5 checks pass.

**Verification:**

- [ ] All 5 review checks above pass
- [ ] No CSS class selectors found (`grep -n "page.locator" scripts/qa-form.ts` returns 0 hits or only acceptable cases)

**Output:**

_(paste any diffs Claude applied during review)_

**Insights:**

_(e.g. which checks Claude usually misses — useful for next exercise)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.1.D.4 — First run

**Action:**

```sh
( cd /Users/camiloposada/Documents/Courses/AI_knowledge/projects/kortex-lab/playground/playwright-learning && \
  pnpm qa )
```

Watch the browser open. Mental notes:

- Does the page load fast enough? (if not, may need higher timeout)
- Does the form fill correctly?
- Do errors appear on edge cases as expected?

**Verification:**

- [ ] Browser opens in headed mode (Chromium window appears)
- [ ] Script either exits 0 (all checks passed) or exits 1 (with bugs)
- [ ] `bugs.md` exists at repo root (empty content if exit 0, items if exit 1)
- [ ] `screenshots/qa-form/` has multiple `.png` files

**Output:**

_(paste exit code + first 20 lines of bugs.md + ls screenshots/qa-form/)_

**Insights:**

_(e.g. how long the run took; any flake; did Chromium prompt for permissions)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.1.D.5 — Review the output

**Action:**

- Open `bugs.md` and read every entry.
- Open `screenshots/qa-form/` and check each screenshot — does each
  step look correct visually?

**Verification:**

- [ ] Read `bugs.md` end-to-end
- [ ] Reviewed at least 4 screenshots (1 per check)
- [ ] Decided per bug: real bug vs false positive

**Output:**

_(paste bugs.md contents + your real-vs-false-positive decision per item)_

**Insights:**

_(e.g. screenshot quality — were they too small / cropped / wrong moment)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.1.D.6 — Fix loop (until bugs.md is empty)

**Action:** for each bug, decide:

- **Real bug** → ask Claude: "Fix the script: [describe the bug]". Re-run `pnpm qa`. Re-review.
- **False positive** → ask Claude: "Behavior X is by design — adjust the script to not flag it". Re-run. Re-review.

Loop until `bugs.md` is empty (or contains only `All checks passed ✓`).

**Verification:**

- [ ] `bugs.md` ends with empty content or "All checks passed ✓"
- [ ] Final exit code of `pnpm qa` is 0
- [ ] You ran the script at least once after each fix
- [ ] **Calibration:** how many fix iterations did it take? (typical: 1-3)

**Output:**

_(paste final bugs.md state + iteration count)_

**Insights:**

_(if iterations > 5, the spec was probably ambiguous — note what was unclear)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Step 11.1.D.7 — Promote to skill (optional)

**Action:** if you liked the script and want to invoke it by name from
any directory:

```sh
mkdir -p ~/.claude/skills/qa-form-expandtesting/scripts
cp scripts/qa-form.ts ~/.claude/skills/qa-form-expandtesting/scripts/
```

Then create `~/.claude/skills/qa-form-expandtesting/SKILL.md` per
playbook §11.1.D.7.

**Verification:**

- [ ] `~/.claude/skills/qa-form-expandtesting/scripts/qa-form.ts` exists
- [ ] `~/.claude/skills/qa-form-expandtesting/SKILL.md` exists with frontmatter
- [ ] (Optional) Skill invocable from another project — test by `cd` to a different folder and confirm Claude Code lists this skill.

**Output:**

_(paste `ls ~/.claude/skills/qa-form-expandtesting/`)_

**Insights:**

_(e.g. tradeoffs of project-scoped script vs global skill)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done · [ ] skipped

**End of Phase E — §11.1 QA Loop exercise complete.**

---

## §11.2 Scraping with Learning Loop (future)

Future exercise. Detailed steps to be added when arriving here. See
playbook §11.2 for the spec. Will scrape [github.com/trending](https://github.com/trending),
output to `data/trending-YYYY-MM-DD.csv`. Demonstrates: initial scrape →
schema discovery → refine.

---

## §11.3 Storage State (future)

Future exercise. Detailed steps to be added when arriving here. See
playbook §11.3 for the spec. Will dump GitHub auth state to
`playwright/.auth/github.json`, then list private repos in a second
script that consumes the state. Demonstrates: persistent auth pattern.

---

## Closure log

When all 3 exercises (or whichever subset is decided) are done, log
the closure here:

- **Closed date:** _(YYYY-MM-DD)_
- **Exercises completed:** _(list which ones)_
- **Exercises skipped + reason:** _(if any)_
- **Insights promoted to playbook:**
  - _(playbook section + insight summary; e.g. "§F Gotchas — added: pnpm peer dep warning on @playwright/cli@1.x can be ignored")_
- **Final iteration count per exercise:** _(calibration data for future)_
- **Final time per exercise:** _(calibration data)_
- **Decision on local clone:** _(kept | deleted)_
- **Registry status updated:** [ ] yes — entry moved Active → Archived in [Kortex playground/registry.md](https://github.com/cposada23/AI_knowledge/blob/main/projects/kortex-lab/playground/registry.md)
