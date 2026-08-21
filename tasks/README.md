# Task files

Task files provide bounded, reviewable checkpoints. They do not replace the [canonical architecture](../architecture/architecture-context-v1.md), [ADRs](../docs/architecture/decisions/README.md), or current GitHub state.

## Convention

Use one checkpoint or bounded task per numbered Markdown file. Every task file must state:

- task status;
- objective and explicit scope;
- explicit out-of-scope work;
- dependencies and required prior gates;
- acceptance criteria;
- required validation;
- expected Git branch, commits, push, and pull-request output.

Task files describe work; they do not authorize work from a later phase. `NEXT-TASK.md` identifies the only next task, and `PROJECT-STATUS.md` records the roadmap state.

## Status values

- `PENDING`: not yet selected as the next checkpoint.
- `NEXT`: selected by `NEXT-TASK.md`, but not started.
- `IN PROGRESS`: active on a branch or pull request.
- `COMPLETE`: reviewed and merged into the authoritative GitHub branch.
- `BLOCKED`: cannot proceed without a documented decision or external prerequisite.

Before starting a task, follow the complete reading order in [`AGENTS.md`](../AGENTS.md).
