# ch-auth-service

## Purpose of This Service

This is the intermediary for internal services, as well as external requests, to our AWS Cognito setup and will handle all calls to/from AWS Cognito.

## Repository
* [Github](https://github.com/stdev/ch-auth-service)

## Object Model

* [Auth Service Object Model](ObjectModels/Auth.md)

## Ports Used
- **port:** `50058`
- **hport:** `5685`

## Access Tokens (JWT)

Almost _all_ (see Exceptions below) API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

### Exceptions (API Endpoints)
- `Authenticate()`
- `CreateUser()`
- `InitiateResetPassword()`
- `ConfirmResetPassword()`

## Testing

TBD

## Functionality

The following contains details for the `Create`, `Read`, `Update`, `Delete`, `List`, along with other, functionality of the Auth Service.

### AUTHENTICATION

The following are available gRPC methods for general product endpoints.

Authentication endpoints accept a header to determine which AWS Cognito user pool to use._

```
X-Auth-Request-From: ch-adminuser-service
```

&rarr; <span style="font-size: .75rem">_Pass this header + value in if you need Auth service to Authenticate using the `ch-administrator-pool`!<span>_

#### `Authenticate()`
> Authenticates a user within AWS Cognito. Authenticated users receive a set of tokens back for accessing the Customer Hub.

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/authenticate/`

##### Accepted Request Arguments
- `email` _(string)_ 
	- required for the [Standard Auth Flow](/Microservices/ch-auth-service/#standard-auth-flow) and [Refresh Auth Flow](/Microservices/ch-auth-service/#refresh-auth-flow)
- `password` _(string)_
	- _**This must be base64 encoded!**_
	- required for the [Standard Auth Flow](/Microservices/ch-auth-service/#standard-auth-flow)
- `refresh_flow` _(bool)_
	- required for the [Refresh Auth Flow](/Microservices/ch-auth-service/#refresh-auth-flow)
- `refresh_token` _(string)_
	- required for the [Refresh Auth Flow](/Microservices/ch-auth-service/#refresh-auth-flow)

##### &rarr; Standard Auth Flow
To initiate a <ins>_standard_</ins> flow, pass in the `email` and `password`.

- **The _Access_ token only lasts 1 hour.**
- **IMPORTANT:** API consumers (clients) should run their API calls in a standard `try/catch` flow that looks for a "token expired" error; if caught, the caller would then send a request using the [Refresh Auth Flow](/Microservices/ch-auth-service/#refresh-auth-flow).
	- An actual error will look something like this:
	```
	{
		"code": 2,
		"metadata": {
			"_internal_repr": {}
		},
		"details": "jwtAuth failed: JWT error: Token is expired"
	}
	```

##### &rarr; Refresh Auth Flow
To initiate a <ins>_refresh_</ins> flow, pass in the `email`, `true` for `refresh_flow`, and the actual Refresh token for `refresh_token`.

- The refresh token can be used for automated "extensions" of the user session since the Access token is only good for 1 hour.
- The refresh token is good for any refresh(es) of the Access token needed within 30 days. After that, the user is required to do the <ins>Standard Auth Flow</ins> (`email` + `password`).

##### Response
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
client.Authenticate(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example (\*STANDARD\* AUTH FLOW)

```bash
curl -X POST /api/authenticate \
  -H 'Content-Type: application/json' \
  -d '{
	"email": "test@example.com",
	"password": "VGVzdGluZzEyMzQhIQ=="
}'
```

##### cURL Request Example (\*REFRESH\* AUTH FLOW)

```bash
curl -X POST /api/authenticate \
  -H 'Content-Type: application/json' \
  -d '{
	"email": "test@example.com",
	"refresh_flow": true,
	"refresh_token": "eyJjdHkiOiJKV1QiLCJlbmMiOiJBMjU2R0NNIiwiYWxnIjoiUlNBLU9BRVAifQ.Fx3um7icHLe3-jwoTmLb0pcwIDVM-6yGQSjN9o-hqtUgN49_DSpwtEf-lst6zjpNmwTn7NKhNTs8tAuzOFvqvbfZ1NxXg68qB-efi4O-SlXuf_keT1bHCKu1DZKgWKHo_m8cXqCTLW_3qSPQQGY4cVfG8LbVPBqlmZEsAXhL34iWMpmkYQmh0al8m1TDw4xBTVYkEwVwaC008OFAN8JNUtTRYr-818HW2uE1B496PDa62QJ1uO-Umsqpm1WtaPLtv8mvPMClPdPqFt2VE2GkD-PzxEFHp2hpin6ER9rrhm-zO9WtwmaL6z8g8uaU6drZSlEkp06asqNJGT8SYAOsxg.iMMfx6NLsAKvc7qy.ZqohHX9nFrKxX2271bHf8SA0s6y1oqo4tCnqbTU3drWr4IXG6X3ZphrOowSj7JQ6ij9osPWPecT1SgtZiKAskTVr_mcw0lL37bqcKT19KCOUcvsb36m2KVWhBhG6vvC2FtD2KomFOLVQwh9FBsXKFTlmhcDr_lFBosBEqv6apCZJbvYkkMKq0xTpsoORaSyP0-YmE_xgnEFxybsyR2qXEloDqj7m1bn62S50KNtTEbUVnpvUTfTs-QHpNOMICJS3w9I0_PFNQ6ZBEsLd7dMvuskmbmY0GoYjgVYCCR_KwjzkKZEjpaVswbALdDtXgyMnZqi9Pv1q6e0z9_vlFqeGkDF-QBfOkv6rDAQsAnZ9T_d6FN4MyyjgtJxpVNYgRMh3aRJAjR1Wiq4DUwp2y0CQe5fsrIl4dWDgk7Iq1kucMZtflg07ZKmdJbgrNG8N5elEMPeoN8VAxCS-jhzSfNNpfjFc2h4qlmNWWP2xon18ZBJ0ApHm2AB-AQVCZo0SwjaJbjzPRhSQG_ep4VU-h--oJEv34s6o8EvL77amYO1Qzp-3E0n-foybmzXy9BqEfIma0DUmergS8xtG-Lyir3pGQgTNWVWDQnR6T01gYycs9jIRO9O_u9OCE7-goSJKKyjv8buklAxkVwkgPRvYg9yJaUa-vesW3TjZVFMu5RRplBoD64vZuKKHi6s3pQUXxV-jM8DHCCvm69Kb4xaLOnzltcWYrxqrhBPnYg3A2wGYZKQc7fUABjdlznb-bDjD8gzxD534jG-QSKo8vwdRdKlnoQtY9h5Pu6qDWpCNld_kKXqQeWu50CG0Wu7nHuBE3sUmZVH-Z2odf4cU29eOpbv6EHNykBtpvFmb59evtZfur6GPPvX9kt_gdL_cUTFiBW34T1clWyZ0KUgGJuEi-gMUIGQAgJkX9DxWZAO7_aNkcaknJXapz42dH0VDo8ptQFRVadabsNnxee_MvACpx1PjUB2GebKL1zhsJ2EW8t-CZFLCYyGmYQDIhO9vewb26Lx6QTBJwuVD7R2-Yq5eqLLIvWBV6L78ZlHP1VO6xrjlEi9IyRPwVLJhQmki28FqQXb6CkI1i6HYizn9nUTGiYmRU0YvzehDrWeCUsF3seVvoSWck4O21BuiUFlUOHm6aBeXjclE8v1X39e4CyjaOJhYKgFPBNPk_hpSCXMMWOkS-tjbz2x-XNl_CNsEG1e3k4POkc1HxjR-ugdheCLiwEl0n-fnPuWE96rtYnOEiTrSrRVA57ODapCEZs1-XrSBa5lo1NKfeR0eGwhsqZs4FQOPC8-tEukTwxnWm5MGSTeN9kAsgbMjqMYPkZ8pf5V4OTjb.0o-Ht_1eFjnTddX_8GmuPw"
}'
```

##### JSON Response Example (BOTH flows - see note below)

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

**NOTE:** The only difference, for a _response_, between the [Standard Auth Flow](/Microservices/ch-auth-service/#standard-auth-flow) and the [Refresh Auth Flow](/Microservices/ch-auth-service/#refresh-auth-flow) is that the `aws_jwt_refresh_token` will be an empty string for the [Refresh Auth Flow](/Microservices/ch-auth-service/#refresh-auth-flow)!

---

#### `Logout()`
> Invalidates all refresh tokens issued to a user. The user's current access and Id tokens remain valid until their expiry.

**NOTE:** This is the "best" we can do with AWS Cognito. As a client caller, make sure to delete the user's JWT Access Token for _immediate_ "logout".

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/authenticate/logout/`

##### Accepted Request Arguments
- `aws_jwt_access_token` _(string)_
	- This is the user's AWS JWT Access Token

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.Logout(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/authenticate/logout \
  -H 'Content-Type: application/json' \
  -d '{
	"aws_jwt_access_token": "eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiJlNTcwNmZiOC1hNWU5LTQyYTYtYjJjZS04M2FjOGRlNWY1YzUiLCJldmVudF9pZCI6IjRhNmU5ZjMwLWY0NjAtNGRjOS05ZTk1LTRkY2FjZGE4M2E4OCIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1NzcxNDMwNzAsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3NzE0NjY3MCwiaWF0IjoxNTc3MTQzMDcxLCJqdGkiOiI1ZjkyZjlmNi0zOGY5LTRlZDktOWZlMC00OGIyMmFmYzEzOTEiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiZTU3MDZmYjgtYTVlOS00MmE2LWIyY2UtODNhYzhkZTVmNWM1In0.QPkPxZXD3k7ohSuZRusIv1iDoiWoje3wa_ZL7FbmrmpI7DSgQ-Svxx_Gm9rVzLTlklPKnVAZaPQps4GwR7COwxY2uv4NKQwVS6KgQlRVdX7cQ6QDgiBxVDmIgdoDU1wMT_arrMz6fBMS57czqt8k1KWpLhh2nf-zXFMYAJLEx5-dQM0-aT93oSsnl583M2dX54lQ8gCfAncA51MZ8Din1rkcD7YZWCdERYA9ee638aTi0GItQalDnogU_HM3ugXnh2h97X4_HVYbalzhmjPnxtp0OHgidPyDR_bKsQiE8wDrVp3D7G6LLnB1ezSiZEwK2j_gNYPwDXbj0N0HoUcDEA"
}'
```

##### JSON Response Example

```json
{}
```

---

#### `AdminLogout()`
> As an administrator, this invalidates all refresh tokens issued to a user. The user's current access and Id tokens remain valid until their expiry.

**NOTE:** This is the "best" we can do with AWS Cognito. As an administrator using this functionality, it is best combined with disabling the user's account for immediate stopping of user access.

##### Accepted Request Arguments
- `email` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.AdminLogout(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/authenticate/logout \
  -H 'Content-Type: application/json' \
  -d '{
	"email": "test@example.com"
}'
```

##### JSON Response Example

```json
{}
```

---


### USER PASSWORD RESET

_User Password Reset endpoints accept a header to determine which AWS Cognito user pool to use._

```
X-Auth-Request-From: ch-adminuser-service
```

&rarr; <span style="font-size: .75rem">_Pass this header + value in if you need Auth service to Authenticate using the `ch-administrator-pool`!<span>_

#### `InitiateResetPassword()`
> Sends a code to the user's email so that they can reset their password

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/authenticate/forgotpassword/:email`

##### Accepted Request Arguments
- `email` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.InitiateResetPassword(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/authenticate/forgotpassword/test@example.com \
  -H 'Content-Type: application/json' \
```

##### JSON Response Example

```json
{}
```

---

#### `ConfirmResetPassword()`
> Resets a user's password within AWS Cognito

**NOTE:** requires the verification code sent to user's email, from `InitiateResetPassword()`

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/authenticate/forgotpassword/confirm`

##### Accepted Request Arguments
- `email` _(string)_
- `new_password` _(string)_ - _**this must be base64 encoded!**_
- `confirmation_code` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.ConfirmResetPassword(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/authenticate/forgotpassword/confirm \
  -H 'Content-Type: application/json' \
  -d '{
	"email": "test@example.com",
	"new_password": "VGVzdGluZyMjIzEyMzQhIQ==",
	"confirmation_code": "122660"
}'
```

##### JSON Response Example

```json
{}
```

---

### AUTH CRUD

#### **** DEPRECATION NOTICE ****

<p style="border: 1px solid #ffd600; background: #ffffd3; padding: 1rem; color: #ff2600;">Most of the following CRUD endpoints (gRPC functions) will be moving directly inside the <a href="/Microservices/ch-user-service/">User Service</a> for convenience. All endpoints/functions affected will be marked with the deprecation notice. Please see <a href="https://soundstrue.atlassian.net/browse/FLAT-1181">this ticket</a> for more details.</p>

The following are available gRPC methods for general product endpoints.

#### `CreateUser()`
> Creates a user within AWS Cognito and then authenticates them (returning a JWT Access token).

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/authenticate/create/`

##### Accepted Request Arguments
- `user` _(AWSCognitoUser object)_
	- `email` _(string)_
	- `first_name` _(string)_
	- `last_name` _(string)_
	- `password` _(string)_  - _**this must be base64 encoded!**_

##### Response
- `token_set` _(AWSCognitoTokenSet object)_
	- `aws_jwt_access_token` _(string)_
	- `aws_jwt_refresh_token` _(string)_
	- `token_type` _(string)_
	- `token_expires` _(int64)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.CreateUser(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/authenticate/create \
  -H 'Content-Type: application/json' \
  -d '{
	"user": {
		"email": "test@example.com",
		"first_name": "TEST",
		"last_name": "TESTER",
		"password": "VGVzdGluZzEyMzQhIQ=="
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

#### <div style="display: inline-block; border: 1px solid #ffb2b2; background: #ffd3d3; padding: .25rem; color: #ff2600; font-size: .5rem;">DEPRECATED</div> `UpdateUser()`
> Update user attributes w/in AWS Cognito

- Updated attributes:
	- `first_name` _(string)_
	- `last_name`
	- `name` (this attribute is updated automatically - you can't pass it in)

- **NOTE:** from [AWS Cognito docs](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-attributes.html#user-pool-settings-usernames):
> "A username is always required to register a user, and it cannot be changed after a user is created."

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/authenticate/update/`

##### Accepted Request Arguments

- `aws_jwt_access_token` _(string)_
- `user` _(AWSCognitoUser object)_
	- `first_name` _(string)_
	- `last_name` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.UpdateUser(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/authenticate/update \
  -H 'Content-Type: application/json' \
  -d '{
	"aws_jwt_access_token": "eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiI4YTZjMTAyNC1kNWRlLTRlZjAtOTA2OC1kZDEyMDc4MTQ4ZjUiLCJldmVudF9pZCI6IjMwNTc2ZjA0LWIxNjYtNDQ1OS04MGE0LTBmOTY1ZGNjY2Q0MSIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1Nzc0MDU4NzgsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3NzQwOTQ3OCwiaWF0IjoxNTc3NDA1ODc4LCJqdGkiOiIyOTliNGQyNi1lMjRiLTQ2Y2QtYWIzMC01NWI5ODhjNTFmZDgiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiOGE2YzEwMjQtZDVkZS00ZWYwLTkwNjgtZGQxMjA3ODE0OGY1In0.LX8OWYJwx5r5S5TUVBBlTU2aCR0qVVYvgIrTeMoXLsi8l33JCpY-iynINuOqdKkUoY-RsDcaY7WvIMn1EwNpekQR2xoLSxAHFqqmJ06AR0npasQIWdsNEf--5vosCvO8anYiX_5eEvqV63aPOsalsMoI2EqrzJ758iAnO1UWC7ZQvAajmzNxDFOieHv1ILDGEhTiDfbGqbOvUYKMTfWjxSpPNuGe3gdv1e9UghP1s713SsfU4IVRHsLBAOBFm3SDd3fA6eMSAB77VJ_GKhXoxJJuiSyQWco9oJm7-J9mszU2aLnwmPsRM-r3YmYabaqgyG5kde63sfchR7VwVDzHhg",
	"user": {
		"email": "test@example.com",
		"first_name": "TEST 1",
		"last_name": "TESTER 1"
	}
}'
```

##### JSON Response Example

```json
{}
```

---

#### <div style="display: inline-block; border: 1px solid #ffb2b2; background: #ffd3d3; padding: .25rem; color: #ff2600; font-size: .5rem;">DEPRECATED</div> `AdminUpdateUser()`

> Update user attributes w/in AWS Cognito _as an Administrator_

- Updated attributes:
	- `first_name` _(string)_
	- `last_name`
	- `name` (this attribute is updated automatically - you can't pass it in)

- **NOTE:** from [AWS Cognito docs](https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-attributes.html#user-pool-settings-usernames):
> "A username is always required to register a user, and it cannot be changed after a user is created."

##### Accepted Request Arguments

- `user` _(AWSCognitoUser object)_
	- `email` _(string)_
	- `first_name` _(string)_
	- `last_name` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.AdminUpdateUser(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/authenticate/admin/update \
  -H 'Content-Type: application/json' \
  -d '{
	"user": {
		"email": "test@example.com",
		"first_name": "TEST 1",
		"last_name": "TESTER 1"
	}
}'
```

##### JSON Response Example

```json
{}
```

---

#### <div style="display: inline-block; border: 1px solid #ffb2b2; background: #ffd3d3; padding: .25rem; color: #ff2600; font-size: .5rem;">DEPRECATED</div> `DeleteUser()`

> Remove a user from AWS Cognito (as that user)

##### Accepted Request Arguments

- `aws_jwt_access_token` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.DeleteUser(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/authenticate/delete \
  -H 'Content-Type: application/json' \
  -d '{
	"aws_jwt_access_token": "eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiIyMjEzYzFjMy1kNjBmLTQ3NDgtYTdmYi02Njk3M2U0MWZhMWMiLCJldmVudF9pZCI6ImE4N2EzODkwLTE4ZDktNDEzNC1hYzA3LTE1OWMyNmU2MjkwOSIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1Nzc0MDI4MTAsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3NzQwNjQxMCwiaWF0IjoxNTc3NDAyODEwLCJqdGkiOiI2ZmEwNTFmYi02Mjk5LTQ5MjQtODBkYy03NjE1MWM0MzA0M2MiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiMjIxM2MxYzMtZDYwZi00NzQ4LWE3ZmItNjY5NzNlNDFmYTFjIn0.kZxQMFH2eIU4QR0rZMLbi7Vx36EPCa71zcXBw6o2WPiv8kgOku64R9ovCRbGLmOxCTi1hG6aQCmwSdqPY9sok4klV5l_wfzbHkf3tofdZcE2Nfq4v3PQ5kLZiJ7az31JB_KTuZtRNNs0e0NNWTmDfuSz9SxgX08-vRGBd24r3kEb_iRTqpUek9y5hGDPPke02c7roLn75sOft5udr6gALe6wqrnnm2PBtd7mP9RNEAzUcWpGN37Jj_Qzh1fgqLh3Jy8rvyfPHeihRzYJHKFHpdBSS6s3zRuhXudlqHEU0_inkClsTE4wqyUHEh0hnD7nQGlbnVBsBNsT-Eu2rBhqZQ"
}'
```

##### JSON Response Example

```json
{}
```

---

#### <div style="display: inline-block; border: 1px solid #ffb2b2; background: #ffd3d3; padding: .25rem; color: #ff2600; font-size: .5rem;">DEPRECATED</div> `AdminDeleteUser()`

> Remove a user from AWS Cognito (as an Administrator)

##### Accepted Request Arguments

- `email` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.DeleteUser(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/authenticate/delete \
  -H 'Content-Type: application/json' \
  -d '{
	"email": "test@example.com",
}'
```

##### JSON Response Example

```json
{}
```

### USER STATUS

#### **** DEPRECATION NOTICE ****

<p style="border: 1px solid #ffd600; background: #ffffd3; padding: 1rem; color: #ff2600;">The following USER STATUS endpoints (gRPC functions) will be moving directly inside the <a href="/Microservices/ch-user-service/">User Service</a> for convenience. All endpoints/functions affected will be marked with the deprecation notice. Please see <a href="https://soundstrue.atlassian.net/browse/FLAT-1181">this ticket</a> for more details.</p>

#### <div style="display: inline-block; border: 1px solid #ffb2b2; background: #ffd3d3; padding: .25rem; color: #ff2600; font-size: .5rem;">DEPRECATED</div> `AdminEnableUser()`
> Enable a user within AWS Cognito

##### Accepted Request Arguments
- `email` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.AdminEnableUser(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/authenticate/admin/enable \
  -H 'Content-Type: application/json' \
  -d '{
	"email": "test@example.com"
}'
```

##### JSON Response Example

```json
{}
```

---

#### <div style="display: inline-block; border: 1px solid #ffb2b2; background: #ffd3d3; padding: .25rem; color: #ff2600; font-size: .5rem;">DEPRECATED</div> `AdminDisableUser()`
> Disable a user within AWS Cognito

##### Accepted Request Arguments
- `email` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.AdminDisableUser(payload, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/authenticate/admin/disable \
  -H 'Content-Type: application/json' \
  -d '{
	"email": "test@example.com"
}'
```

##### JSON Response Example

```json
{}
```

## Notes

The Auth Service connects to AWS Cognito and there are some important settings that need to be noted.

- Currently, AWS Cognito JWT ID & Access tokens expire 1 hour after being issued. We'll need to come up with a strategy to use refresh tokens to keep user sessions alive for much longer ([https://soundstrue.atlassian.net/browse/FLAT-984](https://soundstrue.atlassian.net/browse/FLAT-984)).
- **General settings > Triggers:** Pre sign-up (`cognito-pre-sign-up`)
	- We use a Lambda trigger to automatically approve users. This is _necessary_ in order to retrieve a `JWT Access Token` on registration AND in order to setup a forgot password flow!
		- The **General Account** and **Email** are set as confirmed/verified
- **App integration > Domain name:** This is needed to to register a user to a user pool.
	- We currently use an **Amazon Cognito domain** in the dev env: `https://staging-soundstruehub.auth.us-east-1.amazoncognito.com/`
- In our Dev env, email is being sent through **AWS Cognito**. _**This needs to change for Production to AWS SES due to email limits!!!**_