# Demo 7: GitHub Copilot CLI + Microsoft Work IQ

> [!NOTE]
> This is a demo so it means that the trainer has to run the demo himself

In this demo, we'll install **GitHub Copilot CLI** and set up **Microsoft Work IQ** — a plugin that grounds Copilot in your Microsoft 365 knowledge graph (emails, meetings, Teams messages, and documents) — so your terminal becomes context-aware of your entire workday. As an optional extension, we also show how to use the **Azure MCP Server** and the `/fleet` command to deploy the project to Azure directly from the CLI.

## Prerequisites

- A GitHub account with Copilot enabled (any plan).
- A **Microsoft 365 account** with Copilot enabled (required for Work IQ).
- Node.js 16+ installed.

> [!IMPORTANT]
> If you receive Copilot through an organization, an admin must first enable the **GitHub Copilot CLI** policy in the organization's settings before the CLI will work.

---

## Step 1 — Install GitHub Copilot CLI

Choose the installation method that matches your environment:

### Cross-platform (npm) — recommended for demo consistency

```bash
npm install -g @githubnext/github-copilot-cli
```

### Windows (WinGet)

```powershell
winget install GitHub.Copilot
```

### macOS / Linux (Homebrew)

```bash
brew install copilot-cli
```

Verify the installation:

```bash
copilot --version
```

---

## Step 2 — Authenticate and start your first session

1. Navigate to the project root:

   ```bash
   cd copilot-agent-and-mcp
   ```

2. Start an interactive CLI session:

   ```bash
   copilot
   ```

3. On first run you'll be prompted to sign in. Type `/login` and follow the on-screen device-flow instructions to authenticate with your GitHub account.

   > You only need to do this once.

4. When asked whether you trust the files in the current directory, select **Yes, proceed**.

5. Run a warm-up question against this repo to confirm it's working:

   ```
   Give me an overview of this project.
   ```

   Copilot reads the local files and returns a structured summary of the Express backend, React frontend, and test suite.

---

## Step 3 — Learn the key commands and shortcuts

| Shortcut / Command | What it does |
|---|---|
| `Esc` | Cancel the current operation |
| `Ctrl + C` | Cancel if thinking, clear input, or exit |
| `Ctrl + L` | Clear the screen |
| `Ctrl + T` | Toggle reasoning visibility |
| `Shift + Tab` | Toggle plan mode on/off |
| `@<path>` | Attach a local file as context |
| `/login` | Authenticate with GitHub |
| `/plan` | Draft an implementation plan before coding |
| `/agent` | Select a built-in or custom agent |
| `/model` | Switch AI model mid-session |
| `/delegate` | Offload a task to Copilot cloud agent |
| `/compact` | Manually compress conversation history |
| `/context` | Show current token usage breakdown |
| `/usage` | Show session statistics |
| `/mcp add` | Add an MCP server |
| `/plugin marketplace add` | Enable a plugin source |
| `/plugin install` | Install a plugin |
| `/help` | Full list of commands |

### Non-interactive (scripting) mode

You can call Copilot without an interactive session using `-p`:

```bash
copilot -p "What routes does the backend expose in this project?"
```

Add `-s` to get only the answer — useful in CI pipelines:

```bash
copilot -sp "List all environment variables this app reads at startup"
```

---

## Step 4 — Integrate Microsoft Work IQ

Work IQ is a Microsoft 365 plugin for Copilot CLI. Once installed, your terminal can answer questions about your calendar, emails, Teams conversations, and documents — grounding Copilot in your actual workday context alongside your code.

### 4a — Enable the Copilot plugin marketplace

Inside the interactive Copilot session, run:

```
/plugin marketplace add github/copilot-plugins
```

This registers the Microsoft-provided plugin source.

### 4b — Install the Work IQ plugin

```
/plugin install workiq@copilot-plugins
```

Copilot downloads and registers the Work IQ plugin.

### 4c — First-time setup (EULA + Microsoft sign-in)

Trigger the initialization by asking a workplace question:

```
How many meetings do I have today?
```

You'll be prompted to:
1. Accept the Work IQ End User License Agreement.
2. Sign in with your **Microsoft work account** (separate from your GitHub authentication).

Once completed, you'll see confirmation that Work IQ is active:

```
● skill(workiq)
● WorkIQ is ready to answer workplace questions
```

### 4d — Verify the integration

Try these validation prompts:

```
What meetings do I have today?
```

```
Summarize the action items from my last Teams meeting.
```

```
Find any documents I worked on this week related to API design.
```

---

## Step 5 — Impressive customer demo: Work IQ in action

This is the **wow moment**. Copilot CLI surfaces your Microsoft 365 work context directly in the terminal — no context-switching between apps.

### Scenario

A customer reported an issue via email and it was discussed in a Teams meeting. You need to surface the details quickly without leaving your terminal.

> [!NOTE]
> **Setup for the demo presenter:** Before running this demo, send the following email from your Microsoft 365 account to yourself (or have a colleague send it to you) so Work IQ has something to surface. You can also paste it into a Teams message in a relevant channel.

---

> **From:** Sarah Mitchell &lt;sarah.mitchell@contoso.com&gt;  
> **To:** dev-team@contoso.com  
> **Subject:** Bug Report – Books API returning wrong favorites for shared accounts  
> **Date:** Monday, April 14, 2026 at 10:23 AM  
>
> Hi team,
>
> We've had a few complaints from enterprise customers who share a device login. When two users on the same account access **My Favorites** through the books app, they're seeing each other's saved books instead of their own.
>
> Steps to reproduce:
> 1. Log in as User A, add 2–3 books to favorites.
> 2. Log out. Log in as User B on the same device.
> 3. Navigate to "My Favorites" — User A's favorites appear instead of User B's.
>
> This looks like the favorites are being cached against the device session rather than the authenticated user's JWT. Happy to jump on a call to walk through it.
>
> Affected customers: Northwind Traders, Fabrikam Inc.  
> Priority: High — both customers are up for renewal next month.
>
> Sarah

---

Now, in the interactive Copilot CLI session, type the following prompt:

```
Search my recent emails and Teams messages for any customer feedback about 
the books API. Summarize what was reported.
```

Work IQ surfaces the relevant context from the email. Follow up to dig into the details:

```
Who sent the email about the books API bug? Which customers are affected and what is the priority?
```

Then use the work context to plan your response:

```
Based on the customer feedback you found, draft a brief technical summary of 
the issue and the proposed fix I can share with the team.
```

The result: **customer feedback surfaced and summarized**, entirely within your terminal — no copy-pasting between Outlook, Teams, and your editor.

---

## Optional Demo: Deploy to Azure with /fleet + Azure MCP Server

> [!NOTE]
> This optional demo requires an active Azure subscription and the Azure CLI authenticated (`az login`).

This demo shows how to combine the GitHub Copilot CLI `/fleet` command with the **Azure MCP Server** to deploy the backend to **Azure App Service** — entirely from your terminal, without opening the Azure portal.

### Prerequisites

- Active Azure subscription.
- Azure CLI installed and authenticated (`az login`).
- Project running locally (confirm with `npm run test:backend`).

### OA1 — Add the Azure MCP Server to your Copilot CLI session

Inside the interactive Copilot session, register the Azure MCP Server:

```
/mcp add 
```
- Name: azure (should auto populate)
- Type: STDIO (default)
- Start Command: npx -y @azure/mcp@latest server start

save the configuration and exit MCP configuration.

The Azure MCP Server and its 40+ Azure service tools are now available in your session. Confirm they loaded:

```
/mcp show azure
```

You should see `azure` listed under active MCP servers with a tool count.

### OA2 — Survey your Azure environment

Before provisioning, let Copilot retrieve your current Azure context:

```
List my Azure resource groups and App Service plans.
```

The Azure MCP Server queries your subscription in real time and returns the results. Note a target region and resource group for deployment.

### OA3 — Deploy with /fleet

Use `/fleet` to orchestrate a multi-step deployment. The command spawns parallel sub-agents, each responsible for one stage of the deployment pipeline:

```
/fleet Deploy this Node.js project to Azure App Service.
The backend is at ./backend and listens on port 3000.
Target resource group: rg-demo-agents, region: eastus.
Create the resource group if it does not exist.
```

Copilot coordinates the following tasks in parallel where possible:

1. Creates resource group `rg-demo-agents` (if absent).
2. Provisions an App Service Plan (Free tier).
3. Creates the Web App and configures the Node.js 20 runtime.
4. Packages and deploys the backend via `az webapp deploy`.
5. Sets application settings (`NODE_ENV=production`, `PORT=3000`).

Each sub-agent reports back; the fleet coordinator summarises the outcome.

To view and manage subagent progress, use the following command:
```
/tasks
```


### OA4 — Verify the deployment

```
Get the hostname and deployment status of the web app in resource group rg-demo-agents.
```

Open the returned URL in your browser to confirm the backend is live.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `copilot` command not found | Verify Node.js 16+ is installed. Run `npm install -g @githubnext/github-copilot-cli` again. |
| Authentication fails | Run `/login` inside an interactive session and follow the device flow. |
| CLI policy blocked | Ask your org admin to enable the Copilot CLI policy in organization settings. |
| `ask_work_iq` skill not found | Use natural language prompts instead (e.g. "What meetings do I have?"). The alias resolves automatically. |
| "Failed to Create Conversation" (Work IQ) | Check Microsoft Entra Conditional Access / token-protection policies with your M365 admin. |
| Context window filling up | Use `/compact` to compress history, or `/new` to start a fresh session. |
| Azure MCP tools not listed after `/mcp add` | Ensure Node.js 16+ is installed. Run `/context` to refresh. Restart the Copilot session if needed. |
| Azure MCP authentication error | Run `az login` in a separate terminal before starting the Copilot CLI session. |
| `/fleet` times out during deployment | Break the task into smaller steps: create resources first, then deploy separately. |

---
