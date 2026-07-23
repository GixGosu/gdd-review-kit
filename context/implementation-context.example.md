# Implementation context template

Copy this file to `context/implementation-context.md` and fill it with only
the facts that matter for design review. The real file is ignored by Git so it
can describe an internal codebase without being published with the kit.

## Scope

- Target platform or repository:
- Branch or release baseline:
- What this profile covers:
- What is intentionally out of scope:

## Confirmed capabilities

List the existing behaviors the GDD can reuse. Include concise source paths or
owner-approved references when helpful.

- Example: Supports multi-stage events with independent per-stage scoring.
- Example: Supports teams with a fixed maximum size of four.

## Constraints and known gaps

List hard limits, data-model distinctions, or missing capabilities that can
invalidate a GDD assumption.

- Example: Matchmaking segments by skill rating; it does not use event-earned
  progression points.
- Example: Reward tables vary by mode but not by join day.

## Review questions

List the implementation questions that should be answered before design lock.

- Example: Can the event use variable competition sizes, or must MVP use one
  fixed size?
- Example: What determines eligibility for late joiners?
