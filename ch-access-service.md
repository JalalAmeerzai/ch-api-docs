# ch-access-service

## Purpose of This Service

This is a helper service which helps handle user registrations. During a user registration, the **Access Service** endpoint will be called (as opposed to the **User Service**). The **Access Service** will 1) determine if the user's email matches an organization domain (and modify the user data if needed), 2) send the user data to the **User Service**, and 3) create and send the entry to the **UserProduct Service** (if applicable).

Please see [_Architecture Diagrams_](https://drive.google.com/file/d/1cyy6wLrcHLeN8LrffshQYgKdVKkuexhJ/view?usp=sharing), _User Flows_ tab for the _User Registration Process_.

## Repository
* [Github](https://github.com/stdev/ch-access-service)

## Object Model
* [Access Service Object Model](ObjectModels/Access.md)
* For Calling Services:
	* [User Object Model](ObjectModels/User.md) - this is the user data that will be possibly modified and then sent to the **User Service**.
	* [UserProduct Object Model](ObjectModels/UserProduct.md) - this is the user-product data that will be created and sent to the **UserProduct Service**, if applicable. Applicable options are:
		* If the user registration was done through the e-commerce system's checkout processs, requiring products to be added to the user's account.
		* If the user has a matching organization, which requires products to be added to the user's account.

## Ports Used
- **port:** `50054`
- **hport:** `5681`

## Access Tokens (JWT)

_ALL_ API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

## Testing

## Functionality