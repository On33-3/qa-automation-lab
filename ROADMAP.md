# Roadmap

Technical QA lab on OWASP Juice Shop (local, Docker): automation, performance and security,
plus AI agents that automate the QA flows around them.

## Modules

| # | Module | Topics | Deliverable |
|---|--------|--------|-------------|
| 0 | Planning | Risk-based testing, test pyramid, quality gates | `docs/test-strategy.md` v1 + ADR-001 (Juice Shop as app under test) |
| 1 | Git workflow | Branches, PRs, code review, protected `main` | First full PR |
| 2 | API automation | Auth tokens, fixtures, isolated data, contract assertions | Login, basket and checkout API tests |
| 3 | UI automation | Locators, Page Objects, web-first assertions | 3 critical UI journeys |
| 4 | CI/CD | GitHub Actions workflows, artifacts, caching, parallelism, quality gates | `tests.yml`, green badge |
| 5 | Flakiness + `ci-triage` agent | — | `extract_failures.ts`, design doc, eval set of 6 known failures (≥5/6) |
| 6 | Performance + `perf-analyst` agent | k6, p95, thresholds, load model | `perf.yml`, agent flags the degraded endpoint |
| 7 | Security + `security-triage` agent | OWASP Top 10, DAST vs SAST, passive vs active scan, IDOR, dependencies | `security.yml`, agent evaluated against known Juice Shop vulnerabilities |
| 8 | Documentation | README as a technical case study | Architecture diagram, demo GIF |

## How each module works

1. **Plan first** — update the module plan in `docs/` before writing any code.
2. **Learn** — short explanation of the concepts, with an example on Juice Shop.
3. **Knowledge check** — pass with ≥80%.
4. **PR with green CI** — the module's real work lands through a pull request.
5. **Review** — reviewed as a QA lead would.
6. **Compare plan vs. result** — if anything changed, record it in an ADR in `docs/adr/`.
