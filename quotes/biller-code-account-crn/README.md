# Quotes: Bpay Biller Code and Account CRN from the Deal

Started 18 November 2026. Requested by Grace.

## Goal

When a Quote is created, it should carry the Biller Code and the Account CRN from its Deal.

## Status

- Fields created on Quotes (18 Nov 2026). Done.
- Fields placed on the Quote layout. Not done (Grace, in Setup).
- Account CRN copied from the Deal on create or edit when empty. Function written 18 Nov 2026 (`quote_account_crn_from_deal.dg`). Not yet installed in Zoho.

## What was found (read-only checks, 18 Nov 2026)

Deals (source):

| Label | API name | ID | Type |
|---|---|---|---|
| Account CRN | `Account_CRN` | `93610000033373525` | Text, 10 characters |
| Bpay Biller Code | `Bpay_Biller_Code` | `93610000033811381` | Formula returning text, read only. The API does not return the expression. |

- Fact: Quotes link to Deals through the lookup `Deal_Name` (`93610000000002941`).
- Fact: before this work, Quotes had no Biller Code or CRN field.
- Fact: on the five newest Quotes, the Deal's Account CRN was filled on all five (8 characters, for example `10001758`, `20011250`). The Deal's Bpay Biller Code was `508572` on the DW (Australian) deal and empty on the four UK deals.
- Unverified: why the Deal's Biller Code is empty for UK deals. The formula is assumed to depend on country or MoR.

## What was changed

Created on Quotes through the connector, 18 Nov 2026, at Grace's request:

| Label | API name | ID | Type |
|---|---|---|---|
| Account CRN | `Account_CRN` | `93610000035142239` | Text, 10 characters |
| Bpay Biller Code | `Bpay_Biller_Code` | `93610000035142248` | Formula returning text, expression `'508572'` |

- Fact: Zoho confirmed both as created, and a COQL query on Quotes using both API names succeeded.
- Fact: straight after creation, both read empty on the three newest existing Quotes.
- Unverified: the formula is expected to show `508572` on new Quotes and on existing Quotes once next saved. Not yet seen.

## Bpay Biller Code formula, to match the Deal (18 Nov 2026)

- Fact: the Deal formula is `If(${Deals.Country (EW)}=='AU','508572','')` (Grace's screenshot of the Deals field).
- Fact: Quotes has its own Country (EW) field, `Country_EW`, text, id `93610000014630058`. On the 200 newest Quotes with a Deal it equalled the Deal's Country (EW) on every row (values AU and UK only).
- Fact: the two Quotes created after the field was added show `508572`, so the fixed formula is live. Older Quotes stay empty until next saved.
- The connector cannot edit an existing field, so Grace changes the Quote formula in Setup to:

```
If(${Quotes.Country (EW)}=='AU','508572','')
```

- Unverified: whether Country (EW) is already filled at the moment a Quote is first saved. If a workflow fills it afterwards, the Biller Code may show empty until the next save.
- Status: sent to Grace 18 Nov 2026. Not yet confirmed as applied.

## Open choices

1. Biller Code on UK Quotes: decided 18 Nov 2026. The Quote formula is to match the Deal (508572 only when Country (EW) is AU).
2. Which workflow carries the Account CRN copy (see below).
3. Whether the Books sync needs the CRN. If it does, the copy must run before the sync rules, or the sync functions should read the CRN from the Deal.

## Candidate workflows on Quotes

From Grace's screenshots of Setup, Workflow Rules, 18 Nov 2026. Criteria and function code not yet seen.

| Rule | Trigger | Status | Fit |
|---|---|---|---|
| Quote Currency Follows Deal | Create or Edit | On | Preferred. Already copies a Deal value to the Quote, and catches a Deal linked after creation. |
| PopulateQuoteRecordId | Create | On | Fallback. Appears to run on every new Quote; does not catch later edits. |
| Sync Quote 1 to Books - On Create | Create | On | Order matters if Books needs the CRN. |
| Sync Quote2 to Books | Create | On | Order matters if Books needs the CRN. |

## The automation (written 18 Nov 2026, not yet installed)

Grace's instruction: on create or edit, if Account CRN is empty and the Deal is not empty, map it from the Deal.

Design: a new, separate workflow rule with its own function, so no existing rule or function is changed.

- Function: `quote_account_crn_from_deal.dg` in this folder. Category automation, returns void, one argument `quoteId`.
- It writes only `Quotes.Account_CRN`, and only when that field is empty and the Deal has a value.
- The update is sent with an empty trigger list, so it does not fire the other Quotes workflows (Books sync and so on).
- Not tested: the code has not been compiled or run in Zoho.

Setup steps in Zoho CRM:

1. Setup, Automation, Actions, Functions, New Function. Category: Automation. Display name: Quote Account CRN From Deal. API name: `quote_account_crn_from_deal`. Module: Quotes.
2. Edit Arguments: add `quoteId`, type string.
3. Paste the whole file so the signature is line 1. If Zoho shows a different API name in the right-hand panel, change the name in line 1 to match it exactly. Save.
4. Setup, Automation, Workflow Rules, Create Rule. Module: Quotes. Name: Quote Account CRN From Deal.
5. When: Record action, Create or Edit. Leave "Repeat this workflow whenever a quote is edited" ticked.
6. Condition: Account CRN is empty AND Deal Name is not empty (pattern `1 and 2`).
7. Instant action: Function, choose the function above, map `quoteId` to Quotes, Quote Id. Save and switch on.
8. Test: create a Quote on a Deal that has an Account CRN, then tell Claude the Quote, so it can be read back through the connector.

Limits to know:

- If the CRN is added to the Deal later, the Quote picks it up only the next time that Quote is edited.
- Existing Quotes created from 1 Sept 2026 were backfilled on 18 Nov 2026 (see below). Older Quotes are filled only when next edited.
- Because triggers are suppressed, a rule that needs to react to Account CRN being filled will not fire from this update.

## Backfill of Account CRN on existing Quotes (18 Nov 2026)

Grace's instruction: populate the CRN on all Quotes created from 1 Sept 2026.

- Scope: Quotes with Created Time from 1 Sept 2026 00:00 (Melbourne). 480 Quotes on 201 Deals.
- Rule: write the Deal's `Account_CRN` to the Quote only where the Quote's was empty, a Deal was linked and the Deal had a CRN.
- Written through the connector at 11:41 to 11:43, in 5 batches, with an empty trigger list, so no workflow fired. Modified By on these Quotes now reads Admin EW Zoho.

| Group | Count |
|---|---|
| Updated | 422 |
| Already had a CRN (equal to the Deal's) | 1 |
| Not filled: no Deal linked | 8 |
| Not filled: Deal has no CRN | 49 (on 27 Deals) |

- Fact: checked after the write with a fresh query and jq. All 422 hold exactly the Deal's CRN, 0 differ, and no Quote with a Deal CRN is left empty.
- Files: `backfill-2026-11-18/updated.jsonl` (the 422 Quote ids and the CRN written, which is also the undo list) and `backfill-2026-11-18/not_filled.json` (the 8 Quotes with no Deal, the 27 Deals with no CRN and their 49 Quotes).
- To undo: set `Account_CRN` to empty on the ids in `updated.jsonl`.
- Quotes created before 1 Sept 2026 were not touched.

## Next

1. Grace to install the function and the workflow rule (steps above) and report any compile error.
2. Grace to place both fields on the Quote layout.
3. Test on a new Quote and read it back through the connector.
4. Decide what to do about the 27 Deals with no Account CRN and the 8 Quotes with no Deal (see the backfill section).
