# ch-shopify-helper-service

## Purpose of This Service

The Shopify Helper Service is for Shopify to utilize the Customer Hub user login/logout processes.

## Repository
* [GitHub](https://github.com/stdev/ch-shopify-helper-service)

## Object Model

TBD

## Ports Used
- **port:** `50060`
- **hport:** `5687`

## Access Tokens (JWT)

N/A

## Testing

### Unit Tests

TBD

## Functionality

The following contains details for the functionality of the Shopify Helper Service.

### SHOPIFY HELPER METHODS

The following are available gRPC methods for shopify helper endpoints.

#### `ShopifyLogin()`
> Generates a Shopify MultiPass Token

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/multipass/`

##### Accepted Request Arguments
- `password` _(string)_ - _**this must be base64 encoded!**_
- `user` _(object)_
	- `email` _(string)_
	- `first_name` _(string)_
	- `last_name` _(string)_
	- `return_to` _(string)_

##### Response
- `token_set` _(AWSCognitoTokenSet object)_
	- `aws_jwt_access_token` _(string)_
	- `aws_jwt_refresh_token` _(string)_
	- `token_type` _(string)_
	- `token_expires` _(int64)_
- `multipass_token` _(string)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.GenerateToken(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/multipass \
  -H 'Content-Type: application/json' \
  -d '{
	"user": {
		"email": "johndoe@example.com",
		"first_name": "John",
		"last_name": "Doe"
	}
}'
```
##### JSON Response Example

```json
{
    "multipass_token": "sVSwcIWAz_DKCEKWVqtzUxn5ZO3tj_DsORtNKG56soNs5mk5tIStJqIBwobSd6DQzBBj7Q9pZWDmiVHLBwWbPq8d3bcfrMF8uk_rapZGlMUF4Hhed-Iwq2d5y2i1Vl5VU3lHKmVtZJro52yXEboYxjTCZoBnM5d959k_A4LeWS6G6xHLmMzMNTnuN1v8QSU8z-V7QDY8AL6WYGLT_n8Pyg=="
}
```