# Quotes: Bpay Biller Code and Account CRN from the Deal

Started 18 November 2026. Requested by Grace.

## Goal

When a Quote is created, it should carry the Biller Code and the Account CRN from its Deal.

## Status

- Fields created on Quotes (18 Nov 2026). Done.
- Fields placed on the Quote layout. Not done (Grace, in Setup).
- Account CRN copied from the Deal on create. Not built.

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

## Open choices

1. Biller Code on UK Quotes: the Quote field is a fixed `508572` for every Quote. The Deal's field is empty for UK deals. Keep it fixed, or make the Quote formula conditional.
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

## Next

1. Grace to send the criteria and function code of "Quote Currency Follows Deal".
2. Claude to add the Account CRN copy (Deal `Account_CRN` to Quote `Account_CRN`) and return the full updated function plus setup steps.
3. Grace to place both fields on the Quote layout.
4. Test on a new Quote and read it back through the connector.
