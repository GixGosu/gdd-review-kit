# GDD Multi-Agent Review Kit

Six AI reviewers tear apart your game design document in parallel, then
cross-examine each other. A moderator synthesizes the result. A second
agent team builds an interactive visualization page. You get ranked
problems, unresolved disagreements, and two HTML reports. It supports
Claude Code and Codex.

Review team: systems designer, narrative critic, player psychologist,
feasibility lead, adversarial QA, business analyst, and an optional visual
reviewer for mockups.

Visualization team: data extractor, viz designer, HTML builder, viz
reviewer.

## Requirements

Either of the following:

- [Claude Code](https://docs.claude.com/en/docs/claude-code/overview),
  installed and authenticated
- Codex, with filesystem access to this repository and image-viewing support

## Setup

1. Clone or unzip this kit. You get:
   - `.claude/agents/` with ten agent definitions (six reviewers, four
     for the visualization pipeline)
   - `CLAUDE.md` for the Claude Code workflow and `AGENTS.md` for the Codex
     workflow
   - `codex/roles/visual-reviewer.md` for optional mockup review
   - `codex/roles/implementation-reviewer.md` and a private implementation
     context template for optional platform-aware review
   - a revision-handoff workflow that turns review evidence into an editing
     brief and a paste-ready external-editor prompt
   - `reviews/` where output lands
2. Drop the complete GDD into **`input/`** as a PDF, Word document (`.docx`),
   Markdown, or plain-text file. No trimming, transcription, or conversion is
   required. The Codex workflow reads the full document and creates its own
   review-only text extract. `gdd.txt` in the project root remains supported
   for the original Claude Code workflow.
3. Clear out `reviews/` if it has files from a previous run.
4. Optional: place UI mockups, wireframes, or screenshots in `mockups/`.
   Supported formats are PNG, JPG, JPEG, WEBP, and GIF. These files stay local
   and are not committed by default.
5. Optional: copy `context/implementation-context.example.md` to
    `context/implementation-context.md` and add a concise, approved profile of
    your target platform or codebase. This private file lets Codex add an
    implementation reviewer without requiring everyone to have the same local
    checkout. It is ignored by Git.
6. Optional: copy `context/approved-decisions.example.md` to
   `context/approved-decisions.md` before requesting a revision handoff. Put
   only decisions the design owner has actually approved in it; the file stays
   private and is ignored by Git.

## Running a review

### Claude Code

Open a terminal here, run `claude`, then type these one at a time:

    Run Round 1.

    Run Round 2.

    Run Round 3.

    Run Round 4.

    Run Round 5.

Read the output between rounds.

### Codex

Open this repository in Codex and request the rounds one at a time:

    Run Round 1.

    Run Round 2.

    Run Round 3.

    Run Round 4.

    Run Round 5.

    Create a revision handoff.

    Validate this revised GDD against the revision brief.

Codex reads `AGENTS.md` automatically. It reuses the existing six reviewer
briefs, reads the full GDD from `input/` without requiring a shortened copy,
and adds the visual reviewer whenever `mockups/` contains images or the source
document itself contains reviewable visual material. When fewer than six worker
slots are available, Codex runs reviewers in parallel batches while keeping
each review context independent. When a private implementation context is
present, it also adds an implementation reviewer that distinguishes
config-only work from new or conflicting engineering work.

After Round 3, you can request `Create a revision handoff.` Codex writes a
page/section-specific `reviews/REVISION-BRIEF.md` and a paste-ready
`reviews/EXTERNAL-EDITOR-PROMPT.md`; it does not silently alter the source
GDD. After an external editor produces a replacement document, place it in
`input/` and ask Codex to validate it against the brief.

### What each round does

**Round 1** spawns all six reviewers in parallel. Each gets its own
isolated context with only the GDD and its role. Findings go to
`reviews/<agent>.md`. In Codex, the complete source is first normalized into
`reviews/GDD-SOURCE.md`; a visual reviewer is added when `mockups/` contains
assets or the GDD includes visual material.

**Round 2** re-spawns the reviewers, this time with access to all six
Round 1 files. They argue with each other: flag conflicts, find issues
that only show up when you combine two lenses, revise their own calls.

**Round 3** turns the main session into a moderator. It reads everything
and writes `reviews/SYNTHESIS.md` with a ranked top-5, unresolved
disagreements, quick wins, and a verdict. When mockups were reviewed, the
synthesis calls out the visual findings that affect the top issues.

**Round 4** generates `review-board.html`, a self-contained dark-themed
report you can open in any browser. Designed to be readable on a
projector from the back of a room. Codex includes mockup filenames and their
linked findings without embedding the original images.

**Round 5** runs the visualization team. A data extractor pulls
structured JSON from the reviews. A viz designer picks the right charts
and interactions. An HTML builder produces `review-viz.html` with
interactive filtering, severity breakdowns, a reviewer agreement matrix,
and drill-downs into each finding's cross-examination trail. A viz
reviewer audits the result for accuracy. This round runs in phases
because each agent depends on the one before it.

**Revision handoff** is an opt-in step after synthesis. It separates approved
product decisions from reviewer recommendations, maps edits to the original
document, and gives a document editor concrete replacement content and
acceptance checks. **Revision validation** checks a returned full GDD against
that brief, including rendered visual material where relevant.

## Example GDD

Want to test-drive it? The Deus Ex "Majestic Revolutions" design doc
works well:

https://archive.org/stream/DeusExDesignDoc11081997/Majestic%20Revolutions%20-%20Joe%20Martin_djvu.txt

For Claude Code, save it as `gdd.txt`. For Codex, place the complete text file
in `input/` and run all five rounds—no trimming required.

## Troubleshooting

**A review file is empty or missing.** Tell Claude: "Re-run just the
\<name\> reviewer for Round 1."

**Reviews are too vague.** "Re-run Round 1; every finding must closely
reference a specific passage from gdd.txt."

**Round 2 is all agreement.** "Re-run Round 2 for systems-designer and
player-psychologist as a direct debate on the document's central design
tension."

**Reviews are slow.** Trim `gdd.txt` to a shorter excerpt.

**Round 5 visualization has accuracy errors.** The viz-reviewer writes
its audit to `reviews/viz-audit.md`. Tell Claude: "Re-run the
html-builder with the fixes from the viz audit, then re-run the
viz-reviewer."
