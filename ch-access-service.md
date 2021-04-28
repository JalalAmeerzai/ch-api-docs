# ch-access-service

## Purpose of This Service

This service handles user registrations. During a user registration, the **Access Service** endpoint will be called (as opposed to the **User Service**). The **Access Service** will register a user in the following steps:

1. Verify recaptcha response from client
    - bypass if the email from the token matches the value of CtxKey
2. Call the **Auth Service** and to create the new user in AWS Cognito and retrieve an **Access Token**.
3. Call the **Multipass Token Service** to generate a MultiPass token (for Shopify).
4. Call the **Organization Service** to see if the user's email matches one of Sounds True's recognized organizations.
5. Call the **User Service** to add a create the new user in the Customer Hub.
6. Call the **UserProduct Service** to associate purchased and FREE gift products (including Organization gift products) to the user's account in the Customer Hub. It also associates matching items from Guest Checkouts (based on `email`).
7. If there are any courses, try and register them in Thinkific throught the Thinkific service

Please see [_Architecture Diagrams_](https://drive.google.com/file/d/1cyy6wLrcHLeN8LrffshQYgKdVKkuexhJ/view?usp=sharing), _User Flows_ tab for the _User Registration Process_.

## Repository
* [Github](https://github.com/stdev/ch-access-service)

## Object Model
* [Access Service Object Model](ObjectModels/Access.md)

## Ports Used
- **port:** `50054`
- **hport:** `5681`

## Access Tokens (JWT)

No Access Token is required for `RegisterUser()`. However, most other endpoints require the AWS JWT.

_MOST_ API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All _applicable_ calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

## Functionality

The following contains details for the functionality of the Access Service.

### USER REGISTRATION

The following are available gRPC methods for general user registration endpoint(s).

#### `RegisterUser()`
> Registers a new user and associates any purchased and FREE gift products (including Organization gift products) to the user's account

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/access/`

##### Accepted Request Arguments
- `user` _(string)_ 
	- see the [User Object Model](ObjectModels/User.md) for details of what can be passed in.
- `password` _(string)_
	- _**This must be base64 encoded!**_
- `userproduct` _(bool)_
	- see the [UserProduct Object Model](ObjectModels/UserProduct.md) for details of what can be passed in.
- `g_recaptcha_response` _(string)_
    - The reCaptcha token that Google returns for client-side implementations.
- `multipass_return_to` _(string)_
	- _[For Shopify Only]_ The location of where a user is redirected to after being logged into Shopify (added to the Multipass token) (e.g. `https://soundstrue.com/checkout`)

##### Response
- `registered` _(bool)_
- `message` _(string)_
	- If only user registration success, "User email (`<User.email>`) has been registered."
	- If user + product(s) success, "User email (`<User.email>`) has been registered and the purchased digital products have been added to the Digital Library."
	- If user success BUT product(s) error, "User email (`<User.email>`) has been registered but there was an error adding products to the Digital Library."
- `token_set` _(AWSCognitoTokenSet object)_
	- `aws_jwt_access_token` _(string)_
	- `aws_jwt_refresh_token` _(string)_ **(returned for <ins>_standard_</ins> auth flow only)**
	- `token_type` _(string)_
	- `token_expires` _(int64)_
- `multipass_token` _(string)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.RegisterUser(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/access \
  -H 'Content-Type: application/json' \
  -d '{
    "user": {
        "email": "john.doe@example.com",
        "first_name": "John",
        "last_name": "Doe",
        "phone": "111-111-1111",
        "registered_from": "REGISTRATION_SHOPIFY",
        "platforms": {
        	"PLATFORM_SHOPIFY": {
                "platform_user_id": "9876543213",
                "active": "true"
            }
        },
        "accepts_marketing": "true",
        "accepts_marketing_updated_at": "2019-06-10T00:00:00Z",
        "marketing_opt_in_level": "MARKETING_UNKNOWN"
    },
    "password": "VGVzdGluZyMjIzEyMzQhIQ==",
    "g_recaptcha_response": "03AGdBq25nwKYYFZmiD-NjJkRKs98cdNjsu1OjUyGrLPCwCqiAfoIRJg_Ey9Fm32PKA78NUM-xkyIox6IYk4PkyrmK4xs5D8rNbdDG6zk9XOX3_Suh3YZxvzR2zhcA_7wgmqypS6ylDFD3CQU9CyEf3ymt8c5GHXJURcDe8LQnIMJyuGpy7ncUUZOF6Q5d6mfA3_Ypr3-7UdRnhNSllQDWSHf3wFbH40PaSk_mh6ZM9ee3x69cvVU6JUTdaS3p-qjTudfMCOWrkqcrtOKFQ_QS3iB2uLHN1ZuotVtB6m0mYsoXCEadAUpIz_HHaK4suDDyVhrUPooshs2BtcTkvqAb-6_XsaECiUjcTuza0z9BPzLtOR0Wp8qxMW5CWEsA4Hsfgq4nppsEd_wBkmiR7RcsVARsYT0FT-ToEn0K1zR2cJeHpatopU8lj70t6ybLIvMRgBoru-XQS6inE9zwZhuknEr3tZ5b6FABuA",
    "multipass_return_to": "https://www.soundstrue.com/example",
    "purchased_products": {
		"CE05948": {
			"title": "The Untethered Soul at Work: CE Credits",
			"subtitle": "",
			"purchase_date": "2019-06-10T00:00:00Z",
			"thumbnail_link": "somelocation/thumbnail.jpg",
			"product_format": "CE Credits"
		}
    }
}'
```

##### JSON Response Example

```json
{
    "registered": true,
    "message": "User email (john.doe@soundstrue.com) has been registered and the purchased digital products have been added to the Digital Library.",
    "token_set": {
        "aws_jwt_access_token": "eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiIyZjk5OGYxOC1hYWQ2LTRiMWEtOWIwOC00MTAxYTRjNjIwY2YiLCJldmVudF9pZCI6ImUyNWEyZTM1LWQxYmEtNDJiMy1iYmE1LWE1NTFmYjU0MDMwZCIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1Nzk2NTE1ODcsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3OTY1NTE4NywiaWF0IjoxNTc5NjUxNTg3LCJqdGkiOiI3MGRjYmUwNi00ZGI0LTQxMDYtYWRhYS05NjY4ZDY1Y2Q3NTIiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiMmY5OThmMTgtYWFkNi00YjFhLTliMDgtNDEwMWE0YzYyMGNmIn0.ndVx8OEHg6F2vg5gnxTOtpsIrz8mLvSq16EtXZULM89baN3ux94iquSSgs-k5bdZTkYKnXW6rCYUK194wBABvj37GqOy6ukUwNToZiCAmt-WjhR0sw5li4BLzBGa_hTVAhbCzpS_aYA55YtCJhcHmBj7xep2oE1uynWKDUiz3kpuxlBfc3oF9WWOC_24xb9CXrjhzj5KfhgPFExM3zxCNDshqDsNH7vdyciJzoUl5HNhE4AIHxZyWJ_OJBZWBzFcGO8xNck_3E0U9BLP040wuwxi5DRAM-Cfm-pbYWfIyXibtm6FQDu2EabfcHn_pK03NstjXqH3WLGIIHZ7U632tQ",
        "aws_jwt_refresh_token": "eyJjdHkiOiJKV1QiLCJlbmMiOiJBMjU2R0NNIiwiYWxnIjoiUlNBLU9BRVAifQ.Yjf01zupFRfYhNOBFXzMdyY8GIkGme1LDnmiB-2XjbWJHZyVLheAHLA1foHuTepRbr2HAWQlxhTuKTKYFFDwc9taT937QrjedLc4JFVU_reu1amTZL79oCLiEYida5YjtzxWRqZ1it7pyRi1EVkt69lNytNRZGoKftu3KJDsmbiWovseTBEy--8QnkAjraX7FXBHaB66t52On3zJtfNFNWEm37b-1AWO4_b8t_Z8h8LR21hcMUG2C4kKA_WQlA3U2k-RnlfOKdbh1smBsU4zQfL8xjx3hAWAMhSgTmDeKo_CxCAUw0HlPNZNZX-wo5UUPx59WZBCA8SQ0yDiojCurQ.W9__RitbU4U9Wl6H.DIykTA2HaLy4Z6YGC5Duui677Q_8GAZFoxftBobjYOYokl67pSAnj_fjvDAnsniiP_T9n__n-9-Bjgvz7wctm2X7uS1yadkXDs72-NSDM2488OrReCC8Vgq0w6uoSBmxfadYM_qWE-U2X70u5sEq5hm2oPv_nclw87Iu9TSE--UnPY-i2Ttdd9pLB019uh9mUUICUMGpfY2kQLeWZRvAr3c9NvT_luOoB31bBEK2TuHPjUDl98ckehhuYR4VuVkqLvXJtaw1osX2hQFT4CVQXs6aLQiJYAHDC568d-R2RS0taDAy_5Sf7zgtSfR-vbUAuC-33jHVUAFAhiSJoCm9_FzW7RXNig6dvLbYX94ZGAUwGHilsJ6QagQG5MLstnAgdXHmGgMR3cJ-rXM0K4Vf0RK2qTezBsHmVNiBPI0trBhnTQPVK1A8dMEtmHrKj2zi-r9wpLBz7cUeQr4Zlcmse9wUQqVdUxAM9-Dn7mWmUh_1ezstiJXmdfi90KNYKWKgz7pu-J_Qx0k1xqd4QBEXlbPVuDi_waMHBBl-sSaBzRQOLdrW9U4WS2nL_hsVbrw2WM8TY9WI5zl4MvEljKkR5xm9higAZ96azDiw_mK123y3g-rxXPvamLmeqnBt1lO5acRjIvFIsCrPMsNzu8vtP6iY16L-1j2LwSE-1i6V22V5-9cGHZ3lK8I3oHqETb58lOoeARhKlsTwX7OLEnbRI7cflVApOFOvcPuM2V9v_IDGZ6i5r2nsrB0jQrcI7M3z7lNBGL2Ky41ZfRlTJUY70-XpOTmhNOdunj4Ssd_ypWgI_E-qRugIRYc9h_9klYGFEZertc3K2NzTfcLmm9wulxJUkl8dW5Y7UkFm3YSHde16AeiLGs5GY9TVsIyyLaOFD7lVgCr6F61xU1qmwTZKU9OHzpJICBXQmVYr0PdfdV6RBrMcuuS2Qu2u1DHjDr4vUbpa1Hu5YKl_O9G6Tl29MCMoEp4XMEe6EILo8dIyrXkHQ6taSmiHPIzwRvmhRMK-sZbA3nUE13oGbJmTQ7I-rhwFn9GqsgUEHpo0Cn1BNeztW1ruUC47qV_VlPwFw3Yg5YzT0n9NhGgrbtVXtoqmxpNWFG-2b6TN5sG6kctG9BqeEG_HQBzqmAltEvF1tOUHJ60p0_-TqDIJZFNYvx8eQAnNDniVddL1YuBHXKR1PaiBi9bqwd8shs23DXX8wacmGOE81lGNDs-b3WmR-UJXgSYMkGuhhFf9A5I4biVEeTDdj3OKvxSgiY_f9jbF8nMhGwc4CLJJUBNVHHwQvi0AcxbMbgt9J-i1seFkbd366nGTxZGBXO6KOrFucIHRVenO.gpJLyACnY_frHfvJG-742Q",
        "token_type": "Bearer",
        "token_expires": "3600"
    },
    "multipass_token": "6rDMYwOkxgHeHmfQgm-cDoqpRoZXnqFeydS_XKIqMOitetTBcpcf-Fxqjet1Tm9FLWyPHCGwiwFba7PZF1w7nteSqXrV0uN8-uekCM8zbmHqLbUfizxgUM1135j2EyftW6FisQnRT3GP9XfI06tNoq97mSqkeeoTBvELkZprBmEiKrJqfdAKM2NcnicFjin1lmhb38-R-8AYbTPXQX_qFdYf6MAcDwlxTvXkCqjCEAE="
}
```

### PROMOTIONAL PRODUCTS

The following are available gRPC methods for admin user endpoints.

#### `CreateGiftProduct()`
> Creates a gift product.

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/access/gift/`

##### Accepted Request Arguments
- `sku`_(string)_
- `product` _(Product object)_

##### Response
- `created` _(bool)_
- `message` _(string)_
	- "Product with (sku: `<sku>`) has been created"

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.CreateGiftProduct(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```
##### cURL Request Example

```bash
curl -X POST /api/access/gift/ \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
    "sku": "N123",
	"product": {
        "title": "An ebook",
        "subtitle": "Subtitle",
        "thumbnail_link": "somewhere/something2.jpg",
        "product_format": "FORMAT_EBOOK",
        "registration_promo_item": "true",
        "authors": ["Jane Doe", "Carrol"]
	}
}'
```
##### JSON Response Example

```json
{
    "created": true,
    "message": "Product with (SKU: N123) has been created""
}
```

---

#### `CreateOrgProduct()`
> Creates an organization's product.

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/access/org/`

##### Accepted Request Arguments
- `org_id`_(string)_
- `products` _(map`<string, Product>`)_

##### Response
- `created` _(bool)_
- `message` _(string)_
	- "Product with (orgID: `<org_id>`) has been created"

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.CreateOrgProduct(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```
##### cURL Request Example

```bash
curl -X POST /api/access/gift/ \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"org_id":121,
	"products":{
		"S987": {
		"title": "An ebook",
		"subtitle": "Subtitle",
		"thumbnail_link": "somewhere/something2.jpg",
		"product_format": "FORMAT_EBOOK",
		"registration_promo_item": "true",
		"authors": ["Jane Doe", "Carrol"]
		},
		"S123":{
		"title": "An ebook",
		"subtitle": "Subtitle",
		"thumbnail_link": "somewhere/something2.jpg",
		"product_format": "FORMAT_EBOOK",
		"registration_promo_item": "true",
		"authors": ["Jane Doe", "Carrol"]
		}
	}
}'
```
##### JSON Response Example

```json
{
    "created": true,
    "message": "Product with (orgID: 121) has been created""
}
```

---

#### `GetGiftProduct()`
> Retrieve a gift product.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/access/gift/sku/:sku`

##### Accepted Request Arguments
- `sku`_(string)_

##### Response
- `product` _(GiftProduct object)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	sku: req.params.sku
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.GetGiftProduct(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```
##### cURL Request Example

```bash

curl -X GET /api/access/gift/sku/N123 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```
##### JSON Response Example

```json
{
    "product": {
        "sku": "N123",
        "product": {
            "authors": [
                "Jane Doe",
                "Carrol"
            ],
            "title": "New Title",
            "subtitle": "Subtitle",
            "thumbnail_link": "somewhere/something2.jpg",
            "product_format": "FORMAT_EBOOK",
            "registration_promo_item": "true"
        }
    }
}
```

---

#### `GetOrgProduct()`
> Retrieve an organization's product.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/access/org/id/:org_id'`

##### Accepted Request Arguments
- `org_id`_(string)_

##### Response 
- `org_product` _(OrganizationProduct object)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	org_product: req.params.id
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.GetOrgProduct(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```
##### cURL Request Example

```bash

curl -X GET /api/access/org/id/111 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```
##### JSON Response Example

```json
{
    "org_product": {
        "products": {
            "ABC123": {
                "authors": [
                    "Jane Doe"
                ],
                "title": "An ebook",
                "subtitle": "Subtitle",
                "thumbnail_link": "somewhere/something2.jpg",
                "product_format": "FORMAT_VIDEO",
                "registration_promo_item": "true"
            }
        },
        "org_id": "111"
    }
}
```

---
#### `UpdateGiftProduct()`
> Update data for a GiftProduct

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/access/api/gift/`

##### Accepted Request Arguments
- `sku` _(string)_
- `product` _(object)_

##### Response
- `updated` _(bool)_
- `message` _(string)_
	- "Product (SKU: `<sku>`) has been updated"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	sku: req.body.sku,
	product: req.body.product
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.UpdateGiftProduct(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/access/gift/ \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"sku": "N123",
	"product": {
	"title": "An ebook",
	"subtitle": "Subtitle",
	"thumbnail_link": "somewhere/something2.jpg"
	}
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "Product (SKU: N123) has been updated"
}
```

---
#### `UpdateOrgProduct()`
> Update a product (SKU) within a OrgProduct map

_**NOTES:**_

- Be aware that the `authors` attribute will be _overwritten_ if you try to modify it. For instance, if you need to append an author to the list, you must pass **all** authors in the list.
- You cannot remove **all** others via this gRPC method (i.e. you will only be able to get down to one author in the list).

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/access/org/`

##### Accepted Request Arguments
- `org_id` _(string)_
- `sku` _(string)_
- `product` _(object)_

##### Response
- `updated` _(bool)_
- `message` _(string)_
	- "Product (orgID: `org_id`) has been updated"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	org_id: req.body.org_id,
	sku: req.body.sku,
	product: req.body.product
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.UpdateOrgProduct(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/access/org/ \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"org_id":345,
	"sku": "S987",
	"product":{
		"subtitle": "New Subtitle"
	}
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "Product (orgID: 345) has been updated"
}
```

---
#### `DeleteGiftEntity()`
> Delete a Product entity/row from the access_gifts table

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/access/sku/:sku'`

##### Accepted Request Arguments
- `sku` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "Product (SKU: `<sku>`) has been deleted

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	sku: req.params.sku,
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.DeleteGiftEntity(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X DELETE /api/access/sku/N26 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "Product (SKU: N26) has been deleted"
}
```

---
#### `DeleteOrganizationEntity()`
> Delete a Product entity/row from the access_organization table

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `api/access/org_id/:org_id'`

##### Accepted Request Arguments
- `org_id` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "Product (orgID: `<org_id>`) has been deleted

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	org_id: req.params.org_id,
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.DeleteOrganizationEntity(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X DELETE /api/access/org_id/7890 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "Product (orgID: 7890) has been deleted"
}
```

---
#### `Delete()`
> Remove a SKU from a organization's free products

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `api/access/org_id/:org_id/sku/:sku'`

##### Accepted Request Arguments
- `org_id` _(string)_
- `sku` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "Product (SKU: `<sku>`) has been deleted

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	org_id: req.params.org_id,
	sku: req.params.sku,
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
curl -X DELETE /api/access/org_id/7890/sku/S123 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "Product (SKU: S123) has been deleted"
}
```

---
#### `ListGiftProducts()`
> Retrieve all GiftProduct entities/rows from.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/access/gift/filters/:limit/:key?`
    - `key` (a.k.a. `exclusive_start_key`) - The SKU of the last product retrieved (the result of `last_evaluated_key` in the response) _(optional)_

##### Accepted Request Arguments
- `limit` _(string)_
    - the _amount_ of results to show (i.e. the page amount)
- `exclusive_start_key` _(string)_ (`key` in REST endpoint)
    - This is the result (SKU) to start the next page set from. This should equal the value of `last_evaluated_key` in the reponse, if applicable.

##### Response
- `count` _(int64)_
- **repeated** `product` _(GiftProduct objects)_
- `last_evaluated_key` _(string)_ 
    - This is the last `sku` evaluated by the DB. This should be passed as the `exclusive_start_key` in a subsequential call (i.e. make another of the same call passing the value of `last_evaluated_key` to `exclusive_start_key`).

##### gRPC Request Example (JS)
```js
// set auth header
let payload = {
    limit: req.params.limit,
    exclusive_start_key: req.params.key
};
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.ListGiftProducts(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

_**NOTE:** The following example call is with a pagination of 10, starting at the beginning (first call), and there are only 3 results. Therefore the `last_evaluated_key` is empty.

```bash
curl -X GET /api/access/gift/filters/100 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "product": [
        {
            "sku": "ABC123",
            "product": {
                "authors": [
                    "Jane Doe"
                ],
                "title": "An ebook",
                "subtitle": "Subtitle",
                "thumbnail_link": "somewhere/something2.jpg",
                "product_format": "FORMAT_VIDEO",
                "registration_promo_item": "true"
            }
        },
        {
            "sku": "BC6116W",
            "product": {
                "authors": [
                    "Various Authors"
                ],
                "title": "Walking the Path: A Collection of Spiritual Teachings - Audio Download",
                "subtitle": "",
                "thumbnail_link": "https://cdn.shopify.com/s/files/1/0253/2822/2307/products/pathresize.png?v=1583900205",
                "product_format": "FORMAT_AUDIO",
                "registration_promo_item": "true"
            }
        },
        {
            "sku": "BC6115W",
            "product": {
                "authors": [
                    "Various Authors"
                ],
                "title": "An Experience of Mindfulness - Audio Download",
                "subtitle": "",
                "thumbnail_link": "https://cdn.shopify.com/s/files/1/0253/2822/2307/products/samplemindfulness_1.png?v=1583874814",
                "product_format": "FORMAT_AUDIO",
                "registration_promo_item": "true"
            }
        }
    ],
    "count": "3",
    "last_evaluated_key": ""
}
```

---

#### `ListOrgProducts()`
> Retrieve all GiftProduct entities/rows from.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/access/org/filters/:limit/:key?`
    - `key` (a.k.a. `exclusive_start_key`) - The SKU of the last product retrieved (the result of `last_evaluated_key` in the response) _(optional)_

##### Accepted Request Arguments
- `limit` _(string)_
    - the _amount_ of results to show (i.e. the page amount)
- `exclusive_start_key` _(string)_ (`key` in REST endpoint)
    - This is the result (Organization ID) to start the next page set from. This should equal the value of `last_evaluated_key` in the reponse, if applicable.

##### Response
- `count` _(int64)_
- **repeated** `product` _(OrganizationProduct objects)_
- `last_evaluated_key` _(string)_ 
    - This is the last `id` evaluated by the DB. This should be passed as the `exclusive_start_key` in a subsequential call (i.e. make another of the same call passing the value of `last_evaluated_key` to `exclusive_start_key`).

##### gRPC Request Example (JS)
```js
// set auth header
let payload = {
    limit: req.params.limit,
    exclusive_start_key: req.params.key
};
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.ListOrgProducts(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

_**NOTE:** The following example call is with a pagination of 10, starting at the beginning (first call), and there are only 1 result. Therefore the `last_evaluated_key` is empty.

```bash
curl -X GET /api/access/org/filters/100 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "product": [
        {
            "products": {
                "DD05188W": {
                    "authors": [
                        "Jack Kornfield",
                        "Tara Brach"
                    ],
                    "title": "Mindfulness Daily at Work - Online Learning",
                    "subtitle": "Create a Life-Changing Meditation Practice in Less Than 15 Minutes a Day!",
                    "thumbnail_link": "https://cdn.shopify.com/s/files/1/0253/2822/2307/products/mindfulness-daily-at-work_1.png?v=1583894278",
                    "product_format": "FORMAT_COURSE",
                    "registration_promo_item": ""
                }
            },
            "org_id": "1"
        },
        {
            "products": {
                "ABC123": {
                    "authors": [
                        "Jane Doe"
                    ],
                    "title": "An ebook",
                    "subtitle": "Subtitle",
                    "thumbnail_link": "somewhere/something2.jpg",
                    "product_format": "FORMAT_VIDEO",
                    "registration_promo_item": "true"
                }
            },
            "org_id": "111"
        },
        {
            "products": {
                "DD05188W": {
                    "authors": [
                        "Jack Kornfield",
                        "Tara Brach"
                    ],
                    "title": "Mindfulness Daily at Work - Online Learning",
                    "subtitle": "Create a Life-Changing Meditation Practice in Less Than 15 Minutes a Day!",
                    "thumbnail_link": "https://cdn.shopify.com/s/files/1/0253/2822/2307/products/mindfulness-daily-at-work_1.png?v=1583894278",
                    "product_format": "FORMAT_COURSE",
                    "registration_promo_item": ""
                }
            },
            "org_id": "12"
        }
    ],
    "count": "3",
    "last_evaluated_key": ""
}
```

---

#### `ListAllProducts()`
> Retrieve all GiftProduct entities/rows from.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/access/filters/:limit/keys/:gift_lek?/:org_lek?`
    - `gift_lek` (a.k.a. `exclusive_start_key` for **Gift Products**) - The `sku` of the last product retrieved (the result of `last_evaluated_key` in the response) _(optional)_
    - `org_lek` (a.k.a. `exclusive_start_key` for **Organization Products**) - The `org_id` of the last organization  (with its products) retrieved (the result of `last_evaluated_key` in the response) _(optional)_

##### Accepted Request Arguments
- `limit` _(string)_
    - the _amount_ of results to show (i.e. the page amount)
- `exclusive_start_keys` _(string)_ (`key` in REST endpoint)
    - This is the result (Organization ID) to start the next page set from. This should equal the value of `last_evaluated_key` in the reponse, if applicable.

##### Response
- `org_count` _(int64)_
- `gift_count` _(int64)_
- **repeated** `promo_product` _(PromoProduct objects)_
- `last_evaluated_keys` _(map)_ 
    - This is the last `id` evaluated by the DB. This should be passed as the `exclusive_start_key` in a subsequential call (i.e. make another of the same call passing the value of `last_evaluated_key` to `exclusive_start_key`).
    - `gift_lek` _(string)_
        - The `sku` of the last product retrieved (the result of `last_evaluated_key` in the response) (e.g. `"ABC123"`) _(optional)_
    - `org_lek` _(string)_
        - The `org_id` of the last organization (with its products) retrieved (e.g. `"6"`) _(optional)_

##### gRPC Request Example (JS)
```js
// set auth header
let payload = {
    limit: req.params.limit,
    exclusive_start_keys: {
        "gift_lek": req.params.gift_lek,
        "org_lek": req.params.org_lek
    }
};
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.ListAllProducts(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

_**NOTE:** The following example call is with a pagination of 5, starting at the beginning (first call)._ Further calls would be represented as the following examples show:
- `curl -X GET /api/access/filters/5/keys/6/ABC123` 
- `curl -X GET /api/access/filters/5/keys/12/ABC456` 

```bash
curl -X GET /api/access/filters/100/keys \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "promo_product": [
        {
            "authors": [
                "Jane Doe"
            ],
            "promo_type": "Gift",
            "sku": "ABC123",
            "org_id": "",
            "title": "An ebook",
            "subtitle": "Subtitle",
            "thumbnail_link": "somewhere/something2.jpg",
            "product_format": "FORMAT_VIDEO",
            "registration_promo_item": "true"
        },
        {
            "authors": [
                "Various Authors"
            ],
            "promo_type": "Gift",
            "sku": "BC6116W",
            "org_id": "",
            "title": "Walking the Path: A Collection of Spiritual Teachings - Audio Download",
            "subtitle": "",
            "thumbnail_link": "https://cdn.shopify.com/s/files/1/0253/2822/2307/products/pathresize.png?v=1583900205",
            "product_format": "FORMAT_AUDIO",
            "registration_promo_item": "true"
        },
        {
            "authors": [
                "Various Authors"
            ],
            "promo_type": "Gift",
            "sku": "BC6115W",
            "org_id": "",
            "title": "An Experience of Mindfulness - Audio Download",
            "subtitle": "",
            "thumbnail_link": "https://cdn.shopify.com/s/files/1/0253/2822/2307/products/samplemindfulness_1.png?v=1583874814",
            "product_format": "FORMAT_AUDIO",
            "registration_promo_item": "true"
        },
        {
            "authors": [
                "Jack Kornfield",
                "Tara Brach"
            ],
            "promo_type": "Org",
            "sku": "DD05188W",
            "org_id": "1",
            "title": "Mindfulness Daily at Work - Online Learning",
            "subtitle": "Create a Life-Changing Meditation Practice in Less Than 15 Minutes a Day!",
            "thumbnail_link": "https://cdn.shopify.com/s/files/1/0253/2822/2307/products/mindfulness-daily-at-work_1.png?v=1583894278",
            "product_format": "FORMAT_COURSE",
            "registration_promo_item": ""
        },
        {
            "authors": [
                "Jane Doe"
            ],
            "promo_type": "Org",
            "sku": "ABC123",
            "org_id": "111",
            "title": "An ebook",
            "subtitle": "Subtitle",
            "thumbnail_link": "somewhere/something2.jpg",
            "product_format": "FORMAT_VIDEO",
            "registration_promo_item": ""
        },
        {
            "authors": [
                "Jack Kornfield",
                "Tara Brach"
            ],
            "promo_type": "Org",
            "sku": "DD05188W",
            "org_id": "12",
            "title": "Mindfulness Daily at Work - Online Learning",
            "subtitle": "Create a Life-Changing Meditation Practice in Less Than 15 Minutes a Day!",
            "thumbnail_link": "https://cdn.shopify.com/s/files/1/0253/2822/2307/products/mindfulness-daily-at-work_1.png?v=1583894278",
            "product_format": "FORMAT_COURSE",
            "registration_promo_item": ""
        }
    ],
    "last_evaluated_keys": {
        "gift_lek": "",
        "org_lek": ""
    },
    "org_count": "3",
    "gift_count": "3"
}
```

---