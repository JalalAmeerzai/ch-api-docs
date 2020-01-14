# Organization Object Model

## Overview

**Current version:** 0.0.3

**Logs:**

- 2019-12-10: **UPDATED** example `id` field to represent a realistic value.
- 2019-07-16: Changed `updated_at` to `last_modified`.
- 2019-06-28: Created. Removed a confusing top level key for clarity.

## Purpose

The Organization Object Model is the representation of an organization. It holds all relevant organizational data for business needs.

## Definitions

- `id` _(int)_: This is a unique, global identifier for an individual organization. **Primary Key**.
- `organization_name` _(string)_: The name of the organization.
- `domain_name` _(string): The fully qualified domain name ([FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)) of the organization (e.g. `senate.gov`)
- `contact_email` _(string): The email address of the contact person for the organization.
- `contact_first_name` _(string): The first name of the contact person for the organization.
- `contact_last_name` _(string): The last name of the contact person for the organization.
- `created_at` _(string)_: string representation of when the organization entry was added (creation) UTC datetime (e.g. "2019-06-10 00:00:00").
- `last_modified` _(string)_: string representation of when the organization entry is updated UTC datetime (e.g. "2019-06-10 00:00:00").

## JSON Representation (with example values) (minus comments)

_**NOTE:** The following is a representation of a **single** Organization object._
```json
{
    "id": 1,
    "organization_name": "US Senate",
    "domain_name": "senate.gov",
    "contact_email": "",
    "contact_first_name": "",
    "contact_last_name": "",
    "created_at": "2019-06-10 00:00:00",
    "last_modified": "2019-06-10 00:00:00"
}
```