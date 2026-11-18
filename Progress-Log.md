# Progress Log: Zoho CRM Development

## Current status (18 Nov 2026)

- Repo `gracephamew/zoho` set up on 18 Nov 2026 as the home for Zoho CRM development work.
- Quotes: Account CRN and Bpay Biller Code fields exist. The function that copies Account CRN from the Deal is written but not yet installed in Zoho.

## Open items

1. Quotes: Grace to install `quote_account_crn_from_deal` and its workflow rule (steps in the Quotes README), then test. Not compiled or run yet.
2. Quotes: place Account CRN and Bpay Biller Code on the Quote layout (Grace, in Setup).
3. Quotes: decide whether Bpay Biller Code stays a fixed 508572 on UK Quotes, where the Deal's Biller Code is empty.
4. Quotes: confirm whether the Books sync needs the CRN, which affects workflow order.
5. Zoho CRM licence shows a paid expiry of 24 Nov 2026. Check renewal.
6. Quotes: decide on a one-off backfill of Account CRN on existing Quotes.
7. Decide whether the Crew Package to Asana automation stays in `gracephamew/crew` or moves here.

---

## 18/11/2026

**Chats**
- 10:28, Grace, Zoho CRM connection check and scope of dev work (functions, workflows, schedules, buttons).
- 10:33, Grace, carry Biller Code and Account CRN from Deal to Quote on create.
- 10:36, Grace, create the two fields on Quotes.
- 10:48, Grace, move Zoho work into `gracephamew/zoho`.
- 11:10, Grace, build the automation: on create or edit, if Account CRN is empty, map it from the Deal.

**Done**
- Confirmed the Zoho CRM connector reaches org `7005318614` (production).
- Read-only check of Deals and Quotes fields and the five newest Quotes. Details in `quotes/biller-code-account-crn/README.md`.
- Created two custom fields on Quotes through the connector: Account CRN (`Account_CRN`, text, 10, id `93610000035142239`) and Bpay Biller Code (`Bpay_Biller_Code`, formula returning text, expression `'508572'`, id `93610000035142248`).
- Set up this repo: `CLAUDE.md`, `Project-Instructions.md`, this log and the Quotes work folder. Created the Drive folder `Claude Projects/Zoho-CRM-Development/`.

- Wrote `quotes/biller-code-account-crn/quote_account_crn_from_deal.dg` and the setup steps for a new Quotes workflow rule. Not installed or tested in Zoho.

**Changed**
- The Quotes work was first logged in `gracephamew/crew`. It now lives here, and the crew log points to this repo.

**Decisions**
- Zoho CRM development work lives in `gracephamew/zoho`, separate from the Zoho to Asana automations in `gracephamew/crew`.
- The Account CRN copy is a new, separate workflow rule and function, so no existing rule is changed. Its update suppresses triggers, so other Quotes workflows do not fire from it.
- Bpay Biller Code on Quotes is a calculated field with a fixed value of 508572 (Grace, 18 Nov 2026).

**Learned**
- The Zoho CRM connector can read and write records, create fields and update CRM variables. It has no tools for Deluge functions, workflow rules, schedules, custom buttons or layout edits.
- Deals hold `Account_CRN` (text, 10) and `Bpay_Biller_Code` (formula). The Deal's Biller Code was 508572 on the DW deal and empty on the UK deals checked.
- Existing Quotes show the new formula field as empty straight after creation.

**Next**
- Grace installs the function and rule; Claude reads a test Quote back through the connector.
