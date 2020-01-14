# Product Object Model

## Overview

**Current version:** 0.0.5

**Logs:**

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
- `parent_sku` _(string)_ - The unique identifier for the parent product for the product, if applicable (e.g. "GP006057").

- `title` _(string)_ - The title of the product (e.g. "Alphabreaths")
- `subtitle` _(string)_ - The subtitle of the product (e.g. "The ABCs of Mindful Breathing")
- `body_html` _(string)_ - A description of the product. Supports HTML formatting.
- `format_type` _(string)_ - The format type for the product. Options are:
	- `FORMAT_BOOKS`
	- `FORMAT_VIDEO`
	- `FORMAT_AUDIO`
	- `FORMAT_MUSIC`
- `published` _(string)_ - String representation of whether this product has been published (e.g. "true")
- `published_at` _(string)_ -  The date and time ([ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601)) when the product was published. Can be set to null to unpublish the product from the Online Store channel (e.g. "2019-05-28T0:00:00-05:00").

- `discount_price` _(float)_ - The discounted price of the product (e.g. "10.77"),
- `price` _(float)_ - The full price of the product (e.g. "17.95"),
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
	- `LEVEL_BEGINNER`
	- `LEVEL_INTERMEDIATE`
	- `LEVEL_ADVANCED`
- `audience` _(string)_ - The targeted consumer audience for the product. Options are:
	`AUDIENCE_CHILD`
	`AUDIENCE_PROFESSIONAL`
	`AUDIENCE_NON_PROFESSIONAL`
- `duration` _(string)_ - The general duration of the product. Options are:
	- `DURATION_HOURS`
	- `DURATION_DAYS`
	- `DURATION_WEEKS`
	- `DURATION_MONTHS`
	- `DURATION_YEARS`

- `authors` _(array of objects)_ - Authors listed (with URLs) for the product. An author object consists of:
	- `name` _(string)_ - The name of the author (e.g. "Christopher Willard"").
	- `url` _(string)_ - The full url location of the author page (e.g. "https://www.soundstrue.com/store/christopher-willard-pysd.html").

- `vendor` _(string)_ - The vendor for the product (e.g "Sounds True").
- `barcode` _(string)_ - The barcode for the product.
- `upc` _(string)_ - The UPC for the product.
- `isbn_10` _(string)_ - The [ISBN-10](https://en.wikipedia.org/wiki/International_Standard_Book_Number) for the product.
- `isbn_13` _(string)_ - The [ISBN-13](https://en.wikipedia.org/wiki/International_Standard_Book_Number) for the product.

- `requires_shipping` _(bool)_ - Whether or not the product requires shipping (e.g. a physical product would be `true`)
- `weight` _(float)_ - The numeric weight measurement for the product (e.g. `12.7`).
- `weight_unit` _(string)_ - The unit for the weight measurement. Options are:
	- `WEIGHT_GRAM`
	- `WEIGHT_KILOGRAM`
	- `WEIGHT_POUND`

- `taxable` _(string)_ - Whether or not the product subject to taxes.
- `tax_code` _(string)_ - If `taxable`, this is the tax code for which the product is taxable as (e.g. `DB031013`).

- `equal_buy_links` _(array of objects)_: An array of EBL objects where users can purchase products at other locations. An EBL object contains the following:
	- `name` _(string)_: The display name of the equal buy link.
	- `url` _(string)_: The full URI/URL of the equal buy link.

- `sort_order` _(int)_ - The sort/display order for child products, if applicable (e.g. 10).

- `platform` _(object)_ - Specific platform attributes that are needed, or helpful, for the product. This consists of the following (not an exhaustive list):
	- `name` _(string)_: The name of the platform. Options are:
		- `PLATFORM_UNKNOWN`
		- `PLATFORM_SHOPIFY`
		- `PLATFORM_DIGITAL_LIBRARY`
		- `PLATFORM_THINKIFIC`
	- `product_id` _(string)_: The platform's identifier of the product on its system (e.g. ).
	- `options` _(map)_: Custom platform options for SoundsTrue (e.g. `option1`, `option2`, `option3`). Each option consists of:
		- `name` _(string)_ - The name of the option.
		- `value` _(string)_ - The value of the the option.
	- `product_created_at` _(string)_: The date and time ([ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601)) when the product was created in Shopify (e.g. "2012-02-15T15:12:21-05:00",).
	- `product_updated_at` _(string)_: The date and time ([ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601)) when the product was last modified in Shopify (e.g. "2012-08-24T14:01:47-04:00").

- `created_at` _(string)_ - string representation of the initial product entry (creation) UTC datetime (e.g. "2019-06-10 00:00:00"). "2019-06-10 00:00:00",
- `last_modified` _(string)_ - string representation of when the product entry is updated UTC datetime (e.g. "2019-06-10 00:00:00"). "2019-06-10 00:00:00"

## Mapping of Datapoints (CH --> Shopify)

Mapping of CH datapoints to [Trellis' Shopify doc](https://docs.google.com/spreadsheets/d/1K0gtopSasF_5EJ5-VAq0Cc3DdZIX3GQgVfLaEfS1grU/edit?usp=sharing)

| Top-level datapoint (CH) | Sub-level datapoint (CH) | Shopify Name (Sheets column) |
|:----------|:-------------|:-------------|
| `sku` | | Variant SKU (R) |
| `parent_sku` | | |
| `title` | | Title (C) |
| `subtitle` | | Metafield (AN) |
| `body_html` | | Body (HTML) (D) |
| `format_type` | | Metafield (AR) <br>* _can we get more exact formats (single - Option1 Value won't work)?_ |
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

#### Unassigned from Sheet

| Name (Column) | Notes |
|:---------|:--------|
| Type (F) ||
| Tags (G) | _Grayed out in Sheets_ |
| Template Suffix (H) | _Grayed out in Sheets_ |
| Published Scope (I) ||
| Option 2 Name (N) | _Grayed out in Sheets_ |
| Option 2 Value (O) | _Grayed out in Sheets_ |
| Option 3 Name (P) | _Grayed out in Sheets_ |
| Option 3 Value (Q) | _Grayed out in Sheets_ |
| Variant Grams (U) | _Grayed out in Sheets_ |
| Variant Inventory Tracker (V) | _Grayed out in Sheets_ |
| Variant Inventory Policy (X) ||
| Variant Inventory Cost (Y) ||
| Variant Fulfillment Service (Z) ||
| Image Attachment (AF) | _Grayed out in Sheets_ |
| Image Alt Text (AI) | _Grayed out in Sheets_ |
| Variant Image (AJ) | _Grayed out in Sheets_ |
| Metafield (AO) | _Need description of field_ |
| Metafield (AP) | _Need description of field_ |
| Metafield (AQ) | _Need description of field_ |
| Metafield (AS) | _Need description of field_ |
| Metafield (AT) | _Seems to be "Short Description"_ |
| Metafield (AU) | _Seems to be "Primary Author"_ |
| Metafield (AV) | _Need description of field_ |
| Metafield (AW) | _Need description of field_ |
| Metafield (AX) | _Need description of field_ |
| Metafield (AY) | _Need description of field_ |
| Metafield (BA) | _Need description of field_ |
| Metafield (BB) | _Need description of field_ |
| Metafield (BC) | _Need description of field_ |
| Metafield (BD) | _Need description of field_ |
| Metafield (BE) | _Need description of field_ |
| Metafield (BF) | _Need description of field_ |
| Metafield (BG) | _Need description of field_ |
| Metafield (BH) | _Need description of field_ |
| Metafield (BI) | _Need description of field_ |

## Notes / TODOs

- _This data model is subject to heavy changes depending on what is provided by Trellis._
- The product object model represents our ideal product dataset _within_ the CH ecosystem, not the Shopify system (although, and of course, there is a lot of overlap).

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** Product object._
```jsonc
{
	"sku": "BK05536F",  // primary key
	"parent_sku": "GP006057",
	
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
	"weight": 0,
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
		"name": "PLATFORM_SHOPIFY",
		"product_id": "",
		"product_created_at": "2012-02-15T15:12:21-05:00",
		"product_updated_at": "2012-08-24T14:01:47-04:00",
		"options": {
			"option1": {
				"name": "Format",
				"value": "eBook"
			}
		}
	},

	// CH datetimes
	"created_at": "2019-06-10 00:00:00",
	"last_modified": "2019-06-10 00:00:00"
}
```