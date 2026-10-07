# Common MiniCart acceptance checklist

Fixed before implementation. These cases use the CLI contract; menu numbers may vary. Each member may add tests but must retain these checks. Record evidence from real execution. Repeat each fresh scenario with a new process. B16 is evaluated separately through the deployment document checklist.

| ID | Brief | Procedure | Expected result |
|---|---|---|---|
| C01 | B01 | Fresh start; view catalogue. | All four codes, names, prices and stock limits exactly match the brief. |
| C02 | B02 B03 B07 | Add P001 quantity 2 and P004 quantity 3; view cart. | Correct two item lines and subtotal 56.00. |
| C03 | B02 | Fresh: add P001 12 then 8; attempt another 1. | 20 accepted cumulatively; extra 1 rejected; quantity remains 20. |
| C04 | B04 | Fresh: add P001 2; update P001 to 5. | Replacement quantity 5, line/subtotal 27.50; not quantity 7. |
| C05 | B04 | Remove that item; then update and remove the now absent item. | Empty cart after removal; missing-item operations rejected with no state change. |
| C06 | B05 | Fresh: add code " p001 " quantity " 002 "; apply " save10 " when eligible later. | Code normalisation works and quantity becomes 2; promotion is case-insensitive with trimming. |
| C07 | B05 B06 | Try quantities 0, -1, +1, 1.5, 1e2, blank, abc and non-ASCII numeral ١. | Each rejected; cart/promotion unchanged. Normal shopping still works afterwards. |
| C08 | B06 | Try product P999 and invalid menu input. | Useful error; no mutation or crash; menu continues. |
| C09 | B02 B04 B06 | Fresh: add P003 5; update to 6; separately attempt initial add P003 6. | 5 accepted; both over-stock requests rejected; state preserved. |
| C10 | B08 | Fresh: add P002 24 (48.00); apply SAVE10. | Rejected below threshold; discount 0.00, GST 4.80, final 52.80. |
| C11 | B08 B10 | Fresh: add P002 25 (50.00); apply SAVE10 twice. | Exactly 5.00 discount once; net 45.00; GST 4.50; final 49.50 on both applications. |
| C12 | B08 B06 | Build eligible cart; apply an invalid code before and after SAVE10. | Invalid code rejected; prior cart and valid active promotion remain unchanged. |
| C13 | B09 | Fresh: add P004 4 (60.00), apply SAVE10; update to 5 (75.00). | Active promotion recalculated: discount 7.50, net 67.50, GST 6.75, final 74.25. |
| C14 | B09 | Start example A and apply SAVE10. Remove P001, then add P001 2 again. | At 45.00 promotion removed with notice. Returning to 56.00 does not reactivate it: discount 0.00, GST 5.60, final 61.60. |
| C15 | B09 | Continue C14, explicitly apply SAVE10 again. | Discount 5.60 and final 55.44 restored. |
| C16 | B10 B11 | Run required example A then checkout. | Five receipt totals 56.00 / 5.60 / 50.40 / 5.04 / 55.44; process exits. |
| C17 | B10 B11 | Run required example B then checkout. | Five receipt totals 50.50 / 5.05 / 45.45 / 4.55 / 50.00; half-cent GST rounds up. |
| C18 | B10 B11 | Fresh: add P001 1; checkout without promotion. | Totals 5.50 / 0.00 / 5.50 / 0.55 / 6.05, all money displayed to two places. |
| C19 | B11 | Fresh: view cart and attempt checkout while empty. | Empty message, checkout rejected, menu continues; no successful receipt or exit. |
| C20 | B12 | Finish or exit a run with items; relaunch. | Cart empty; original stock limits restored; no persistent customer/cart data. |
| C21 | B13 | Use normal exit; separately send real process EOF at menu and quantity prompt. | Clean exit without traceback or receipt. Record exit codes and actual technique. |
| C22 | B13 | Human presses physical Ctrl+C at menu and during an action prompt in separate runs. | Clean return to terminal; no traceback/receipt. A mocked exception alone is only partial evidence. |
| C23 | B14 | In a disposable copy/environment follow README setup, dependency install and launch. | Commands work on recorded target. Record interpreter/dependency versions; never claim clean-machine proof from reusing the original .venv. |
| C24 | B15 | Run meaningful pytest suite and map tests/CLI/manual evidence to C01-C24. | At least eight meaningful functions, full initial/final output and exit codes; report pass/fail/skip, gaps and corrections honestly. |
