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

## 3. 双飞翼布局

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <style>
        .content {
  	    float: left;
  	    width: 100%;
        }
        .main {
  	    height: 200px;
  	    margin-left: 110px;
  	    margin-right: 220px;
  	    background-color: green;
        }
	.left {
	    float: left;
	    height: 200px;
	    width: 100px;
	    margin-left: -100%;
	    background-color: red;
	}
	.right {
	    width: 200px;
	    height: 200px;
	    float: right;
	    margin-left: -200px;
	    background-color: blue;
	}	
    </style>
</head>
<body>
    <div class="content">
        <div class="main"></div>
    </div>
    <div class="left"></div>
    <div class="right"></div>
</body>
</html>
```

利用的是浮动元素 margin 负值的应用，主体内容可以优先加载

![img](https://pic3.zhimg.com/80/v2-4535c7dd53d222b7948bfd439c790bfe_720w.png)

**个人对双飞翼布局的理解**：假定html节点宽度为1300px

1. content块因为width:100%，所以宽度为父元素html节点宽度，即独占一行，而且设置了`float:left`，相当于开启了BFC，父子元素的外边距不会重叠，因此在子元素main设置了margin-left和margin-right后效果应如下图

![img](../image/two0.png)

2. 设置了了left块，个人理解，在兄弟元素都是float的情况下，一般情况应该都会放在一行，但是content块width为100%，于是它占据了0-1300的位置，float应该占据1300-1400的位置，但因为页面宽度就是1300，它只能到第二行去，设置`margin-left: -100%;`后left相当于向左前进了1300的位置，它跑到了0-100的位置，覆盖在了content这块区域的上面

![image](../image/two1.png)

3. right块原理类似left，left跑到上一行去后，它就是第二行的第一个元素，占据0-200的位置，但因为`float:right`，所以在最右边，实际上margin-left为0，在设置`margin-left:-200px`后也跑到了第一行，覆盖了content的1100-1300的这块区域

![img](https://pic3.zhimg.com/80/v2-4535c7dd53d222b7948bfd439c790bfe_720w.png)

4. 附注：但在下面这种情况时，即使给left设置`margin-left:-200%`，它也不会跑到第一行的最左边，因为浮动元素不会超过它上边的浮动的兄弟元素，最多和它一样高

![image](../image/two2.png)

## 4. 圣杯布局

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <style>
	.container {
	    margin-left: 120px;
	    margin-right: 220px;
	}
	.main {
	    float: left;
	    width: 100%;
	    height: 300px;
	    background-color: red;
	}
	.left {
	    float: left;
	    width: 100px;
	    height: 300px;
	    margin-left: -100%;
	    position: relative;
	    left: -120px;
	    background-color: blue;
	}
	.right {
	    float: left;
	    width: 200px;
	    height: 300px;
	    margin-left: -200px;
	    position: relative;
	    right: -220px;
	    background-color: green;
	}
    </style>
</head>
<body>
    <div class="container">
	<div class="main"></div>
	<div class="left"></div>
	<div class="right"></div>
    </div>
</body>
</html>
```

跟双飞翼布局很像，有一些细节上的区别，相对于双飞翼布局来说，HTML 结构相对简单，但是样式定义就稍微复杂，也是优先加载内容主体。由于container设置了`margin-left`和`margin-right`，因此其width比html根节点小，其子元素的`width:100%`也是相对于container

## 5. Flex 布局

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <style>
	.container {
            display: flex;
	}
	.main {
            flex-grow: 1;
	    height: 300px;
	    background-color: red;
	}
	.left {
	    order: -1;
	    flex: 0 1 200px;
	    margin-right: 20px;
	    height: 300px;
	    background-color: blue;
	}
	.right {
	    flex: 0 1 100px;
            margin-left: 20px;
	    height: 300px;
	    background-color: green;
	}
    </style>
</head>
<body>
    <div class="container">
	<div class="main"></div>
	<div class="left"></div>
	<div class="right"></div>
    </div>
</body>
</html>
```

简单实用，未来的趋势，需要考虑浏览器的兼容性。

## 6. Table 布局

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <style>
        .container {
	    display: table;
	    width: 100%;
        }
        .left, .main, .right {
	    display: table-cell;
        }
        .left {
	    width: 200px;
	    height: 300px;
	    background-color: red;
        }
        .main {
	    background-color: blue;
        }
        .right {
	    width: 100px;
	    height: 300px;
	    background-color: green;
        }
    </style>
</head>
<body>
    <div class="container">
	<div class="left"></div>
	<div class="main"></div>
	<div class="right"></div>
    </div>
</body>
</html>
```

缺点：无法设置栏间距

## 7. 绝对定位布局

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <style>
	.container {
	    position: relative;
	}
	.main {
	    height: 400px;
	    margin: 0 120px;
	    background-color: green;
	}
	.left {
	    position: absolute;
	    width: 100px;
	    height: 300px;
	    left: 0;
	    top: 0;
	    background-color: red;
	}
	.right {
	    position: absolute;
	    width: 100px;
	    height: 300px;
	    background-color: blue;
            right: 0;
	    top: 0;
	}
    </style>
</head>
<body>
    <div class="container">
        <div class="main"></div>
	<div class="left"></div>
	<div class="right"></div>
    </div>
</body>
</html>
```

简单实用，并且主要内容可以优先加载。