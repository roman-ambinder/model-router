# model-router

Makes Claude pick the cheapest model that does each task well, with no manual switching. Built for the Code tab of Claude Desktop and for Claude Code.

## What it does

Models shown apply once step 3 of Install is done.

| Task | Handled by | Model |
|---|---|---|
| Lookups, searches, summaries | `scout` subagent | Haiku |
| Coding, writing, small fixes | Main session | Sonnet |
| Specs, plans, design, reviews | `architect` subagent | Opus |
| Main session failed twice on the same problem | `architect` subagent | Opus |
| Tiny tasks (under about 2 minutes) | Main session, never handed off | Sonnet |

How it works:

- Two subagents are pinned to their models.
- A routing table (`rules/routing.md`) is loaded into every session by a small start-up hook.
- The main session runs on `opusplan`: Sonnet for normal work, Opus while in plan mode.

## Install

1. Install the plugin from GitHub. In a terminal:

   ```bash
   claude plugin marketplace add roman-ambinder/model-router
   claude plugin install model-router@model-router
   ```

   Or inside a session: `/plugin marketplace add roman-ambinder/model-router`, then `/plugin install model-router@model-router`. You can also open the `model-router.plugin` file in Claude and accept it.
2. Start a new local session. Plugins do not load in WSL or cloud sessions.
3. Run `/model opusplan` once and press Enter to save it as your default. The plugin cannot set this for you. If `/model` is not available, add `"model": "opusplan"` to `~/.claude/settings.json` yourself.
4. Check that the session shows `opusplan` as its model. If you skip step 3, everything runs on your default model and you save nothing.

## Test it

Run these four prompts in any project:

| # | Prompt | Pass when |
|---|---|---|
| 0 | "Do you have a 'Model routing' table in your instructions? List its rows." | Claude lists the five rows |
| 1 | "Where is the main entry point of this project, and what does it do?" | The `model-router:scout` subagent runs |
| 2 | "Rename one local variable in that file to a clearer name." | No subagent runs |
| 3 | "Write a short design for adding rate limiting to this project." | The `model-router:architect` subagent runs, or the session enters plan mode |

## Tune it

Routing is driven by two things:

- Each agent's `description:` line in `agents/`. Claude reads it to decide when to hand off.
- The table in `rules/routing.md`.

Edit either one, raise `version` in `.claude-plugin/plugin.json`, and reinstall.

## Troubleshooting

- **Test 0 fails, or the hook reports an error.** Copy the contents of `rules/routing.md` into `~/.claude/CLAUDE.md` (on Windows: `%USERPROFILE%\.claude\CLAUDE.md`). Add it to the end; do not replace what is there.
- **`opusplan` is rejected, or plan mode stays on Sonnet.** Run `/model sonnet`, or pick Sonnet in the model picker. Planning still reaches Opus through the `architect` subagent.
- **Your account blocks Opus.** `architect` should run on another permitted model. If it errors, change `model: opus` to `model: sonnet` in `agents/architect.md`. `scout` routing still works.
- **Too many handoffs.** Make the agent descriptions narrower, or raise the "tiny tasks" limit in the table.

## License

MIT. See `LICENSE`.
