# vue-router的两种模式

## 背景

大多数vue项目采用SPA(单页面应用)的模式开发，不同视图的切换，都要通过前端路由去管理和控制

前端路由的作用，就是改变视图的同时不会向后端发出请求。
vue-Router的原理就是利用了浏览器自身的两个特性(hash和history),来实现前端路由的功能。

## vue-router 的两种方式（浏览器环境下）

### 1. Hash （对应HashHistory）

hash（“#”）符号的本来作用是加在URL中指示网页中的位置：

```
http://www.example.com/index.html#print
```

\#符号本身以及它后面的字符称之为hash(也就是我之前为什么地址栏都会有一个‘#’)，可通过window.location.hash属性读取。它具有如下特点：

1. hash虽然出现在URL中，但不会被包括在HTTP请求中。它是用来指导浏览器动作的，对服务器端完全无用，因此，改变hash不会重新加载页面

2.可以为hash的改变添加监听事件：

```
window.addEventListener("hashchange", funcRef, false)
```

1. 每一次改变hash（window.location.hash），都会在浏览器的访问历史中增加一个记录

利用hash的以上特点，就可以来实现前端路由“更新视图但不重新请求页面”的功能了。

### 2. History  （对应HTML5History）

[History接口](https://developer.mozilla.org/en-US/docs/Web/API/History)是浏览器历史记录栈提供的接口，通过back(), forward(), go()等方法，我们可以读取浏览器历史记录栈的信息，进行各种跳转操作。

从HTML5开始，History interface提供了两个新的方法：pushState(), replaceState()使得我们可以对浏览器历史记录栈进行修改：

```
window.history.pushState(stateObject, title, URL)
window.history.replaceState(stateObject, title, URL)
```

stateObject: 当浏览器跳转到新的状态时，将触发popState事件，该事件将携带这个

	1. stateObject参数的副本 
 	2. title: 所添加记录的标题 
 	3. URL: 所添加记录的URL

这两个方法有个共同的特点：当调用他们修改浏览器历史记录栈后，虽然当前URL改变了，但浏览器不会刷新页面，这就为单页应用前端路由“更新视图但不重新请求页面”提供了基础。 浏览器历史记录可以看作一个「栈」。栈是一种后进先出的结构，可以把它想象成一摞盘子，用户每点开一个新网页，都会在上面加一个新盘子，叫「入栈」。用户每次点击「后退」按钮都会取走最上面的那个盘子，叫做「出栈」。而每次浏览器显示的自然是最顶端的盘子的内容。

## 两种模式比较

hash模式 会在浏览器的URL中加入'#'，而HTM5History就没有'#'号，URL和正常的URL一样。 另外： history.pushState()相比于直接修改hash主要有以下优势：

1. pushState设置的新URL可以是与当前URL同源的任意URL；而hash只可修改#后面的部分，故只可设置与当前同文档的URL
2. history和hash都是利用浏览器的两种特性实现前端路由，history是利用浏览历史记录栈的API实现，hash是监听location对象hash值变化事件来实现
3. pushState设置的新URL可以与当前URL一模一样，这样也会把记录添加到栈中；而hash设置的新值必须与原来不一样才会触发记录添加到栈中
4. pushState通过stateObject可以添加任意类型的数据到记录中；而hash只可添加短字符串
5. pushState可额外设置title属性供后续使用

### history的缺点

通过history api，我们丢掉了丑陋的#，但是它也有个毛病：

不怕前进，不怕后退，就怕**刷新**，**f5**，（如果后端没有准备的话）,因为刷新是实实在在地去请求服务器的,不玩虚的。

当你使用 history 模式时，URL 就像正常的 url，例如` http://www.yongcun.wang/tclass`，也好看！

不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问`http://www.yongcun.wang/tclass`就会返回 404，这就不好看了。

在hash模式下，前端路由修改的是#中的信息，而浏览器请求时是不带它玩的，所以没有问题.但是在history下，你可以自由的修改path，当刷新时，如果服务器中没有相应的响应或者资源，会分分钟刷出一个404来。

所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。

给个警告，因为这么做以后，你的服务器就不再返回 404 错误页面，因为对于所有路径都会返回 index.html 文件。为了避免这种情况，你应该在 Vue 应用里面覆盖所有的路由情况，然后在给出一个 404 页面。

