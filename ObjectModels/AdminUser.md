# AdminUser Object Model

## Overview

**Current version:** 0.0.4

**Logs:**

- 2020-02-12: **Updated** the timestamp format to a standard format [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339). **Added** DB mappings for option names.
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
    - `STATE_UKNOWN` (DB: `0`)
	- `ADMIN_ENABLED` (DB: `1`)
	- `ADMIN_DISABLED` (DB: `2`)
- `permissions` _(array)_: An array of permissions for the admin user which determines their access level. Contains the following:
	- `PERM_UKNOWN` (DB: `0`)
	- **Admin Options (view/modify other admins)** &darr;
	- `ADMIN_VIEW` (DB: `1`)
	- `ADMIN_ADD` (DB: `2`)
	- `ADMIN_MODIFY` (DB: `3`)
	- `ADMIN_DELETE` (DB: `4`)
	- **User Options (view/modify users)** &darr;
	- `USERS_VIEW` (DB: `5`)
	- `USERS_ADD` (DB: `6`)
	- `USERS_MODIFY` (DB: `7`)
	- `USERS_DELETE` (DB: `8`)
	- **Add/Remove Items from DL** &darr;
	- `USER_ITEMS_ADD` (DB: `9`)
	- `USER_ITEMS_DELETE` (DB: `10`)
	- **Gift Management** &darr;
	- `GIFTS_VIEW` (DB: `11`)
	- `GIFTS_ADD` (DB: `12`)
	- `GIFTS_DELETE` (DB: `13`)
	- **Organizations Management** &darr;
	- `ORGANIZATIONS_VIEW` (DB: `14`)
	- `ORGANIZATIONS_ADD` (DB: `15`)
	- `ORGANIZATIONS_DELETE` (DB: `16`)
	- **Bulk Items Operations (must have `users` permissions)** &darr;
	- `BULK_ITEMS_ADD` (DB: `17`)
	- `BULK_ITEMS_DELETE` (DB: `18`)

- `created_at` _(string)_: string representation of user creation [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339) datetime (e.g. "2019-06-10T00:00:00Z").
- `last_modified` _(string)_: string representation of user updated [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) / [RFC 3339](https://tools.ietf.org/html/rfc3339) datetime (e.g. "2019-06-10T00:00:00Z").

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
    "created_at": "2019-06-10T00:00:00Z",
    "last_modified": "2019-06-10T00:00:00Z"
}
```