- Với những event cơ bản như page_view, page_ping thì không cần tạo schema
- Với custome event thì cần custom một schema mới cho event đó  

- Iglu là schema host, nếu muốn cài đặt một custom event thì cần cài đặt Iglu server và kết nối với Enrich component

- Sau khi làm xong, thì cần tạo một schema để gửi tới iglu-server để match với custom event. 

- Các bước tạo schema:
	+ Để gửi event mới đến snowplow, cần tạo một schema mới cho event đó 
	+ Lấy thử một json data mẫu
		* VD:
		```json
			{
			    "productId": "ASO01043",
			    "category": "Dresses",
			    "brand": "ACME",
			    "price": 49.95,
				"sizes": [
					"xs",
					"s",
					"l",
					"xl",
					"xxl"
				],
			    "availableSince": "2013-03-07"
			}
		```
	+ Vào https://jsonschema.net/home
	+ Parse data vào  cột bên trái 
	+ Lấy data ở cột bên phải:
		
		*VD:
		```json
			{
				"$schema": "http://json-schema.org/draft-07/schema",
				"$id": "http://example.com/example.json",
				"type": "object",
				"title": "The Root Schema",
				"description": "The root schema comprises the entire JSON document.",
				"required": [
					"productId",
					"category",
					"brand",
					"price",
					"sizes",
					"availableSince"
				],
				"properties": {
					"productId": {
						"$id": "#/properties/productId",
						"type": "string",
						"title": "The Productid Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
						"ASO01043"
						]
					},
					"category": {
						"$id": "#/properties/category",
						"type": "string",
						"title": "The Category Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
						"Dresses"
						]
					},
					"brand": {
						"$id": "#/properties/brand",
						"type": "string",
						"title": "The Brand Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
						"ACME"
						]
					},
					"price": {
						"$id": "#/properties/price",
						"type": "number",
						"title": "The Price Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": 0,
						"examples": [
						49.95
						]
					},
					"sizes": {
						"$id": "#/properties/sizes",
						"type": "array",
						"title": "The Sizes Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": [],
						"items": {
						"$id": "#/properties/sizes/items",
						"type": "string",
						"title": "The Items Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
							"xs",
							"s",
							"l",
							"xl",
							"xxl"
						]
						}
					},
					"availableSince": {
						"$id": "#/properties/availableSince",
						"type": "string",
						"title": "The Availablesince Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
						"2013-03-07"
						]
					}
				}
			}
		```	

	+ Chuyển thành dạng self-describing json-schema:
		* Tại sao phải dùng dạng json-schema tự định nghĩa?
			+ Trả lời:
				- Khi snowplow được chuyển từ web analytics sang platform event analytics, số lượng những event có thể xảy ra sẽ phát triển cực nhanh. 
				- Để cung cấp một định nghĩa rõ ràng hơn cho event được gửi đến snowplow, ta dùng self-describing event  

		* Để  tạo self describe, add thêm thông tin về:
			+ vendor: Để biết ai là người tạo schema
			+ name: Tên của json schema là gì
			+ format: trong trường hợp này mặc định là "jsonschema"
			+ version: define version của schema, mặc định: "1-0-0"
		* VD:
		```json
			{
				"$schema": "http://json-schema.org/draft-07/schema#",
				"$id": "http://example.com/example.json",
				"self": {
					"vendor": "com.snowplowanalytics",
					"name":  "get_item",
					"format": "jsonschema",
					"version": "1-0-0"
				},
				"type": "object",
				"title": "The Root Schema",
				"description": "The root schema comprises the entire JSON document.",
				"required": [
					"productId",
					"category",
					"brand",
					"price",
					"sizes",
					"availableSince"
				],
				"properties": {
					"productId": {
						"$id": "#/properties/productId",
						"type": "string",
						"title": "The Productid Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
						"ASO01043"
						]
					},
					"category": {
						"$id": "#/properties/category",
						"type": "string",
						"title": "The Category Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
						"Dresses"
						]
					},
					"brand": {
						"$id": "#/properties/brand",
						"type": "string",
						"title": "The Brand Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
						"ACME"
						]
					},
					"price": {
						"$id": "#/properties/price",
						"type": "number",
						"title": "The Price Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": 0,
						"examples": [
						49.95
						]
					},
					"sizes": {
						"$id": "#/properties/sizes",
						"type": "array",
						"title": "The Sizes Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": [],
						"items": {
						"$id": "#/properties/sizes/items",
						"type": "string",
						"title": "The Items Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
							"xs",
							"s",
							"l",
							"xl",
							"xxl"
						]
						}
					},
					"availableSince": {
						"$id": "#/properties/availableSince",
						"type": "string",
						"title": "The Availablesince Schema",
						"description": "An explanation about the purpose of this instance.",
						"default": "",
						"examples": [
						"2013-03-07"
						]
					}
				}
			}
		```

	+ Sau khi tạo json schema như trên, các event khi đi qua json schema sẽ chuyển thành các self-describing có dạng như sau:
		```json
			{
				"schema": "iglu:com.snowplowanalytics/get_item/jsonschema/1-0-0",
				"data": {
						"productId": "ASO01043",
						"category": "Dresses",
						"brand": "ACME",
						"price": 49.95,
						"sizes": ["s", "l"],
						"availableSince": "2013-03-07"
					}
			}
		```
		![Giải thích cấu trúc của schema URI](https://raw.githubusercontent.com/wiki/snowplow/iglu/images/iglu-schema-key.png)
	+ Trước khi gửi data đến snowplow, check schema đã đúng định dạng chưa bằng [online JSON schema validator](https://jsonschemalint.com) 


