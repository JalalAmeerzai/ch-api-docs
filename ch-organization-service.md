# ch-organization-service

## Purpose of This Service

The Organization Service holds information about organizations and processes all CRUDL functionality.

## Repository
* [GitHub Link](https://github.com/stdev/ch-organization-service)

## Object Model
* [Organization Service Object Model](ObjectModels/Organization.md)

## Access Tokens (JWT)

_ALL_ API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

## Functionality

The following contains details for the `Create`, `Read`, `Update`, `Delete`, and `List` functionality of the Organization Service.

### ORGANIZATIONS

The following are available gRPC methods for admin user endpoints.

#### `Create()`
> Creates an organization.

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/organization/`

##### Accepted Request Arguments
- `organization` _(Organization object)_

##### Response
- `created` _(bool)_
- `message` _(string)_
    - "Organization with ID `<Organization.id>` has been created"

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

##### JSON Response Example

```json
{
    "created": true,
    "message": "Organization with ID (11) has been created"
}
```

---

#### `GetByID()`
> Retrieve an organization by **id**.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/organization/:id`

##### Accepted Request Arguments
- `organization_id` _(int64)_

##### Response
- `organization` _(Organization object)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	organization_id: req.params.id
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

##### JSON Response Example

```json
{
    "organization": {
        "id": "11",
        "organization_name": "US Senate",
        "domain_name": "dep3.senate.org",
        "contact_email": "",
        "contact_first_name": "",
        "contact_last_name": "",
        "created_at": "2020-04-03T16:13:40Z",
        "last_modified": "2020-04-03T16:13:40Z"
    }
}
```

---

#### `GetByDomain()`
> Retrieve an organization by **domain_name**.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/organization/domain/:domain_name`

##### Accepted Request Arguments
- `domain_name` _(string)_

##### Response
- `organization` _(Organization object)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	domain_name: req.params.domain
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.GetByDomain(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### JSON Response Example

```json
{
    "organization": {
        "id": "11",
        "organization_name": "US Senate",
        "domain_name": "dep3.senate.org",
        "contact_email": "",
        "contact_first_name": "",
        "contact_last_name": "",
        "created_at": "2020-04-03T16:13:40Z",
        "last_modified": "2020-04-03T16:13:40Z"
    }
}
```

---

#### `Update()`
> Update Organization information.

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/organization/`

##### Accepted Request Arguments
- `organization` _(Organization object)_

##### Response
- `updated` _(bool)_
- `message` _(string)_
    - "Organization ID `<Organization.id>` has been updated"
	
##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	organization_id: req.params.id,
	organization: req.body
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

##### JSON Response Example

```json
{
    "updated": true,
    "message": "Organization (ID: 11) has been updated"
}
```

#### `Delete()`
> Remove an organization by ID.

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/organization/:id`

##### Accepted Request Arguments
- `organization_id` _(int64)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
    - "Organization ID `<Organization.id>` has been deleted"
	
##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	organization_id: req.params.id
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

##### JSON Response Example

```json
{
    "updated": true,
    "message": "Organization (ID: 11) has been deleted"
}
```

---

#### `ListOrganizations()`
> Intended for **Admin UI ONLY**.
> Retrieve _ALL_ organizations from the DB.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/organization/filters/:limit/:key?`
	- `key` (a.k.a. `exclusive_start_key`) - The Organization ID of the last product retrieved (the result of `last_evaluated_key` in the response) _(optional)_

##### Accepted Request Arguments
- `limit` _(string)_
    - the _amount_ of results to show (i.e. the page amount)
- `exclusive_start_key` _(string)_ (`key` in REST endpoint)
    - This is the result (ID) to start the next page set from. This should equal the value of `last_evaluated_key` in the reponse, if applicable.

##### Response
- `count` _(int64)_
- **repeated** `organizations` _(Organization objects)_
- `last_evaluated_key` _(string)_ 
    - This is the last `id` evaluated by the DB. This should be passed as the `exclusive_start_key` in a subsequential call (i.e. make another of the same call passing the value of `last_evaluated_key` to `exclusive_start_key`).

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
client.ListOrganizations(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		console.log("RES RESULT");
		res.json(result);
	}
});
```

##### JSON Response Example

```json
{
    "organization": [
        {
            "id": "8",
            "organization_name": "US Senate",
            "domain_name": "department1.senate.org",
            "contact_email": "",
            "contact_first_name": "",
            "contact_last_name": "",
            "created_at": "2020-04-01T21:11:22Z",
            "last_modified": "2020-04-01T21:11:22Z"
		},
		        {
            "id": "6",
            "organization_name": "US Senate",
            "domain_name": "senate.org",
            "contact_email": "",
            "contact_first_name": "",
            "contact_last_name": "",
            "created_at": "2019-10-17 20:53:58",
            "last_modified": "2019-10-17 20:53:58"
		},
		        {
            "id": "123456789",
            "organization_name": "US Senate",
            "domain_name": "senate.gov",
            "contact_email": "",
            "contact_first_name": "",
            "contact_last_name": "",
            "created_at": "2020-04-02T21:38:44Z",
            "last_modified": "2020-04-02T21:38:44Z"
		}
	],
    "count": "3"
}
```

---