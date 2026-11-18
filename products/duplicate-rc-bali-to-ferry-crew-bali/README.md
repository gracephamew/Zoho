# Products: RC Bali - IDR duplicated to Ferry Easy Wedding Crew Bali - IDR

Done 18 November 2026 at Grace's request.

## What was asked

Duplicate every product of "RC Indonesia - IDR" to the vendor Ferry Easy Wedding Crew Bali - IDR (`93610000035108815`, code `IDRCFJ`). Product Code to come from the SKU, Product Name to be a copy of Concat Product Name, Status set to Inactive.

## Answers Grace gave during the work

- Source vendor: there is no "RC Indonesia - IDR". Grace confirmed RC Bali - IDR (`93610000026193080`, code `IDRCBL`), all 51 products.
- Names: keep the text "RC Bali" in the product names.
- Workflows: on when creating the copies.
- Vendor Tax: 29 source products had no Vendor Tax, which Zoho requires. Grace chose ID-IDR (Tax Free), the same as the other RC Bali products.

## Result (Fact, read back from Zoho after the work)

- 51 new products under Ferry Easy Wedding Crew Bali - IDR, one for each RC Bali product. The RC Bali products were not changed.
- Product Codes `IDRCFJ-7728` to `IDRCFJ-7778`. Each equals its SKU.
- Product Name equals Concat Product Name on all 51, for example `IDRCFJ-7728 • RC Bali - Photography 2 Hours - 1 photographer`.
- Status is Inactive on all 51.
- `mapping.csv` lists each source product and its copy.

## What Zoho's workflows changed on the copies

Workflows ran on create, as chosen. Compared with the source products they changed:

| Field | Change | Copies affected |
|---|---|---|
| Product Code | Set from the SKU automatically | 51 |
| Product Active | Set to false | 22 (the others were already false) |
| Tax and Taxable | "AU GST - 10.0 %" and taxable became "AU GST Free - 0.0 %" and not taxable | 45 |
| Cost Price (AUD) | Recalculated from the IDR cost at today's rate | 50 |
| Unit Price (AUD) | Recalculated | 36 |
| Unit Price GBP | Recalculated | 39 |
| FX Rate Vendor GBP | Updated to today's rate | 51 |
| Product Type and Usage Unit | Filled with Service and Each where empty | 29 |

- View: the tax change follows the vendor's country. The 45 source products carry Vendor-Country "Australia" and AU GST 10%; the 6 that carry "Indonesia" are GST Free. The copies carry "Indonesia" (the new vendor's country), so Zoho made all 51 GST Free. Unverified: the workflow itself has not been seen.
- Fact: Zoho Books UK Product ID is empty and Zoho Books UK Item Sync is false on all 51. Unverified: whether any item was created in Zoho Books (AU); the connector cannot see Books from here.

## Fields set differently from the source on purpose

- Vendor Name: the new vendor.
- Vendor Code `IDRCFJ`, Vendor "Ferry Easy Wedding Crew Bali", Manufacturer "IDRCFJ - Ferry Easy Wedding Crew Bali - IDR", Vendor-Country "Indonesia".
- Status Inactive.
- Vendor Tax ID-IDR (Tax Free) on the 29 that had none.

Everything else was copied as it was, including Add-on Options on `IDRCFJ-7729`, which still names the old codes `AUEWINC-6177` and `AUEWRC-6196`.

## Things to know

1. The Product Name will not stay in the "•" form. An existing Zoho workflow rewrites Product Name as "code . name". It did so about four and a half minutes after each copy was created, and the names were then set again without triggering workflows. Unverified but likely: the next time anyone edits one of these products in Zoho, the name returns to "code . name".
2. Subforms (Add-on Products, Bundled Products) were empty on all 51 source products, so there was nothing to copy.
3. Images were empty on all 51 source products.
4. To undo: delete the 51 products listed in `mapping.csv` (they go to the recycle bin).

## Files

- `mapping.csv`: source product and its copy.
- `source_products_snapshot.json`: the 51 RC Bali products as read before the work.
- `create_payloads.json`: what was sent to Zoho for each copy.
