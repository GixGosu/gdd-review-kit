# Approved design decisions

Copy this file to `context/approved-decisions.md` before requesting a revision
handoff. Record only decisions that the design owner has explicitly approved.
The real file is ignored by Git.

## Decision log

For each decision, include the exact rule, scope, and status. Avoid opinions or
unvalidated reviewer suggestions.

### Example: progression terminology

- **Status:** Approved
- **Decision:** Use one player-facing progression currency named `Event Points`.
- **Scope:** All GDD prose, UI copy, analytics, configuration, and support
  tooling.

### Example: late entry

- **Status:** Approved
- **Decision:** Teams entering after the first event day begin at zero earned
  Event Points; only stages available after entry count toward participation.
- **Scope:** Eligibility, progress thresholds, rewards, UI, analytics, QA, and
  support tooling.

## Open decisions

List questions that are not yet approved. The revision handoff will preserve
them as decisions to make, not silently choose an answer.

- Example: What is the maximum wait time before an incomplete team is
  auto-filled or offered an alternate path?
