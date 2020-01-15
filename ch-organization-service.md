# ch-organization-service

## Purpose of This Service

- This service is the source of truth for _all_ relevant organizational data for business needs. All other services (internal/external) should utilize this service.
- The Organizational Service processes all CRUDL functionality for admin Users only.

## Repository
* [GitHub Link](https://github.com/stdev/ch-organization-service)

## Object Model
* [Organization Service Object Model](ObjectModels/Organization.md)

## Access Tokens (JWT)

_ALL_ API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

## Testing

### Unit Tests

Unit tests are included for this service. Current packages that include them are:

- `api` (JWT)
- `db` (All CRUDL functionality)

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

##### gRPC Response Example

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

##### gRPC Response Example

---

TODO - add GetByDomain()

---

#### `Update()`
> Update Organization information.

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/organization/`

##### Accepted Request Arguments
- `organization_id` _(int64)_
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

##### gRPC Response Example

---

#### `ListOrganizations()`
> Intended for **Admin UI ONLY**.
> Retrieve _ALL_ organizations from the DB.

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/organization/`

##### Accepted Request Arguments
- _N/A_

##### Response
- `count` _(int64)_
- **repeated** `organizations` _(Organization objects)_

##### gRPC Request Example (JS)
```js
// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.ListOrganizations({}, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		console.log("RES RESULT");
		res.json(result);
	}
});
```

##### gRPC Response Example

---