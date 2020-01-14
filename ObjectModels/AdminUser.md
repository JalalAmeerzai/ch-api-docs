# AdminUser Object Model

## Overview

**Current version:** 0.0.3

**Logs:**

- 2019-11-27: **Changed** the permissions (based on Admin Requirements Breakdown doc) plus some of the structure and updated the example.
- 2019-07-19: **Changed** the primary identifier for an admin user to `email`. The reason is that if dyanmoDB (key-value store) is keyed from the email, the email is unique. All user identifier links will be updated to hold the email instead of ID. **Removed** `id` as not needed anymore.
- 2019-07-16: Changed `updated_at` to `last_modified`.
- 2019-06-28: Created. Updated the TODOs.

## Purpose

The AdminUser Object Model is the representation of the admin user. It holds all relevant admin user data for business needs. Admin users will be employees that control things such as bulk adding products to user accounts, etc.

## Definitions

- `email` _(string)_: The email address of the administrator. **Primary Key**.
- `first_name` _(string)_: This is the administrator's first name.
- `last_name` _(string)_: This is the administrator's last name.
- `name` _(string)_: The administrator's first and last names (combined).
- `phone` _(string)_: The work phone number (in [E.164 format](https://en.wikipedia.org/wiki/E.164)) for the administrator.
- `phone_ext` _(string)_: The work phone number extension for the administrator (e.g. 104).
- `state` _(string)_: The state of a administrator's general account. Options are:
    - `ADMIN_ENABLED` _(default)_
    - `ADMIN_DISABLED`
- `permissions` _(array)_: An array of permissions for the admin user which determines their access level. Contains the following:
	- **Super Admin Options (view/modify other admins)** &darr;
	- `ADMINISTRATOR_VIEW`
	- `ADMIN_ADD`
	- `ADMIN_MODIFY`
	- `ADMIN_DELETE`
	- **User Options (view/modify users)** &darr;
	- `USERS_VIEW`
	- `USERS_ADD`
	- `USERS_MODIFY`
	- `USERS_DELETE`
	- **Add/Remove Items from DL** &darr;
	- `USER_ITEMS_ADD`
	- `USER_ITEMS_DELETE`
	- **Gift Management** &darr;
	- `GIFTS_VIEW`
	- `GIFTS_ADD`
	- `GIFTS_DELETE`
	- `ORGANIZATIONS_VIEW`
	- `ORGANIZATIONS_ADD`
	- `ORGANIZATIONS_DELETE`
	- **Bulk Items Operations (must have `users` permissions)** &darr;
	- `BULK_ITEMS_ADD`
	- `BULK_ITEMS_DELETE`
- `created_at` _(string)_: string representation of user creation UTC datetime (e.g. "2019-06-10 00:00:00").
- `last_modified` _(string)_: string representation of user updated UTC datetime (e.g. "2019-06-10 00:00:00").

## Notes / TODOs

- I modified this heavily based on the AdminRequirements_Breakdown.md document attached to https://soundstrue.atlassian.net/browse/FLAT-901. _This object model is subject to changes!_

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** AdminUser object._
```json
{
    "email": "johndoe@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "name": "John Doe",
	"phone": "1234567890",
	"phone_ext": "123",
	"state": "ADMIN_ENABLED",
	"permissions": [
		"ADMINISTRATOR_VIEW", "ADMIN_ADD", "ADMIN_MODIFY", "ADMIN_DELETE", "USERS_VIEW", "USERS_ADD", "USERS_MODIFY", "USERS_DELETE", "USER_ITEMS_ADD", "USER_ITEMS_DELETE", "GIFTS_VIEW", "GIFTS_ADD", "GIFTS_DELETE", "ORGANIZATIONS_VIEW", "ORGANIZATIONS_ADD", "ORGANIZATIONS_DELETE", "BULK_ITEMS_ADD", "BULK_ITEMS_DELETE"
	],
    "created_at": "2019-06-10 00:00:00",
    "last_modified": "2019-06-10 00:00:00"
}
```