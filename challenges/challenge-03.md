# Challenge 03: Quality Gates - Hooks and Linters

**Estimated time:** 30 minutes | **Difficulty:** Medium (L400)

## Introduction

AI agents write code fast. Without guardrails, that speed becomes a liability - inconsistent formatting, lint violations piling up, files modified in directories nobody asked the agent to touch. You have seen this already if you looked closely at what your Squad produced in the last challenge. Maybe the indentation is off. Maybe there is an unused import. Maybe the agent dropped a temp file somewhere unexpected.

This challenge is about building a multi-layered quality enforcement pipeline. Not one gate - three. Git hooks catch problems at commit time. Copilot hooks intercept the agent *before* it writes bad code. And a post-write lint cycle makes sure anything that slips through gets caught and fixed before you move on. By the end of this challenge, every line of agent-generated code will pass through automated checks before it lands in your repository.

Think of it like code review, except it happens in milliseconds and never gets tired.

## Prerequisites

- Challenge 02 completed (you have a working feature built by Squad, and the app runs)

## Description

Your goal is to set up three layers of automated quality enforcement for agent-generated code. Each layer catches different problems at different points in the workflow.

### Layer 1: Git Hooks with Husky

Install Husky to manage git hooks in your project. Configure a **pre-commit** hook that runs the following checks:

- **C# formatting:** `dotnet format` in verify mode against the API project (`src/RecipeHub.Api/`). If formatting is wrong, the commit should be rejected.
- **TypeScript linting:** ESLint with zero warnings allowed against TypeScript files in the Web project (`src/RecipeHub.Web/`).
- **Formatting check:** Prettier in check mode against TypeScript and CSS files.

The hook must actually block a commit. To prove it works, introduce a deliberate formatting violation (add some badly indented code, remove a semicolon, whatever breaks the rules) and attempt a commit. It should fail.

### Layer 2: Copilot Hooks (preToolUse)

Create a Copilot hooks configuration file at `.copilot/hooks.json` in the project root. This file adds a `preToolUse` hook that acts as a file-system fence for your agents. The hook must:

- **Block file writes** to any path outside of `src/RecipeHub.Api/`, `src/RecipeHub.Web/`, and `.squad/` directories. If an agent tries to write to the project root, or to some random temp directory, the hook should deny the operation and explain why.
- **Block dangerous shell commands** - things like `rm -rf`, `DROP TABLE`, `truncate`, or anything that could cause irreversible damage. The hook reads JSON from stdin describing the tool call and returns a deny decision when the command matches a blocklist.

The hook is a shell script. It receives JSON on stdin with `toolName` and `toolArgs` fields, and it outputs JSON with a `permissionDecision` field. A decision of `"deny"` stops the tool call dead.

### Layer 3: Post-Write Lint Cycle

After your agents write code, run the full lint and format suite across both projects. If anything fails, direct the agent to fix the specific violations. Then run the suite again. The cycle should look like this:

1. Agent writes code
2. Linters run and report violations
3. Agent fixes the violations
4. Linters run again and pass
5. Commit succeeds

Demonstrate this full cycle at least once. Ask your Squad to write a small piece of new code (a helper function, a new component, anything real), then run the quality gates and have the agent fix whatever breaks.

### Constraints

- All three layers must be in place and functional at the same time.
- The pre-commit hook must be committed to the repository (inside `.husky/`), not just local config.
- The Copilot hooks configuration must be a file in the project, not a global setting.
- You cannot skip a layer. Even if your agent writes perfect code on the first try, you still need to prove each gate works independently.

## Success Criteria

- [ ] Husky is installed and a pre-commit hook file exists in `.husky/`
- [ ] The pre-commit hook runs `dotnet format --verify-no-changes` and `eslint --max-warnings 0` and blocks commits when either check fails
- [ ] A Copilot `preToolUse` hook configuration exists at `.copilot/hooks.json` in the project root
- [ ] The preToolUse hook blocks file writes to paths outside `src/RecipeHub.Api/`, `src/RecipeHub.Web/`, and `.squad/`
- [ ] At least one blocked operation is demonstrated to a coach (either a rejected commit or a denied tool call)
- [ ] The full lint-fix cycle is demonstrated: agent writes code, lint fails, agent fixes, lint passes, commit succeeds
- [ ] Agent-generated code passes all three quality layers on the final commit

## Hints

<details>
<summary>Hint 1: Getting Husky running</summary>

Start in the project root. Run `npx husky init` to scaffold the `.husky/` directory and a sample pre-commit hook. Open `.husky/pre-commit` and replace the default content with your lint commands. You will want to `cd src/RecipeHub.Api && dotnet format --verify-no-changes` for C# and `cd src/RecipeHub.Web && npx eslint --max-warnings 0 src/` for TypeScript. Chain them so a failure in any step aborts the commit. Test by introducing a bad format change and running `git commit` - it should refuse.
</details>

<details>
<summary>Hint 2: Writing the preToolUse hook script</summary>

Create `.copilot/hooks.json` in your project root. The structure maps hook names to shell commands. Your preToolUse entry should point to a script (for example, `.copilot/pre-tool-check.sh`). That script reads JSON from stdin using `INPUT=$(cat)`, then extracts the tool name and arguments with `jq`. Check if `toolArgs` contains file paths outside your allowed directories. To block, output: `{"permissionDecision":"deny","permissionDecisionReason":"Write blocked: path outside allowed directories"}`. If everything looks fine, output nothing or output `{"permissionDecision":"allow"}`.
</details>

<details>
<summary>Hint 3: Putting it all together</summary>

For the preToolUse script, parse the JSON like this: extract the tool name with `echo "$INPUT" | jq -r '.toolName'`. If the tool is a file-write operation, extract the path from `toolArgs` and check it against your allowlist using simple string matching (bash `[[ ]]` with glob patterns works). For dangerous commands, grep the `toolArgs` for your blocklist terms. For the full lint-fix cycle, prompt your Squad directly: "Write a new React component in src/RecipeHub.Web/src/components/ called RecipeRating.tsx, then run eslint and prettier against it, and fix any issues before committing." Watch the gates fire in sequence.
</details>

## Learning Resources

- [Husky - Git hooks made easy](https://typicode.github.io/husky/)
- [dotnet format command reference](https://learn.microsoft.com/en-us/dotnet/core/tools/dotnet-format)
- [ESLint Getting Started](https://eslint.org/docs/latest/use/getting-started)
- [Prettier CLI documentation](https://prettier.io/docs/cli)
- [GitHub Copilot hooks](https://docs.github.com/en/copilot/customizing-copilot/extending-copilot-coding-agent-with-mcp/using-copilot-coding-agent-hooks)

## Advanced Challenge

Add a **pre-push** hook that goes further than the pre-commit check. Have it run the full test suite (`dotnet test` for the API, `npm test` for the client) and block the push if any test fails. Then configure a `postToolUse` hook that automatically appends a comment header to any new file the agent creates - something like `// Generated by Squad agent - reviewed by quality gate pipeline` with a timestamp. This creates an audit trail showing which files were agent-generated and when they passed quality checks.
