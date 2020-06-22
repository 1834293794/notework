# [详解 CSS 七种三栏布局技巧](https://zhuanlan.zhihu.com/p/25070186)

三栏布局，顾名思义就是两边固定，中间自适应。三栏布局在开发十分常见，那么什么是三栏布局？比如我打开某东的首页：

![img](https://pic1.zhimg.com/80/v2-a0098f2bc21152879d2cfe3888a040f8_720w.png)

映入眼帘的就是一个常见的三栏布局：即左边商品导航和右边导航固定宽度，中间的主要内容随浏览器宽度自适应。

下面围绕的这样的目的，即左右模块固定宽度，中间模块随**浏览器变化**自适应，想要完成的最终效果如下图所示：

![img](https://pic3.zhimg.com/80/v2-4535c7dd53d222b7948bfd439c790bfe_720w.png)

红色和蓝色宽度固定，绿色宽度自适应，下面七种方法实现的最终效果跟这个差不多，可能会稍有不同。

**下面七种技巧各有千秋，在开发中可以根据实际需求选择适合自己的方法进行编码。**

## 1. 流体布局

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <style>
	.left {
	    float: left;
	    height: 200px;
	    width: 100px;
	    background-color: red;
	}
	.right {
	    width: 200px;
	    height: 200px;
	    background-color: blue;
	    float: right;
	}
	.main {
	    margin-left: 120px;
	    margin-right: 220px;
	    height: 200px;
	    background-color: green;
	}
    </style>
</head>
<body>
    <div class="container">
        <div class="left"></div>
        <div class="right"></div>
        <div class="main"></div>
    </div>
</body>
</html>
```

左右模块各自向左右浮动，并设置中间模块的 margin 值使中间模块宽度自适应。

缺点就是主要内容无法最先加载，当页面内容较多时会影响用户体验。

**注意，因为left和right都已经脱离文档流了，所以main的margin-left和margin-right的根据都是网页根节点html，main的宽度也是感觉html的宽度变化而变化，也就是说如果只是增加了left的宽度，main的宽度并不会跟着变化，更可能只是一部分被left所覆盖**

## 2. BFC 三栏布局

BFC 规则有这样的描述：BFC 区域，不会与浮动元素重叠。因此我们可以利用这一点来实现 3 列布局。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <style>
	.left {
	    float: left;
	    height: 200px;
	    width: 100px;
	    margin-right: 20px;
	    background-color: red;
	}
	.right {
	    width: 200px;
	    height: 200px;
	    float: right;
	    margin-left: 20px;
	    background-color: blue;
	}	
	.main {
	    height: 200px;
	    overflow: hidden;
	    background-color: green;
	}
    </style>
</head>
<body>
    <div class="container">
        <div class="left"></div>
        <div class="right"></div>
        <div class="main"></div>
    </div>
</body>
</html>
```

**该布局main的宽度不仅会根据html的宽度变化而变化，也会根据left和right的宽度变化而变化**

缺点跟方法一类似，主要内容模块无法最先加载，当页面中内容较多时会影响用户体验。因此为了解决这个问题，有了下面要介绍的布局方案双飞翼布局。