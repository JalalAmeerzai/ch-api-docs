# ch-product-service

## Purpose of This Service

- The Product Service holds all the necessary data for those products in relation to the product and business needs.

## Repository
* [Github](https://github.com/stdev/ch-product-service)

## Object Model
* [Product Service Object Model](ObjectModels/Product.md)

## Ports Used
- **port:** `50056`
- **hport:** `5683`

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

The following contains details for the `Create`, `Read`, `Update`, `Delete`, and `List` functionality of the Product Service.

### PRODUCTS

The following are available gRPC methods for general product endpoints.

#### `Create()`
> Creates a product.

##### Accepted Request Arguments
- `product` _(Product object)_

##### Response
- `created` _(bool)_
- `message` _(string)_
	- "Product (sku: `<Product.sku>`) has been created"

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
curl -X POST /api/product \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8,Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"product": {
    	"sku": "BK05536F",
    	"parent_sku": "GP006057",
    	"title": "Alphabreaths",
    	"subtitle": "The ABCs of Mindful Breathing",
    	"body_html": "<p>In <em>Alphabreaths</em>, children will learn their ABCs and the basics of mindfulness through playful breathing exercises. Breaths like Mountain Breath and Redwood Breath will connect them with nature, while breaths like Heart Breath and Wish Breath will help them remember to fill their heart with gratitude and send good wishes to others.</p><p>Simple, playful, and with delightful illustrations, <em>Alphabreaths</em> is the perfect introduction to mindfulness and breath awareness</p>",
    	"format_type": "FORMAT_BOOKS",
    	"published": "true",
    	"published_at": "2019-05-28T0:00:00-05:00",
    	"discount_price": "10.77",
    	"price": "17.95",
    	"inventory_qty": 0,
    	"images": [
        	{
            	"src": "http://example.com/image1.jpg",
            	"sort_order": 10
        	},
        	{
            	"src": "http://example.com/image2.jpg",
            	"sort_order": 20
        	}
    	],
    	"handle": "alphabreaths",
    	"meta_title": "",
    	"meta_description": "",
    	"parent_category": "Meditation",
    	"sub_category": "Mindfulness Meditation",
    	"level": "LEVEL_BEGINNER",
    	"audience": "AUDIENCE_NON_PROFESSIONAL",
    	"duration": "DURATION_HOURS",
    	"authors": [
        	{
            	"name": "Christopher Willard",
            	"url": "https://www.soundstrue.com/store/christopher-willard-pysd.html"
        	},
        	{
            	"name": "Daniel Rechtschaffen",
            	"url": "https://www.soundstrue.com/store/daniel-rechtschaffen.html"
        	}
    	],
    	"vendor": "Sounds True",
    	"barcode": "",
    	"upc": "",
    	"isbn_10": "",
    	"isbn_13": "",
    	"requires_shipping": "",
    	"weight": 0,
    	"weight_unit": "",
    	"taxable": "",
    	"tax_code": "",
    	"equal_buy_links": [
        	{
            	"name": "Amazon",
            	"url": "https://www.amazon.com/Alphabreaths-Breathing-Christopher-Willard-PsyD/dp/1683641973"
        	},
        	{
            	"name": "Barnes & Noble",
            	"url": "https://www.barnesandnoble.com/w/alphabreaths-christopher-willard-psyd/1129070167"
        	},
        	{
            	"name": "IndieBound",
            	"url": "https://www.barnesandnoble.com/w/alphabreaths-christopher-willard-psyd/1129070167"
        	}
    	],
    	"sort_order": 10,
    	"platform": {
        	"name": "PLATFORM_SHOPIFY",
	       "product_id": "",
    	    "product_created_at": "2012-02-15T15:12:21-05:00",
        	"product_updated_at": "2012-08-24T14:01:47-04:00",
        	"options": {
            	"option1": {
                	"name": "Format",
                	"value": "eBook"
            	}
        	}
    	},
    	"created_at": "2019-06-10 00:00:00",
    	"last_modified": "2019-06-10 00:00:00"
	}
}'
```

##### JSON Response Example

```json
{
    "created": true,
    "message": "Product (sku: BK05536F) has been created"
}
```

---

#### `Get()`
> Retrieve a product by **SKU**.

##### Accepted Request Arguments
- `sku` _(string)_

##### Response
- `product` _(Product object)_

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
curl -X GET /api/product/BK05536F \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
```

##### JSON Response Example

```json
{
    "product": {
        "images": [
            {
                "src": "http://example.com/image1.jpg",
                "sort_order": "10"
            },
            {
                "src": "http://example.com/image2.jpg",
                "sort_order": "20"
            }
        ],
        "authors": [
            {
                "name": "Christopher Willard",
                "url": "https://www.soundstrue.com/store/christopher-willard-pysd.html"
            },
            {
                "name": "Daniel Rechtschaffen",
                "url": "https://www.soundstrue.com/store/daniel-rechtschaffen.html"
            }
        ],
        "equal_buy_links": [
            {
                "name": "Amazon",
                "url": "https://www.amazon.com/Alphabreaths-Breathing-Christopher-Willard-PsyD/dp/1683641973"
            },
            {
                "name": "Barnes & Noble",
                "url": "https://www.barnesandnoble.com/w/alphabreaths-christopher-willard-psyd/1129070167"
            },
            {
                "name": "IndieBound",
                "url": "https://www.barnesandnoble.com/w/alphabreaths-christopher-willard-psyd/1129070167"
            }
        ],
        "sku": "BK05536F",
        "parent_sku": "GP006057",
        "title": "Alphabreaths",
        "subtitle": "The ABCs of Mindful Breathing",
        "body_html": "<p>In <em>Alphabreaths</em>, children will learn their ABCs and the basics of mindfulness through playful breathing exercises. Breaths like Mountain Breath and Redwood Breath will connect them with nature, while breaths like Heart Breath and Wish Breath will help them remember to fill their heart with gratitude and send good wishes to others.</p><p>Simple, playful, and with delightful illustrations, <em>Alphabreaths</em> is the perfect introduction to mindfulness and breath awareness</p>",
        "format_type": "FORMAT_BOOKS",
        "published": "true",
        "published_at": "2019-05-28T0:00:00-05:00",
        "discount_price": 10.770000457763672,
        "price": 17.950000762939453,
        "inventory_qty": "0",
        "handle": "alphabreaths",
        "meta_title": "",
        "meta_description": "",
        "parent_category": "Meditation",
        "sub_category": "Mindfulness Meditation",
        "level": "LEVEL_BEGINNER",
        "audience": "AUDIENCE_NON_PROFESSIONAL",
        "duration": "DURATION_HOURS",
        "vendor": "Sounds True",
        "barcode": "",
        "upc": "",
        "isbn_10": "",
        "isbn_13": "",
        "requires_shipping": "",
        "weight": 0,
        "weight_unit": "",
        "taxable": "",
        "tax_code": "",
        "sort_order": "10",
        "platform": {
            "options": {
                "option1": {
                    "name": "Format",
                    "value": "eBook"
                }
            },
            "name": "PLATFORM_SHOPIFY",
            "product_id": "",
            "product_created_at": "2012-02-15T15:12:21-05:00",
            "product_updated_at": "2012-08-24T14:01:47-04:00"
        },
        "created_at": "2019-06-10 00:00:00",
        "last_modified": "2019-06-10 00:00:00"
    }
}
```

---

#### `Update()`
> Update product information.

##### Accepted Request Arguments
- `sku` _(string)_
- `product` _(Product object)_
- **EXCEPTIONS:** This endpoint cannot update the following `Product` attributes:
	- `sku`
	- `options`
	- `created_at`
##### Response
- `updated` _(bool)_
- `message` _(string)_
	- "Product (SKU: `<Product.sku>`) has been updated"
	
##### gRPC Request Example (JS)
```js
// set payload
let payload = {
	sku: req.params.id,
	product: req.body
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
curl -X PUT /api/product/BK05536F \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
  -d '{
	"title": "NEW Alphabreaths",
	"subtitle": "NEW The ABCs of Mindful Breathing",
	"inventory_qty": 100
}'
```

##### JSON Response Example

```json
{
    "updated": true,
    "message": "Product (SKU: BK05536F) has been updated"
}
```

---

#### `Delete()`
> Delete a product by **SKU**.

##### Accepted Request Arguments
- `sku` _(string)_

##### Response
- `deleted` _(bool)_
- `message` _(string)_
	- "Product (sku: `<Product.sku>`) has been deleted"

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
curl -X DELETE /api/product/BK05536F \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json' \
```

##### JSON Response Example

```json
{
    "deleted": true,
    "message": "Product (SKU: BK05536F) has been deleted"
}
```

---

#### `List()`
> Retrieve all Product entities/rows.

##### Accepted Request Arguments
- _N/A_

##### Response
- `count` _(int64)_
- **repeated** `products` _(Product objects)_

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
curl -X GET /api/product/ \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL2NvZ25pdG8taWRwLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL3VzLWVhc3QtMV9sTGkwNHFPMDIiLCJpYXQiOjE1NjQ2OTg0MTcsImV4cCI6MTU5NjIzNDQxNywiYXVkIjoiMThsNGNhcjJzMGRwdThuYzFidXNjYnA2c2giLCJzdWIiOiJhYWFhYWFhYS1iYmJiLWNjY2MtZGRkZC1lZWVlZWVlZWVlZWUiLCJhdXRoX3RpbWUiOiIxNTY0Njk5OTMzIiwidG9rZW5fdXNlIjoiYWNjZXNzIiwidXNlcm5hbWUiOiJqb2huZG9lQGV4YW1wbGUuY29tIn0.ZafS5KN-pz30q12yb8AZ3Oy0-JcznKFSn7lWraJuOi8' \
  -H 'Content-Type: application/json'
```

##### JSON Response Example

```json
{
    "products": [
        {
            "images": [
                {
                    "src": "https://example.com/image_005_thumb.jpg",
                    "sort_order": "1"
                },
                {
                    "src": "https://example.com/image_005_full.png",
                    "sort_order": "2"
                }
            ],
            "authors": [
                {
                    "name": "Author John",
                    "url": "https://example.com/author-john"
                },
                {
                    "name": "Author Jane",
                    "url": "https://example.com/author-jane"
                }
            ],
            "equal_buy_links": [
                {
                    "name": "Amazon",
                    "url": "https://amazon.com/product-title-5"
                }
            ],
            "sku": "CDE005",
            "parent_sku": "ABC123",
            "title": "Product Title 5",
            "subtitle": "Product Subtitle 5",
            "body_html": "<p>Hello, <i>World</i> for product 5!",
            "format_type": "FORMAT_BOOKS",
            "published": "true",
            "published_at": "2019-05-28T0:00:00-05:00",
            "discount_price": 12.75,
            "price": 20.25,
            "inventory_qty": "10",
            "handle": "product-title-005",
            "meta_title": "",
            "meta_description": "",
            "parent_category": "Parent Category",
            "sub_category": "Subcategory",
            "level": "LEVEL_INTERMEDIATE",
            "audience": "AUDIENCE_NON_PROFESSIONAL",
            "duration": "DURATION_DAYS",
            "vendor": "The Vendor",
            "barcode": "9783161480050",
            "upc": "",
            "isbn_10": "",
            "isbn_13": "978-3-16-148005-0",
            "requires_shipping": "true",
            "weight": 3.200000047683716,
            "weight_unit": "pounds",
            "taxable": "true",
            "tax_code": "taxcode",
            "sort_order": "5",
            "platform": {
                "options": {
                    "option1": {
                        "name": "Format",
                        "value": "Books"
                    }
                },
                "name": "PLATFORM_SHOPIFY",
                "product_id": "123456789",
                "product_created_at": "2019-04-28T0:00:00-05:00",
                "product_updated_at": "2019-05-28T0:00:00-05:00"
            },
            "created_at": "2019-11-19 20:43:14",
            "last_modified": "2019-11-19 20:43:14"
        },
        {
            "images": [
                {
                    "src": "https://example.com/image_001_thumb.jpg",
                    "sort_order": "1"
                },
                {
                    "src": "https://example.com/image_001_full.png",
                    "sort_order": "2"
                }
            ],
            "authors": [
                {
                    "name": "Author John",
                    "url": "https://example.com/author-john"
                },
                {
                    "name": "Author Jane",
                    "url": "https://example.com/author-jane"
                }
            ],
            "equal_buy_links": [
                {
                    "name": "Amazon",
                    "url": "https://amazon.com/product-title-1"
                }
            ],
            "sku": "CDE001",
            "parent_sku": "ABC123",
            "title": "Product Title 1",
            "subtitle": "Product Subtitle 1",
            "body_html": "<p>Hello, <i>World</i> for product 1!",
            "format_type": "FORMAT_BOOKS",
            "published": "true",
            "published_at": "2019-05-28T0:00:00-05:00",
            "discount_price": 12.75,
            "price": 20.25,
            "inventory_qty": "10",
            "handle": "product-title-001",
            "meta_title": "",
            "meta_description": "",
            "parent_category": "Parent Category",
            "sub_category": "Subcategory",
            "level": "LEVEL_INTERMEDIATE",
            "audience": "AUDIENCE_NON_PROFESSIONAL",
            "duration": "DURATION_DAYS",
            "vendor": "The Vendor",
            "barcode": "9783161480010",
            "upc": "",
            "isbn_10": "",
            "isbn_13": "978-3-16-148001-0",
            "requires_shipping": "true",
            "weight": 3.200000047683716,
            "weight_unit": "pounds",
            "taxable": "true",
            "tax_code": "taxcode",
            "sort_order": "1",
            "platform": {
                "options": {
                    "option1": {
                        "name": "Format",
                        "value": "Books"
                    }
                },
                "name": "PLATFORM_SHOPIFY",
                "product_id": "123456789",
                "product_created_at": "2019-04-28T0:00:00-05:00",
                "product_updated_at": "2019-05-28T0:00:00-05:00"
            },
            "created_at": "2019-11-19 20:43:13",
            "last_modified": "2019-11-19 20:43:13"
        },
        {
            "images": [
                {
                    "src": "https://example.com/image_006_thumb.jpg",
                    "sort_order": "1"
                },
                {
                    "src": "https://example.com/image_006_full.png",
                    "sort_order": "2"
                }
            ],
            "authors": [
                {
                    "name": "Author John",
                    "url": "https://example.com/author-john"
                },
                {
                    "name": "Author Jane",
                    "url": "https://example.com/author-jane"
                }
            ],
            "equal_buy_links": [
                {
                    "name": "Amazon",
                    "url": "https://amazon.com/product-title-6"
                }
            ],
            "sku": "CDE006",
            "parent_sku": "ABC123",
            "title": "Product Title 6",
            "subtitle": "Product Subtitle 6",
            "body_html": "<p>Hello, <i>World</i> for product 6!",
            "format_type": "FORMAT_BOOKS",
            "published": "true",
            "published_at": "2019-05-28T0:00:00-05:00",
            "discount_price": 12.75,
            "price": 20.25,
            "inventory_qty": "10",
            "handle": "product-title-006",
            "meta_title": "",
            "meta_description": "",
            "parent_category": "Parent Category",
            "sub_category": "Subcategory",
            "level": "LEVEL_INTERMEDIATE",
            "audience": "AUDIENCE_NON_PROFESSIONAL",
            "duration": "DURATION_DAYS",
            "vendor": "The Vendor",
            "barcode": "9783161480060",
            "upc": "",
            "isbn_10": "",
            "isbn_13": "978-3-16-148006-0",
            "requires_shipping": "true",
            "weight": 3.200000047683716,
            "weight_unit": "pounds",
            "taxable": "true",
            "tax_code": "taxcode",
            "sort_order": "6",
            "platform": {
                "options": {
                    "option1": {
                        "name": "Format",
                        "value": "Books"
                    }
                },
                "name": "PLATFORM_SHOPIFY",
                "product_id": "123456789",
                "product_created_at": "2019-04-28T0:00:00-05:00",
                "product_updated_at": "2019-05-28T0:00:00-05:00"
            },
            "created_at": "2019-11-19 20:43:14",
            "last_modified": "2019-11-19 20:43:14"
        },
        {
            "images": [
                {
                    "src": "https://example.com/image_007_thumb.jpg",
                    "sort_order": "1"
                },
                {
                    "src": "https://example.com/image_007_full.png",
                    "sort_order": "2"
                }
            ],
            "authors": [
                {
                    "name": "Author John",
                    "url": "https://example.com/author-john"
                },
                {
                    "name": "Author Jane",
                    "url": "https://example.com/author-jane"
                }
            ],
            "equal_buy_links": [
                {
                    "name": "Amazon",
                    "url": "https://amazon.com/product-title-7"
                }
            ],
            "sku": "CDE007",
            "parent_sku": "ABC123",
            "title": "Product Title 7",
            "subtitle": "Product Subtitle 7",
            "body_html": "<p>Hello, <i>World</i> for product 7!",
            "format_type": "FORMAT_BOOKS",
            "published": "true",
            "published_at": "2019-05-28T0:00:00-05:00",
            "discount_price": 12.75,
            "price": 20.25,
            "inventory_qty": "10",
            "handle": "product-title-007",
            "meta_title": "",
            "meta_description": "",
            "parent_category": "Parent Category",
            "sub_category": "Subcategory",
            "level": "LEVEL_INTERMEDIATE",
            "audience": "AUDIENCE_NON_PROFESSIONAL",
            "duration": "DURATION_DAYS",
            "vendor": "The Vendor",
            "barcode": "9783161480070",
            "upc": "",
            "isbn_10": "",
            "isbn_13": "978-3-16-148007-0",
            "requires_shipping": "true",
            "weight": 3.200000047683716,
            "weight_unit": "pounds",
            "taxable": "true",
            "tax_code": "taxcode",
            "sort_order": "7",
            "platform": {
                "options": {
                    "option1": {
                        "name": "Format",
                        "value": "Books"
                    }
                },
                "name": "PLATFORM_SHOPIFY",
                "product_id": "123456789",
                "product_created_at": "2019-04-28T0:00:00-05:00",
                "product_updated_at": "2019-05-28T0:00:00-05:00"
            },
            "created_at": "2019-11-19 20:43:14",
            "last_modified": "2019-11-19 20:43:14"
        },
        {
            "images": [
                {
                    "src": "https://example.com/image_008_thumb.jpg",
                    "sort_order": "1"
                },
                {
                    "src": "https://example.com/image_008_full.png",
                    "sort_order": "2"
                }
            ],
            "authors": [
                {
                    "name": "Author John",
                    "url": "https://example.com/author-john"
                },
                {
                    "name": "Author Jane",
                    "url": "https://example.com/author-jane"
                }
            ],
            "equal_buy_links": [
                {
                    "name": "Amazon",
                    "url": "https://amazon.com/product-title-8"
                }
            ],
            "sku": "CDE008",
            "parent_sku": "ABC123",
            "title": "Product Title 8",
            "subtitle": "Product Subtitle 8",
            "body_html": "<p>Hello, <i>World</i> for product 8!",
            "format_type": "FORMAT_BOOKS",
            "published": "true",
            "published_at": "2019-05-28T0:00:00-05:00",
            "discount_price": 12.75,
            "price": 20.25,
            "inventory_qty": "10",
            "handle": "product-title-008",
            "meta_title": "",
            "meta_description": "",
            "parent_category": "Parent Category",
            "sub_category": "Subcategory",
            "level": "LEVEL_INTERMEDIATE",
            "audience": "AUDIENCE_NON_PROFESSIONAL",
            "duration": "DURATION_DAYS",
            "vendor": "The Vendor",
            "barcode": "9783161480080",
            "upc": "",
            "isbn_10": "",
            "isbn_13": "978-3-16-148008-0",
            "requires_shipping": "true",
            "weight": 3.200000047683716,
            "weight_unit": "pounds",
            "taxable": "true",
            "tax_code": "taxcode",
            "sort_order": "8",
            "platform": {
                "options": {
                    "option1": {
                        "name": "Format",
                        "value": "Books"
                    }
                },
                "name": "PLATFORM_SHOPIFY",
                "product_id": "123456789",
                "product_created_at": "2019-04-28T0:00:00-05:00",
                "product_updated_at": "2019-05-28T0:00:00-05:00"
            },
            "created_at": "2019-11-19 20:43:14",
            "last_modified": "2019-11-19 20:43:14"
        },
        {
            "images": [
                {
                    "src": "https://example.com/image_003_thumb.jpg",
                    "sort_order": "1"
                },
                {
                    "src": "https://example.com/image_003_full.png",
                    "sort_order": "2"
                }
            ],
            "authors": [
                {
                    "name": "Author John",
                    "url": "https://example.com/author-john"
                },
                {
                    "name": "Author Jane",
                    "url": "https://example.com/author-jane"
                }
            ],
            "equal_buy_links": [
                {
                    "name": "Amazon",
                    "url": "https://amazon.com/product-title-3"
                }
            ],
            "sku": "CDE003",
            "parent_sku": "ABC123",
            "title": "Product Title 3",
            "subtitle": "Product Subtitle 3",
            "body_html": "<p>Hello, <i>World</i> for product 3!",
            "format_type": "FORMAT_BOOKS",
            "published": "true",
            "published_at": "2019-05-28T0:00:00-05:00",
            "discount_price": 12.75,
            "price": 20.25,
            "inventory_qty": "10",
            "handle": "product-title-003",
            "meta_title": "",
            "meta_description": "",
            "parent_category": "Parent Category",
            "sub_category": "Subcategory",
            "level": "LEVEL_INTERMEDIATE",
            "audience": "AUDIENCE_NON_PROFESSIONAL",
            "duration": "DURATION_DAYS",
            "vendor": "The Vendor",
            "barcode": "9783161480030",
            "upc": "",
            "isbn_10": "",
            "isbn_13": "978-3-16-148003-0",
            "requires_shipping": "true",
            "weight": 3.200000047683716,
            "weight_unit": "pounds",
            "taxable": "true",
            "tax_code": "taxcode",
            "sort_order": "3",
            "platform": {
                "options": {
                    "option1": {
                        "name": "Format",
                        "value": "Books"
                    }
                },
                "name": "PLATFORM_SHOPIFY",
                "product_id": "123456789",
                "product_created_at": "2019-04-28T0:00:00-05:00",
                "product_updated_at": "2019-05-28T0:00:00-05:00"
            },
            "created_at": "2019-11-19 20:43:14",
            "last_modified": "2019-11-19 20:43:14"
        },
        {
            "images": [
                {
                    "src": "https://example.com/image_002_thumb.jpg",
                    "sort_order": "1"
                },
                {
                    "src": "https://example.com/image_002_full.png",
                    "sort_order": "2"
                }
            ],
            "authors": [
                {
                    "name": "Author John",
                    "url": "https://example.com/author-john"
                },
                {
                    "name": "Author Jane",
                    "url": "https://example.com/author-jane"
                }
            ],
            "equal_buy_links": [
                {
                    "name": "Amazon",
                    "url": "https://amazon.com/product-title-2"
                }
            ],
            "sku": "CDE002",
            "parent_sku": "ABC123",
            "title": "Product Title 2",
            "subtitle": "Product Subtitle 2",
            "body_html": "<p>Hello, <i>World</i> for product 2!",
            "format_type": "FORMAT_BOOKS",
            "published": "true",
            "published_at": "2019-05-28T0:00:00-05:00",
            "discount_price": 12.75,
            "price": 20.25,
            "inventory_qty": "10",
            "handle": "product-title-002",
            "meta_title": "",
            "meta_description": "",
            "parent_category": "Parent Category",
            "sub_category": "Subcategory",
            "level": "LEVEL_INTERMEDIATE",
            "audience": "AUDIENCE_NON_PROFESSIONAL",
            "duration": "DURATION_DAYS",
            "vendor": "The Vendor",
            "barcode": "9783161480020",
            "upc": "",
            "isbn_10": "",
            "isbn_13": "978-3-16-148002-0",
            "requires_shipping": "true",
            "weight": 3.200000047683716,
            "weight_unit": "pounds",
            "taxable": "true",
            "tax_code": "taxcode",
            "sort_order": "2",
            "platform": {
                "options": {
                    "option1": {
                        "name": "Format",
                        "value": "Books"
                    }
                },
                "name": "PLATFORM_SHOPIFY",
                "product_id": "123456789",
                "product_created_at": "2019-04-28T0:00:00-05:00",
                "product_updated_at": "2019-05-28T0:00:00-05:00"
            },
            "created_at": "2019-11-19 20:43:14",
            "last_modified": "2019-11-19 20:43:14"
        },
        {
            "images": [
                {
                    "src": "https://example.com/image_004_thumb.jpg",
                    "sort_order": "1"
                },
                {
                    "src": "https://example.com/image_004_full.png",
                    "sort_order": "2"
                }
            ],
            "authors": [
                {
                    "name": "Author John",
                    "url": "https://example.com/author-john"
                },
                {
                    "name": "Author Jane",
                    "url": "https://example.com/author-jane"
                }
            ],
            "equal_buy_links": [
                {
                    "name": "Amazon",
                    "url": "https://amazon.com/product-title-4"
                }
            ],
            "sku": "CDE004",
            "parent_sku": "ABC123",
            "title": "Product Title 4",
            "subtitle": "Product Subtitle 4",
            "body_html": "<p>Hello, <i>World</i> for product 4!",
            "format_type": "FORMAT_BOOKS",
            "published": "true",
            "published_at": "2019-05-28T0:00:00-05:00",
            "discount_price": 12.75,
            "price": 20.25,
            "inventory_qty": "10",
            "handle": "product-title-004",
            "meta_title": "",
            "meta_description": "",
            "parent_category": "Parent Category",
            "sub_category": "Subcategory",
            "level": "LEVEL_INTERMEDIATE",
            "audience": "AUDIENCE_NON_PROFESSIONAL",
            "duration": "DURATION_DAYS",
            "vendor": "The Vendor",
            "barcode": "9783161480040",
            "upc": "",
            "isbn_10": "",
            "isbn_13": "978-3-16-148004-0",
            "requires_shipping": "true",
            "weight": 3.200000047683716,
            "weight_unit": "pounds",
            "taxable": "true",
            "tax_code": "taxcode",
            "sort_order": "4",
            "platform": {
                "options": {
                    "option1": {
                        "name": "Format",
                        "value": "Books"
                    }
                },
                "name": "PLATFORM_SHOPIFY",
                "product_id": "123456789",
                "product_created_at": "2019-04-28T0:00:00-05:00",
                "product_updated_at": "2019-05-28T0:00:00-05:00"
            },
            "created_at": "2019-11-19 20:43:14",
            "last_modified": "2019-11-19 20:43:14"
        },
        {
            "images": [
                {
                    "src": "https://example.com/image_009_thumb.jpg",
                    "sort_order": "1"
                },
                {
                    "src": "https://example.com/image_009_full.png",
                    "sort_order": "2"
                }
            ],
            "authors": [
                {
                    "name": "Author John",
                    "url": "https://example.com/author-john"
                },
                {
                    "name": "Author Jane",
                    "url": "https://example.com/author-jane"
                }
            ],
            "equal_buy_links": [
                {
                    "name": "Amazon",
                    "url": "https://amazon.com/product-title-9"
                }
            ],
            "sku": "CDE009",
            "parent_sku": "ABC123",
            "title": "Product Title 9",
            "subtitle": "Product Subtitle 9",
            "body_html": "<p>Hello, <i>World</i> for product 9!",
            "format_type": "FORMAT_BOOKS",
            "published": "true",
            "published_at": "2019-05-28T0:00:00-05:00",
            "discount_price": 12.75,
            "price": 20.25,
            "inventory_qty": "10",
            "handle": "product-title-009",
            "meta_title": "",
            "meta_description": "",
            "parent_category": "Parent Category",
            "sub_category": "Subcategory",
            "level": "LEVEL_INTERMEDIATE",
            "audience": "AUDIENCE_NON_PROFESSIONAL",
            "duration": "DURATION_DAYS",
            "vendor": "The Vendor",
            "barcode": "9783161480090",
            "upc": "",
            "isbn_10": "",
            "isbn_13": "978-3-16-148009-0",
            "requires_shipping": "true",
            "weight": 3.200000047683716,
            "weight_unit": "pounds",
            "taxable": "true",
            "tax_code": "taxcode",
            "sort_order": "9",
            "platform": {
                "options": {
                    "option1": {
                        "name": "Format",
                        "value": "Books"
                    }
                },
                "name": "PLATFORM_SHOPIFY",
                "product_id": "123456789",
                "product_created_at": "2019-04-28T0:00:00-05:00",
                "product_updated_at": "2019-05-28T0:00:00-05:00"
            },
            "created_at": "2019-11-19 20:43:14",
            "last_modified": "2019-11-19 20:43:14"
        }
    ],
    "count": "9"
}
```

---