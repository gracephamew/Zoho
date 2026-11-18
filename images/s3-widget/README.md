# Showing S3 images in Zoho CRM with a widget: outline of the work

Prepared 18 November 2026 for Grace. Status: outline only. Nothing built, nothing changed in Zoho or S3.

This is "option 3" from the display options: a small custom widget on the Vendor and Product pages that loads pictures live from S3, so S3 stays the only copy.

Most of the Zoho detail below is from knowledge of the platform, not from a test in this org. Treat it as Unverified until step 6.

## What the widget would do

1. Open on a Vendor or Product record.
2. Read the record's code (`Vendor_Code` or `Product_Code`).
3. Work out the S3 folder from the code, using the agreed naming structure.
4. Show the pictures as a gallery, grouped by type (resort, dining, room, bath and so on), with a click to enlarge.

## The limit to know first

- A widget shows on the record page, as a related list or behind a button.
- View: it will not fill Zoho's own image fields, so Canvas image elements, list views, Quote templates and PDFs would stay empty.
- Unverified: whether a widget can be placed inside a Canvas record page in this org.
- View: because images must appear in Canvas, the likely answer is a hybrid. The lead image (`_01`) is copied into the Zoho image field for Canvas, and the widget shows the full gallery from S3.

## Steps

### 1. Decisions (Grace)

- Naming structure (option 2 is recommended; see `../naming-structure/README.md`).
- Whether pictures are readable by a plain link or private.
- Where the gallery appears: Products, Vendors or both.
- Whether to use the hybrid for Canvas.

### 2. S3 and AWS setup (whoever manages AWS)

- Bucket and folders laid out as `vendors/<VendorCode>/` and `products/<VendorCode>/<ProductCode>/`.
- Read access for the widget: either public read through a web address (ideally CloudFront), or private with signed links, which needs a small service to issue them.
- A way for the widget to know which files exist. A browser cannot list a bucket safely. Options:
  - a. a small `index.json` file in each folder, written whenever pictures are uploaded (recommended);
  - b. a small web service that lists a folder on request;
  - c. a text field on the record holding the file names;
  - d. the widget tries `_01`, `_02` and so on until one is missing (simple, but slow and limited).
- Cross-origin (CORS) setting on the bucket so Zoho pages may read the index file.

### 3. Zoho preparation (Claude through the connector, with Grace's go-ahead)

- Confirm the fields the widget reads: `Vendor_Code`, `Product_Code`, `Room_Name`.
- Hold the S3 base address in one place, for example a CRM variable, so it is not written into the widget.
- Give the 10 Venues and 7 Suppliers with no Vendor Code a code.

### 4. Build the widget (Claude)

- A small web page (HTML, CSS, JavaScript) using Zoho's widget kit to read the open record.
- Gallery grouped by type, lead image first, click to enlarge, a clear message when a record has no pictures.
- Packaged as a zip for Zoho's internal hosting. Unverified: whether the package can be produced here without Zoho's command-line tool; if not, Grace or a developer runs one packaging command.

### 5. Install (Grace, in Zoho Setup)

- Setup, Developer Hub, Widgets: create the widget, hosting Internal, upload the zip.
- Add it to the Products and Vendors layouts as a related list (or a button).
- Give the right profiles access.

### 6. Test

- One Venue, one Supplier and one room Product with a handful of real pictures in S3.
- Check: record page, Canvas view, mobile app, a user with a standard profile, a record with no pictures.
- This is where the Canvas question is settled.

### 7. Hybrid step for Canvas, if needed (Claude writes, Grace installs)

- A Deluge function that copies the lead image from S3 into the Zoho image field (`Record_Image`, `Room_Hero_Image` or `Venue_Hero_Images`).
- Run by a button or schedule, and when pictures change.

### 8. Roll out

- Load pictures for the first group (suggested: the 447 active room Products and their Venues).
- Write a short guide for staff on naming and uploading pictures.
- Record the widget, the bucket and where credentials are kept in the Connections list.

## What is needed from outside this session

- Someone with access to the AWS account.
- Grace (or an admin) for Setup steps in Zoho, since the connector cannot install widgets or edit layouts.

## Rough size

View, not a quote: steps 3 to 6 are a few working sessions once the S3 decisions in step 2 are made. Step 2 depends on who manages AWS.
