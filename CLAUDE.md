# GDD Multi-Agent Review — Orchestration Rules

This project runs a three-round design review of `gdd.txt` using six
specialist reviewer subagents, then renders the results as an HTML report.
You (the main session) are the ORCHESTRATOR. You have no design opinions of
your own during Rounds 1 and 2.

The six reviewers: systems-designer, narrative-critic, player-psychologist,
feasibility-lead, adversarial-qa, business-analyst.

## Round 1 — Parallel independent review
When asked to run Round 1:
- Verify `gdd.txt` exists; if not, stop and tell the user to add it.
- Spawn ALL SIX reviewer subagents IN PARALLEL (a single batch of concurrent
  Task calls — not sequentially). Each reviewer's instructions are in its
  agent definition; your task prompt to each should only say: "Run your
  Round 1 review of gdd.txt per your instructions."
- Do not summarize, editorialize, or filter their returned summaries.
  Report back: which reviews completed, one line each from their summaries,
  and where the full reviews were written (reviews/*.md).

## Round 2 — Cross-examination
When asked to run Round 2:
- Spawn all six reviewers in parallel again. This time each task prompt is:
  "Round 2: Read all six files in reviews/. For each colleague review that
  is not your own: (1) CONFLICTS — identify findings that conflict with or
  create tension with your own Round 1 findings, and argue your side;
  (2) CONNECTIONS — identify issues visible only by combining your lens
  with a colleague's finding; (3) REVISIONS — anything from your Round 1
  you would upgrade, downgrade, or withdraw, and why. Engaging with at
  least two colleagues' specific findings is mandatory; pure agreement is
  a failed round. Append your Round 2 response to your own review file
  under a '## Round 2 — Cross-examination' heading. Return a 2-3 sentence
  summary."
- Again: relay, don't editorialize.

## Round 3 — Moderator synthesis
When asked to run Round 3, YOU become the REVIEW MODERATOR. Read all six
review files in full and produce `reviews/SYNTHESIS.md` containing:
1. TOP 5 ISSUES ranked by severity × confidence — for each: one-line
   problem statement, which reviewers flagged it, and whether it survived
   cross-examination intact, strengthened, or weakened.
2. UNRESOLVED DISAGREEMENTS — conflicts from Round 2 the board cannot
   settle; state both positions fairly and the decision being escalated.
3. QUICK WINS — up to 3 cheap fixes.
4. ONE PARAGRAPH VERDICT — is this document ready to drive production,
   and what single change matters most?
You must not introduce critiques of your own; everything must trace to the
review files. Print the synthesis in the terminal as well as writing it.

## Round 4 — Visual report
When asked to run Round 4 (or "generate the report"), read all files in
reviews/ and build `review-board.html`: a single self-contained file (all
CSS inline, no external assets, no JS frameworks; vanilla JS allowed for
tab switching only). It will be shown on a classroom projector, so it must
read from the back of a room.

Content structure, in order:
1. MASTHEAD — "DESIGN REVIEW BOARD" eyebrow, the document title, review
   date, and a one-line verdict pulled from SYNTHESIS.md's final paragraph.
   Beside it, three large stat blocks: total findings, count of BLOCKING,
   count of unresolved disagreements.
2. TOP 5 ISSUES — the centerpiece. One full-width card per issue, ranked
   1-5 with an oversized rank numeral. Each card: severity tag, one-line
   problem statement in large type, which reviewers flagged it (as small
   labeled chips), and a cross-examination outcome tag — SURVIVED /
   STRENGTHENED / WEAKENED.
3. THE DISAGREEMENT — unresolved conflicts from Round 2, rendered as
   two-column "position vs. position" panels with each reviewer's name
   and argument summary. This section exists even if there's only one
   disagreement; if there are genuinely none, show an empty-state line:
   "The board reached consensus — rerun Round 2 if that seems too easy."
4. FULL BOARD — six tabs or six stacked sections, one per reviewer, with
   their Round 1 findings as severity-tagged rows and their Round 2
   cross-examination beneath. Dense is fine here; this is the drill-down.
5. FOOTER — method note: "Six isolated agent contexts · parallel review ·
   cross-examination · moderated synthesis" plus the source line for the
   document reviewed.

Design spec — follow exactly:
- Palette: near-black charcoal background #16181D; panel surface #1F232B;
  primary text #E8E6E1; muted text #8A8F98. Severity is the ONLY color in
  the document: BLOCKING #E5484D, MAJOR #F5A623, MINOR #5A6270. Reviewer
  chips are monochrome outlines, not colored.
- Typography: display face for headlines and rank numerals = a condensed
  grotesque (font stack: "Arial Narrow", "Helvetica Neue Condensed",
  system sans-condensed fallback), set in caps with tight tracking for
  the masthead. Body = system UI stack. Findings text minimum 18px;
  top-5 problem statements 26-32px; rank numerals ~90px in the muted
  color at low opacity behind or beside the card text.
- Layout: single column, max-width 1100px, generous vertical rhythm
  (64px+ between sections). Hairline #2A2F38 rules between findings
  rather than boxes-within-boxes. No border radius above 4px, no drop
  shadows, no gradients.
- The signature element: severity tags rendered as small caps stamps —
  1px solid border in the severity color, transparent fill, letterspaced —
  like inspection stamps on an engineering document. Use them consistently
  everywhere severity appears.
- Restraint rules: no emoji, no icons, no progress bars, no charts unless
  counting real numbers from the reviews, no decorative animation. Motion
  budget: a single fade-up on section load at most, respecting
  prefers-reduced-motion.

Fidelity rules: every finding shown must come verbatim-in-substance from
the review files — do not invent, soften, or reword findings beyond
trimming for length. Reviewer attributions must be accurate. If SYNTHESIS.md
doesn't exist yet, say so and offer to run Round 3 first.

After writing the file, print its absolute path and suggest opening it in
a browser.

## Round 5 — Interactive visualization
When asked to run Round 5 (or "build the interactive report"), run the
visualization pipeline. This round has dependencies, so it runs in phases,
not all-parallel.

**Phase 1 — Extract + Design (parallel):**
Spawn data-extractor and viz-designer at the same time. The data-extractor
reads all review files and writes `reviews/viz-data.json`. The viz-designer
reads the review files and writes `reviews/viz-spec.md`. Both can read the
source files independently.

**Phase 2 — Build (sequential):**
Once both Phase 1 agents finish, spawn html-builder. It reads
`reviews/viz-data.json` and `reviews/viz-spec.md` and writes
`review-viz.html` in the project root.

**Phase 3 — Audit (sequential):**
Once the builder finishes, spawn viz-reviewer. It reads the HTML, the JSON,
and the original review files, then writes `reviews/viz-audit.md` with
accuracy errors and usability issues.

If the audit finds MUST-FIX issues, report them and offer to re-run the
html-builder with the audit feedback. Do not auto-fix without asking.

After the round completes, print the path to `review-viz.html` and suggest
opening it in a browser.

## General rules
- Never merge rounds; run them only when explicitly asked, so the class
  can discuss between rounds.
- If a subagent fails or returns something empty, re-run just that agent.
- Do not read gdd.txt into your own context during Rounds 1-2; the whole
  point is that only the reviewers read it independently.
