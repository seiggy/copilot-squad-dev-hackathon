# GitHub Copilot & Squad - Developer Workflow Hackathon

A half-day hands-on hackathon where developers learn to use GitHub Copilot and Squad to supercharge their day-to-day development workflow. From hiring an AI development team, to shipping features, enforcing code quality, generating tests, debugging production issues, and setting up autonomous operations -- all using the [RecipeHub](https://github.com/seiggy/copilot-squad-recipe-app) .NET 10 + React recipe management application as the project under development.

**Duration:** 4 hours | **Level:** L400 (Expert) | **Format:** Individual or paired

**Sample App Repository:** [seiggy/copilot-squad-recipe-app](https://github.com/seiggy/copilot-squad-recipe-app) -- fork this repo to your own GitHub account and work in your fork for all challenges. You need your own fork so that autonomous agents in Challenge 06 can create issues and PRs on your repository.

**Target audience:** Developers building .NET 10 + TypeScript/React applications who want to integrate GitHub Copilot and Squad into their daily workflow. You should be comfortable writing C# and TypeScript, working with Git from the command line, and navigating VS Code or Visual Studio 2026.

## Prerequisites

| Prerequisite | Details |
|---|---|
| GitHub account | With GitHub Copilot access (Individual, Business, or Enterprise) |
| Node.js | v22+ LTS |
| .NET 10 SDK | Latest |
| GitHub CLI (gh) | Latest |
| GitHub Copilot CLI | Latest (or VS Code/Visual Studio 2026 with Copilot extension) |
| Git | Latest |
| Docker | For Aspire Dashboard in Challenge 06 |

If you open this repository in GitHub Codespaces, everything above is pre-installed. That is the recommended path.

## Challenges

| Challenge | Title | Duration | Difficulty | Description |
|---|---|---|---|---|
| 00 | [Base Camp](challenges/challenge-00.md) | 20 min | Setup | Install tools, authenticate, verify environment |
| 01 | [Assemble Your Squad](challenges/challenge-01.md) | 25 min | Easy | Initialize Squad, explore agent casting, customize routing |
| 02 | [Ship a Feature](challenges/challenge-02.md) | 35 min | Medium | Build full-stack favorites feature with parallel agents |
| 03 | [Quality Gates](challenges/challenge-03.md) | 30 min | Medium | Configure git hooks, Copilot hooks, and linter enforcement |
| 04 | [Test Coverage Blitz](challenges/challenge-04.md) | 30 min | Hard | Generate 80%+ coverage with Tester agent, create testing SKILL.md |
| 05 | [Break-Fix](challenges/challenge-05.md) | 35 min | Hard | Diagnose and fix 3 bugs with agents, try /remote from phone |
| 06 | [Autonomous Operations](challenges/challenge-06.md) | 35 min | Expert | Ralph watch mode, SDK governance hooks, Aspire observability |

## Getting Started

**Step 1: Get the challenge instructions (this repo)**

Clone this repository for the challenge instructions and coach materials.

**Step 2: Fork and clone the sample app**

Fork the [RecipeHub sample app](https://github.com/seiggy/copilot-squad-recipe-app) to your own GitHub account, then clone your fork. You **must** use a fork (not a direct clone) because Challenge 06 requires Ralph to create issues and PRs on your repository.

```bash
# Fork via the GitHub UI or CLI:
gh repo fork seiggy/copilot-squad-recipe-app --clone
cd copilot-squad-recipe-app
```

**Option A: GitHub Codespaces (recommended)**

After forking, open **your fork** in Codespaces by clicking the green "Code" button on your forked repo, then "Open with Codespaces." The dev container will install every tool you need. Once the terminal is ready, jump straight to `challenges/challenge-00.md` in this repo for instructions.

**Option B: Local Dev Container**

Clone your fork, open the folder in VS Code, and accept the prompt to reopen in a dev container. Docker Desktop must be running.

**Option C: Bare metal**

Install every prerequisite from the table above manually. Not recommended unless you have a good reason.

Whichever option you pick, start with Challenge 00 to verify your environment before moving on.

## How the Challenges Work

Each challenge file lives in the `challenges/` folder. Challenges build on each other, so work through them in order. Every challenge contains:

- **Objectives** -- what you will accomplish
- **Steps** -- instructions with enough detail to guide you without hand-holding
- **Success Criteria** -- a checklist to verify you are done
- **Advanced Challenges** -- optional stretch goals if you finish early

Coaches have answer keys and verification commands. If you get stuck for more than 5 minutes, flag a coach.

## Interfaces You Will Use

This hackathon exercises GitHub Copilot and Squad across multiple surfaces:

- **GitHub Copilot CLI** -- terminal-based agent interaction
- **VS Code Copilot Chat** -- inline chat, @squad mentions, slash commands
- **Visual Studio 2026** -- integrated Copilot experience for .NET work
- **/remote (mobile/tablet)** -- remote agent execution from a phone or tablet in Challenge 05

## Architecture Overview

See the [RecipeHub README](https://github.com/seiggy/copilot-squad-recipe-app#readme) for a full description of the application architecture and project structure.

## Useful Links

- [RecipeHub Sample App](https://github.com/seiggy/copilot-squad-recipe-app) -- the project used for all challenges
- [Squad by Brady Gaster](https://github.com/bradygaster/squad)
- [GitHub Copilot CLI docs](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/use-copilot-cli)
- [What's new in .NET 10](https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-10)
- [GitHub Copilot documentation](https://docs.github.com/en/copilot)

## Coach Materials

If you are facilitating this hackathon, see:

- [Facilitation Guide](coach/facilitation-guide.md) -- timing, per-challenge coaching notes, common blockers
- [Scoring Rubric](coach/scoring-rubric.md) -- point allocation and evaluation criteria
