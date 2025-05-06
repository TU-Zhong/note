以当前打开的

`t.http`

文件为例，HTTP请求文件的常用语法规则如下：

## HTTP请求文件常用语法规则

1. **请求行**：包含HTTP方法、URL和协议版本
    
    Copy
    
    PUT http://localhost:8081/shop
    
2. **请求头**：每行一个，格式为"键: 值"
    
    Copy
    
    Content-Type: application/json
    
3. **空行**：请求头和请求体之间必须有一个空行
4. **请求体**：对于PUT、POST等方法，可以包含JSON、表单数据等
    
    Copy
    
    {
    
      "area": "大关",
    
      "openHours": "10:00-22:00",
    
      "sold": 4215,
    
      "address": "进化路锦昌文化苑29号",
    
      "comments": 3035,
    
      "avgPrice": 80,
    
      "score": 37,
    
      "name": "104茶餐厅",
    
      "typeId": 1,
    
      "id": 1
    
    }
    
5. **多个请求**：可以在同一文件中定义多个请求，用`###`分隔
6. **变量使用**：可以定义和使用变量
    
    Copy
    
    @hostname = localhost
    
    @port = 8081
    
    GET http://{{hostname}}:{{port}}/shop/1
    
7. **环境配置**：可以设置不同环境的变量
    
    Copy
    
    ### 开发环境
    
    @dev_host = localhost:8081
    
    ### 生产环境
    
    @prod_host = api.example.com
    
8. **认证信息**：可以添加认证头
    
    Copy
    
    GET http://localhost:8081/user
    
    Authorization: Bearer {{token}}
    
9. **文件上传**：可以指定上传文件
    
    Copy
    
    POST http://localhost:8081/upload
    
    Content-Type: multipart/form-data; boundary=boundary
    
    --boundary
    
    Content-Disposition: form-data; name="file"; 
    
    filename="example.jpg"
    
    Content-Type: image/jpeg
    
    < ./example.jpg
    
    --boundary--
    

您可以根据需要扩展当前的

`t.http`

文件，添加更多的HTTP请求，如GET、POST、DELETE等。