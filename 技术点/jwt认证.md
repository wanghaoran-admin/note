### JWT 的基本结构

一个 JWT 由三部分组成，分别是头部（Header）、有效载荷（Payload）和签名（Signature），它们之间用句点（.）分隔。





#### **Header**

头部通常包含两部分信息：令牌的类型（JWT）和所使用的签名算法（如 HMAC SHA256 或 RSA）。

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```



#### **Payload**

有效载荷【存放数据】

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```





#### Signature

- 用于验证消息未被篡改，同时可以验证发件人身份。通过将编码后的头部、有效载荷和密钥组合并进行签名来生成。

- 示例（使用 HMAC SHA256 算法）：

  ```json
  HMACSHA256(
    base64UrlEncode(header) + "." +
    base64UrlEncode(payload),
    secret)
  ```





### JWT使用

#### 工具类

```java
public class JwtUtil {

    private static final String SECRET_KEY = "your_secret_key"; // 替换为你的密钥
    private static final long EXPIRATION_TIME = 12 * 60 * 60 * 1000; // 12小时

    // 创建 JWT
    public static String createToken(String subject,Map<String,Object> claims) {
        return Jwts.builder()
                .setClaims(claims)
                .setSubject(subject)
                .setIssuedAt(new Date(System.currentTimeMillis()))
                .setExpiration(new Date(System.currentTimeMillis() + EXPIRATION_TIME))
                .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
                .compact();
    }

    // 解析 JWT
    public static Claims parseToken(String token) {
        return Jwts.parser()
                .setSigningKey(SECRET_KEY)
                .parseClaimsJws(token)
                .getBody();
    }

    // 获取 JWT 中的主体
    public static String getSubject(String token) {
        return parseToken(token).getSubject();
    }

    // 检查 JWT 是否过期
    public static boolean isExpired(String token) {
        return parseToken(token).getExpiration().before(new Date());
    }
}
```





#### 测试

```java
@org.junit.jupiter.api.Test
public void createJwt() throws Exception {
    Map<String,Object> claims = new HashMap<>();
    claims.put("username","admin");
    claims.put("roles","admin");
    String token = JwtUtil.createToken("admin",claims);
    System.out.println(token);
}


@org.junit.jupiter.api.Test
public void parseToken() throws Exception {
    String jwt = "eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJhZG1pbiIsInJvbGVzIjoiYWRtaW4iLCJleHAiOjE3Mjg4ODI0OTcsImlhdCI6MTcyODg3ODg5NywidXNlcm5hbWUiOiJhZG1pbiJ9.VjU6mftJy-N-lRLJsWFHSea0YGnDY4-vQun3NKPFNOQ";
    Claims claims = JwtUtil.parseToken(jwt);
    System.out.println(claims.get("roles"));
}


@org.junit.jupiter.api.Test
public void encodedPassword () throws Exception {
    String encodedPassword = passwordEncoder.encode("123");
    System.out.println(encodedPassword);
}
```