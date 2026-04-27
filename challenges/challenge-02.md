# Challenge 02: Ship a Feature with Your Squad

**Estimated time:** 35 minutes
**Difficulty:** Medium (L400)

## Introduction

You have a squad. Now make it build something.

The recipe-sharing app your team inherited is functional but bare-bones -- users can browse recipes and create new ones via the UI form, but there is no way to mark a recipe as a favorite or come back to a curated list later. Product wants a "favorites" feature, and you are going to delegate the entire thing to your squad rather than writing it yourself.

This is where multi-agent development stops being theoretical. You will hand off a feature request the same way a tech lead hands a spec to a distributed team: state what you want, let the agents decompose the work, and watch Backend and Frontend work in parallel. The Lead agent breaks down the request, Backend builds API endpoints and a new data model in .NET, Frontend adds the React UI, and your job is to observe the coordination, check the output, and verify the whole thing works end-to-end.

Pay attention to what happens in `.squad/decisions.md` and the agent history files. These are not just log artifacts -- they are the shared memory that makes your squad smarter on the next task.

## Prerequisites

- Challenge 01 completed (your squad is initialized with configured agents and routing rules)

**Important:** Before starting this challenge, make sure your application is running. If you haven't already started it, run from the sample app repo root (`copilot-squad-recipe-app/`):

```
dotnet run --project src/RecipeHub.AppHost
```

The Aspire Dashboard will open in your browser showing both `api` and `web` resources. You need both services healthy to test the favorites feature you're about to build.

## Description

Your squad needs to deliver a complete "favorites" feature for the recipe-sharing app. The feature spans the full stack:

**Backend (API):**
- A new data model for favorites (linking the single implicit user to recipes — no authentication required, favorites belong to one user)
- REST endpoints to add a favorite, remove a favorite, and list all favorites
- SQLite persistence consistent with the existing recipe data layer

**Frontend (React):**
- A visible toggle (button, heart icon, star -- the squad can pick) on recipe cards that marks/unmarks a recipe as a favorite
- A dedicated favorites page (or section) reachable from the app navigation that displays all saved favorites

**Integration:**
- The frontend must call the backend API. Favorites added through the UI should appear on the favorites page.

Here is the catch: you are not writing this code yourself. You are delegating to your squad and observing how they coordinate. Give the team a high-level feature request and let the Lead decompose it into tasks. Watch for parallel execution -- Backend and Frontend may work simultaneously on independent pieces. When it happens, note at least one instance where you observed parallel work (check git commit timestamps, coordinator logs, or agent output showing concurrent activity).

After the feature is built, verify it works end-to-end: run both the API and frontend, add a favorite through the UI, and confirm it appears on the favorites page. Then inspect the coordination artifacts:
- Open `.squad/decisions.md` and look for architectural decisions the agents recorded during the build (data model shape, endpoint naming, state management approach).
- Check at least one agent's history file in `.squad/agents/{agent-name}/history.md` to see what learnings were captured about your project.

The feature does not need to be production-polished. You are not grading code quality -- you are proving that a multi-agent squad can take a feature request, split the work, build both halves in parallel, and produce something that compiles and runs.

## Success Criteria

- [ ] At least 2 new API endpoints exist for favorites (add favorite, list favorites) and return valid responses
- [ ] The React frontend shows a visible mechanism to favorite/unfavorite a recipe (button, icon, or toggle on recipe cards)
- [ ] A favorites page or section displays the user's saved favorites and is reachable from the app navigation
- [ ] The feature works end-to-end: favoriting a recipe through the UI adds it to the favorites list
- [ ] At least one instance of parallel agent execution was observed and documented (e.g., Backend and Frontend working on separate files at overlapping times, visible in git log, coordinator output, or agent history)
- [ ] `.squad/decisions.md` contains at least 2 new entries created during this feature build
- [ ] At least one agent's `history.md` file (in `.squad/agents/`) contains new learnings about the project
- [ ] Both the API and the React frontend compile and run without errors after the feature is added

## Hints

<details>
<summary>Hint 1: Prompting the whole team</summary>

Start broad. Address the entire squad with a single feature request rather than micromanaging individual agents. Something like:

"Team, we need a favorites feature. Users should be able to mark recipes as favorites and see all their favorites on a dedicated page. The backend needs REST endpoints for adding, removing, and listing favorites. The frontend needs a toggle on recipe cards and a new favorites page."

The Lead agent should decompose this into tasks and fan out the work. Watch the output -- you should see Backend and Frontend receiving tasks and working at the same time.

</details>

<details>
<summary>Hint 2: If agents produce inconsistent or incomplete results</summary>

Parallel agents do not share context windows. Sometimes the Backend picks a response shape that the Frontend does not expect, or one agent finishes before the other and the integration is off.

If the first pass does not connect cleanly, try targeted follow-ups to individual agents:

- "@Backend, add favorite/unfavorite REST endpoints to the recipe app. Use a Favorite model with Id, RecipeId, and UserId fields. Store in SQLite."
- "@Frontend, add a heart toggle on each recipe card that calls POST /api/favorites and DELETE /api/favorites/{id}. Create a /favorites page that calls GET /api/favorites."

Breaking a big request into two directed prompts gives each agent clearer constraints.

</details>

<details>
<summary>Hint 3: Observing and verifying parallel execution</summary>

Parallel execution means Backend and Frontend agents work on different files at overlapping times. To observe it:

- **Check git log:** Run `git log --all --oneline --graph --date-order` and look for commits by different agents with close timestamps
- **Watch coordinator output:** When you send the feature request, the coordinator's dispatch logs may show multiple agents receiving tasks simultaneously
- **Review agent history:** Check `.squad/agents/{agent-name}/history.md` files for entries showing concurrent work sessions

To verify the feature works, run both services, open the frontend in a browser, click a favorite toggle on a recipe card, navigate to the favorites page, and confirm the recipe appears.

To inspect the files yourself:

```
cat .squad/decisions.md
```

For agent history, look inside the agent-specific directories:

```
ls .squad/agents/
cat .squad/agents/backend/history.md
```

The decisions file should contain entries about choices like data model design, endpoint naming, or which React patterns to use. History files capture project-specific learnings that persist across sessions -- things like "this project uses SQLite with Entity Framework" or "the React app uses React Router for navigation."

If your agents did not automatically record decisions, you can prompt them directly: "Record the architectural decisions you made for the favorites feature in decisions.md."

</details>

## Learning Resources

- Squad parallel execution: https://github.com/bradygaster/squad/blob/dev/docs/src/content/docs/features/parallel-execution.md
- Squad guide: https://github.com/bradygaster/squad/blob/dev/docs/src/content/docs/guide.md
- .NET Minimal API tutorial: https://learn.microsoft.com/en-us/aspnet/core/tutorials/min-web-api

## Advanced Challenge

After the feature works, try this: ask your squad to refactor the favorites feature so that the favorite state is optimistic on the frontend. The heart toggle should flip immediately when clicked, then roll back if the API call fails. Direct the Frontend agent specifically and see whether it references the decisions from the initial build or starts from scratch. This tests whether the squad's shared memory actually influences follow-up work.
