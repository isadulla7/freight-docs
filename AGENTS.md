# Agent entry point

This file is the vendor-neutral starting point for Codex, Claude Code, and future coding agents working on `isadulla7/freight-docs`.

## Required reading order

Before any work, read and reconcile these sources in order:

1. [`architecture/architecture-context-v1.md`](architecture/architecture-context-v1.md)
2. [`PROJECT-STATUS.md`](PROJECT-STATUS.md)
3. [`NEXT-TASK.md`](NEXT-TASK.md)
4. [`HANDOFF.md`](HANDOFF.md)
5. The ADRs relevant to the task, starting from the [ADR index](docs/architecture/decisions/README.md)
6. The task file referenced by `NEXT-TASK.md`
7. The current Git branch, working-tree status, remote branch, and open pull-request state

If these sources disagree, GitHub repository state is authoritative. Stop and record the conflict instead of guessing.

## Working rules

- GitHub repository state is the source of truth.
- Never depend on prior ChatGPT, Codex, Claude Code, or other conversation history.
- Inspect existing code and documentation before modifying them.
- Never silently change accepted architecture. Architecture-impacting changes require an ADR and review.
- Preserve the module boundaries defined by the canonical architecture documents.
- Prefer minimal complexity and avoid premature abstractions.
- Keep changes within the active task's explicit scope and out-of-scope boundaries.
- Never commit secrets, credentials, tokens, private keys, or environment-specific sensitive values.
- Use small, logical commits with clear messages.
- Validate links, formatting, consistency, and task-specific acceptance criteria before pushing.
- Before handing work to another agent, update `HANDOFF.md` with the current Git and task state.

Do not repeat architecture rules in agent-specific files. Link to the canonical context and accepted ADRs instead.
