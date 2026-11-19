# Context links for a new Claude project (Zoho CRM work)

Written 19/11/2026 18:18 (Melbourne). No secrets are in this file; credentials stay in the claude.ai connector settings.

## Read first, in this order

1. Main guide (rules, how Zoho work is done, connections): https://github.com/gracephamew/Zoho/blob/claude/optimistic-mayer-468mga/CLAUDE.md
2. Progress log (current status, open items 1 to 16): https://github.com/gracephamew/Zoho/blob/claude/optimistic-mayer-468mga/Progress-Log.md
3. Project instructions master copy: https://github.com/gracephamew/Zoho/blob/claude/optimistic-mayer-468mga/Project-Instructions.md
4. Shared project memory index `MEMORY.md` (how the org behaves, written by the Cowork project "Zoho CRM aa"): https://drive.google.com/file/d/1nci1JNbsX71MXT0-sE1rAv5NyvmygY34/view

## Where the work lives

| Item | Link |
|---|---|
| GitHub repo (private). All work is on the branch `claude/optimistic-mayer-468mga`; there is no main branch yet | https://github.com/gracephamew/Zoho/tree/claude/optimistic-mayer-468mga |
| Drive project folder `Claude Projects/Zoho-CRM-Development` (same files as the repo; file ids change on each update, so use the folder) | https://drive.google.com/drive/folders/1Whk7uE8Rpefd0lEgAZcx5atNc8maA4PN |
| Drive shared memory `_Claude_Project_Memory` (103 files on 19/11/2026) | https://drive.google.com/drive/folders/1VqlBSUs31NADNdBeJ9gaj9_SbIFH9GX5 |
| Drive root `Claude Projects` | https://drive.google.com/drive/folders/1OhGfDEYkZ29MH2tY7BCbvcbjxPOfB_jG |
| Sister project, Zoho to Asana automations (repo `gracephamew/crew`) | https://github.com/gracephamew/crew |

## Work folders (repo)

| Work | Link |
|---|---|
| Purchase Orders: PO to Vendor Bills repair (Phase 1 findings, wiring map, defect lists) | https://github.com/gracephamew/Zoho/blob/claude/optimistic-mayer-468mga/purchase-orders/po-bill-repair/README.md |
| The six live functions, as pasted 19/11/2026 | https://github.com/gracephamew/Zoho/tree/claude/optimistic-mayer-468mga/purchase-orders/po-bill-repair/live-code-2026-11-19 |
| Quotes: Bpay Biller Code and Account CRN from the Deal (function not yet installed) | https://github.com/gracephamew/Zoho/tree/claude/optimistic-mayer-468mga/quotes/biller-code-account-crn |
| Images: S3 file naming options | https://github.com/gracephamew/Zoho/tree/claude/optimistic-mayer-468mga/images/naming-structure |
| Images: S3 widget outline | https://github.com/gracephamew/Zoho/tree/claude/optimistic-mayer-468mga/images/s3-widget |
| Products: RC Bali to Ferry Easy Wedding Crew Bali | https://github.com/gracephamew/Zoho/tree/claude/optimistic-mayer-468mga/products/duplicate-rc-bali-to-ferry-crew-bali |
| Products: Ferry Crew Bali to 8 Easy Wedding Crew Bali, with rate card costs | https://github.com/gracephamew/Zoho/tree/claude/optimistic-mayer-468mga/products/duplicate-ferry-crew-bali-to-8-ew-crew-bali |

## PO bill repair source documents (Drive memory, 19/11/2026)

| Document | Link |
|---|---|
| Brief for Claude Code | https://drive.google.com/file/d/1-v9qvXeZ1WCCougQh1OKeliRjlDJQ5IP/view |
| PO to Vendor Bills framework | https://drive.google.com/file/d/1-aHa-tWdUSdyTs9_F9QuI2WIfKp-Dy7n/view |
| `CreateVendorPaymentSchedule1` v4 draft code | https://drive.google.com/file/d/10kQBTRfeYkwrbuimDmJoK0vWizM01bVU/view |
| PO record lock note | https://drive.google.com/file/d/1rVdup8YF0IJNU9hQXWFiCVcQvaxaqYg9/view |

## Zoho (production org `7005318614`, AU data centre)

| Item | Link |
|---|---|
| CRM home | https://crm.zoho.com.au/crm/org7005318614 |
| Functions list | https://crm.zoho.com.au/crm/org7005318614/settings/functions |
| Function `93610000004305171` (name not confirmed) | https://crm.zoho.com.au/crm/org7005318614/settings/functions?viewId=93610000001436653&functionId=93610000004305171&tab=overview |
| Function `93610000004897390` (name not confirmed) | https://crm.zoho.com.au/crm/org7005318614/settings/functions?viewId=93610000001436653&functionId=93610000004897390&tab=overview |
| Workflow rule `SyncBillsToZohoBooks` | https://crm.zoho.com.au/crm/org7005318614/settings/workflow-rules/93610000004897404 |
| Workflow rule "Create Vendor Payment Schedule" (id `93610000004176388`; link built from the id, not opened) | https://crm.zoho.com.au/crm/org7005318614/settings/workflow-rules/93610000004176388 |
| Vendor: Ferry Easy Wedding Crew Bali - IDR | https://crm.zoho.com.au/crm/org7005318614/tab/Vendors/93610000035108815 |
| Vendor: 8 Easy Wedding Crew Bali - IDR (link built from the id) | https://crm.zoho.com.au/crm/org7005318614/tab/Vendors/93610000035149380 |
| Zoho Books org ids | AU `7005347798`; UK `20112648963` (not reachable by the connector) |

## Other

| Item | Link |
|---|---|
| Pricing sheet "Photography Finance - WS Price Studio 8" | https://docs.google.com/spreadsheets/d/1_Pj72StD1dfa3-dUlM2IDnHPWMTnxzwMOE7py5HWPEA/edit |

## Connectors the new project needs switched on

Zoho CRM, Zoho Books, Google Drive, Google Sheets, GitHub (repo `gracephamew/Zoho`). For writing functions in Setup: a browser on Grace's computer (Claude in Chrome, or the desktop app).

## Skills to use

`zoho-deluge-function` (before any Deluge), `technical-project-writing-style`, `zoho-crm-aa-style`.
