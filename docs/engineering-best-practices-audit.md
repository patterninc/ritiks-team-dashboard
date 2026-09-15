# Engineering Best Practices Audit — patterninc/ritiks-team-dashboard

| | |
|---|---|
| **Audit date** | 2026-09-15 |
| **Auditor** | Claude — gauge-repo skill |
| **Rubric version** | `item-credit-v1` — 2026-09-04 (`references/best-practices.md`) |

## Repo profile

`patterninc/ritiks-team-dashboard` is a single-file static HTML dashboard ("Copy of Main Dashboard" per `backstage.yaml`). The entire application is `index.html` (2,706 lines): inline CSS and ~1,800 lines of inline JavaScript that call the Asana REST API directly from the browser using a user-supplied Personal Access Token stored in `localStorage`, and render charts via Chart.js loaded from a version-pinned jsDelivr CDN URL with a Subresource Integrity hash. There is no build step, no package manifest, no server, no database, no deployment configuration, no `.github/` directory, no README, and no tests. Git history is a single commit ("Onboard ritiks-team-dashboard to Backstage") by one bot contributor (`patterninc-gha-runner`). The GitHub owner is verified as `patterninc`, so inherited Wiz (secret scanning, SAST, dependency coverage) and Toolsmith (MCP) controls apply, and the org-wide `require-pr-review` ruleset protects the default branch. This profile — a client-side-only static page with no build, deploy, backend, or dependency manifest — justifies the large number of N/A verdicts below; documentation basics (README, AGENTS.md) and testing of the substantial inline JavaScript remain applicable.

## Scorecard

| Metric | Value |
|--------|-------|
| **Critical gates** | **RED** |
| **Adjusted compliance** | **28.6%** |

Critical gates are RED: items 2 (AGENTS.md), 6 (README), 16 (required CI), and 23 (unit tests) are applicable and Gap. Adjusted compliance is calculated independently:

`(6 Met + 0.5 × 0 Partial) / (49 total - 28 justified N/A) = 6 / 21 = 28.6%`

### Status totals

| Status | Items |
|--------|------:|
| Met | 6 |
| Partial | 0 |
| Gap | 15 |
| N/A | 28 |
| **Total** | **49** |

### Per-category breakdown

| Category | Met | Partial | Gap | N/A |
|----------|----:|--------:|----:|----:|
| Documentation & Context | 0 | 0 | 3 | 6 |
| Guardrails & Enforcement | 3 | 0 | 5 | 5 |
| Testing & Feedback Loops | 0 | 0 | 6 | 7 |
| Environment & Tooling | 3 | 0 | 0 | 10 |
| Agent dispatch | 0 | 0 | 1 | 0 |
| **Total** | **6** | **0** | **15** | **28** |

## Documentation & Context

| # | Practice | Status | Evidence | Recommendation / rationale |
|---|----------|--------|----------|----------------------------|
| 1 | Skills / reusable prompt workflows | **Not applicable** | No `.claude/skills/` or similar; repo is one static page | No recurring multi-step tasks exist in a single-file static page that would justify saved workflows. |
| 2 | AGENTS.md | **Gap** | No `AGENTS.md` (repo contains only `index.html` and `backstage.yaml`) | Add `AGENTS.md` explaining the single-file architecture, the Asana API integration, the localStorage PAT flow, and how to preview changes (open `index.html` in a browser). |
| 3 | Architecture decision records | **Not applicable** | No `docs/adr/` | Single static file with no evolving architecture; the notable decisions (single-file, CDN Chart.js, client-side PAT) belong in the README/AGENTS.md rather than an ADR log. |
| 4 | Runbooks | **Not applicable** | No operational surface | No deployment, keys, or infrastructure to operate; nothing to roll back or rotate. |
| 5 | API contract docs (OpenAPI / protobuf) | **Not applicable** | Repo consumes the Asana API (`index.html` calls `https://app.asana.com/api/1.0/...`); it exposes no API of its own | No owned wire contract to document. |
| 6 | README with setup & run instructions | **Gap** | No `README.md` | Add a README: what the dashboard shows, how to open/serve it, how to obtain and enter an Asana Personal Access Token, and the localStorage caveat. |
| 7 | Changelog with migration notes | **Not applicable** | Single commit; no releases or external consumers | Internal single-page tool with no versioned releases or upgrade path. |
| 8 | On-call playbooks | **Not applicable** | No production service or paging surface | Client-side page with no incident-response scope. |
| 9 | CODEOWNERS | **Gap** | No `.github/CODEOWNERS`; sole contributor is `patterninc-gha-runner` | Add `CODEOWNERS` mapping `*` to the owning team so PR review assignment is automatic. |

## Guardrails & Enforcement

| # | Practice | Status | Evidence | Recommendation / rationale |
|---|----------|--------|----------|----------------------------|
| 10 | Linters | **Gap** | No ESLint/htmlhint config; ~1,800 lines of inline JS in `index.html` | Add HTML/JS linting (e.g., ESLint with an HTML plugin, or extract the script and lint it) — the inline script is large enough to benefit. |
| 11 | Formatters | **Gap** | No Prettier or formatter config | Add Prettier for HTML/CSS/JS to keep the large single file consistently formatted. |
| 12 | Type checking | **Not applicable** | All JS is inline in `index.html`; no modules or build step | Static type checking would require extracting the script into a build pipeline, which is beyond this repo's single-file scope; revisit if the script is ever extracted. |
| 13 | Pre-commit hooks | **Gap** | No `.pre-commit-config.yaml` or `.husky/` | Add pre-commit hooks running the formatter/linter once those exist. |
| 14 | Commit message conventions | **Gap** | Single commit; no convention documented or enforced | Adopt Conventional Commits and note it in the README/AGENTS.md. |
| 15 | Branch protection rules | **Met** | Org-wide `require-pr-review` ruleset (active, default branch): deletion and force-push blocked, 1 required approving review, stale review dismissal | — |
| 16 | Required CI checks before merge | **Gap** | No `.github/workflows/`; no CI exists, so no required status checks | Add a minimal CI workflow (lint + tests once they exist) and mark it as a required status check on `main`. |
| 17 | Dependency allow-lists / deny-lists | **Not applicable** | No package manager; sole third-party dependency is one pinned CDN `<script>` (Chart.js 4.5.0) | No dependency graph to police. |
| 18 | License compliance scanning | **Not applicable** | No dependency manifest to scan | Single MIT-licensed CDN library; no automated license surface. |
| 19 | Secret scanning | **Met** | Inherited Pattern Wiz policy (org-wide, verified `patterninc` owner) | — |
| 20 | SAST / static analysis gates | **Met** | Inherited Pattern Wiz policy | — |
| 21 | Max complexity limits | **Not applicable** | No lint infrastructure; single-file dashboard | Complexity ceilings add little value for a self-contained page; fold into linter rules if item 10 is adopted. |
| 22 | Import boundary enforcement | **Not applicable** | No modules or imports — one inline script | No architectural layers to enforce. |

## Testing & Feedback Loops

| # | Practice | Status | Evidence | Recommendation / rationale |
|---|----------|--------|----------|----------------------------|
| 23 | Unit tests | **Gap** | No test files anywhere; `index.html` contains substantial logic (member stats, overdue calculations, filtering, caching) | Extract the pure computation functions (stats aggregation, date/overdue logic) into a testable script and add unit tests. |
| 24 | Integration tests | **Not applicable** | Single client-side file; no owned backend, database, or service composition | Nothing to integrate beyond the third-party Asana API; browser E2E (item 27) is the meaningful multi-component check here. |
| 25 | Snapshot / golden-file tests | **Not applicable** | No test infrastructure; no serialized output distinct from the rendered UI | UI output is better covered by E2E/visual checks than golden files. |
| 26 | Contract tests (Pact) | **Not applicable** | Repo is a consumer of Asana's public API and owns no provider contract | Cannot run consumer-provider verification against a third-party SaaS API. |
| 27 | End-to-end tests (Playwright) | **Gap** | No E2E tests; the repo is entirely a browser UI | Add a small Playwright suite (mock the Asana API) covering token entry, tab navigation, and chart/stat rendering — the highest-value test type for this repo. |
| 28 | Visual regression tests | **Gap** | No screenshot-diff tooling; the product is a visual dashboard | Add screenshot comparisons for the main tabs once Playwright exists (piggybacks on item 27). |
| 29 | Test coverage thresholds | **Gap** | No tests, so no coverage enforcement | Enforce a coverage floor in CI once the unit test suite (item 23) lands. |
| 30 | Mutation testing | **Not applicable** | No test suite; repo size and risk do not justify mutation testing | Disproportionate for a single-page internal dashboard. |
| 31 | Load / performance benchmarks | **Not applicable** | Static page, no server; load is borne by Asana's API | No owned throughput/latency surface. |
| 32 | Flaky test quarantine | **Not applicable** | No test suite or CI volume where flakiness management applies | Revisit only if a sizeable suite emerges. |
| 33 | Structured CI output | **Gap** | No CI exists | When adding CI (item 16), emit machine-readable results (e.g., JUnit XML from Playwright/Vitest). |
| 34 | Deterministic test fixtures | **Gap** | No fixtures; the app depends on live Asana API responses | Record fixed Asana API response fixtures so any test run is reproducible without live credentials. |
| 35 | Smoke tests for deploys | **Not applicable** | No deployment pipeline or hosted environment defined in the repo | Nothing is deployed from this repo; revisit if it gains a hosting target (e.g., GitHub Pages). |

## Environment & Tooling

| # | Practice | Status | Evidence | Recommendation / rationale |
|---|----------|--------|----------|----------------------------|
| 36 | Devcontainer config | **Not applicable** | No toolchain — the page opens directly in a browser | No environment to containerize. |
| 37 | One-command setup (make dev) | **Not applicable** | Zero setup: open `index.html` | Nothing to bootstrap; the README (item 6) should simply state this. |
| 38 | Seed scripts for local databases | **Not applicable** | No database | Data comes live from the Asana API. |
| 39 | MCP servers for external tools | **Met** | Toolsmith-managed MCP access (verified `patterninc` owner) | — |
| 40 | Scoped secrets per environment | **Not applicable** | No credentials committed or configured; the Asana PAT is user-supplied at runtime and kept in the user's browser `localStorage` (`index.html` lines ~868–896), never in the repo; no deploy environments exist | No repo- or environment-held secrets to scope. |
| 41 | Preview environments per PR | **Not applicable** | No deployment target exists | Nothing to preview-deploy; revisit if the page is hosted. |
| 42 | Hot-reload / watch mode | **Not applicable** | No build step; browser refresh gives instant feedback | Watch tooling adds nothing over refreshing a static file. |
| 43 | Structured logging (JSON) | **Not applicable** | Client-side page; no production log stream | No owned log pipeline. |
| 44 | Observable traces and metrics | **Not applicable** | No production runtime owned by this repo | No instrumentation surface. |
| 45 | Feature flags with local overrides | **Not applicable** | Single-purpose internal dashboard with no release gating | No runtime toggling need. |
| 46 | Database migration tooling | **Not applicable** | No database or schema | Nothing to migrate. |
| 47 | Dependency update automation | **Met** | Org-wide Wiz coverage for verified Pattern repos | — |
| 48 | Reproducible builds (lockfiles) | **Met** | No build step; the only dependency is exactly pinned with integrity verification: `chart.js@4.5.0` via jsDelivr with `integrity="sha384-..."` and `crossorigin="anonymous"` (`index.html` line 7) | Pinned version plus SRI hash makes the page's dependency load byte-reproducible. |

## Documentation & Context (agent dispatch)

| # | Practice | Status | Evidence | Recommendation / rationale |
|---|----------|--------|----------|----------------------------|
| 49 | Agent-dispatch manifest (`.agents/pattern-agents.json`) | **Gap** | No `.agents/` directory | Add `.agents/pattern-agents.json` with the GitHub slug, owning team Slack channel, and ClickUp list so dispatched agents can self-configure. |

## Prioritized recommendations

1. **[S] Gap — README (item 6, critical gate):** Add `README.md` covering what the dashboard is, how to open/serve `index.html`, how to create and enter an Asana Personal Access Token, and that the token lives only in browser `localStorage`.
2. **[S] Gap — AGENTS.md (item 2, critical gate):** Add `AGENTS.md` documenting the single-file architecture, Asana API usage, the loader/tab/chart structure, and how to verify changes (open in browser; no build).
3. **[M] Gap — unit tests (item 23, critical gate):** Extract the pure stats/filtering/date logic from the inline script into a testable module and cover it with unit tests (Vitest or similar).
4. **[M] Gap — required CI (item 16, critical gate):** Add a GitHub Actions workflow running lint + tests on PRs and configure it as a required status check on `main` (the org ruleset currently requires review only).
5. **[S] Gap — CODEOWNERS (item 9):** Add `.github/CODEOWNERS` so the owning team is auto-assigned as reviewer.
6. **[S] Gap — formatter (item 11):** Adopt Prettier for the HTML/CSS/JS in `index.html`.
7. **[S] Gap — pre-commit hooks (item 13):** Wire the formatter/linter into pre-commit.
8. **[S] Gap — commit conventions (item 14):** Adopt Conventional Commits and document it.
9. **[S] Gap — agent-dispatch manifest (item 49):** Add `.agents/pattern-agents.json`.
10. **[S] Gap — coverage threshold (item 29):** Enforce a coverage floor in CI once unit tests exist.
11. **[M] Gap — linters (item 10):** Add ESLint (HTML-aware) over the inline script; also worth flagging the 26 `innerHTML` assignments that interpolate Asana task/user data — prefer `textContent` or sanitization to reduce XSS exposure.
12. **[M] Gap — E2E tests (item 27):** Add Playwright flows (token entry, tabs, charts) against mocked Asana responses.
13. **[M] Gap — deterministic fixtures (item 34):** Record fixed Asana API response fixtures for tests.
14. **[M] Gap — structured CI output (item 33):** Emit JUnit-style results from the CI test steps.
15. **[M] Gap — visual regression (item 28):** Add screenshot diffs for the dashboard tabs on top of the Playwright suite.

## Declined practices

| # | Practice | Rationale |
|---|----------|-----------|
| 1 | Skills / reusable prompt workflows | No recurring multi-step tasks in a one-file static page. |
| 3 | Architecture decision records | No evolving architecture; key decisions fit in README/AGENTS.md. |
| 4 | Runbooks | No deployment, keys, or infrastructure to operate. |
| 5 | API contract docs | Consumes Asana's API; owns no wire contract. |
| 7 | Changelog with migration notes | No releases or external consumers to migrate. |
| 8 | On-call playbooks | No production service or incident scope. |
| 12 | Type checking | No modules or build step; would require restructuring beyond the repo's scope. |
| 17 | Dependency allow/deny lists | No package manager; one pinned CDN script. |
| 18 | License compliance scanning | No dependency manifest to scan. |
| 21 | Max complexity limits | No lint infrastructure; low value for a self-contained page. |
| 22 | Import boundary enforcement | No modules or imports. |
| 24 | Integration tests | No owned backend/database/service composition; E2E is the meaningful check. |
| 25 | Snapshot / golden-file tests | No serialized output distinct from the rendered UI. |
| 26 | Contract tests | Third-party SaaS provider (Asana); no consumer-provider pact possible. |
| 30 | Mutation testing | Disproportionate for repo size and risk. |
| 31 | Load / performance benchmarks | Static page; no owned server load surface. |
| 32 | Flaky test quarantine | No test suite or CI volume. |
| 35 | Smoke tests for deploys | Nothing is deployed from this repo. |
| 36 | Devcontainer config | No toolchain; page opens directly in a browser. |
| 37 | One-command setup | Zero setup required. |
| 38 | Seed scripts | No database. |
| 40 | Scoped secrets per environment | No committed credentials or deploy environments; PAT is user-supplied at runtime and stays in browser localStorage. |
| 41 | Preview environments per PR | No deployment target exists. |
| 42 | Hot-reload / watch mode | Browser refresh of a static file suffices. |
| 43 | Structured logging | No production log stream. |
| 44 | Traces and metrics | No owned production runtime. |
| 45 | Feature flags | Single-purpose internal tool; no release gating. |
| 46 | Database migration tooling | No database or schema. |

## Beyond the checklist

- The single CDN dependency is exactly pinned (`chart.js@4.5.0`) and protected with a Subresource Integrity hash plus `crossorigin="anonymous"` — better supply-chain hygiene than many built projects achieve.
- The Asana PAT is never committed or transmitted to any first-party server: it is entered by the user, kept in `localStorage`, validated against the Asana API, and there is an explicit disconnect flow that clears it (`index.html` ~lines 868–896).
- The dashboard caches expensive Asana board-task counts in `localStorage` with timestamps to limit API load.
- The repo is onboarded to Backstage (`backstage.yaml`) with cost-center, environment, and ownership labels.
