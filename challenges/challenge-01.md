# Challenge 01: Assemble Your Squad

**Estimated Time:** 25 minutes
**Difficulty:** Easy (L400)

## Introduction

Think about the last time you onboarded a new developer. You didn't just hand them a laptop and say "go." You gave them context -- what the project does, which parts of the codebase they own, who to ask about what. You set up roles and responsibilities so work doesn't overlap and nothing falls through the cracks.

AI agents need the same treatment. A single general-purpose assistant will give you generic answers. But a team of specialized agents, each with a defined role, domain knowledge, and routing rules? That team accumulates context over time. The backend agent learns your API conventions. The frontend agent picks up your component patterns. The tester remembers which edge cases bit you last sprint.

Squad formalizes this. It creates persistent, named agents with charters that define what they know, what tools they can use, and what parts of the codebase they own. It sets up a coordinator that routes your requests to the right specialist without you having to think about it. In this challenge, you are going to hire your AI development team and get them oriented on the RecipeHub codebase.

## Prerequisites

- Challenge 00 completed (development environment configured, authentication verified, Squad CLI and Copilot tools working)

## Description

Your starter project is a half-built recipe management application called RecipeHub: a .NET 10 minimal API backend with a React/TypeScript frontend, backed by SQLite. The project lives in its own repository at [seiggy/copilot-squad-recipe-app](https://github.com/seiggy/copilot-squad-recipe-app). Right now it has no AI team assigned to it. Your job is to fix that.

**Initialize your Squad.** Run the Squad initialization process in the root of the sample app project (`copilot-squad-recipe-app/`). Squad will ask you to describe what you are building. Be honest and specific about the stack -- the quality of the agents it creates depends directly on how well you describe the project. A vague description gets you vague agents.

**Review the proposed team.** Squad will cast a team of agents drawn from a fictional universe (you might get NASA call signs, you might get something else entirely -- the casting system is deterministic based on your project shape). You should end up with at least four specialists: a Lead who handles architecture decisions and code review, a Backend developer for the .NET API layer, a Frontend developer for the React UI, and a Tester for quality and coverage. Accept the team or request changes if the roles don't match what you need.

**Explore what Squad created.** After initialization, a `.squad/` directory appears in your project root. Dig into it. Read `team.md` to see your roster. Open `routing.md` to understand how requests get dispatched. Pick at least one agent's `charter.md` and read it -- understand what expertise, tools, and responsibilities Squad assigned to that agent. Also check that the GitHub Copilot coordinator was registered properly.

**Customize the routing rules.** The default routing is generic. Your project has a specific structure, and your agents should know about it. Update `routing.md` so that:

- File paths matching your API and backend C# code route to the Backend agent
- File paths matching your React/TypeScript client code route to the Frontend agent
- Anything related to tests routes to the Tester agent

Use the actual agent names from your cast -- check `.squad/agents/` to see what Squad named them.

**Verify the team is responsive.** Send a direct message to one specific agent asking it to describe its area of the codebase. Then send a team-wide prompt and watch the coordinator figure out who should respond. Both interactions should produce meaningful, role-appropriate responses.

## Success Criteria

- [ ] A `.squad/` directory exists at the project root containing `team.md`, `routing.md`, `decisions.md`, and at least 4 agent subdirectories under `.squad/agents/`
- [ ] `.github/agents/squad.agent.md` exists, registering the Squad coordinator with GitHub Copilot
- [ ] Each agent directory contains a `charter.md` that defines the agent's role, area of expertise, and available tools
- [ ] `routing.md` contains at least 3 custom routing rules that map project file patterns to specific agents (backend C# paths, frontend TSX paths, and test paths)
- [ ] At least one agent responds to a direct message with context-appropriate output (not a generic reply)
- [ ] A team-wide prompt is successfully routed by the coordinator to multiple agents

## Hints

<details>
<summary>Hint 1: Getting a good team starts with a good description</summary>

When `squad init` asks you to describe your project, don't just say "a web app." Tell it: ".NET 10 minimal API backend, React with TypeScript frontend, SQLite database, recipe-sharing application." The casting system uses your description to decide what specialists to create and what expertise to assign each one. A detailed description means agents that actually understand your stack from day one.
</details>

<details>
<summary>Hint 2: Routing rules follow a pattern-based format</summary>

Open `.squad/routing.md` after initialization and look at its existing structure. Routing rules map file path patterns to agent names. You need to add module sections with glob patterns -- something like `src/RecipeHub.Api/**/*.cs` targeting your Backend agent and `src/RecipeHub.Web/**/*.tsx` targeting your Frontend agent. The agent names are not "Backend" or "Frontend" -- they are the cast names Squad assigned. Look inside `.squad/agents/` to find the actual directory names, and use those in your routing rules.
</details>

<details>
<summary>Hint 3: Talking to your agents</summary>

To send a direct message to a specific agent, use the `@AGENT_NAME` syntax followed by your request. In the CLI, start an interactive session with `copilot --agent squad --yolo` and type something like `@apollo, describe the current API structure` (substituting your actual agent's cast name). In VS Code, open Copilot Chat, pick the Squad participant, and use the same @-mention syntax. For a team-wide prompt, drop the @-mention and just ask a question -- the coordinator reads `routing.md` to decide who handles it.
</details>

## Learning Resources

- [Squad repository and documentation](https://github.com/bradygaster/squad)
- [Squad first session tour](https://github.com/bradygaster/squad/blob/dev/docs/src/content/docs/tour-first-session.md)
- [Squad casting system](https://github.com/bradygaster/squad/blob/dev/docs/src/content/docs/features/casting.md)
- [GitHub Copilot agents overview](https://docs.github.com/en/copilot/using-github-copilot/using-extensions-to-integrate-external-tools)

## Advanced Challenge

Already comfortable with the default team? Try this: add a fifth custom agent. Create a new agent directory under `.squad/agents/` with a `charter.md` that defines a "DevOps" or "Infrastructure" specialist responsible for Dockerfiles, CI/CD workflows, and deployment configuration. Update `routing.md` to send `.github/workflows/**`, `Dockerfile`, and `docker-compose*.yml` patterns to this new agent. Then ask the coordinator a deployment-related question and confirm it routes to your custom agent instead of the Lead.
