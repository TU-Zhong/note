

你提到的 **JWT 不可逆** 是针对 **加密型 JWT（如 JWE）** 而言的，而大多数情况下 JWT 使用的是 **签名（如 JWS）**，其 Payload（即 claims）是 **Base64Url 编码的明文**，并非加密。因此，即使没有密钥，也可以直接解码得到原始数据。以下是详细解释：

---

### **1. JWT 的两种类型**
- **JWS (JSON Web Signature)**：仅签名，Payload 是明文（Base64Url 编码），但通过签名确保数据未被篡改。
- **JWE (JSON Web Encryption)**：加密型，Payload 是密文，需要密钥才能解密。

大多数场景（如身份验证）使用的是 **JWS**，因为只需验证数据的完整性，无需加密。

---

### **2. JWS 的结构与解析**
以典型的 JWS 为例：
```
Header（算法描述） + "." + 
Payload（明文数据） + "." + 
Signature（签名）
```

#### **步骤 1：直接解码 Payload**
- **Payload 是 Base64Url 编码的 JSON 字符串**，无需密钥即可解码。
- 例如，使用在线工具 [jwt.io](https://jwt.io) 或代码直接解码：
  ```java
  String[] parts = token.split("\\.");
  String payloadJson = new String(Base64.getUrlDecoder().decode(parts[1]));
  System.out.println(payloadJson); // 输出明文 claims
  ```

#### **步骤 2：签名验证的作用**
- **验证数据来源和完整性**：只有持有正确密钥的服务端才能生成有效签名，确保 Payload 未被篡改。
- **不涉及加密**：即使攻击者修改了 Payload，由于无法生成正确的签名，服务端会拒绝该 Token。

---

### **3. 为什么说 JWT “不可逆”？**
- **针对加密场景（JWE）**：如果使用 JWE，Payload 是加密的，没有密钥确实无法解密。
- **语境误解**：你可能将“签名验证”和“加密解密”概念混淆了。签名是公开可验证的，而加密是保密性需求。

---

### **4. 代码示例分析**
你的代码中使用的是 **JWS（签名）**，因此可以轻松解码 Payload：
```java
Claims claims = Jwts.parser()
        .setSigningKey(secretKey) // 验证签名是否合法
        .parseClaimsJws(token)
        .getBody(); // 直接返回解码后的明文 claims
```
- **`getBody()` 只是解码 Base64Url**，并不涉及密钥。
- **`setSigningKey` 仅用于验证签名**，防止数据篡改。

---

### **5. 如何真正加密 JWT？**
如果需要保护 Payload 的隐私性（如存储敏感信息），应使用 **JWE**：
```java
// 生成加密 JWT（需使用非对称加密库）
String jweToken = Jwts.builder()
        .setSubject("user123")
        .encryptWith(key, KeyAlgorithm.A256KW, EncryptionAlgorithm.A256GCM) // 加密
        .compact();

// 解析时需解密
Jwe<Claims> jwe = Jwts.parser()
        .decryptWith(key)
        .build()
        .parseEncryptedClaims(jweToken);
Claims claims = jwe.getPayload();
```

---

### **总结**
- **JWS（签名）**：Payload 是明文，可逆，依赖签名防篡改。
- **JWE（加密）**：Payload 是密文，不可逆，需密钥解密。
- 常见 JWT 实现（如你的代码）使用的是 JWS，因此可直接解码 claims。

如果希望 Payload 不可读，需改用 JWE 加密。当前代码中的 `parseClaimsJws` 方法仅适用于 JWS 解析，不涉及加密。