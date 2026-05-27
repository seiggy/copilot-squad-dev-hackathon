# Challenge 05: Break-Fix Under Pressure

**Estimated time:** 35 minutes
**Difficulty:** Hard (L400)

## Introduction

You have spent the last four challenges building features, wiring up quality gates, and writing tests. Your codebase has coverage, linting, formatting hooks, and a testing skill your agents know how to use. Everything looks solid.

It is not.

Three bugs have been hiding in the starter project, dormant until now (unless you just happened to stumble upon it!). One crashes an API endpoint. Another makes the UI flicker and contradict itself under fast user input. The third quietly accepts data that should never pass validation. These are the kinds of bugs that show up in production at 4pm on a Friday -- the kind where reading stack traces and stepping through code is what separates a quick fix from a four-hour firefight.

This challenge is different from the ones before it. You will not be told what is wrong. You will be told what users see. Your job is to describe those symptoms to your squad, let the agents trace the root cause, and verify the fixes with tests. The agents have context from every previous challenge -- your history, your architectural decisions, your testing conventions. That accumulated knowledge matters now more than ever.

As a stretch goal, you will set up remote access to your squad and fix at least one bug from your phone or tablet.

## Prerequisites

- Challenge 04 completed (test suite passing at 80%+ coverage, testing skill created)
- All quality gates from Challenge 03 still active (linting, formatting, pre-commit hooks)

## Description

### Bug: Cook Mode failure

**What the user sees:** Selecting "Cook Mode" on a recipe causes the app to spin for several seconds, and then an error message returns `Couldn't load cook mode. Request failed: 404 Not Found`

**Your task:** Report this symptom to your squad. Do not tell them what the fix is. Describe what you observed and let them investigate the server code, read the logs, and propose a fix. Once the fix is in, have the Tester agent write a regression test that covers the empty-favorites case.

### Bug 2 - Poor UI Accessibility

**What the user sees:** Several portions of the UI have poor accessibility. Text that is hard to read, blinding white boxes on a black background, grey text on a black background, light grey on a white background.

**Your task:** Describe to the Squad the problems you see in the UI, have them make a refactoring pass at cleaning up the accessibility concerns. Try giving them goals, such as telling them you'd like to meet `WCAG 2.1` readability standards.

### Bug 3 - Missing Observability Instrumentation

**What the user sees:** Your data engineer has reported that the observability traces aren't useful to him and his team. Help him out by getting the agents to fix the missing OpenTelemetry configuration for SQLite

**Your task:** Direct your squad to help you fix this bug. See if they can find the documentation, and successfully enable OpenTelemetry support for SQLite and Aspire so that your data layer telemetry shows in the Aspire Traces dashboard.

## Success Criteria

- [ ] All 3 bugs are fixed and the application runs without errors
- [ ] Each bug fix that can has at least one regression test that would catch the original issue
- [ ] All existing tests from Challenge 04 still pass (no regressions introduced)
- [ ] All quality gates pass -- linting, formatting, and pre-commit hooks report clean
- [ ] Orchestration logs or `decisions.md` show that agents were used for diagnosis (not just manual debugging)

## Advanced Challenge: Remote Debugging

Set up remote access to your squad so you can interact with agents from a phone or tablet:

1. Launch GitHub Copilot CLI: `copilot --agent squad`
2. Enable GitHub Copilot Remote Access: `/remote on` and Ctrl+e to show the QR Code
3. Scan the QR code that appears in the terminal with your phone's camera
4. From your phone's browser, direct an agent to investigate or fix one of the three bugs

Remote access to your development agents means you can triage production issues from anywhere -- a conference room whiteboard session, a commute, or a couch. For an extra challenge, fix all three bugs entirely from your phone using the remote tunnel -- no laptop keyboard allowed.

## Hints

<details>
<summary>Hint 1: Talk symptoms, not solutions</summary>

The whole point of this challenge is letting agents diagnose the problem from symptoms. For Bug 1, try something like:

"I click on the Cook Mode button on the Recipe Detail view, and it shows a spinner for several seconds and then returns an error: `Couldn't load cook mode. Request failed: 404 Not Found`"

Let the agent read the code, find the null reference, and propose its own fix. If you hand it the answer, you skip the part that matters. The goal here is to show that the agents can handle more complex work and can help with debugging issues that you might not immediately know the fix for.

</details>

<details>
<summary>Hint 2: UI Accessibility</summary>

For Bug 2, accessibility problems can sometimes be a personal preference thing. Using standards such as WCAG 2.2, can help you steer the agents in a way that has solid rules to follow. Use tools like `eslint-plugin-jsx-a11y` to help scan for and surface these problems in a more automated fashion.

</details>

<details>
<summary>Hint 3: Validation and working with remote search</summary>

For Bug 3, try directing the agent to search the Aspire documentation, https://aspire.dev/docs/ Tell the agent that you specifically want to see data platform traces for your SQLite db activity in the Aspire dashboard. Often, pointing agents to known documentation repositories, and nudging them in the direction you'd like them to solve a problem, can help reduce strange behaviors.

</details>
