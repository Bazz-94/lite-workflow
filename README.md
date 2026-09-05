# Task workflow skills

A lite, 3-step AI workflow that takes an idea from rough to reviewed, staged code, with the artifacts on
disk as the only state — so any session can be interrupted and picked up later.

## Workflow

| Step | Command                          | Output                                                |
| ---- | -------------------------------- | ----------------------------------------------------- |
| 1    | `/ideate-lite <idea>`            | a `task.md` per PR-sized task                         |
| 2    | `/plan-lite <task id>`           | a `plan.md` of commit-sized sub-tasks                 |
| 3    | `/build-lite <task id> [--auto]` | reviewed code changes in the working tree             |

`/clear` between steps. Each one starts from the artifacts on disk, and the skills say so when they hand off.

Steps 1 and 2 are conversations — you approve the breakdown before anything is written, and re-running either with
the same id amends what it produced. Step 3 iterates sub-task by sub-task, each one reviewed by a sub-agent and then
by you; `--auto` drops the per-sub-task review so it runs unattended, and you review the whole task at the end.

The agent leaves its work in the working tree. Staging, committing and raising the PR are yours.

## Status

`/status-lite [repo name]` reports where every task stands, across repos. It is read-only — a table in the chat,
nothing written to disk. Add the repos it watches to
[.claude/skills/status-lite/tracked-repos.md](.claude/skills/status-lite/tracked-repos.md); with no repo name it
reports all of them.

## Artifacts

```
artifacts/lite-workflow/{task id}/
  task.md
  plan.md
  {module}.{ext}
```

The `{module}.{ext}` files are the interfaces `/plan-lite` writes for the modules under the plan's **API Design**.
They are the API contract `/build-lite` implements against, and they stay in the task directory.

A task id is a two-letter code plus a number — `aa-00`, `aa-05`. The code is allocated once per ideate session and
shared by every task that session produces, so related tasks stay recognisable while each stands on its own.

Commit the artifacts alongside the code. They are the workflow's only state, so they need to survive an interrupted
session and travel with the branch. Delete a task's directory once it is done.
