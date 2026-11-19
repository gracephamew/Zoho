# Progress Log: Zoho CRM Development

## Current status (19 Nov 2026)

- Repo `gracephamew/zoho` set up on 18 Nov 2026 as the home for Zoho CRM development work.
- Quotes: Account CRN and Bpay Biller Code fields exist. Account CRN is filled on 423 of the 480 Quotes created from 1 Sept 2026. The function that copies Account CRN from the Deal is written but not yet installed in Zoho.

## Open items

1. Quotes: Grace to install `quote_account_crn_from_deal` and its workflow rule (steps in the Quotes README), then test. Not compiled or run yet.
2. Quotes: place Account CRN and Bpay Biller Code on the Quote layout (Grace, in Setup).
3. Quotes: Grace to change the Bpay Biller Code formula on Quotes to `If(${Quotes.Country (EW)}=='AU','508572','')` in Setup, then confirm. New UK Quotes show 508572 until this is done.
4. Quotes: confirm whether the Books sync needs the CRN, which affects workflow order.
5. Zoho CRM licence shows a paid expiry of 24 Nov 2026. Check renewal.
6. Quotes: 27 Deals have no Account CRN (49 Quotes from 1 Sept stay empty) and 8 Quotes have no Deal. Decide whether those Deals should get a CRN. List in `quotes/biller-code-account-crn/backfill-2026-11-18/not_filled.json`.
7. Decide whether the Crew Package to Asana automation stays in `gracephamew/crew` or moves here.
8. Images: Grace to choose a file naming option (`images/naming-structure/README.md`) and answer its seven questions, including whether S3 files are public and how re-issued products keep their pictures.
9. Images: 10 Venues and 7 Suppliers have no Vendor Code; 10 Products have a SKU with no vendor part.
10. Images widget: confirm who manages the AWS account, whether S3 pictures are public or private, and whether to use the hybrid (lead image copied into Zoho for Canvas, full gallery by widget). See `images/s3-widget/README.md`.
11. Products: the 51 Ferry Easy Wedding Crew Bali copies have Product Name in the "•" form, which the existing Product workflow will rewrite as "code . name" on the next edit. Decide which form is wanted. Also confirm the GST Free tax on the copies is right, and check Zoho Books for any items created.
12. 8 Easy Wedding Crew Bali: 12 single products have no cost because the rate card does not cover them (1 videographer, 1-hour, 2 Hours - 2 Photographers, Content 1 to 3 hours). Ask the contractor, or retire them. Packages still carry Ferry's costs. No products exist yet for 4-hour, 12-hour or family photography rates.
13. PO to Vendor Bills repair: Grace to approve Phase 1 (read-only wiring check). Claude Code then needs the Associations of "Create Vendor Payment- Parent" and the bodies of `SyncBillToZohoBooks` and `SyncBillsToZohoBooks` from Setup. See `purchase-orders/po-bill-repair/README.md`.

---

## 19/11/2026

**Chats**
- 17:14, Grace, update context from the Drive folder `_Claude_Project_Memory`.

**Done**
- Read the memory index `MEMORY.md` (12 Nov 2026), `project-knowledge-maintenance.md`, and the five files added since the index: the PO bill repair brief for Claude Code, the PO to Vendor Bills framework, the `CreateVendorPaymentSchedule1` v4 draft code, the PO record lock note (all 19 Nov) and the Installment Calculator summary (17 Nov).
- Listed the folder: 103 files. The other 96 files were not opened; the index line for each was read.
- Added `purchase-orders/po-bill-repair/README.md` (digest and Drive file ids) and a "Shared project memory" section to `CLAUDE.md`.

**Decisions**
- The Drive memory folder stays the source of truth. This repo holds digests and file ids, not copies (Claude's reading of the note "snapshot not a mirror"; Grace to correct if she wants copies here).

**Learned**
- The tax change seen on 18 Nov when duplicating products (AU GST 10% to GST Free) is the known live workflow that rewrites Taxable and Tax on Product saves, recorded in the memory since September.
- The connector can read picklist values through field metadata, so the PO Status picklist can be re-read without Setup. Rules, function bodies and Associations still need Setup.
- Zoho Books, Zoho Analytics and Zoho Projects connectors are now listed for Claude sessions. Not tested.

**Next**
- Phase 1 of the PO bill repair, once Grace approves.

---

## 18/11/2026

**Chats**
- 10:28, Grace, Zoho CRM connection check and scope of dev work (functions, workflows, schedules, buttons).
- 10:33, Grace, carry Biller Code and Account CRN from Deal to Quote on create.
- 10:36, Grace, create the two fields on Quotes.
- 10:48, Grace, move Zoho work into `gracephamew/zoho`.
- 11:10, Grace, build the automation: on create or edit, if Account CRN is empty, map it from the Deal.
- 11:25, Grace, make the Quote Biller Code formula match the Deal's.
- 11:38, Grace, populate Account CRN on all Quotes created from 1 Sept 2026.
- 12:02, Grace, show S3-hosted product images in CRM and Canvas; analyse Vendors and Products and propose image file naming options.
- 12:25, Grace, sample file of 30 Vendors and up to 200 Products with option 2 file names.
- 13:44, Grace, outline of the work to build a widget that shows S3 images in Zoho.
- 14:08, Grace, duplicate the RC Bali - IDR products to the vendor Ferry Easy Wedding Crew Bali - IDR.
- 15:36, Grace, duplicate the Ferry Easy Wedding Crew Bali products to 8 Easy Wedding Crew Bali - IDR with prices from the Studio 8 sheet; then check the prices against the contractor's rate card and correct Zoho and the sheet.

**Done**
- Confirmed the Zoho CRM connector reaches org `7005318614` (production).
- Read-only check of Deals and Quotes fields and the five newest Quotes. Details in `quotes/biller-code-account-crn/README.md`.
- Created two custom fields on Quotes through the connector: Account CRN (`Account_CRN`, text, 10, id `93610000035142239`) and Bpay Biller Code (`Bpay_Biller_Code`, formula returning text, expression `'508572'`, id `93610000035142248`).
- Set up this repo: `CLAUDE.md`, `Project-Instructions.md`, this log and the Quotes work folder. Created the Drive folder `Claude Projects/Zoho-CRM-Development/`.

- Wrote `quotes/biller-code-account-crn/quote_account_crn_from_deal.dg` and the setup steps for a new Quotes workflow rule. Not installed or tested in Zoho.

- Checked Country (EW) on Quotes (`Country_EW`) against the Deal: equal on the 200 newest Quotes with a Deal. Wrote the matching Quote formula for Grace to paste.

- Backfilled Account CRN from the Deal on 422 of the 480 Quotes created from 1 Sept 2026 (triggers off). Verified afterwards: all 422 equal the Deal's CRN, none differ. Not filled: 8 with no Deal, 49 on 27 Deals with no CRN; 1 was already filled.

- Read-only profile of 542 Vendors and 7,349 Products for image naming. Wrote `images/naming-structure/README.md` with four naming options and a recommendation. Nothing changed in Zoho or S3.

- Built `images/naming-structure/sample-option-2.csv` from live Zoho data: 10 Venues, 20 Suppliers and 185 of their active Products (70 rooms), each with its S3 folder and example file names in the option 2 structure.

- Wrote `images/s3-widget/README.md`: outline of the widget build (eight steps), including the Canvas limit and a hybrid approach. Outline only.

- Created 51 products under Ferry Easy Wedding Crew Bali - IDR (`93610000035108815`) as copies of the 51 RC Bali - IDR products, codes `IDRCFJ-7728` to `IDRCFJ-7778`, Status Inactive, Product Name equal to Concat Product Name. Workflows on, as Grace chose. Checked field by field. Details and mapping in `products/duplicate-rc-bali-to-ferry-crew-bali/`.

- Created 51 products under 8 Easy Wedding Crew Bali - IDR (`93610000035149380`), codes `IDRCS8-7779` to `IDRCS8-7829`, Inactive. Set Vendor (Cost + Com) (LC) on the 30 singles from the contractor's rate card (18 priced, 12 cleared as not on the card). Corrected nine cells in the pricing sheet (green) and added a rate card tab. Details in `products/duplicate-ferry-crew-bali-to-8-ew-crew-bali/`.

**Changed**
- The Quotes work was first logged in `gracephamew/crew`. It now lives here, and the crew log points to this repo.

**Decisions**
- 8 Easy Wedding Crew Bali costs: the drone add-on is included wherever a product has videographers; products the rate card does not cover have their cost cleared (Grace, 18 Nov 2026).
- Zoho CRM development work lives in `gracephamew/zoho`, separate from the Zoho to Asana automations in `gracephamew/crew`.
- The Account CRN copy is a new, separate workflow rule and function, so no existing rule is changed. Its update suppresses triggers, so other Quotes workflows do not fire from it.
- Bpay Biller Code on Quotes was first a fixed 508572; changed the same day to match the Deal formula, 508572 only when Country (EW) is AU (Grace, 18 Nov 2026).

**Learned**
- The Zoho CRM connector can read and write records, create fields and update CRM variables. It has no tools for Deluge functions, workflow rules, schedules, custom buttons or layout edits.
- Deals hold `Account_CRN` (text, 10) and `Bpay_Biller_Code` (formula). The Deal's Biller Code was 508572 on the DW deal and empty on the UK deals checked.
- Existing Quotes show the new formula field as empty straight after creation.

- The Deal formula is `If(${Deals.Country (EW)}=='AU','508572','')`. The connector cannot edit an existing field, so formula changes are made in Setup.

- COQL with `limit 2000` returned all 480 Quotes in one call. `updateRecords` with `"trigger": []` takes 100 records per call and reports Modified By as Admin EW Zoho.

- `Vendor_Code` (445 of 542, unique) and `Product_Code` (all 7,349, unique, shape vendor code, hyphen, global counter) are clean and safe for file names. Names are not. There is no field linking re-issued products; the SKU counter is not a per-vendor series.
- Products already has image fields: `Record_Image`, `Room_Hero_Image`, `Room_Images` (up to 10 each), `Room_Floor_Plan`. Vendors has `Record_Image`, `Venue_Hero_Images`, `Vendor_Images`.

- Creating a Product with workflows on: Zoho sets Product Code from the SKU, sets Product Active false when Status is Inactive, recalculates AUD and GBP prices, sets Tax by Vendor-Country, and about 4.5 minutes later rewrites Product Name as "code . name". Vendor Tax is mandatory on the DW Services layout. `getRecords` accepts at most 50 fields per call.

- The Studio 8 pricing sheet was 500,000 low on photography (file handling fee left out) and had no drone or video-only lines. "Vendor (Cost + Com) (LC)" is `Original_Charge_LC`. Clearing it makes Zoho set `Cost_Price_LC` to 0 but leaves the AUD cost and unit price.
- A fresh subagent can send prepared create batches from files, which keeps large payloads out of the main session.

**Next**
- Grace installs the function and rule; Claude reads a test Quote back through the connector.
