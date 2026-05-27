# Challenge 00: Base Camp - Environment Setup

**Estimated time:** 20 minutes
**Difficulty:** Easy (Setup)

## Introduction

Before you can work with your AI development team, you need the right tools. This challenge ensures your environment is fully configured for GitHub Copilot + Squad across all interfaces -- CLI, VS Code, and Visual Studio 2026. You will also verify connectivity and authentication so nothing blocks you during the hands-on challenges ahead.

The starter project is a recipe management app called **RecipeHub** with a .NET 10 Minimal API backend and a React 19 + TypeScript frontend. It lives in its own repository at [seiggy/copilot-squad-recipe-app](https://github.com/seiggy/copilot-squad-recipe-app). You will not change any code in this challenge -- you just need to get everything running and confirm the tools respond.

> **Important:** You must **fork** this repo to your own GitHub account, not just clone it. Challenge 06 requires Ralph (the autonomous operations daemon) to create issues and PRs on your repository, which only works if you own the repo.

## Description

Set up your development environment and verify that every tool in the stack is working. By the end of this challenge, you should have a running application, authenticated CLI tools, and at least one working Copilot interface.

Here is what you need to accomplish:

1. **Fork and clone the sample app repository.** Fork [seiggy/copilot-squad-recipe-app](https://github.com/seiggy/copilot-squad-recipe-app) to your own GitHub account, then clone your fork. Open it in GitHub Codespaces (recommended) or a local dev container:

   ```bash
   # Fork and clone in one step using GitHub CLI:
   gh repo fork seiggy/copilot-squad-recipe-app --clone
   cd copilot-squad-recipe-app
   ```

   Confirm the .NET 10 SDK and Node.js 22+ are available.

2. **Install Squad CLI** globally:

   ```
   npm install -g @bradygaster/squad-cli
   ```
   
   Verify installation:
   
   ```
   squad --version
   ```

3. **Authenticate with GitHub** using the GitHub CLI:

   ```
   gh auth login
   ```

   Select GitHub.com, HTTPS, and follow the browser-based login flow.

4. **Run the Squad health check** and resolve any issues it reports:

   ```
   squad doctor
   ```

   > [!NOTE]
   > Ignore the error that says the `.squad/` directory is missing. We will solve that shortly.

5. **Verify GitHub Copilot** works in at least one of these interfaces:
   - **CLI:** Run `copilot` to start an interactive session. Type a test prompt and confirm a response.
   - **VS Code:** Open the Copilot Chat panel (Ctrl+Shift+I or Cmd+Shift+I). Send a test message.
   - **Visual Studio 2026:** Open Copilot Chat from the top menu. Send a test message.

6. **Run the starter application** through the Aspire AppHost:

   ```
   dotnet run --project src/RecipeHub.AppHost
   ```

   This starts both the API and Web services and launches the Aspire Dashboard. Check the terminal output for the dashboard URL (usually `https://localhost:17xxx`). In the dashboard, verify both the `api` and `web` resources show as healthy. Then check:
   - The API health endpoint responds (navigate to the `api` resource's HTTP endpoint and append `/health`)
   - The React app loads (navigate to the `web` resource's HTTP endpoint)

## Understanding the Application

Before you move on to Challenge 01, make sure you understand how to start and verify the application — you will need this running for every challenge after this one.

### How to Start the Application

The RecipeHub app uses **.NET Aspire 13.2** as its orchestrator. Unlike traditional projects where you start the API and frontend separately, Aspire manages everything through a single AppHost project.

**To start the entire stack:**

```bash
dotnet run --project src/RecipeHub.AppHost
```

**What happens when you run this:**

1. The AppHost builds the .NET API project (`src/RecipeHub.Api/`)
2. The AppHost starts the React frontend (`src/RecipeHub.Web/`) by running `npm run dev` automatically
3. The Aspire Dashboard launches in your browser (URL printed to terminal, typically `https://localhost:17xxx/login?t=xxxxxxx`)
4. Both services are registered in the dashboard with health checks, logs, and traces

**Note:** The dashboard port is randomized on each person's machine. Always check the terminal output for the exact URL.

### Verifying Everything Works

Once the AppHost starts, open the Aspire Dashboard URL shown in your terminal. You should see:

- **Resources view:** Two resources listed — `api` and `web`
- **Health status:** Both showing green/healthy checkmarks
- **Endpoints:** Each resource has an HTTP endpoint link you can click

**Verification steps:**

1. **Check the API health endpoint:**
   - In the dashboard, find the `api` resource
   - Click its HTTP endpoint (or copy the URL)
   - Append `/health` to the URL (e.g., `http://localhost:5123/health`)
   - You should see `Healthy` or a 200 OK response

2. **Check the React frontend:**
   - In the dashboard, find the `web` resource
   - Click its HTTP endpoint (typically `http://localhost:5173` or similar)
   - You should see the recipe list page load with 12 seeded recipes displayed

3. **Verify Aspire integration:**
   - In the dashboard, go to the Structured Logs view
   - You should see log entries from both `api` and `web` services
   - Check the Traces view — you should see HTTP requests being tracked

### Troubleshooting

**AppHost won't start:**
- Ensure .NET 10 SDK is installed: `dotnet --version` (should show 10.x.x)
- Check if the Aspire workload is installed: `dotnet workload list` (should show `aspire`)
- If missing: `dotnet workload install aspire`

**Dashboard URL doesn't open:**
- The URL is printed in the terminal when AppHost starts — look for a line like `Aspire Dashboard: https://localhost:17xyz`
- If you closed the terminal, check `dotnet run` output again
- The dashboard requires HTTPS; if you see certificate warnings, accept them for localhost
- Run `dotnet dev-certs https --trust` to trust your developer certificate on your machine

**Web service fails to start:**
- The AppHost runs `npm run dev` for you automatically
- If it fails, check that Node.js 22+ is installed: `node --version`
- Ensure `src/RecipeHub.Web/package.json` exists and dependencies are present
- The AppHost will show detailed error logs in the dashboard's Logs view

**Ports conflict:**
- Aspire assigns ports dynamically — both API and Web ports are randomized
- If a conflict occurs anyway, stop other local services or restart the AppHost
- Check the dashboard Resources view to see which ports were assigned

### Stopping the Application

Press **Ctrl+C** in the terminal where `dotnet run` is active. This stops the AppHost, which in turn stops both the API and Web services cleanly.

## Success Criteria

- [ ] `squad doctor` reports all checks passing (Except for the `.squad/` folder)
- [ ] `gh auth status` shows you are authenticated to GitHub.com
- [ ] `squad --version` prints the installed version (confirms Squad CLI is working)
- [ ] The Aspire AppHost starts successfully and the Aspire Dashboard is accessible in your browser
- [ ] Both `api` and `web` resources show as healthy in the Aspire Dashboard
- [ ] The API health endpoint returns 200 OK
- [ ] The React frontend loads and displays the recipe list
- [ ] At least one Copilot interface responds to a test prompt

## Learning Resources

- Squad repository: https://github.com/bradygaster/squad
- GitHub Copilot CLI installation: https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli
- GitHub CLI authentication: https://cli.github.com/manual/gh_auth_login

## Hints

<details>
<summary>Hint 1: GitHub CLI authentication trouble</summary>

If `squad doctor` fails on the GitHub CLI check, run `gh auth login` and select GitHub.com with HTTPS. Choose "Login with a web browser" when prompted. After completing the browser flow, run `gh auth status` to confirm. Then retry `squad doctor`.

</details>

<details>
<summary>Hint 2: Copilot CLI not found</summary>

The Copilot CLI extension is separate from the GitHub CLI itself. If `copilot` is not recognized as a command, you may need to install it as a `gh` extension:

```
gh extension install github/gh-copilot
```

After installation, use `gh copilot` to start an interactive session. Check the installation docs linked above for details on your platform.

</details>

<details>
<summary>Hint 3: Getting the starter app running</summary>

The application uses .NET Aspire 13.2 for orchestration. You don't start the API and Web projects separately — instead, run the AppHost, which manages both:

```
dotnet run --project src/RecipeHub.AppHost
```

The AppHost will:
- Build the API project (`src/RecipeHub.Api/`)
- Start the Web project (`src/RecipeHub.Web/`) using `npm run dev`
- Launch the Aspire Dashboard (check terminal output for the URL, typically `https://localhost:17xxx`)

In the dashboard, you'll see both `api` and `web` resources listed with their health status and endpoints. Click on an endpoint to navigate directly to it. If ports conflict or services don't start, check the dashboard's logs view for detailed error messages.

</details>
