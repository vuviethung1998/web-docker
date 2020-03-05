Hướng dẫn tạo self-describing event và context

- Snowplow support một lượng lớn events(gọi là first-class citizen)
- Nếu muốn track một event mà snowplow không nhận ra là first-class citizen thì bạn có thể track bằng cách tạo self-describing event 
- Các bước để gửi các event và context vào snowplow, cần đi qua các bước sau:
    + Nhận diện những thứ bạn track và khi nào bạn track
    + Thêm code để track
    + Tạo một JSON schema cho mỗi loại event và context type
    + Tạo một JSON schema cho mỗi loại event 
    <!-- + Khai báo và upload lên JSON Paths file tương ứng 
    + Tạo một Redshift table trong Redshift cluster -->


- Bước 1: Nhận biết bạn track cái gì và khi nào?
    + Ví dụ: Nếu có những image trong trang web và sẽ resize khi actions "onmouseover" và "onmouseout" được fire
    ```html
    <p><img id="First"  onmouseover="smallImg(this)" onmouseout="normalImg(this)" border="1" height = "100px" width = "100px" src="pic1.jpg"></p>
    <p><img id="Second" onmouseover="smallImg(this)" onmouseout="normalImg(this)" border="1" height = "100px" width = "100px" src="pic2.jpg"></p>
        
    <script type="text/javascript">
        function smallImg(x) {
            x.style.height = "50px";
        }

        function normalImg(x) {
            x.style.height = "100px";
        }
    </script>
    ```
- Bước 2: Thêm code để tracking
    + Để track các event này, chúng ta sẽ xử dụng method "trackSelfDescribingEvent" theo như [doc](https://github.com/snowplow/snowplow/wiki/2-Specific-event-tracking-with-the-Javascript-tracker#38-tracking-custom-self-describing-unstructured-events)
        * Ví dụ với smallImg:
        ```html
        <script type="text/javascript">
            function smallImg(x) {
                x.style.height = "50px";
                // track our event
                var data = {
                    "imgId": x.id,
                    "imgSrc": x.src,
                    'imgEvent': "smallImg"
                }
		    window.snowplow('trackSelfDescribingEvent', {'schema': 'iglu:com.example_company/onmouse_img/jsonschema/1-0-0', 'data': data});

            // Code tương tự với normalImg 
            }
        </script>
        ```
- Bước 3: Tạo các JSON schema với mỗi event/context type 
    + Đây là ví dụ của schema bên trên:
    ```json
    {
	    "$schema": "http://iglucentral.com/schemas/com.snowplowanalytics.self-desc/schema/jsonschema/1-0-0#",
        "description": "onmouse_img example",
        "self": {
            "vendor": "com.localhost",
            "name": "onmouse_img",
            "format": "jsonschema",
            "version": "1-0-0"
        },
        "type": "object",
        "properties": {
            "imgId": {
                "type": "string"
            },
            "imgSrc": {
                "type": "string"
            },
            "imgEvent": {
                "enum": ["smallImg", "normalImg"]
            }
        },
        "required": ["imgId", "imgSrc", "imgEvent"],
        "additionalProperties": false
    }
    ``` 
    
    + Các parameter trong phần self sẽ tạo nên URI cho JSON schema trong Iglu schema registry với form: iglu:vendor/name/format/version
    ```
      iglu:com.example_company/event_name/jsonschema/1-0-0
    ---- ------------ ---------- ---------- -----
        |        |           |     |           |- schema version (model-revision-addition)
        |        |           |     |- schema format
        |        |           |- event name
        |        |- vendor of the event
        |- schema methodology
    ```