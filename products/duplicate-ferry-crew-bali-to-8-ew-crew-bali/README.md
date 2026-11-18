# Products: Ferry Easy Wedding Crew Bali - IDR duplicated to 8 Easy Wedding Crew Bali - IDR

Done 18 November 2026 at Grace's request.

## What was asked

1. Duplicate all Ferry Easy Wedding Crew Bali - IDR products to the vendor 8 Easy Wedding Crew Bali - IDR (`93610000035149380`, code `IDRCS8`).
2. Apply Vendor (Cost + Com) (LC) prices from the sheet "Photography Finance - WS Price Studio 8" to the single products. Packages not to be touched.
3. Later the same day Grace pasted the contractor's rate card and asked whether the prices were right; if not, to correct Zoho CRM and the spreadsheet and highlight changed cells in green.

## Result (Fact, read back from Zoho)

- 51 new products, codes `IDRCS8-7779` to `IDRCS8-7829`, Status Inactive, Product Name equal to Concat Product Name. Workflows were on when creating, as for the first duplication.
- 21 packages (`IDRCS8-7779` to `IDRCS8-7799`): costs copied from Ferry, unchanged.
- 30 single products (`IDRCS8-7800` to `IDRCS8-7829`): Vendor (Cost + Com) (LC) (`Original_Charge_LC`) and Cost Price (LC) (`Cost_Price_LC`) set from the contractor's rate card.
- `mapping.csv` lists every Ferry product, its copy and the costs.

## The contractor's rate card (shooting only, IDR)

| Service | 4 h | 6 h | 8 h | 10 h | 12 h |
|---|---|---|---|---|---|
| Photography, 2 photographers | 4,500,000 | 6,000,000 | 7,500,000 | 9,000,000 | 10,500,000 |
| Videography, 2 videographers | 4,500,000 | 6,000,000 | 7,500,000 | 9,000,000 | 10,500,000 |
| Content creation, 1 content creator | 1,500,000 | 2,000,000 | 2,500,000 | 3,000,000 | 3,500,000 |
| Optional drone with videography (add) | 1,500,000 | 1,750,000 | 2,000,000 | 2,250,000 | 2,500,000 |

Family photography, 1 photographer: 1 hour 2,000,000; 2 hours 3,000,000. Photography and videography rates each include 500,000 for same-night file handling and upload.

## Were the first prices right?

No. The sheet's top block was first applied to 16 singles. Compared with the rate card it was 500,000 low on photography (it left out the file handling fee), it priced two videographers the same as two photographers with no drone, and its content add-on was one step too high. The sheet had no line at all for content-only or video-only products.

## Decisions Grace made

- Drone: include the drone add-on in the cost wherever the product has videographers.
- Products the rate card does not cover: set the cost to empty.

## How each single product was priced

| Products | Cost (IDR) | Basis |
|---|---|---|
| 6, 8, 10 Hours - 2 Photographers (8 Hours exists twice) | 6.0m, 7.5m, 9.0m | Photography |
| 6, 8, 10 Hours - 2 Videographers | 7.75m, 9.5m, 11.25m | Videography + drone |
| 6, 8, 10 Hours - 2 Photographers, 2 Videographers | 13.75m, 17.0m, 20.25m | Photography + videography + drone |
| 6, 8, 10 Hours - 2 Photographers, 2 Videographers, 1 Content Creator | 15.75m, 19.5m, 23.25m | Photography + videography + drone + content |
| 4, 6, 8, 10 Hours - 1 Content Creator (6 Hours exists twice) | 1.5m, 2.0m, 2.5m, 3.0m | Content |

Cost cleared (not on the rate card), 12 products: the six with 1 videographer (`IDRCS8-7815` to `7817`, `7825` to `7827`); 1 Hour - 2 Photographers, 1 Videographer (`7800`); 1 Hour - 2 Photographers, 1 Videographer, 1 Content Creator (`7828`); 2 Hours - 2 Photographers (`7821`); Content Creator 1, 2 and 3 hours (`7805` to `7807`).

On those 12, Zoho shows Vendor (Cost + Com) (LC) empty and Cost Price (LC) 0. The AUD Cost Price and Unit Price still hold the figures copied from Ferry; Zoho did not clear them.

## Spreadsheet changes (green cells)

Sheet `1_Pj72StD1dfa3-dUlM2IDnHPWMTnxzwMOE7py5HWPEA`, tab "Internal Pricing", column H:

| Cell | Row | Was | Now |
|---|---|---|---|
| H21, H23, H25 | 2 Photographers 6, 8, 10 h | 5.5m, 7.0m, 8.5m | 6.0m, 7.5m, 9.0m |
| H42, H44, H46 | 2 Photographers, 2 Videographers | `=H21*2` and so on (11.0m, 14.0m, 17.0m) | `=H21*2+1750000`, `=H23*2+2000000`, `=H25*2+2250000` (13.75m, 17.0m, 20.25m) |
| H49, H51, H53 | plus 1 Content Creator | `=H42+2500000`, `+3000000`, `+3500000` | `=H42+2000000`, `=H44+2500000`, `=H46+3000000` (15.75m, 19.5m, 23.25m) |

New tab "8 EW Crew Bali rate card": the rate card and the 30 single products with their costs; the "Now in Zoho" column is green.

Not highlighted but changed in value, because their formulas depend on H21, H23 and H25: the 1-videographer rows H28, H30, H32 (now 8.75m, 11.0m, 13.25m) and H35, H37, H39 (now 11.25m, 14.0m, 16.75m). The rate card does not price one videographer, so these figures are not confirmed by the contractor.

Not changed: the 1-photographer rows, the editing fee rows and the lower block labelled Ferry Tjoe Photography.

## Things to know

1. Packages under the new vendor still carry Ferry's costs.
2. The rate card also gives 4-hour and 12-hour rates and family photography. No products exist for those yet.
3. Product Name is in the "•" form, set after Zoho's delayed action. An edit in Zoho is expected to turn it back to "code . name".
4. Zoho's workflows recalculated the AUD cost from the new IDR cost. Unit Price stayed at the fixed AUD price where one is set.
5. To undo: delete the 51 products in `mapping.csv`.
