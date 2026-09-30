# NPS Plan Review Assistant

This folder is a civil plan-review workspace for NPS Global engineers (NPS Maricopa, NPS Pinal, NPS Agua Fria). It was distilled from Tom Haws's (TGH) review-assist trial with Claude Code, Aug-Sep 2026. Everything below is a lesson that was earned by a correction or a confirmed success on a real job.

The person running this session may not be Tom. Learn their own conventions as you go and save them to memory; where they differ from Tom's, theirs win in their sessions.

## Folder layout

- `<ProjectName>\<YYYY-MM-DD>\*.pdf` — one folder per job, one dated subfolder per submittal round.
- `example\LDLPM\` — a finished two-round review for reference (see "Worked example" at the bottom). Do not edit it.
- `LDLPM-practice\` — the same job with Claude's round-2 markups removed, for a practice run.

## Trigger: "Review <ProjectName>"

That phrase alone is a complete instruction. Open `<ProjectName>\<most-recent-date-folder>\` and run the SOP below. Don't ask the user to re-explain the process.

## Review SOP

1. **One True Copy.** Annotate the PDF in the dated folder *in place*. Never make a `-CLAUDE.pdf` or any other duplicate; the user manages versions. Save via `doc.save(tmp)` in the scratchpad then `os.replace(tmp, target)` (a bare same-path save without `incremental=True` fails in PyMuPDF). Re-dump `page.annots()` at the start of every turn to pick up the user's replies/edits.
2. **Check the text layer first.** Try `page.get_text("dict")`. Architect-authored sheets often have real font text (exact sizes available directly). NPS/AutoCAD sheets are usually SHX vector outlines with **no text layer**: reading is visual, `search_for()` finds nothing.
3. **Render at high zoom.** The Read tool's built-in PDF view is too low-res for notes, legends and title blocks on 24x36 sheets. Use PyMuPDF: `page.get_pixmap(matrix=pymupdf.Matrix(4,4))` into the scratchpad, then crop regions to read.
4. **Whole-sheet first pass, wide not zoomed**, for relative properties: text size vs. the sheet's dominant size, color use, and on G&D plans **scan and summarize all contours (existing and proposed) before evaluating any drainage narrative**. Tight crops are for reading content; they destroy the side-by-side comparison that makes a size mismatch obvious.
5. **Hillside / G&D checklist** (see Domain lessons).
6. **Self-help before asking.** For any existing markup or open question, scan the *whole set* (cross-sheet details answer a lot), then the standards (below), before asking the user.
7. **Measure, don't eyeball** any measurable graphic property (text height, offset, alignment, distance). Methods under Techniques.
8. **Prior-draft back-check.** If an older dated folder exists, dump its annotations before assuming a first pass. Don't trust "Fixed"/"Revised" replies: render old and new at the same coordinates and compare. Render the *whole containing text block*, not just the icon spot; neighboring defects surface that way. Report both confirmed fixes and items that look closed but aren't (a drafter's "reply" that is really an unanswered question back to the engineer).
9. **Utility sheets: LF takeoff cross-check.** Sum the LF labels on plan per system and compare to the construction-note quantity column; count fitting bubbles too. This is how an undefined run gets *proved* rather than guessed.
10. **Legal descriptions: traverse them, don't just proofread.** Parse each course from the POC, compute coordinates, report misclosure/precision and area, plot to PNG with courses labeled (matplotlib). Compare area to the site area used in the drainage report.
11. **Hydraulic/hydrologic reports:** these usually *do* have a text layer, so `get_text()` + an old-vs-new text diff is the fastest first pass. Then recompute the model's internals from the figure labels (e.g. Hazen-Williams per link vs. the printed headloss). Summary tables sometimes print the value the model was *fitted to* instead of what it *produced*.
12. **Write findings into the PDF** (format below), not as a chat summary.
13. **Before/after any bulk annotation edit:** list, act, re-list, diff. Filter deletes on the most specific property (a unique tag in the content), never just author or subtype. Deleting the reviewer's own objects by accident is the worst failure here.
14. **End with a short chat summary** pointing to the PDF and highlighting only what needs a human judgment call.

## Markup format (the deliverable)

- **One standalone sticky per finding, placed adjacent to its referent** (`page.add_text_annot(point, text)`, `title="Claude"`). Not reply threads (drafters can't find them), not FreeText callouts (eat plan real estate). Placement accuracy matters: derive coordinates from the rendered crop, don't estimate.
- **Content format**, for readers who are often non-native English speakers:
  ```
  [F12 - DATA]
  Action item: <one short imperative>

  Detail: <plain short sentences>
  ```
  Tags: `F<n>` my own findings, `C<n>`/`C<n>a` replies to the reviewer's notes, `BC-*` back-check items, plus one `BACK-CHECK SUMMARY` sticky. No-defect notes still get `Action item: None.`
- **Correcting my own earlier finding:** edit that annotation in place (`set_info`, `update`), prefix `[F7 - CORRECTED, supersedes my original F7]`. Don't add a second contradicting note.
- **Non-default icons** (`icon="UpLeftArrow"` etc.): after `annot.update()`, run `doc.xref_set_key(annot.xref, 'AP', 'null')` or every extended icon renders as the same star.
- **Once the reviewer says a PDF is going out "as is," stop editing it.**

## Reading Tom's (TGH) markups

- Colors (`annot.colors["stroke"]`): **green** `[0,1,0]` = his default; **purple/lavender** = his round two (a *date* signal). **My note recolored green** = strong endorsement + routed to a specific person. Uncolored ≠ rejected: it may just not be that person's to act on. Report as still open.
- One sticky often **bundles several intents**: a real defect, a rhetorical self-check ("is this short for the same reason as X?"), a style nit, a process aside. Separate them; don't treat every note as a defect.
- **Proximity is not reply.** His note beside my finding may be an independent finding. If unsure what it refers to, say so.
- Verify literal wording against the render. "Remove space" was a *leading* space misaligning a note column, not a token typo.
- Prefer "here's an anomaly, please confirm why" over asserting a guessed root cause.

## Office standards

- **Haws Drafting Standards** (canonical): Google Doc, Drive id `1FzhEU2r3iEdV0wBVjZ9Ao4acDRvKEkxxTeUvfIcaqTw`. Ask Tom for access if it isn't shared with you. **NPS-Standards addenda:** `NPS-Resource\Business\NPS-Standards\*.odt` in the NPS Dropbox. Read `.odt` by unzipping `content.xml` in Python.
- **Color encodes plotted lineweight**, not aesthetics:

  | Color | Lineweight | Use |
  |---|---|---|
  | blue | 0.13 mm | existing / survey base (intentionally lightest; not a defect) |
  | 2 yellow | 0.25 mm | misc annotation |
  | 4 cyan | 0.35 mm | default proposed linework, general annotation |
  | 6 magenta | 0.50 mm | notes, main proposed linework, callouts |
  | 1 red | 0.70 mm | large text, property lines |
  | 3 green | 1.00 mm | titles and **water/sewer utilities** |

  PDF stroke widths map: 2.88pt=1.00mm, 2.04=0.70, 1.44=0.50, 0.96=0.35, 0.72=0.25.
- **Sheet layout:** plan view at the RIGHT, construction notes as close to it as possible (field readability). Legend, if any, far left. A blank left column is correct, not wasted space. For undefined abbreviations prefer a plain abbreviations list over a graphic legend.
- Vicinity maps: show only streets needed to legally drive to the site; in Maricopa County organize around the 1-mile section-line grid.
- Body text columns are single MTEXT objects, so a size anomaly confined to one column is usually one formatting slip, not N mistakes. If a defect looks baked into a reused title block/template, say it may recur and deserves a template-level fix.
- Technical reports: executive summary at the beginning **or** conclusion at the end.

## Do NOT flag (confirmed false positives)

- A leader shoulder running under/through its label text. Standard convention.
- Designing right up to a setback line (15.10' vs 15.00'). Check setback *compliance*, never *margin*; the owner gets the full envelope.
- Crowding inside a fixed title-block/seal cell, unless actually unreadable.
- Blue existing linework plotting below 0.25 mm.
- Non-round offsets (6.03'). OFFSET from real alignments is correct practice; ORTHO is the real footgun.
- Before calling a symbol undefined, check the adjacent construction-note box number. It usually defines it.

## Domain lessons

**Hillside / G&D (especially Maricopa County):**
- Read cut slopes from the **plan view** contours, not only sections. Distinguish **diffuse flow onto a cut face** (rill erosion; the harder problem) from **concentrated flow at a point** (a design opportunity: Rational Method Q100 + rock chute per Robinson/FHWA; calculators at hawsedc.com/engcalcs). Suggest checking Q100 depth at concentration points. A report that explicitly separates the two cases is doing it right; say so.
- MC hard requirements (not inferable from judgment): **hillside setback** no-disturb zone (distinct from "erosion setback"; house, walls and cut slopes must stay outside); **cross sections every 25 ft along the house**; a plan-view **"CRITICAL POINT FOR ROOF HEIGHT"** leader (ROOF= / FF2= / FF1= / NG= / HEIGHT=, highest to lowest).
- Check **every platted easement** against the building and wall footprint, not just note its existence.
- Confirm which jurisdiction governs (MC vs City of Phoenix, etc.) before applying a numeric threshold. If unverified, say so rather than asserting compliance.
- Retaining walls: HFG/LFG belong in plan view. A table-only presentation is a real finding (it invites table-vs-plan mismatches), not a style preference. Always re-sum any table's TOTAL row.
- On Phoenix Hillside questions outside routine practice, suggest involving **Mary** or **Plamen (PKD)**, the office's Phoenix Hillside mentors.

**Water:**
- Don't stack peak fixture demand on a fire-flow case; they don't coincide. Service/meter adequacy uses everyday/peak-hour main pressure; fire residual governs the hydrant.
- Before calling a pipe size gratuitous because "the upstream main dominates," vary that size and tabulate. When margins are thin every loss term matters; frame it as a diminishing-returns curve.

**Drainage evidence:** before asserting where flow enters a site, the legend-distinct wash line, any labeled CFS arrow, and the specially-designed wall segment should all agree. If your traced line disagrees, distrust the traced line.

**Re-derive before defending.** When a new question touches the numbers behind an earlier finding, recompute the whole chain; the finding may be wrong. Correct it in place.

## Techniques

- **Python:** use `python` (on Windows, run via PowerShell; `python3` may not exist). Needs `pip install pymupdf pypdf numpy matplotlib`.
- **Measuring text height on a no-text-layer sheet:** render with `annots=False` (sticky icons corrupt ink measurements), grayscale numpy array, threshold `<128`, drop columns dark almost everywhere (rule lines), find contiguous dark row-bands per text line, convert px→pt via `/zoom`. First find the sheet's dominant size, then scan for outliers.
- **Measuring a real distance off a scaled sheet:** calibrate from two vector-precise points with a known printed dimension (e.g. dimension witness ticks from `page.get_drawings()`), compute pt-per-foot, then compute algebraically or draw a ruler overlay. Never infer scale from eyeballed crop pixels.

## NPS context

NPS Global (npsge.org): no owners, part-time freelancers, small self-managed teams, colleagues in many countries. The project engineer is usually also the job's internal PM. Many readers of these markups are non-native English speakers; hence plain, short, action-first comments.

## Worked example: LDLPM (Little Dealer Little Prices of Mesa, RV maintenance building, 2038 N Country Club Dr, Mesa AZ)

- **Round 1** `example\LDLPM\2026-08-09\LDLPM-C203-UTILITY-PLAN.pdf`: single utility sheet in an architect's title block (partial real text layer). Tom's green markups + Claude's F1-F17. The LF takeoff (sewer 817 LF, water 282 LF) is what proved the 78 LF bubble-53 run was a separate undefined item.
- **Round 2** `example\LDLPM\2026-09-29\LDLPM-CIVIL.pdf`: 6-sheet "draft 1 permit application" set (C101-C204), 46 stickies: F1-F35 new, BC-* back-checks, one BACK-CHECK SUMMARY on C203. Tom's verdict: *"exactly what I want."* Highlights: wall top above FF; bleed-off drawn gravity but specified as pumped; water crossing labels putting pipe above grade; legal description with 7 duplicated courses (422 ft misclosure as written, 0.004 ft with the duplicate removed, area = the drainage report's 5.78 ac).
- **Practice:** `LDLPM-practice\` is round 2 with Claude's stickies removed (Tom's remain). "Review LDLPM-practice" should reproduce the example's shape; compare against `example\`.
