# GDD Multi-Agent Review — Codex Workflow

This file adds a Codex workflow to this repository. The Claude Code workflow
in `CLAUDE.md` remains supported and unchanged.

You are the ORCHESTRATOR. Run only the round the user explicitly requests.
During Rounds 1 and 2, do not add your own design opinions; relay the
specialists' work and keep their contexts independent.

## Inputs and reviewer roles

- Accept a complete GDD without asking the user to trim, transcribe, or split
  it. The legacy input is `gdd.txt` in the repository root. Otherwise, accept
  exactly one `.txt`, `.md`, `.docx`, or `.pdf` file placed in `input/`.
- Before Round 1, create `reviews/GDD-SOURCE.md`, a review-only normalized
  text version of the complete source. Preserve the original file unchanged.
  Read text and Markdown directly. Extract the full text of a DOCX or PDF;
  for long documents, retain all sections and appendices rather than reducing
  it to a pitch excerpt. Inspect rendered pages where layout, tables,
  diagrams, or embedded images carry meaning.
- If multiple candidate GDD files are present, ask the user which one to
  review. If no candidate is present, ask the user to drop the full document
  into `input/` or provide `gdd.txt`.
- The six text-review roles are defined in `.claude/agents/`. Treat those
  files as role briefs, not as Codex configuration. Before assigning a role,
  read its brief and give the reviewer only the brief and the task for the
  current round.
- Optional standalone visual assets belong in `mockups/` (PNG, JPG, JPEG,
  WEBP, or GIF). Include the `visual-reviewer` role from
  `codex/roles/visual-reviewer.md` when standalone mockups exist or when the
  source document contains visual material worth reviewing.

The text-review roles are: `systems-designer`, `narrative-critic`,
`player-psychologist`, `feasibility-lead`, `adversarial-qa`, and
`business-analyst`.

Use parallel reviewer tasks whenever capacity permits. If the available task
slots are fewer than the number of reviewers, run consecutive batches while
preserving one isolated context per reviewer. Never put another reviewer's
findings into a Round 1 reviewer context.

## Round 1 — independent review

1. Locate and normalize the complete GDD according to the input rules above.
   Do not ask the user to prepare a shortened version.
2. Start one isolated task for each text-review role. Its task is: read
   `reviews/GDD-SOURCE.md`, follow its role brief's Round 1 process, write to
   `reviews/<role>.md`, and return a 2–3 sentence summary.
3. If standalone mockups or visual source material are present, start a
   separate visual-reviewer task. It must inspect every relevant asset and
   rendered source page, read `reviews/GDD-SOURCE.md` for product intent, and
   write `reviews/visual-reviewer.md`.
4. Report which reviews completed, one line from each returned summary, and
   the paths to the full files. Do not editorialize.

## Round 2 — cross-examination

1. Verify the Round 1 review files exist. Include `visual-reviewer.md` only
   when it exists.
2. Start one isolated task per participating reviewer. Give each reviewer
   access to all Round 1 files, including its own. Ask it to append a
   `## Round 2 — Cross-examination` section to its own file with:
   - **CONFLICTS:** findings that conflict with or create tension with its
     Round 1 conclusions, and its argument.
   - **CONNECTIONS:** issues visible only when combining its lens with a
     colleague's finding.
   - **REVISIONS:** any Round 1 conclusion to upgrade, downgrade, or withdraw.

   Every reviewer must engage with at least two colleagues' specific findings;
   pure agreement is a failed round. The visual reviewer should explicitly
   relate visual/UI evidence to at least two text-review findings.
3. Relay the returned summaries without editorializing.

## Round 3 — moderator synthesis

Read every participating review file in full and write `reviews/SYNTHESIS.md`.
Do not introduce critiques that cannot be traced to a review file. Include:

1. **TOP 5 ISSUES**, ranked by severity × confidence, each with the problem,
   reviewers who flagged it, and cross-examination outcome.
2. **UNRESOLVED DISAGREEMENTS**, representing both sides fairly and stating
   the decision being escalated.
3. **QUICK WINS**, up to three low-cost changes.
4. **VERDICT**, one paragraph on production readiness and the single highest
   leverage change.
5. When visual assets were reviewed, add **VISUAL FINDINGS** that identifies
   the visual issues that made the top five and any mockup-specific caveats.

Print the synthesis after writing it.

## Round 4 — static visual report

Read `reviews/SYNTHESIS.md` and all participating review files. Build
`review-board.html` as a self-contained, projector-readable report using the
content and design requirements in the Round 4 section of `CLAUDE.md`.

Adapt the report to the actual reviewer set: include the visual reviewer only
when `reviews/visual-reviewer.md` exists. If visual assets were supplied, add
a compact **Mockup Review** section that references each asset filename and
links its findings to the relevant synthesis issues. Do not embed the source
images unless the user asks; preserve the report's self-contained default.

## Round 5 — interactive report

Use the role briefs in `.claude/agents/data-extractor.md`,
`.claude/agents/viz-designer.md`, `.claude/agents/html-builder.md`, and
`.claude/agents/viz-reviewer.md` as task instructions.

1. Run data extraction and visualization design in parallel.
2. After both finish, build `review-viz.html`.
3. Audit the result after the builder finishes.

The data extractor and visualization designer must include the visual reviewer
when present. The interactive report must surface mockup filenames and visual
findings without inventing image-derived data. If `reviews/viz-audit.md`
contains MUST-FIX issues, report them and wait for the user's approval before
rebuilding.

## General rules

- Keep every generated review in `reviews/` and never overwrite the source
  GDD or mockup assets.
- If one reviewer fails or produces an empty result, re-run only that reviewer.
- Do not merge rounds without an explicit user request.
- For image inspection, use Codex's image-viewing capability; do not infer
  visual details from filenames or uninspected files.
