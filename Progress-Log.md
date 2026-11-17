# Progress Log: Zoho CRM Development

## Current status (18 Nov 2026)

- Repo `gracephamew/zoho` set up on 18 Nov 2026 as the home for Zoho CRM development work.
- Quotes: Account CRN and Bpay Biller Code fields exist. Nothing copies Account CRN from the Deal yet.

## Open items

1. Quotes: Grace to send the criteria and function code of the "Quote Currency Follows Deal" workflow, so the Account CRN copy can be added there.
2. Quotes: place Account CRN and Bpay Biller Code on the Quote layout (Grace, in Setup).
3. Quotes: decide whether Bpay Biller Code stays a fixed 508572 on UK Quotes, where the Deal's Biller Code is empty.
4. Quotes: confirm whether the Books sync needs the CRN, which affects workflow order.
5. Zoho CRM licence shows a paid expiry of 24 Nov 2026. Check renewal.
6. Decide whether the Crew Package to Asana automation stays in `gracephamew/crew` or moves here.

---

## 18/11/2026

**Chats**
- 10:28, Grace, Zoho CRM connection check and scope of dev work (functions, workflows, schedules, buttons).
- 10:33, Grace, carry Biller Code and Account CRN from Deal to Quote on create.
- 10:36, Grace, create the two fields on Quotes.
- 10:48, Grace, move Zoho work into `gracephamew/zoho`.

**Done**
- Confirmed the Zoho CRM connector reaches org `7005318614` (production).
- Read-only check of Deals and Quotes fields and the five newest Quotes. Details in `quotes/biller-code-account-crn/README.md`.
- Created two custom fields on Quotes through the connector: Account CRN (`Account_CRN`, text, 10, id `93610000035142239`) and Bpay Biller Code (`Bpay_Biller_Code`, formula returning text, expression `'508572'`, id `93610000035142248`).
- Set up this repo: `CLAUDE.md`, `Project-Instructions.md`, this log and the Quotes work folder. Created the Drive folder `Claude Projects/Zoho-CRM-Development/`.

**Changed**
- The Quotes work was first logged in `gracephamew/crew`. It now lives here, and the crew log points to this repo.

**Decisions**
- Zoho CRM development work lives in `gracephamew/zoho`, separate from the Zoho to Asana automations in `gracephamew/crew`.
- Bpay Biller Code on Quotes is a calculated field with a fixed value of 508572 (Grace, 18 Nov 2026).

**Learned**
- The Zoho CRM connector can read and write records, create fields and update CRM variables. It has no tools for Deluge functions, workflow rules, schedules, custom buttons or layout edits.
- Deals hold `Account_CRN` (text, 10) and `Bpay_Biller_Code` (formula). The Deal's Biller Code was 508572 on the DW deal and empty on the UK deals checked.
- Existing Quotes show the new formula field as empty straight after creation.

**Next**
- Add the Account CRN copy to an existing Quotes workflow once its criteria and code are seen.
