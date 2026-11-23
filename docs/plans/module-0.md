# Module 0 plan: Planning

## Goal
Define a risk table for the Juice Shop features, scored by impact and likelihood, to decide which feature has the highest priority, whether to automate it or test it manually, and which features are more or less likely to fail. Delivered in `docs/test-strategy.md` v1.

## Deliverables
- `docs/test-strategy.md` v1
- ADR-001 (Juice Shop as app under test)

## Steps
1. Write the risk ranking for the 5 features (impact × likelihood) in `docs/test-strategy.md`.
2. Write the reasoning for each feature: why, trigger and test level.
3. Write the tie-break rules and the test level definitions.
4. Install Docker, start Juice Shop and verify the "to verify" assumptions with DevTools.
5. Write ADR-001: which Juice Shop version to pin, plus `docker-compose.yml`.
6. Open the PR and compare plan vs. result.

## Done when
- All 5 features have Why, trigger and test level filled in `docs/test-strategy.md`.
- `docker compose up -d` starts Juice Shop, `http://localhost:3000` responds, and every "to verify" note in `docs/test-strategy.md` is confirmed, corrected, or explicitly marked as not verifiable.
- `docs/adr/001-*.md` exists with context, decision (the exact Juice Shop version, and how upgrades are handled) and consequences, and `docker-compose.yml` uses that exact version, not `latest`.

## Open questions
Taken from `docs/architecture.md`. Write what you know and mark the rest "to verify".

1. Which risks justify which test level (API vs. UI) for login, basket and checkout?
   - **Checkout (score 9): API + E2E.** API checks what is sent and received (payloads, nulls, invalid data). E2E checks what the user sees, with one full journey because E2E is the most fragile level. Two levels because it has the highest score.
   - **Add to basket (score 6): API + E2E.** API: add an item, change quantity, check the total and that each user has their own basket (to verify). E2E: one journey where the user adds a product and sees it in the basket.
   - **Login (score 3): API + 1 E2E.** A cheap API smoke test that always runs (status 200 and a token) detects a failure early. One E2E for the user's view. Login is also used as setup via API in other tests. Third-party providers: none in the login request (verified with DevTools).
2. What goes into the PR gate vs. the scheduled runs?
   - **PR gate:** the login API smoke test and the checkout API tests. They are fast and check the health of the project: they make sure the basics work and do not cause a bigger problem.
   - **Scheduled (nightly):** the full E2E suite and the k6 load test. They are slow and look deeper for anything that slipped through, keeping the product consistent over time.
   - Criteria: speed and score from the risk table.
3. Where does the non-local `BASE_URL` check live so every track uses the same one?
   - The value comes from a single environment variable, `BASE_URL` (default `http://localhost:3000`).
   - A single shared guard checks it. If the host is not local, it stops with an error before anything runs and no request is sent.
   - Every track (Playwright, k6, ZAP) calls the guard before starting. How it is built is decided in Module 4.
4. Naming conventions: ADRs, test files, tags.
   - ADRs: `docs/adr/NNN-short-title.md`
   - Plans: `docs/plans/module-N.md`
   - Test files: `<feature>.spec.ts`
   - Tags: `@smoke` (fast critical subset, PR gate) and `@regression` (the rest, scheduled runs)
5. Which Juice Shop version to pin, and how upgrades are handled (ADR-001).
   - Pinned version: `bkimminich/juice-shop:v20.4.0` (latest release found on GitHub; the tag was verified with `docker pull`).
   - Upgrade handling: decided in ADR-001 (see its "Upgrade policy" section).

## Plan vs. result
Filled in at the end of the module.

Notes so far:
- The "done when" criterion for step 4 changed from "confirmed or corrected" to also allow "explicitly marked as not verifiable". Assumptions about third-party code cannot be verified, and the pinned version controls when that code changes.
- Verification corrected two assumptions: About Us is not static text (it shows server data), and the basket likelihood no longer relies on "code in constant movement" because the version is pinned.
