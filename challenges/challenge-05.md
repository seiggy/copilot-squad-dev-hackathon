# Challenge 05: Break-Fix Under Pressure

**Estimated time:** 35 minutes
**Difficulty:** Hard (L400)

## Introduction

You have spent the last four challenges building features, wiring up quality gates, and writing tests. Your codebase has coverage, linting, formatting hooks, and a testing skill your agents know how to use. Everything looks solid.

It is not.

Three bugs have been hiding in the starter project, dormant until now. One crashes an API endpoint. Another makes the UI flicker and contradict itself under fast user input. The third quietly accepts data that should never pass validation. These are the kinds of bugs that show up in production at 4pm on a Friday -- the kind where reading stack traces and stepping through code is what separates a quick fix from a four-hour firefight.

This challenge is different from the ones before it. You will not be told what is wrong. You will be told what users see. Your job is to describe those symptoms to your squad, let the agents trace the root cause, and verify the fixes with tests. The agents have context from every previous challenge -- your history, your architectural decisions, your testing conventions. That accumulated knowledge matters now more than ever.

As a stretch goal, you will set up remote access to your squad and fix at least one bug from your phone or tablet.

## Prerequisites

- Challenge 04 completed (test suite passing at 80%+ coverage, testing skill created)
- All quality gates from Challenge 03 still active (linting, formatting, pre-commit hooks)

## Description

### Activate the bugs

The starter project includes a script that introduces three pre-planted bugs into your codebase. Run it to activate the issues:

```
npm run activate-bugs
```

After activation, your app compiles and starts -- but certain operations will fail or behave incorrectly. Verify activation succeeded by calling `GET /api/favorites` on a user with no favorites — it should crash with a 500 error and log an exception.

**Important:** Do not read the script source code to find the answers. The bugs are injected as real code changes (not comments or hints), and you must diagnose from observed symptoms. The point is diagnosis, not shortcuts.

### Bug 1 - API 500 Error

**What the user sees:** Calling `GET /api/favorites` returns a 500 Internal Server Error, but only for users who have not saved any favorites yet. Users with at least one favorite see their list just fine. The server logs show an unhandled exception.

**Your task:** Report this symptom to your squad. Do not tell them what the fix is. Describe what you observed and let them investigate the server code, read the logs, and propose a fix. Once the fix is in, have the Tester agent write a regression test that covers the empty-favorites case.

### Bug 2 - UI Race Condition

**What the user sees:** Rapidly clicking the favorite toggle on a recipe card causes inconsistent UI state. The heart icon flickers between filled and empty states, and the backend may receive duplicate add/remove requests in rapid succession. It does not happen every time -- sometimes it takes three or four rapid clicks to trigger it.

**Your task:** Have your squad reproduce this issue, diagnose the root cause in the frontend state management (likely missing debouncing or async state guards), and implement a fix. The fix should handle the case where multiple toggle requests overlap. Ask the Tester to write a test that simulates rapid toggling and verifies consistent UI state.

### Bug 3 - Validation Bypass

**What the user sees:** The `POST /api/recipes` endpoint happily accepts recipes with empty names, negative serving counts, or other obviously invalid data. It should reject these with a 400 Bad Request and a clear error message.

**Your task:** Direct your squad to add proper input validation on the API side and write tests that cover the edge cases -- empty strings, negative numbers, zero servings, missing required fields, boundary values.

## Success Criteria

- [ ] All 3 bugs are fixed and the application runs without errors
- [ ] Each bug fix has at least one regression test that would catch the original issue
- [ ] All existing tests from Challenge 04 still pass (no regressions introduced)
- [ ] All quality gates pass -- linting, formatting, and pre-commit hooks report clean
- [ ] Orchestration logs or `decisions.md` show that agents were used for diagnosis (not just manual debugging)

## Advanced Challenge: Remote Debugging

Set up remote access to your squad so you can interact with agents from a phone or tablet:

1. Authenticate with devtunnel: `devtunnel user login`
2. Start your squad with tunnel access enabled: `squad start --tunnel`
3. Scan the QR code that appears in the terminal with your phone's camera
4. From your phone's browser, direct an agent to investigate or fix one of the three bugs

This is not just a party trick. Remote access to your development agents means you can triage production issues from anywhere -- a conference room whiteboard session, a commute, or a couch. For an extra challenge, fix all three bugs entirely from your phone using the remote tunnel -- no laptop keyboard allowed.

## Hints

<details>
<summary>Hint 1: Talk symptoms, not solutions</summary>

The whole point of this challenge is letting agents diagnose the problem from symptoms. For Bug 1, try something like:

"The GET /api/favorites endpoint returns a 500 error when I call it for a user who hasn't saved any favorites yet. Can you check the server logs, trace the exception, and figure out what is going wrong in the favorites controller?"

Let the agent read the code, find the null reference, and propose its own fix. If you hand it the answer, you skip the part that matters.

</details>

<details>
<summary>Hint 2: Reproducing the race condition</summary>

For Bug 2, the intermittent nature makes it tricky. Ask your Tester agent to write a test that fires multiple favorite toggle actions in quick succession without waiting for each one to resolve. Something like: "@Tester, write a test that rapidly toggles a recipe's favorite status 5 times in a row and checks whether the final UI state is consistent."

The fix usually involves adding debouncing to the click handler or using async state guards to prevent overlapping requests.

</details>

<details>
<summary>Hint 3: Validation and working with agent memory</summary>

For Bug 3, be specific about what "invalid" means when you talk to the Backend agent: "@Backend, the POST /api/recipes endpoint currently accepts empty names and negative serving counts. Add model validation that rejects these with a 400 response. Servings must be a positive integer and name must be a non-empty string."

Then have the Tester write the edge cases: "@Tester, write tests for POST /api/recipes that send invalid payloads -- empty name, negative servings, zero servings, missing name field -- and verify each gets a 400 status code."

Remember that your agents have been building up context across all previous challenges. They have read your `history.md`, your `decisions.md`, and the testing skill you created in Challenge 04. If an agent seems confused about your project structure, point it to those files -- they are its memory.

</details>
