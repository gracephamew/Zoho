# Purchase Orders: repair the PO to Vendor Bills workflow

Context loaded 19 November 2026 from the shared project memory on Drive. Nothing has been changed in Zoho for this work.

Status: waiting for Grace to approve Phase 1 (read-only wiring check).

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
