# Access Object Model**

## Overview

**Current version:** 0.0.3

**Logs:**

- 2020-03-13: **Added** `return_to` parameter for the Shopify Multipass token.
- 2020-01-21: **Added** `password` and updated the JSON example.
- 2019-10-04: Created.

## Purpose

The Access Object Model is a representation of a call to register a user, with or without product associations.

## Definitions

- `user` _(object)_: This is the same data that is available per the [User Object Model](User.md).
- `password` _(string)_: This is the user's password; **base64 encoded** (e.g. `VGVzdGluZzEyMzQhIQ==`)
- `return_to` _(string)_: _[For Shopify Only]_ The location of where a user is redirected to after being logged into Shopify (added to the Multipass token) (e.g. `https://soundstrue.com/checkout`)
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
        "name": "John Doe",  // normalize (add in - Access)
        "verified_email": "true",
        "phone": "111-111-1111",
        "addresses": [
            "207119551": {
                "input_from": "PLATFORM_SHOPIFY",  // normalize (add in - Access)
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
                "input_from": "PLATFORM_SHOPIFY",   // normalize (add in - Webhook listener for Shopify)
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
        "state": "USER_ENABLED",  // normalize (if from Shopify, normalize to gRPC equivalent; otherwise add - Access sets this)
        "note": "This customer is loyal",
		"registered_from": "REGISTRATION_SHOPIFY",  // normalize (add in - Webhook listener for Shopify)
		// normalize `platforms` (add in - Webhook listener for Shopify)
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
        "last_modified": "2019-06-10 00:00:00"  // normalize (if from Shopify, change `updated_at` to `last_modified`)
    },
	"password": "VGVzdGluZyMjIzEyMzQhIQ==",
    "purchased_products": {
		"CE05948": {
			"title": "The Untethered Soul at Work: CE Credits",
			"subtitle": "",
			"purchase_date": "2019-06-10 00:00:00",
			"thumbnail_link": "somelocation/thumbnail.jpg",
			"product_format": "CE Credits"
		}
    }
}
```