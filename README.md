# ProjectCosmos: a small AI delivery team for Claude Code

One file, `cosmos_agents.py`, holds the whole team: 10 role agents, the shared
rules and the Team Leader's runbook. Standard library only. Edit the team here,
then install it into any project.

## Get it on another machine

```powershell
gh repo clone 220404773/agents C:\dev\agents    # once
git -C C:\dev\agents pull                        # later, for updates
```

Then run `install` from that folder into each project. To change the team, edit
`cosmos_agents.py` here, commit and push, then re-run `install` in each
project that should get the change (`check` shows which projects differ).

## Use it

```powershell
python cosmos_agents.py install C:\dev\my-project     # write .claude/ into a project
python cosmos_agents.py check   C:\dev\my-project     # which files differ from this file
python cosmos_agents.py list                          # agents, models, tool-call caps
python cosmos_agents.py install . --profile standard  # opus where economy uses sonnet
python cosmos_agents.py install . --model architect=opus
```

`install` is safe to re-run: changed files are backed up to
`.claude/.cosmos-backup/<time>/` first. `project.md` (per-project settings) is
created once and never overwritten. **Restart Claude Code after installing.**

## Start a new project

1. Create the folder **outside OneDrive** (e.g. `C:\dev\<name>`) and `git init`.
2. `python cosmos_agents.py install C:\dev\<name>`
3. Fill in `.claude\skills\projectcosmos\project.md` (purpose, stack, test
   command, cost ceiling, files never to touch, services never to call live).
4. Open the folder in Claude Code and say `develop <what you want>`.
   `-- full` runs every stage; quick is the default. A small fix needs no mode.

## How the team works (v3.1)

- The Team Leader (the main session) **staffs by expertise**: HTML, email and UI
  go to the frontend engineer only; data and logic to the backend engineer only;
  a one-module fix goes to its owner (and the reviewer if non-trivial). Lead
  engineer only when two or more engineers share a contract. Ideologist and
  Architect only when the problem or design is new. Security only when secrets,
  auth, untrusted input, dependencies or deploy credentials are involved. DevOps
  only when something deploys.
- Pipeline order: idea, requirements, design, build, review, test, security,
  deploy, docs, sign-off. The **BA is dispatched exactly twice**.
- Fix loops: all findings in one message; quick mode one round, full two.
- Every agent has a **tool-call cap** and **per-document line budgets**.
- Token ledger: about 350k agent tokens for quick, about 1M for full; drop
  optional stages at 70%, stop and ask at 100%.

## Lessons from the first run (eTenders digest, Sept to Oct 2026)

- Capped agents used 20-37k tokens a stage; uncapped ones 80-330k.
- Resumed agents carry their whole history: prefer a fresh agent per stage.
- 1,000-line artifacts get re-read by every later stage. Keep them short.
- Cloud-synced folders (OneDrive) re-send whole changed files into the session
  as notices. Work in a plain local folder such as `C:\dev`.
- A usage limit can cut an agent off mid-edit: run the tests before resuming it.
- Never let an agent verify its own cost or price claims: check the vendor page.
- Agent and skill headers are YAML: an unquoted `: ` in a description breaks them.
- `.funcignore` (Azure Functions) uses gitignore rules: a bare `tools` deleted
  `pandas/core/tools` from the deployed packages. Anchor folders with `/tools`.
- After an Azure Functions deploy, confirm the function is registered
  (`az functionapp function list`), or the timer never fires.

## Files

| File | Purpose |
|---|---|
| `cosmos_agents.py` | The installer and the whole team definition (edit here) |
| `README.md` | This file |
| `ProjectCosmos Agent Team v1_1.md / .pdf` | The original v1.1 specification you wrote |
| `example-etenders-run/` | A real run's decision log, sign-off, deploy plan and filled-in `project.md` |
| `notes/agent_team_and_gotchas.md` | Usage notes and gotchas saved to Claude's memory |
