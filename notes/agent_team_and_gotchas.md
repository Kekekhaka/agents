---
name: reference-agent-team-and-gotchas
description: "Where the ProjectCosmos agent team lives, and tool gotchas hit on this machine"
metadata:
  node_type: memory
  type: reference
  originSessionId: 37f4ef6a-9a26-4d73-b518-17b569a1731b
  modified: 2026-10-05T12:48:42.311Z
---

**Agent team:** one file, `tools/cosmos_agents.py` (stdlib only). `python tools/cosmos_agents.py install <dir>` writes `.claude/agents/` and the runbook into any project; `check` shows drift; `list` shows models. `--profile economy` (default, Sonnet for Architect, Lead, Security, DevOps; Haiku for the lighter roles) or `--profile standard`. Per-project settings live in `.claude/skills/projectcosmos/project.md`, which install never overwrites. Restart Claude Code to load changed agents.

**Team v3.0 (2026-10-03) rules:** the Team Leader staffs by expertise (HTML/UI/email = frontend only, data/logic = backend only, one-module fix = owning engineer + reviewer only); lead-engineer only when 2+ engineers share a contract; ideologist and architect skipped when the brief or design already covers it; security only when a trigger applies; devops only when something deploys. **The BA is dispatched exactly twice** (requirements, then final docs). Fix loops: quick 1 round, full 2, all findings in one message. Each agent has a tool-call cap in its file. Token ledger: quick about 350k agent tokens, full about 1M; drop optional stages at 70%, stop and ask at 100%.

**Gotchas:**
- **`.funcignore` uses gitignore rules** (deploy action uses the npm `ignore` package): a bare `tools` also deletes `.python_packages/.../pandas/core/tools` and the function fails with `No module named 'pandas.core.tools'`. Always anchor project entries with a leading `/`. `tests/test_funcignore.py` guards this.
- **After any deploy, check the timer registered:** `az functionapp function list` must show `etenders_digest`. The deploy action did not re-sync triggers; if the list is empty, run `az rest --method post --url https://management.azure.com/subscriptions/<sub>/resourceGroups/etenders-rg/providers/Microsoft.Web/sites/etenders-digest-func/syncfunctiontriggers?api-version=2022-03-01`, otherwise scheduled runs silently never fire (only "past due" catch-ups when something wakes the app).
- **Read run results from App Insights** via `az rest --resource https://api.applicationinsights.io` (query the `RUN_SUMMARY` traces; `requests` table lists every execution). Needs `export MSYS_NO_PATHCONV=1`.
- YAML headers: an unquoted `: ` in a description breaks the agent or skill file. `cosmos_agents.py` quotes agent descriptions; keep the SKILL description colon-free.
- PowerShell: `$PID` is read-only (process ID); use another variable name. Use `-o none` on `az functionapp config appsettings set` so it doesn't print every secret.
- Git Bash: prefix `export MSYS_NO_PATHCONV=1` when passing `/subscriptions/...` scopes to `az`.
- `az consumption usage list` returns no costs on this subscription; use the Cost Management REST query (`az rest`), and the Azure Retail Prices API (`prices.azure.com`, `currencyCode=ZAR`) for prices. Microsoft's rate was R16.00 per US$.
- Never deploy between 05:45-06:15 or 17:45-18:15 UTC: a restart kills a run in progress.
- A usage limit can cut a subagent off mid-edit; check what it left half-done (run the tests) before resuming it.
