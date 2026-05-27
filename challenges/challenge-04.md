# Challenge 04: Test Coverage Blitz

**Estimated time:** 30 minutes
**Difficulty:** Hard (L400)

## Introduction

Your squad shipped the favorites feature in Challenge 02 and wired up quality gates in Challenge 03. Good. But here is the uncomfortable truth: those gates are guarding code that has almost no test coverage. A linter can catch style problems. A pre-commit hook can block sloppy formatting. Neither of them will tell you that your favorites endpoint silently returns a 200 when it should return a 404, or that your React toggle component breaks when the API is slow.

This is where the Tester agent earns its keep. Instead of hand-writing dozens of test files yourself, you will direct the Tester to generate tests across both stacks -- xUnit for the .NET API, Vitest plus Testing Library for the React frontend. You will also build a custom skill file that teaches the squad your project's testing conventions, so every future test follows the same patterns. Think of it as writing down the rules your team would normally carry around in their heads.

Try to aim for 80% line coverage on both sides. Make it a rule for your agents, and setup enforcement rules like you learned before.

## Prerequisites

- Challenge 03 completed (quality gates active -- git hooks, Copilot hooks, linter enforcement)
- Starter application running with the favorites feature from Challenge 02

## Description

Your test suite is empty or close to it. By the end of this challenge, both the .NET API and the React frontend should have real, meaningful tests that pass your quality gates and hits 80% line coverage.

**What you need to accomplish:**

Measure your starting point. Run coverage reports on both stacks so you know the gap. Then put the Tester agent to work.

On the API side, you need xUnit tests that cover the full surface of the recipes controller -- every CRUD operation and every favorites endpoint. Do not stop at happy paths. A test suite that only checks "create recipe returns 201" is incomplete. What happens when someone tries to favorite a recipe that does not exist? What about duplicate favorites? Invalid input? Those edge cases are where bugs actually hide.

On the frontend side, you need component tests using Vitest and Testing Library. The recipe list should render correctly. The favorites toggle should respond to clicks. The favorites page should show the right items. These tests should interact with the component the way a user would -- clicking buttons and reading text -- not reaching into internal state.

Beyond generating tests, create a custom Squad skill that captures your project's testing conventions. This goes in `.squad/skills/testing-conventions/SKILL.md` and should document the patterns your team decided on: how tests are named, how test data is set up, how the API layer gets mocked, and what coverage targets apply. Once this skill exists, any agent on the squad can reference it when writing future tests. The skill starts at `low` confidence -- that is how all earned skills begin. Prompt the Squad agent to create the skill for you with a description of your rules, and instruct it to change as you work. It will grow in confidence as the team uses it more and you accept the outputs.

Every generated test must pass the linting rules you set up in Challenge 03. If the Tester produces tests that fail ESLint or `dotnet format`, those tests need to be fixed before you are done.

## Success Criteria

- [ ] `dotnet test` passes with at least 80% line coverage on the API project
- [ ] `npx vitest run --coverage` passes with at least 80% line coverage on the React frontend
- [ ] At least 10 unit tests exist for the .NET API, covering both recipes CRUD and favorites endpoints
- [ ] At least 8 component or unit tests exist for the React frontend
- [ ] A valid `testing-conventions/SKILL.md` exists in `.squad/skills/` with correct YAML frontmatter (name, domain, triggers, roles, confidence)
- [ ] The skill body documents at least: naming conventions, test data strategy, mocking approach, and coverage targets
- [ ] All tests pass linting -- ESLint for TypeScript test files, `dotnet format` for C# test files
- [ ] No test directly accesses component internals or private methods (tests should survive a refactor)

## Hints

<details>
<summary>Hint 1: Measuring your baseline</summary>

Before you ask the Tester to do anything, measure where you stand. For the .NET API:

```
dotnet test --collect:"Code Coverage"
```

This will generate a coverage report that opens in Visual Studio Enterprise. You can convert this to the standard Cobertura format using the `dotnet-coverage` tool. Run `dotnet tool install --global dotnet-coverage` to install the tool. Then run `dotnet-coverage merge -f cobertura -o cobertura.xml **\*.coverage` to convert your reports. This XML file can be used with the report generator to view in a human frienedly format. To launch and view the report, install the ReportGenerator tool `dotnet tool install -g dotnet-reportgenerator-globaltool`. Then run `reportgenerator -reports:cobertura.xml -reportTypes:Html -targetdir:tests/reports/coverage/`

> [!NOTE]
> See the .NET Code Coverage tool docs here: https://learn.microsoft.com/en-us/dotnet/core/additional-tools/dotnet-coverage

For the React frontend:

```
cd src/RecipeHub.Web
npx vitest run --coverage
```

Both will likely show coverage near zero. That is expected. Now you have a number to beat. Direct the Tester agent with a clear starting point: "@Tester, we need 80% test coverage. Start with unit tests for the recipes API controller -- cover all CRUD endpoints and include edge cases for not-found and invalid-input scenarios."

</details>

<details>
<summary>Hint 2: Building the testing-conventions skill</summary>

Create the file at `.squad/skills/testing-conventions/SKILL.md`. The frontmatter needs these fields:

```yaml
---
name: Testing Conventions
domain: quality
triggers: [test, coverage, xunit, vitest, testing-library, unit-test]
roles: [tester, developer]
confidence: low
---
```

In the markdown body below the frontmatter, document the specific patterns for this project. What naming pattern do test methods follow? (Something like `MethodName_Scenario_ExpectedResult` for C#, `describe/it` blocks for TypeScript.) How is test data created -- factory functions, inline builders, fixture files? How do you mock the API in frontend tests? State the 80% coverage floor explicitly. The more concrete you are, the more useful this skill becomes for future agent work.

</details>

<details>
<summary>Hint 3: Closing the coverage gap</summary>

If you are stuck in the 60-70% range, the uncovered code is almost certainly in error handling and branching logic. Ask the Tester to read the coverage report: "@Tester, analyze the coverage report and write tests for uncovered branches in the recipes controller. Focus on error handling paths -- what happens on bad input, missing records, and duplicate operations."

For the React side, coverage gaps usually come from untested user interactions. Direct the Frontend agent (or the Tester) to simulate real clicks: "@Frontend, write tests that click the favorite toggle button and verify the UI updates correctly. Also test what renders when the favorites list is empty."

If individual tests fail linting, do not skip the lint step. Have the agent fix the style issues -- that is exactly what the quality gates from Challenge 03 are for.

</details>

## Learning Resources

- Squad skills documentation: https://bradygaster.github.io/squad/docs/features/skills/
- Squad skill-discovery sample: https://github.com/bradygaster/squad/blob/dev/samples/skill-discovery/README.md
- xUnit documentation: https://xunit.net/docs/getting-started/v3/cmdline
- Vitest coverage guide: https://vitest.dev/guide/coverage
- Testing Library guiding principles: https://testing-library.com/docs/guiding-principles
- Helpful Code Coverage in .NET Blog Post: https://www.mytechramblings.com/posts/code-coverage-in-dotnet/

## Advanced Challenge

Once you hit 80%, push for 90% and add integration tests that hit the actual API endpoints through the test server. On the .NET side, use `WebApplicationFactory<T>` to spin up the API in-memory and run HTTP requests against it. On the React side, use Mock Service Worker (MSW) to intercept network calls and test full page flows -- loading the recipe list, toggling a favorite, navigating to the favorites page. Then update your testing-conventions skill to `medium` confidence and add the integration test patterns you discovered.
