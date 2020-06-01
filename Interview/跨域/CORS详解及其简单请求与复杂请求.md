# CORS(跨源资源共享)

## 前言：CORS原理

1. 跨域资源共享标准新增了一组 HTTP 首部字段，服务器通过这些字段来控制浏览器有权访问哪些资源。

2. 为了安全起见请求方式分为两类，一类不会预先发送options请求，一些会预先发送options请求。

3. `GET` 以外的 HTTP 请求，或者搭配某些 MIME 类型的 `POST` 请求会触发options请求。

4. 服务器验证OPTIONS完成后才会允许发送事件的http请求。

不会触发http预检请求的便是简单请求，想法能够触发http预检请求的便是复杂请求。

## CORS 中新增的 HTTP 首部字段

1. Access-Control-Allow-Origin

响应首部中可以携带这个头部表示服务器允许哪些域可以访问该资源，其语法如下：

```jsx
Access-Control-Allow-Origin: <origin> | *
```

其中，origin 参数的值指定了允许访问该资源的外域 URI。对于不需要携带身份凭证的请求，服务器可以指定该字段的值为通配符，表示允许来自所有域的请求。

2. Access-Control-Allow-Methods

该首部字段用于**预检请求**的响应，指明实际请求所允许使用的HTTP方法。其语法如下：

```xml
Access-Control-Allow-Methods: <method>[, <method>]*
```

3. Access-Control-Allow-Headers

该首部字段用于**预检请求**的响应。指明了实际请求中允许携带的首部字段。其语法如下：

```xml
Access-Control-Allow-Headers: <field-name>[, <field-name>]*
```

4. Access-Control-Max-Age

该首部字段用于**预检请求**的响应，指定了预检请求能够被缓存多久，其语法如下：

```jsx
Access-Control-Max-Age: <delta-seconds>
```

5. Access-Control-Allow-Credentials

该字段可选。它的值是一个布尔值，表示是否允许发送Cookie。默认情况下，Cookie不包括在CORS请求之中。设为`true`，即表示服务器明确许可，Cookie可以包含在请求中，一起发给服务器。其语法如下：

```bash
Access-Control-Allow-Credentials: true
```

另外，如果要把 Cookie 发送到服务器，除了服务端要带上`Access-Control-Allow-Credentials`首部字段外，另一方面请求中也要带上`withCredentials`属性。

但是需要注意的是：如果需要在 Ajax 中设置和获取 Cookie，那么`Access-Control-Allow-Origin`首部字段不能设置为`*` ，必须设置为具体的 origin 源站。详细可阅读文章[CORS 跨域 Cookie 的设置与获取](https://www.jianshu.com/p/13d53acc124f)

6. Origin

该首部字段表明预检请求或实际请求的源站。不管是否为跨域请求，Origin字段总是被发送。其语法如下：

```jsx
Origin: <origin>
```

7. Access-Control-Request-Method

该首部字段用于**预检请求**。其作用是，将实际请求所使用的 HTTP 方法告诉服务器。其语法如下：

```jsx
Access-Control-Request-Method: <method>
```

8. Access-Control-Request-Headers

该首部字段用于**预检请求**。其作用是，将实际请求所携带的首部字段告诉服务器。其语法如下：

```xml
Access-Control-Request-Headers: <field-name>[, <field-name>]*
```

## 简单请求

1. 使用下列方法之一：

   GET

   POST

   HEAD

2. 不得人为设置该集合之外的其他首部字段。该集合为：

   Accept

   Accept-Language

   Content-Language

   Content-Type

3. Content-Type 的值仅限于下列三者之一：

   text/plain

   multipart/form-data

   application/x-www-form-urlencoded

4. 请求中的任意XMLHttpRequestUpload 对象均没有注册任何事件监听器；XMLHttpRequestUpload 对象可以使用 XMLHttpRequest.upload 属性访问

5. 请求中没有使用 ReadableStream 对象

简单请求的发送从代码上来看和普通的XHR没太大区别，但是HTTP头当中要求总是包含一个域（Origin）的信息。该域包含协议名、地址以及一个可选的端口。不过这一项实际上由浏览器代为发送，并不是开发者代码可以触及到的。

简单请求的部分响应头如下：

> Access-Control-Allow-Origin（必含）
>
>  Access-Control-Allow-Credentials（可选） 
>
>  Access-Control-Expose-Headers（可选） – 该项确定XmlHttpRequest2对象当中getResponseHeader()方法所能获得的额外信息。
>
> 通常情况下，getResponseHeader()方法只能获得如下的信息： 
>
> Cache-Control 
>
> Content-Language 
>
> Content-Type 
>
> Expires 
>
> Last-Modified 
>
> Pragma 
>
> 当你需要访问额外的信息时，就需要在这一项当中填写并以逗号进行分隔

## 复杂请求

简单来说，任何不满足上述简单请求要求的请求，都属于复杂请求。比如说你需要发送PUT、DELETE等HTTP动作，或者发送Content-Type: application/json的内容。

复杂请求表面上看起来和简单请求使用上差不多，但实际上浏览器发送了不止一个请求。其中最先发送的是一种"预请求"，此时作为服务端，也需要返回"预回应"作为响应。预请求实际上是对服务端的一种权限请求，只有当预请求成功返回，实际请求才开始执行。

预请求以OPTIONS形式发送，当中同样包含域，并且还包含了两项CORS特有的内容

> Access-Control-Request-Method – 该项内容是实际请求的种类，可以是GET、POST之类的简单请求，也可以是PUT、DELETE等等。
>
>  Access-Control-Request-Headers – 该项是一个以逗号分隔的列表，当中是复杂请求所使用的头部。

显而易见，这个预请求实际上就是在为之后的实际请求发送一个权限请求，在预回应返回的内容当中，服务端应当对这两项进行回复，以让浏览器确定请求是否能够成功完成。

复杂请求的部分响应头及解释如下：

> Access-Control-Allow-Origin（必含） – 和简单请求一样的，必须包含一个域。  
>
> Access-Control-Allow-Methods（必含） – 这是对预请求当中Access-Control-Request-Method的回复，这一回复将是一个以逗号分隔的列表。尽管客户端或许只请求某一方法，但服务端仍然可以返回所有允许的方法，以便客户端将其缓存。 
>
>  Access-Control-Allow-Headers（当预请求中包含Access-Control-Request-Headers时必须包含） – 这是对预请求当中Access-Control-Request-Headers的回复，和上面一样是以逗号分隔的列表，可以返回所有支持的头部。这里在实际使用中有遇到，所有支持的头部一时可能不能完全写出来，而又不想在这一层做过多的判断，没关系，事实上通过request的header可以直接取到Access-Control-Request-Headers，直接把对应的value设置到Access-Control-Allow-Headers即可。  
>
> Access-Control-Allow-Credentials（可选） – 和简单请求当中作用相同  
>
> Access-Control-Max-Age（可选） – 以秒为单位的缓存时间。预请求的的发送并非免费午餐，允许时应当尽可能缓存。