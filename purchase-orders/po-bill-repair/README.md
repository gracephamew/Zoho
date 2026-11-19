# Purchase Orders: repair the PO to Vendor Bills workflow

Context loaded 19 November 2026 from the shared project memory on Drive. Nothing has been changed in Zoho for this work.

Status: Phase 1 connector checks done 19/11/2026 (read-only, see "Phase 1 findings" below). The wiring map is not complete: three items must come from Setup (Grace).

## Source documents (Drive, `_Claude_Project_Memory`, written 19/11/2026 by the Cowork project)

These are the source of truth. This file is a digest, not a copy.

| Document | Drive file id | What it holds |
|---|---|---|
| po-bill-repair-claude-code-brief-2026-11-19.md | `1-v9qvXeZ1WCCougQh1OKeliRjlDJQ5IP` | The brief for Claude Code: objective, facts, constraints, six phases |
| po-bill-workflow-framework-2026-11-19.md | `1-aHa-tWdUSdyTs9_F9QuI2WIfKp-Dy7n` | Full chain: PO Status values, rules, functions, Books sync, defects, verdict |
| createvendorpaymentschedule1-v4-code-2026-11-19.md | `10kQBTRfeYkwrbuimDmJoK0vWizM01bVU` | Draft Deluge v4 of `automation.CreateVendorPaymentSchedule1`, full code, write-set diff |
| po-record-lock-after-vendor-bills-2026-11-19-v2.md | `1rVdup8YF0IJNU9hQXWFiCVcQvaxaqYg9` | Record lock rule, the two locking Status values, PO-1211 case |

## Objective

Stop Purchase Orders from stacking duplicate Vendor Bills. One guarded path creates or edits bills. A bill already synced to Zoho Books is never touched.

## What is established (as recorded in the source documents)

- Fact: rule "Create Vendor Payment Schedule" (id `93610000004176388`, On) runs when PO Status becomes "Vendor Bills Created in CRM". Its function takes the argument `recid`.
- View: that function is `automation.CreateVendorPay`. `automation.CreateVendorPaymentSchedule1` takes `poId`, so nothing calls it. Unverified until its Associations are read in Setup.
- Fact: `CreateVendorPay` calls `Create_1_VendorBill` (row 1 only, syncs to Books at once) and `CreateVendorBills` (every row with no Bill).
- Fact: `Recreate_VPS` builds only a VPS and its rows. It creates no bill.
- Fact: `Zoho_Books_Bill_Id` is the only trustworthy "synced" field.
- Fact (audit): 17 POs built two or more lots of bills. 26 POs carry more than one VPS. 47 bills have no VPS lookup.
- Fact: record lock "Bills issued PO Locked" locks a PO when Status is "Vendor Bills Created in CRM" or "Vendor Bills Manually Updated", for all profiles except Administrator. The API user Support A N is Administrator, so functions and API calls are not stopped by the lock.
- Fact: "Vendor Bills Manually Updated" was added by Grace on 19/11/2026 and starts no bill build.

## Constraints

1. No write to any rule or function without Grace's approval in chat.
2. A write-set diff is mandatory for every Deluge change: Added, Removed, Changed source. Grace approves each.
3. Never call `Create_1_VendorBill` on a recreated VPS.
4. Never set "Vendor Bills Created in CRM" on a PO that already has bills. Use "Vendor Bills Manually Updated".
5. For money, ask Zoho Books. CRM payment fields are a cache.
6. Confirm API names in Setup before coding.
7. Full code always. Australian spelling. No em dashes. Dates DD/MM/YYYY.

## Phases (from the brief)

1. Confirm the wiring, no writes: Associations of "Create Vendor Payment- Parent"; read `SyncBillToZohoBooks` and the Vendor Bills rule `SyncBillsToZohoBooks`; re-read the live PO Status picklist. Test: Grace signs off the wiring map.
2. Guarded entry function on the Status rule that checks existing bills first. Test: dry run on PO-1211, log only.
3. Safe bill creation inside v4 (`CreateVendorBills(newVpsId)` for rows with no Bill). Test on a sandbox PO.
4. Split label from trigger: Status stays a label; a "Rebuild bills" button or field is the trigger.
5. Sync failure signal: write to `Sync_Error_Message` when the Books sync fails.
6. History clean-up, starting with PO-0402. Grace approves each void by hand.

## What Claude Code can and cannot do for Phase 1

- Can, through the connector: read the PO Status picklist from the field metadata, and read POs, VPS records and Vendor Bills.
- Cannot: read workflow rules, function bodies or function Associations. Grace reads those in Setup and pastes or screenshots them.

## Notes from Claude Code on the v4 draft (View, not yet discussed with Grace)

- The draft's five `invokeurl` writes pass the JSON as `parameters:`. The project memory records that `parameters:` where `body:` was needed caused the $0 planning fee bug. Check which is right for a CRM v8 PUT before v4 is saved.
- The draft header says the function is on the rule "Create Vendor Payment Schedule". The framework document says the rule most likely calls `CreateVendorPay` instead, so v4 will not run until the rule is re-pointed or an entry function exists.

## Open items carried in the source documents

- PO-1211 by hand: VPS898 Payment 3 amount and date, B1493 issue date, the `Grand_Total_in_PO` and `Sum_of_Payable_amt` caches.
- Unverified: whether the record lock blocks API writes by a user who is not exempt.
- Unverified: which function ran on 07/10/2026 20:11 (user Support A N).

## Phase 1 findings from the connector (19/11/2026 17:18 to 17:21, read-only)

Method: field metadata (`getFields`) for Purchase_Orders, Vendor_Bills, Vendor_Payment_Schedules and Vendor_Payment_Version; COQL over all 919 POs, 730 VPS and 1,289 Vendor Bills. No record, field, rule or function was written. Lists of record ids are in `phase1-anomalies-2026-11-19.json`.

### PO Status picklist (Fact, 14 values; shown as display => stored where they differ)

Created; PO & Quote Approved => Approved; Sent to Vendor for Signoff => Delivered; Cancelled; Updated; PO Approved. Pending Quote Approval => Pending Couple Approval; Invoice Attached; Resend Approved Summary to Finance => Sent Approved Summary to Finance; Vendor Bills Created in CRM; Recreate Vendor Bills in CRM => Test VPS Creation; Automated Approval WO Bill; PO & Quote Approved Paid via Qwilr; Void; Vendor Bills Manually Updated.

- Fact: "Void" is in the live picklist and is not in the framework document.
- Fact: several display values differ from the stored value. A rule or function that tests the Status must use the form that the function actually receives. Unverified which form Deluge returns for these.

### PO Status against VPS and bills (Fact)

| PO Status | POs | With bills | With VPS, no bills | Neither |
|---|---|---|---|---|
| Vendor Bills Created in CRM | 623 | 614 | 9 | 0 |
| PO & Quote Approved | 120 | 8 | 0 | 112 |
| Created | 81 | 1 | 0 | 80 |
| Cancelled | 25 | 16 | 0 | 9 |
| Automated Approval WO Bill | 18 | 0 | 0 | 18 |
| PO Approved. Pending Quote Approval | 17 | 0 | 0 | 17 |
| Sent to Vendor for Signoff | 9 | 0 | 0 | 9 |
| Void | 8 | 4 | 1 | 3 |
| "Approved" (returned separately from PO & Quote Approved) | 5 | 2 | 0 | 3 |
| Recreate Vendor Bills in CRM | 3 | 3 | 0 | 0 |
| Resend Approved Summary to Finance | 2 | 1 | 1 | 0 |
| Invoice Attached | 1 | 0 | 0 | 1 |
| Vendor Bills Manually Updated | 0 | | | |
| (no Status) | 7 | 2 | 0 | 5 |

- Fact: 9 POs say "Vendor Bills Created in CRM" but have a VPS and no bill.
- Fact: 37 POs have bills while their Status is something else (16 Cancelled, 8 PO & Quote Approved, 4 Void and others). Constraint 4 applies to these: setting "Vendor Bills Created in CRM" on them would build a second lot.

### VPS (Fact)

- 730 VPS. Stored Status: Bill Issued 691, Draft 32, Voided 3, empty 3, Approved- Issue Bill 1.
- The Status picklist options are -None-, Planned, Draft, Void, Approved- Issue Bill (stored "Approved"), Bill Issued, Paid, Cancelled Deal, Adjusted, Ready for Bill. "Voided" is not an option, yet 3 records hold it, so a function wrote it. The v4 draft also writes and filters "Voided". It works as stored text but will not match a picklist filter on "Void". Decide on one word before v4 is saved.
- 21 POs carry more than one VPS that is not Voided (the brief's audit said 26 with more than one VPS). 20 of the 21 have at least one bill synced to Books. By PO Status: 16 Vendor Bills Created in CRM, 3 Recreate Vendor Bills in CRM, 1 Resend Approved Summary to Finance, 1 Cancelled.
- 30 VPS have no Purchase Order lookup.
- VPS_Version values: "1" 714, "2" 10, "21" 3, and one each of "11", "111", "211". View: the odd values look like text joined instead of a number increased.
- Subform module `Vendor_Payment_Version` (id `93610000004295366`): `Bill` (lookup to Vendor_Bills), `Payment_Version` (text, label "Payment N0"), `Payment_Name`, `Scheduled_Amount_Vendor_Currency`, `Balance_due`, `Due_Date`, `Due_Reminder_Date`, `Bill_Issue_date`, `Paid_Date`, `Status` (text), `Status_Last_Updated`, `Bills_VPS_Custom_Number` (label "Bill Name"), `Tax`, `Mor_Tax`, `Parent_Id`.

### Vendor Bills (Fact)

- 1,289 bills. 717 have a `Zoho_Books_Bill_Id`. `Sync_to_books`: YES 720, empty 568, NO 1.
- 4 bills say YES with no Books id; 1 has a Books id without YES. This supports the rule that only `Zoho_Books_Bill_Id` is trusted.
- `Sync_Error_Message` is empty on all 1,289 bills, so nothing writes a failure signal today (Phase 5).
- `Payment_Status` stored: Unpaid 694, Paid 586, Voided 5, empty 3, Open 1. "Unpaid" is not a picklist option (options: -None-, Draft, Issued, Partially Paid, Paid, Voided, Credited, Open, Overdue, Bill Not Found, Approved).
- 5 bills read Paid with no Books id, and 133 read Unpaid with a Books id. These are CRM cache values; money questions go to Zoho Books.
- `Status` on bills is empty on 1,244; Voided 21, Paid 16, Approved- Sync to Books 7, Cancelled Deal 1.
- 42 bills have no VPS lookup (audit said 47); 1 of them has a Books id. 71 bills have no PO lookup.

### Wiring map so far

| Link | State |
|---|---|
| PO Status "Vendor Bills Created in CRM" starts rule `93610000004176388` | Fact per source documents. Not readable by the connector. |
| Which function that rule calls (`CreateVendorPay` or `CreateVendorPaymentSchedule1`) | Unverified. Needs the Associations of "Create Vendor Payment- Parent" from Setup. |
| Fields the functions read and write on PO, VPS, VPS rows and bills | Fact: every API name used in the v4 draft exists in the live org. |
| Bill to Books sync (`SyncBillToZohoBooks`, rule `SyncBillsToZohoBooks`) | Unverified. Code and rule criteria needed from Setup. |
| Failure signal | Fact: none written today. |

### Needed from Grace (Setup) to finish Phase 1

1. Setup, Functions, "Create Vendor Payment- Parent": the Associations panel (which rules, buttons or schedules call it) and the first line of its code.
2. The full code of `SyncBillToZohoBooks`.
3. Setup, Workflow Rules, Vendor Bills, `SyncBillsToZohoBooks`: when it runs, its criteria and its action.

## Bill to Books sync rule, from Grace's screenshot (19/11/2026 17:59)

- Fact: rule `SyncBillsToZohoBooks`, id `93610000004897404`, module Vendor Bills. It runs on Bill Issue Date at 08:00 AM, Recur Once. Condition: Status isn't "Cancelled Deal". Instant action: function `SyncBillToZohoBooks`. No scheduled actions.
- Fact: Grace also sent two function links, ids `93610000004305171` and `93610000004897390`. Claude Code cannot open Setup pages, so their names and code are still unread.
- View: `93610000004897390` is `SyncBillToZohoBooks`, because its id sits next to the rule's id. `93610000004305171` would then be "Create Vendor Payment- Parent". Unverified.

What the bill data shows against this rule (read-only COQL, 1,263 bills with a Bill Issue Date; ids in `phase1-bills-issue-date-passed-not-in-books-2026-11-19.json`):

- Fact: 692 bills have an issue date that has passed and a Books id. 57 have an issue date that has passed and no Books id. `Sync_Error_Message` is empty on all 57.
- Fact: of the 57, 15 were created after their own issue date. View: a date rule cannot fire for a date already past, so these were never going to sync by this rule.
- Fact: the other 42 existed before their issue date and still have no Books id; 28 of them have a March 2026 issue date. Unverified why (failed sync, or the rule was created after those dates).
- Fact: 26 bills with a future issue date are already in Books, so a second path syncs bills before the rule runs (the source documents name `Create_1_VendorBill`).
- Fact: the rule's condition excludes only "Cancelled Deal". 7 bills with Status "Voided", a future issue date and no Books id pass the condition. Unverified whether the function skips voided bills or bills that already have a Books id; that needs the code.
- Fact: 14 voided bills carry a Books id. For their state in Books, ask Zoho Books.

## Live code read 19/11/2026 18:02 (pasted by Grace, kept unchanged in `live-code-2026-11-19/`)

Two functions: `SyncBillToZohoBooks.dg` (argument `recid`) and `CreateVendorPaymentSchedule1.dg` (argument `poId`). Which Setup link belongs to which, and the Associations of each, are still Unverified. The code of `CreateVendorPay` ("Create Vendor Payment- Parent"), `CreateVendorBills`, `Create_1_VendorBill` and `Recreate_VPS` has not been seen by Claude Code.

### `SyncBillToZohoBooks` (Fact unless marked)

What it does: reads the bill, stops unless MoR is "Easy Weddings Travel Pty Ltd", finds the vendor in Books by CRM vendor id, finds each product in Books by SKU text search, posts a bill to Books org `7005347798`, approves it, then writes `Sync_to_books` = "Yes" and `Zoho_Books_Bill_Id` on the CRM bill.

Write set: Books: one new bill, then approve. CRM: `Vendor_Bills.Sync_to_books`, `Vendor_Bills.Zoho_Books_Bill_Id`.

Defects:

1. No check for an existing `Zoho_Books_Bill_Id`. A second run posts the bill again. Unverified whether Books rejects a repeated bill number for the same vendor.
2. No check for a voided bill. Neither `Status` nor `Payment_Status` is tested here, and the rule tests only Status "Cancelled Deal".
3. Every failure is silent. Wrong or empty MoR, vendor not in Books, Books error on create: nothing is written to the bill. `Sync_Error_Message` is never written.
4. Vendor search: if Books answers code 0 with no contacts, `.get(0)` fails and the function stops.
5. A product not found in Books is dropped from the bill without notice, so the Books bill can be smaller than the CRM bill. The search is a text search on SKU and takes the first hit, which may be a different item.
6. The approve call's answer is not checked.
7. The CRM update has no trigger option, so Vendor Bills edit workflows can fire from it.
8. The Vendor record is re-read inside the product loop (one extra call per line). Two connections are used: "zohobooks" for reads, "books" for writes.

Data check against defect 3 (57 bills past their issue date with no Books id): 25 have no MoR and 2 have MoR "Easy Weddings Limited", so the function skipped them by design and left no note. The other 30 have the right MoR and still did not sync (24 of them with March 2026 issue dates). Unverified why; the function logs are the only record.

### `CreateVendorPaymentSchedule1`, live version (Fact unless marked)

What it does: if the PO has no VPS, calls `CreateVendorPay(poId)` and stops. Otherwise it keeps bills whose `Payment_Status` is "Paid", sets `Payment_Status` = "Voided" on every other bill, sets `Status` = "Voided" on every existing VPS, calls `Recreate_VPS(poId, remainingAmount, nextVersion, paidCount)`, then points the paid bills at a VPS.

Write set: `Vendor_Bills.Payment_Status`, `Vendor_Bills.Vendor_Payment_Schedule`, `Vendor_Payment_Schedules.Status`, plus whatever `CreateVendorPay` and `Recreate_VPS` write.

Defects:

1. It voids by `Payment_Status`, the CRM cache. A bill that is in Books but not shown as Paid in CRM is marked Voided in CRM, and nothing is sent to Books. This breaks the rule that a synced bill is never touched (133 bills are Unpaid in CRM with a Books id).
2. It decides "paid" from the same cache, so a bill paid in Books but stale in CRM is voided and its amount is scheduled again.
3. It voids `Payment_Status`, not the bill's `Status`. The sync rule and function look at neither, so a bill voided here still goes to Books on its issue date.
4. Version bug: `nextVersion = 1` sits inside the loop, so only the last VPS counts, and `VPS_Version` is text, so `"2" + 1` gives "21". This explains the stored values "21", "11", "111", "211". (View on the mechanism; the values are Fact.)
5. "Pick latest created VPS" takes the first search result. The search order is not stated, so paid bills can be attached to an old, voided VPS.
6. It writes "Voided" to VPS Status, which is not a picklist option.
7. After it runs, the new VPS has no bills (per the source documents, `Recreate_VPS` builds none). Whatever creates bills next is outside this function.
8. No trigger option on any update, and no guard against running twice.

### Wiring map, updated

| Link | State |
|---|---|
| PO Status "Vendor Bills Created in CRM" starts rule `93610000004176388` | Fact per source documents |
| Function on that rule | Unverified. Needs the rule's action or the functions' Associations. |
| `CreateVendorPaymentSchedule1(poId)` calls `CreateVendorPay(poId)` when the PO has no VPS, else voids and calls `Recreate_VPS` | Fact (code) |
| What calls `CreateVendorPaymentSchedule1` | Unverified |
| Bill reaches Books by rule `93610000004897404` on Bill Issue Date 08:00, function `SyncBillToZohoBooks(recid)` | Fact (screenshot and code) |
| Second, earlier path to Books | Fact from data (26 future-dated bills already in Books). Source documents name `Create_1_VendorBill`. Code unseen. |
| Failure signal | Fact: none |

Still needed to close Phase 1: the Associations panels of the two linked functions; the action shown on the rule "Create Vendor Payment Schedule" (`93610000004176388`); the code of `CreateVendorPay`, `CreateVendorBills`, `Create_1_VendorBill` and `Recreate_VPS` if Grace wants them in the map rather than taken from the source documents.

## Remaining four functions read 19/11/2026 18:10 (pasted by Grace, kept unchanged in `live-code-2026-11-19/`)

`CreateVendorPay.dg` (v2, 16/09/2026), `CreateVendorBills.dg`, `Create_1_VendorBill.dg`, `Recreate_VPS.dg`. With these, all six functions in the chain have been read. Associations are still Unverified.

### The chain as the code has it (Fact)

1. `CreateVendorPay(recid)`: stops if the PO has any related Vendor Bill. If the PO has a VPS, it takes the first one returned and calls `Create_1_VendorBill` and `CreateVendorBills` on it. Otherwise it builds a VPS (version 1, Status Draft, rows from Vendor Payment 1 to 4), updates Services Booked and the PO, then calls the same two functions.
2. `Create_1_VendorBill(vpsId)`: creates a bill from row 1 of the VPS, links it to row 1, then calls `SyncBillToZohoBooks` at once, whatever the Bill Issue Date.
3. `CreateVendorBills(vpsId)`: creates a bill for every row with no Bill, then sets VPS Status "Bill Issued".
4. `CreateVendorPaymentSchedule1(poId)`: the rebuild path, described above. Calls `Recreate_VPS`.
5. `Recreate_VPS`: builds a new VPS only. No bills.
6. `SyncBillToZohoBooks(billId)`: also run by the date rule on Bill Issue Date at 08:00.

### Main finding: Bill 1 is sent to Books twice by design

- Fact: `Create_1_VendorBill` syncs Bill 1 the moment it is created. Since `CreateVendorPay` v2 (16/09/2026), Bill 1's issue date is its due date minus 7 days, usually in the future.
- Fact: the date rule then runs `SyncBillToZohoBooks` on that bill again on its issue date, and that function has no check for an existing Books id.
- Fact: 26 bills are in Books today with an issue date still to come. The rule will run on each of them.
- Unverified: whether Books refuses the second post because the bill number already exists for that vendor. If it refuses, the second run fails silently and does no harm. If it accepts, Books holds two bills and the CRM `Zoho_Books_Bill_Id` is overwritten with the second. How to check: in Zoho Books, search one Bill 1 created after 16/09/2026 whose issue date has passed, for example CRM bill `93610000022578179` (created 17/09/2026, issue date 23/09/2026), and count the Books bills with its number.
- Fact: the header of `CreateVendorPay` v2 says Bill 1 now syncs 7 days before it is due "like every other bill". The code does not do that; Bill 1 still syncs at creation.

### Other defects (Fact unless marked)

`CreateVendorPay`:
1. The "bills already exist" guard counts voided bills too. After a rebuild, the new VPS can never get bills through this function.
2. When a VPS exists and no bill does, it uses the first VPS returned (not the newest, not checked for Voided) and makes Bill 1 on it without checking that row 1 is free. 9 POs are in this state now.
3. The related list names "Vendor_Bills" and "Vendor_Payment_Schedule" are Unverified against the live related lists. If either is wrong, its guard does not work.

`Create_1_VendorBill`:
4. No check that row 1 already has a Bill.
5. If the bill is not created, it still calls the sync with an empty id.
6. The product search text has a leading space (" Vendor Bill 1"), and an empty result is read with `.get(0)`.

`CreateVendorBills`:
7. The product is chosen by a counter, not by the row's payment number. The first row without a bill always gets "Vendor Bill 2".
8. If the product is not found, a bill with no lines is created. It can never sync.
9. VPS Status is set to "Bill Issued" even when a bill failed to create.
10. Bills take MoR from the Deal. A Deal with no MoR gives a bill with no MoR, which the sync skips silently (25 bills today).

`Recreate_VPS`:
11. It writes the row date to `Bill_Issue_Date`. The field's API name is `Bill_Issue_date`. View: the issue date is not saved on rebuilt rows, so their bills have no issue date and the date rule never syncs them. 26 bills have no Bill Issue Date.
12. It still uses the old rules: Payment 1 due from the approved date, issue date 30 days before due. Grace's 16/09/2026 rule (PO dates are due dates, issue 7 days before) is not applied here.
13. It does not write Tax, Mor_Tax or VPS Status. With Mor_Tax empty, `CreateVendorBills` would mark the bill "GST 10%".
14. It reads the PO subform `Product_Details`; `CreateVendorPay` reads `Purchase_Items`. Unverified which is right.
15. The amounts arrive as Int arguments. Unverified whether cents are lost.
16. It skips the first N payments by number, assuming the paid bills are always the first N.

### Wiring map, state at 18:10

| Link | State |
|---|---|
| Rule `93610000004176388` (PO Status "Vendor Bills Created in CRM") calls `CreateVendorPay(recid)` | View, strong: the argument name matches. Associations not seen. |
| What calls `CreateVendorPaymentSchedule1(poId)` | Unverified. View: a rule or button on "Recreate Vendor Bills in CRM". |
| `CreateVendorPay` -> `Create_1_VendorBill` -> `SyncBillToZohoBooks` (immediate) | Fact (code) |
| `CreateVendorPay` -> `CreateVendorBills` | Fact (code) |
| `CreateVendorPaymentSchedule1` -> `CreateVendorPay` or `Recreate_VPS` | Fact (code) |
| Date rule `93610000004897404` -> `SyncBillToZohoBooks` | Fact (screenshot) |
| Failure signal anywhere in the chain | Fact: none |

To close Phase 1: the Associations panel of `CreateVendorPay` and of `CreateVendorPaymentSchedule1`, then Grace's sign-off.
