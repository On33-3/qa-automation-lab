# ADR-001: Juice Shop as the app under test

## Status
Accepted

## Context
The lab needs an app under test that runs locally, is safe to attack because we control it, and has features that can be tested, such as login, basket, checkout and product search.

## Decision
- App under test: OWASP Juice Shop, running locally in Docker.
- Pinned version: `bkimminich/juice-shop:v20.4.0` (the tag was verified with `docker pull`). Never `latest`.
- Why pin: it prevents sudden, uncontrolled changes, such as tests failing with no change on our side. A stable version also gives every track (performance, security, E2E) the same app to work on, so results can be compared and reproduced.

## Upgrade policy
- The Juice Shop version is only changed through a pull request.
- The PR gate runs before merging. A version change raises the likelihood of every feature to 3, so the full suite also runs on that PR.
- If tests fail, read the logs and decide: is it a contract change or a bug in the new version?
  - Contract change: adapt the test layer (API client / Page Objects) in one place.
  - Bug in the new version: do not merge, keep the old pinned version.
- Nothing is merged while the checks are red.

## Consequences
- Good: it is simpler to test and to manipulate the data, so testing needs fewer external dependencies.
- Cost or risk: tests may need fixing when a new version changes features or flows, or adds new integrations. Juice Shop is also more of a playground than a real production app, so it is less realistic.
