# Facilitation Guide

This guide is for coaches running the GitHub Copilot and Squad Developer Workflow Hackathon. Read it end to end before the event. Better yet, run every challenge yourself at least once so you know where the rough edges are.

## Event Overview

Four hours, seven challenges, one recipe-sharing application built on .NET 10 and React. Participants will go from zero to a fully operational AI-augmented development workflow: assembling a Squad of agents, shipping a feature, enforcing quality, generating tests, debugging under pressure, and configuring autonomous operations.

The audience is L400 -- these are experienced developers. They do not need hand-holding on C# or TypeScript fundamentals. What they need is guidance on Squad concepts, Copilot interaction patterns, and the "why" behind each workflow step.

## Suggested Agenda

| Time | Activity | Notes |
|---|---|---|
| 0:00 - 0:05 | Welcome, introductions, share repo link | Keep it short. Drop the repo URL in chat immediately. |
| 0:05 - 0:25 | Challenge 00: Base Camp | Walk the room. Catch auth issues early. |
| 0:25 - 0:50 | Challenge 01: Assemble Your Squad | First real interaction with Squad. Excitement is high -- let them explore. |
| 0:50 - 1:25 | Challenge 02: Ship a Feature | The meaty one before break. Check in at the 20-minute mark. |
| 1:25 - 1:35 | Break (10 min) | Hard stop. People will want to keep going -- pull them away. |
| 1:35 - 2:05 | Challenge 03: Quality Gates | Hooks and linting. Some find this tedious -- frame it as "protecting your future self." |
| 2:05 - 2:35 | Challenge 04: Test Coverage Blitz | Tester agent does heavy lifting. Coach participants to review, not just accept. |
| 2:35 - 3:10 | Challenge 05: Break-Fix Under Pressure | Inject some energy here. This is the "production is down" simulation. |
| 3:10 - 3:45 | Challenge 06: Autonomous Operations | The capstone. Docker must be running. Check early. |
| 3:45 - 4:00 | Wrap-up, Q&A, feedback | Ask "What surprised you?" -- gets better answers than "Any questions?" |

The timing has about 15 minutes of slack built in across the full event. If you fall behind, the first place to compress is Challenge 03 (cut the advanced challenge) or Challenge 06 (skip Aspire dashboard setup and just demo it).

## Pre-Event Setup (Do This the Day Before)

1. **Ensure the sample app repo is accessible.** Participants will fork [seiggy/copilot-squad-recipe-app](https://github.com/seiggy/copilot-squad-recipe-app) to their own GitHub accounts and clone their forks. Verify the .NET 10 backend builds and the React frontend starts. Remind participants that a fork (not a direct clone) is required -- Challenge 06's Ralph watch mode needs write access to create issues and PRs.
2. **Test Codespaces.** Open the sample app repo in a fresh Codespace. Time the postCreateCommand. If it takes more than 3 minutes, optimize the Dockerfile.
3. **Pre-create 3 GitHub Issues** for Challenge 06 (Ralph autonomous triage). Label them bug, enhancement, and chore. Write realistic titles and descriptions.
4. **Run every challenge yourself.** All seven. Time yourself. Note anything that feels unclear or breaks.
5. **Prepare a backup plan.** If GitHub Copilot is experiencing an outage, have Azure OpenAI API keys ready as a BYOK fallback. Test the fallback path too.
6. **Print or bookmark the scoring rubric.** You will use it during the wrap-up.
7. **Check port availability.** The app uses 5000 (API), 5173 (React dev server), and 18888 (Aspire dashboard). If running in Codespaces, port forwarding handles this. Locally, make sure nothing else claims those ports.

## Per-Challenge Coach Notes

---

### Challenge 00: Base Camp - Environment Setup

**Time:** 20 minutes | **Difficulty:** Setup

**What to watch for:**
- gh auth login is the number-one blocker. Participants forget to select the right scopes, or their token is expired. Walk the room during this challenge.
- .NET 10 SDK version mismatches. If participants installed .NET 10 preview months ago, they might have a stale version. dotnet --version should show 10.x.
- Node.js version too old. node --version must be 22+. If they are on an older version, nvm use 22 fixes it.
- Codespaces users should sail through this in 10 minutes. Use the extra time to help bare-metal users.

**How to verify success:**

```bash
dotnet --version        # 10.x
node --version          # v22+
gh --version            # any recent
gh auth status          # logged in with correct scopes
gh copilot --version    # confirms Copilot CLI installed
```

**When to intervene:** If someone is stuck on auth for more than 5 minutes, sit with them and debug it. Do not let anyone fall behind on Challenge 00 -- it poisons every subsequent challenge.

**Key talking point:** "Challenge 00 is never exciting, but every minute you invest here saves you ten minutes later. Trust the process."

---

### Challenge 01: Assemble Your Squad

**Time:** 25 minutes | **Difficulty:** Easy

**What to watch for:**
- Participants who skip reading the generated agent files and just say "it works." Push them to open .github/agents/ and read the definitions. Understanding what Squad created is the whole point.
- Routing customization is where it gets interesting. Some participants will over-engineer the routing rules. Remind them: start simple, refine later.
- If @squad is not recognized in Copilot Chat, the .github/agents/ directory is missing or malformed. Check the file paths.

**How to verify success:**

```bash
ls .github/agents/       # should list agent definition files
cat .github/agents/*.md  # readable agent definitions exist
# Ask participant to demo: type @squad in Copilot Chat and show agent suggestions
```

**Common mistakes:**
- Editing agent files with syntax errors (bad YAML frontmatter). If an agent stops responding, check the file for formatting issues.
- Confusing Squad initialization with Copilot Chat setup. Squad is an additional layer on top of Copilot, not a replacement.

**Questions to ask participants:**
- "Which agent would you ask to review a pull request? Why?"
- "How would you change the routing if you wanted all database questions to go to a specific agent?"

---

### Challenge 02: Ship a Feature

**Time:** 35 minutes | **Difficulty:** Medium

**What to watch for:**
- This is the first challenge where participants build real code with agents. Some will try to write everything themselves and use agents as spell-check. Push them to delegate more.
- Parallel agent execution is a key concept. If a participant is running agents sequentially (finish backend, then start frontend), ask: "Could your frontend agent start on the React component while the backend agent writes the API endpoint?"
- Decision recording (the decisions.md or similar file) is easy to skip. It matters. Decisions made by agents should be traceable.
- All work happens in the sample app repo (`copilot-squad-recipe-app/`), not in the challenge instructions repo.

**How to verify success:**

```bash
dotnet build             # backend compiles
npm run build            # frontend compiles
dotnet run &             # API starts
npm run dev &            # frontend starts
# Navigate to the favorites feature in the browser -- it should work end to end
```

**Common mistakes:**
- Agent generates code that references packages not in the project. Participant needs to install the NuGet package or npm package before the code compiles.
- React component created but not wired into the router. The feature "works" in isolation but is unreachable from the UI.
- Forgetting to run database migrations if the feature adds a new table or column.

**When to intervene:** At the 20-minute mark, do a room check. Anyone who has not started on the frontend component yet is behind. Suggest they ask the frontend agent to scaffold the component while they review the backend agent's work.

**Key talking point:** "The value of Squad is not that it writes code faster. It is that it writes code in parallel while you stay in the driver's seat reviewing and steering."

---

### Challenge 03: Quality Gates - Hooks and Linters

**Time:** 30 minutes | **Difficulty:** Medium

**What to watch for:**
- Three layers of enforcement: git hooks (pre-commit), Copilot hooks (agent output validation), and linter configuration. Participants need to get all three working.
- Git hooks are fragile. If the hook script is not executable (chmod +x), it silently does nothing. Watch for this.
- Some participants will configure linters so strictly that their own code from Challenge 02 fails. That is fine -- it is a teaching moment about incremental adoption.

**How to verify success:**

```bash
# Attempt a commit with a linting violation -- it should be blocked
echo "var x = 1" > test.js && git add test.js && git commit -m "test"
# Should fail due to pre-commit hook

# Check Copilot hook configuration exists
cat .copilot/hooks.json  # or equivalent config file

# Run linters manually
dotnet format --verify-no-changes
npx eslint src/
```

**Common mistakes:**
- Installing husky but not running npx husky install (or the init command for newer versions). The hooks directory does not get created.
- Writing a Copilot hook that is too aggressive and blocks legitimate agent output. Start permissive, tighten later.
- Forgetting to add the hook scripts to source control. Hooks are local by default -- you need husky or a similar tool to share them.

**If participants find this tedious:** Acknowledge it. "Nobody loves configuring linters. But three months from now, when an agent tries to commit code with a SQL injection vulnerability, this hook is what saves you." Reframe it as defensive engineering.

**Key talking point:** "Agents are fast but they are not infallible. Quality gates are your safety net."

---

### Challenge 04: Test Coverage Blitz

**Time:** 30 minutes | **Difficulty:** Hard

**What to watch for:**
- The Tester agent will generate a lot of tests quickly. The risk is that participants accept them all without reading them. Push back on this. Ask: "What is this test actually verifying?"
- 80% coverage is the target. Participants who obsess over 100% will run out of time. Coach them toward meaningful coverage, not vanity numbers.
- The SKILL.md file is a core deliverable. It teaches the Tester agent project-specific testing conventions. Participants who write a thin SKILL.md will get generic tests. Participants who invest in it will get tests that match their project's patterns.

**How to verify success:**

```bash
dotnet test --collect:"XPlat Code Coverage"   # check .NET coverage
npx vitest run --coverage                            # check React coverage
# Both should show 80%+ line coverage

cat SKILL.md   # or the project-specific skill file path
# Should contain testing conventions, not boilerplate
```

**Common mistakes:**
- Generated tests that compile but test nothing meaningful (e.g., expect(true).toBe(true)). If you see these, have the participant ask the Tester agent to write assertion-rich tests.
- Coverage report not generated because the coverage tool is not installed. coverlet.collector NuGet package is needed for .NET; vitest run --coverage needs no extra install but the vitest config must not suppress it.
- SKILL.md written as a wishlist instead of concrete instructions. "Write good tests" is not a skill. "Use xUnit with FluentAssertions, arrange-act-assert pattern, one assertion per test" is a skill.

**Questions to ask participants:**
- "If you deleted the SKILL.md, how would the generated tests change?"
- "Which of these generated tests would you actually trust in a production codebase?"

---

### Challenge 05: Break-Fix Under Pressure

**Time:** 35 minutes | **Difficulty:** Hard

**What to watch for:**
- Three pre-planted bugs. Participants need to find and fix all three using agents. The temptation is to read the code manually and fix it themselves. Encourage them to use the Debugger or Fixer agent to diagnose the issue first.
- The /remote challenge (running an agent from a phone or tablet) is the crowd-pleaser. It is also the most likely to hit infrastructure issues. devtunnel authentication is required. If a participant cannot get devtunnel working, let them skip /remote and focus on the bug fixes.
- Regression tests are required for each fix. A fix without a test is not done.

**How to verify success:**

```bash
dotnet test              # all tests pass, including new regression tests
npm test                 # all tests pass
# Run the app and verify each of the 3 bugs is fixed
# Check git log for 3 separate fix commits with associated test commits
```

**Common mistakes:**
- Fixing the symptom but not the root cause. Agent suggests a null check that hides a deeper issue. Ask: "Why was it null in the first place?"
- Regression test that only covers the happy path. The bug was an edge case -- the regression test should hit the edge case.
- devtunnel not authenticated. Run devtunnel user login and make sure it succeeds before attempting /remote.
- Port forwarding confusion. The tunnel needs to forward to the correct local port (5000 for API, 5173 for frontend).

**Energy tip:** This challenge simulates a production incident. Play it up. "Your pager just went off. Three bugs in production. Clock is ticking." A little drama keeps the energy high in the back half of the event.

**When to intervene:** If someone has been stuck on the same bug for 10 minutes, give them a nudge: "Have you asked the agent to check the request pipeline?" or "Look at the error message more carefully -- what type is it expecting?" Do not give the answer directly.

---

### Challenge 06: Autonomous Operations

**Time:** 35 minutes | **Difficulty:** Expert

**What to watch for:**
- Docker must be running for the Aspire dashboard. If it is not, this surfaces immediately. Check at the start of the challenge, not the end.
- Ralph watch mode is the centerpiece. It monitors the repo and triages issues autonomously. Participants need to see it pick up at least 2 of the pre-created issues and propose actions.
- SDK governance hooks are the advanced piece. These are programmatic hooks (TypeScript/C# in squad.config.ts or equivalent) rather than markdown configuration. This is where the L400 audience earns their rating.

**How to verify success:**

```bash
docker ps                # Aspire dashboard container running
curl http://localhost:18888  # Aspire dashboard responds

# Check Ralph watch mode is active
# Look for agent activity in the terminal or logs

# Verify governance hooks
cat squad.config.ts      # or equivalent SDK config
# Should contain at least one governance rule
```

**Common mistakes:**
- Docker not running. On macOS, Docker Desktop needs to be launched. In Codespaces, Docker is available but the daemon may need a moment after container start.
- Ralph watch mode starts but immediately exits because there are no issues to triage. Make sure the 3 pre-created issues exist and are open.
- Aspire dashboard port (18888) conflicts with another service. Stop whatever is on that port or change the Aspire config.
- Governance hooks that are syntactically valid but logically empty (no actual rules). Check that the hooks do something observable.

**Key talking point:** "This is where it all comes together. You are not just using agents interactively anymore -- you are setting up an autonomous development operation that runs while you sleep."

---

## Common Blockers Across All Challenges

These issues come up repeatedly. Have answers ready.

| Blocker | Fix |
|---|---|
| gh auth login wrong scopes | Re-run with gh auth login --scopes copilot,repo,read:org |
| Expired GitHub token | gh auth refresh |
| Squad CLI version mismatch | npm install -g @bradygaster/squad-cli@latest (or whatever the current install command is) |
| @squad not recognized in Copilot Chat | Check .github/agents/ exists and files have valid frontmatter |
| Port 5000 in use | lsof -i :5000 to find the culprit, then stop it |
| Port 5173 in use | Same approach as above |
| Port 18888 in use | Same approach as above |
| Docker not running | Start Docker Desktop, or in Codespaces wait 30 seconds for dockerd |
| devtunnel not authenticated | devtunnel user login with GitHub account |
| .NET restore fails | Check NuGet.config, try dotnet nuget locals all --clear |
| npm install fails | Delete node_modules and package-lock.json, run npm install again |

## Handling Teams That Are Ahead

Point them to the Advanced Challenge section at the bottom of each challenge file. If they have finished all advanced challenges:

- Ask them to write a custom 6th agent with a specialized skill (e.g., a Documentation agent that generates OpenAPI specs).
- Have them try SDK-first configuration (squad.config.ts) instead of markdown if they have not already.
- Challenge them to get 95%+ test coverage.
- Ask them to help a neighboring participant who is stuck. Peer coaching reinforces their own learning.

## Handling Teams That Are Behind

The minimum viable path through the hackathon is: 00 -> 01 -> 02 -> 05. This gives participants the core experience (setup, squad assembly, feature shipping, and debugging) while skipping the quality and operations layers.

If a team is behind:
- **Skip Challenge 03** entirely. Quality gates are important but not essential for the learning arc.
- **Compress Challenge 04** to "generate tests for one stack only" (pick .NET or React, not both).
- **Skip Challenge 06** and do a live demo of Ralph watch mode instead.

Never skip Challenge 00, 01, or 02. The rest of the hackathon makes no sense without them.

## Wrap-Up and Retrospective (15 minutes)

Do not skip this. The wrap-up is where learning solidifies.

**Structure:**
1. (5 min) Ask each participant or team: "What surprised you most?" Collect answers on a whiteboard or in chat.
2. (3 min) Quick show of hands: "How many of you will use Squad in your actual projects this week?" Follow up with "What is the first thing you will try?"
3. (3 min) Address any unanswered questions from the challenges.
4. (2 min) Share the scoring results if you used the rubric. Keep it light -- this is a learning event, not a competition.
5. (2 min) Point participants to next steps: Squad documentation, Copilot best practices, community channels.

**Feedback:** Send a short survey (5 questions max) within 1 hour of the event ending. Response rates drop off a cliff after that.
