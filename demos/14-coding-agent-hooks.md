# Demo 6: Using GitHub Copilot Hooks to Run Custom Scripts

> [!NOTE]
> This is a demo so it means that the trainer has to run the demo himself

In this demo, we'll use GitHub Copilot **hooks** to execute a custom bash/PowerShell script automatically when a Copilot agent session starts. The hook will print session start time when the session begins.

Hooks allow you to extend and customize agent behavior by executing shell commands at key points during execution—such as session start, session end, prompt submission, or tool use.

## Prerequisites

- A GitHub account with Copilot enabled and access to the cloud agent.
- A repository where you have write access.

## Environment configuration

- Create a new repository from [ps-copilot-sandbox/copilot-agent-and-mcp](https://github.com/ps-copilot-sandbox/copilot-agent-and-mcp) which is a template repository or reuse an existing repository.
- Clone the repository on your laptop.
- Use the instructions in the [`README.md` file](/README.md) to set up your development environment.

## Key concept: Hooks

Hooks are defined in JSON configuration files placed in the `.github/hooks/` directory of your repository. They must be present on the **default branch** to take effect.

### Available hook triggers

| Trigger | When it runs |
|---|---|
| `sessionStart` | When a Copilot agent session begins |
| `sessionEnd` | When a Copilot agent session ends |
| `userPromptSubmitted` | Each time the user submits a prompt |
| `preToolUse` | Before a tool (e.g., bash, file edit) is invoked |
| `postToolUse` | After a tool completes |
| `errorOccurred` | When an error occurs during the session |

## Setup Steps

### Step 1 — Create the hook configuration file

Create a new file at `.github/hooks/recordSessionhook.json` with the following content:

```json
{
  "version": 1,
  "hooks": {
    "sessionStart": [
        {
            "type": "command",
            "bash": "echo \"Session started: $(date)\" >> logs/session.log",
            "powershell": "Add-Content -Path logs/session.log -Value \"Session started: $(Get-Date)\"",
            "cwd": ".",
            "timeoutSec": 10
        }
    ]
  }
}
```

> [!TIP]
> Keep this hook intentionally simple for demo purposes so you can quickly validate that `sessionStart` is firing.

### Step 2 — Commit and push to the default branch

```bash
git add .github/hooks/recordSessionhook.json
git commit -m "Add recordSessionhook for sessionStart"
git push
```

> [!IMPORTANT]
> The hooks configuration file **must** be on the default branch (e.g., `main`) to be picked up by the Copilot cloud agent.

### Step 3 — Start a Copilot agent session

1. Open your repository on GitHub.
2. Make sure the `logs/session.log` file exists (create an empty file if needed) and is writable.
3. Start a new Copilot agent session (e.g., via GitHub Copilot Workspace or the cloud agent interface) and ask "what's the latest version of dotnet?"
4. The `sessionStart` hook fires automatically and logs the session start time to `logs/session.log`.
5. Verify the output in the `logs/session.log` file.

## Troubleshooting

| Problem | Solution |
|---|---|
| Hook does not execute | Verify the JSON file is in `.github/hooks/`. Check for valid JSON (`jq . recordSessionhook.json`). Ensure `"version": 1` is present. |
| Hook times out | The default timeout is 30 seconds. Increase `timeoutSec` if needed for slower environments. |
| No output is visible | Confirm your hook uses the correct shell key (`bash` or `powershell`) and inspect session logs for hook output. |

## Further reading

- [About hooks](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-hooks)
- [Hooks configuration reference](https://docs.github.com/en/copilot/reference/hooks-configuration)
- [Using hooks with GitHub Copilot agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-hooks)
- [About GitHub Copilot cloud agent](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent)
