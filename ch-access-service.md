# ch-access-service

## Purpose of This Service

This service handles user registrations. During a user registration, the **Access Service** endpoint will be called (as opposed to the **User Service**). The **Access Service** will register a user in the following steps:

1. Call the **Auth Service** and to create the new user in AWS Cognito and retrieve an **Access Token**.
2. Call the **Multipass Token Service** to generate a MultiPass token (for Shopify).
3. Call the **Organization Service** to see if the user's email matches one of Sounds True's recognized organizations.
4. Call the **User Service** to add a create the new user in the Customer Hub.
5. Call the **UserProduct Service** to associate purchased and FREE gift products (including Organization gift products) to the user's account in the Customer Hub.

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

---
