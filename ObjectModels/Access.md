# Access Object Model

## Overview

**Current version:** 0.0.5

**Logs:**

- 2021-04-30: **Corrected** documentation for some definitions and **added** `g_recaptcha_response` and `send_welcome_email` definitions. Cleaned up example.
- 2020-03-17: **Corrected** documentation for the `multipass_return_to` parameter for the Shopify Multipass token.
- 2020-03-13: **Added** `return_to` parameter for the Shopify Multipass token.
- 2020-01-21: **Added** `password` and updated the JSON example.
- 2019-10-04: Created.

## Purpose

The Access Object Model is a representation of a call to register a user, with or without product associations.

## Definitions

- `user` _(object)_: This is the same data that is available per the [User Object Model](User.md).
- `password` _(string)_: This is the user's password; **base64 encoded** (e.g. `VGVzdGluZzEyMzQhIQ==`)
- `multipass_return_to` _(string)_: _[For Shopify Only]_ The location of where a user is redirected to after being logged into Shopify (added to the Multipass token) (e.g. `https://soundstrue.com/checkout`)
- `g_recaptcha_response` _(string)_: This is the Google recaptcha response that will need to be sent to the service for verification.
- `purchased_products` _(map of objects)_: Products to be associated with with the user. This is the same data that is available per the [Product Object Model](Product.md). _**NOTE:** only use this field if payment has been verified!_
- `send_welcome_email` _(bool as string)_: optional to allow for disabling of the custom welcome email in AWS Cognito (default is `"true"`) (e.g. `"true"`)

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** registration object._
```jsonc
{
	"user": {
        "email": "johndoe@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "verified_email": "true",
        "phone": "111-111-1111",
        "addresses": [
            "207119551": {
                "input_from": "PLATFORM_SHOPIFY",
                "company": "Sounds True",
                "address1": "413 S Arthur Ave",
                "city": "Louisville",
                "postal_code": "80027",
                "region": "Colorado",
                "region_code": "CO",
                "country": "United States",
                "country_iso_2": "US",
                "default": "false"
            }
        ],
        "state": "USER_ENABLED",
        "note": "This customer is loyal",
		"registered_from": "REGISTRATION_SHOPIFY",
        "platforms": {
            "PLATFORM_SHOPIFY": {
                "platform_user_id": "9876543210",
                "active": "true"
            },
            "PLATFORM_DIGITAL_LIBRARY": {
                "platform_user_id": "johndoe@example.com",
                "active": "true"
            }
        }
    },
	"password": "VGVzdGluZyMjIzEyMzQhIQ==",
    "purchased_products": {
		"CE05948": {
			"purchase_date": "2019-06-10T00:00:00Z",
			"product_format": "FORMAT_CE_CREDITS",
            "can_access": "true"
		}
    },
    "send_welcome_email": "true" // can be omitted unless "false"
}
```