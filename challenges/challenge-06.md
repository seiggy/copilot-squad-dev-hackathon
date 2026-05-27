# Challenge 06: Autonomous Operations

**Estimated time:** 35 minutes | **Difficulty:** Expert (L400)

## Introduction

You have been in the driver's seat for every challenge so far -- setting up your squad, building features, enforcing quality, writing tests, fixing bugs. That ends now.

In this final challenge, you step back and let the machines run. Ralph watches your GitHub Issues and dispatches agents to handle them without you typing a single command. Governance hooks enforce policy so agents stay inside the lines even when nobody is looking. And the .NET Aspire Dashboard gives you real-time telemetry so you can see exactly what your agents are doing, how many tokens they burn, and how long each task takes.

This is the difference between using AI as a tool and running AI as a team. You are building the operational layer -- the part that lets a squad function overnight, over a weekend, or across time zones without a human babysitting every pull request.

Three tracks. One converging test at the end.

## Prerequisites

- Challenge 05 completed (bugs fixed, tests passing, quality gates working)
- Docker available in your environment (for the Aspire Dashboard)
- GitHub CLI authenticated with `repo` scope (`gh auth status` to verify)

## Description

You need to bring up three capabilities in parallel, then prove they work together in a single end-to-end cycle.

### Track A: Ralph Watch Mode

Ralph is Squad's autonomous operations daemon. When running in watch mode, it polls your GitHub repository for new issues, builds a context snapshot, and dispatches the right agent to handle each one.

> **Reminder:** Ralph needs to create labels, assign agents, and open PRs on your repository. This is why you forked the sample app in Challenge 00 instead of cloning it directly. Make sure you are working in your fork and that `gh auth status` shows write access to your forked repo.

Get Ralph running in watch-and-execute mode against your forked repository (See: https://bradygaster.github.io/squad/docs/features/ralph/#watch-mode-squad-watch). Set it to poll every 5 minutes with execution enabled. Then create three distinct GitHub Issues on your fork:

1. A **feature request** -- something small and concrete that an agent can actually implement (a new utility function, an API endpoint, a config option)
2. A **bug report** -- describe a real or plausible defect with reproduction steps
3. A **documentation task** -- a missing README section, an undocumented function, a stale comment

Watch Ralph triage each issue. It should pick them up, assign them to agents, and start working. At least one of those issues needs to reach a working resolution -- actual code changes committed or a PR opened.

### Track B: SDK Governance Hooks

Autonomous agents need guardrails. Using the Squad SDK, build a governance hook pipeline that enforces three policies:

1. **File guards** -- Agents may only write to `src/RecipeHub.Api/`, `src/RecipeHub.Web/`, and `.squad/` directories. Any attempt to modify files outside those paths gets blocked.
2. **PII scrubbing** -- Email addresses, phone numbers, and similar personally identifiable information in agent output must be redacted before it hits any log or commit message.
3. **Reviewer lockout** -- If an agent reviewed a file (approved or commented on a PR touching that file), it cannot subsequently edit that same file. Separation of duties, enforced by code.

Wire these hooks into your Squad configuration so they apply to all agent operations. Verify they actually fire -- trigger a blocked operation and confirm the pipeline rejects it.

### Track C: Aspire Observability

Spin up the .NET Aspire Dashboard and point Squad's OpenTelemetry exporter at it. The dashboard runs as a Docker container and provides a web UI for traces, metrics, and logs.

Once it is running, configure the `OTEL_EXPORTER_OTLP_ENDPOINT` environment variable so Squad knows where to send telemetry. Then run at least one agent task (or let Ralph handle an issue from Track A) and confirm that metrics appear in the dashboard. You are looking for:

- `squad.agent.spawns` -- did the agent start?
- `squad.tokens.input` / `squad.tokens.output` -- how many tokens did it consume?
- `squad.agent.duration` -- how long did the task take?

### Capstone: Full Cycle

With all three tracks active -- Ralph polling, governance hooks enforced, Aspire collecting telemetry -- create one final GitHub Issue. Then sit back and watch:

1. Ralph detects the issue and triages it
2. An agent picks it up and starts working
3. Governance hooks constrain which files the agent can touch and scrub any PII from output
4. Metrics flow into the Aspire Dashboard in real time
5. The issue moves toward resolution

This is the proof that your squad can operate autonomously under policy, with full observability.

## Success Criteria

- [ ] Ralph watch mode is running and actively polling the repository for issues
- [ ] At least 2 of the 3 created GitHub Issues are triaged (picked up and assigned to agents)
- [ ] At least 1 issue has a working resolution -- code changes committed or a PR opened
- [ ] A governance hook pipeline exists with at least 2 of 3 policies active (file guards, PII scrubbing, reviewer lockout)
- [ ] A blocked operation is logged or demonstrated (an agent tried something the hooks denied)
- [ ] The .NET Aspire Dashboard is accessible in a browser and displays at least one Squad metric (agent spawns, token usage, or task duration)
- [ ] Agent-generated changes still pass all quality gates from previous challenges

## Hints

<details>
<summary>Hint 1: Where to start and in what order</summary>

Start the Aspire Dashboard first -- it takes a moment to pull the image and initialize. Run the dashboard with ports 18888 (web UI) and 4317 (OTLP receiver) exposed, both auth modes set to Unsecured. While that starts, set `OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317` in your shell environment.

Next, get Ralph going: `squad watch --execute --interval 5`. Then create issues from the GitHub web UI or use `gh issue create --title "..." --body "..."` from the command line. Three issues, three different types.

Save the governance hooks for last -- they are code, and you can iterate on them while Ralph is already polling. Or, even better, create a github issue and let the squad do it!
</details>

<details>
<summary>Hint 2: Building the governance pipeline</summary>

Install the Squad SDK if you have not already: `npm install @bradygaster/squad-sdk`. The key import is `HookPipeline` from the hooks submodule.

Create a pipeline with an options object. The `allowedWritePaths` property takes an array of glob patterns like `['src/RecipeHub.Api/**', 'src/RecipeHub.Web/**', '.squad/**']`. Set `scrubPii: true` and `reviewerLockout: true`. You can also declare hooks in `squad.config.ts` using `defineHooks()` if you prefer a declarative approach.

To test the file guard, have an agent attempt to write to a path outside the allowed list -- `package.json` is a good candidate. The hook should return `action: 'block'` and you will see the denial in the audit output.
</details>

<details>
<summary>Hint 3: Troubleshooting Ralph and telemetry</summary>

If Ralph is not picking up issues, check three things: (1) run `gh auth status` and confirm you have a token with `repo` scope, (2) the issues must be open and unassigned -- Ralph skips issues that already have an assignee, (3) run `squad watch --health` to see whether the polling loop is active and what it last scanned.

If the Aspire Dashboard shows no data, verify that the OTLP endpoint is reachable from your Squad process. The dashboard listens on port 18889 internally for OTLP but you map it to 4317 externally. Double-check your Docker port mapping: `-p 4317:18889` for the collector and `-p 18888:18888` for the web UI.

Ralph uses a 4-tier error recovery system: circuit breaker reset, auth reprobe, git pull, then a 30-minute pause. If you see Ralph pausing, check `squad watch --health` to find out which tier it hit.
</details>

## Learning Resources

- [Squad Ralph documentation](https://bradygaster.github.io/squad/docs/features/ralph/)
- [Squad SDK reference](https://bradygaster.github.io/squad/docs/reference/sdk/)
- [Squad tools and hooks](https://bradygaster.github.io/squad/docs/reference/tools-and-hooks/)
- [.NET Aspire Dashboard (standalone)](https://learn.microsoft.com/en-us/dotnet/aspire/fundamentals/dashboard/standalone)
- [OpenTelemetry environment variables](https://learn.microsoft.com/en-us/dotnet/core/diagnostics/observability-otlp-example)

## Advanced Challenge

Set up the third layer of Ralph: a GitHub Actions workflow (`squad-heartbeat.yml`) that runs on a cron schedule and dispatches Squad agents entirely in the cloud. The workflow should check for untriaged issues, run Squad in a containerized action, and post results back to the issue thread. Combine this with a branch protection rule that requires governance hook checks to pass before any agent-authored PR can merge. Full lights-out operation -- no human in the loop at all.
