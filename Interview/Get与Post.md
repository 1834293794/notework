# GET与POST的区别

- GET在浏览器回退时是无害的，而POST会再次提交请求。
- GET产生的URL地址可以被Bookmark，而POST不可以。
- GET请求会被浏览器主动cache，而POST不会，除非手动设置。
- GET请求只能进行url编码，而POST支持多种编码方式。
- GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
- GET请求在URL中传送的参数是有长度限制的，而POST没有。
- 对参数的数据类型，GET只接受ASCII字符，而POST没有限制。
- GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
- GET参数通过URL传递，POST放在Request body中。

#### GET 和 POST 报文上的区别

先下结论，**GET 和 POST 方法没有实质区别**，只是报文格式不同。

GET 和 POST 只是 HTTP 协议中两种请求方式，而 HTTP 协议是基于 TCP/IP 的应用层协议，无论 GET 还是 POST，用的都是同一个传输层协议，所以在传输上，没有区别。

报文格式上，不带参数时，最大区别就是第一行方法名不同

POST 方法请求报文第一行是这样的 `POST /uri HTTP/1.1 \r\n`

GET 方法请求报文第一行是这样的 `GET /uri HTTP/1.1 \r\n`

是的，不带参数时他们的区别就仅仅是报文的前几个字符不同而已

带参数时报文的区别呢？ 在约定中，GET 方法的参数应该放在 url 中，POST 方法参数应该放在 body 中

举个例子，如果参数是 name=qiming.c, age=22。

GET 方法简约版报文是这样的

```
GET /index.php?name=qiming.c&age=22 HTTP/1.1
Host: localhost
```

POST 方法简约版报文是这样的

```
POST /index.php HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

name=qiming.c&age=22
```

现在我们知道了两种方法本质上是 TCP 连接，没有差别，也就是说，如果我不按规范来也是可以的。我们可以在 URL 上写参数，然后方法使用 POST；也可以在 Body 写参数，然后方法使用 GET。当然，这需要服务端支持。

#### GET 方法参数写法是固定的吗？

在约定中，我们的参数是写在 `?` 后面，用 `&` 分割。

我们知道，解析报文的过程是通过获取 TCP 数据，用正则等工具从数据中获取 Header 和 Body，从而提取参数。

也就是说，我们可以自己约定参数的写法，只要服务端能够解释出来就行，一种比较流行的写法是 `http://www.example.com/user/name/chengqm/age/22`。

#### POST 方法比 GET 方法安全？

按照网上大部分文章的解释，POST 比 GET 安全，因为数据在地址栏上不可见。

然而，从传输的角度来说，他们都是不安全的，因为 HTTP 在网络上是明文传输的，只要在网络节点上捉包，就能完整地获取数据报文。

要想安全传输，就只有加密，也就是 HTTPS。

#### GET 方法的长度限制是怎么回事？

在网上看到很多关于两者区别的文章都有这一条，提到浏览器地址栏输入的参数是有限的。

首先说明一点，HTTP 协议没有 Body 和 URL 的长度限制，对 URL 限制的大多是浏览器和服务器的原因。

浏览器原因就不说了，服务器是因为处理长 URL 要消耗比较多的资源，为了性能和安全（防止恶意构造长 URL 来攻击）考虑，会给 URL 长度加限制。

#### POST 方法会产生两个 TCP 数据包？

有些文章中提到，post 会将 header 和 body 分开发送，先发送 header，服务端返回 100 状态码再发送 body。

HTTP 协议中没有明确说明 POST 会产生两个 TCP 数据包，而且实际测试(Chrome)发现，header 和 body 不会分开发送。

所以，header 和 body 分开发送是部分浏览器或框架的请求方法，不属于 post 必然行为。