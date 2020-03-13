# UserProduct Object Model

## Overview

**Current version:** 0.0.10

**Logs:**

- 2020-02-26: **Removed** defined product types in favor of imported product types from the product service.
- 2020-02-12: **Updated** the timestamp format to a standard format [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339). **Updated** the `product_format` options. **Added** DB mappings for option names.
- 2019-12-10: **Changed** changed some types to be correct and updated the example.
- 2019-11-07: **Changed** `can_access` `bool` type to a string representation.
- 2019-10-11: **Added** `recent_product` to address the "resume bar" functionality needs.
- 2019-10-09: **Changed** `product_type` to `product_format` for consistency.
- 2019-10-08: **Modified** `media_metadata` again to fix gRPC incompatability.
- 2019-10-07: **Added** `last_accessed` to keep track of last tracks accessed by the user and **modified** `media_metadata` to include changes for individual tracks (also added their examples).
- 2019-09-05: Implemented the TODOs (added `purchase_date` and `registration_promo_item`), updated the structure of containing the objects to a map, and changed `name` to `title`.
- 2019-07-19: Changed `user_id` to `user_email` as the primary identifier (see [User Service](https://github.com/stdev/ch-user-service/blob/develop/user/User.md) for details).
- 2019-07-16: **Added** `subtitle` and `thumbnail_link`. **Changed** `updated_at` to `last_modified`. Fixed formatting of `purchased_products`.
- 2019-07-01: Fixed minor formatting.
- 2019-06-28: Created. Removed a confusing top level key for clarity.

## Purpose

The UserProduct Object Model is the representation of the user's purchased, digital products. It holds all the necessary data for those products in relation to the user and business needs.

## Definitions

- `user_email` _(string)_: This is an individual user's email (which is the unique primary key in the `user` table).
- `purchased_products` _(map of objects)_: A map of products owned by a user with the `sku` as a key and containing the following within an object:
	- `title` _(string)_: The name of the product.
	- `subtitle` _(string)_: The subtitle of the product.
	- `purchase_date` _(string)_: The datetime of purchase for the product (e.g. `"2019-06-1T 00:00:00V"`)
	- `thumbnail_link` _(string)_: The thumbnail image for the product.
	- `product_format` _(string)_: The type/format of a product containing the following (see [Product.format_type](Product.md)).
	- `can_access` _(string representation of bool)_: string representation of whether access to this product by this customer is valid. A use case for this is to deny access to a user's product when a payment (recurring payments) is missed.
	- `media_metadata` _(map)_: The metadata for media containing the following options (if applicable, for _each individual track_):
		- **[video/audio only]**
			- *KEY:* string representation of the individual audio track (or video) number (e.g. track 1's key would be `"1"`)
			- *VALUE:* string representation of last known video/audio time location (or video) (e.g. "00:31:16")
		- **[eBook only]**
			- `current_page` _(string)_:  last known page number _(**NOTE:** we might have to include other info, such as chapter, for the ebook)_.
	- `last_accessed` _(object)_: The track and datetime last accessed by the user (applies to video/audio types only).
		- `track` _(string)_: the string representation of an individual track number.
		- `access_time` _(string)_: string representation of when the track was last accessed UTC datetime (e.g. "2019-06-10T00:00:00Z").
	- `registration_promo_item` _(string representation of bool)_: Whether or not this product was given as a free product upon registration.
	- `authors` _(list of strings)_: Authors for the product.
- `recent_product` _(string)_: The SKU of the product that was last interacted with by the user.
- `created_at` _(string)_: string representation of the initial user product entry (creation) [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339) datetime (e.g. "2019-06-10T00:00:00Z").
- `last_modified` _(string)_: string representation of when the user product entry is updated [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339) datetime (e.g. "2019-06-10T00:00:00Z").

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** UserProduct object._
```json
{
	"user_email": "johndoe@example.com",
	"purchased_products": {
		"CE05948": {
            "title": "The Untethered Soul at Work: CE Credits",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_CE_CREDITS",
            "can_access": "true",
            "registration_promo_item": "true",
            "authors": [
                "John O'\''Donohue"
            ]
        },
		"AF02384W": {
            "title": "Longing and Belonging: Audio Download",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_AUDIO",
            "can_access": "true",
            "media_metadata": {
                "1": "00:16:23"
            },
            "registration_promo_item": "false",
            "authors": [
                "Author 1"
            ]
        },
        "VID123D": {
            "title": "Some Video (D)",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_VIDEO",
            "can_access": "true",
            "media_metadata": {
                "1": "01:21:49"
            },
            "registration_promo_item": "false",
            "authors": [
                "Author 2"
            ]
        },
        "VID123E": {
            "title": "Some Video (E)",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_VIDEO",
            "can_access": "true",
            "media_metadata": {
                "1": "01:21:49"
            },
            "registration_promo_item": "false"
        },
        "VID123F": {
            "title": "Some Video (F)",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_VIDEO",
            "can_access": "true",
            "media_metadata": {
                "1": "01:21:49"
            },
            "registration_promo_item": "false"
        },
        "BK05619W": {
            "title": "Making Magic: eBook",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_EBOOK",
            "can_access": "true",
            "media_metadata": {
                "current_page": "16"
            },
            "registration_promo_item": "false",
            "authors": [
                "Author 3",
                "Author 4"
            ]
        }
	},
	"recent_product": "AF02384W",
	"created_at": "2019-06-10T00:00:00Z",
	"last_modified": "2019-06-10T00:00:00Z"
}
```