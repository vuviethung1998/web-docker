Các event được tracking trên snowplow: 	

    3.1 Pageview events
    3.2 Page pings
    3.3 Link clicks
    3.4 Ad impressions
    3.5 Ecommerce transactions
    3.6 Social events
    3.7 Item views
    3.8 Error tracking
    3.9 Custom structured events
    3.10 Custom unstructured events  


- Pageview tracking: Record số lượng view của một trang 
	- Các parameter được tracking:
		- event: pageview 
		- url: http%3A%2F%2Ftest.psybazaar.com%2F2-tarot-cards
		- page:  Tarot%20cards    			 	//page title 
		- refr: http%3A%2F%2Ftest.psybazaar.com%2F 		// referrel url  
		- //Document character set
		- //Domain user ID
		- //Domain session ID
		- // Browser language
		- // Client time zone


- Page pings: Record mức độ người dùng quan tâm đến content của trang web sau khi load xong (VD: xem người dùng scroll sâu đến bao nhiêu % của trang web )
	- Các parameter được tracking:
		- pp_mix: Minimum page x offset seen in the last ping period 	
		- pp_max: Maximum page x offset seen in the last ping period
		- pp_miy: Minimum page y offset seen in the last ping period
		- pp_may: Maximum page y offset seen in the last ping period
	- Ví dụ: 
		e=pp        // Page ping
		// Max and min x and y offsets
		pp_mix=0    
		pp_max=7
		pp_miy=0
		pp_may=746

		// Other relevant fields
		duid=91a88a7ec90ebbb1 // Domain user id
		vid=1                 // Domain session index
		page=Tarot cards - Psychic Bazaar    // Page title
		refr=http=//www.psychicbazaar.com/   // Page referrer
		url=http://www.psychicbazaar.com/2-tarot-cards // Page URL

		tid=344664            // Transaction ID
		dtm=1361534887845     // Client timestamp
		vp=1105x390           // Viewport dimensions
		ds=1097x1413          // Document dimensions
		aid=pbzsite           // App ID
		lang=en-GB            // Browser language
		cs=UTF-8              // Docuemnt characterset
		res=1920x976          // Monitor resolution / size


- Link click: Record các link đã click. Mỗi click event được lưu trong một schema 
	- Ví dụ:
		
		{
			"$schema": "http://iglucentral.com/schemas/com.snowplowanalytics.self-desc/schema/jsonschema/1-0-0#",
			"description": "Schema for a link click event",
			"self": {
				"vendor": "com.snowplowanalytics.snowplow",
				"name": "link_click",
				"format": "jsonschema",
				"version": "1-0-0"
			},

			"type": "object",
			"properties": {
				"elementId": {
					"type": "string"
				},
				"elementClasses": {
					"type": "array",
					"items": {
						"type": "string"
					}
				},
				"elementTarget": {
					"type": "string"
				},
				"targetUrl": {
					"type": "string",
					"minLength": 1
				}
			},
			"required": ["targetUrl"],
			"additionalProperties": false
		}
			
- Ad impression tracking: (deprecated)
		
- Ecommerce transaction tracking: track một giao dịch điện tử , log dữ liệu chi tiết của item trong giao dịch đó  
	- Các parameter:
		- Transaction parameter:
			VD:
				duid=aeb1691c5a0ee5a6   // Domain user ID  
				&vid=2                  // Domain session index
				&aid=1                  // App ID

				&e=tr            	// Transacton event type.
				&tr_id=12345        // Order ID
				&tr_af=westernWear 	// Affiliation
				&tr_tt=19.99 		// Transaction total value
				&tr_tx=4.99 		// Transaction tax value
				&tr_sh=2.99 		// Transaction shipping price
				&tr_ci=london		// City on customer address
				&tr_st=london 		// State on customer address
				&tr_co=united kingdom	// Country on customer address
				&tr_cu= GBP    // Transaction currency
		- Item transaction parameter: 
			VD: 
				uid=aeb1691c5a0ee5a6    // User ID  
				&vid=2                  // Visit ID (i.e. session number for this user_id)  
				&tid=508780             // Transaction ID  
				&aid=1                  // App ID
				&tv=js-0.5.2            // Tracker version

				&e=ti 				// Transaction item event type
				&ti_id=12345 		// Order ID
				&ti_sk=pbz0025 		// Item SKU
				&ti_na=black-tarot 	// Item name
				&ti_ca=tarot 		// Item category
				&ti_pr=7.99 		// Item price
				&ti_qu=1 			// Item quantity
				&ti_cu=USD      // Currency
- Social tracking: Chưa được implement 
- Item view: Chưa được implement 
- Error tracking: Chưa được implement  	
- Custom structured event: Track các event chưa được support bởi snowplow 
	- VD add-to-basket:
		uid=aeb1691c5a0ee5a6    // User ID  
		&vid=2                  // Visit ID (i.e. session number for this user_id)  
		&tid=508780             // Transaction ID  
		&aid=1                  // App ID
		&tv=js-0.5.2            // Tracker version

		&e=se                    // event = custom  
		&se_ca=ecomm            // event_category = ecomm  
		&se_ac=add-to-basket    // event_action = add-to-basket  
		&se_la=178              // event_label = 178 (product_id of item added to basket)  
		&se_pr=1                // event_property = 1 (quantity of item added to basket)  
		&se_va=14.99            // event_value = 14.99 (price of item added to basket)  
- Custom unstructured event: Track các event  mà các trường không được fix cứng 
	- VD add-to-basket:
		{
		  // Tells Snowplow this is an unstructured event
		  "schema": "iglu:com.snowplowanalytics.snowplow/unstruct_event/jsonschema/1-0-0",
		  "data": {

		    // Tells Snowplow this is a viewed_product event
		    "schema": "iglu:com.my_company/viewed_product/jsonschema/1-0-0",
		    "data": {

		      // The event data itself
		      "product_id": "ASO01043",
		      "price": 49.95
		    }
		  }
		}
	- Có thể pass &e=ue và &ue_pr="data"// event = unstructured để cho phép lấy được nhiều data hơn 

	- VD: 

		uid=aeb1691c5a0ee5a6   // User ID  
		&vid=2                 // Visit ID (i.e. session number for this user_id)  
		&tid=508780            // Transaction ID  
		&aid=1                 // App ID
		&tv=js-0.13.1          // Tracker version

		&e=ue                  // event = unstructured 				&ue_pr="%7B%22schema%22%3A%22iglu%3Acom.snowplowanalytics.snowplow%2Funstruct_event%2Fjsonschema%2F1-0-0%22%2C%22data%22%3A%7B%22schema%22%3A%22iglu%3Acom.my_company%2Fviewed_product%2Fjsonschema%2F1-0-0%22%2C%22data%22%3A%7B%22product_id%22%3A%22ASO01043%22%2C%22price%22%3A49.95%7D%7D%7D"


-Custom context: để gắn thông tin thêm vào bất kỳ JSON event và pass cho tracker
	- VD:
		{
		  // Tells Snowplow this is an array of custom contexts
		  schema: 'iglu:com.snowplowanalytics.snowplow/contexts/jsonschema/1-0-0'
		  data: [
		    {

		      // Tells Snowplow that this is a "user" context
		      schema: 'iglu:com.my_company/user/jsonschema/1-0-0',
		      data: {

			// The context data itself
			fb_uid: '9999xyz'
		      }
		    }
		  ]
		}
	-Parameter: 
		co 	context 	JSON 
		cx 	context 	JSON (URL-safe Base64 encoded) 	

	- Tracker có thể configure để encode context sang Base64 để đảm bảo k có data-lost, nhưng data sẽ lớn hơn và không dễ đọc
	
	- VD k dùng Base-64:
		uid=aeb1691c5a0ee5a6    // User ID  
		&vid=2                  // Visit ID (i.e. session number for this user_id)  
		&tid=508780             // Transaction ID  
		&aid=1                  // App ID
		&tv=js-0.5.2            // Tracker version
		&e=se                   // event = custom  
		&se_ca=ecomm            // event_category = ecomm  
		&se_ac=add-to-basket    // event_action = add-to-basket  
		&se_la=178              // event_label = 178 (product_id of item added to basket)  
		&se_pr=1                // event_property = 1 (quantity of item added to basket)  
		&se_va=14.99            // event_value = 14.99 (price of item added to basket)  
		&co=%7B%22schema%22:%22iglu:com.snowplowanalytics.snowplow/contexts/jsonschema/1-0-0%22,%22data%22:%5B%7B%22schema%22:%22iglu:com.my_company/user/jsonschema/1-0-0%22,%22data%22:%7B%22fb_uid%22:%229999xyz%22%7D%7D%5D%7D

	- VD dùng Base-64:
		uid=aeb1691c5a0ee5a6    // User ID  
		&vid=2                  // Visit ID (i.e. session number for this user_id)  
		&tid=508780             // Transaction ID  
		&aid=1                  // App ID
		&tv=js-0.5.2            // Tracker version

		&e=se                   // event = custom  
		&se_ca=ecomm            // event_category = ecomm  
		&se_ac=add-to-basket    // event_action = add-to-basket  
		&se_la=178              // event_label = 178 (product_id of item added to basket)  
		&se_pr=1                // event_property = 1 (quantity of item added to basket)  
		&se_va=14.99            // event_value = 14.99 (price of item added to basket) 	&cx=ew0KICBzY2hlbWE6ICdpZ2x1OmNvbS5zbm93cGxvd2FuYWx5dGljcy5zbm93cGxvdy9jb250ZXh0cy9qc29uc2NoZW1hLzEtMC0wJyANCiAgZGF0YToge1sNCiAgICB7DQogICAgICBzY2hlbWE6ICdpZ2x1OmNvbS5teV9jb21wYW55L3VzZXIvanNvbnNjaGVtYS8xLTAtMCcgDQogICAgICBkYXRhOiB7DQogICAgICAgIGZiX3VpZDogJzk5OTl4eXonDQogICAgICB9DQogICAgfQ0KICBdfQ0KfQ==
			 
		

