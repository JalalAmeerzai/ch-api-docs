# ch-adminuser-service

## Purpose of This Service

The AdminUser service holds all the administrators and their permissions data, mainly for Customer Hub administration within the Admin Console.

## Repository
* [Github](https://github.com/stdev/ch-adminuser-service)

## Object Model

* [AdminUser Service Object Model](ObjectModels/AdminUser.md)

## Access Tokens (JWT)

_ALL_ API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

## Functionality

The following contains details for the `Create`, `Read`, `Update`, `Delete`, and `List` functionality of the AdminUser Service.

### ADMINISTRATORS

The following are available gRPC methods for general product endpoints.

#### `Create()`
> Creates an administrator.

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/administrator/`

##### Accepted Request Arguments
- `administrator` _(Admin object)_

##### Response
- `created` _(bool)_
- `message` _(string)_
	- "Administrator (email: `<Admin.email>`) has been created"

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
curl -X POST /api/administrator/ \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"administrator": {
		"email": "st.admin@adminexample.com",
		"first_name": "ST",
		"last_name": "Administrator",
		"name": "ST Administrator",
		"phone": "",
		"phone_ext": "",
		"state": "ADMIN_ENABLED",
		"permissions": [
			"ADMIN_VIEW", "ADMIN_ADD", "ADMIN_MODIFY", "ADMIN_DELETE",
			"USERS_VIEW", "USERS_ADD", "USERS_MODIFY", "USERS_DELETE",
			"USER_ITEMS_ADD", "USER_ITEMS_DELETE",
			"GIFTS_VIEW", "GIFTS_ADD", "GIFTS_DELETE",
			"ORGANIZATIONS_VIEW", "ORGANIZATIONS_ADD", "ORGANIZATIONS_DELETE",
			"BULK_ITEMS_ADD", "BULK_ITEMS_DELETE"
		]
	}
}'
```

##### JSON Response Example

```json
{
    "created": true,
    "message": "Administrator (email: st.admin@adminexample.com) has been created"
}
```

---

#### `Get()`
> Retrieves an administrator.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/administrator/:email`

##### Accepted Request Arguments
- `email` _(string)_

##### Response
- `administrator` _(Admin object)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	email: req.params.email
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
curl -X GET /api/administrator/st.admin@adminexample.com \
  -H 'Authorization: Bearer eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiI0Mjc0OTQxYi03OTI2LTQ3ZWEtOGI5Yi04NDBjN2E3MTNhZTAiLCJldmVudF9pZCI6ImZmYzMxZmY2LTA3ZjQtNGM2My05NjJlLTM0MzY5NDIwNmZhYyIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1NzY1MzY1MzYsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3NjU0MDEzNiwiaWF0IjoxNTc2NTM2NTM2LCJqdGkiOiIxMzIyZGY3My03MjVkLTQ0NWEtYmRhYi03YjgxM2FjOGRlMjMiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiNDI3NDk0MWItNzkyNi00N2VhLThiOWItODQwYzdhNzEzYWUwIn0.Y7yIkHBQaQue4As53gbUyBdG5vZhhirKVi452UnWHv9JLh4HbCuZfSO-SilI-co-QE6Jo7UU6-6jnOifWzolK7995SBy9aibCtXkvK3NmBcvFS77x1WNTbHM_egWeHp_tbObDNvxqiXH9vc_qWDmJtal2HAEQv6V78Vw380XY2NqL3GFeu55NOs9WIspbHeO3C3GReBB89pGACe5Fpg3ygJolPlN8PyxR3od0Egx-ZGQ8CvEBISEObCyi-4Igw7X66ztReUEPez_HY9SE_vCuBBloAunFiqlz31BVPmauPgfqLnUg2MFZQOit35ekAFEI7nZXpwGY82b8MUfDxvcMQ' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "administrator": {
        "permissions": [
            "ADMIN_VIEW",
            "ADMIN_ADD",
            "ADMIN_MODIFY",
            "ADMIN_DELETE",
            "USERS_VIEW",
            "USERS_ADD",
            "USERS_MODIFY",
            "USERS_DELETE",
            "USER_ITEMS_ADD",
            "USER_ITEMS_DELETE",
            "GIFTS_VIEW",
            "GIFTS_ADD",
            "GIFTS_DELETE",
            "ORGANIZATIONS_VIEW",
            "ORGANIZATIONS_ADD",
            "ORGANIZATIONS_DELETE",
            "BULK_ITEMS_ADD",
            "BULK_ITEMS_DELETE"
        ],
        "email": "st.admin@adminexample.com",
        "first_name": "ST",
        "last_name": "Administrator",
        "name": "ST Administrator",
        "phone": "",
        "phone_ext": "",
        "state": "ADMIN_ENABLED",
        "created_at": "2019-12-13T23:02:24Z",
        "last_modified": "2019-12-13T23:02:24Z"
    }
}
```

---

#### `Update()`
> Updates an administrator.

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/administrator/:email`

##### Accepted Request Arguments
- `email` _(string)_
- `administrator` _(AdminUpdate object)_

##### Response
- `updated` _(bool)_
- `message` _(string)_
	- "Administrator (email: `<Admin.email>`) has been updated"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	email: req.params.email,
	administrator: req.body
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
curl -X PUT /api/administrator/st.admin@adminexample.com \
  -H 'Authorization: Bearer eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiI0Mjc0OTQxYi03OTI2LTQ3ZWEtOGI5Yi04NDBjN2E3MTNhZTAiLCJldmVudF9pZCI6ImE3N2Q4ZTMzLWVmOTEtNDE5Ni05YjhhLTRmN2FmNDI2ODliMCIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1NzY2OTIzMTksImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3NjY5NTkxOSwiaWF0IjoxNTc2NjkyMzE5LCJqdGkiOiIxOGMzNjgxOS1lMWZmLTQ0NDEtOGFiNi02YzZiZTkxOGE4OWQiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiNDI3NDk0MWItNzkyNi00N2VhLThiOWItODQwYzdhNzEzYWUwIn0.aOi7QWo_m6u4lB8ltUeKcfLhsluulmFhGt54dOlkmcZU2RwmVNYVFgzjsqWFHiOJT9Kyu0d1h-32df_zMFbrKeCIyZBvTcVfHYnha3XEUICti8KKHJcFdQscbf0icxrSYdVrDgth05axkp8WN9u3yU4BPURk_H9BVnVq6yFD5kyB-0zOdGYeFy7jqy2tpq5IoXzR4f-B72r1qBULALRgJdU8TLBeslPXEJht0hsjP632xkmiHee3bU2Q3KoGd7OPQ7HrwLcfiCzmR93CixvdKDQxUWw9J6mYUVSSO3NqRcj1LEFOEdauxJfGWTpsectXeaAS1YPOSG6HU2AbLRiBhw' \
  -H 'Content-Type: application/json' \
  -d '{
	"phone": "12345678790",
	"phone_ext": "123",
	"state": "ADMIN_DISABLED",
	"permissions": [
		"USERS_VIEW", "USERS_ADD", "USERS_MODIFY", "USERS_DELETE"
	]
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "Administrator (email: st.admin@adminexample.com) has been updated"
}
```

---

#### `Delete()`
> Deletes an administrator.

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/administrator/:email`

##### Accepted Request Arguments
- `email` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "Administrator (email: `<Admin.email>`) has been deleted"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	email: req.params.email
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
curl -X DELETE /api/administrator/st.admin@adminexample.com \
  -H 'Authorization: Bearer eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiI0Mjc0OTQxYi03OTI2LTQ3ZWEtOGI5Yi04NDBjN2E3MTNhZTAiLCJldmVudF9pZCI6ImZmYzMxZmY2LTA3ZjQtNGM2My05NjJlLTM0MzY5NDIwNmZhYyIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1NzY1MzY1MzYsImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3NjU0MDEzNiwiaWF0IjoxNTc2NTM2NTM2LCJqdGkiOiIxMzIyZGY3My03MjVkLTQ0NWEtYmRhYi03YjgxM2FjOGRlMjMiLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiNDI3NDk0MWItNzkyNi00N2VhLThiOWItODQwYzdhNzEzYWUwIn0.Y7yIkHBQaQue4As53gbUyBdG5vZhhirKVi452UnWHv9JLh4HbCuZfSO-SilI-co-QE6Jo7UU6-6jnOifWzolK7995SBy9aibCtXkvK3NmBcvFS77x1WNTbHM_egWeHp_tbObDNvxqiXH9vc_qWDmJtal2HAEQv6V78Vw380XY2NqL3GFeu55NOs9WIspbHeO3C3GReBB89pGACe5Fpg3ygJolPlN8PyxR3od0Egx-ZGQ8CvEBISEObCyi-4Igw7X66ztReUEPez_HY9SE_vCuBBloAunFiqlz31BVPmauPgfqLnUg2MFZQOit35ekAFEI7nZXpwGY82b8MUfDxvcMQ' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "Administrator (email: st.admin@adminexample.com) has been deleted"
}
```

---

#### `List()`
> Intended for **Admin UI ONLY**.
> Retrieve _ALL_ administrators from the DB.

##### Accepted Request Arguments
- `limit` _(string)_
    - the _amount_ of results to show (i.e. the page amount)
- `exclusive_start_key` _(string)_ (`key` in REST endpoint)
    - This is the result (email) to start the next page set from. This should equal the value of `last_evaluated_key` in the reponse, if applicable.

##### Response
- `count` _(int64)_
- **repeated** `administrators` _(Admin objects)_
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
client.List(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		console.log("RES RESULT");
		res.json(result);
	}
});
```

##### cURL Request Example

_**NOTE:** The following example call is with a pagination of 10, starting at the beginning (first call), and there are 10 results. Therefore the `last_evaluated_key` is the last evaluated `email`.

```bash
curl -X GET /api/administrator/filters/10 \
  -H 'Accept: */*' \
  -H 'Accept-Encoding: gzip, deflate' \
  -H 'Authorization: Bearer eyJraWQiOiJpNWFmZyszSHpoVlFQakVrVmx2K3BGVGZNM2orQnRMU3g5VjYxT0YwRXVrPSIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiI0Mjc0OTQxYi03OTI2LTQ3ZWEtOGI5Yi04NDBjN2E3MTNhZTAiLCJldmVudF9pZCI6ImE0NjE1ODJjLThkOWQtNDg2MC1hMmRlLWYzZjMxNTM5NWE0NSIsInRva2VuX3VzZSI6ImFjY2VzcyIsInNjb3BlIjoiYXdzLmNvZ25pdG8uc2lnbmluLnVzZXIuYWRtaW4iLCJhdXRoX3RpbWUiOjE1NzY1Mjg2OTksImlzcyI6Imh0dHBzOlwvXC9jb2duaXRvLWlkcC51cy1lYXN0LTEuYW1hem9uYXdzLmNvbVwvdXMtZWFzdC0xX2xMaTA0cU8wMiIsImV4cCI6MTU3NjUzMjI5OSwiaWF0IjoxNTc2NTI4Njk5LCJqdGkiOiI4NTIxZjgwNS0yYmUxLTQwNTgtYmVmZi0xMDZiNzQ2MmQ2NzciLCJjbGllbnRfaWQiOiIxOGw0Y2FyMnMwZHB1OG5jMWJ1c2NicDZzaCIsInVzZXJuYW1lIjoiNDI3NDk0MWItNzkyNi00N2VhLThiOWItODQwYzdhNzEzYWUwIn0.BDe5izX3gRjV4RlKyQh8wY4pgh1GHHgCURLO6ltiR3AGnLV-yu5dCDf9Akx7Fi_Q4h9K-XVP0Gzs7u745mHH5XS4dpQ8pmMbCXGqEvx4MfNVAHd9Q-bdbAPfnqACNDY8hd7CNZVY60iy3RhFKL5i9ov60IfN47xDtlBjQfhbh_BMkQwRRuMivtLAtK8qmc-_BALENof2ds1ElOwWeTHc-rdOokztK9lgEMknP_2NuwQJQeBfkyK0Ak3dqnidzPgWVWK649B0LlUbk14xsDwO69NMgTIF6pZREl4WU_RwMWVMs9eexDJQvJK8D039InqFYWZrJMIRui6VieePwMOWXQ' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "administrators": [
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "dynamodb_8@example.com",
            "first_name": "DyanmoDB-8",
            "last_name": "User-8",
            "name": "DyanmoDB Administrator 8",
            "phone": "888-888-8888",
            "phone_ext": "888",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-16T20:47:56Z",
            "last_modified": "2019-12-16T20:47:56Z"
        },
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "dynamodb_4@example.com",
            "first_name": "DyanmoDB-4",
            "last_name": "User-4",
            "name": "DyanmoDB Administrator 4",
            "phone": "444-444-4444",
            "phone_ext": "444",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-16T20:47:56Z",
            "last_modified": "2019-12-16T20:47:56Z"
        },
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "dynamodb_5@example.com",
            "first_name": "DyanmoDB-5",
            "last_name": "User-5",
            "name": "DyanmoDB Administrator 5",
            "phone": "555-555-5555",
            "phone_ext": "555",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-16T20:47:56Z",
            "last_modified": "2019-12-16T20:47:56Z"
        },
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "dynamodb_1@example.com",
            "first_name": "DyanmoDB-1",
            "last_name": "User-1",
            "name": "DyanmoDB Administrator 1",
            "phone": "111-111-1111",
            "phone_ext": "111",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-16T20:47:56Z",
            "last_modified": "2019-12-16T20:47:56Z"
        },
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "dynamodb_6@example.com",
            "first_name": "DyanmoDB-6",
            "last_name": "User-6",
            "name": "DyanmoDB Administrator 6",
            "phone": "666-666-6666",
            "phone_ext": "666",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-16T20:47:56Z",
            "last_modified": "2019-12-16T20:47:56Z"
        },
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "dynamodb_3@example.com",
            "first_name": "DyanmoDB-3",
            "last_name": "User-3",
            "name": "DyanmoDB Administrator 3",
            "phone": "333-333-3333",
            "phone_ext": "333",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-16T20:47:56Z",
            "last_modified": "2019-12-16T20:47:56Z"
        },
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "dynamodb_2@example.com",
            "first_name": "DyanmoDB-2",
            "last_name": "User-2",
            "name": "DyanmoDB Administrator 2",
            "phone": "222-222-2222",
            "phone_ext": "222",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-16T20:47:56Z",
            "last_modified": "2019-12-16T20:47:56Z"
        },
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "dynamodb_7@example.com",
            "first_name": "DyanmoDB-7",
            "last_name": "User-7",
            "name": "DyanmoDB Administrator 7",
            "phone": "777-777-7777",
            "phone_ext": "777",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-16T20:47:56Z",
            "last_modified": "2019-12-16T20:47:56Z"
        },
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "dynamodb_9@example.com",
            "first_name": "DyanmoDB-9",
            "last_name": "User-9",
            "name": "DyanmoDB Administrator 9",
            "phone": "999-999-9999",
            "phone_ext": "999",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-16T20:47:56Z",
            "last_modified": "2019-12-16T20:47:56Z"
        },
        {
            "permissions": [
                "ADMIN_VIEW",
                "ADMIN_ADD",
                "ADMIN_MODIFY",
                "ADMIN_DELETE",
                "USERS_VIEW",
                "USERS_ADD",
                "USERS_MODIFY",
                "USERS_DELETE",
                "USER_ITEMS_ADD",
                "USER_ITEMS_DELETE",
                "GIFTS_VIEW",
                "GIFTS_ADD",
                "GIFTS_DELETE",
                "ORGANIZATIONS_VIEW",
                "ORGANIZATIONS_ADD",
                "ORGANIZATIONS_DELETE",
                "BULK_ITEMS_ADD",
                "BULK_ITEMS_DELETE"
            ],
            "email": "st.admin@adminexample.com",
            "first_name": "ST",
            "last_name": "Administrator",
            "name": "ST Administrator",
            "phone": "",
            "phone_ext": "",
            "state": "ADMIN_ENABLED",
            "created_at": "2019-12-13T23:02:24Z",
            "last_modified": "2019-12-13T23:02:24Z"
        }
    ],
    "last_evaluated_key": "johndoe@example.com",
    "count": "10"
}
```

---