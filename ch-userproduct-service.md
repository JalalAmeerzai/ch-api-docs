# ch-userproduct-service

## Purpose of This Service

- The UserProduct Service holds all the necessary data for those products in relation to the user and business needs.
- The UserProduct Service processes all CRUDL functionality for non-admin Users within the Customer Hub ecosystem.

## Repository
* [Github](https://github.com/stdev/ch-userproduct-service)

## Object Model
* [UserProduct Service Object Model](ObjectModels/UserProduct.md)

## Ports Used
- **port:** `50053`
- **hport:** `5680`

## Access Tokens (JWT)

_ALL_ API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

## Testing

### Unit Tests

Unit tests are included for this service. Current packages that include them are:

- `db` (All CRUDL functionality)

## Functionality

The following contains details for the `Create`, `Read`, `Update`, `Delete`, and `List` functionality of the UserProduct Service.

### USERPRODUCT

The following are available gRPC methods for general UserProduct endpoints.

#### `Create()`
> Initial creation of a user entry w/ products (new users only)

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/userproduct/`

##### Accepted Request Arguments
- `user_email` _(string)_
- `purchased_products` _(map`<string, Product>`)_

##### Response
- `created` _(bool)_
- `message` _(string)_
	- "The user (email: `<User.email>`) and their product entries have been created"

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
curl -X POST /api/userproduct \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
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
        }
    }
}'
```
##### JSON Response Example

```json
{
    "created": true,
    "message": "The user (email: johndoe@example.com) and their product entries have been created"
}
```

---

#### `AddProducts()`
> Add purchased product(s) to existing user

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/userproduct/add`

##### Accepted Request Arguments
- `user_email` _(string)_
- `purchased_products` _(map`<string, Product>`)_

##### Response
- `created` _(bool)_
- `message` _(string)_
	- "The user (email: `<User.email>`) and their product entries have been created"

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.AddProducts(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/userproduct/add \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
    "user_email": "johndoe@example.com",
    "purchased_products": {
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
    }
}'
```
##### JSON Response Example

```json
{
    "created": true,
    "message": "The product entries have been added to the user's data (email: johndoe@example.com)"
}
```

---

#### `Get()`
> Retrieve a single purchased product for a user.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/userproduct/:user_email/`

##### Accepted Request Arguments
- `user_email` _(string)_

##### Response
- `user_product` _(UserProduct object)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_email: req.params.email
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.Get(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/userproduct/johndoe@example.com \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "user_product": {
        "purchased_products": {
            "VID123D": {
                "authors": [
                    "Author 2"
                ],
                "media_metadata": {
                    "1": "01:21:49"
                },
                "title": "Some Video (D)",
                "subtitle": "",
                "purchase_date": "2019-06-10T00:00:00Z",
                "thumbnail_link": "somelocation/thumbnail.jpg",
                "product_format": "FORMAT_VIDEO",
                "can_access": "true",
                "last_accessed": null,
                "registration_promo_item": "false"
            },
            "VID123E": {
                "authors": [],
                "media_metadata": {
                    "1": "01:21:49"
                },
                "title": "Some Video (E)",
                "subtitle": "",
                "purchase_date": "2019-06-10T00:00:00Z",
                "thumbnail_link": "somelocation/thumbnail.jpg",
                "product_format": "FORMAT_VIDEO",
                "can_access": "true",
                "last_accessed": null,
                "registration_promo_item": "false"
            },
            "VID123F": {
                "authors": [],
                "media_metadata": {
                    "1": "01:21:49"
                },
                "title": "Some Video (F)",
                "subtitle": "",
                "purchase_date": "2019-06-10T00:00:00Z",
                "thumbnail_link": "somelocation/thumbnail.jpg",
                "product_format": "FORMAT_VIDEO",
                "can_access": "true",
                "last_accessed": null,
                "registration_promo_item": "false"
            },
            "AF02384W": {
                "authors": [
                    "Author 1"
                ],
                "media_metadata": {
                    "1": "00:16:23"
                },
                "title": "Longing and Belonging: Audio Download",
                "subtitle": "",
                "purchase_date": "2019-06-10T00:00:00Z",
                "thumbnail_link": "somelocation/thumbnail.jpg",
                "product_format": "FORMAT_AUDIO",
                "can_access": "true",
                "last_accessed": null,
                "registration_promo_item": "false"
            },
            "BK05619W": {
                "authors": [
                    "Author 3",
                    "Author 4"
                ],
                "media_metadata": {
                    "current_page": "16"
                },
                "title": "Making Magic: eBook",
                "subtitle": "",
                "purchase_date": "2019-06-10T00:00:00Z",
                "thumbnail_link": "somelocation/thumbnail.jpg",
                "product_format": "FORMAT_EBOOK",
                "can_access": "true",
                "last_accessed": null,
                "registration_promo_item": "false"
            },
            "CE05948": {
                "authors": [
                    "John O'Donohue"
                ],
                "media_metadata": {},
                "title": "The Untethered Soul at Work: CE Credits",
                "subtitle": "",
                "purchase_date": "2019-06-10T00:00:00Z",
                "thumbnail_link": "somelocation/thumbnail.jpg",
                "product_format": "FORMAT_CE_CREDITS",
                "can_access": "true",
                "last_accessed": null,
                "registration_promo_item": "true"
            }
        },
        "user_email": "johndoe@example.com",
        "recent_product": "",
        "created_at": "2019-11-01T21:01:26Z",
        "last_modified": "2019-11-01T21:01:29Z"
    }
}
```

---

#### `GetProduct()`
> Retrieve a single purchased product for a user.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/userproduct/:user_email/sku/:sku/`

##### Accepted Request Arguments
- `user_email` _(string)_
- `sku` _(string)_

##### Response
- `user_product` _(map`<string, Product>`)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_email: req.params.email,
	sku: req.params.sku
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.GetProduct(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/userproduct/johndoe@example.com/sku/CE05948 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "user_product": {
        "CE05948": {
            "authors": [
                "John O'Donohue"
            ],
            "media_metadata": {},
            "title": "The Untethered Soul at Work: CE Credits",
            "subtitle": "",
            "purchase_date": "2019-06-10T00:00:00Z",
            "thumbnail_link": "somelocation/thumbnail.jpg",
            "product_format": "FORMAT_CE_CREDITS",
            "can_access": "true",
            "last_accessed": null,
            "registration_promo_item": "true"
        }
    }
}
```

---

#### `Update()`
> Update [flat] data for a UserProduct entity/row

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/userproduct/:user_email/`

##### Accepted Request Arguments
- `user_email` _(string)_
- `user_product` _(object)_

##### Response
- `updated` _(string)_
- `message` _(string)_
	- "The UserProduct entry (for email: `<UserProduct.UserEmail>`) has been updated"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_email: req.params.email,
	user_product: req.body.user_product
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
curl -X PUT /api/userproduct/johndoe@example.com \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"user_product": {
		"recent_product": "CE05948"	
	}
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "The UserProduct entry (for email: johndoe@example.com) has been updated"
}
```

---

#### `UpdateProduct()`
> Update a product (SKU) within a UserProduct entity/row

_**NOTES:**_

- Be aware that the `authors` attribute will be _overwritten_ if you try to modify it. For instance, if you need to append an author to the list, you must pass **all** authors in the list.
- You cannot remove **all** others via this gRPC method (i.e. you will only be able to get down to one author in the list).
- When updating the `media_metadata` field with this method, you'll need to pass any and all data that you want to keep (similar to authors).

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/userproduct/:user_email/product/`

##### Accepted Request Arguments
- `user_email` _(string)_
- `sku` _(string)_
- `product` _(object)_

##### Response
- `updated` _(string)_
- `message` _(string)_
	- "The user's (email: `<User.email>`) product (SKU: `<UserProduct.SKU>`) has been updated"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_email: req.params.email,
	sku: req.body.sku,
	product: req.body.product
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.UpdateProduct(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/userproduct/johndoe@example.com \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
    "sku": "VID123D",
    "product": {
    	"title": "Some Video 999",
        "subtitle": "A Subtitle",
        "thumbnail_link": "somelocation/thumbnail999.jpg",
        "media_metadata": {
            "1": "05:38:17"
        }
    }
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "The user's (email: johndoe@example.com) product (SKU: VID123D) has been updated"
}
```

---

#### `UpdateEmail()`
> Update a user's email in a UserProduct entity/row

**WARNING:** calling this method directly *without* changing the [User Service primary key (email)](ch-user-service/#updateemail)
will result in a user's products not associating to the user.
_ALWAYS_ use this functionality with care and through the User Service unless a necessary reason exists to call this method directly.

**NOTE:** This gRPC method receives a call when `User:UpdateEmail()` gets called because `UserProduct.UserEmail` is a dependency (FK) of `User.Email`.

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/userproduct/:user_email/email/`

##### Accepted Request Arguments
- `user_email` _(string)_
- `new_user_email` _(string)_

##### Response
- `updated` _(string)_
- `message` _(string)_
	- "The User (email: `<User.email>`) has been updated (previous email: `<User.email>`)"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_email: req.params.email,
    new_user_email: req.body.new_email
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
curl -X PUT /api/userproduct/email/johndoe@example.com \
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
    "message": "The User (email: johndoe99@example.com) has been updated (previous email: johndoe@example.com)"
}
```

---

#### `DeleteEntity()`
> Delete a UserProduct entity/row

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/userproduct/:user_email/`

##### Accepted Request Arguments
- `user_email` _(string)_

##### Response
- `deleted` _(string)_
- `message` _(string)_
	- "User (email: `<User.email>`) and ALL of their purchased product records have been deleted"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_email: req.params.email,
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.DeleteEntity(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X DELETE /api/userproduct/johndoe@example.com \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "User (email: johndoe@example.com) and ALL of their purchased product records have been deleted"
}
```

---

#### `Delete()`
> Remove a SKU from a user's purchased products

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/userproduct/:user_email/sku/:sku/`

##### Accepted Request Arguments
- `user_email` _(string)_
- `sku` _(string)_

##### Response
- `deleted` _(string)_
- `message` _(string)_
	- "The user's (email: `<User.email>`) product (SKU: `<UserProduct.SKU>`) has been deleted"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_email: req.params.email,
	sku: req.params.sku
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
curl -X DELETE /api/userproduct/johndoe@example.com/sku/VID123D \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "The user's (email: johndoe@example.com) product (SKU: VID123D) has been deleted"
}
```

---

#### `DeleteMediaMetadata()`
> Remove `media_metadata` (user progress) data for a particular product

**NOTE:** Use this for the reset functionality required by the Digital Library.

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/userproduct/:user_email/media/:sku/`

##### Accepted Request Arguments
- `user_email` _(string)_
- `sku` _(string)_

##### Response
- `deleted` _(string)_
- `message` _(string)_
	- "The media metadata has been deleted for user's (email: `<UserProduct.UserEmail>`) product (SKU: `<UserProduct.Sku>`)"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	user_email: req.params.email,
	sku: req.params.sku
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.DeleteMediaMetadata(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X DELETE /api/userproduct/johndoe@example.com/sku/VID123D \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "The media metadata has been deleted for user's (email: johndoe@example.com) product (SKU: VID123D)"
}
```

---

#### `ListUserProducts()`
> Retrieve all UserProduct entities/rows.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/userproduct/`

##### Accepted Request Arguments
- _N/A_

##### Response
- `count` _(int64)_
- **repeated** `user_products` _(UserProduct objects)_

##### gRPC Request Example (JS)
```js
// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.ListUserProducts({}, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/userproduct/ \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "user_products": [
        {
            "purchased_products": {
                "BK05619W": {
                    "authors": [
                        "Author 3",
                        "Author 4"
                    ],
                    "media_metadata": {
                        "current_page": "16"
                    },
                    "title": "Making Magic: eBook",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_EBOOK",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                },
                "CE05948": {
                    "authors": [
                        "John O'Donohue"
                    ],
                    "media_metadata": {},
                    "title": "The Untethered Soul at Work: CE Credits",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_CE_CREDITS",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "true"
                },
                "VID123D": {
                    "authors": [
                        "Author 2"
                    ],
                    "media_metadata": {
                        "1": "01:21:49"
                    },
                    "title": "Some Video (D)",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_VIDEO",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                },
                "VID123E": {
                    "authors": [],
                    "media_metadata": {
                        "1": "01:21:49"
                    },
                    "title": "Some Video (E)",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_VIDEO",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                },
                "VID123F": {
                    "authors": [],
                    "media_metadata": {
                        "1": "01:21:49"
                    },
                    "title": "Some Video (F)",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_VIDEO",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                },
                "AF02384W": {
                    "authors": [
                        "Author 1"
                    ],
                    "media_metadata": {
                        "1": "00:16:23"
                    },
                    "title": "Longing and Belonging: Audio Download",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_AUDIO",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                }
            },
            "user_email": "johndoe@example.com",
            "recent_product": "",
            "created_at": "2019-11-01T21:01:26Z",
            "last_modified": "2019-11-01T21:01:29Z"
        },
        {
            "purchased_products": {
                "ABC123": {
                    "authors": [
                        "Some Author 1"
                    ],
                    "media_metadata": {
                        "1": "00:31:16",
                        "2": "00:27:37",
                        "3": "01:05:26"
                    },
                    "title": "Video Presentation",
                    "subtitle": "Awesome Sauce",
                    "purchase_date": "2019-09-06T00:00:00Z",
                    "thumbnail_link": "",
                    "product_format": "FORMAT_VIDEO",
                    "can_access": "true",
                    "last_accessed": {
                        "track": "2",
                        "access_time": "2019-10-07T06:30:57Z"
                    },
                    "registration_promo_item": "false"
                },
                "ABC-234": {
                    "authors": [
                        "Some Author 2",
                        "Some Author 3"
                    ],
                    "media_metadata": {
                        "1": "00:25:44"
                    },
                    "title": "Audio Presentation",
                    "subtitle": "Cool Deal",
                    "purchase_date": "2019-09-06T00:00:00Z",
                    "thumbnail_link": "",
                    "product_format": "FORMAT_AUDIO",
                    "can_access": "true",
                    "last_accessed": {
                        "track": "1",
                        "access_time": "2019-10-08T15:45:12Z"
                    },
                    "registration_promo_item": "false"
                }
            },
            "user_email": "dynamodb@example.com",
            "recent_product": "ABC123",
            "created_at": "2019-11-01T20:27:12Z",
            "last_modified": "2019-11-01T20:27:12Z"
        },
        {
            "purchased_products": {
                "VID123F": {
                    "authors": [],
                    "media_metadata": {
                        "1": "01:21:49"
                    },
                    "title": "Some Video (F)",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_VIDEO",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                },
                "VID123D": {
                    "authors": [],
                    "media_metadata": {},
                    "title": "Some Video (D)",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_VIDEO",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                },
                "AF02384W": {
                    "authors": [],
                    "media_metadata": {
                        "1": "00:16:23"
                    },
                    "title": "Longing and Belonging: Audio Download",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_AUDIO",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                },
                "BK05619W": {
                    "authors": [],
                    "media_metadata": {
                        "current_page": "16"
                    },
                    "title": "Making Magic: eBook",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_EBOOK",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                },
                "VID123E": {
                    "authors": [],
                    "media_metadata": {},
                    "title": "Some Video (E)",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_VIDEO",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "false"
                },
                "CE05948": {
                    "authors": [],
                    "media_metadata": {},
                    "title": "The Untethered Soul at Work: CE Credits",
                    "subtitle": "",
                    "purchase_date": "2019-06-10T00:00:00Z",
                    "thumbnail_link": "somelocation/thumbnail.jpg",
                    "product_format": "FORMAT_CE_CREDITS",
                    "can_access": "true",
                    "last_accessed": null,
                    "registration_promo_item": "true"
                }
            },
            "user_email": "johndoe99@example.com",
            "recent_product": "CE05948",
            "created_at": "2019-10-16T19:48:24Z",
            "last_modified": "2019-10-25T17:07:26Z"
        }
    ],
    "count": "3"
}
```

---