保持代码风格的一致性是非常好的习惯！既然你的其他接口都用 `@RequestParam`，那我们把这个接口也统一改过来。

通常使用 `@RequestParam` 时，建议将请求方式改为 **GET**（因为这属于“获取”数据的操作，符合 RESTful 规范），当然如果你非要用 POST 也可以，但我下面按标准的 **GET** 请求来写。

### 1. 修改后的 Controller (`@RequestParam` 版)

这种写法比 `Map` 更清晰，Spring 会自动帮你检查必填参数。

Java

```java
    // ⚠️ 注意：通常 RequestParam 配合 GetMapping 使用
    @GetMapping("/table/data") 
    public Result getTableData(@RequestHeader("Authorization") String token, 
                               // 1. 必填参数：如果不传 tableName，Spring 直接报 400 错误，不用你手动检查 null
                               @RequestParam("tableName") String tableName,
                               // 2. 选填参数：schema 可能为空，所以设为 false
                               @RequestParam(value = "schema", required = false) String schema) {
        try {
            // 简单校验一下空字符串的情况
            if (tableName.trim().isEmpty()) {
                return Result.error("表名不能为空");
            }
            
            // 处理 schema 为空串的情况
            if (schema != null && schema.trim().isEmpty()) {
                schema = null;
            }

            // 调用 Service
            Map<String, Object> data = metadataService.getTableData(token, schema, tableName);
            
            return Result.success(data);

        } catch (IllegalArgumentException e) {
            // 捕获“表不存在”等预期内异常
            return Result.error(e.getMessage());
        } catch (Exception e) {
            e.printStackTrace();
            return Result.error("系统查询异常: " + e.getMessage());
        }
    }
```

---

### 2. 🚨 前端调用必须修改！

这一点至关重要。

- **`@RequestBody`** 对应前端的 **JSON Body**。
    
- **`@RequestParam`** 对应前端的 **URL 参数 (Query Params)**。
    

所以，你的 **前端 Vue (`TableDataViewer.vue`)** 代码必须从 `axios.post` 改为 `axios.get`，且传参方式要变。

**请修改 `TableDataViewer.vue` 里的 `fetchData` 函数：**

JavaScript

```
const fetchData = async () => {
  if (!props.params.tableName) return
  
  loading.value = true
  try {
    // ❌ 原来的写法 (对应 @RequestBody):
    // const res = await axios.post('http://.../table/data', { tableName: 'xxx' })

    // ✅ 新的写法 (对应 @RequestParam):
    // 这里的 params 属性会自动把参数拼接到 URL 后面
    // 变成: /api/db/table/data?tableName=t_student&schema=dbo
    const res = await axios.get('http://localhost:8080/api/db/table/data', {
      headers: {
        'Authorization': '你的token...' // 确保这里带上了 token
      },
      params: {
        tableName: props.params.tableName,
        schema: props.params.schema || '' // 如果没有 schema 就传空或不传
      }
    })
    
    // ... 后面的逻辑不变 ...
    if (res.data.code === 200) {
      tableColumns.value = res.data.data.columns
      tableData.value = res.data.data.rows
    } 
    // ...
```

### 总结变化

1. **后端**：`@RequestBody Map` -> `@RequestParam String`。
    
2. **后端注解**：`@PostMapping` -> `@GetMapping` (推荐)。
    
3. **前端**：`axios.post(url, data)` -> `axios.get(url, { params: data })`。
    

这样你的代码风格就完全统一了！