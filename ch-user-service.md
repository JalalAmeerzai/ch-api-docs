# ch-user-service

## Purpose of This Service

This service is the source of truth for _all_ user data within the Sounds True digital experience. All other services (internal/external) should utilize this service for non-admin user needs.

The User Service processes all CRUDL functionality for non-admin Users within the Customer Hub ecosystem.

## Repository
* [Github](https://github.com/stdev/ch-user-service)

## Object Model
* [User Service Object Model](ObjectModels/User.md)

## Access Tokens (JWT)

_ALL_ API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

## Functionality

The following contains details for the `Create`, `Read`, `Update`, `Delete`, and `List` functionality of the User Service.

### USERS

The following are available gRPC methods for general user endpoints.

#### `Create()`
> Creates a non-admin user.

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/user/`

##### Accepted Request Arguments
- `user` _(User object)_

##### Response
- `created` _(bool)_
- `message` _(string)_
	- "User (email: `<User.email>`) has been created"

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.Create(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/user \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
    "user": {
        "email": "johndoe@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "name": "John Doe",
        "verified_email": true,
        "phone": "111-111-1111",
        "addresses": {
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
                "default": false
            },
            "207119552": {
                "input_from": "PLATFORM_SHOPIFY",
                "company": "",
                "address1": "123 Main St",
                "address2": "Apt. 4",
                "city": "New York",
                "postal_code": "10002",
                "region": "New York",
                "region_code": "NY",
                "country": "United States",
                "country_iso_2": "US",
                "default": true
            }
        },
        "state": "USER_ENABLED",
        "note": "This customer is loyal",
        "registered_from": "REGISTRATION_SHOPIFY",
        "platforms": {
        	"PLATFORM_SHOPIFY": {
                "platform_user_id": 9876543210,
                "active": "true"
            },
            "PLATFORM_DIGITAL_LIBRARY": {
                "platform_user_id": 123456789,
                "active": "true",
                "api_key": "D437B971-178B-4E37-974F-FA0C6808900A",
                "preferences": {
                    "download_wifi_only": false,
                    "ui_theme": "sometheme",
                    "video_quality": "high",
                    "audio_quality": "medium"
                }
            }
        },
        "organizations": {
        	"1": "US Senate",
        	"2": "S1T Test"
        },
        "multipass_token": "sVSwcIWAz_DKCEKWVqtzUxn5ZO3tj_DsORtNKG56soNs5mk5tIStJqIBwobSd6DQzBBj7Q9pZWDmiVHLBwWbPq8d3bcfrMF8uk_rapZGlMUF4Hhed-Iwq2d5y2i1Vl5VU3lHKmVtZJro52yXEboYxjTCZoBnM5d959k_A4LeWS6G6xHLmMzMNTnuN1v8QSU8z-V7QDY8AL6WYGLT_n8Pyg==",
        "accepts_marketing": "true",
        "accepts_marketing_updated_at": "2019-06-10T00:00:00Z",
        "marketing_opt_in_level": "MARKETING_UNKNOWN"
    }
}'
```

##### JSON Response Example

```json
{
    "created": true,
    "message": "User (email: johndoe@example.com) has been created"
}
```

---

#### `Get()`
> Retrieve a user by **email**.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/user/:user_id`

##### Accepted Request Arguments
- `user_id` _(string)_

##### Response
- `user` _(User object)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.GetByID(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/user/johndoe@example.com \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "user": {
        "previous_emails": [],
        "addresses": {
            "207119551": {
                "input_from": "PLATFORM_SHOPIFY",
                "company": "Sounds True",
                "address1": "413 S Arthur Ave",
                "address2": "",
                "city": "Louisville",
                "country": "United States",
                "country_iso_2": "US",
                "postal_code": "80027",
                "region": "Colorado",
                "region_code": "CO",
                "default": "false"
            },
            "207119552": {
                "input_from": "PLATFORM_SHOPIFY",
                "company": "",
                "address1": "123 Main St",
                "address2": "Apt. 4",
                "city": "New York",
                "country": "United States",
                "country_iso_2": "US",
                "postal_code": "10002",
                "region": "New York",
                "region_code": "NY",
                "default": "true"
            }
        },
        "platforms": {
            "PLATFORM_DIGITAL_LIBRARY": {
                "platform_user_id": "123456789",
                "api_key": "D437B971-178B-4E37-974F-FA0C6808900A",
                "active": "true"
            },
            "PLATFORM_SHOPIFY": {
                "platform_user_id": "9876543210",
                "api_key": "",
                "active": "true"
            }
        },
        "organizations": {
            "1": "US Senate",
            "2": "S1T Test"
        },
        "email": "johndoe@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "name": "John Doe",
        "verified_email": true,
        "phone": "111-111-1111",
        "state": "USER_ENABLED",
        "note": "This customer is loyal",
        "registered_from": "REGISTRATION_SHOPIFY",
        "accepts_marketing": "false",
        "accepts_marketing_updated_at": "2019-07-26T00:00:00Z",
        "marketing_opt_in_level": "MARKETING_UNKNOWN",
        "created_at": "2019-09-12T17:00:06Z",
        "last_modified": "2019-09-12T17:00:45Z",
        "multipass_token": "sVSwcIWAz_DKCEKWVqtzUxn5ZO3tj_DsORtNKG56soNs5mk5tIStJqIBwobSd6DQzBBj7Q9pZWDmiVHLBwWbPq8d3bcfrMF8uk_rapZGlMUF4Hhed-Iwq2d5y2i1Vl5VU3lHKmVtZJro52yXEboYxjTCZoBnM5d959k_A4LeWS6G6xHLmMzMNTnuN1v8QSU8z-V7QDY8AL6WYGLT_n8Pyg=="
    }
}
```


---

#### `Update()`
> Update user information.

**NOTES:**

- If you update maps (i.e. `addresses`, `platforms`, and/or `organizations`), you **must** pass in _**everything**_ that needs to be **kept** (remain) in the map. 
- This endpoint now updates the following AWS Cognito user attributes automatically
    - `first_name`, `last_name`, and `email`
- The `name` attribute was `DEPRECATED` and no longer supported by the APIs (see Object Model for more info)

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/user/:user_id`

##### Accepted Request Arguments
- `user_id` _(string)_
- `user` _(User object)_
    - `new_email` - _Allowed in `Update()` only!_ Pass in **only** to change a user's email.

**EXCEPTIONS:** This endpoint cannot update the following `User` attributes:

- `email` - This attribute is used internally. To update an email, use the `new_email` attribute instead
- `created_at`
- `previous_emails`

##### Response
- `updated` _(bool)_
- `message` _(string)_
	- "User (email: `<User.email>`) has been updated"
        - if `new_email` was passed in and the update was successful, `<User.email>` will be the new email
	
##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
	user: req.body
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.Update(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/user/johndoe@example.com \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
    "first_name": "JohnnyBoy",
    "accepts_marketing": "false",
    "accepts_marketing_updated_at": "2019-07-26T00:00:00Z",
    "verified_email": true,
    "new_email": "johnnyboydoe@example.com",
    "multipass_token": "sVSwcIWAz_DKCEKWVqtzUxn5ZO3tj_DsORtNKG56soNs5mk5tIStJqIBwobSd6DQzBBj7Q9pZWDmiVHLBwWbPq8d3bcfrMF8uk_rapZGlMUF4Hhed-Iwq2d5y2i1Vl5VU3lHKmVtZJro52yXEboYxjTCZoBnM5d959k_A4LeWS6G6xHLmMzMNTnuN1v8QSU8z-V7QDY8AL6WYGLT_n8Pyg==",
    "platforms": {
        "PLATFORM_SHOPIFY": {
            "platform_user_id": 123456789,
            "active": "false"
        }
    }
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "User (email: johnnyboydoe@example.com) has been updated"
}
```

---

#### `UpdateEmail()`
> Update user email (dynamoDB primary key).

**NOTE:** This gRPC method makes a call to `UserProduct:UpdateEmail()` because `UserProduct.UserEmail` is a dependency (FK) of `User.Email`.

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/user/email/:user_id`

##### Accepted Request Arguments
- `user_id` _(string)_
- `new_user_id` _(string)_

##### Response
- `updated` _(bool)_
- `message` _(string)_
	- "User (email: `<User.email>`) has been updated (previous email: `<User.email>`)"
	
##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
    new_user_id: req.body.new_email
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.UpdateEmail(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/user/email/johndoe@example.com \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
    "new_email": "johndoe99@example.com"
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "User (email: johndoe99@example.com) has been updated (previous email: johndoe@example.com)"
}
```

---

#### `Delete()`
> Remove a user by email.

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/user/:user_id`

##### Accepted Request Arguments
- `user_id` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "User (email: `<User.email>`) has been deleted"
	
##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.Delete(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X DELETE /api/user/johndoe99@example.com \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "User (email: johndoe99@example.com) has been deleted"
}
```


---

#### `ListUsers()`
> Intended for **Admin UI ONLY**.
> Retrieve _ALL_ users from the DB.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/user/filters/:limit/:key?`
    - `key` (a.k.a. `exclusive_start_key`) - The User email of the last product retrieved (the result of `last_evaluated_key` in the response) _(optional)_

##### Accepted Request Arguments
- `limit` _(string)_
    - the _amount_ of results to show (i.e. the page amount)
- `exclusive_start_key` _(string)_ (`key` in REST endpoint)
    - This is the result (email) to start the next page set from. This should equal the value of `last_evaluated_key` in the reponse, if applicable.

##### Response
- `count` _(int64)_
- **repeated** `users` _(User objects)_
- `last_evaluated_key` _(string)_ 
    - This is the last `email` evaluated by the DB. This should be passed as the `exclusive_start_key` in a subsequential call (i.e. make another of the same call passing the value of `last_evaluated_key` to `exclusive_start_key`).

##### gRPC Request Example (JS)
```js
let payload = {
    limit: req.params.limit,
    exclusive_start_key: req.params.key
};
// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.ListUsers(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		console.log("RES RESULT");
		res.json(result);
	}
});
```

##### cURL Request Example

_**NOTE:** The following example call is with a pagination of 10, starting at the beginning (first call), and there are 2 results. Therefore the `last_evaluated_key` is empty.

```bash
curl -X GET /api/user/filters/10 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "users": [
        {
            "previous_emails": [],
            "addresses": {},
            "platforms": {},
            "organizations": {},
            "email": "johndoe@example.com",
            "first_name": "",
            "last_name": "",
            "name": "",
            "verified_email": false,
            "phone": "",
            "state": "USER_ENABLED",
            "note": "",
            "registered_from": "REGISTRATION_UNKNOWN",
            "accepts_marketing": "",
            "accepts_marketing_updated_at": "",
            "marketing_opt_in_level": "MARKETING_UNKNOWN",
            "created_at": "2019-09-13T20:41:18Z",
            "last_modified": "2019-09-13T20:41:18Z"
        },
        {
            "previous_emails": [
                "dynamodb001@example.com"
            ],
            "addresses": {
                "1": {
                    "input_from": "PLATFORM_SHOPIFY",
                    "company": "",
                    "address1": "123 Main Sreet",
                    "address2": "",
                    "city": "New York",
                    "country": "United States",
                    "country_iso_2": "US",
                    "postal_code": "10001",
                    "region": "New York",
                    "region_code": "NY",
                    "default": "true"
                },
                "2": {
                    "input_from": "PLATFORM_SHOPIFY",
                    "company": "",
                    "address1": "PO Box 123456",
                    "address2": "",
                    "city": "New York",
                    "country": "United States",
                    "country_iso_2": "US",
                    "postal_code": "10001",
                    "region": "New York",
                    "region_code": "NY",
                    "default": "false"
                }
            },
            "platforms": {
                "PLATFORM_DIGITAL_LIBRARY": {
                    "platform_user_id": "0",
                    "api_key": "",
                    "active": "true"
                },
                "PLATFORM_SHOPIFY": {
                    "platform_user_id": "101",
                    "api_key": "",
                    "active": "true"
                }
            },
            "organizations": {
                "1": "US Senate"
            },
            "email": "dynamodb@example.com",
            "first_name": "DyanmoDB",
            "last_name": "User",
            "name": "DyanmoDB User",
            "verified_email": false,
            "phone": "111-111-1111",
            "state": "USER_ENABLED",
            "note": "",
            "registered_from": "REGISTRATION_DIGITAL_LIBRARY",
            "accepts_marketing": "false",
            "accepts_marketing_updated_at": "",
            "marketing_opt_in_level": "MARKETING_UNKNOWN",
            "created_at": "2019-09-13T20:36:51Z",
            "last_modified": "2019-09-13T20:36:51Z",
            "multipass_token": "sVSwcIWAz_DKCEKWVqtzUxn5ZO3tj_DsORtNKG56soNs5mk5tIStJqIBwobSd6DQzBBj7Q9pZWDmiVHLBwWbPq8d3bcfrMF8uk_rapZGlMUF4Hhed-Iwq2d5y2i1Vl5VU3lHKmVtZJro52yXEboYxjTCZoBnM5d959k_A4LeWS6G6xHLmMzMNTnuN1v8QSU8z-V7QDY8AL6WYGLT_n8Pyg=="
        }
    ],
    "last_evaluated_key": "",
    "count": "2"
}
```


---

### ADDRESSES

The following are available gRPC methods for user address endpoints.

#### `AddAddress()`
> Creates an address entry for a non-admin user.

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/user/:user_id/address/`

##### Accepted Request Arguments
- `user_id` _(string)_
- `address_id` _(string)_
- `address` _(Address object)_

##### Response
- `added` _(bool)_
- `message` _(string)_
	- "The address has been added to User (email: `<User.email>`)"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
	address_id: req.body.address_id,
	address: req.body.address
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.AddAddress(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/user/johndoe@example.com/address \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"address_id": "1234567890",
	"address": {
        "input_from": "PLATFORM_DIGITAL_LIBRARY",
        "company": "",
        "address1": "1000 PDL Ave",
        "city": "New York",
        "country": "United States",
        "country_iso_2": "US",
        "postal_code": "10003",
        "region": "New York",
        "region_code": "NY",
        "default": "false"
    }
}'
```

##### JSON Response Example

```json
{
    "added": true,
    "message": "The address has been added to User (email: johndoe@example.com)"
}
```


---

#### `GetDefaultAddress()`
> Retrieve the user address with the `default` attribute set to `true`.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/user/:user_id/address/default/`

##### Accepted Request Arguments
- `user_id` _(string)_

##### Response
- `default_address` _(Address object)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.GetDefaultAddress(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/user/johndoe@example.com/address/default \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "default_address": {
        "input_from": "PLATFORM_SHOPIFY",
        "company": "",
        "address1": "123 Main St",
        "address2": "Apt. 4",
        "city": "New York",
        "country": "United States",
        "country_iso_2": "US",
        "postal_code": "10002",
        "region": "New York",
        "region_code": "NY",
        "default": "true"
    }
}
```


---

#### `UpdateAddress()`
> Update a user's address by an address ID.

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/user/:user_id/address/:address_id/`

##### Accepted Request Arguments
- `user_id` _(string)_
- `address_id` _(string)_
- `address` _(Address)_

##### Response
- `updated` _(bool)_
- `message` _(string)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.UpdateAddress(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/user/johndoe@example.com/address/1234567890 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"address": {
        "company": "S1T",
        "address2": "Apt. 4A"
    }
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "The address has been updated for User (email: johndoe@example.com)"
}
```


---

#### `DeleteAddress()`
> Remove a user's address by ID.

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/user/:user_id/address/:address_id/`

##### Accepted Request Arguments
- `user_id` _(string)_
- `address_id` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "The address ID `<Address.id>` has been deleted for User (email: `<User.email>`)"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
	address_id: req.params.addressId
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.DeleteAddress(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X DELETE /api/user/johndoe@example.com/address/1234567890 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "The address ID 1234567890 has been deleted for User (email: johndoe@example.com)"
}
```


---

#### `ListAddresses()`
> Retrieve all of user's addresses.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/user/:user_id/address/`

##### Accepted Request Arguments
- `user_id` _(string)_

##### Response
- `addresses` _(map`<string, Address>`)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.ListAddresses(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/user/johndoe@example.com/address \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "users": [
        {
            "previous_emails": [],
            "addresses": {
                "207119551": {
                    "input_from": "PLATFORM_SHOPIFY",
                    "company": "Sounds True",
                    "address1": "413 S Arthur Ave",
                    "address2": "",
                    "city": "Louisville",
                    "country": "United States",
                    "country_iso_2": "US",
                    "postal_code": "80027",
                    "region": "Colorado",
                    "region_code": "CO",
                    "default": "false"
                },
                "207119552": {
                    "input_from": "PLATFORM_SHOPIFY",
                    "company": "",
                    "address1": "123 Main St",
                    "address2": "Apt. 4",
                    "city": "New York",
                    "country": "United States",
                    "country_iso_2": "US",
                    "postal_code": "10002",
                    "region": "New York",
                    "region_code": "NY",
                    "default": "true"
                }
            },
            "platforms": {
                "PLATFORM_SHOPIFY": {
                    "platform_user_id": "9876543210",
                    "api_key": "",
                    "active": "true"
                },
                "PLATFORM_DIGITAL_LIBRARY": {
                    "platform_user_id": "123456789",
                    "api_key": "D437B971-178B-4E37-974F-FA0C6808900A",
                    "active": "true"
                }
            },
            "organizations": {
                "1": "US Senate",
                "2": "S1T Test"
            },
            "email": "johndoe@example.com",
            "first_name": "John",
            "last_name": "Doe",
            "name": "John Doe",
            "verified_email": true,
            "phone": "111-111-1111",
            "state": "USER_ENABLED",
            "note": "This customer is loyal",
            "registered_from": "REGISTRATION_SHOPIFY",
            "accepts_marketing": "true",
            "accepts_marketing_updated_at": "2019-06-10T00:00:00Z",
            "marketing_opt_in_level": "MARKETING_UNKNOWN",
            "created_at": "2019-09-16T14:58:27Z",
            "last_modified": "2019-09-16T14:58:27Z"
        },
        {
            "previous_emails": [
                "dynamodb001@example.com"
            ],
            "addresses": {
                "1": {
                    "input_from": "PLATFORM_SHOPIFY",
                    "company": "",
                    "address1": "123 Main Sreet",
                    "address2": "",
                    "city": "New York",
                    "country": "United States",
                    "country_iso_2": "US",
                    "postal_code": "10001",
                    "region": "New York",
                    "region_code": "NY",
                    "default": "true"
                },
                "2": {
                    "input_from": "PLATFORM_SHOPIFY",
                    "company": "",
                    "address1": "PO Box 123456",
                    "address2": "",
                    "city": "New York",
                    "country": "United States",
                    "country_iso_2": "US",
                    "postal_code": "10001",
                    "region": "New York",
                    "region_code": "NY",
                    "default": "false"
                }
            },
            "platforms": {
                "PLATFORM_DIGITAL_LIBRARY": {
                    "platform_user_id": "0",
                    "api_key": "",
                    "active": "true"
                },
                "PLATFORM_SHOPIFY": {
                    "platform_user_id": "101",
                    "api_key": "",
                    "active": "true"
                }
            },
            "organizations": {
                "1": "US Senate"
            },
            "email": "dynamodb@example.com",
            "first_name": "DyanmoDB",
            "last_name": "User",
            "name": "DyanmoDB User",
            "verified_email": false,
            "phone": "111-111-1111",
            "state": "USER_ENABLED",
            "note": "",
            "registered_from": "REGISTRATION_DIGITAL_LIBRARY",
            "accepts_marketing": "false",
            "accepts_marketing_updated_at": "",
            "marketing_opt_in_level": "MARKETING_UNKNOWN",
            "created_at": "2019-09-13T21:13:31Z",
            "last_modified": "2019-09-13T21:13:31Z"
        }
    ],
    "count": "2"
}
```


---

### PLATFORMS

The following are available gRPC methods for user platform endpoints.

#### `AddPlatform()`
> Creates a platform entry for a non-admin user.

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/user/:user_id/platform/`

##### Accepted Request Arguments
- `user_id` _(string)_
- `platform_name` _(string|PlatformName)_
- `platform` _(Platform object)_

##### Response
- `added` _(bool)_
- `message` _(string)_
	- "`<Platform.name>` has been added to User (email: `<User.email>`)"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
	platform_name: req.body.platform_name,
	platform: req.body.platform
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.AddPlatform(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/user/johndoe@example.com/platform \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"platform_name": "PLATFORM_OCEP",
	"platform": {
        "platform_user_id": 56789123540,
        "active": "true",
        "api_key": "2CA7DFA6-74EB-4096-B68A-7C398E827AC6"
	}
}'
```

##### JSON Response Example

```json
{
    "added": true,
    "message": "PLATFORM_OCEP has been added to User (email: johndoe@example.com)"
}
```


---

#### `GetPlatform()`
> Retrieve a user's platform by **name**.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/user/:user_id/platform/:platform_name`

##### Accepted Request Arguments
- `user_id` _(string)_
- `platform_name` _(string|PlatformName)_

##### Response
- `platform` _(map`<string, Platform>`)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
	platform_name: req.body.platform_name
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.GetPlatform(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/user/johndoe@example.com/platform/PLATFORM_OCEP \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "platform": {
        "PLATFORM_OCEP": {
            "platform_user_id": "56789123540",
            "api_key": "2CA7DFA6-74EB-4096-B68A-7C398E827AC6",
            "active": "true"
        }
    }
}
```


---

#### `UpdatePlatform()`
> Update a user's platform by platform name.

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/user/:user_id/platform/:platform_name`

##### Accepted Request Arguments
- `user_id` _(string)_
- `platform_name` _(string|PlatformName)_
- `platform` _(Platform object)_

##### Response
- `updated` _(bool)_
- `message` _(string)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
	platform_name: req.body.platform_name,
	platform: req.body.platform
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.UpdatePlatform(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/user/johndoe@example.com/platform/PLATFORM_OCEP \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"platform": {
        "active": "false"
	}
}'
```

##### JSON Response Example

```json
{
	"platform": {
        "active": "false"
	}
}
```


---

#### `DeletePlatformByName()`
> Remove a user's platform by name.

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/user/:user_id/platform/:platform_name`

##### Accepted Request Arguments
- `user_id` _(string)_
- `platform_name` _(string|PlatformName)_

##### Response
- `updated` _(bool)_
- `message` _(string)_
	- "<Platform.name> has been updated for User (email: `<User.email>`)"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
	platform_name: req.body.platform_name
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.DeletePlatformByName(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X DELETE /api/user/johndoe@example.com/platform/PLATFORM_OCEP \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "PLATFORM_OCEP for User (email: johndoe@example.com) has been deleted"
}
```


---

#### `ListPlatforms()`
> Retrieve all of a user's platforms.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/user/:user_id/platform/`

##### Accepted Request Arguments
- `user_id` _(string)_

##### Response
- `platforms` _(map`<string, Platform>`)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.ListPlatforms(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/user/johndoe@example.com/platform \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "platforms": {
        "PLATFORM_SHOPIFY": {
            "platform_user_id": "9876543210",
            "api_key": "",
            "active": "true"
        },
        "PLATFORM_OCEP": {
            "platform_user_id": "56789123540",
            "api_key": "2CA7DFA6-74EB-4096-B68A-7C398E827AC6",
            "active": "false"
        },
        "PLATFORM_DIGITAL_LIBRARY": {
            "platform_user_id": "123456789",
            "api_key": "D437B971-178B-4E37-974F-FA0C6808900A",
            "active": "true"
        }
    }
}
```


---

### ORGANIZATIONS

The following are available gRPC methods for user organizations endpoints.

#### `AddOrganization()`
> Creates an organization entry for a non-admin user.

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/user/:user_id/organization/`

##### Accepted Request Arguments
- `user_id` _(string)_
- `organization` _(map`<string, string>`)_

##### Response
- `added` _(bool)_
- `message` _(string)_
	- "Organization (name: `<Organization.name>`) has been added to User (email: `<User.email>`)"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
	organization: req.body.organization
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.AddOrganization(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/user/johndoe@example.com/organization \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"organization": {
		"101": "Another Org"	
	}
}'
```

##### JSON Response Example

```json
{
    "added": true,
    "message": "Organization (name: Another Org) has been added to User (email: johndoe@example.com)"
}
```


---

#### `DeleteOrganization()`
> Remove a user's organization by ID.

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/user/:user_id/organization/:organization_id/`

##### Accepted Request Arguments
- `user_id` _(string)_
- `organization_id` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "Organization ID `<organization_id>` for User (email: `<User.email>`) has been deleted"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id,
	organization_id: req.params.orgId
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.DeleteOrganization(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X DELETE /api/user/johndoe@example.com/organization/101 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "Organization (id: 101) for User (email: johndoe@example.com) has been deleted"
}
```


---

#### `ListOrganizations()`
- Retrieve all of a user's organizations.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/user/:user_id/organization/`

##### Accepted Request Arguments
- `user_id` _(string)_

##### Response
- `organizations` _(map`<string, string>`)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_id: req.params.id
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.ListOrganizations(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/user/johndoe@example.com/organization \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "organizations": {
        "1": "US Senate",
        "2": "S1T Test",
        "101": "Another Org"
    }
}
```
