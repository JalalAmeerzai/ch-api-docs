# AdminUser Object Model

## Overview

**Current version:** 0.0.1

**Logs:**

- 2019-12-31: Created.

## Purpose

The Multipass Object Model is the representation of the user info for the Shopify Multipass Token.

## Definitions

### User Info

The AWS Cognito User represents user data that will be stored in AWS Cognito.

- `email` _(string)_: The email address of the user.
- `first_name` _(string)_: This is the user's first name.
- `last_name` _(string)_: This is the user's last name.
- `return_to` _(string)_: Redirect users to a specific page of the Shopify store.

## Notes / TODOs

Please see notes on the main documentation page.

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** User Info object._
```json
{
	"user": {
		"email": "test@example.com",
		"first_name": "TEST",
		"last_name": "TESTER",
		"return_to": "http://store.com/some_specific_location"
	}
}
```