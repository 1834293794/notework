# Cookie、Session、Token、localStorage、sessionStorage

## Cookie

### 前言

网络早期最大的问题之一是如何管理状态。简而言之，服务器无法知道两个请求是否来自同一个浏览器。当时最简单的方法是在请求时，在页面中插入一些参数，并在下一个请求中传回参数。这需要使用包含参数的隐藏的表单，或者作为URL参数的一部分传递。这两个解决方案都手动操作，容易出错。cookie出现来解决这个问题。

### **如何工作**

当网页要发http请求时，浏览器会先检查是否有相应的cookie，有则自动添加在request header中的cookie字段中。这些是浏览器自动帮我们做的，而且每一次http请求浏览器都会自动帮我们做。这个特点很重要，因为这关系到“什么样的数据适合存储在cookie中”。

存储在cookie中的数据，每次都会被浏览器自动放在http请求中，如果这些数据并不是每个请求都需要发给服务端的数据，浏览器这设置自动处理无疑增加了网络开销；但如果这些数据是每个请求都需要发给服务端的数据（比如身份认证信息），浏览器这设置自动处理就大大免去了重复添加操作。所以对于那种设置“每次请求都要携带的信息（最典型的就是身份认证信息）”就特别适合放在cookie中，其他类型的数据就不适合了。

### Cookie属性

#### name字段

一个cookie的名称

#### value字段

一个cookie的值

#### domain字段

可以访问此cookie的域名

domain属性可以使多个web服务器共享cookie。domain属性的默认值是创建cookie的网页所在服务器的主机名。不能将一个cookie的域设置成服务器所在的域之外的域。
 例如让位于a.sodao.com的服务器能够读取b.sodao.com设置的cookie值。如果b.sodao.com的页面创建的cookie把 它的path属性设置为"/"，把domain属性设置成".sodao.com"，那么所有位于b.sodao.com的网页和所有位于 a.sodao.com的网页，以及位于sodao.com域的其他服务器上的网页都可以访问这个cookie。

#### path字段

可以访问此cookie的页面路径

#### Size字段

此cookie大小

#### http字段

cookie的httponly属性，若此属性为True，则只有在http请求头中会有此cookie信息，而不能通过document.cookie来访问此cookie。

#### secure字段

设置是否只能通过https来传递此条cookie。

#### expires/Max-Age字段

**expires属性——时间要转成GMT形式 **
 指定了cookie的生存期，默认情况下cookie是暂时存在的，他们存储的值只在浏览器会话期间存在，当用户退出浏览器后这些值也会丢失，如果想让 cookie存在一段时间，就要为expires属性设置为未来的一个用毫秒数表示的过期日期或时间点，expires默认为设置的expires的当前时间。现在已经被**max-age**属性所取代，

**max-age用秒来设置cookie的生存期。**

如果max-age属性为正数，则表示该cookie会在max-age秒之后自动失效。浏览器会将max-age为正数的cookie持久化，即 写到对应的cookie文件中。无论客户关闭了浏览器还是电脑，只要还在max-age秒之前，登录网站时该cookie仍然有效。

如果max-age为负数，则表示该cookie仅在本浏览器窗口以及本窗口打开的子窗口内有效，关闭窗口后该cookie即失效。max-age 为负数的Cookie，为临时性cookie，不会被持久化，不会被写到cookie文件中。cookie信息保存在浏览器内存中，因此关闭浏览器该 cookie就消失了。cookie默认的max-age值为-1。

‍如果max-age为0，则表示删除该cookie。**cookie机制没有提供删除cookie的方法，因此通过设置该cookie即时失效实现删除cookie的效果。失效的Cookie会被浏览器从cookie文件或者内存中删除。**

**如果不设置expires或者max-age这个cookie默认是Session的，也就是关闭浏览器该cookie就消失了。**

### **特征**

1. 不同的浏览器存放的cookie位置不一样，也是不能通用的。
2. cookie的存储是以域名形式进行区分的，不同的域下存储的cookie是独立的。
3. 我们可以设置cookie生效的域（当前设置cookie所在域的子域），也就是说，我们能够操作的cookie是当前域以及当前域下的所有子域
4. 一个域名下存放的cookie的个数是有限制的，不同的浏览器存放的个数不一样,一般为20个。
5. 每个cookie存放的内容大小也是有限制的，不同的浏览器存放大小不一样，一般为4KB。
6. cookie也可以设置过期的时间，默认是会话结束的时候，当时间到期自动销毁

## Session

### **什么是 Session**

Session 代表着服务器和客户端一次会话的过程。Session 对象存储特定用户会话所需的属性及配置信息。这样，当用户在应用程序的 Web 页之间跳转时，存储在 Session 对象中的变量将不会丢失，而是在整个用户会话中一直存在下去。当客户端关闭会话，或者 Session 超时失效时会话结束。

## Cookie 和 Session 有什么不同？

- 作用范围不同，Cookie 保存在客户端（浏览器），Session 保存在服务器端。
- 存取方式的不同，Cookie 只能保存 ASCII，Session 可以存任意数据类型，一般情况下我们可以在 Session 中保持一些常用变量信息，比如说 UserId 等。
- 有效期不同，Cookie 可设置为长时间保持，比如我们经常使用的默认登录功能，Session 一般失效时间较短，客户端关闭或者 Session 超时都会失效。
- 隐私策略不同，Cookie 存储在客户端，比较容易遭到不法获取，早期有人将用户的登录名和密码存储在 Cookie 中导致信息被窃取；Session 存储在服务端，安全性相对 Cookie 要好一些。
- 存储大小不同， 单个 Cookie 保存的数据不能超过 4K，Session 可存储数据远高于 Cookie。

## 为什么需要 Cookie 和 Session，他们有什么关联？

浏览器是没有状态的(HTTP 协议无状态)，这意味着浏览器并不知道是张三还是李四在和服务端打交道。这个时候就需要有一个机制来告诉服务端，本次操作用户是否登录，是哪个用户在执行的操作，那这套机制的实现就需要 Cookie 和 Session 的配合。

那么 Cookie 和 Session 是如何配合的呢？我画了一张图大家可以先了解下。



![img](https://user-gold-cdn.xitu.io/2019/5/13/16aafb5d90f398e2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



1. 用户第一次请求服务器的时候，服务器根据用户提交的相关信息，创建创建对应的 Session ，请求返回时将此 Session 的唯一标识信息 SessionID 返回给浏览器，浏览器接收到服务器返回的 SessionID 信息后，会将此信息存入到 Cookie 中，同时 Cookie 记录此 SessionID 属于哪个域名。

2. 当用户第二次访问服务器的时候，请求会自动判断此域名下是否存在 Cookie 信息，如果存在自动将 Cookie 信息也发送给服务端，服务端会从 Cookie 中获取 SessionID，再根据 SessionID 查找对应的 Session 信息，如果没有找到说明用户没有登录或者登录失效，如果找到 Session 证明用户已经登录可执行后面操作。

根据以上流程可知，SessionID 是连接 Cookie 和 Session 的一道桥梁，大部分系统也是根据此原理来验证用户登录状态。

## 如果浏览器中禁止了 Cookie，如何保障整个机制的正常运转。

第一种方案，每次请求中都携带一个 SessionID 的参数，也可以 Post 的方式提交，也可以在请求的地址后面拼接 `xxx?SessionID=123456...`。

第二种方案，Token 机制。Token 机制多用于 App 客户端和服务器交互的模式，也可以用于 Web 端做用户状态管理。

Token 的意思是“令牌”，是服务端生成的一串字符串，作为客户端进行请求的一个标识。Token 机制和 Cookie 和 Session 的使用机制比较类似。

当用户第一次登录后，服务器根据提交的用户信息生成一个 Token，响应时将 Token 返回给客户端，以后客户端只需带上这个 Token 前来请求数据即可，无需再次登录验证。

## Cookie+Session的问题

每个用户(客户端)只需要保存自己的会话标识(Session id)，而服务端则要保存所有用户的会话标识(Session id)。 如果访问服务端的用户逐渐变多， 就需要保存成千上万，甚至几千万个，这对服务器说是一个难以接受的开销 。 再比如，服务端是由2台服务器组成的一个集群， 小明通过服务器A登录了系统， 那session id会保存在服务器A上， 假设小明的下一次请求被转发到服务器B怎么办? 服务器B可没有小明 的 session id。

好吧，这个问题可以通过**分布式Session**来解决

## 如何考虑分布式 Session 问题？

在互联网公司为了可以支撑更大的流量，后端往往需要多台服务器共同来支撑前端用户请求，那如果用户在 A 服务器登录了，第二次请求跑到服务 B 就会出现登录失效问题。

分布式 Session 一般会有以下几种解决方案：

- Nginx ip_hash 策略，服务端使用 Nginx 代理，每个请求按访问 IP 的 hash 分配，这样来自同一 IP 固定访问一个后台服务器，避免了在服务器 A 创建 Session，第二次分发到服务器 B 的现象。
- Session 复制，任何一个服务器上的 Session 发生改变（增删改），该节点会把这个 Session 的所有内容序列化，然后广播给所有其它节点。
- 共享 Session，将Session Id 集中存储到一个地方，所有的机器都来访问这个地方的数据。这种方案的优点是架构清晰，缺点是工程量比较大。

建议采用第三种方案。但这也存在问题，持久层万一挂了，就会单点失败，就是说这个负责存储 session 的服务器挂了， 所有用户都得重新登录一遍，这是用户难以接受的。那么索性存储Session的服务器也搞成集群，增加其可靠性，避免单点故障，但不管如何，Session 引发出来的问题层出不穷。

于是有人就在思考， 为什么服务端必须要保存这session呢， 只让每个客户端去保存不行吗?可是服务端如果不保存这些session id ，又将如何验证客户端发送的 session id 的确是服务端生成的呢? 如果不验证，服务端无法判断是否是合法登录的用户，对，这里的问题是验证， session 只是解决这个验证问题的而产生的一个解决方案，是否还有其它方案呢?

## **基于Token 的身份验证**

token是无状态的，token字符串里就保存了所有的用户信息，服务器不记录哪些用户登录了或者哪些 JWT 被发布了

- 客户端登陆传递信息给服务端，服务端收到后把用户信息加密（token）传给客户端，客户端将token存放于`localStroage`等容器中。客户端每次访问都传递token，服务端解密token，就知道这个用户是谁了。通过`cpu`加解密，服务端就不需要存储session占用存储空间，就很好的解决负载均衡多服务器的问题了。这个方法叫做[JWT(JSON Web Token)

### JWT(JSON Web Token)

#### JWT 的格式

下面，我们会探讨一下 JWT 的组成和格式是什么

JWT 主要由三部分组成，每个部分用 `.` 进行分割，各个部分分别是

- `Header`
- `Payload`
- `Signature`

因此，一个非常简单的 JWT 组成会是下面这样



![img](https://user-gold-cdn.xitu.io/2020/4/5/17147e39ca50d82a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



然后我们分别对不同的部分进行探讨。

**Header**

Header 是 JWT 的标头，它通常由两部分组成：`令牌的类型(即 JWT)`和使用的 `签名算法`，例如 HMAC SHA256 或 RSA。

例如

```
{
  "alg": "HS256",
  "typ": "JWT"
}
复制代码
```

指定类型和签名算法后，Json 块被 `Base64Url` 编码形成 JWT 的第一部分。

**Payload**

Payload 部分也是一个 JSON 对象，用来存放实际需要传递的数据

```
{
  // 7个官方字段
  "iss": "a.com", // issuer：签发人
  "exp": "1d", // expiration time： 过期时间
  "sub": "test", // subject: 主题
  "aud": "xxx", // audience： 受众
  "nbf": "xxx", // Not Before：生效时间
  "iat": "xxx", // Issued At： 签发时间
  "jti": "1111", // JWT ID：编号
  // 可以定义私有字段
  "name": "John Doe",
  "admin": true
}
```

然后 payload Json 块会被`Base64Url` 编码形成 JWT 的第二部分。JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。

**signature**

JWT 的第三部分是一个签证信息，这个签证信息由三部分组成

- header (base64后的)
- payload (base64后的)
- secret

比如我们需要 HMAC SHA256 算法进行签名

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

签名用于验证消息在此过程中没有更改，并且对于使用私钥进行签名的令牌，它还可以验证 JWT 的发送者的真实身份

#### 拼凑在一起

现在我们把上面的三个由点分隔的 Base64-URL 字符串部分组成在一起，这个字符串可以在 HTML 和 HTTP 环境中轻松传递这些字符串。

下面是一个完整的 JWT 示例，它对 header 和 payload 进行编码，然后使用 signature 进行签名

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```



![img](https://user-gold-cdn.xitu.io/2020/4/5/17147e39cc29f678?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 如何保证安全？

- 发送JWT要使用HTTPS；不使用HTTPS发送的时候，JWT里不要写入秘密数据
- JWT的payload中要设置expire时间

这里有一个**非常重要的提醒**，token 是通过 HMACSHA256 算法生成，header 和 payload 是 Base64URL 编码，这**不是加密**。如果你去访问 [jwt.io](https://link.jianshu.com?t=https://jwt.io/) 网站，粘贴 token 以及选择 HMACSHA256 算法，可以解析出 token 读取它详细的内容。因此里面不能携带一些敏感数据，比如密码，绝对不能存储在 payload 中。

### 验证流程：

- 用户输入登录信息
- 服务器判断登录信息正确，返回一个 token
- token 存储在客户端，大多数通常在 local storage，但是也可以存储在 session storage 或者 cookie 中。
- 接着发起请求的时候将 token 放进 Authorization header，或者同样可以通过上面的方式。
- 服务器端解码 JWT 然后验证 token，如果 token 有效，则处理该请求。
- 一旦用户登出，token 在客户端被销毁，不需要经过服务器端。

###  Token总结

如此一来，服务端就不需要保存 Token 了， 当把这个Token发给服务端时，服务端使用相同的HMAC-SHA256 算法和相同的密钥，对数据再计算一次签名， 和 Token 中的签名做个对比， 如果相同，说明已经登录过了， 即验证成功。若不相同， 那么说明这个请求是伪造的。

这样一来， 服务端只需要生成 Token，而不需要保存Token， 只是验证Token就好了 ，也就实现了时间换取空间(CPU计算时间换取session 存储空间)。没了session id 的限制， 当用户访问量增大， 直接加机器就可以轻松地做水平扩展，也极大的提高了可扩展性。

附注：如果服务器保存 token 可以存储在 `redis` 里或者数据库里都无所谓，存` redis `里应该更常见，但是这样一来和 session 其实区别就不大了，如果采用 JWT 的方式，是不需要后端存储的。

## localStorage（本地存储）

HTML5新方法，不过**IE8及以上**浏览器都兼容。

一般用于性能优化，可以保存图片、js、css、html 模板、大量数据

### 特点

- 生命周期：持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。
- 存储的信息在同一域中是共享的。
- 当本页操作（新增、修改、删除）了localStorage的时候，本页面不会触发storage事件,但是别的页面会触发storage事件。
- 大小：据说是5M（跟浏览器厂商有关系）
- 在非IE下的浏览中可以本地打开。IE浏览器要在服务器中打开。
- localStorage本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡
- localStorage受同源策略的限制

### storage事件

当storage发生改变的时候触发。
**注意：** 当前页面对storage的操作会触发其他页面的storage事件

## sessionStorage

其实跟localStorage差不多，也是本地存储，会话本地存储

### 特点

​	用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。也就是说只要这个浏览器窗口没有关闭，即使刷新页面或进入同源另一页面，数据仍然存在。关闭窗口后，sessionStorage即被销毁，或者在新窗口打开同源的另一个页面，sessionStorage也是没有的。

### 举例

假如打开浏览器，在一个标签页下进入www.a.com/discuss，在该页面存储了一个sessionStorage，

那么包括以下情况，sessionStorage仍然存在

1. 刷新当前页面
2. 在当前页面点击某个超链接，当前标签页自动跳转到同源另一页面，如www.a.com/center
3. 在当前页面点击某个超链接，自动跳出一个新标签页并跳转到同源另一页面，如www.a.com/center，则新标签页同样具有同一sessionStorage

包括以下情况，sessionStorage不存在

1. 关闭浏览器重新打开
2. 不关闭浏览器的情况下，打开新的浏览器窗口，即使进入www.a.com/discuss，该页面也不存在sessionStorage
3. 在同一浏览器窗口，手动新建一个标签页，进入www.a.com/discuss，该页面也不存在sessionStorage
4. 在www.a.com/discuss页面下，通过按鼠标中键打开一个新的标签页

## cookie、localStorage、sessionStorage之间的区别

1. cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。而sessionStorage和localStorage**不会自动把数据发给服务器，仅在本地保存**。cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。
2. **存储大小限制**不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。
3. **数据有效期**不同，sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。
4. **作用域**不同，localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。sessionStorage比localStorage更严苛一点，除了协议、主机名、端口外，还要求在同一窗口（也就是浏览器的标签页）下