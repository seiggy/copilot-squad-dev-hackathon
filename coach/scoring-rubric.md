# Scoring Rubric

Use this rubric to evaluate participant progress during and after the hackathon. Total possible: 100 points, plus up to 10 bonus points.

The rubric is designed for coaches to do a quick walk-through with each participant or team during the wrap-up. Most criteria can be verified in under a minute with the check commands listed below.

## Scoring Summary

| Challenge | Points | Weight |
|---|---|---|
| 00: Base Camp | 10 | Foundation |
| 01: Assemble Your Squad | 10 | Foundation |
| 02: Ship a Feature | 20 | Core |
| 03: Quality Gates | 15 | Core |
| 04: Test Coverage Blitz | 15 | Core |
| 05: Break-Fix | 15 | Applied |
| 06: Autonomous Operations | 15 | Capstone |
| **Total** | **100** | |
| Bonus | up to 10 | Extra credit |

---

## Challenge 00: Base Camp - Environment Setup (10 points)

| Criteria | Points | Verification |
|---|---|---|
| .NET 10 SDK installed and `dotnet --version` returns 10.x | 2 | `dotnet --version` |
| Node.js 22+ installed | 2 | `node --version` |
| GitHub CLI authenticated with correct scopes | 2 | `gh auth status` |
| GitHub Copilot CLI functional | 2 | `gh copilot --version` |
| Starter project builds and runs (both backend and frontend) | 2 | `dotnet build && npm run build` |

**Done (10):** All five criteria met.
**Partially Done (5):** Tools installed but starter project does not build, or auth is incomplete.
**Not Done (0):** Environment not functional.

---

## Challenge 01: Assemble Your Squad (10 points)

| Criteria | Points | Verification |
|---|---|---|
| Squad initialized, `.github/agents/` directory exists with agent definitions | 3 | `ls .github/agents/` |
| At least 3 agents are defined and responsive in Copilot Chat | 4 | Participant demos @squad mention and gets agent response |
| Routing customized (at least one custom routing rule added or modified) | 3 | `cat .github/agents/*.md` -- look for non-default routing |

**Done (10):** All agents defined, responsive, and routing customized.
**Partially Done (5):** Agents exist but routing is default/unchanged, or one agent is non-responsive.
**Not Done (0):** Squad not initialized.

---

## Challenge 02: Ship a Feature (20 points)

| Criteria | Points | Verification |
|---|---|---|
| Backend API endpoint for favorites exists and returns data | 5 | `curl http://localhost:5000/api/favorites` (or equivalent) |
| Frontend component renders favorites and allows add/remove | 5 | Visual check in browser |
| Feature works end-to-end (add a favorite in UI, verify it persists via API) | 5 | Manual test in browser + API call |
| Decision log exists documenting agent choices | 3 | Check for decisions file in repo |
| Evidence of parallel agent execution (git log shows interleaved work or participant can describe the workflow) | 2 | `git --no-pager log --oneline -10` or conversation with participant |

**Done (20):** Feature works E2E, decisions recorded, parallel workflow used.
**Partially Done (10-15):** Feature partially works (e.g., backend done but frontend incomplete), or no decision log.
**Not Done (0-5):** Feature does not compile or no meaningful progress.

---

## Challenge 03: Quality Gates - Hooks and Linters (15 points)

| Criteria | Points | Verification |
|---|---|---|
| Git pre-commit hook installed and functional | 5 | Attempt a bad commit -- it should be rejected |
| Copilot hook configuration exists | 5 | Check config file (`.copilot/hooks.json` or equivalent) |
| Linter rules configured for both .NET and TypeScript | 3 | `dotnet format --verify-no-changes` and `npx eslint src/` |
| At least one blocked operation demonstrated (commit rejected, agent output filtered, or lint failure caught) | 2 | Participant shows a screenshot, terminal output, or live demo |

**Done (15):** All three layers configured and at least one blocked operation demonstrated.
**Partially Done (7-10):** One or two layers configured but not all three, or no demonstrated block.
**Not Done (0-5):** No hooks or linters configured.

---

## Challenge 04: Test Coverage Blitz (15 points)

| Criteria | Points | Verification |
|---|---|---|
| .NET test coverage at 80% or higher | 4 | `dotnet test --collect:"XPlat Code Coverage"` -- check report |
| React/TypeScript test coverage at 80% or higher | 4 | `npx vitest run --coverage` -- check report |
| All generated tests pass | 3 | `dotnet test` and `npm test` both green |
| SKILL.md created with project-specific testing conventions | 2 | `cat .squad/skills/testing-conventions/SKILL.md` -- check for concrete, specific conventions |
| Generated tests pass linting rules from Challenge 03 | 2 | `dotnet format --verify-no-changes` and `npx eslint src/` on test files |

**Done (15):** 80%+ coverage both stacks, SKILL.md created, tests pass and pass linting.
**Partially Done (7-10):** Coverage below 80% in one stack, or SKILL.md is generic/missing, or tests fail linting.
**Not Done (0-5):** No meaningful test coverage generated.

---

## Challenge 05: Break-Fix Under Pressure (15 points)

| Criteria | Points | Verification |
|---|---|---|
| Bug 1 identified and fixed | 3 | Code review of the fix + `dotnet test` passes |
| Bug 2 identified and fixed | 3 | Code review of the fix + `npm test` passes |
| Bug 3 identified and fixed | 3 | Code review of the fix + full test suite passes |
| Regression test exists for each fix (3 tests minimum) | 4 | `git --no-pager log --oneline` -- look for test commits paired with fix commits |
| No regressions introduced (full test suite still passes) | 2 | `dotnet test && npm test` -- all green |

**Done (15):** All 3 bugs fixed, regression tests exist for each, no regressions.
**Partially Done (7-10):** 1-2 bugs fixed, or fixes lack regression tests.
**Not Done (0-5):** No bugs fixed or fixes introduce new failures.

---

## Challenge 06: Autonomous Operations (15 points)

| Criteria | Points | Verification |
|---|---|---|
| Ralph watch mode running and active | 4 | Terminal or logs show Ralph monitoring activity |
| At least 2 pre-created issues triaged by Ralph (comments, labels, or proposed PRs) | 4 | Check GitHub Issues for agent activity |
| SDK governance hooks configured in squad.config.ts (or equivalent) | 4 | `cat squad.config.ts` -- at least one governance rule present |
| Aspire dashboard visible and showing telemetry | 3 | `curl http://localhost:18888` -- dashboard responds; visual check shows traces |

**Done (15):** Ralph running and triaging, governance hooks active, Aspire dashboard showing data.
**Partially Done (7-10):** Ralph running but not triaging, or Aspire not configured, or governance hooks are empty shells.
**Not Done (0-5):** No autonomous operations set up.

---

## Bonus Points (up to 10 extra)

| Criteria | Points | Verification |
|---|---|---|
| All 3 bug fixes in Challenge 05 performed via /remote from a phone or tablet | +3 | Participant demonstrates or shows devtunnel logs |
| SDK-first configuration (squad.config.ts) used instead of markdown config throughout | +3 | `cat squad.config.ts` -- configuration is programmatic, not just a wrapper around markdown |
| Custom 5th agent added to the Squad with a defined role and working routing | +2 | `ls .github/agents/` shows extra agent; participant demos it responding |
| Testing SKILL.md promoted to medium confidence (agent consistently follows its conventions) | +2 | Run test generation twice -- both outputs follow SKILL.md conventions |

---

## Overall Scoring Scale

| Score | Rating | Description |
|---|---|---|
| 90-110 | Outstanding | Completed all challenges including advanced work and bonus items. Ready to teach others. |
| 75-89 | Strong | Completed most challenges fully. Solid understanding of Squad workflows. |
| 60-74 | Proficient | Completed core challenges. Some gaps in advanced topics. Good foundation to build on. |
| 40-59 | Developing | Completed setup and basics. Needs more practice with the harder challenges. |
| Below 40 | Incomplete | Did not get through enough material. May need to revisit prerequisites or try a shorter format. |

## How to Use This Rubric

1. During the event, do informal check-ins at each challenge transition. You do not need to score every criterion in real time -- just note who is on track and who is falling behind.
2. During the wrap-up (last 15 minutes), walk through each team's environment and run the verification commands. This takes 2-3 minutes per team.
3. Share scores lightly. This is a learning event. Use scores to identify who might want follow-up resources, not to rank participants against each other.
4. If you run this as a competition, use the Outstanding/Strong/Proficient tiers for awards rather than raw point totals. It feels better and avoids arguments about partial credit.
