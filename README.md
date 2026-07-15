# GDD Multi-Agent Review Kit (Claude Code)

Six AI reviewers tear apart your game design document in parallel, then
cross-examine each other. A moderator synthesizes the result. A second
agent team builds an interactive visualization page. You get ranked
problems, unresolved disagreements, and two HTML reports. The whole
thing runs in one Claude Code terminal.

Review team: systems designer, narrative critic, player psychologist,
feasibility lead, adversarial QA, business analyst.

Visualization team: data extractor, viz designer, HTML builder, viz
reviewer.

## Requirements

[Claude Code](https://docs.claude.com/en/docs/claude-code/overview),
installed and authenticated.

## Setup

1. Clone or unzip this kit. You get:
   - `.claude/agents/` with ten agent definitions (six reviewers, four
     for the visualization pipeline)
   - `CLAUDE.md` with orchestration rules for all five rounds
   - `reviews/` where output lands
2. Drop your game design document in as **`gdd.txt`**. Plain text. If
   it's long, trim to the pitch and gameplay overview sections. A few
   thousand words is the sweet spot.
3. Clear out `reviews/` if it has files from a previous run.

## Running a review

Open a terminal here, run `claude`, then type these one at a time:

    Run Round 1.

    Run Round 2.

    Run Round 3.

    Run Round 4.

    Run Round 5.

Read the output between rounds.

### What each round does

**Round 1** spawns all six reviewers in parallel. Each gets its own
isolated context with only the GDD and its role. Findings go to
`reviews/<agent>.md`.

**Round 2** re-spawns the reviewers, this time with access to all six
Round 1 files. They argue with each other: flag conflicts, find issues
that only show up when you combine two lenses, revise their own calls.

**Round 3** turns the main session into a moderator. It reads everything
and writes `reviews/SYNTHESIS.md` with a ranked top-5, unresolved
disagreements, quick wins, and a verdict.

**Round 4** generates `review-board.html`, a self-contained dark-themed
report you can open in any browser. Designed to be readable on a
projector from the back of a room.

**Round 5** runs the visualization team. A data extractor pulls
structured JSON from the reviews. A viz designer picks the right charts
and interactions. An HTML builder produces `review-viz.html` with
interactive filtering, severity breakdowns, a reviewer agreement matrix,
and drill-downs into each finding's cross-examination trail. A viz
reviewer audits the result for accuracy. This round runs in phases
because each agent depends on the one before it.

## Example GDD

Want to test-drive it? The Deus Ex "Majestic Revolutions" design doc
works well:

https://archive.org/stream/DeusExDesignDoc11081997/Majestic%20Revolutions%20-%20Joe%20Martin_djvu.txt

Save it as `gdd.txt`, trim to the intro pitch and gameplay overview if
you want faster results, and run all five rounds.

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
