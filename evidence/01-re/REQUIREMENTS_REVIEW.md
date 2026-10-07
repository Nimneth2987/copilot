# Requirements Review

Status: Draft — awaiting human review. No client approval is recorded. This document is the Phase 1 requirements artefact, not raw conversation evidence and not an implementation or test result.

## Run and revision metadata

- Protocol: `minicart-cli-v1`
- Run: `2026-10-08-fresh`
- Member/platform: Nimneth / GitHub Copilot in VS Code
- Run root: `experiments/minicart/2026-10-08-fresh/copilot`
- Revision: Initial draft (`REQUIREMENTS_REVIEW.md`); no earlier review exists to archive.
- Source brief: [CLIENT_BRIEF.md](../../inputs/CLIENT_BRIEF.md), agreed 7 October 2026.
- Common acceptance checklist: [COMMON_ACCEPTANCE.md](../../inputs/COMMON_ACCEPTANCE.md).
- Run context: [RUN_CONTEXT.md](../RUN_CONTEXT.md). It records file read/write and terminal access as available, Python 3.12 installed globally, Windows 11, and browsing/source access unavailable.
- Initial prompt source: [01_RE_INITIAL.txt](../../inputs/prompts/01_RE_INITIAL.txt). The operator's raw session prompt and all visible responses have not yet been captured in `evidence/prompts/` and `evidence/responses/`; those evidence records remain pending and must not be replaced by this review.
- Actions in this phase: file reads and this draft were agent-executed. No command, application code, environment setup, or application test was executed. No human review or approval has been received.

## Stakeholders and problem

- Client/product owner: defines the transaction workflow, fixed catalogue and business rules, and resolves the explicitly open choices.
- Customer/operator using the CLI: views products, edits one cart, applies an eligible promotion, checks out, or exits.
- Run operator: captures exact prompts and visible responses, maintains phase evidence, and records human review/approval before design.
- Evaluator/reviewer: checks traceability to B01-B16 and C01-C24, evidence quality, and whether phase boundaries were respected.
- Future maintainer: receives the source, exact setup/run/test instructions, and deployment handover documentation.

Problem statement: provide a small local command-line shopping cart for one customer transaction in each process run. It must accurately enforce the fixed catalogue, quantity and promotion rules, calculate AUD totals including GST, and exit predictably without persistent customer/cart data or external services.

## Scope and exclusions

In scope for the product: catalogue display; cart add, view, update and remove; validation and useful errors; SAVE10 handling; exact financial calculations; checkout receipt; clean exits; reproducible setup and declared test dependency; meaningful automated tests and acceptance evidence; and later documentation-only deployment planning.

In scope for this phase only: eliciting, analysing, specifying, and validating the requirements; documenting traceability, draft acceptance criteria, assumptions, open questions, and future proposals.

Out of scope for this phase: architecture/module design, environment creation, implementation, and application test execution. Out of scope for the product/run: persistence, cross-run stock reservation, accounts, database, network service, payment collection, actual hosting/publication/store submission, and any GUI or other extension to the CLI baseline.

## Functional requirements with IDs and B01-B16 mapping

| ID | Requirement | Brief mapping |
|---|---|---|
| FR-01 | Display each catalogue product's code, name, AUD unit price before GST, and per-run stock limit using the fixed four-row catalogue. | B01 |
| FR-02 | Add a product by code and positive whole-number quantity. Repeated adds accumulate, and the resulting per-product quantity must not exceed that product's stock limit. | B02 |
| FR-03 | Display each cart line's code/name, quantity, unit price, line total, and pre-GST subtotal; when empty, display a clear empty-cart message. | B03, B07 |
| FR-04 | Update an existing cart item's quantity as a replacement quantity, not an increment; remove an existing item explicitly. Reject update/remove for an absent item. | B04 |
| FR-05 | Trim surrounding whitespace from product/promotion codes and quantity text; compare product and promotion codes case-insensitively. Accept quantities only when the trimmed text consists solely of ASCII digits and has a positive value. Accept leading zeros; reject zero, signs, negatives, fractions, exponent notation, blank, nonnumeric, and non-ASCII numerals. | B05 |
| FR-06 | Reject unknown products, invalid quantities, over-stock changes, invalid promotions, and invalid menu choices with useful feedback. Rejected operations leave the whole cart and active promotion unchanged. Invalid menu choices return to the menu without a crash. | B06, B08 |
| FR-07 | Calculate subtotal as the sum of quantity multiplied by unit price, before GST. | B07 |
| FR-08 | Apply SAVE10 only when explicitly requested and subtotal is at least AUD 50.00. Reject other codes and below-threshold application. At most one promotion is active; applying SAVE10 again does not stack or alter the result. | B08 |
| FR-09 | On cart edits, recalculate active SAVE10 while subtotal is at least AUD 50.00. If subtotal falls below the threshold, deactivate it and notify the customer. A later subtotal increase does not reactivate it; explicit reapplication is required. | B09 |
| FR-10 | Use exact monetary arithmetic. Round the 10% discount to cents using nearest-cent half-up rounding, subtract it from subtotal, round 10% GST on the discounted amount the same way, and add GST to obtain the final total. Display every money amount to two decimal places. | B10 |
| FR-11 | Permit checkout only for a nonempty cart. Display subtotal, discount, amount after discount, GST, and final total; then exit. Do not collect payment. On empty checkout, reject and continue shopping. Receipt itemisation is optional; cart itemisation remains compulsory under FR-03. | B11 |
| FR-12 | Start every launch with an empty cart and the same fixed per-product quantity limits; do not persist data or reserve stock across runs. | B12 |
| FR-13 | Offer a normal exit. Handle EOF and Ctrl+C cleanly, without traceback or accidental checkout. | B13 |
| FR-14 | Supply `app/app.py` as CLI entry point, a README with exact setup/run/test instructions, and a declared test dependency file. Keep runtime dependencies to the Python standard library and automated tests on pytest 8 or later. Put project `.venv` under `app/`; reuse a compatible installed Python when available and do not pre-create the application environment before coding. Record exact patch/package versions. | B14 and general brief constraints |
| FR-15 | Supply meaningful automated tests for normal, invalid, and boundary cases, with at least eight test functions; provide evidence against the common acceptance checklist. Report test-function and parameterised-case counts separately; neither count is a quality score. | B15 |
| FR-16 | Produce deployment documentation/research for local CLI handover and outline future web, Android, and iOS release workflows with source links. Do not publish, host, submit to stores, or add payments. | B16 |

## Quality requirements and constraints

| ID | Requirement/constraint | Traceability |
|---|---|---|
| QR-01 | Financial results must be exact and reproducible, including half-up cent rounding and two-decimal output. | B10; examples A/B; C16-C18 |
| QR-02 | Invalid/rejected actions must not corrupt cart or active-promotion state; error handling must preserve continued use where specified. | B06, B08, B11, B13; C05-C12, C19, C21-C22 |
| QR-03 | CLI interaction must be understandable through clear empty-cart, validation, promotion-deactivation, and exit/receipt feedback. Exact wording and menu order remain open. | B03, B06, B09, B13 |
| QR-04 | Runtime uses Python 3.12 and its standard library; tests use pytest 8 or later. Record actual interpreter patch and package versions rather than assuming them. | Common brief, B14-B15 |
| QR-05 | Setup, launch, and test instructions must be exact and verifiable for the recorded target. A reused original environment is not evidence of a clean-machine setup. | B14; C23 |
| QR-06 | Acceptance evidence must distinguish agent-executed, human-executed, and not-executed actions, include commands/output and exit codes where applicable, and state gaps, retries, and uncertainty honestly. | B15; common acceptance evidence protocol |
| QR-07 | Phase separation and scope control: keep this phase to requirements; keep the CLI baseline fixed; preserve failed/prior evidence; record GUI/extensions as future proposals only. | Common brief and phase prompt |

## Business rules and worked financial examples

- Currency is AUD. Catalogue prices are before GST. The fixed catalogue is P001 Notebook, AUD 5.50, stock 20; P002 Pen, AUD 2.00, stock 50; P003 Backpack, AUD 45.00, stock 5; P004 Water Bottle, AUD 15.00, stock 10.
- Each run is one customer transaction with a fresh cart. Stock limits apply to total quantity of each product in that run; they do not represent shared inventory.
- SAVE10 is a single 10% discount, explicitly applied at subtotal >= AUD 50.00. It cannot stack. An eligible active promotion recalculates after edits, is removed with notice below the threshold, and must be explicitly reapplied after eligibility returns.
- Calculation sequence is subtotal; rounded discount; amount after discount; rounded GST on that amount; final total. Rounding is nearest cent, half-up, at both discount and GST stages.
- Example A: P001 x2 plus P004 x3 gives subtotal 56.00; SAVE10 discount 5.60; after discount 50.40; GST 5.04; final 55.44.
- Example B: P001 x1 plus P003 x1 gives subtotal 50.50; SAVE10 discount 5.05; after discount 45.45; GST 4.55 (the half cent rounds up); final 50.00.
- Additional no-promotion reference from the acceptance checklist: P001 x1 gives 5.50 subtotal, 0.00 discount, 5.50 after discount, 0.55 GST, and 6.05 final.

## Assumptions and open questions

Assumptions for this draft, all consistent with the brief: the fixed catalogue and its quantities/prices are the complete initial catalogue; one process launch is one transaction; stock is a per-run maximum rather than shared inventory; and checkout ends the process after showing the required five totals. These are not new client decisions.

Open questions for the client/operator (fixed B01-B16 rules are not being reopened):

1. Which menu wording/order would you prefer? The brief explicitly leaves both open.
2. Should the optional checkout receipt include item lines, or only the five required totals?
3. What concise wording should normal exit, EOF, and Ctrl+C use? The clean-exit behavior itself is fixed; only wording is open.
4. Do you prefer concise or more explanatory validation messages, provided each remains useful and rejected operations preserve state?
5. For later comparison, should future feature proposals remain limited to a basic GUI, or may other non-baseline extensions be recorded as separate proposals? Any such capability remains unimplemented in this run.

No question is raised about the fixed catalogue, quantity syntax, stock enforcement, SAVE10 threshold/rounding, GST, no-payment checkout, per-run reset, required files, minimum tests, or documentation-only deployment because the brief already fixes those decisions.

## Client answers and decisions with decision IDs

No client answers or decisions have been received in this initial draft. Do not infer approval from this document or from the shared protocol.

| Decision ID | Topic | Client answer/decision | Status |
|---|---|---|---|
| D-01 | Menu wording/order | Awaiting client response to question 1. | Open |
| D-02 | Optional receipt itemisation | Awaiting client response to question 2. | Open |
| D-03 | Exit/EOF/Ctrl+C wording | Awaiting client response to question 3. | Open |
| D-04 | Validation-message detail | Awaiting client response to question 4. | Open |
| D-05 | Future proposal breadth | Awaiting client response to question 5. | Open |

## Acceptance criteria and traceability

These criteria are proposed for later verification, not evidence that application behavior has been run. C01-C24 refer to the frozen [common acceptance checklist](../../inputs/COMMON_ACCEPTANCE.md). Execute its fresh-process/manual requirements in the later testing phase and preserve real outputs there.

| Requirement IDs | Draft testable acceptance criteria | Checklist mapping |
|---|---|---|
| FR-01 | On a fresh launch, catalogue output contains P001 Notebook 5.50/20, P002 Pen 2.00/50, P003 Backpack 45.00/5, and P004 Water Bottle 15.00/10, with fields identified. | C01 |
| FR-02, FR-03, FR-07 | Adding P001 x2 and P004 x3 then viewing cart shows both correct item lines and 56.00 subtotal. Adding P001 x12 then x8 gives 20; another x1 is rejected and quantity remains 20. | C02-C03 |
| FR-04 | Adding P001 x2 then updating to 5 yields quantity 5, line total/subtotal 27.50 (not 7 units). Removing it empties the cart; subsequent update and remove are rejected without mutation. | C04-C05 |
| FR-05 | Input ` p001 ` and ` 002 ` adds quantity 2; a trimmed mixed-case ` save10 ` is recognized when eligible. Each of `0`, `-1`, `+1`, `1.5`, `1e2`, blank, `abc`, and `١` is rejected; cart and promotion remain unchanged and later valid shopping works. | C06-C07 |
| FR-06 | P999 and an invalid menu choice produce useful feedback; neither corrupts cart state nor crashes, and the menu remains usable. For an invalid promotion before/after a valid active SAVE10, cart and promotion are preserved. | C08, C12 |
| FR-02, FR-04, FR-06 | P003 x5 succeeds. Updating to 6 and independently adding 6 from a fresh cart are rejected, leaving the corresponding cart unchanged. | C09 |
| FR-08 | At subtotal 48.00, SAVE10 is rejected and totals remain 0.00 discount, 4.80 GST, 52.80 final. At 50.00, applying SAVE10 once or repeatedly yields exactly 5.00 discount, 45.00 after discount, 4.50 GST, and 49.50 final. | C10-C11 |
| FR-09 | P004 x4 gives 60.00 subtotal; after applying SAVE10, updating to x5 recalculates to 7.50 discount, 67.50 after discount, 6.75 GST, and 74.25 final. For example A, removing P001 lowers subtotal to 45.00 and deactivates/notifies; adding P001 x2 returns to 56.00 without reactivation (0.00 discount, 5.60 GST, 61.60 final), until SAVE10 is explicitly reapplied (5.60 discount, 55.44 final). | C13, C14, C15 |
| FR-10, FR-11 | Checkout of example A displays 56.00 / 5.60 / 50.40 / 5.04 / 55.44 and exits. Example B displays 50.50 / 5.05 / 45.45 / 4.55 / 50.00. P001 x1 without promotion displays 5.50 / 0.00 / 5.50 / 0.55 / 6.05. All amounts use two decimal places; no payment is collected. Empty checkout is rejected and shopping continues. | C16, C17, C18, C19 |
| FR-12 | After an item-containing run ends, a new process starts with an empty cart and original stock limits. | C20 |
| FR-13 | Normal exit works. Real EOF at menu and quantity prompt exits without traceback/receipt; physical Ctrl+C at menu and during an action prompt exits without traceback/receipt. Record actual exit codes and techniques; a mocked exception alone is partial evidence. | C21-C22 |
| FR-14, QR-04, QR-05 | In a disposable environment, README setup/dependency-install/launch commands work on the recorded target; `app/app.py`, README, and declared test dependency file exist. Record exact Python/package versions and do not claim clean-machine proof from the original `.venv`. | C23 |
| FR-15, QR-06 | Later pytest evidence includes full initial/final output and exit codes, pass/fail/skip counts, at least eight meaningful test functions, separate parameterised-case counts, and explicit mapping/gaps for C01-C24. | C24 |
| FR-16 | Later deployment document covers local CLI handover and outlines web, Android, and iOS workflows with source links; it records no actual publication, hosting setup, store submission, or payment implementation. | B16 deployment checklist to be verified in the deployment phase |

## Future feature proposals

| Proposal ID | Proposal | Rationale and boundary |
|---|---|---|
| FP-01 | Consider a GUI front end as a separate future increment. | May improve discoverability for users who prefer visual interaction. It is not part of this CLI baseline, is not approved scope, and must not be implemented in this run. Any future proposal must preserve the fixed business rules or be separately reviewed. |

No other extension is proposed in this draft. Web/mobile release workflow documentation required by B16 is deployment planning, not a commitment to build those clients.

## Validation findings and unresolved issues

- The provided brief and acceptance checklist are mutually consistent on the core cart, promotion, financial, lifecycle, and phase constraints; no contradiction requiring a fixed decision to be reopened was identified.
- The template headings are retained. Every B01-B16 requirement is represented in FR-01 through FR-16; quality constraints are separately identified. The acceptance matrix points to C01-C24, with B16 tracked for its later deployment-document checklist.
- Genuine low-impact presentation choices remain open: menu order/wording, optional receipt itemisation, exit wording, and degree of validation-message detail. They do not block specifying the fixed behavior.
- Clarify the distinction between a real physical Ctrl+C check and mocked exception coverage during testing; the acceptance checklist already treats them differently.
- Raw prompt and response evidence has not yet been saved to `evidence/prompts/` and `evidence/responses/`. Operator capture is required before treating the evidence trail as complete.
- No application behavior was executed or validated in this phase. Acceptance criteria remain proposed and unverified. No application tests were run, intentionally.
- No GUI or other extension was implemented. No architecture, modules, environment, or application code were designed or created.

## Revision history and operator review status

| Revision | Change | Review status |
|---|---|---|
| Initial | First requirements draft created from the fixed brief, common checklist, run context, and supplied template. | Draft — awaiting human review; not approved. |

Operator/client review record: pending. Record reviewer, date, decision, requested changes, and any explicit approval here only after the human supplies them. Design must not begin until an operator approval record exists. On any subsequent RE revision, first archive the current document as the next `REQUIREMENTS_REVIEW_vNN.md`; preserve earlier drafts and evidence.

Operator review: Approved by Nimneth on 2026-10-08 at 00:21 AEDT.
