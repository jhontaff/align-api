# 0027 — Production, multi-user baseline (YAGNI dropped as a principle)

- **Status:** Accepted
- **Date:** 2026-10-29

## Context

`CLAUDE.md` described Align as a personal learning project and told every design to "follow YAGNI" and "implement only today's requirements". Several records justified trade-offs with "Align is single-user in practice".

Neither is true anymore. Align runs in production with multiple independent users. The question that made this explicit came from the bank-email ingestion design for Finance. Excluding transfers between a user's own accounts needs the user's account numbers, and a single list in `align.env` only works if there is exactly one user.

## Decision

- Align is a production platform with multiple independent users. YAGNI is removed as a guiding principle.
- Every feature is built to production standard from its first version:
  - **Multi-user safe:** personal data is owned by a `User` and scoped to it. No global lists, shared config or properties hold one user's data.
  - **Secure by default:** untrusted input is verified before use, and personal data is minimized in storage and logs.
  - **Operable:** failures are visible, and data is recoverable.
- Speculative generality is still avoided: an abstraction needs at least two real use cases. Production readiness is about the qualities of the feature being built, not about building features nobody asked for.

## Alternatives rejected

- **Keep YAGNI and treat multi-user as "today's requirement".** It would be technically consistent, because real users are a present requirement. It was rejected because the YAGNI framing had already been used to justify single-user shortcuts, and the project owner wants the production baseline stated explicitly.

## Consequences

- Records whose rationale relies on "single-user" must be re-reviewed: 0002, 0006, 0009, 0012, 0015, 0017, 0020 and 0022. Each one either still holds (a `Later notes` bullet says so) or gets a superseding record. Tracked in `CLAUDE.md` Open items.
- Backups (0017) and the shared Gemini key pool now handle other people's data. That raises their priority.
- New per-user concepts, such as "own bank accounts" for the email ingestion, are modelled as entities owned by `User`, never as configuration.

## Later notes

- 2026-10-29: `CLAUDE.md` reframed as a company product. The assistant's role changed from "mentor, learning first" to Senior Architect / Tech Lead, and a **Definition of done** was added. The developer still owns the implementation. The Definition of done adds expand/contract migrations, because the automatic deploy rollback restores the jar but not the schema.
