---
title: jwt
tags: springboot
category: springboot
---


[JWT使用详解](https://blog.csdn.net/qqzhengwei/article/details/111852328)

[jwt官方网站](https://github.com/auth0/java-jwt)

[什么是 JWT -- JSON WEB TOKEN](https://www.jianshu.com/p/576dbf44b2aehttps://www.jianshu.com/p/576dbf44b2ae)

[Java如何进行Base64的编码(Encode)与解码(Decode)](https://blog.csdn.net/zhou_kapenter/article/details/62890262?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162426408316780274167113%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=162426408316780274167113&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-62890262.first_rank_v2_pc_rank_v29&utm_term=java%E4%B8%ADbase64%E8%A7%A3%E7%A0%81&spm=1018.2226.3001.4187)

这是关于jwt的介绍

## jwt的简介

> ​ Json web token (JWT), 是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准（(RFC 7519).该token被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。JWT的声明一般被用来在身份提供者和服务提供者间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该token也可直接被用于认证，也可被加密。



## jwt的作用

1.授权 

> 这是使用JWT的最常见方案。一旦用户登录，每个后续请求将包括JWT，从而允许用户访问该令牌允许的路由，服务和资源。单点登录是当今广泛使用JWT的一项功能，因为它的开销很小并且可以在不同的域中轻松使用



## jwt的组成

第一部分我们称它为头部（header),第二部分我们称其为载荷（payload, 类似于飞机上承载的物品)，第三部分是签证（signature).

### header
jwt的头部承载两部分信息：

   

 - 声明类型，这里是jwt
 -    声明加密的算法 通常直接使用 HMAC SHA256



完整的头部就像下面这样的JSON：

``` 
{
  'typ': 'JWT',
  'alg': 'HS256'
}

```

然后将头部进行base64加密（该加密是可以对称解密的),构成了第一部分.

``` 
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9

```


### playload
载荷就是存放有效信息的地方。这个名字像是特指飞机上承载的货品，这些有效信息包含三个部分


 -    标准中注册的声明
 -    公共的声明
 -    私有的声明


**标准中注册的声明 (建议但不强制使用) ：**

 - iss: jwt签发者
 - sub: jwt所面向的用户
 - aud: 接收jwt的一方
 - exp: jwt的过期时间，这个过期时间必须要大于签发时间
 - nbf: 定义在什么时间之前，该jwt都是不可用的.
 - iat: jwt的签发时间
 - jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。



**公共的声明 ：**

公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息.但不建议添加敏感信息，因为该部分在客户端可解密.



**私有的声明 ：**

私有声明是提供者和消费者所共同定义的声明，一般不建议存放敏感信息，因为base64是对称解密的，意味着该部分信息可以归类为明文信息。


定义一个playload

``` 
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}


```

然后将其进行base64加密，得到Jwt的第二部分。

``` 
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9

```


**signature**

jwt的第三部分是一个签证信息，这个签证信息由三部分组成：

 -    header (base64后的)
 -    payload (base64后的)
 -    secret


这个部分需要base64加密后的header和base64加密后的payload使用.连接组成的字符串，然后通过header中声明的加密方式进行加盐secret组合加密，然后就构成了jwt的第三部分。


``` 
// javascript
var encodedString = base64UrlEncode(header) + '.' + base64UrlEncode(payload);

var signature = HMACSHA256(encodedString, 'secret'); // TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ

```

将这三部分用.连接成一个完整的字符串,构成了最终的jwt:

``` 
  eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ

```


> 注意：secret是保存在服务器端的，jwt的签发生成也是在服务器端的，secret就是用来进行jwt的签发和jwt的验证，所以，它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个secret, 那就意味着客户端是可以自我签发jwt了。


## 使用

一般是在请求头里加入Authorization，并加上Bearer标注：

``` 
fetch('api/user/1', {
  headers: {
    'Authorization': 'Bearer ' + token
  }
})

```
![jwt工作流程](./images/1821058-2e28fe6c997a60c9.png)


### maven依赖

``` 
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.16.0</version>
</dependency>

```

### 选择静态secrets或者秘钥

``` 
//HMAC
Algorithm algorithmHS = Algorithm.HMAC256("secret");

//RSA
RSAPublicKey publicKey = //Get the key instance
RSAPrivateKey privateKey = //Get the key instance
Algorithm algorithmRS = Algorithm.RSA256(publicKey, privateKey);

```

### 创建并签署令牌(这里我们使用的都是HS256加密)

``` 
String Token = JWT.create()
                .sign(Algorithm.HMAC256(secret));

```
### 解码

``` 
DecodedJWT jwt = JWT.require(Algorithm.HMAC256(secret))
                .build().verify(Token);
```

### 解码并获得头信息

> 解码出来的都是通过base64编码的密文，可以通过Base64.getDecoder().decode（）解码
 
>  String s = "{\"typ\":\"JWT\",\"alg\":\"HS256\"}";
        byte[] encode = Base64.getEncoder().encode(s.getBytes());//只接受byte数组
        byte[] decode = Base64.getDecoder().decode(encode);
        String s1 = new String(decode);
        System.out.println(s1);//s1是s原样输出

``` 
DecodedJWT verify = JWT.require(Algorithm.HMAC256(secret))
                .build().verify(Token);
        String header = verify.getHeader();
		System.out.println(new String(Base64.getDecoder().decode(header)));
```

### 添加头信息

**不添加头信息**

> 默认如上代码，不添加头信息时，默认是
> {"typ":"JWT","alg":"HS256"}


**添加头信息**

>  可以再创建令牌时添加
>    HashMap<String,Object> headerClaims = new HashMap<>();
        headerClaims.put("owner","auth0");
        String Token = JWT.create()
                .withHeader(headerClaims)
                .sign(Algorithm.HMAC256(secret));


**获取整个头信息**

>   DecodedJWT jwt = JWT.require(Algorithm.HMAC256(secret))
                .build().verify(Token);
        String header = jwt.getHeader();//是密文，通过base64解码可以看清
		System.out.println(new String(Base64.getDecoder().decode(header)));
		
> 结果如下
>  {"owner":"auth0","alg":"HS256","typ":"JWT"}

		
		
**获取自己添加的头信息**

>  Claim owner = jwt.getHeaderClaim("owner");
        String s = owner.asString();
		



## 创建可以添加的参数

![表格](./attachments/1624266109690.table.html)


最常用的是直接添加到负载自己想要的信息

> public JWTCreator.Builder withClaim(String name, Integer value) throws IllegalArgumentException {
            this.assertNonNull(name);
            this.addClaim(name, value);
            return this;
        }

## 在验证中获取信息

**获取整个头信息**

>   DecodedJWT jwt = JWT.require(Algorithm.HMAC256(secret))
                .build().verify(Token);
        String header = jwt.getHeader();//是密文，通过base64解码可以看清
		System.out.println(new String(Base64.getDecoder().decode(header)));
		
> 结果如下
>  {"owner":"auth0","alg":"HS256","typ":"JWT"}

**获取单个信息**

>Claim owner = jwt.getHeaderClaim("owner");
>获取的都是Claim类

**获取整个负载**

``` 
 DecodedJWT jwt = JWT.require(Algorithm.HMAC256(secret))
                .build().verify(Token);
				String payload = jwt.getPayload();
				 System.out.println(new String(Base64.getDecoder().decode(payload)));
```

> 获取的是整个字符串


**获取负载中单个信息**

``` 
Claim username = jwt.getClaim("username");
String s = username.asString();
```

### 对于Claim类的处理

>  Boolean asBoolean();
> Integer asInt();
>  Long asLong();
> Double asDouble();
> String asString();
> Date asDate();



## 总结

> 获取整个头信息时，获取的是字符串，是密文，无法直接看懂，必须经过base64转码，而单个获取信息时，返回的是claim类,jwt提供了一些处理函数，可以转换为字符串或数组