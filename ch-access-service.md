# ch-access-service

## Purpose of This Service

This service handles user registrations. During a user registration, the **Access Service** endpoint will be called (as opposed to the **User Service**). The **Access Service** will register a user in the following steps:
1. Call the **Auth Service** and to create the new user in AWS Cognito and retrieve an **Access Token**.
2. Call the **Multipass Token Service** to generate a MultiPass token (for Shopify).
3. Call the **Organization Service** to see if the user's email matches one of Sounds True's recognized organizations.
5. Call the **User Service** to add a create the new user in the Customer Hub.
6. Call the **UserProduct Service** to associate purchased and FREE gift products (including Organization gift products) to the user's account in the Customer Hub.

Please see [_Architecture Diagrams_](https://drive.google.com/file/d/1cyy6wLrcHLeN8LrffshQYgKdVKkuexhJ/view?usp=sharing), _User Flows_ tab for the _User Registration Process_.

## Repository
* [Github](https://github.com/stdev/ch-access-service)

## Object Model
* [Access Service Object Model](ObjectModels/Access.md)

## Ports Used
- **port:** `50054`
- **hport:** `5681`

## Access Tokens (JWT)

Currently, no Access Tokens are required.

## Functionality

The following contains details for the functionality of the Access Service.

### USER REGISTRATION

The following are available gRPC methods for general user registration endpoint(s).

#### `UserRegistration()`
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
        "accepts_marketing_updated_at": "2019-06-10 00:00:00",
        "marketing_opt_in_level": "MARKETING_UNKNOWN"
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
}'
```

##### JSON Response Example

```json
{
    "token_set": {
        "aws_jwt_access_token": "eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiJlNTcwNmZiOC1hNWU5LTQyYTYtYjJjZS04M2FjOGRlNWY1YzUiLCJldmVudF9pZCI6IjAyNzFmZWU5LWM2NGEtNDQ1Mi1iZGQ5LTVhYzlhZGY1NzlkMyIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1NzcxNDA3ODUsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3NzE0NDM4NSwiaWF0IjoxNTc3MTQwNzg1LCJqdGkiOiI4MTFmZTI0OS1hZmVlLTQ2ZTItOTkzZC05ODMzNDdjODEzM2EiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiZTU3MDZmYjgtYTVlOS00MmE2LWIyY2UtODNhYzhkZTVmNWM1In0.Z4gPq4tXr4KBLj-7aold_rABMJuXbjm1GLHhN2cGex-VLZUzPVGNHnymjIlE7OGzxs0EbIJHCF3phpZd33Fhypt03E9T46AF7_cYnMLfPVFhrT3LQ7gXbg-ucip7CQYDtjictth_NBTRzO3xiL-NnuSXjwg_IrnCWIgQ8jBExcC9SeSuYJcVv08irYOjhtzYzzTsZXglPwyx0GA899yfljyvzLL9ZuWj3nXBD_SppKJFo4HNdSiRrYonHhKntQxRvjw5-uuUkav5LNnPqkqgfJkpZGQnGVIq3UBUodGsIapwRytcLyXUUiV8IJsRk3d233e6TnCpD4YmCbYxpnOxJg",
        "aws_jwt_refresh_token": "eyJjdHkiOiJKV1QiLCJlbmMiOiJBMjU2R0NNIiwiYWxnIjoiUlNBLU9BRVAifQ.S1qQyCxntdRWoBpCVGeS4IqkbSiAvKrybgpPvuv8OUx56AXmIykf4rUtej-4Y0yhGdibTvcg_OfqRbO2ZulFLHSbYmgkOMsYFUbdSJ6UdBscuvsv6vVt5OM-q5T2-_2jeV2Y8khsbja4PtAZkQH3JTkIOouStKsJYzGWb0OXw9ShX4gGuHE0tv2M5cbOrg6sK9STOGOXa4GyiDQGEW-0d99QFCYWmDMsklwqmnWuFpm5VhlwnL1bj_hJD5sAvKha2Ul-U4lcW85Zef2-U3U6lpQn2dEiE1Owma7onZ8otxbkz7IW-4hNVK2gJNIZ1ocln1gD9X8a33CAFlx1Iwgzqg.vbEIyrbQc--hb60t.uoLVSUSZ_xEc8-JNCyWBN-gOC9CBG8iKPt1mlVgcne1Rv7UUjMhIVD7FzXGqZm5vblfwcq1yEvQNNaZBgJxipIltelo6BVXOR34OTtWTaO-Fn9C2s0WsGCFfProMg9-KHX9Gy5oZ0Op2f8UDJuPRZpDmSDsMM4469fhWM3j5PAR8EZxK4HcjpXpLN25rzNeTy1Y-IonntRmzYxZd1rQto7HPRoL3tovspzLMnesTcvGzEKSda8hARV5Nc8OkqAOlz_OwVwQqYIE97XyogVBweo5xyb_1hRZspbJpGU_L87OiiBcCcIP1Pv_szGtpi79YfdrH9kuhUDJYUGbQDvJAYBAfq9B0BL11DZONJk0Lx_fhv4MJ95CLOEClDGf9-M1wIZqbXyZT5G5xrfJvdz6s2TJNgng5mXK1lZF1W1qsJG7pkIuTRxSdEuMxr2J8k_FCkIa0ccH3NX_PTgKEB992vBPzVqq9nAu5D-CvxM2B7xMWOG3v41yL-9XFgV5O0twUrAJ9ogSdJ5akT748CISzSFik5sieLmtHrwBNf9U3lDfedko4Fd3AK3JhaFtYSsk08SoV7oYniZzsScWZq-HrsPiKRH5cYSKrDF3uXijqM3fHBdmcL48AfzZi4iyyx8qjAWBXQK9VKTek8418NmWkVhGhqocCh8i0bE1h5vY81BAN5Gn109w7loNB8RQ_eJ6vDgBKVuLHXIHDaz3PgaOf7f6zCE4bukiVrlfD58ouBXTkG-G-XUeY23e-JACMB7bJd_8ewb6yow0Ad5fuej-74XDVLQfCy1LwCgGMb_8C86zW2M-fsve6eIR08iUOpQN4ZD35AsqnbwLK32tc0dv4eEdf-1V-JDj0jnQKXmRZ8-mf7oUEdpK2-BoRV0Rm9J2dgK9zetqYxMPHEeVkuI3HVtI5iVF5vB76HrtGDlBxYjLgxgwA1-BdFNMGypLIt6qd3tcypwiK5iiNH8NU6GkO7WAht3GV8Bajx5gSAFMT05tvWH2k2KxhXmk-4Hm1bsxuEyAVKFftQQ2IoQQp-gjY-w-IjSPs-K3ERRp983Fam-xvkEORBQQwZaeARcNH8m3Fucq3YTpOmYUj6TFKDN_Zr70pz10VVF6kMEg-8e8dUYgaBIIEFMnTVApJaIzwhLZwkboWjZbwKeqkHYcJJZRXZ6D_WOaQz9K2qIjQ8WK6yqyt64J-JoPGjYndIYoAaduNfv36bBW79mMX0v2QiJyjAY4A896SOkd5zWIchD3XucPRwF3PsBhdf0Mnw1NXH7rYvynx6eNOFdFxIZgBDYEEzM20_0dNFcjshGH6kZ7HyZU1iuAglwddT2q7LcRW3vng.O8DI8e_eVPpJjSz0DnIZsw",
        "token_type": "Bearer",
        "token_expires": "3600"
    }
}
```

---
