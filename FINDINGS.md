# Findings — Revenue Intelligence Inbox

Three evidence-backed findings from the supplied transcripts, plus one deliberately rejected decision.

---

## Finding 1: The Attachment Upload Bug in M07 Is Not Resolved by the Workaround

**What I noticed:**
In M07 (Willowfield Trust / SpendNest discussion), the customer reported that file attachment fails via the "Recreate payment" route. A workaround was found during the session — using the Bill pay → invoice inbox → Review path — and the workaround does work. However, Team member A explicitly confirmed that the workaround does not resolve the underlying bug.

**Source passage:**
- M07 Turn 15 (source_id: 4f304fff-fd40-5cfc-a366-0a134c445c2a): Team member A: *"Yes. I will still raise the original behaviour with the team. The workaround does not mean the direct upload issue has been resolved."*
- M07 Turn 14: Customer A: *"Okay, we have an alternative for now. It is still extra steps compared with doing it directly when recreating the payment."*

**Where it affected the product:**
The analysis for M07 explicitly separates the workaround from the resolution. The routing is "Support / product issue" (not "Customer follow-up" alone), and the recommended action includes raising a separate product ticket for the direct upload failure. The evidence list in the M07 detail view shows both Turn 12 (workaround confirmed) and Turn 15 (bug still open) to make this distinction visible to the manager.

---

## Finding 2: M03 Has a Critical Prepaid vs. Credit Card Mismatch — and the Customer Said Not to Schedule a Follow-up Call

**What I noticed:**
In M03 (Asterloom Systems), Customer A expected a corporate credit card. SpendNest's domestic offering is prepaid. This was clearly surfaced mid-meeting. Additionally, Customer B (likely finance/CFO representative) explicitly asked not to schedule a follow-up call until they have had internal discussions and CFO sign-off.

**Source passages:**
- M03 Turn 6 (source_id: 398672e7-62dd-5afd-846d-0237d49792fb): Customer A: *"Wait, these are prepaid? I had thought about a corporate credit card. So we put money in before anyone spends, and there is no credit facility on these domestic cards?"*
- M03 Turn 36: Customer B: *"Send the details first. We need time with the CFO and will tell you if another call is needed. Let us make that decision internally before scheduling more."*
- M03 Turn 24: Team member A corrected pricing: *"INR 26,000 plus tax is the recurring annual subscription, and INR 13,000 is the one-time accounting integration setup."*

**Where it affected the product:**
The M03 analysis clearly flags the prepaid/credit mismatch as the primary blocker. The recommended action does NOT include scheduling a follow-up call. The routing is "Deal / commercial next steps" + "Customer follow-up" — the follow-up route is specifically labelled as "awaiting their signal, not proactively scheduled." The corrected pricing (INR 26,000 + INR 13,000 — not the combined INR 39,000 that appeared earlier) is preserved in the evidence section.

---

## Finding 3: C09 — Contact Requested Anonymity; No Qualified Opportunity Exists

**What I noticed:**
In C09 (cold call to Vedant), the contact said their company uses internally developed software and is not looking externally. He referred SpendNest to the business head Rishabh on a professional networking site, but explicitly asked that his own name NOT be used when making contact. He also did not know whether their internal system handles payments (as opposed to just approvals).

**Source passages:**
- C09 transcript (source_id: 4a1de681-f884-55e8-b1ad-de22d17cd959): Vedant: *"We have developed our own software internally, so at present we aren't looking for anything outside the organisation."*
- C09: Vedant: *"No, please don't use my name."* (in response to "Would it be all right if I mentioned your name?")

**Where it affected the product:**
The C09 analysis includes a prominent privacy flag that reads: "Contact (Vedant) explicitly requested their name NOT be used in any future outreach to their business head." The routing is "Sales research / qualification" — not "Deal" or "Partner." The recommended action says to approach Rishabh (if at all) based on general qualification, not with Vedant's name. No qualified opportunity is claimed.

---

## Deliberately Rejected Decision: Deduplicating by Person Name or Company

**The alternative I considered:**
Group or deduplicate records by the apparent company or person name (e.g., treating C01 and M01 as "Harborlight Advisory" records together, or collapsing background calls from the same rep into a single entry).

**Why I rejected it:**
The DATA_CONTRACT.md is explicit: identity is the pair (source_type, source_id). Neither company name nor rep name nor record_ref is a stable identity key. In this dataset, similar names appear across records with different source IDs. Deduplicating by name would risk hiding genuine distinct interactions and would break the revision-handling logic (which operates per source ID). The assignment also says "Never key records by company name, title, phone, record_ref or array position."

Additionally, meeting organizers are not confirmed salespersons or attendees. Joining call-rep names with meeting-organizer emails by guessing they refer to the same person would introduce unsupported inferences.

**The trade-off I accepted:**
The inbox shows all 44 records individually, which means background calls (B01–B20) appear as separate rows. A person-grouping view could be built on top, but it would be labelled as a derived grouping, not a deduplication. This keeps the data model honest: no source record is hidden or merged without evidence.

**Where this choice is visible in the product:**
- The dashboard "Activity by person" bar chart groups by rep_name (for calls) or organizer_email (for meetings), clearly labelled separately.
- The footnote states: "An organizer is not automatically a salesperson or confirmed attendee."
- The inbox table shows all 44 rows by default; filters narrow the view but never hide records permanently.
