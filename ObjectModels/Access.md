# Access Object Model**

## Overview

**Current version:** 0.0.1

**Logs:**

- 2019-10-04: Created.

## Purpose

The Access Object Model is a representation of a call to register a user, with or without product associations.

## Definitions

- `user` _(object)_: This is the same data that is available per the [User Object Model](User.md).
- `purchased_products` _(array of objects)_: This is the same data that is available per the [Product Object Model](Product.md).
	- **NOTE:** Product data is subject to change.

## NOTES

_**This object model is in active development so it is subject to a lot of changes!**_

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** registration object, during the checkout process for a non-registered user._
```jsonc
{
	"user": {
        "email": "johndoe@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "name": "John Doe",  // normalize (add in)
        "verified_email": "true",
        "phone": "111-111-1111",
        "addresses": [
            "207119551": {
                "input_from": "PLATFORM_SHOPIFY",  // normalize (add in)
                "company": "Sounds True",
                "address1": "413 S Arthur Ave",
                "city": "Louisville",
                "postal_code": "80027",
                "region": "Colorado",
                "region_code": "CO",
                "country": "United States",
                "country_iso_2": "US",
                "default": "false"
            },
            "207119552": {
                "input_from": "PLATFORM_SHOPIFY",   // normalize (add in)
                "company": "",
                "address1": "123 Main St",
                "address2": "Apt. 4",
                "city": "New York",
                "postal_code": "10002",
                "region": "New York",
                "region_code": "NY",
                "country": "United States",
                "country_iso_2": "US",
                "default": "true"
            }
        ],
        "state": "USER_ENABLED",  // normalize (if from Shopify, normalize to gRPC equivalent; otherwise add)
        "note": "This customer is loyal",
		"registered_from": "REGISTRATION_SHOPIFY",  // normalize (add in)
		// normalize `platforms` (add in)
        "platforms": {
            "PLATFORM_SHOPIFY": {
                "platform_user_id": "9876543210",
                "active": "true"
            },
            "PLATFORM_OCEP": {
                "platform_user_id": "56789123540",
                "active": "true",
                "api_key": "2CA7DFA6-74EB-4096-B68A-7C398E827AC6"
            },
            "PLATFORM_DIGITAL_LIBRARY": {
                "platform_user_id": "johndoe@example.com",  // same as user ID (email)
                "active": "true",
                "api_key": "D437B971-178B-4E37-974F-FA0C6808900A"
            }
        },
        "accepts_marketing": "false",
        "accepts_marketing_updated_at": "2019-06-10 00:00:00",
        "marketing_opt_in_level": "MARKETING_UNKNOWN",  // normalize (if from Shopify, normalize to gRPC equivalent; otherwise add)
        "created_at": "2019-06-10 00:00:00",
        "last_modified": "2019-06-10 00:00:00"  // normalize (if from Shopify, change `updated_at` to `last_modified`)
    },
	"products": [
		{
			"id": 808950810,  // maps to info["line_items"][i]["variant_id"] (assuming webhook data) - UNSURE
			"product_id": 632910392,  // maps to info["line_items"][i]["product_id"] (assuming webhook data) - we NEED the actual product (child/parent)
			"sku": "AF05649W", // maps to info["line_items"][i]["sku"] (assuming webhook data)
			"title": "The Yoga Sutras: Audio Download",  // maps to info["line_items"][i]["title"] (assuming webhook data) - unsure
			"subtitle": "",  // not exactly sure what this maps to in Shopify
			"purchase_date": "2019-10-03 16:11:15", // maps to info["created_at"] (assuming webhook data) (NEEDED???)
			"thumbnail_link": "somelocation/thumbnail.jpg",  // Shopify to send the image src (link) data???
			"product_type": "TYPE_AUDIO",  // normalize (add in) UserProduct:ProductType
			"can_access": "true",  // normalize (add in)
			"registration_promo_item": true,  // Can we know if this is a promo item or not???? i.e. Is this info in Shopify?
		}
	]
}
```