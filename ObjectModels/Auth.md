# Auth Object Model

## Overview

**Current version:** 0.0.1

**Logs:**

- 2019-12-31: Created.

## Purpose

The Auth Object Model is the representation of the user for the Auth Service and the format it needs to communicate with AWS Cognito.

## Definitions

### AWS Cognito User

The AWS Cognito User represents user data that will be stored in AWS Cognito.

- `email` _(string)_: The email address of the user. This also serves as the _username_.
- `first_name` _(string)_: This is the user's first name.
- `last_name` _(string)_: This is the user's last name.
- `password` _(string)_: This is the user's password; **base64 encoded** (e.g. `VGVzdGluZzEyMzQhIQ==`)

### AWS Cognito Token Set

The AWS Cognito Token Set represents token data that will be returned from AWS Cognito.

- `aws_jwt_access_token` _(string)_: The Access Token needed to communicate with most of the Customer Hub APIs.
- `aws_jwt_refresh_token` _(string)_: The Refresh Token needed to retrieve a new Access Token to extend the session length.
- `token_type` _(string)_: Type of token (e.g. `Bearer`)
- `token_expires` _(int)_: Length of time the Access Token is good for (e.g. `3600`)

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** AWS Cognito User object._
```json
{
	"user": {
		"email": "test@example.com",
		"first_name": "TEST",
		"last_name": "TESTER",
		"password": "VGVzdGluZzEyMzQhIQ=="
	}
}
```