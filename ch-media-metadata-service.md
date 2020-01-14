# ch-media-metadata-service

## Purpose of This Service

- _**NOTE:** Current (old) example data can be found here:_ [https://docs.google.com/spreadsheets/d/1KezbGIvli5gKP3weONERwxGvWnyoro5ZT5JoWMykfvc/edit?usp=sharing_](https://docs.google.com/spreadsheets/d/1KezbGIvli5gKP3weONERwxGvWnyoro5ZT5JoWMykfvc/edit?usp=sharing)
- The Media Metadata Service holds all the necessary location data for products with physical file (e.g. audio / video).

## Repository
* [GitHub](https://github.com/stdev/ch-media-metadata-service)

## Object Model
* [Media Metadata Service Object Model](ObjectModels/MediaMetadata.md)

## Ports Used
- **port:** `50055`
- **hport:** `5682`

## Access Tokens (JWT)

_ALL_ API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

## Testing

### Unit Tests

Unit tests are included for this service. Current packages that include them are:

- `db` (All CRUDL functionality)

## Functionality

The following contains details for the `Create`, `Read`, `Update`, `Delete`, and `List` functionality of the UserProduct Service.

### MEDIA METADATA

The following are available gRPC methods for general user endpoints.

#### `Create()`
> Creates a Media Metadata entity (row)

##### Accepted Request Arguments
- `media_metadata` _(object)_

**NOTE:** there is no check for uniqueness of the file objects!

##### Response
- `created` _(bool)_
- `message` _(string)_
	- "Media Metadata (sku: `<MediaMetadata.product_sku>`) has been created"

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
curl -X POST /api/mediametadata \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"media_metadata": {
		"product_sku": "ABC123",
		"file_data": [
			{
				"title": "Some Title 1",
				"media_type": "MEDIA_TYPE_MP3",
				"media_s3_bucket": "soundstrue-downloads",
				"media_s3_key": "products/some_dir_ABC123/Title-0101.mp3",
				"duration": 14400,
				"file_size": "20.5 MB",
				"sort_order": 10
			},
			{
				"title": "Some Title 2",
				"media_type": "MEDIA_TYPE_M4A",
				"media_s3_bucket": "soundstrue-downloads",
				"media_s3_key": "products/some_dir_ABC123/Title-0102.m4a",
				"duration": 14400,
				"file_size": "21.23 MB",
				"sort_order": 20
			},
			{
				"title": "Some Title 3",
				"media_type": "MEDIA_TYPE_MP4",
				"media_s3_bucket": "soundstrue-downloads",
				"media_s3_key": "products/some_dir_ABC123/Title-0103.mp4",
				"duration": 14400,
				"file_size": "22.86 MB",
				"sort_order": 30
			}
		]
	}
}'
```
##### JSON Response Example

```json
{
    "created": true,
    "message": "Media Metadata (sku: ABC123) has been created"
}
```

---

#### `AddFiles()`
> Append file metadata objects to an existing product's file_data list

##### Accepted Request Arguments
- `product_sku` _(string)_
- `files` _(list)_

**NOTE:** there is no check for uniqueness of the file objects!

##### Response
- `created` _(bool)_
- `message` _(string)_
	- "The file(s) metadata entries have been added to the product's data (sku: `<MediaMetadata.product_sku>`)"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	product_sku: req.params.sku,
	files: req.body.files
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.AddFiles(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/mediametadata/ABC123 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"files": [
		{
			"title": "Some Title 4",
			"media_type": "MEDIA_TYPE_MP3",
			"media_s3_bucket": "soundstrue-downloads",
			"media_s3_key": "products/some_dir_ABC123/Title-0104.mp3",
			"duration": 14400,
			"file_size": "27.23 MB",
			"sort_order": 40
		},
		{
			"title": "Some Title 5",
			"media_type": "MEDIA_TYPE_MP3",
			"media_s3_bucket": "soundstrue-downloads",
			"media_s3_key": "products/some_dir_ABC123/Title-0105.mp3",
			"duration": 14400,
			"file_size": "28.35 MB",
			"sort_order": 50
		},
		{
			"title": "Some Title 6",
			"media_type": "MEDIA_TYPE_MP3",
			"media_s3_bucket": "soundstrue-downloads",
			"media_s3_key": "products/some_dir_ABC123/Title-0106.mp3",
			"duration": 14400,
			"file_size": "23.7 MB",
			"sort_order": 60
		}
	]
}'
```
##### JSON Response Example

```json
{
    "created": true,
    "message": "The file(s) metadata entries have been added to the product's data (sku: ABC123)"
}
```

---

#### `Get()`
> Retrieve a single Media Metadata object (by SKU).

##### Accepted Request Arguments
- `sku` _(string)_

##### Response
- `media_metadata` _(object)_

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	sku: req.params.sku
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
curl -X GET /api/mediametadata/AF04734W \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "media_metadata": {
        "file_data": [
            {
                "additional_formats": {},
                "title": "Somatic Descent 1",
                "media_type": "MEDIA_TYPE_MP3",
                "media_s3_bucket": "soundstrue-downloads",
                "media_s3_key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent-0101.mp3",
                "duration": "14400",
                "total_pages": "0",
                "file_size": "20.5 MB",
                "sort_order": "10",
                "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7b"
            },
            {
                "additional_formats": {},
                "title": "Somatic Descent 2",
                "media_type": "MEDIA_TYPE_MP3",
                "media_s3_bucket": "soundstrue-downloads",
                "media_s3_key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent-0102.mp3",
                "duration": "14400",
                "total_pages": "0",
                "file_size": "21.23 MB",
                "sort_order": "20",
                "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7c"
            },
            {
                "additional_formats": {},
                "title": "Somatic Descent 3",
                "media_type": "MEDIA_TYPE_MP3",
                "media_s3_bucket": "soundstrue-downloads",
                "media_s3_key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent-0103.mp3",
                "duration": "14400",
                "total_pages": "0",
                "file_size": "22.86 MB",
                "sort_order": "30",
                "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7d"
            },
            {
                "additional_formats": {
                    "720": {
                        "bucket": "soundstrue-downloads",
                        "key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent_720.mp4"
                    },
                    "1080": {
                        "bucket": "soundstrue-downloads",
                        "key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent_1080.mp4"
                    }
                },
                "title": "Somatic Descent VIDEO",
                "media_type": "MEDIA_TYPE_MP4",
                "media_s3_bucket": "soundstrue-downloads",
                "media_s3_key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent.mp4",
                "duration": "14400",
                "total_pages": "0",
                "file_size": "22.86 MB",
                "sort_order": "30",
                "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7e"
            }
        ],
        "product_sku": "AF04734W",
        "created_at": "2020-01-08 17:49:04",
        "last_modified": "2020-01-08 17:49:04"
    }
}
```

---

#### `UpdateFileData()`
> Modifies a single file object data for a product (SKU, S3 Bucket, and S3 Key are required)

##### Accepted Request Arguments
- `sku` _(string)_
- `media_s3_bucket` _(string)_
- `media_s3_key` _(string)_

##### Response
- `updated` _(bool)_
- `message` _(string)_
  - "The file data (sku: `<MediaMetadata.product_sku>`) has been updated"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	product_sku: req.params.sku,
	file_data: req.body.file_data
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.UpdateFileData(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X PUT /api/mediametadata/ABC123/file \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"file_data": {
		"media_s3_bucket": "soundstrue-downloads",
		"media_s3_key": "products/some_dir_ABC123/Title-0102.m4a",
		"title": "A Different Title",
		"sort_order": "700"
	}
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "The file data (sku: ABC123) has been updated"
}
```

---

#### `Delete()`
> Deletes an entire row/entity given a SKU

##### Accepted Request Arguments
- `product_sku` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "The Media Metadata (sku: `<MediaMetadata.product_sku>`) record has been deleted"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	product_sku: req.params.sku
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
curl -X DELETE /api/mediametadata/ABC123 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```
##### JSON Response Example

```json
{
    "deleted": true,
    "message": "The Media Metadata (sku: ABC123) record has been deleted"
}
```

---

#### `DeleteFileData()`
> Removes an object within `file_data` based on filter (filter: `media_s3_bucket` & `media_s3_key`)

##### Accepted Request Arguments
- `product_sku` _(string)_
- `media_s3_bucket` _(string)_
- `media_s3_key` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "The file metadata (title: `<MediaMetadata.title>`) for (sku: `<MediaMetadata.product_sku>`) has been deleted"

##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	product_sku: req.params.sku,
	media_s3_bucket: req.body.media_s3_bucket,
	media_s3_key: req.body.media_s3_key	
};

// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.DeleteFileData(payload, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X DELETE /api/mediametadata/ABC123/file \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"media_s3_bucket": "soundstrue-downloads",
	"media_s3_key": "products/some_dir_ABC123/Title-0102.m4a"
}'
```
##### JSON Response Example

```json
{
    "deleted": true,
    "message": "The file metadata (title: Some Title 2) for (sku: ABC123) has been deleted"
}
```

---

#### `List()`
> Retrieve all Media Metadata entities/rows.

##### Accepted Request Arguments
- _N/A_

##### Response
- `count` _(int64)_
- **repeated** `media_metadata` _(MediaMetadata objects)_

##### gRPC Request Example (JS)
```js
// set auth header
let metadata = new grpc.Metadata();
metadata.set("authorization", auth);

// make gRPC call
client.List({}, metadata, (err, result) => {
	if (err) {
		// handle error
	} else {
		res.json(result);
	}
});
```

##### cURL Request Example

```bash
curl -X GET /api/mediametadata \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
```
##### JSON Response Example

```json
{
    {
    "media_metadata": [
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Some Title 1",
                    "media_type": "MEDIA_TYPE_MP3",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/some_dir_ABC123/Title-0101.mp3",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "20.5 MB",
                    "sort_order": "10",
                    "guid": ""
                },
                {
                    "additional_formats": {},
                    "title": "Some Title 2",
                    "media_type": "MEDIA_TYPE_EPUB",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/some_dir_ABC123/Title-0102.wav",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "21.23 MB",
                    "sort_order": "20",
                    "guid": ""
                },
                {
                    "additional_formats": {},
                    "title": "Some Title 3",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/some_dir_ABC123/Title-0103.m4b",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "22.86 MB",
                    "sort_order": "30",
                    "guid": ""
                },
                {
                    "additional_formats": {},
                    "title": "Some Title 4",
                    "media_type": "MEDIA_TYPE_MP3",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/some_dir_ABC123/Title-0104.mp3",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "27.23 MB",
                    "sort_order": "40",
                    "guid": ""
                },
                {
                    "additional_formats": {},
                    "title": "Some Title 5",
                    "media_type": "MEDIA_TYPE_MP3",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/some_dir_ABC123/Title-0105.mp3",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "28.35 MB",
                    "sort_order": "50",
                    "guid": ""
                },
                {
                    "additional_formats": {},
                    "title": "Some Title 6",
                    "media_type": "MEDIA_TYPE_MP3",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/some_dir_ABC123/Title-0106.mp3",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "23.7 MB",
                    "sort_order": "60",
                    "guid": ""
                }
            ],
            "product_sku": "ABC123",
            "created_at": "2019-11-04 23:51:08",
            "last_modified": "2019-11-04 23:51:26"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Somatic Descent 1",
                    "media_type": "MEDIA_TYPE_MP3",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent-0101.mp3",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "20.5 MB",
                    "sort_order": "10",
                    "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7b"
                },
                {
                    "additional_formats": {},
                    "title": "Somatic Descent 2",
                    "media_type": "MEDIA_TYPE_MP3",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent-0102.mp3",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "21.23 MB",
                    "sort_order": "20",
                    "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7c"
                },
                {
                    "additional_formats": {},
                    "title": "Somatic Descent 3",
                    "media_type": "MEDIA_TYPE_MP3",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent-0103.mp3",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "22.86 MB",
                    "sort_order": "30",
                    "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7d"
                },
                {
                    "additional_formats": {
                        "720": {
                            "bucket": "soundstrue-downloads",
                            "key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent_720.mp4"
                        },
                        "1080": {
                            "bucket": "soundstrue-downloads",
                            "key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent_1080.mp4"
                        }
                    },
                    "title": "Somatic Descent VIDEO",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent.mp4",
                    "duration": "14400",
                    "total_pages": "0",
                    "file_size": "22.86 MB",
                    "sort_order": "30",
                    "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7e"
                }
            ],
            "product_sku": "AF04734W",
            "created_at": "2020-01-08 17:49:04",
            "last_modified": "2020-01-08 17:49:04"
        }
    ],
    "count": "2"
}
}
```

---

