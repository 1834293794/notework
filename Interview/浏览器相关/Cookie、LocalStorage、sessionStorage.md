# Cookie、localStorage、sessionStorage

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

## localStorage（本地存储）

HTML5新方法，不过**IE8及以上**浏览器都兼容。

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

1. cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。
2. **存储大小限制**不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。
3. **数据有效期**不同，sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。
4. **作用域**不同，localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。sessionStorage比localStorage更严苛一点，除了协议、主机名、端口外，还要求在同一窗口（也就是浏览器的标签页）下