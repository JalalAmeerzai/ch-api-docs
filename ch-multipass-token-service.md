# ch-multipass-token-service

## Purpose of This Service

The Multipass Token Service generates MultiPass tokens for use with signing into a Shopify account. See [https://help.shopify.com/en/api/reference/plus/multipass](https://help.shopify.com/en/api/reference/plus/multipass) for details.

## Repository
* [GitHub](https://github.com/stdev/ch-multipass-token-service)

## Object Model

TBD

## Ports Used
- **port:** `50059`
- **hport:** `5686`

## Access Tokens (JWT)

TBD

## Testing

### Unit Tests

TBD

## Functionality

The following contains details for the `GenerateToken` functionality of the Multipass Token Service.

### MULTIPASS TOKENS

The following are available gRPC methods for multipass token endpoints.

#### `GenerateToken()`
> Generates a Shopify MultiPass Token

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/multipass/`

##### Accepted Request Arguments
- `user` _(object)_
	- `email` _(string)_
	- `first_name` _(string)_
	- `last_name` _(string)_
	- `return_to` _(string)_

##### Response
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
		"last_name": "Doe",
		"return_to": "http://store.com/some_specific_location"
	}
}'
```
##### JSON Response Example

```json
{
    "multipass_token": "sVSwcIWAz_DKCEKWVqtzUxn5ZO3tj_DsORtNKG56soNs5mk5tIStJqIBwobSd6DQzBBj7Q9pZWDmiVHLBwWbPq8d3bcfrMF8uk_rapZGlMUF4Hhed-Iwq2d5y2i1Vl5VU3lHKmVtZJro52yXEboYxjTCZoBnM5d959k_A4LeWS6G6xHLmMzMNTnuN1v8QSU8z-V7QDY8AL6WYGLT_n8Pyg=="
}
```