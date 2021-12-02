## API的鉴权方式

- cookie + session

用户第一次请求服务器的时候，服务器根据用户提交的相关信息，创建创建对应的 Session ，请求返回时将此 Session 的唯一标识信息 SessionID 返回给浏览器，浏览器接收到服务器返回的 SessionID 信息后，会将此信息存入到 Cookie 中，同时 Cookie 记录此 SessionID 属于哪个域名。

当用户第二次访问服务器的时候，请求会自动判断此域名下是否存在 Cookie 信息，如果存在自动将 Cookie 信息也发送给服务端，服务端会从 Cookie 中获取 SessionID，再根据 SessionID 查找对应的 Session 信息，如果没有找到说明用户没有登录或者登录失效，如果找到 Session 证明用户已经登录可执行后面操作。

- HTTP Basic

将账号和密码拼接然后 base64 编码加到 header 头中。很显然，密码很容易被窥探，因为账号和密码几乎是『明文』传输的，而且每次请求都传，可以挟持编码后的用户名、密码信息，然后发给服务器进行认证；可以与SSL配合，隐藏用户名密码。

- HTTP Digest

不以明文发送密码，在上述第2步时服务器响应返回随机字符串nonce，而客户端发送响应摘要 =MD5(HA1:nonce:HA2)，其中HA1=MD5(username:realm:password),HA2=MD5(method:digestURI)
在HTTP 摘要认证中使用 MD5 加密是为了达成"不可逆的"，也就是说，当输出已知的时候，确定原始的输入应该是相当困难的。

如果密码本身太过简单，也许可以通过尝试所有可能的输入来找到对应的输出（穷举攻击），甚至可以通过字典或者适当的查找表加快查找速度。

- Token

token 通过一次登录验证，得到一个鉴权字符串，然后以后带着这个鉴权字符串进行后续操作，这样就可以解决每次请求都要带账号密码的问题，而且也不需要反复使用账号和密码。

## Token的种类
一般来说 token 主要三种：
- 自定义的 token：开发者根据业务逻辑自定义的 token
- JWT：JSON Web Token，定义在 RFC 7519 中的一种 token 规范
- Oauth2.0：定义在 RFC 6750 中的一种授权规范，但这其实并不是一种 token，只是其中也有用到 token

## Token相对于Cookies+Session的优势

- 鉴权大致流程

客户端使用用户名跟密码请求登录服务端收到请求，验证用户名与密码。

验证成功后，服务端会签发一个 Token，再把这个 Token 发送给客户端，客户端收到 Token 以后可以把它存储起来，比如放在localStorage中。

客户端每次向服务端请求资源的时候需要带着服务端签发的 Token，服务端收到请求去验证客户端请求里面带着的 Token，如果验证成功就放行。

- 优势

支持跨域访问: Cookie是不允许垮域访问的，token支持

无状态： token无状态，session有状态的

去耦: 不需要绑定到一个特定的身份验证方案。Token可以在任何地方生成，只要在 你的API被调用的时候， 你可以进行Token生成调用即可

更适用于移动应用: Cookie不支持手机端访问的

性能: 在网络传输的过程中，性能更好

基于标准化: 你的API可以采用标准化的 JSON Web Token (JWT). 这个标准已经存在 多个后端库（.NET, Ruby, Java,Python, PHP）和多家公司的支持（如： Firebase,Google, Microsoft）

- 缺点

占带宽：

正常情况下要比 session_id 更大，需要消耗更多流量，挤占更多带宽，假如你的网站每月有 10 万次的浏览器，就意味着要多开销几十兆的流量。听起来并不多，但日积月累也是不小一笔开销。实际上，许多人会在 JWT 中存储的信息会更多。

注销Token：

当后端在token 有效期内废弃一个 token 或者更改它的权限的话，不会立即生效，一般需要等到有效期过后才可以。另外，当用户 Logout 的话，token 也还有效。除非在后端增加额外的处理逻辑

性能问题：

JWT 的卖点之一就是加密签名，由于这个特性，接收方得以验证 JWT 是否有效且被信任。但是大多数 Web 身份认证应用中，JWT 都会被存储到 Cookie 中，这就是说你有了两个层面的签名。

听着似乎很牛逼，但是没有任何优势，为此，你需要花费两倍的 CPU 开销来验证签名。对于有着严格性能要求的 Web 应用，这并不理想，尤其对于单线程环境。

## 关于JWT
JWT 全称 JSON Web Tokens ，是一种规范化的 token。可以理解为对 token 这一技术提出一套规范，是在 `RFC 7519` 中提出的。

JWT 格式组成：头部+载荷+签名 ( header + payload + signature )，中间用`.`分隔

例如：xxxx.yyyy.zzzz

- 头部（header）

头部通常由两部分组成：令牌的类型（即JWT）和签名使用的算法，可以表示为一个JSON对象。例如：{"typ":"JWT","alg":"HS256"}。

然后对其进行BASE64编码得到：
```
JTdCJTIydHlwJTIyJTNBJTIySldUJTIyJTJDJTIyYWxnJTIyJTNBJTIySFMyNTYlMjIlN0Q=
```

BASE64：一种基于64个可打印字符来表示二进制数据的表示方法。由于2
的6次方等于64，所以每6个比特为一个单元，对应某个可打印字符。三个字节有24
个比特，对应于4个Base64单元，即3个字节需要用4个可打印字符来表示。JDK 中
提供了非常方便的 BASE64Encoder 和 BASE64Decoder，用它们可以非常方便的
完成基于 BASE64 的编码和解码

- 载荷（palyload)

载荷中放置了token的一些基本信息，以帮助接受它的服务器来理解这个token，以及包含一些自定义的信息，用户信息交换。

这些有效信息分三个部分：

1. 标准中注册的声明（建议但不强制使用）
```
iss: jwt签发者。

sub: jwt所面向的用户。

aud: 接收jwt的一方 。

exp: jwt的过期时间，这个过期时间必须要大于签发时间 。

nbf: 定义在什么时间之前，该jwt都是不可用的.。

iat: jwt的签发时间 。

jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。
```

2. 公共的声明

公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息，但不建议添加敏感信息，因为该部分在客户端可以进行解密

3. 私有声明

私有声明是提供者和消费者共同动议的声明，一般不建议存放敏感信息，因为BASE64是对称解密的，意味着该部分信息可以归类为明文信息

例如定义一个`palyload：{"sub":"1234567890","name":"John Doe","admin":true}`，将其进行BASE64编码得到：

```
JTdCJTIyc3ViJTIyJTNBJTIyMTIzNDU2Nzg5MCUyMiUyQyUyMm5hbWUlMjIlM0ElMjJKb2huJUEwRG9lJTIyJTJDJTIyYWRtaW4lMjIlM0F0cnVlJTdE
```

- 签名（signature）

jwt的第三部分是一个签证信息，这个签证信息由三部分组成：

```
header (base64后的)

payload (base64后的)

secret
```

这个部分需要base64加密后的header和base64加密后的payload使用，连接组成的字符串，然后通过header中声明的加密方式进行加盐secret组合加密，然后就构成了jwt的第三部分如下：(就是使用头部指明的签名算法对已经加密了以后的字符串在进行加密得到第三部分)

```
TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

最终得到的一个完整的jwt：

```
JTdCJTIydHlwJTIyJTNBJTIySldUJTIyJTJDJTIyYWxnJTIyJTNBJTIySFMyNTYlMjIlN0Q=.JTdCJTIyc3ViJTIyJTNBJTIyMTIzNDU2Nzg5MCUyMiUyQyUyMm5hbWUlMjIlM0ElMjJKb2huJUEwRG9lJTIyJTJDJTIyYWRtaW4lMjIlM0F0cnVlJTdE.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

Signature 部分是对前两部分的签名，防止数据篡改。首先需要指定一个密钥（secret）。这个密钥只有服务器才知道，不能泄露给用户。然后使用 Header 里面指定的签名算法，按照下面的公式产生签名： HMACSHA256(base64UrlEncode(header) + “.” + base64UrlEncode(payload), secret) 。

算出签名以后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用(.)分隔，就可以返回给用户。

secret是保存在服务器端的，jwt的签发生成也是在服务器端的，secret就是用来进行jwt的签发和jwt的验证，所以它就是你服务端的私钥，在任何场景都不应该流露出去。一旦客户端得知这个secret, 那就意味着客户端是可以自我签发jwt了。