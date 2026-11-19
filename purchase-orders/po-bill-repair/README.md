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
