# Image file naming for Vendors and Products: options for review

Prepared 18 November 2026 for Grace. Status: options only, nothing decided, nothing built.

## Goal

Images are hosted in an S3 bucket and must show in Zoho CRM and in Canvas views. Each image file needs a name that ties it to one Vendor or one Product, and that keeps several images of the same thing together and in order.

Three kinds of image:

- Venue pictures: resort, restaurants, events, views and facilities, general accommodation.
- Supplier pictures: examples of their work, normally from weddings.
- Product pictures: for accommodation, the room, bathroom and view.

## What the data shows (read-only check of Zoho CRM, 18 Nov 2026)

All Fact unless marked. 542 Vendors and 7,349 Products were read.

Vendors:

- `Vendor_Code` is the stable key. 445 of 542 Vendors have one, with no duplicates. All are uppercase letters and digits only, mostly 6 characters, for example `INAYST` (Ayana Estate), `IDGMK7` (Gusmank).
- Every Active Venue (199), Active Supplier (200) and Contractor (7) has a code.
- 97 Vendors have no code, including 10 Venues and 7 Suppliers (for example Zoëtry Mallorca, Four Seasons Resort Mallorca At Formentor, The Tamarind Resort Nusa Lembongan). 95 of the 97 also have no Status.
- Vendor names are not safe for file names: nearly all contain spaces and a currency suffix such as "- IDR", and 21 contain an ampersand.

Products:

- `Product_Code` (the SKU) is the stable key. Every Product has one, with no duplicates. The shape is vendor code, a hyphen, then a number, for example `INAYST-5118`.
- The number is one running counter across the whole database (100 to 7727). It is not a series within a vendor and it does not group versions of the same product.
- The vendor part of the SKU equals the Vendor's current code on 7,286 Products (99.1%). It is fixed when the Product is created and does not change if the Product moves to another Vendor (50 `AUEWINC-` Products now sit under RC Bali, `IDRCBL`).
- 10 Products have a SKU with no vendor part, for example `-2866`. These are tests, placeholders or Products of a Vendor with no code.
- There is no field that links versions of the same product. Re-issues are new Products with new SKUs: 387 groups (805 Products) share a name once the year is removed.
- Accommodation: 1,054 Products carry an accommodation category, 983 active, under 68 Vendors. Only 477 of them are actual rooms (those with `Room_Name` filled). The rest are meal plans, rollaway beds and similar.
- Product and room names are not safe for file names (spaces, brackets, slashes, ampersands, accents).

Existing Zoho image fields:

- Vendors: Vendor Image (`Record_Image`, 1 image), Venue Hero Images (`Venue_Hero_Images`), Vendor Images (`Vendor_Images`).
- Products: Product Image (`Record_Image`, 1 image), Room Hero Image (`Room_Hero_Image`, up to 10), Room Images (`Room_Images`, up to 10), Room Floor Plan (`Room_Floor_Plan`, 1 file).
- Unverified: how many of these fields already hold images, and the size limit of the two Vendor image fields.

## Rules common to every option

1. The key is always the Zoho code, copied exactly: `Vendor_Code` for Vendor images, `Product_Code` for Product images. Never the name.
2. Segments are joined with an underscore. The hyphen is kept only inside the SKU, where it already is.
3. Codes stay in capitals as in Zoho. Everything else is lower case. S3 treats `A` and `a` as different, so one rule avoids near-duplicates.
4. The series number is two digits with a leading zero: `01`, `02` up to `99`. It gives the display order.
5. Number `01` is the lead (hero) image of its group.
6. Letters, digits, underscore and hyphen only. No spaces, brackets, ampersands or accents.
7. Extension in lower case: `.jpg`, `.png` or `.webp`.
8. Replacing an image keeps the same name. A new image takes the next free number. Numbers are not re-used for a different picture.

## Option 1: code and series only

The shortest names. The category is not in the name.

| Kind | Pattern | Example |
|---|---|---|
| Venue or Supplier | `<VendorCode>_<nn>` | `INAYST_01.jpg`, `INAYST_02.jpg`, `IDGMK7_01.jpg` |
| Product | `<ProductCode>_<nn>` | `INAYST-5118_01.jpg`, `INAYST-5118_02.jpg` |

- For: very simple, nothing to classify, hard to get wrong.
- Against: the name does not say whether a venue picture is the restaurant or the pool, so the automation cannot send pictures to different Zoho fields or Canvas sections. Order within a venue has to be managed by hand across all picture types.

## Option 2: code, short type word and series (recommended)

One short word says what the picture shows. Each type has its own series.

| Kind | Pattern | Type words |
|---|---|---|
| Venue | `<VendorCode>_<type>_<nn>` | `resort`, `dining`, `event`, `facility`, `accom` |
| Supplier | `<VendorCode>_work_<nn>` | `work` |
| Product, accommodation | `<ProductCode>_<type>_<nn>` | `room`, `bath`, `view`, `plan` |
| Product, other | `<ProductCode>_item_<nn>` | `item` |

Examples:

- `INAYST_resort_01.jpg`, `INAYST_dining_01.jpg`, `INAYST_event_03.jpg`, `INAYST_facility_02.jpg`, `INAYST_accom_01.jpg`
- `IDGMK7_work_01.jpg` to `IDGMK7_work_12.jpg`
- `INAYST-5118_room_01.jpg`, `INAYST-5118_bath_01.jpg`, `INAYST-5118_view_01.jpg`, `INAYST-5118_plan_01.jpg`
- `IDMIMP-7297_item_01.jpg`

- For: a file still explains itself when it is outside its folder. The automation can route by type, for example `room_01` to Room Hero Image and the rest to Room Images. Sorting by name groups the types together.
- Against: someone has to choose the type word when naming. The list of words must be fixed and kept short.

## Option 3: code, readable name, type and series

As option 2, with a readable name added for people and for search engines.

| Kind | Pattern | Example |
|---|---|---|
| Venue | `<VendorCode>_<vendor-name>_<type>_<nn>` | `INAYST_ayana-estate_resort_01.jpg` |
| Supplier | `<VendorCode>_<vendor-name>_work_<nn>` | `IDGMK7_gusmank_work_01.jpg` |
| Product | `<ProductCode>_<product-name>_<type>_<nn>` | `INAYST-5118_one-bedroom-suite-resort-view_room_01.jpg` |

- For: readable without looking the code up. Useful if the same files are later shown on a public website.
- Against: long names. If a Vendor or Product is renamed, either the files are renamed or the name goes stale. Names need cleaning by rule (839 Product names contain an ampersand, 3,021 contain brackets). The code already identifies the record, so the name adds no accuracy.

## Option 4: folders carry the meaning, short file names

The S3 folder path says what the picture is. The file name is just the series, or the series with the type.

```
vendors/INAYST/resort/01.jpg
vendors/INAYST/dining/01.jpg
vendors/IDGMK7/work/01.jpg
products/INAYST/INAYST-5118/room/01.jpg
products/INAYST/INAYST-5118/bath/01.jpg
```

- For: tidy to browse, easy to list everything for one Vendor, and easy to set access by folder.
- Against: a file downloaded or emailed on its own is just `01.jpg` and has lost its meaning. Moving a file to the wrong folder silently changes what it is.

## Recommended combination

View: use option 2 names inside a simple version of the option 4 folders. The folder makes browsing easy, and the name stays meaningful anywhere.

```
vendors/<VendorCode>/<VendorCode>_<type>_<nn>.jpg
products/<VendorCode>/<ProductCode>/<ProductCode>_<type>_<nn>.jpg
```

Examples:

```
vendors/INAYST/INAYST_resort_01.jpg
vendors/IDGMK7/IDGMK7_work_01.jpg
products/INAYST/INAYST-5118/INAYST-5118_room_01.jpg
products/INAYST/INAYST-5118/INAYST-5118_bath_01.jpg
```

The vendor folder under `products/` is taken from the front of the SKU, not from the Product's current Vendor, so it never changes.

## Questions that affect the choice

1. Re-issued products. A new price year creates a new Product with a new SKU, so its pictures would be named for the old SKU. Options: (a) copy the pictures under the new SKU each time; (b) name room pictures by room instead of by SKU, for example `INAYST_room-<room number>_...`, which needs a new stable room identifier in Zoho; (c) stop re-issuing and update the existing Product. View: (a) is the least change now; (b) is cleaner long term for rooms.
2. Vendors with no code. 10 Venues and 7 Suppliers need a Vendor Code before they can have pictures.
3. The 10 Products whose SKU has no vendor part. They should be fixed or left out.
4. Which Products get pictures. Suggested start: the 447 active room Products (accommodation category with Room Name filled), then other Products as needed.
5. Zoho field limits. Room Images and Room Hero Image hold up to 10 pictures each. If a room has more than 10 pictures in S3, a rule is needed for which ones go to Zoho (for example numbers 01 to 10).
6. One master size, or also smaller copies. If smaller copies are wanted, add a size word at the end, for example `_thumb`, rather than a second name.
7. Whether the same files will ever be public (website, brochures). If yes, option 3 becomes more attractive.

## Sample

`sample-option-2.csv` shows option 2 on real records: 10 Venues, 20 Suppliers and 185 of their active Products (70 rooms), with the S3 folder and example file names for each. The file names are examples only; no such files exist yet.

## Not done

- No files have been renamed and nothing has been changed in Zoho or S3.
- The S3 bucket has not been seen, so current file names and folder layout are unknown.
