# ch-shopify-helper-service

## Purpose of This Service

The Shopify Helper Service is for Shopify to utilize the Customer Hub user login/logout processes.

## Repository
* [GitHub](https://github.com/stdev/ch-shopify-helper-service)

## Object Model

TBD

## Access Tokens (JWT)

Access and Refresh Tokens are returned with the `ShopifyLogin` method. It requires one for the `ShopifyLogout` method.

## Functionality

The following contains details for the functionality of the Shopify Helper Service.

### SHOPIFY HELPER METHODS

The following are available gRPC methods for shopify helper endpoints.

#### `ShopifyLogin()`
> 1. Authenticate user with AWS Cognito
> 2. Generate Shopify MultiPass Token to allow user to sign into Shopify

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/shopify/login/`

##### Accepted Request Arguments
- `password` _(string)_ - _**this must be base64 encoded!**_
- `user` _(object)_
	- `email` _(string)_
	- `first_name` _(string)_
	- `last_name` _(string)_
	- `return_to` _(string)_ - redirect to a specific page within Shopify

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
client.ShopifyLogin(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/shopify/login \
  -H 'Content-Type: application/json' \
  -d '{
	"password": "VGVzdGluZzEyMzQhIQ==",
	"user": {
		"email": "test@example.com",
		"first_name": "Test",
		"last_name": "Tester",
		"return_to": "www.ecommerce-store.com/somewhere"
	}
}'
```
##### JSON Response Example

```json
{
    "token_set": {
        "aws_jwt_access_token": "eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiJmOGY4ZmY4NS0xZjE3LTQ0ZjktOTc1ZC00NzI0MWM5ODc1OGUiLCJldmVudF9pZCI6ImRiOTQwMTJlLTc2NTMtNDcwZS1hY2U1LTAwYTg1MmExNmZlYSIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1NzkxMjg5MDIsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3OTEzMjUwMiwiaWF0IjoxNTc5MTI4OTAyLCJqdGkiOiJkZWMzODZkYi0yNTliLTRkOTUtOTg5OS04NGViZTg3OTRjYmIiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiZjhmOGZmODUtMWYxNy00NGY5LTk3NWQtNDcyNDFjOTg3NThlIn0.LzHbdx6gL2cBjr4O6TJJUGSaFsXgannOaxemr-f1ZeCrqVkseWlvFpkSSbyRWhszOPvHX1_RFIrW8VKyFHDrHI42Hp2yATbSuTyzHjaSW2JMs0jE-j7nrMcChujwxh4s8BEj6c-xIM9th33revU03uQfLOZwuOUn5wGgylknuzmBht5dGb9373wJnHvWWtkwfT4Wu9h1fC1Je5HvuTsHM8dfmpt2S-e-zKAdHJ852swcQrf14d7R_TEPlpXplhO8m79iXbA7NBHk4VOKWsZSzkW2BvhE6juE6Eo0s5swPfWevjKyNjy0CaNOT0IHK91EwLPXkfeOxnA2wRz15R4NIQ",
        "aws_jwt_refresh_token": "eyJjdHkiOiJKV1QiLCJlbmMiOiJBMjU2R0NNIiwiYWxnIjoiUlNBLU9BRVAifQ.aEsb3KZYWC5Qex4_MIrQ3E9xXd5BKlggoHcUX1EEZOocyHmTTBHFWylOq168SgACwKskNnVOCk2jj2gQHPNjOSjgO8ppC7zypn2iIU3LAk3JsCTf3kBZJIxvPyZsckuB8Bqik1bN5OigpNruP0b7nyDlyRleFyq0A-HclIqNBKt6Sx4XSGhJV3fIdDwSChFTrhbd8jI3QHSpW2yOjueiGHlKz0xQM0Fx1JWEU1xlAGCPKtmECG5YzgE8hLVfhE6TGxJ_yEfzx-r8VdBS_PLbb6XQd_JQCz56OQ77hiDmGWjpoAmQq6tJ1zXrZ3ur8dAsLF48eBbtTZeUHv2UAMBAMw.DHRfftxPmzO1bZx5.YHWvJW8tLh5sStcbN9qkVOpc98JkM4dEVASpQQoPGqwVL5nOPqN_j-pGidjodXG1LWQS35nsZ8CIKHbvPc-1DWObESzq9updi-g-zGxLhZ50nvingIVajSogdCOwB-465yRVaZ094r8vcjuF_N89VW10E41LHQI_-caSH88d9BBpO7wcZjCSAzdmiifCGx3FGbOo1XJkSCujis6aP2QoSVBhtFSJtAcFOQeAQL434gjaIfyYXu0CQY4fyboirJhh3PYayZoTwH4dQ6P-AnYg77ibiJ0UwTe3OSXJj7RMhYk0Z8JlTp49GPsSv5GKXEZNyntTOIUWPJGTV-Y-A0OgjE62WVPflJvIOXXopErc0_OooMstM3RQKWJWJpaH4OIkFtutTNH69ur5K8w6-ioVUNMtC3zLvK9SBuQRBu9WHghae5GfBUOnx4SzXxygRB4aRg8fmNkK9z9g-Ujy-jOLpmz2HGeLolI3jhTmz0xRo-Jz4-lSLQgCxY0zG29VpnTheqFYVOk6CGoiCjGPvf048U0XZzXPP6lEPA2BuL8loG0OrH0PpNjhtAk0eAhCeFAWqzDSaNvEmUzjb_qDTikSk3fKAlTsqFbfR9hP-R5POht8GsUGAbLyK0xih-nQ3PbH3VvxfGrL7InP_h1ZohnU0df-PWWtT_5QkrwJwJRCNVtndASjGirtsITZ10JK_DgbWqz_mevyMvZ-hyBHd3TO3WpJyebgk8xXjBd_hcc9mwlPeZBor6Ws0LLycHQ4saktiSSLPrKZyMGfxx0CDkfNrVOByGP6CJ6yqIIDgRrz4aXyruE9xTIbti7C6He_vkbvrw_Eos_dIJPcbazMVkCZKrvoLOUmJRxptsrlo1ytQDi9wigkooKZ31fSgRDScfU6j9hNfeLsuNyjhjO2TrFa8jaYyqifLgaC9V1g1CeRUthuYvMAPfrxOy1zPcCa-68LkhtT-p1ojjREaRMnxTN2X_IXRdapfi_-TuFcA0J3iPLY2TGI1WwvlA-dnHOyRp1UTZN59yVKyB5pQaVKzIST_MBxEZiGxO1HRGrDWuB_ZmVCpUAB2lW1SAY7yiYhpR0FxbZmWj9zlxgosu2f4GjHx1Zua-DP86pRvk3twOMtLwjVHpikrTccmJXPIP2uWCpkLKnAk1yFfk9gQCiEUYejgStGmbP0A1r2ymBMyTAVcKIV4dEQ0xrCsRwtxky7U3PtgmI13WmaJr6p_-74JEQx8iIms40dfP7hrJppiJn0_E8E_IXsq1b7ad5zBqEOcXVMbGJ2YlMhcTh2dzvTeNBtTJtGwndZgObsnqRZ-IJTq_KAQngNgJpcglX-vMUJiVz3.0cvV4iHTuDT5pjDiAVvLtw",
        "token_type": "Bearer",
        "token_expires": "3600"
    },
    "multipass_token": "PRS8CWRMCdIonXTxJwOdRhQ073lU7cTDNf6otEjXYRi48-gFZIAms2j1lT5AL9VYCE_PVhoy_kkpMC-2cHA_Uy8dEzTjuGzLTicvFM0Mfk_GZfIyEJWedWRPY_JpiyB21PVwX69OgOpBtpbigAOQB-PGVcnj3xN-0cb1mBOUgOYwz-5inQaMIvoeLHlOOQvvlLZbGRIVkjU5XgxtbKMgZpWDqaTAYXS1qS_NyIJyIAv_XsF7pf2_6vTFnNiCI08bg7t-msChMgHtG6agUaKdjyDTRSmKwE5Lx_h0ADZiT38="
}
```

---

#### `ShopifyLogout()`
> 1. Remove Shopify MultiPass Token from User object in DB
> 2. Logout a user with AWS Cognito (through Auth Service)

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/shopify/logout`

##### Accepted Request Arguments
- `email` _(string)_
- `aws_jwt_access_token` _(string)_

##### Response

If successful, this service sends back a success response with an empty body.

##### gRPC Request Example (JS)
```js
// set payload
let payload = req.body;

// make gRPC call
client.ShopifyLogout(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X POST /api/shopify/logout \
  -H 'Content-Type: application/json' \
  -d '{
	"email": "geoffs@soundstrue.com",
	"aws_jwt_access_token": "eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiJmOGY4ZmY4NS0xZjE3LTQ0ZjktOTc1ZC00NzI0MWM5ODc1OGUiLCJldmVudF9pZCI6IjI3YzVmMzk5LWEzY2MtNDQ2MC1hMDBlLTZhYjc1OTBlOGQ3NiIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1NzkxMzE1OTUsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3OTEzNTE5NSwiaWF0IjoxNTc5MTMxNTk1LCJqdGkiOiJiMThjZDVmOC1lMmQwLTQzNmEtYmUxYy04ZTU4OTlkMTZmNTMiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiZjhmOGZmODUtMWYxNy00NGY5LTk3NWQtNDcyNDFjOTg3NThlIn0.Wh41xz0vkhTGW2CRvRzapuBojhket-uL7EZE00I7zslWzDIoxBm6wesMid7b-g0P34SfTjimv0FVDhpWHfn8epc2JfXIKIxuqWP64iXQ_obbjIYxvTpuY_CJZIfyE3RBnCzpznlJpXl6oPkvl_zAkQgcrgajxxAbltg2gnoxKM1Ym1BD1rk1nVabtNA_Sx2nLXQsKKDnHG77Wux_a06vgnWM661WnI8pC_YQRMT4YuD-oKscWb-niOK0uiGH3x9Ge_o9HMb-yprRd9CPtFiN4l6p8e2z4sB09YbcYOxqePxYYok4J1oweejLtzqn5wABa5BJVXjtgHB1IgUl8Z5Npg"
}'
```
##### JSON Response Example

```json
{}
```