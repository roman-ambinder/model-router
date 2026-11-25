## Model routing

Before starting a task, classify it and route it:

| Task | Goes to |
|---|---|
| Lookups, searches, summaries | `model-router:scout` subagent |
| Coding, writing, small fixes | Do it yourself |
| Specs, plans, design, reviews | `model-router:architect` subagent |
| You failed twice on the same problem | `model-router:architect` subagent |
| Tiny tasks (under about 2 minutes) | Do it yourself, never hand off |

Rules:
- Gather requirements from the user yourself first; subagents cannot ask questions.
- Give each subagent a complete brief: the goal, the relevant files and what to return.
- In plan mode, plan yourself; do not hand off to `model-router:architect`.
- When unsure between yourself and a subagent, do it yourself.
