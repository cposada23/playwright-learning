# Learning progress — playwright-learning

> **Source of truth for cross-session continuity.** If you stop a
> session mid-step, find the first unchecked box below and resume
> there. Each step has: action (the exact command or task),
> verification (binary check — did it work?), output (what to
> paste/log), and insights (anything surprising worth recording).
>
> **Cross-references:**
> - PRD: [PRD.md](PRD.md)

## Current step pointer

**As of 2026-04-28:** Setup Steps 1-4 ✅ done (pnpm + Playwright + Chromium installed; `tsconfig.json` overwritten per spec; `package.json` configured with `"type": "module"` + 4 scripts; `.gitignore` extended with 5 Playwright entries). **Next:** Setup Step 5 (initial commit + push of the full scaffold to `https://github.com/cposada23/playwright-learning`).

_(Update this pointer at the end of each work session so the next session knows exactly where to resume.)_

---

## Setup

The setup happens once at the top of the repo. After Setup Step 6
verifies, all 3 exercises share the same `node_modules`, tsconfig, and
Playwright install.

### Setup Step 1 — Initialize TypeScript + Playwright

**Action:** run from inside this repo:

```sh
( cd <path-to-this-repo> && \
  pnpm init && \
  pnpm add -D typescript @types/node tsx && \
  pnpm add -D @playwright/cli @playwright/test playwright && \
  pnpm exec tsc --init && \
  pnpm exec playwright install chromium && \
  mkdir -p playwright/.auth scripts data screenshots logs )
```

**Verification:** all of these must be true after the command:

- [x] `package.json` exists at repo root
- [x] `pnpm-lock.yaml` exists at repo root
- [x] `tsconfig.json` exists at repo root (created by `tsc --init`)
- [x] `node_modules/playwright/` directory exists
- [x] `node_modules/@playwright/cli/` directory exists
- [x] `node_modules/@playwright/test/` directory exists
- [x] All 5 folders exist: `playwright/.auth/`, `scripts/`, `data/`, `screenshots/`, `logs/`
- [x] Chromium binary downloaded (Chrome for Testing 147.0.7727.15 to `~/Library/Caches/ms-playwright/`)

**Output (run on 2026-04-27):**

```
devDependencies installed (first batch):
+ @types/node 25.6.0
+ tsx 4.21.0
+ typescript 6.0.3

devDependencies installed (second batch):
+ @playwright/cli 0.1.9
+ @playwright/test 1.59.1
+ playwright 1.59.1

tsconfig.json: created (default content from `tsc --init`)

Browsers downloaded to ~/Library/Caches/ms-playwright/:
- Chromium 147.0.7727.15 (playwright v1217) — 165.5 MiB
- FFmpeg v1011 — 1 MiB
- Chrome Headless Shell 147.0.7727.15 — 92 MiB
Total download: ~258 MiB

Total time: ~3 min (mostly Chromium download).

Versions confirmed:
- playwright --version → "Version 1.59.1"
- playwright-cli --version → "0.1.9"
```

**Insights:**

1. **Stack bleeding edge:** node v25.8.2 + TypeScript v6.0.3 + Playwright v1.59.1. TS v6 is the very recent major bump (most repos are still on v5). If anything in the next steps breaks unexpectedly, this combo is the first suspect.
2. **pnpm security warning:** `Ignored build scripts: esbuild@0.27.7. Run "pnpm approve-builds" to pick which dependencies should be allowed to run scripts.` This is pnpm's default security posture — it blocks post-install scripts from packages until explicitly approved. Not an error. Can be ignored for this exercise; in production we'd run `pnpm approve-builds` and approve `esbuild` (which `tsx` depends on).
3. **`@playwright/cli` is at v0.1.9** — much lower version than `playwright` (v1.59.1) or `@playwright/test` (v1.59.1). The CLI package is a separate, newer (Microsoft 2026) thin CLI package, not co-versioned with the core library.
4. **Chrome for Testing, not regular Chrome:** Playwright downloads "Chrome for Testing" (CfT) builds, which are pinned versions Google maintains specifically for automation. Different from the Chrome installed via brew/Apple. Lives in `~/Library/Caches/ms-playwright/` — outside this repo (cache, not project file).

**Step status:** [ ] not started · [ ] in-progress · [x] done

---

### Setup Step 2 — Configure tsconfig.json

**Action:** replace the auto-generated `tsconfig.json` content with:

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

- [x] `tsconfig.json` matches the block above exactly
- [x] `cat tsconfig.json | head -3` shows `{` `"compilerOptions": {` `"target": "es2022",`

**Output (run on 2026-04-28):**

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

**Insights:**

1. **TypeScript v6 `tsc --init` ships a much more opinionated default** than what the spec assumes. The auto-generated config from Step 1 included: `module: nodenext`, `target: esnext`, `verbatimModuleSyntax: true`, `isolatedModules: true`, `noUncheckedSideEffectImports: true`, `moduleDetection: force`, `noUncheckedIndexedAccess: true`, `exactOptionalPropertyTypes: true`, `jsx: react-jsx`, `sourceMap/declaration/declarationMap: true`. None of these are in the spec — the wholesale overwrite drops all of them.
2. **`module: nodenext` → `module: esnext` + `moduleResolution: bundler`** is the meaningful divergence. `nodenext` enforces `.js` import suffixes and node-style ESM resolution; `bundler` is more permissive and matches how `tsx` (the runner used in Setup Step 3) actually resolves modules. The spec's choice is correct for this stack — `tsx` is bundler-like, not pure node ESM.
3. **`target: es2022` vs `esnext`** — the spec pins to a stable target instead of letting TS v6 default to `esnext` (moving target). Cleaner for a learning repo where reproducibility matters more than bleeding-edge syntax.
4. **No tripping points during overwrite.** Wholesale replace via Write tool, no merge needed. The original file's comments (e.g. `// Visit https://aka.ms/tsconfig...`) are gone — expected, the spec is comment-free.
5. **`rootDir: "./scripts"` + `include: ["scripts/**/*"]`** anchors the compile scope to a single folder. Setup Step 1 already created `scripts/` empty — Step 3+4 will start populating it.

**Step status:** [ ] not started · [ ] in-progress · [x] done

---

### Setup Step 3 — Configure package.json

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

- [x] `cat package.json | grep '"type"'` shows `"type": "module"`
- [x] `pnpm run` (with no args) lists `qa`, `scrape`, `login`, `list-private`

**Output (run on 2026-04-28):**

```
Commands available via "pnpm run":
  qa
    tsx scripts/qa-form.ts
  scrape
    tsx scripts/scrape-trending.ts
  login
    tsx scripts/login-and-dump-state.ts
  list-private
    tsx scripts/list-private-repos.ts
```

**Insights:**

1. **Wholesale replacement, not merge.** The default `pnpm init` shipped a `"scripts": { "test": "echo \"Error: no test specified\" && exit 1" }` placeholder. Spec says "Add (or replace)" — I replaced wholesale, dropping the default `test` stub. If a future exercise needs a `test` script (Vitest, Playwright test runner), it gets re-added then.
2. **`"main": "index.js"` left intact.** Default from `pnpm init`. Not referenced by any of the new scripts (all entry points are `tsx scripts/*.ts`), so harmless to keep. Cleaner to leave the field than to delete and re-add when something needs it.
3. **No ESM vs CJS conflict observed.** `"type": "module"` makes `.js` files ESM by default, but every entry point in the scripts section goes through `tsx` to `.ts` source — `tsx` handles ESM/CJS interop transparently for `.ts`. The `tsx` runner is exactly what makes `"type": "module"` safe to set without rewriting any existing JS.
4. **`pnpm run` (no args) is the cleaner verification** vs `cat package.json | grep`. It both confirms the scripts are registered AND that `package.json` is valid JSON (a syntax error would break `pnpm run` before listing).

**Step status:** [ ] not started · [ ] in-progress · [x] done

---

### Setup Step 4 — Extended .gitignore

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
is auth state JSON with valid session tokens — never commit.
`.playwright-cli/` is local cache.

**Verification:**

- [x] `cat .gitignore | tail -10` shows the 5 new lines
- [x] Existing Node entries (`node_modules/`, etc.) still present

**Output (run on 2026-04-28):**

```
vite.config.js.timestamp-*
vite.config.ts.timestamp-*
.vite/

# Playwright (this repo)
playwright/.auth/
.playwright-cli/
screenshots/
data/
logs/
```

Verified Node entries intact: `*.log`, `node_modules/`, `.env`, `.env.*` still present (grep).

**Insights:**

1. **The `gh repo create --gitignore Node` template already had `logs`** (line 2 of the original, no trailing slash). Adding `logs/` is technically redundant but more explicit (matches directories only — `logs` matches both files and dirs). Followed the spec wholesale; the redundancy is benign and keeps all 5 playwright-specific entries grouped together.
2. **None of the other 4 entries** (`playwright/.auth/`, `.playwright-cli/`, `screenshots/`, `data/`) were in the Node template — expected, since the template predates Playwright (and `data/` is a generic output convention specific to this learning repo).
3. **Added a `# Playwright (this repo)` section header** to keep the additions identifiable as a group, separate from the Node template's organic sections (Logs, Diagnostic reports, Runtime data, etc.). Makes future cleanup obvious — the boundary between "GitHub default Node template" and "this repo's customizations" is explicit.
4. **No accidental truncation.** Verified existing entries (`node_modules/`, `.env`, `*.log`) still present after the append via grep — sanity check that the Edit didn't replace more than intended.

**Step status:** [ ] not started · [ ] in-progress · [x] done

---

### Setup Step 5 — Initial commit + push

**Action:** commit the scaffold and push to GitHub.

```sh
( cd <path-to-this-repo> && \
  git add . && \
  git status && \
  git commit -m "chore: initial sandbox scaffold (setup)" && \
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

### Setup Step 6 — Verify setup

**Action:**

```sh
( cd <path-to-this-repo> && \
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

**End of Setup. Next: Exercise 1.**

---

## Exercise 1 — QA Loop (Form Validator)

Target: [https://practice.expandtesting.com/notes/app/register](https://practice.expandtesting.com/notes/app/register)

### Exercise 1 Step 1 — Create spec file

**Action:** create `docs/qa-form-spec.md` describing target URL, happy
path, 3 edge cases (invalid email, weak password, mismatched
confirm password), and the format for results.

**Verification:**

- [ ] `docs/qa-form-spec.md` exists
- [ ] File contains: target URL, happy path (5 fields), 3 edge cases, results format

**Output:**

_(paste `wc -l docs/qa-form-spec.md`)_

**Insights:**

_(e.g. did Claude Code copy the spec verbatim or reword it? Reword is fine if semantics match.)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Exercise 1 Step 2 — Generate the script via Claude Code (plan mode first)

**Action:** open Claude Code from inside the repo, enter plan mode (`Shift+Tab`),
ask for a Playwright script that validates the signup form per
`docs/qa-form-spec.md` with these requirements:

- Path: `scripts/qa-form.ts`
- Headed mode (so you see the browser)
- Generate unique email per run (`Date.now()` suffix)
- Screenshot per step to `screenshots/qa-form/<step>.png`
- `bugs.md` at the end with results of all 4 checks
- Exit code 0 if all OK, 1 if any bug
- Semantic selectors (`getByRole` / `getByLabel`), no CSS classes
- 5s timeout per interaction, 10s for redirects
- DO NOT run the script yet — show the code first

Accept the plan, let Claude write `scripts/qa-form.ts`.

**Verification:**

- [ ] `scripts/qa-form.ts` exists
- [ ] First lines of the file import from `@playwright/test` or `playwright`
- [ ] No syntax errors when running `pnpm exec tsc --noEmit` (optional check)

**Output:**

_(paste `wc -l scripts/qa-form.ts` + first 5 lines)_

**Insights:**

_(e.g. did Claude pick `@playwright/test` or `playwright` programmatic? Either is fine; note which one and why.)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done

---

### Exercise 1 Step 3 — Review the script (mandatory before running)

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

### Exercise 1 Step 4 — First run

**Action:**

```sh
( cd <path-to-this-repo> && pnpm qa )
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

### Exercise 1 Step 5 — Review the output

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

### Exercise 1 Step 6 — Fix loop (until bugs.md is empty)

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

### Exercise 1 Step 7 — Promote to skill (optional)

**Action:** if you liked the script and want to invoke it by name from
any directory, copy it to your global Claude Code skills folder
(`~/.claude/skills/qa-form-expandtesting/`) with a `SKILL.md`
descriptor.

**Verification:**

- [ ] `~/.claude/skills/qa-form-expandtesting/scripts/qa-form.ts` exists
- [ ] `~/.claude/skills/qa-form-expandtesting/SKILL.md` exists with frontmatter
- [ ] (Optional) Skill invocable from another project — test by `cd` to a different folder and confirm Claude Code lists this skill.

**Output:**

_(paste `ls ~/.claude/skills/qa-form-expandtesting/`)_

**Insights:**

_(e.g. tradeoffs of project-scoped script vs global skill)_

**Step status:** [ ] not started · [ ] in-progress · [ ] done · [ ] skipped

**End of Exercise 1.**

---

## Exercise 2 — Scraping with Learning Loop (future)

Future exercise. Detailed steps to be added when arriving here. Will
scrape [github.com/trending](https://github.com/trending), output to
`data/trending-YYYY-MM-DD.csv`. Demonstrates: initial scrape → schema
discovery → refine.

---

## Exercise 3 — Storage State (future)

Future exercise. Detailed steps to be added when arriving here. Will
dump GitHub auth state to `playwright/.auth/github.json`, then list
private repos in a second script that consumes the state.
Demonstrates: persistent auth pattern.

---

## Closure log

When all exercises (or whichever subset is decided) are done, log the
closure here:

- **Closed date:** _(YYYY-MM-DD)_
- **Exercises completed:** _(list which ones)_
- **Exercises skipped + reason:** _(if any)_
- **Notable insights:**
  - _(insight summary; e.g. "pnpm peer dep warning on @playwright/cli@1.x can be ignored")_
- **Final iteration count per exercise:** _(calibration data for future)_
- **Final time per exercise:** _(calibration data)_
- **Decision on local clone:** _(kept | deleted)_
