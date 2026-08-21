# Project status

This file is a navigation summary. Accepted architecture remains in the [canonical context](architecture/architecture-context-v1.md) and [ADRs](docs/architecture/decisions/README.md).

## Current state

| Field | Value |
|---|---|
| Current architecture version | `v1.0` |
| Authoritative `main` | Resolve the current SHA from GitHub; repository state is authoritative |
| Agent Handoff System merge SHA | `81ac2cb7023dd33329d8ab3e4b8d1316d40dd3c5` |
| Latest completed architecture gate | Architecture v1.0 Lock |
| Current task | `[2] API Contract v1` — in progress on branch `codex/api-contract-v1` |
| Next task | `[3] Backend Foundation` — [`tasks/003-backend-foundation.md`](tasks/003-backend-foundation.md) (after API Contract v1 review and merge) |

## Roadmap

| Phase | Checkpoint | Status |
|---:|---|---|
| 0 | Canonical Context | **COMPLETE** |
| 1 | Architecture v1.0 Lock | **COMPLETE** |
| 1.5 | Agent Handoff System | **COMPLETE** |
| 2 | API Contract v1 | **IN PROGRESS** |
| 3 | Backend Foundation | **PENDING** |
| 4 | Local Infrastructure | **PENDING** |
| 5 | Identity/Auth | **PENDING** |
| 6 | Accounts | **PENDING** |
| 7 | Fleet | **PENDING** |
| 8 | Freight | **PENDING** |
| 9 | Marketplace | **PENDING** |
| 10 | Shipment | **PENDING** |
| 11 | Communication | **PENDING** |
| 12 | Mobile/Web | **PENDING** |

Statuses change only when the corresponding review gate is completed in GitHub. Phase 2 becomes **COMPLETE** when the API Contract v1 PR is reviewed and merged.
