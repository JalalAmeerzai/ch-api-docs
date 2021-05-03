# ch-media-metadata-service

## Purpose of This Service

The Media Metadata Service holds all the necessary location data for products with physical file (e.g. audio / video).

## Repository
* [GitHub](https://github.com/stdev/ch-media-metadata-service)

## Object Model
* [Media Metadata Service Object Model](ObjectModels/MediaMetadata.md)

## Access Tokens (JWT)

_ALL_ API endpoints **require** an [AWS Cognito Access Token](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-with-identity-providers.html) (JWT format). All calls need to include this Access Token in an `Authorization` header (with a `Bearer` token) like the following:

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8
```

## Functionality

The following contains details for the `Create`, `Read`, `Update`, `Delete`, and `List` functionality of the UserProduct Service.

### MEDIA METADATA

The following are available gRPC methods for general user endpoints.

#### `Create()`
> Creates a Media Metadata entity (row)

##### REST Endpoint
- **METHOD:** `POST`
- **Endpoint:** `/api/mediametadata/`

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

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/mediametadata/:sku`

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

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/mediametadata/:sku`

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
                "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7b",
                "cloud_front_url": "",
                "resolution": "",
                "dl_downloadable": "true",
                "dl_playable": "true",
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
                "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7c",
                "cloud_front_url": "",
                "resolution": "",
                "dl_downloadable": "true",
                "dl_playable": "true",
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
                "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7d",
                "cloud_front_url": "",
                "resolution": "",
                "dl_downloadable": "true",
                "dl_playable": "true",
            },
            {
                "additional_formats": {
                    "720": {
                        "bucket": "soundstrue-downloads",
                        "key": "video/Somatic Descent_-_AF04734W/mp4/720/Somatic_Descent_MOV_1920_2_2MB_Mp4_Avc_Aac_16x9_1280x720p_24Hz_4.5Mbps_qvbr.mp4",
                        "cloud_front_url": "https://dev-media-converted.soundstrue.com/video/Somatic Descent_-_AF04734W/mp4/720p/Somatic_Descent_MOV_1920_2_2MB_Mp4_Avc_Aac_16x9_1280x720p_24Hz_4.5Mbps_qvbr.mp4?Expires=1582131045&Signature=UjUFQDp1lxOHgT2QV~eRL44fePT3AGymIXuoz67u8ItefCP2zSC74irqN7I~LbuVTU3on7haPQEHZaSfgSsGcme-Gmrwh6AHNJTQUq2tUbYrDoY2XQi~088mKbFiMliExENI2SKjwbQ8TJuDMHknwMHbWV-JN5jps5dbpSRqpgktthSIJB3C8Tr8S1frliCF7~KgtPtbOu~eqWW6G6CTfVs2FS~RycF1FY5c8bPGLQtrg3u7cYaMfYjlvNbwKjgbZHETtbE5TvJt7jDat~bhYSHQX9j8c-Ydd-XBcqG-An88T0RpZ0EbopOWz3wdKhoHx4axH-nie3V2~bid-VI4JQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA"

                    },
                    "480": {
                        "bucket": "soundstrue-downloads",
                        "key": "video/Somatic Descent_-_AF04734W/mp4/480/Somatic_Descent_MOV_1920_2_2MB_Mp4_Avc_Aac_16x9_1280x720p_24Hz_4.5Mbps_qvbr.mp4",
                        "cloud_front_url": "https://dev-media-converted.soundstrue.com/video/Somatic Descent_-_AF04734W/mp4/480p/Somatic_Descent_MOV_1920_2_2MB_Mp4_Avc_Aac_16x9_1280x720p_24Hz_4.5Mbps_qvbr.mp4?Expires=1582131045&Signature=UjUFQDp1lxOHgT2QV~eRL44fePT3AGymIXuoz67u8ItefCP2zSC74irqN7I~LbuVTU3on7haPQEHZaSfgSsGcme-Gmrwh6AHNJTQUq2tUbYrDoY2XQi~088mKbFiMliExENI2SKjwbQ8TJuDMHknwMHbWV-JN5jps5dbpSRqpgktthSIJB3C8Tr8S1frliCF7~KgtPtbOu~eqWW6G6CTfVs2FS~RycF1FY5c8bPGLQtrg3u7cYaMfYjlvNbwKjgbZHETtbE5TvJt7jDat~bhYSHQX9j8c-Ydd-XBcqG-An88T0RpZ0EbopOWz3wdKhoHx4axH-nie3V2~bid-VI4JQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA"

                    },
                    "raw": {
                        "bucket": "soundstrue-downloads",
                        "key": "video/SomaticDescent_AF04734W/raw/Somatic_Descent_MOV_1920_2_2MB.mov",
                        "cloud_front_url": "https://dev-media-converted.soundstrue.com/video/SomaticDescent_AF04734W/raw/Somatic_Descent_MOV_1920_2_2MB.mov?Expires=1582131045&Signature=UjUFQDp1lxOHgT2QV~eRL44fePT3AGymIXuoz67u8ItefCP2zSC74irqN7I~LbuVTU3on7haPQEHZaSfgSsGcme-Gmrwh6AHNJTQUq2tUbYrDoY2XQi~088mKbFiMliExENI2SKjwbQ8TJuDMHknwMHbWV-JN5jps5dbpSRqpgktthSIJB3C8Tr8S1frliCF7~KgtPtbOu~eqWW6G6CTfVs2FS~RycF1FY5c8bPGLQtrg3u7cYaMfYjlvNbwKjgbZHETtbE5TvJt7jDat~bhYSHQX9j8c-Ydd-XBcqG-An88T0RpZ0EbopOWz3wdKhoHx4axH-nie3V2~bid-VI4JQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA"

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
                "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7e",
                "cloud_front_url": "https://dev-media-converted.soundstrue.com/video/Somatic Descent_-_AF04734W/mp4/1080p/Somatic_Descent_MOV_1920_2_2MB_Mp4_Avc_Aac_16x9_1920x1080p_24Hz_4.5Mbps_qvbr.mp4?Expires=1582131045&Signature=UjUFQDp1lxOHgT2QV~eRL44fePT3AGymIXuoz67u8ItefCP2zSC74irqN7I~LbuVTU3on7haPQEHZaSfgSsGcme-Gmrwh6AHNJTQUq2tUbYrDoY2XQi~088mKbFiMliExENI2SKjwbQ8TJuDMHknwMHbWV-JN5jps5dbpSRqpgktthSIJB3C8Tr8S1frliCF7~KgtPtbOu~eqWW6G6CTfVs2FS~RycF1FY5c8bPGLQtrg3u7cYaMfYjlvNbwKjgbZHETtbE5TvJt7jDat~bhYSHQX9j8c-Ydd-XBcqG-An88T0RpZ0EbopOWz3wdKhoHx4axH-nie3V2~bid-VI4JQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                "resolution": "1080p",
                "dl_downloadable": "true",
                "dl_playable": "true",
            }
        ],
        "product_sku": "AF04734W",
        "created_at": "2020-01-08T17:49:04Z",
        "last_modified": "2020-01-08T17:49:04Z"
    }
}
```

---

#### `UpdateFileData()`
> Modifies a single file object data for a product (SKU, S3 Bucket, and S3 Key are required)

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/mediametadata/:sku/file`

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

##### REST Endpoint
- **METHOD:** `DELETE`
- **Endpoint:** `/api/mediametadata/:sku`

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

##### REST Endpoint
- **METHOD:** `PUT`
- **Endpoint:** `/api/mediametadata/:sku/file`

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

##### REST Endpoint
- **METHOD:** `GET`
- **Endpoint:** `/api/mediametadata/filters/:limit/:key?`
    - `key` (a.k.a. `exclusive_start_key`) - The Product SKU of the last product retrieved (the result of `last_evaluated_key` in the response) _(optional)_
    
##### Accepted Request Arguments
- `limit` _(string)_
    - the _amount_ of results to show (i.e. the page amount)
- `exclusive_start_key` _(string)_ (`key` in REST endpoint)
    - This is the result (product_sku) to start the next page set from. This should equal the value of `last_evaluated_key` in the reponse, if applicable.

##### Response
- `count` _(int64)_
- **repeated** `media_metadata` _(MediaMetadata objects)_
- `last_evaluated_key` _(string)_ 
    - This is the last `product_sku` evaluated by the DB. This should be passed as the `exclusive_start_key` in a subsequential call (i.e. make another of the same call passing the value of `last_evaluated_key` to `exclusive_start_key`).

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
		res.json(result);
	}
});
```

##### cURL Request Example

_**NOTE:** The following example call is with a pagination of 14 and key of `ABC123`, starting at the second+ call, and there are 14 results. Therefore the `last_evaluated_key` is the last evaluated `product_sku`.

```bash
curl -X GET /api/mediametadata/filters/14/ABC123 \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
```

##### JSON Response Example

```json
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
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Some Title 2",
                    "media_type": "MEDIA_TYPE_EPUB",
                    "media_s3_bucket": "soundstrue-downloads",
                    "media_s3_key": "products/some_dir_ABC123/Title-0102.wav",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "21.23 MB",
                    "sort_order": "20",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
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
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
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
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
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
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
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
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
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
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
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
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
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
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                }
            ],
            "product_sku": "ABC123",
            "created_at": "2019-11-04 23:51:08",
            "last_modified": "2020-02-13T21:20:00Z"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Qigong-Mpeg-4",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "video/VT00002W_Qigong/mp4/vt00002w_Qigong-MPEG-4_.mp4",
                    "duration": "4723",
                    "total_pages": "0",
                    "file_size": "976.65 MB",
                    "sort_order": "2",
                    "guid": "084cef1a-fd3d-448d-94f2-b5b27300cb5e",
                    "cloud_front_url": "https://dev-media.soundstrue.com/video/VT00002W_Qigong/mp4/vt00002w_Qigong-MPEG-4_.mp4?Expires=1582131039&Signature=SNFONyqhGn1s9heSii-iA2k5gfxz3BQxONtuVjYlTW2J-v0j1hHXtcqOOjs4HabxniSfV5pBTAx8T3g02GB1Q0C7HkC7KDY3d4hxndPCjCSXiX8-KcFYW0M9eBN~zeiSXnTtVS8y5LlDiMu14M4oeQwwSiCc3u7STOp2YO8bKnS-c0Hp-AOTz5CVkqaSs2jiM6sFihi6Vrxh18qMHB8~VDXhlm2BIqvn2vnPwJaIr4fJsmt6l1esmLdM92uWtTpRjSlV0ChOlMvKi3zEHaWnTVccMfZRE-AqOWkgjIbT86Y5fKTG7ZH5KXhTBGcZJUhfPoLFDjZxdTqPQ0dU9PoW0w__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "480p",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Qigongipod",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "video/VT00002W_Qigong/mp4/vt00002w_QigongiPod.mp4",
                    "duration": "4723",
                    "total_pages": "0",
                    "file_size": "653.9 MB",
                    "sort_order": "3",
                    "guid": "12a8bb07-8fea-4700-9e0e-0da1c6270136",
                    "cloud_front_url": "https://dev-media.soundstrue.com/video/VT00002W_Qigong/mp4/vt00002w_QigongiPod.mp4?Expires=1582131039&Signature=eCzu9XjjUTu1eqJzrgEadjxPT6ebNtSq6FFmLCqgSMEB440tIv464USslGDAgtti1FEqKURssWMvnMo6CMYv3GdQBxV8ATMA1cpP6c-vol7my61sOHOpocT2ADWwUwOEtSsPxotNUCw21POV-vBKq3rN1OlEi3Vmy5K9TJ8DGk84WqslLKEI~4nwCOS1Dp~nNgt7NLggDQwCM2Z-rDMltfr17Bl8HX6ztUF5uFghywwwwRXB4Wd1Y17OvEciliIIM-R82YwogOKNOQ14DL4goU6NoEb8Z6~9dQL3Qxg1kcHmsQcC7YJAOfLmw8LquXKzceBPKJZC4peml0x9ZFgLPA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "480p",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Qigongipod",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "video/VT00002W_Qigong/mp4/__vt00002w_QigongiPod.mp4",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "0.0 MB",
                    "sort_order": "1",
                    "guid": "8f1de92b-f5cb-4ba8-ace1-e57a88f90637",
                    "cloud_front_url": "https://dev-media.soundstrue.com/video/VT00002W_Qigong/mp4/__vt00002w_QigongiPod.mp4?Expires=1582131040&Signature=jmSWD4LbaM0eug0spdfEjBYCqgmmcxq8l1NPEZszu9O7wCTYONXxQtTsGLa0RhdCebuz2k-KsEnqHt3hozu7Tjwemba~Eyym0bEGDRADg~HS-pZZqwt2Vmqwcg0RsDz3bPPmDe-JbuiE~Q7APpl0l-klSSqN8sk-Ll2JiAACvmzU3wsbo6f7AGEg0tgR3~xWJm1opqzvMGwaOZDtDHQxpGLaLC-dluv-WTOdn~N6u7ehFQPS1ZvHhCS2J5TX08dabqmiONuKlU8DSTr6O6KwCr3fduftAhUzsq~bl1F~xTDvcTJEZ3sukzlbBbOWPa~D-mexlhPAIUgMdcylt9BP5A__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Sg",
                    "media_type": "MEDIA_TYPE_PDF",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "video/VT00002W_Qigong/VT00002W_SG.pdf",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "3.62 MB",
                    "sort_order": "2",
                    "guid": "ec328fa0-965c-4c52-860d-eb08ea40bcfb",
                    "cloud_front_url": "https://dev-media.soundstrue.com/video/VT00002W_Qigong/VT00002W_SG.pdf?Expires=1582131040&Signature=IZ4PBNlfzVJ7D-DVK3g3eZTImx5cne4BeqdkHpIw2kER2kgf9EP~d9cC1zhhrK2lgFPSEZG0o2XnFJ7578x9oHo7PQiduXdFHc2mTxqWjCLu3C7ke9NK04CWR4GSjAyHsWTgigKko81h0v8JSsi~Ke6UYW5gFag2KCE858H0r3QAuizWwPRYKoC4mIh6VzJ2PEtcagio4Z7JAi6AJgj4waQiNGPq5xkiVdNfmGDsj0gzikpyf9cNY3XivwXoMy7alPfPZS9X7cL1SF3aFdJINem54UOBotMQOcyw1gqNtLiL6IuJy5NdriexQPbwCSICBG26mbDZrhyFzUiqjXCZ3A__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "false"
                },
                {
                    "additional_formats": {},
                    "title": "Qigong - Pdf",
                    "media_type": "MEDIA_TYPE_PDF",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "video/VT00002W_Qigong/VT00002W.pdf",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "0.01 MB",
                    "sort_order": "1",
                    "guid": "4f358b76-cad8-42a3-8b6d-64a819939a1a",
                    "cloud_front_url": "https://dev-media.soundstrue.com/video/VT00002W_Qigong/VT00002W.pdf?Expires=1582131040&Signature=ZFKIa9Ldc5WHE2z-zMmNIGgTFGsIiqOpcDrka0eNUmFiSx512H~-Xz8azmyLRz6PT976u0zfB73pGauJd6sscXrVN8Dh8~cDM9nNYU3sWfHds0fnOrhz7hsve0eFD79hJS-1iBF~N4kb5hr1NXuydKUKVvkvnI2wZ29w4-FJkoCk29AbIuZgAgdJ7vvPAKbpzF6LGNAUDVNG4o5GxEspYbD3KoV1qFkNa6zT7T1e5i-pvtbT33SpKnOxXODZVjR1l~av0DSwVN6BJl927J~Fq2fJH6p7GR6fhil1O1VJjFp6Bdf7ESbgqtlLWWMqLJkIjunu3Y1WgNvYyB6RPuSYsQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "false"
                }
            ],
            "product_sku": "VT00002W",
            "created_at": "2020-02-07 14:49:14",
            "last_modified": "2020-02-07 14:51:23"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Áine Minogue - Celtic Pilgrimage - 01 - The Gathering",
                    "media_type": "MEDIA_TYPE_MP3",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "Audio/MM01231W_Celtic_Pilgrimage/mp3/Áine_Minogue_-_Celtic_Pilgrimage_-_01_-_The_Gathering.mp3",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "11.42 MB",
                    "sort_order": "1",
                    "guid": "67b6edc2-5ec9-4fe3-a18f-59a82a1f4ddc",
                    "cloud_front_url": "https://dev-media.soundstrue.com/Audio/MM01231W_Celtic_Pilgrimage/mp3/%C3%81ine_Minogue_-_Celtic_Pilgrimage_-_01_-_The_Gathering.mp3?Expires=1582131040&Signature=WTLNw3A84XufJycKTtEbIYVe6pPEn1OKhSdrsRjcSq1qFep7fT6BzdX8bNWDbsjBXw~WfcjifjdFGko9E914gHPThZtn~N-T2lby5f4zXW3-I9SYeCAiAtssEHH1yyvTqHrtoDOY95Vjmwc5Mtcd5cSdNF2nAon4NRxMwDg-FAKsxUacOYLnW~m2CI9ShwKnuTOXd8syI94GBM71n9ynAy7tv9H4uWmG~iMH8evrf1KS-ywKgEX7CSgBx2QflbICp8zsjmlg~I8Jz6l-KTJnD364bgaRJwI2w6-2FD3-yPJGsEeMnK465kxjGyrWpgieDBdNUgXURlqIqgc5bwScyA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Áine Minogue - Celtic Pilgrimage - 01 - The Gathering",
                    "media_type": "MEDIA_TYPE_MP3",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "Audio/MM01231W_Celtic_Pilgrimage/mp3/Áine_Minogue_-_Celtic_Pilgrimage_-_01_-_The_Gathering.mp3",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "11.42 MB",
                    "sort_order": "2",
                    "guid": "1aacc963-20e3-4321-bf56-e3c1639e4114",
                    "cloud_front_url": "https://dev-media.soundstrue.com/Audio/MM01231W_Celtic_Pilgrimage/mp3/%C3%81ine_Minogue_-_Celtic_Pilgrimage_-_01_-_The_Gathering.mp3?Expires=1582131040&Signature=WTLNw3A84XufJycKTtEbIYVe6pPEn1OKhSdrsRjcSq1qFep7fT6BzdX8bNWDbsjBXw~WfcjifjdFGko9E914gHPThZtn~N-T2lby5f4zXW3-I9SYeCAiAtssEHH1yyvTqHrtoDOY95Vjmwc5Mtcd5cSdNF2nAon4NRxMwDg-FAKsxUacOYLnW~m2CI9ShwKnuTOXd8syI94GBM71n9ynAy7tv9H4uWmG~iMH8evrf1KS-ywKgEX7CSgBx2QflbICp8zsjmlg~I8Jz6l-KTJnD364bgaRJwI2w6-2FD3-yPJGsEeMnK465kxjGyrWpgieDBdNUgXURlqIqgc5bwScyA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                }
            ],
            "product_sku": "MM01231W",
            "created_at": "2020-02-06 01:26:52",
            "last_modified": "2020-02-06 01:27:50"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Chakra Flow - Introduction",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "video/VT04081W/mp4/Seane_Corn-Chakra_Flow_01_Introduction.mp4",
                    "duration": "279",
                    "total_pages": "0",
                    "file_size": "49.6 MB",
                    "sort_order": "1",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Chakra Flow - The Subtle Body",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "video/VT04081W/mp4/Seane_Corn-Chakra_Flow_02_The-Subtle-Body.mp4",
                    "duration": "1305",
                    "total_pages": "0",
                    "file_size": "232 MB",
                    "sort_order": "2",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Chakra Flow - The Chakras",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "video/VT04081W/mp4/Seane_Corn-Chakra_Flow_03_The-Chakras.mp4",
                    "duration": "1183",
                    "total_pages": "0",
                    "file_size": "210.3 MB",
                    "sort_order": "3",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Chakra Flow - The Chakra Reference Chart",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "video/VT04081W/mp4/Seane_Corn-Chakra_Flow_04_The-Chakra-Reference-Chart.mp4",
                    "duration": "665",
                    "total_pages": "0",
                    "file_size": "118.3 MB",
                    "sort_order": "4",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                }
            ],
            "product_sku": "VT04081W",
            "created_at": "2020-02-10 21:43:08",
            "last_modified": "2020-02-10 21:43:08"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-0702",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0702.m4a",
                    "duration": "249",
                    "total_pages": "0",
                    "file_size": "9.65 MB",
                    "sort_order": "6",
                    "guid": "1ca3d16a-ca80-4edf-afaf-4776207aa038",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0702.m4a?Expires=1582131041&Signature=l~Neb3INAL6ZazT3TAElgUfaGdoDyr-XUhyG1pTqeLL6GjdBDsd7N4Y3w5Asw4yM4FBgO8CssM7WduG-R3q7mqC8Lg42dlPfRZtX4cajgRRRV7kB2BdogKdsMoDC9XnG~zKHhcsuqbZVUBod2iwO7Zw1IIUrDAxTPUofz8bDjAMIGdxfcoh7Ap1uBAQVC3IlGaOloh8j8QsHAWOEAhY3tz91OBIuMneanS8mGwu5AFtBPUmKjCqsk1ZwG8wHodO5Q7-oqrfOivNB1ZZrJ0Srv0cVScQaVF5MT0HKyjELkJmj7Xmt4EMBo5gCB8klOBPT9pRqQwwGYk7PNbrH~3FoZQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-0105",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0105.m4a",
                    "duration": "135",
                    "total_pages": "0",
                    "file_size": "5.29 MB",
                    "sort_order": "1",
                    "guid": "541dbc45-a5a8-4588-9087-35d1b0b37376",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0105.m4a?Expires=1582131041&Signature=MIhUKLNVBSWwpA-koS9aXlB-FXiSqip5PNb2odZWxMNjPChV1ddoBJf4ZNYUoLN66CsuOQBy6CQbLqzqJ0uwJw947-tnBR9BN65y~yUgtmq4TmIM9gMjfQhu2slP8uTjorKO6Z73svKYMR7nALMdaHGgWrg~onpDMnf96g6dwez~-s9rjpeO7UYfCnVuZ12enq2W57HXxwqc8uXLBCfi7JdUJs-L81GQ1pDh5Gnx08~ndD8ddlgd4AZF9pb9ls13WRrQRzLvAVohRbnAQBTlv-YJrjdGkeSZRFNrk5L3dvjK~mBHg2oo-CjCD~5AHhK7aBhJvvP7Eubyl7FX3diBuA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-1001",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-1001.m4a",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "14.2 MB",
                    "sort_order": "9",
                    "guid": "b560dac2-e8ed-4a7d-b578-e53eb3b918d5",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-1001.m4a?Expires=1582131041&Signature=ffcc2ErDedUAxTiNbhFuld8m7wXKlfaAgK6qX8G013uLOWfgRCSH4seW8Y2obADS0lUdW7FeevnOMdQR0K~75lWKOMNv4nS1FP8OZwq9LsX3g~6cXcMvmJ7ZX70NJ7XvapWLteUEJU0ScUU9D5o4qrCgERT3sGQTU1QKFeSo83XWJGLqiDGpMwyuiWKWpQ5gILJKnnZszwPEA6suVwz2qBNz6d05G~SES7T5N9pKDlc4LOT44eZccAcq7fUcyBzQgG0hACMCP89~AukeM2x713O~4NwSL3~r3ohcpgR3HzvfVcuI0vojK5~~J7-4iglVIhb~fUVygpWkQHIFF5ukcA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-0501",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0501.m4a",
                    "duration": "519",
                    "total_pages": "0",
                    "file_size": "20.18 MB",
                    "sort_order": "3",
                    "guid": "8a433af0-956f-4879-8701-e450813ecf21",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0501.m4a?Expires=1582131041&Signature=m9BKSUf1dFZbecDeA34PDcr9ztC5eSK3zRKzGiL7yG0vDAP7iLw3u01kqMMSOZIVoTVivJXGb1jZcf9vqeEJCx84lsGnHgYQCwW-A-XnnX5bVZb7N0NGa0WUnBCRMOsNM~G1bIVs7oMlvL4XnTb-miIiXSx66Q4tmQ2auDssH0LMhplMIyAn~WO9cae74IXnKGQEmjxoHCo1ICfBclBGWQaT4BFAdT7Qm1CIqXK3nYnlpNtTGjRMY9hccFXP2jxp-w1TNPzy1Ei71v9UJRUWZYva2AmVS4ty1N-L~GsbRWYd2hhVs~O7P0irar8KMiQIUZLgHVML1P7NcHdfknrmkQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-0301",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0301.m4a",
                    "duration": "464",
                    "total_pages": "0",
                    "file_size": "18.0 MB",
                    "sort_order": "2",
                    "guid": "e0c54ceb-0e5f-42c8-be0a-b5dd1e8fde96",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0301.m4a?Expires=1582131042&Signature=GXGxyqBDKyH9o4so2yDDEe0gH8vfalCCdbQHGOvK788SFKG68lakehCEPdasuvfxeWVAdmvqQpmzoHnnS1CdUZ-rsG9TPUyk3~YaFfiHoQ1~4TU1Wxei5kZFI8cxg4RuVxgUz4j8-2lN6zKlPoqYsUW6bCPODL6YHVXrEze1MYOfs8zqRo0r~xe8fkxfE~mvuNlcM-rbi9-2beIP4bg7~2-9xxBjA8LVZx4wNdkj3dtuABTO1gSgixDl82cKVz3VK40ngIq-4q50Fa-XuLatFZB3L~D1ESUvjmIZ5QCT~q4Ind6NkCJ4BAwuhT1JBlvxd7Au3qZfXPyPmLjhg9w~dw__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-0502",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0502.m4a",
                    "duration": "358",
                    "total_pages": "0",
                    "file_size": "13.86 MB",
                    "sort_order": "4",
                    "guid": "152f671c-a0e3-42f4-bd99-8f14f9f4b54e",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0502.m4a?Expires=1582131042&Signature=mvF43QWxdbYPlncC9T12KFTvkRqtdrCDZ0XcPwpiodXK9SmdtaYtFH07AzPLd0KbTfhDeBSqVzlVYKvimDBbBhaXXbGsILQBN81QELMT0VAgw3yMPjr~ijx7vzNQ4GvapiQiRUSx20lXCr2Et7FTNsVvkbX6rNMNqOJF4sQnGZ54MSVYACfs-XLsfRom7BGikmd5fHE~zcvGfZG2kujOMHBC2BElLJoz6JhYEuASE7uHEKmjFuXg0desPVr4CjhBOTgGRl4A10ildyRDdMoDdFwMq2~7POhBnyaUiorAsd6V0PcYq039nfPiUh9LaeDej2apAasJHHSZhJFh~HeZSA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-1107",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-1107.m4a",
                    "duration": "141",
                    "total_pages": "0",
                    "file_size": "5.57 MB",
                    "sort_order": "10",
                    "guid": "1de7ad61-7a38-452c-97fe-a1ff75b62bfd",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-1107.m4a?Expires=1582131042&Signature=D7Dx5j~fx5OV7Ycua2ZFv9V7qLMzX6F2bJoJEmrJevzVb3y0qnVZ1H10a~ayXtOP~pa1QaodsPfmlsvx2SpjZLAAfgcgpdNifKt8qvxqgYqV68oNsiGEulNi7oRQQKSiQHw7AGtpg4-JmbbfXeGxob2g6ZCvu0XjWxsWSimeqd6-Zp6MTjPp9niD2xBTrrZAOxpdt5GHSt4Ze-fkW0uE52wgvEnz-1bnQlUn3ZzkuEv7~RGEQTND2RMzWzG4yFnG5Duph92OfhF~5BI~06rhh61SlCDcte8~BDG4-ARKxNO3VLw0rJKls-tYeyAnMw0xCEOt0tP2u~9iiIHMBCZ04g__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-0603",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0603.m4a",
                    "duration": "577",
                    "total_pages": "0",
                    "file_size": "22.2 MB",
                    "sort_order": "5",
                    "guid": "d95b9ce0-0c88-43f0-bc38-97a7dabe24e4",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0603.m4a?Expires=1582131042&Signature=defov1hAO6cr0RnJAtrodobMTsp6F4n0Qc4pkRIZkcnTu5uYVKPB7-mUIbDeMBHFHFFfGFQIoSWnPuZWa1hfh5ycek2c4Tdtn91IyjlWAwgIdjr-imUwsSh-WaZJwlMLqHPGLge~ISMQNS5cTDdJu-iHK3QG3PTESAVQEk4JaFt0KeCBXhqta5GmdFDL5B5QbOkMXmM6MOywsrByieNMBTFPW-62UHoOwwkOMBJzjNxzhUfkSotybpF8ngiHniOzYAIZ1-hUBwMfgdUFsw3-Y2hEvMLtN-Cx6C~JaL~RMSWnkdss8DkjEhxPxeqBaoJO5oG0rJfaLybsFsxGjUeZEA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-0710",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0710.m4a",
                    "duration": "474",
                    "total_pages": "0",
                    "file_size": "18.24 MB",
                    "sort_order": "7",
                    "guid": "eb8331d6-4e32-48de-9cae-fb2afc2b97ef",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0710.m4a?Expires=1582131043&Signature=AmM554R25kTBlLwxY93Z9qj2VydGsQKpd4mjNvpQAdI-iu-2ToL7lYpkjea-WA7XxKsmxZBaufy9MFeKs389ujoEzQcWpaeUrvAIwK~jJETzRUmoyS80~2byRlgmRnV4cd3FFZxfeOhfnV~LgsSl8CnR9gkTI9u4T8opqdaGFniKwZWlNKyK3RaEiWz0P-DLMNrryYACZyuDVFa~ZG8iNXefCmTph8LBeXOFfgar51jwE0TrABdaStZFuPWyauYEKfCDpDxdVat-sS55O02X7aiz~8RA2~vt4X2BVbmUumaa2XxVLLOoTS9BVCFUCopphSY76VB~5diOwyurAOd7AQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Jena La Flamme-Pleasurable Weight Loss-0711",
                    "media_type": "MEDIA_TYPE_M4A",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0711.m4a",
                    "duration": "503",
                    "total_pages": "0",
                    "file_size": "19.56 MB",
                    "sort_order": "8",
                    "guid": "482b0858-77f4-457f-90a1-3f0811942f9a",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/m4a/Jena_la_Flamme-Pleasurable_Weight_Loss-0711.m4a?Expires=1582131043&Signature=E3uyU6389sw3chj6TWqrW-SnBMEudbhVqRVqH8FwZc7hfriG5IJQeLUU6nKyB8wNe-VC9I9vVFuU25W0t22oWQXDLOTC0zJzAJ41rlR29G4JtiD43oOysAPCyfZ0E0U2Gy~p1GmdgmqtQ3RVYCLOwBFWBQPmXthbHB9hEVTMYM2OZBzGeFkQUW7CuXnPrCXp0zFnbDrgSu0znVu7SzLnYneHcUo~Of7xVAeCuzqJey~kzUe4g~XPuAkjwq~eWjhgFkFjFkYNNYmakdvru7AKd7WqBfIMF2WOT8yhQN8bx5O6b-qhwF88ryk4-3bxmA5dbo5KwOMjCgI4i1R7KGHR~w__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Laflamme-Table-Of-Contents",
                    "media_type": "MEDIA_TYPE_PDF",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04306W__Pleasurable_Weight_Loss/laFlamme-Table-of-Contents-AB04306W.pdf",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "0.62 MB",
                    "sort_order": "1",
                    "guid": "00d1b890-cee6-433b-8c23-7e773557a68a",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04306W__Pleasurable_Weight_Loss/laFlamme-Table-of-Contents-AB04306W.pdf?Expires=1582131043&Signature=OFwB01DEbrwONVpHhjDD43VTId8BO6pGjJ6~v6lkabVCWDeFJeYjZLHAaTs0wGVM3FVx8Qr-oChukbin2ER6g7b5JVfePEQFXKepYCyb~LPbi59RwQkX1EiuuvVwMRZz9rNysSJT41jPs3SIMkH7eQs~2ABaw9VL~jxSm4YWOAFfRFUsWpvDEiSzM1VRNIIA5iSuRJMPqi2qbaGbQcxVL6Xi~~nUpu-Ql0bBNdV5P49PeuwH2yoRUNga-psjIYUXO64-QKVq9yWUpoaerm3BtzPvo6XsEvREf8o6du~GO3DMjQPkANpcvQP-2Iznge5~o4K3fE1qC2SIzvGL2t3cKQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "false"
                }
            ],
            "product_sku": "AB04306W",
            "created_at": "2020-02-07 14:56:46",
            "last_modified": "2020-02-07 14:57:49"
        },
        {
            "file_data": [
                {
                    "additional_formats": {
                        "480": {
                            "bucket": "st-test-convert-us-east-1",
                            "key": "video/New_Video_Product_-_VT03594W/mp4/480p/file_example_MOV_1920_2_2MB_someting,something_somethingelse_Mp4_Avc_Aac_16x9_858x480p_24Hz_3Mbps_qvbr.mp4",
                            "cloud_front_url": "https://dev-media-converted.soundstrue.com/video/New_Video_Product_-_VT03594W/mp4/480p/file_example_MOV_1920_2_2MB_someting,something_somethingelse_Mp4_Avc_Aac_16x9_858x480p_24Hz_3Mbps_qvbr.mp4?Expires=1582131044&Signature=Cg6yfvI05UKTwtdGNJXWpUA1Rf9a5ZfS3JdJYsoMCwqMHdApp1HGxNHJWTh2GXJn-1SKLRErucPQYAb4K~XSV-ay1dqFujBkEzjfOGPBMIVslOI8yiNzQpwMAFIqIpnn4Xvn9rmo~6snHUy4pjQzcyvvabs1t4a2O4PP-mUK40pYZ3RgG7bHvA2KAt4EIK5WaPjDc6lqbkvQUcLN7KHeVQZdYez1-mqEQS1Re6A34WDZN2OlrdIYYAKifvH7ltB7BIyb4umis7293f~Xg544puDfTeg-zHP13~PoloETGlUIlOPca7RDRjKWwtAK18uuvLwYtj9KbPcUg405tPh~FQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA"
                        },
                        "720": {
                            "bucket": "st-test-convert-us-east-1",
                            "key": "video/New_Video_Product_-_VT03594W/mp4/720p/file_example_MOV_1920_2_2MB_someting,something_somethingelse_Mp4_Avc_Aac_16x9_1280x720p_24Hz_4.5Mbps_qvbr.mp4",
                            "cloud_front_url": "https://dev-media-converted.soundstrue.com/video/New_Video_Product_-_VT03594W/mp4/720p/file_example_MOV_1920_2_2MB_someting,something_somethingelse_Mp4_Avc_Aac_16x9_1280x720p_24Hz_4.5Mbps_qvbr.mp4?Expires=1582131044&Signature=kQ6kEo7ZDjm-bcJXqafKdicq0YDtUDL8eYmyGv5UXr0UlxFV~E4fbMLVy17QEdUEIb94LPled-n7xJPbnco9RIuUlH7AGmi79hfH2IEVIJnJvno46eMpMrmxx~PMNn1jV76yRhhqgVaLOBshqiE7BiBXMmSKcpIWbn-7wmrSwVhonQfZXCy-r2fT46GzDYlA3jqiPUc-vg0fOe-Z-~WIk7UIgdE2IgM9IzLbhYqnpAtHJaj4dgJI-Rr7UVkRrj7F8q8wDELjVcmxISsVlq-GkfWwDF91ybKVgLt-m6kpQ4-edpSHR5YaREJYO~qdTXN71FWTc5QZaMhd3ZLH4EHPog__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA"
                        },
                        "raw": {
                            "bucket": "st-test-mc-bucket-2-389739",
                            "key": "video/New_Video_Product_-_VT03594W/raw/file_example_MOV_1920_2_2MB_someting,something_somethingelse.mov",
                            "cloud_front_url": "https://dev-media.soundstrue.com/video/New_Video_Product_-_VT03594W/raw/file_example_MOV_1920_2_2MB_someting%2Csomething_somethingelse.mov?Expires=1582131043&Signature=SL-bfQLZrFbRFEzpQWqw7B2vWbS7Zh6wrUGn~lmq5uCuoJT1Lt8ZebqUFBSOw03HOyVbnPpsdpBx0V1~-K9DvCH0GsxPJotURUzIC-1Ke9QgqHDvK2pUp5knYQK2m7Ljzm~mcCt7162L180ZIirqbFQue7CmN2OH5UyY79Kgk~sHWLoLgm7H2kNT84bPa6FCHq8wWUnuKXGIMRHgDddm3gqPkDHlSHcjawTYOvAeBMhuBam--xaHHXJbL72nJiT~fHPhNKHX5QZIrZVvEgcMSObXUB2ZrjEhCjIy-1B4Nv0yCtI7fDu36XZHVWM2K5-mD4yI-Wm-d5PEhR4czbMW8Q__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA"
                        }
                    },
                    "title": "File Example Mov 1920 2 2Mb Someting,Something Somethingelse",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-convert-us-east-1",
                    "media_s3_key": "video/New_Video_Product_-_VT03594W/mp4/1080p/file_example_MOV_1920_2_2MB_someting,something_somethingelse_Mp4_Avc_Aac_16x9_1920x1080p_24Hz_6Mbps_qvbr.mp4",
                    "duration": "30",
                    "total_pages": "0",
                    "file_size": "2.25 MB",
                    "sort_order": "1",
                    "guid": "edd7c511-169b-4314-860c-343176a23d45",
                    "cloud_front_url": "https://dev-media-converted.soundstrue.com/video/New_Video_Product_-_VT03594W/mp4/1080p/file_example_MOV_1920_2_2MB_someting,something_somethingelse_Mp4_Avc_Aac_16x9_1920x1080p_24Hz_6Mbps_qvbr.mp4?Expires=1582131043&Signature=AbdlCW78a7nGKZI2j9BHvEwXrSfXr0U3HvpXA26yTFzkrO2UjP0u2g6don-sAGVKzYFQwFWcu~cQz~88h9zZ2hzY-od6fBHa~20~nqj50I2epbBJO6FTuIZtUyzHZg5vmZhmhTI-Wz9bp1x5JYFUltvz1B7zYJeRv-aPT16BPCMjusqPV6l-bXZ8W3VZP6B~fMT2RNGsnbxOoqo7My8OtLTuRAKmyFJADKpzs4YLdbhJJ028L9iIZI~giRxvYEfB5ERirGKw4WbWnoaP3x0HpJHaHbZ05kJmNAWM0hzgvCwjYpXyq2bDRG1IguxQD1Kri6Z-kDy5~tgIpfPdvGhJmA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "1080p",
                    "dl_downloadable": "true",
                    "dl_playable": "true",
                }
            ],
            "product_sku": "VT03594W",
            "created_at": "2020-02-07 17:02:46",
            "last_modified": "2020-02-07 17:03:28"
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
                    "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7b",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
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
                    "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7c",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
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
                    "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7d",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {
                        "720": {
                            "bucket": "soundstrue-downloads",
                            "key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent_720.mp4",
                            "cloud_front_url": ""
                        },
                        "1080": {
                            "bucket": "soundstrue-downloads",
                            "key": "products/SomaticDescent_AF04734W/Reginald_A_Ray-Somatic_Descent_1080.mp4",
                            "cloud_front_url": ""
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
                    "guid": "11e2d1c7-7c60-4999-b4f1-6f5c7b25ff7e",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                }
            ],
            "product_sku": "AF04734W",
            "created_at": "2020-02-19T16:20:32Z",
            "last_modified": "2020-02-19T16:20:32Z"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Inside The Miracle",
                    "media_type": "MEDIA_TYPE_UNKNOWN",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04298W_Inside_the_Miracle/m4b/Inside_the_Miracle.m4b",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "852.7 MB",
                    "sort_order": "1",
                    "guid": "7ff0bf14-b750-40b5-8788-91dc9d081d56",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04298W_Inside_the_Miracle/m4b/Inside_the_Miracle.m4b?Expires=1582131044&Signature=T2Flu2ZL0H9xwlyasxgoTUx-p7u9UCeghbO9HNj9vKKy2PHaIUw9ADNWzYyvm54XrVfcO5Cl8u5kCrocqBXojFfS4GFeNm26RTbJq3K7bh4bzAz5Zk6Y8n7ISsePDJ2ND~Ib8uKOQvbCtQy-mhUTuMs-VLrTh-KS~3fBVt7keus-b4K7kfi4csnebaklrZNb2cjNDas9dxtUYGjo2Dd1Gxj0NInxQL46O6hKe0PLwFfkQ63rksuIqEKx1LMhLG8AWpxfwSoxJAS0m-edhfmGicVQErQqfxDpVtFRMyoh3T81hMBVQv8WcS1hbVElJp6Frun0fLwhUTleRPLn-j~ehw__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "Nepo-Table-Of-Contents",
                    "media_type": "MEDIA_TYPE_PDF",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "audio/AB04298W_Inside_the_Miracle/Nepo-Table-of-Contents-AB04298W.pdf",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "5.27 MB",
                    "sort_order": "1",
                    "guid": "93f58cca-2d62-4dcb-82d5-ce81f046eaa0",
                    "cloud_front_url": "https://dev-media.soundstrue.com/audio/AB04298W_Inside_the_Miracle/Nepo-Table-of-Contents-AB04298W.pdf?Expires=1582131044&Signature=njUfmYgtaKC-308WckQO5vldxhq4sY2erwbgyy8GxlJWhDe8PBE5F9uIWc1wx3SPlkuv9ivNQ11f64hy0Tiplb1Heso0XdWR5KlhbPokXO2O4AWluxgZQMMVYFnQ-T3gofAhlOLsNweknztiXEEcmn3HErKuYNsvW4rRShkJezrhz9J6XDWRQ1A4GuDhPMTaNJ51PBcoALIhcWUQG2hLO3fWQaF1I4b5L8KrDYxOJk7Gk48jEMYVCUNHUFk55OllxqNi-fCopmCMixUDcR6Hy9HaznIf0eLNBiTEDjULpNC7I1YOHWxatBFs9NbfSnq94-zsDCRAYjf63Jevlm-XEw__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "false",
                }
            ],
            "product_sku": "AB04298W",
            "created_at": "2020-02-07 15:15:59",
            "last_modified": "2020-02-07 15:17:03"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Break Through Pain",
                    "media_type": "MEDIA_TYPE_PDF",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "Ebook/BD00849E-BreakThroughPain/9781604077278_Break_Through_Pain.pdf",
                    "duration": "0",
                    "total_pages": "108",
                    "file_size": "11.42 MB",
                    "sort_order": "1",
                    "guid": "7dc5fef5-334d-47af-bc2c-70ce2a404835",
                    "cloud_front_url": "https://dev-media.soundstrue.com/Ebook/BD00849E-BreakThroughPain/9781604077278_Break_Through_Pain.pdf?Expires=1582131045&Signature=Sr1n9zDMEVjkIaFFvbUOFQz0Pr1U3irqZ2qfBVUnRoaZzSmh~wCefw16d0Ko9L9nWbQu0suF3v1sbIntSgHoVCDl846wAx54-bwWRuX1a2xycUvxe7Zlr-7nwgH7Fabuz15rKBxq9StAL8LllDoHuDmzmKe9pgDPDyZqIBR~i680ayGBL96yfhRxJvykQuUtZufajQwfJaUji5lszI6m95VpqR~JdS854XcQQcgx6kN2VBLURCUtS59qVzYZBt3nz0g4zYsoYe3RCgw9~oDviLcd9~7FGFp6nzioaZeoawX2W4LgEnrCZHA8HhaD-j~MvZSLvAjVUXBnHxFMHnf4sA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "false"
                },
                {
                    "additional_formats": {},
                    "title": "Break Through Pain",
                    "media_type": "MEDIA_TYPE_EPUB",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "Ebook/BD00849E-BreakThroughPain/9781604077278_Break_Through_Pain.epub",
                    "duration": "0",
                    "total_pages": "108",
                    "file_size": "30.13 MB",
                    "sort_order": "1",
                    "guid": "1035cfd6-6cb4-42fb-a85e-ff1e3c374db3",
                    "cloud_front_url": "https://dev-media.soundstrue.com/Ebook/BD00849E-BreakThroughPain/9781604077278_Break_Through_Pain.epub?Expires=1582131045&Signature=F9lSMA8wxaqqDeGhzHhdVyBfw0ky1REFi51XvC1prDZ252Wz0UmUE0sS3ckKrx2y1EO7V2yl5qg1JWzrc5izMljUN3EBjQW9iZ45EdO17Kk9HVgybsOkOZjpQoCSK6tz6Aj0W7VJ-kL~MuW3cP8RqK7Ukj9JfMF48a7n17G7ko~GDbaveRLEj4cUCaGT-riFdFNhABM3cyh5vgERzEJmYhrrG-5SB16Vc-tFoqGkVPsphMu0F-Z5ZnSQAtFOxu5~RENbPOnoIaFN-jqBXxBGuXLaP8-LrrTLL7bn2QJH3PHZkN2JFGqJCmlw4wyXctMqxmHNEuGyDLbnnSx1CSkFiA__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "false",
                    "dl_playable": "false"
                },
                {
                    "additional_formats": {},
                    "title": "Breakthroughpain Cover",
                    "media_type": "MEDIA_TYPE_JPG",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "Ebook/BD00849E-BreakThroughPain/9781604077278_BreakThroughPain_cover.jpg",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "0.2 MB",
                    "sort_order": "1",
                    "guid": "1741b071-2457-4ee7-9c8f-6f94a96878bd",
                    "cloud_front_url": "https://dev-media.soundstrue.com/Ebook/BD00849E-BreakThroughPain/9781604077278_BreakThroughPain_cover.jpg?Expires=1582131045&Signature=EHzqozYUFlqED4OsFwOEWzV5w7as14n17bp0LVgucUvx32IOU3OA9zySXFENaYXv~UFiUijFzGtv0ChFVh7vQOeCslXIKXuRZ~wB8pMaLbITbZTFwGcL0i7peMWlwGsSXA~2TLZIu0mFmI6TAouFGzRw2RQ5Gt1NuCFd0xTbonz5aabOsDUUSfi58hreeZyoTiCESCOzHk80rhO6b6hZMy6lFL0R5Vuq-hIy~BqCj~nlqa2rf1p1QXdQfXZk4SXzaH9UZ1xRlGdADXhXc2AtfFe6qa2ekZespKT3dell3xNogJMrWsRRsMjoH6yUPn8hZwcKFjY4UGUREJB9nBmzkQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "false"
                }
            ],
            "product_sku": "BD00849E",
            "created_at": "2020-01-27 18:27:49",
            "last_modified": "2020-02-04 15:26:44"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Alphabreaths",
                    "media_type": "MEDIA_TYPE_UNKNOWN",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "books/BK05537F/Alphabreaths-BK05537F.epub",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "50.1 MB",
                    "sort_order": "1",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "false",
                    "dl_playable": "false"
                }
            ],
            "product_sku": "BK05536F",
            "created_at": "2020-01-30 20:57:31",
            "last_modified": "2020-01-30 20:57:31"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "QIGONG",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "video/VT00002W/iPod-mp4/vt00002w_QigongiPod.mp4",
                    "duration": "4723",
                    "total_pages": "0",
                    "file_size": "623.6 MB",
                    "sort_order": "1",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                }
            ],
            "product_sku": "VT0002W",
            "created_at": "2020-02-11 00:11:44",
            "last_modified": "2020-02-11 00:13:35"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "A LAMP IN THE DARKNESS",
                    "media_type": "MEDIA_TYPE_UNKNOWN",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "books/BD03823W/Lamp-In-Darkness-BK03823W.epub",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "1.1 MB",
                    "sort_order": "1",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "false",
                    "dl_playable": "false"
                }
            ],
            "product_sku": "BD03823W",
            "created_at": "2020-02-10 22:18:52",
            "last_modified": "2020-02-10 22:18:52"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "Remote Viewing - Epub",
                    "media_type": "MEDIA_TYPE_EPUB",
                    "media_s3_bucket": "st-test-mc-bucket-2-389739",
                    "media_s3_key": "ebook/BD00887W_-_Remote_Viewing/9781591798682.epub",
                    "duration": "0",
                    "total_pages": "0",
                    "file_size": "4.55 MB",
                    "sort_order": "1",
                    "guid": "3a0b0536-3df4-4121-8426-749a710fb3ad",
                    "cloud_front_url": "https://dev-media.soundstrue.com/ebook/BD00887W_-_Remote_Viewing/9781591798682.epub?Expires=1582131045&Signature=UjUFQDp1lxOHgT2QV~eRL44fePT3AGymIXuoz67u8ItefCP2zSC74irqN7I~LbuVTU3on7haPQEHZaSfgSsGcme-Gmrwh6AHNJTQUq2tUbYrDoY2XQi~088mKbFiMliExENI2SKjwbQ8TJuDMHknwMHbWV-JN5jps5dbpSRqpgktthSIJB3C8Tr8S1frliCF7~KgtPtbOu~eqWW6G6CTfVs2FS~RycF1FY5c8bPGLQtrg3u7cYaMfYjlvNbwKjgbZHETtbE5TvJt7jDat~bhYSHQX9j8c-Ydd-XBcqG-An88T0RpZ0EbopOWz3wdKhoHx4axH-nie3V2~bid-VI4JQ__&Key-Pair-Id=APKAIPGHOJ2BKOMIM4BA",
                    "resolution": "",
                    "dl_downloadable": "false",
                    "dl_playable": "false"
                }
            ],
            "product_sku": "BD00887W",
            "created_at": "2020-02-07 03:32:15",
            "last_modified": "2020-02-07 03:32:15"
        },
        {
            "file_data": [
                {
                    "additional_formats": {},
                    "title": "MASTER SERIES DVD 1",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "video/VT03943W/mp4/playable-streaming/-pd/Master+Series+DVD1_pd.mp4",
                    "duration": "3282",
                    "total_pages": "0",
                    "file_size": "584.2 MB",
                    "sort_order": "1",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "MASTER SERIES DVD 2",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "video/VT03943W/mp4/playable-streaming/-pd/Master+Series+DVD2_pd.mp4",
                    "duration": "3996",
                    "total_pages": "0",
                    "file_size": "711.6 MB",
                    "sort_order": "2",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "MASTER SERIES DVD 3",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "video/VT03943W/mp4/playable-streaming/-pd/Master+Series+DVD3_pd.mp4",
                    "duration": "2850",
                    "total_pages": "0",
                    "file_size": "507.7 MB",
                    "sort_order": "3",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                },
                {
                    "additional_formats": {},
                    "title": "MASTER SERIES DVD 4",
                    "media_type": "MEDIA_TYPE_MP4",
                    "media_s3_bucket": "st-test-media-2019",
                    "media_s3_key": "video/VT03943W/mp4/playable-streaming/-pd/Master+Series+DVD4_pd.mp4",
                    "duration": "3598",
                    "total_pages": "0",
                    "file_size": "640.8 MB",
                    "sort_order": "4",
                    "guid": "",
                    "cloud_front_url": "",
                    "resolution": "",
                    "dl_downloadable": "true",
                    "dl_playable": "true"
                }
            ],
            "product_sku": "VT03943W",
            "created_at": "2020-02-10 23:09:57",
            "last_modified": "2020-02-10 23:09:57"
        }
    ],
    "last_evaluated_key": "ABC789",
    "count": "14"
}
```

---

