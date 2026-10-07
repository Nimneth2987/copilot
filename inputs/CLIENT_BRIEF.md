# MiniCart common client brief
Protocol: minicart-cli-v1 | Run: 2026-10-08-fresh | Agreed 7 October 2026

Build MiniCart, a small Python 3.12 command-line shopping cart for one customer transaction per run. Start with no previous application code. Complete requirements engineering, design, coding, testing and deployment planning as separate reviewed phases. Runtime uses the Python standard library; automated tests use pytest 8 or later. Exact patch/package versions must be recorded, not assumed to match other machines. Architecture and internal implementation are the agent's choices.

## Fixed catalogue
Prices are AUD before GST. Initial stock is a fixed per-product quantity limit for each run.
| Code | Product | Unit price | Stock limit |
|---|---|---|---|
| P001 | Notebook | 5.50 | 20 |
| P002 | Pen | 2.00 | 50 |
| P003 | Backpack | 45.00 | 5 |
| P004 | Water Bottle | 15.00 | 10 |

## Required behaviour
B01. Show the catalogue with code, name, unit price and stock limit.
B02. Add products by code and positive whole-number quantity. Repeated additions accumulate. The total quantity per product cannot exceed its stock limit.
B03. Show cart items with code/name, quantity, unit price, line total and subtotal. Show a clear empty-cart message.
B04. Update an existing item to a replacement positive quantity within stock. Remove an existing item explicitly. Updating/removing an item not in the cart is rejected.
B05. Trim surrounding whitespace on codes and quantities. Product and promotion codes are case-insensitive. Quantities must contain only ASCII digits after trimming and represent a positive integer; leading zeros are allowed. Reject zero, negatives, signs, fractions, scientific notation, blank and nonnumeric input.
B06. Reject unknown products, invalid quantities and over-stock operations with a useful message. Rejected operations preserve the entire cart and active promotion. An invalid menu choice returns to the menu without crashing.
B07. Subtotal is the sum of quantity times unit price before GST.
B08. SAVE10 gives 10% off the subtotal when subtotal is at least 50.00. It must be explicitly applied. Reject other promotion codes or applying below the threshold. Only one promotion is active; repeating SAVE10 must not stack discounts or change the financial result.
B09. After cart edits, an active SAVE10 is recalculated while subtotal remains at least 50.00. If subtotal drops below 50.00, remove the promotion and notify the user. Increasing the subtotal again does not reactivate it; the user must explicitly apply it again.
B10. Calculate discount to cents using nearest-cent half-up rounding, subtract it from subtotal, then calculate 10% GST on the amount after discount using the same rounding. Final total is amount after discount plus GST. All money output uses two decimal places. Financial results must be exact; choose an appropriate implementation.
B11. Checkout a nonempty cart by displaying five totals: subtotal, discount, amount after discount, GST and final total. Exit after the receipt. Do not collect payment. Receipt itemisation is optional; cart itemisation in B03 is compulsory. Empty checkout is rejected and shopping continues.
B12. Each launch starts with an empty cart and the same stock limits. There is no persistence, stock reservation across runs, account, database or network service.
B13. Provide a normal exit option and handle EOF and Ctrl+C cleanly without a traceback or accidental checkout.
B14. Provide app/app.py as the CLI entry point, a README with exact setup/run/test instructions and a declared test dependency file. Keep the internal module design open to the agent. The project .venv belongs under app/. Reuse an installed compatible Python if available; do not pre-create the application environment before coding.
B15. Provide meaningful automated tests for normal, invalid and boundary cases, including at least eight test functions, and evidence against the common acceptance checklist. Function counts and parameterised case counts must be reported separately; neither is a quality score.
B16. Deployment is documentation and research only. Plan local CLI handover and outline future web, Android and iOS release workflows with source links. No actual publication, hosting setup, store submission or payments are required.

## Required financial examples
A: P001 quantity 2 plus P004 quantity 3; apply SAVE10. Subtotal 56.00; discount 5.60; amount after discount 50.40; GST 5.04; final 55.44.
B: P001 quantity 1 plus P003 quantity 1; apply SAVE10. Subtotal 50.50; discount 5.05; amount after discount 45.45; GST 4.55; final 50.00.

## Open choices and future ideas
Menu wording/order, internal modules, data structures, error wording and optional receipt itemisation are open. Ask useful questions about genuinely unresolved choices and record each answer. Fixed B01-B16 rules cannot be silently replaced by a local decision. A GUI or other new capability may be proposed and justified in RE/design, but must be recorded as a future increment and not implemented in this run. Each member resolves open questions independently with their agent; there is no shared RE approval meeting. Record differences for later comparison.
