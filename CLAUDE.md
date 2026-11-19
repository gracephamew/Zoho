# CLAUDE.md: Zoho CRM Development

Main guide for Claude working in this repository. Read this first.

Last updated 19 November 2026.

---

## 1. What this project is

Development work inside the Easy Weddings (Destinations by Easy Weddings) Zoho CRM: Deluge functions, workflow rules, schedules, custom buttons, fields and data fixes.

The Zoho to Asana automations (Crew Package to Asana, Alec's Social Content) are a separate project in `gracephamew/crew`.

| Piece of work | Folder | Status |
|---|---|---|
| Quotes: Bpay Biller Code and Account CRN from the Deal | `quotes/biller-code-account-crn/` | Fields created 18 Nov 2026. Copy function written, not yet installed in Zoho. |
| Images: S3 file naming for Vendor and Product pictures | `images/naming-structure/` | Options written 18 Nov 2026. Awaiting Grace's choice. |
| Images: widget to show S3 pictures on Vendor and Product pages | `images/s3-widget/` | Outline written 18 Nov 2026. Not started. |
| Products: RC Bali - IDR duplicated to Ferry Easy Wedding Crew Bali - IDR | `products/duplicate-rc-bali-to-ferry-crew-bali/` | Done 18 Nov 2026. 51 copies, Inactive. |
| Products: Ferry Easy Wedding Crew Bali duplicated to 8 Easy Wedding Crew Bali - IDR, with rate card costs | `products/duplicate-ferry-crew-bali-to-8-ew-crew-bali/` | Done 18 Nov 2026. 51 copies, Inactive. 12 singles have no cost. |
| Purchase Orders: repair the PO to Vendor Bills workflow | `purchase-orders/po-bill-repair/` | Context loaded 19 Nov 2026. Waiting for Grace to approve Phase 1 (read-only). No changes made. |

## 2. Where things are

| Item | Location |
|---|---|
| Working environment | This repository, `gracephamew/zoho` (private), from 18 Nov 2026 |
| Project folder (Drive) | `Claude Projects/Zoho-CRM-Development/`, https://drive.google.com/drive/folders/1Whk7uE8Rpefd0lEgAZcx5atNc8maA4PN |
| Progress log | `Progress-Log.md` here and in the Drive project folder |
| Project instructions master copy | `Project-Instructions.md` here and in the Drive project folder |
| Shared project memory (Drive) | `_Claude_Project_Memory`, https://drive.google.com/drive/folders/1VqlBSUs31NADNdBeJ9gaj9_SbIFH9GX5 (103 files on 19 Nov 2026). Index: `MEMORY.md`, file id `1nci1JNbsX71MXT0-sE1rAv5NyvmygY34` |

One folder per piece of work, grouped by module (for example `quotes/`, `deals/`). Each folder holds a `README.md` with what was found, what was changed, and the code.

## 3. How Zoho work is done

The Zoho CRM connector can:

- read, search, query (COQL), create, update, upsert and delete records in any module;
- read and write related records, notes and tags;
- read field metadata and create custom fields;
- read modules, layouts and related lists (no edits);
- read and update CRM variables.

It has no tools for Deluge functions, workflow rules, schedules, custom buttons, blueprints or layout edits. For those:

1. Claude reads the live modules, fields and layouts by ID, so the code matches the org.
2. Claude writes the Deluge code and the exact setup steps, and saves both in the work folder.
3. Grace pastes the code into Zoho CRM Setup and saves. Compile errors come back to Claude.
4. Claude checks the result by reading the affected records through the connector.

## 3a. Shared project memory

The Cowork project "Zoho CRM aa" keeps its memory in the Drive folder `_Claude_Project_Memory`. It is written by other Claude sessions and is the source of truth for how this org behaves. The desktop memory is the master; the Drive folder is its exported copy plus files added by cloud chats.

1. Before starting a piece of Zoho work, read `MEMORY.md` (the index) and then the files it points to for that topic.
2. Do not copy memory files into this repo. Write a short digest in the work folder and cite the Drive file ids.
3. Do not edit or delete files in that folder unless Grace asks. New findings from this repo go in the work folder README and the progress log.
4. Files dated after the index (12 Nov 2026) are not in `MEMORY.md` yet. List the folder by modified time to find them.

Standing facts from the memory that apply to all work here:

- A live workflow rewrites Taxable and Tax on every Product save. Use an empty trigger list to keep a value.
- A vendor with no Vendor Code produces products with no code prefix.
- `Zoho_Books_Bill_Id` is the only trustworthy sign that a Vendor Bill is synced. CRM payment fields are a cache; for money, ask Zoho Books.
- The dev-test deal `12aa34bb` is not sandboxed. Its writes reach the live Books org.
- Deluge: run the `zoho-deluge-function` skill before writing any function; every new version carries a write-set diff and removes nothing without Grace's approval.
- Never name an invoice by Subject alone; regenerated plans reuse Subjects. The instalment plan matches deposits by Subject text, so renaming an invoice can change what a couple is billed.
- A dead scheduled run can leave a false record. Read the artefact back before recording that it was written.

## 4. Rules

1. Production org. Read first. Ask Grace before any write that changes live records, fields or settings, unless she has asked for that exact change.
2. Never write secrets into code, docs, chat or Drive. Record only where they are kept.
3. Identify modules, fields, vendors and products by ID or API name, never by label alone.
4. Before changing an existing workflow or function, get its current criteria and code, and keep the previous version in the work folder.
5. Writing for Grace: Australian spelling, full sentences, no em dashes, label what is Fact, View or Unverified, give options for open choices.
6. Start every reply with the date and Melbourne time, for example `18/11/2026 10:50 (Melbourne)`.
7. Save everything produced to the Drive project folder as well as this repo. To update a Drive file, upload the new version and move the old one to the bin, so there is one current copy.
8. When real work is done, or someone asks to "update the progress log", add to today's dated section of `Progress-Log.md` (chats with start time, who and topic; Done; Changed; Decisions; Learned; Next). Keep "Current status" and "Open items" at the top current. Update both the repo and Drive copies.
9. Keep the Connections list below dated and current.

## 5. Connections

| System | Used by | Account / ID | Credentials kept in | Checked |
|---|---|---|---|---|
| Zoho CRM (AU DC) | Claude sessions, through the Zoho CRM connector | Easy Weddings Travel Pty Ltd, org `7005318614`, production | claude.ai connector settings | 18 Nov 2026, connected |
| Google Drive connector | Claude sessions | Project folder above | claude.ai connector settings | 18 Nov 2026, connected |
| Google Sheets connector | Claude sessions | Pricing sheet `1_Pj72StD1dfa3-dUlM2IDnHPWMTnxzwMOE7py5HWPEA` (Photography Finance - WS Price Studio 8) | claude.ai connector settings | 18 Nov 2026, connected |
| Zoho Books (AU `7005347798`; UK `20112648963`, not reachable) | Claude sessions, through the Zoho Books connector | Easy Weddings | claude.ai connector settings | 19 Nov 2026, connector listed, not tested |
| Zoho Analytics, Zoho Projects connectors | Claude sessions | Easy Weddings | claude.ai connector settings | 19 Nov 2026, connectors listed, not tested |
| GitHub | This repo | `gracephamew/zoho` (private) | claude.ai GitHub connection | 18 Nov 2026 |

Note: the Zoho CRM licence (Zoho One Enterprise, 18 users) shows a paid expiry of 24 Nov 2026 (checked 18 Nov 2026).
