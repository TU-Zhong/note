

在 JWT 的解析过程中，`setSigningKey` 方法设置的密钥 **并非存储在 Token 本身**，而是由**解析方（服务端）主动提供**的。它的作用是验证 Token 的签名是否合法。以下是详细解释：

---

### **1. JWT 的结构回顾**
一个 JWT 的完整结构如下：
```
Header（头部） . Payload（载荷） . Signature（签名）
```
- **Header**：描述签名算法（如 `HS256`）。
- **Payload**：存储数据（如 `sub`、`exp`、自定义 claims）。
- **Signature**：由 `Header + Payload` 通过密钥（`secretKey`）和算法生成的签名。

---

### **2. `setSigningKey` 的作用**
- **验证签名**：  
  当解析 Token 时，服务端会使用 `setSigningKey` 提供的密钥重新计算签名，并与 Token 中的第三部分（Signature）比对，确保数据未被篡改。
- **密钥来源**：  
  该密钥是**服务端自己保存的**（或从安全配置中读取），与生成 JWT 时使用的密钥完全一致。**它不会存储在 Token 中**。

---

### **3. 密钥与 Token 的关系**
- **生成 Token 时**：用密钥对 Header 和 Payload 进行签名，生成最终的 Token。
  ```java
  // 生成 Token 时使用密钥签名
  String token = Jwts.builder()
          .setSubject("user123")
          .signWith(SignatureAlgorithm.HS256, secretKey.getBytes(StandardCharsets.UTF_8))
          .compact();
  ```

- **解析 Token 时**：用同样的密钥验证签名。
  ```java
  // 解析 Token 时需设置同样的密钥
  Claims claims = Jwts.parser()
          .setSigningKey(secretKey.getBytes(StandardCharsets.UTF_8)) // 密钥由服务端提供
          .parseClaimsJws(token)
          .getBody();
  ```

---

### **4. 为什么密钥不存储在 Token 中？**
- **安全性**：密钥是敏感信息，如果放在 Token 中，可能被他人截获并伪造 Token。
- **解耦**：密钥由服务端控制，可以随时更换（如定期轮换），而无需修改 Token 结构。

---

### **5. 密钥管理最佳实践**
- **对称加密（HMAC）**：  
  使用复杂的随机字符串（如 UUID），并通过安全配置（如 Vault、KMS）存储，而非硬编码在代码中。
- **非对称加密（RSA）**：  
  - 生成 Token 时用**私钥**签名。
  - 解析 Token 时用**公钥**验证（此时 `setSigningKey` 设置的是公钥）。

---

### **6. 常见错误**
- **密钥不一致**：生成和解析时使用的密钥不同，导致签名验证失败，抛出 `SignatureException`。
- **编码问题**：  
  ```java
  // 错误：未指定字符编码，可能导致不同系统环境下密钥不一致
  .setSigningKey(secretKey.getBytes())

  // 正确：明确指定 UTF-8 编码
  .setSigningKey(secretKey.getBytes(StandardCharsets.UTF_8))
  ```

---

### **总结**
- `setSigningKey` 设置的密钥是**服务端独立保管**的，与 Token 无关。
- 密钥是 JWT 安全性的核心，需通过安全机制管理（如加密存储、定期轮换）。
- Token 中只包含 Header、Payload 和 Signature，不包含密钥本身。

如果需要进一步了解密钥管理或 JWT 安全实践，可以随时提问！ 🔐