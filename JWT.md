# JWT



![image-20211126171801494](F:\笔记图片\img\image-20211126171801494.png)

![image-20211126172017573](F:\笔记图片\img\image-20211126172017573.png)

![image-20211126172159867](F:\笔记图片\img\image-20211126172159867.png)



**JWT的使用**

1. 引入依赖

   ```xml
    <dependency>
        <groupId>com.auth0</groupId>
        <artifactId>java-jwt</artifactId>
        <version>3.10.3</version>
   </dependency>
   ```

2. ```java 
   
           HashMap<String, Object> map = new HashMap<>();
           Calendar instance = Calendar.getInstance();
           instance.add(Calendar.SECOND,20);
      
           String token = JWT.create()
                   .withHeader(map)  //头
                   .withClaim("userId", 1)   //payload
                   .withClaim("username", "zgx")
                   .withExpiresAt(instance.getTime()) //设置过期时间
                   .sign(Algorithm.HMAC256("@qdasqs"));//签名
      
           System.out.println(token);
   
   
   
      @Test
       public void test() {
           //创建验证对象
           JWTVerifier verifier = JWT.require(Algorithm.HMAC256("@qdasqs")).build();
   
           DecodedJWT verify = verifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MzkwNDE2MDgsInVzZXJJZCI6MSwidXNlcm5hbWUiOiJ6Z3gifQ.896VblFDXs-R2OmytjyBfsqdLsRLCXVeplJk0QcmJRI");
           System.out.println(verify.getClaim("userId").asInt());
           System.out.println(verify.getClaim("username").asString());
       }
   
   ```





## JWT工具的封装

```Java
public class JWTUtils {
    private static final String SINGLE = "!@qwed";

    /**
     * 生成token
     * @param map
     * @return
     */
    public String getToken(Map<String,String> map) {
        
        Calendar instance = Calendar.getInstance();
        instance.add(Calendar.DATE,7); //默认七天过期

        //创建jwt builder
        JWTCreator.Builder builder = JWT.create();
        
        //payload
        map.forEach((k,v) -> {
            builder.withClaim(k,v);
        });

        String token = builder.withExpiresAt(instance.getTime()).sign(Algorithm.HMAC256(SINGLE));

        return token;
    }

    /**
     * 验证token
     * @param token
     */
    public static void verifyToken(String token) {
        JWT.require(Algorithm.HMAC256(SINGLE)).build().verify(token);
    }
    
    public static DecodedJWT getMessage(String token) {
        DecodedJWT verify = JWT.require(Algorithm.HMAC256(SINGLE)).build().verify(token);
        
        return verify;
    }
}
```

## Springboot整合JWT

```Java
@GetMapping("/user/login")
public Map<String,String> login(Users user) {
    Map<String,String> map = new HashMap<>();
    try {
        Users user1 = service.login(user);
        HashMap<String,String> payload = new HashMap<>();
        payload.put("id",user1.getId().toString());
        payload.put("name",user1.getUsername());
        String token = JWTUtils.getToken(payload);
        map.put("token",token);
        map.put("state","true");
        map.put("msg","登陆成功");
    } catch (Exception e) {
        map.put("state","false");
        map.put("msg","登陆失败");
    }

    return map;
}
```

