# CSS 选择器解析顺序

开篇，我们还是不厌其烦的回顾一下浏览器的渲染过程，先上图：

![img](https://pic2.zhimg.com/80/v2-eb10a9f57cf0c1d329eaf23650e2b89d_720w.jpg)

正如上图所展示的，我们浏览器渲染过程分为了两条主线：
其一，HTML Parser 生成的 DOM 树；
其二，CSS Parser 生成的 Style Rules ；

在这之后，DOM 树与 Style Rules 会生成一个新的对象，也就是我们常说的 Render Tree 渲染树，结合 Layout 绘制在屏幕上，从而展现出来。

##  CSS 选择器时是从右往左解析，这是为什么呢？

1. HTML 经过解析生成 DOM Tree（这个我们比较熟悉）；而在 CSS 解析完毕后，需要将解析的结果与 DOM Tree 的内容一起进行分析建立一棵 Render Tree，最终用来进行绘图。**Render Tree 中的元素（WebKit 中称为「renderers」，Firefox 下为「frames」）与 DOM 元素相对应，但非一一对应**：一个 DOM 元素可能会对应多个 renderer，如文本折行后，不同的「行」会成为 render tree 中不同的 renderer。也有的 DOM 元素被 Render Tree 完全无视，比如 display:none 的元素。

2. 在建立 Render Tree 时（WebKit 中的「Attachment」过程），浏览器就要为每个 DOM Tree 中的元素根据 CSS 的解析结果（Style Rules）来确定生成怎样的 renderer。对于每个 DOM 元素，必须在所有 Style Rules 中找到符合的 selector 并将对应的规则进行合并。选择器的「解析」实际是在这里执行的，在遍历 DOM Tree 时，从 Style Rules 中去寻找对应的 selector。

3. 因为所有样式规则可能数量很大，而且绝大多数不会匹配到当前的 DOM 元素（因为数量很大所以一般会建立规则索引树），所以有一个快速的方法来判断「这个 selector 不匹配当前元素」就是极其重要的。

4. 如果正向解析，例如「div div p em」，我们首先就要检查当前元素到 html 的整条路径，找到最上层的 div，再往下找，如果遇到不匹配就必须回到最上层那个 div，往下再去匹配选择器中的第一个 div，回溯若干次才能确定匹配与否，效率很低。

**举例**

```html
<div>
   <div class="jartto">
      <p><span> 111 </span></p>
      <p><span> 222 </span></p>
      <p><span> 333 </span></p>
      <p><span class='yellow'> 444 </span></p>
   </div>
</div>
```

CSS 选择器：

```css
div > div.jartto p span.yellow{
   color:yellow;
}
```

对于上述例子，如果按**从左到右的方式**进行查找：

1. 先找到所有 div 节点；
2. 在 div 节点内找到所有的子 div ,并且是 class = “jartto”；
3. 然后再依次匹配 p span.yellow 等情况；
4. 遇到不匹配的情况，就必须回溯到一开始搜索的 div 或者 p 节点，然后去搜索下个节点，重复这样的过程。

这样的搜索过程对于一个只是匹配很少节点的选择器来说，效率是极低的，因为我们花费了大量的时间在回溯匹配不符合规则的节点。

如果换个思路，我们一开始过滤出跟目标节点最符合的集合出来，再在这个集合进行搜索，大大降低了搜索空间。来看看从右到左来解析选择器：
1.首先就查找到 的元素；
2.紧接着我们判断这些节点中的前兄弟节点是否符合 P 这个规则，这样就又减少了集合的元素，只有符合当前的子规则才会匹配再上一条子规则。

结果显而易见了，众所周知，在 DOM 树中一个元素可能有若干子元素，如果每一个都去判断一下显然性能太差。而一个子元素只有一个父元素，所以找起来非常方便。

试想一下，如果采用`从左至右`的方式读取 CSS 规则，那么大多数规则读到最后（最右）才会发现是不匹配的，这样会做费时耗能，最后有很多都是无用的；而如果采取`从右向左`的方式，那么只要发现最右边选择器不匹配，就可以直接舍弃了，避免了许多无效匹配。

浏览器 CSS 匹配核心算法的规则是以`从右向左`方式匹配节点的。这样做是为了减少无效匹配次数，从而匹配快、性能更优。

## 有何收获？

1.使用 id selector 非常的高效。在使用 id selector 的时候需要注意一点：因为 id 是唯一的，所以不需要既指定 id 又指定 tagName：

```text
Bad
p#id1 {color:red;}  
Good  
#id1 {color:red;}
```

当然，你非要这么写也没有什么问题，但这会增加 CSS 编译与解析时间，实在是不值当。

2.避免深层次的 node ，譬如：

```text
Bad  
div > div > div > p {color:red;} 
Good  
p-class{color:red;}
```

3.慎用 ChildSelector ；

4.不到万不得已，不要使用 attribute selector，如：p[att1=”val1”]。这样的匹配非常慢。更不要这样写：p[id=”id1”]。这样将 id selector 退化成 attribute selector。

```text
Bad  
p[id="id1"]{color:red;}  
p[class="class1"]{color:red;}  
Good 
#id1{color:red;}  
.class1{color:red;}
```