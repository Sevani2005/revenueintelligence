# Five-Slide Presentation — Revenue Intelligence Inbox

---

## Slide 1: The Manager Problem and Main Findings

**The Problem:**
A sales manager receives calls, meeting records, and transcripts from multiple systems. Records arrive out of order, transcripts arrive late, and stored labels do not always reflect what was actually said. The manager cannot easily see: what needs action today, what is a real opportunity vs. a polite no, and whether a support issue has been resolved or just worked around.

**This dataset (21 Aug 2026, Asia/Kolkata):**
- 44 unique source records: 30 calls, 14 meetings
- 18 usable transcripts post-update (17 initially); 26 records have no usable text
- All activity falls on one local date, so date-only filtering is insufficient

**Three main findings from the transcripts:**
1. **M07 (Willowfield Trust):** The workaround for the attachment upload bug is NOT a resolution. Three separate product issues still require investigation tickets.
2. **M03 (Asterloom Systems):** The primary blocker is a prepaid vs. credit card mismatch. CFO approval is needed. The customer explicitly said NOT to schedule a follow-up call — wait for their signal.
3. **C09:** Contact requested anonymity. No qualified opportunity exists. The privacy restriction must be respected in all future outreach.

---

## Slide 2: Interface Choices and Deliberate Omissions

**What I built:**
- Dashboard with computed metrics (from imported data, not hardcoded)
- Searchable, filterable inbox showing all 44 records
- Detail view with full transcript, pre-generated analysis (what/why/evidence/uncertainty/action), and editable routing
- Import view with idempotent re-import and demo replay sequence
- Export view generating downloadable JSON + CSV manifests

**Deliberate omissions:**
- **No pipeline value totals:** The data does not supply verified deal values. Showing a "pipeline total" would be fabricated.
- **No cross-person performance ranking:** This is a selected sample, not a complete team day. Rankings would misrepresent the data.
- **No AI live inference at runtime:** Analysis is pre-generated from the supplied transcripts. This is declared clearly in the UI. Runtime computes metrics, routing counts, and filter results.
- **No automatic follow-up scheduling:** The app does not auto-book calls. C10 refused a follow-up; C03 asked for email + Mon/Tue. The distinction matters.

**Why these omissions:**
Making up numbers or rankings would be dishonest. The assignment says "Do not invent pipeline value, deal outcomes or account links that the data does not establish."

---

## Slide 3: Routing Design and a Difficult Case

**Routing categories:**
1. Partner opportunity
2. Deal / commercial next steps
3. Customer follow-up
4. Sales research / qualification
5. Support / product issue
6. Internal training / process
7. Human review / insufficient evidence

**Design decisions:**
- Records can have multiple routes (e.g., M01 = Partner + Deal; M04 = Follow-up + Support)
- Route counts can exceed unique record counts — this is labelled clearly
- Every route shows its reason, derived from transcript evidence
- Managers can override any route, and the manual flag persists across imports

**Difficult case — M07 (Willowfield Trust):**
- M07 arrived in the update batch with no transcript initially, then gained one at revision 2
- The analysis correctly routes it to "Support / product issue" (not customer follow-up alone)
- Four distinct bugs are listed; the workaround is distinguished from the fix
- The manual-override flag survives the update import
- This is the primary test case for the revision/update/override replay sequence

---

## Slide 4: Verification — Key Number and Key Conclusion

**Verifying the record count (44 unique records):**
- Imported 01_initial.json: 44 records parsed, keyed by (source_type, source_id)
- Re-imported same file: all 44 already exist at rev 1 → all skipped (0 new records)
- Imported 02_update.json: 1 record (M07) at rev 2 → updated (not duplicated)
- Re-imported initial again: M07 already at rev 2, initial is rev 1 → skipped
- Final count: 44. Verified by the import log.

**Verifying usable transcript count (18 post-update):**
- Calls with usable transcript: 10 (C01–C10: all have non-null, non-empty transcript field)
- Meetings with usable transcript initially: 8 (M01, M02, M03, M04, M06, M09, M10 + one more)
  - M07 had no usable transcript in rev 1 (transcript_payload.turns = [])
  - M07 gains turns in rev 2 → 18th usable transcript
- Formula: usable count / total records = 18 / 44 = 40.9% coverage

**Verifying the M03 pricing conclusion:**
- Turn 22 states INR 39,000 as a combined figure
- Turn 24 corrects it: INR 26,000 (recurring subscription) + INR 13,000 (one-time setup)
- The app preserves the corrected breakdown, not the initial combined figure

---

## Slide 5: Trade-offs, AI Usage, Limitations, and Improvements

**AI usage:**
- Pre-generated analysis for all 18 usable transcripts was written with AI assistance
- The application computes metrics, filtering, routing counts, and export manifests at runtime without AI
- All transcript-derived claims were verified against the actual text in CONVERSATIONS.md
- Analysis is labelled "Pre-generated" in the UI

**Trade-offs:**
- **Single HTML file vs. multi-file:** Chose single file for reviewers to open without a server. Trade-off: large file size.
- **Pre-generated analysis vs. live inference:** Pre-generated is transparent, reproducible, and does not require API keys. Trade-off: does not auto-analyse newly imported records without human assistance.
- **localStorage vs. a database:** Sufficient for the exercise; survives browser restart. Trade-off: storage limits if importing large datasets.

**Known limitations:**
- If a reviewer has browser localStorage cleared, state resets (data must be re-imported)
- The app does not auto-generate analysis for records added beyond the supplied 44
- Meeting turn timecodes are display labels, not unique IDs — not used as evidence citations

**What I would improve next:**
1. Add a live analysis option (user-initiated, not automatic) when a transcript changes
2. Add a person/company grouping view as a derived layer (not deduplication)
3. Add a coaching view for call reps based on transcripts (C01, C03, C07 are good training material)
4. Add search-within-transcript highlighting

**Time spent:** Approximately 7–8 hours
