# Product Object Model

## Overview

**Current version:** 0.1.1

**Logs:**

- 2020-08-03: **Added** course bundle information.
- 2020-02-26: **Updated** `platform` to be a map and **changed** `product_id` to be `external_id` to cover all possible platform ID options (e.g. Product ID, Course ID, User ID (RCassidy)).
- 2020-02-12: **Updated** the timestamp format to a standard format [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339). **Updated** the `format_type` options. **Added** DB mappings for option names.
- 2020-02-11: **Changed** the type for the product price fields and the weight field from `float` to `string`.
- 2019-11-14: **Updated** the structure of options within the platform key to allow for "custom" options (`option1`, `option2`, `option3`).
- 2019-11-06: [Major Change!] Modified the entire structure to be favorable for CH. Added an initial mapping of CH --> Trellis' Shopify doc.
- 2019-10-22: **Removed** the product `id` as no longer needed. **Changed** `SKU` to be the primary key.
- 2019-10-09: **Added** `product_format` to take the place of `option` as holding the product type/format information.
- 2019-07-17: [Major Change!] Changed `name` to `title` and ADDED / MODIFIED a lot of data points.
- 2019-07-16: **Added** `subtitle`, `image_link`, and `thumbnail_link`. **Changed** `updated_at` to `last_modified` and `author` type to _(array of objects)_ to include author `url`.
- 2019-07-01: Created. Updated the TODOs.

## Purpose

The Product Object Model is the representation of the products within the ecommerce platform. It holds a copy of the ecommerce data (plus additional data points needed from a business perspective).

## Definitions

- `sku` _(string)_ - The unique identifier for the product (e.g. "BK05536F").
- `group_id` _(string)_ - The unique identifier (ID or SKU) for the parent product for the product, if applicable (e.g. "123456789" or SKU).

- `title` _(string)_ - The title of the product (e.g. "Alphabreaths")
- `subtitle` _(string)_ - The subtitle of the product (e.g. "The ABCs of Mindful Breathing")
- `body_html` _(string)_ - A description of the product. Supports HTML formatting.
- `format_type` _(string)_ - The format type for the product. Options are:
	- `FORMAT_UNKNOWN` (DB: `0`)
	- `FORMAT_HARD_BOOK` (DB: `1`) - _hard-cover book_
	- `FORMAT_EBOOK` (DB: `2`)
	- `FORMAT_VIDEO` (DB: `3`)
	- `FORMAT_AUDIO` (DB: `4`)
	- `FORMAT_MUSIC` (DB: `5`)
	- `FORMAT_CE_CREDITS` (DB: `6`)
	- `FORMAT_COURSE` (DB: `7`)
	- `FORMAT_SOFT_BOOK` (DB: `8`) - _paperback book_
	- `FORMAT_CD` (DB: `9`)
	- `FORMAT_DVD` (DB: `10`)
	- `FORMAT_CARD_DECK` (DB: `11`)
	- `FORMAT_ENHANCED_EBOOK` (DB: `12`)
	- `FORMAT_COURSE_BUNDLE` (DB: `13`)
- `published` _(string)_ - String representation of whether this product has been published (e.g. "true")
- `published_at` _(string)_ -  The date and time ([ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601)) when the product was published. Can be set to null to unpublish the product from the Online Store channel (e.g. "2019-05-28T0:00:00-05:00").

- `discount_price` _(string)_ - The discounted price of the product (e.g. "10.77"),
- `price` _(string)_ - The full price of the product (e.g. "17.95"),
- `inventory_qty` _(int)_ - The available quanity for the product (e.g. `0`),

- `images` _(array of objects)_ - The image(s) for the product. A single image object ontains the following:
	- `src` _(string)_: The full location of the image (e.g. "http://example.com/image1.jpg")
	- `sort_order` _(int)_: The sort order for the image (e.g. `10`)

- `handle` _(string)_ - The relative URI key for the product (e.g. "alphabreaths").
- `meta_title` _(string)_ - The global meta title for the product.
- `meta_description` _(string)_ - The global meta title for the product

- `parent_category` _(string)_ - The category for the product (e.g. "Meditation).
- `sub_category` _(string)_ - The subcategory for the product (e.g. "Mindfulness Meditation")
- `level` _(string)_ - The targeted consumer level of the product. Options are:
	- `LEVEL_UNKNOWN` (DB: `0`)
	- `LEVEL_BEGINNER` (DB: `1`)
	- `LEVEL_INTERMEDIATE` (DB: `2`)
	- `LEVEL_ADVANCED` (DB: `3`)
- `audience` _(string)_ - The targeted consumer audience for the product. Options are:
	- `AUDIENCE_UNKNOWN` (DB: `0`)
	- `AUDIENCE_CHILD` (DB: `1`)
	- `AUDIENCE_PROFESSIONAL` (DB: `2`)
	- `AUDIENCE_NON_PROFESSIONAL` (DB: `3`)
- `duration` _(string)_ - The general duration of the product. Options are:
	- `DURATION_UNKNOWN` (DB: `0`)
	- `DURATION_HOURS` (DB: `1`)
	- `DURATION_DAYS` (DB: `2`)
	- `DURATION_WEEKS` (DB: `3`)
	- `DURATION_MONTHS` (DB: `4`)
	- `DURATION_YEARS` (DB: `5`)

- `authors` _(array of objects)_ - Authors listed (with URLs) for the product. An author object consists of:
	- `name` _(string)_ - The name of the author (e.g. "Christopher Willard"").
	- `url` _(string)_ - The full url location of the author page (e.g. "https://www.soundstrue.com/store/christopher-willard-pysd.html").

- `vendor` _(string)_ - The vendor for the product (e.g "Sounds True").
- `barcode` _(string)_ - The barcode for the product.
- `upc` _(string)_ - The UPC for the product.
- `isbn_10` _(string)_ - The [ISBN-10](https://en.wikipedia.org/wiki/International_Standard_Book_Number) for the product.
- `isbn_13` _(string)_ - The [ISBN-13](https://en.wikipedia.org/wiki/International_Standard_Book_Number) for the product.

- `requires_shipping` _(bool)_ - Whether or not the product requires shipping (e.g. a physical product would be `true`)
- `weight` _(string)_ - The numeric weight measurement for the product (e.g. "12.7").
- `weight_unit` _(string)_ - The unit for the weight measurement.

- `taxable` _(string)_ - Whether or not the product subject to taxes.
- `tax_code` _(string)_ - If `taxable`, this is the tax code for which the product is taxable as (e.g. `DB031013`).

- `equal_buy_links` _(array of objects)_: An array of EBL objects where users can purchase products at other locations. An EBL object contains the following:
	- `name` _(string)_: The display name of the equal buy link.
	- `url` _(string)_: The full URI/URL of the equal buy link.

- `sort_order` _(int)_ - The sort/display order for child products, if applicable (e.g. 10).

- `platform` _(object of embedded objects|map of maps)_ - Specific platform attributes that are needed, or helpful, for the product. Includes the `external_type` and `external_id` options. This key consists of the following:
	- `name` _(string)_: The name of the platform. Options are:
		- `PLATFORM_SHOPIFY`
		- `PLATFORM_THINKIFIC`
		- `PLATFORM_RCASSIDY`
	- The map values consists of the following:
		- `external_id` _(string)_: The platform's identifier of the product, course, user (cecredit_rcassidy) on its system (e.g. `45678902132`).
		- `product_created_at` _(string)_: The date and time ([ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601)) when the product was created in Shopify (e.g. "2012-02-15T15:12:21-05:00",).
		- `product_updated_at` _(string)_: The date and time ([ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601)) when the product was last modified in Shopify (e.g. "2012-08-24T14:01:47-04:00").
		- `options` _(map)_: Custom platform options for SoundsTrue (e.g. `option1`, `option2`, `option3`). Each option consists of:
			- `name` _(string)_: The name of the option.
			- `value` _(string)_: The value of the the option.

- `created_at` _(string)_ - string representation of the initial product entry (creation) [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339) datetime (e.g. "2019-06-10T00:00:00Z").
- `last_modified` _(string)_ - string representation of when the product entry is updated [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339) datetime (e.g. "2019-06-10T00:00:00Z").

- `thinkific_bundle_id` _(string)_ - The ID for bundles that this product contains.
- `thinkific_bundled_courses` _(array of objects)_ - Information for the bundled courses associated to this product.
	- `id` _(int)_: The course ID (e.g. `12345`).
	- `title` _(string)_: The course title (e.g. "Bundled Course Introduction").
	- `slug` _(string)_: The slug (location) of the course (not the full URL) (e.g. "bundled-course-introduction").
	- `sort_order` _(int)_: The order that this course is supposed to be represented in (e.g. `1`).

## Mapping of Datapoints (CH --> Shopify)

Mapping of CH datapoints to [Trellis' Shopify doc](https://docs.google.com/spreadsheets/d/1K0gtopSasF_5EJ5-VAq0Cc3DdZIX3GQgVfLaEfS1grU/edit?usp=sharing).

| Top-level datapoint (CH) | Sub-level datapoint (CH) | Shopify Name (Sheets column) |
|:----------|:-------------|:-------------|
| `sku` | | Variant SKU (R) |
| `parent_sku` | | |
| `title` | | Title (C) |
| `subtitle` | | Metafield (AN) |
| `body_html` | | Body (HTML) (D) |
| `format_type` | | Option1 |
| `published` | | Published (J) |
| `published_at` | | Published At (K) |
| `discount_price` | | Variant Price (AA) |
| `price` | | Variant Compare At Price (AB) |
| `inventory_qty` | | Variant Inventory Qty (W) |
| `images` | | |
| | `src` | Image Src (AG) |
| | `sort_order` | Image Position (AH) <br>* _grayed out in Sheets_ |
| `handle` | | Handle (B) |
| `meta_title` | | Metafields Global Title Tag (S) <br>* _grayed out in Sheets_ |
| `meta_description` | | Metafields Global Description Tag (T) <br>* _grayed out in Sheets_ |
| `parent_category` | | Parent Category (BJ) |
| `sub_category` | | Sub Category (BK) |
| `level` | | Level (BL) |
| `audience` | | Audience (BM) |
| `duration` | | Duration (BN) |
| `authors` | | Metafield (AZ) (includes link) |
| `vendor` | | Vendor (E) |
| `barcode` | | Variant Barcode (AE) |
| `isbn_10` | |  |
| `isbn_13` | |  |
| `requires_shipping` | | Variant Requires Shipping (AC) |
| `weight` | | Variant Weight (AK) |
| `weight_unit` | | Variant Weight Unit (AL) |
| `taxable` | | Variant Taxable (AD) |
| `tax_code` | | Variant Tax Code (AM) <br>* _grayed out in Sheets_ |
| `equal_buy_links` | | Metafield (BB - BI) |
| `sort_order` | | N/A <br>* _internal_ |
| `platform` | | |
| | `name` |  N/A <br>* _internal_ |
| | `product_id` | `id` (_key taken from API_) |
| | `product_created_at` | `created_at` (_key taken from API_) |
| | `product_updated_at` | `updated_at` (_key taken from API_) |
| | `options` <br>-> `name`<br>-> `value` | <br>-> Option1 Name (L)<br>-> Option1 Value (M) |
| `created_at` | | N/A <br>* _internal_ |
| `last_modified` | | N/A <br>* _internal_ |

## Notes / TODOs

- Change the mapping to actual data fields (not Google Sheets).
- The product object model represents our ideal product dataset _within_ the CH ecosystem, not the Shopify system (although, and of course, there is a lot of overlap).

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** Product object._
```jsonc
{
	"sku": "BK05536F",  // primary key
	"group_id": "1234567890",
	
	// general product info
	"title": "Alphabreaths",
	"subtitle": "The ABCs of Mindful Breathing",
	"body_html": "<p>In <em>Alphabreaths</em>, children will learn their ABCs and the basics of mindfulness through playful breathing exercises. Breaths like Mountain Breath and Redwood Breath will connect them with nature, while breaths like Heart Breath and Wish Breath will help them remember to fill their heart with gratitude and send good wishes to others.</p><p>Simple, playful, and with delightful illustrations, <em>Alphabreaths</em> is the perfect introduction to mindfulness and breath awareness</p>",
	"format_type": "FORMAT_BOOKS",
	"published": "true",
	"published_at": "2019-05-28T0:00:00-05:00",

	// price + qty
	"discount_price": "10.77",
	"price": "17.95",
	"inventory_qty": 0,

	// images 
	// TODO add width/height info OR some kind of size/resolution differentiator?
	"images": [
		{
			"src": "http://example.com/image1.jpg",
			"sort_order": 10
		},
		{
			"src": "http://example.com/image2.jpg",
			"sort_order": 20
		}
	],

	// location + meta
	"handle": "alphabreaths",
	"meta_title": "",
	"meta_description": "",
	
	// category info
	"parent_category": "Meditation",
	"sub_category": "Mindfulness Meditation",
	"level": "LEVEL_BEGINNER",
	"audience": "AUDIENCE_NON_PROFESSIONAL",
	"duration": "DURATION_HOURS",
	
	// authors
	"authors": [
		{
			"name": "Christopher Willard",
			"url": "https://www.soundstrue.com/store/christopher-willard-pysd.html"
		},
		{
			"name": "Daniel Rechtschaffen",
			"url": "https://www.soundstrue.com/store/daniel-rechtschaffen.html"
		}
	],

	// vendor + codes (ISBN, etc)
	"vendor": "Sounds True",
	"barcode": "",
	"upc": "",
	"isbn_10": "",
	"isbn_13": "",

	// shipping info
	"requires_shipping": "",
	"weight": "0",
	"weight_unit": "",
	
	// tax
	"taxable": "",
	"tax_code": "",

	// EBL
	"equal_buy_links": [
		{
			"name": "Amazon",
			"url": "https://www.amazon.com/Alphabreaths-Breathing-Christopher-Willard-PsyD/dp/1683641973"
		},
		{
			"name": "Barnes & Noble",
			"url": "https://www.barnesandnoble.com/w/alphabreaths-christopher-willard-psyd/1129070167"
		},
		{
			"name": "IndieBound",
			"url": "https://www.barnesandnoble.com/w/alphabreaths-christopher-willard-psyd/1129070167"
		}
	],

	// sort order for child products
	"sort_order": 10,

	// platform-specific info
	"platform": {
		"PLATFORM_SHOPIFY": {
			"external_id": "156302615654",
			"product_created_at": "2012-02-15T15:12:21-05:00",
			"product_updated_at": "2012-08-24T14:01:47-04:00",
			"options": {
				"option1": {
					"name": "Format",
					"value": "eBook"
				}
			}
		},
		"PLATFORM_THINKIFIC": {
			"external_id": "2345678"
		},
		"PLATFORM_RCASSIDY": {
			"external_id": "80415780952"
		}
	},

	// CH datetimes
	"created_at": "2019-06-10T00:00:00Z",
	"last_modified": "2019-06-10T00:00:00Z",

	// Course Bundles
	"thinkific_bundle_id": "30745",
	"thinkific_bundled_courses": [
            {
                "id": "573677",
                "name": "00. MMFT Drip Demo - Introduction & Overview",
                "slug": "mmft-drip-demo",
                "sort_order": "0"
            },
            {
                "id": "579183",
                "name": "01. MMFT Drip Demo",
                "slug": "copy-of-mmft-drip-demo-module-2",
                "sort_order": "1"
            },
            {
                "id": "573703",
                "name": "2 MMFT Drip Demo",
                "slug": "mmft-drip-demo-module-2",
                "sort_order": "2"
            },
            {
                "id": "573715",
                "name": "03 MMFT Drip Demo",
                "slug": "mmft-drip-demo-module-3",
                "sort_order": "3"
            },
            {
                "id": "573716",
                "name": "04. MMFT Drip Demo",
                "slug": "mmft-drip-demo-module-4",
                "sort_order": "4"
            },
            {
                "id": "573721",
                "name": "05. MMFT Drip Demo",
                "slug": "mmft-drip-demo-module-5",
                "sort_order": "5"
            },
            {
                "id": "573723",
                "name": "06. MMFT Drip Demo",
                "slug": "mmft-drip-demo-module-6",
                "sort_order": "6"
            },
            {
                "id": "573726",
                "name": "07. MMFT Drip Demo",
                "slug": "mmft-drip-demo-module-7",
                "sort_order": "7"
            },
            {
                "id": "573729",
                "name": "08. MMFT Drip Demo",
                "slug": "mmft-drip-demo-module-8",
                "sort_order": "8"
            }
        ]
}
```