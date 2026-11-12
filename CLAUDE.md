# qa-automation-lab

Technical QA lab. App under test: OWASP Juice Shop, running locally in Docker.
Three tracks: automation (Playwright + TypeScript), performance (k6), security (ZAP baseline + API authorization tests).
Long term, AI agents automate the QA flows around them: CI failure triage, performance regression analysis and
security alert triage. `qa-mentor` explains concepts and runs knowledge checks while the work gets done.

## Language
- Talk to the learner in Rioplatense Spanish; technical terms stay in English.
- Everything committed to the repo is in English: code, docs, ADRs, PR descriptions, commit messages.
  Exception: the mentor's prompts in `.claude/` stay in Spanish, the language the mentor speaks.

## Verify
- Start the app: `docker compose up -d` (Juice Shop at http://localhost:3000)
- Tests: `npx playwright test` · Lint: `npm run lint` · Types: `npx tsc --noEmit`
- Performance: `k6 run perf/<script>.js` · Security: see `.github/workflows/security.yml`

## Rules
1. Learning first. Code marked `TODO(human)` is written by the learner. Do not fill it in, even if asked casually;
   give hints, smaller steps or an example on a different case instead. If the learner explicitly asks for the solution,
   give it and note it in the PR description ("written with AI").
2. Plan before code. Every module starts by updating its plan in `docs/` and ends by comparing plan vs. result.
   Any change of direction gets an ADR in `docs/adr/`.
3. Agents: a deterministic script computes, the AI only interprets. AI agents are read-only and write one
   fixed-format file; only reviewed scripts publish anything (PR comments, issues).
4. Every agent has an evaluation set with known answers in `agents/<name>/eval/`. No agent ships without it.
5. Security testing only against the local Juice Shop container. Never the public demo, real sites or third-party
   APIs. Active scans run only on the learner's local machine.
6. No secrets in the repo. Claude runs in CI with the `CLAUDE_CODE_OAUTH_TOKEN` secret only.
7. No third-party proprietary material: no code, prompts, data or client names from other organizations.
8. Technical content only: no personal data, CV material, interview notes or learning scores in this repo.
9. `main` is protected. Work in branches, open a PR, merge only with green checks.
