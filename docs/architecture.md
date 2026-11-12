# Architecture

**Status:** Draft — input for Module 0. Each decision here is reviewed in Module 0 and either confirmed
in `docs/test-strategy.md` / an ADR, or changed (with an ADR explaining why).

## Goals

1. **Professional quality:** the repo should read like a real QA platform, not a collection of scripts.
2. **Scalable by design:** adding a test, a module, a workflow or an agent should not require rewriting what exists.
3. **Safe by default:** the rules in `CLAUDE.md` are enforced by code where possible, not by memory.

## Repository layout

```
qa-automation-lab/
├── docs/
│   ├── architecture.md        this file
│   ├── test-strategy.md       Module 0
│   ├── plans/module-N.md      one plan per module, compared against the result at the end
│   └── adr/NNN-title.md       decisions and changes of direction
├── tests/
│   ├── api/                   Module 2
│   └── ui/                    Module 3
├── perf/                      k6 scripts, Module 6
├── security/                  ZAP configuration, Module 7
├── agents/
│   └── <name>/
│       ├── script             deterministic: computes
│       ├── prompt.md          AI: interprets
│       └── eval/              cases with known answers
├── .github/workflows/         tests.yml, perf.yml, security.yml
├── docker-compose.yml         local Juice Shop
└── reports/                   all generated output (git-ignored)
```

## Guardrails

Each guardrail exists to prevent a specific, predictable failure.

| # | Guardrail | Failure it prevents |
|---|-----------|---------------------|
| 1 | Juice Shop image pinned to an exact version, never `latest` | An upstream release changes an endpoint and tests fail with no change on our side |
| 2 | A single `BASE_URL` setting, defaulting to `http://localhost:3000`; tooling refuses to run against non-local hosts | Accidentally testing or scanning the public demo or a real site (rule 5) |
| 3 | Every test creates its own data (e.g. registers its own user via API) | Tests that pass alone but fail in parallel because they share state |
| 4 | Tests talk to the app only through one layer (API client / Page Objects) | One endpoint or selector change forcing edits in dozens of files |
| 5 | Agents follow a fixed contract: `script → JSON → AI → fixed-format file → validation → reviewed script publishes` | Hallucinated or malformed AI output reaching a PR comment or issue |
| 6 | All generated output goes to `reports/` | Reports (which may contain tokens) scattered around and committed by mistake |

## Scalability principles

- **Tracks are independent.** API, UI, performance and security can each run on their own, locally and in CI.
  A broken perf script must not block a UI fix.
- **Parallel-safe by default.** Because of guardrail 3, tests can be sharded across CI workers as the suite grows.
- **Tiered quality gates.** A fast subset (e.g. `@smoke`) runs on every PR; the full suite, performance and
  security run on a schedule or before release. Gate thresholds live in config, not inside test code.
- **Agents share one pattern.** A new agent is a copy of the same folder shape and the same output contract;
  it ships only when its eval set passes (rule 4).
- **Configuration over code.** Environment-specific values (URLs, thresholds, credentials for the local app)
  come from environment variables or config files, never hard-coded.

## Engineering principles

Checklist used in every review. Each item names the failure it prevents.

### Repository and workflow
- [ ] Conventional Commits and small, single-purpose PRs — large PRs don't get a real review.
- [ ] PR template: plan updated, risks covered, plan vs. result, "written with AI" when it applies.
- [ ] Secret scanning and push protection enabled — a leaked token is blocked before it is published.
- [ ] Lockfile committed; Dependabot opens update PRs that go through CI — upstream breakage shows up as a red PR.
- [ ] GitHub Actions pinned by commit SHA, not tag — a compromised action tag cannot inject code
      (the `tj-actions/changed-files` incident, March 2025).

### Test automation
- [ ] One behavior per test, one reason to fail — a failing test says what broke.
- [ ] No fixed waits; web-first assertions only — fixed waits pass locally and flake in CI.
- [ ] Flaky tests are quarantined with an issue, not retried until green — retries can hide real race conditions.
- [ ] Traces, screenshots and videos uploaded as CI artifacts on failure — failures are diagnosable without reproducing them.

### Performance
- [ ] Percentiles (p95, p99), never averages — an average hides the slow tail.
- [ ] Thresholds are code and fail the run — a slow result without a threshold is just a number.
- [ ] Error rate checked together with latency — a fast `500` is not a fast endpoint.
- [ ] Results compared against a baseline from the same environment — shared CI runners are noisy.

### Security
- [ ] Layered checks: SAST (CodeQL), dependencies (Dependabot / `npm audit`), DAST (ZAP) — each finds what the others miss.
- [ ] Accepted risks are documented in the ZAP rules file with a reason — nothing is silenced without a trace.
- [ ] Authorization tested with negative cases (user A cannot read user B's data) — IDOR / broken access control.
- [ ] Workflows default to `permissions: contents: read`; extra permissions per job only — limits the blast radius of a compromised step.

### AI agents
- [ ] Simplest thing that works: a deterministic workflow before an autonomous agent.
- [ ] Every input is untrusted (CI logs, ZAP alerts, HTTP bodies) — prompt injection is the top LLM risk.
- [ ] Agents are read-only, hold no secrets and write one file; only reviewed scripts publish.
- [ ] Structured output validated against a schema; invalid output publishes nothing (fail closed).
- [ ] Eval sets act as prompt regression tests, including tricky cases; they run on every prompt or model change.
- [ ] Model version pinned; each run logs input, output, model and cost to `reports/`.
- [ ] Hard limits: timeout, max tokens, max runs per PR.

## Open questions for Module 0

- Which risks justify which test level (API vs. UI) for login, basket and checkout?
- What exactly goes into the PR gate vs. the scheduled runs?
- Where does the non-local `BASE_URL` check live so every track uses the same one?
- Naming conventions: plans, ADRs, test files, tags.
- Which Juice Shop version to pin, and how upgrades are handled (ADR-001).
