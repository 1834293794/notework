# animation、transition、transform的区别

## **简述**

1. transform用于元素旋转、缩放、移动、倾斜等效果

2. transition用于较为单一的动画

3. animation一般用于较为复杂、有中间态的动画

## [transform](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform)

```
transform是通过修改CSS视觉格式化模型的坐标空间来实现的。
常用属性有 scale()、rotate()、translate()、matrix()、skew()根据给定的X轴Y轴元素翻转给定的角度。
属性可以单独使用也可叠加使用。

transform: scale(2, 0.5) // x轴缩放2倍，y轴缩放0.5倍
transform: rotate(0.5turn) // 顺时针旋转0.5圈
transform: rotate(140deg) // 顺时针旋转140度
transform: translate(120px, 160px) //  x轴平移120px,y轴平移160px
transform: skew(30deg,20deg) // x轴旋转30度,y轴旋转20度
transform: matrix(1, 2, 3, 4, 5, 6) // 6个值的矩阵（需要再详细）
transform: scale(0.5) translate(-100%, -100%); // x、y轴缩放0.5倍，再根据自身长宽向左向上translate移动100%（100%是由自身宽高决定的）
```

## [transitions](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions)

可以为一个元素在不同状态之间切换的时候定义不同的过渡效果 。比如在不同的伪元素比如:hover，:active或者通过JsvaScript实现的状态变化

transitions 可以决定哪些属性发生动画效果 (明确地列出这些属性)，何时开始 (设置 delay），持续多久 (设置 duration) 以及如何动画 (定义*timing function*，比如匀速地或先快后慢)。

CSS 过渡 由简写属性[ ](https://developer.mozilla.org/en-US/docs/CSS/transition)[`transition`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transition) 定义是最好的方式，可以避免属性值列表长度不一，节省调试时间。

**transition-property**

指定哪个或哪些 CSS 属性用于过渡。只有指定的属性才会在过渡中发生动画，其它属性仍如通常那样瞬间变化。

**transition-duration**

指定过渡的时长。或者为所有属性指定一个值，或者指定多个值，为每个属性指定不同的时长。

**transition-timing-function**

指定一个函数，定义属性值怎么变化

**transition-delay**

指定延迟，即属性开始变化时与过渡开始发生时之间的时长。

## [animation](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation)

### Keyframes介绍：

Keyframes被称为关键帧，其类似于Flash中的关键帧。在CSS3中其主要以“@keyframes”开头，后面紧跟着是动画名称加上一对花括号“{…}”，括号中就是一些不同时间段样式规则。

```css
@keyframes changecolor{
  0%{
   background: red;
  }
  100%{
    background: green;
  }
}
```

##### animation

```css
animation: [name/动画名称] [duration/动画时间] [timing-function/动画周期(ease)] delay[动画延时] [iteration-count/动画播放次数] [direction/指定是否应该轮流反向播放动画] [fill-mode/规定当动画不播放时（当动画完成时，或当动画有一个延迟未开始播放时），要应用到元素的样式] [play-state/指定动画是否正在运行或已暂停];
```

| 属性            | 属性单独使用                                                 | 属性作用                                                     | 属性可选值                                                   |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| name            | *[animation-name](https://link.jianshu.com?t=http://www.runoob.com/cssref/css3-pr-animation-name.html)* | 动画名称(@keyframes name)                                    | @keframes name                                               |
| duration        | *[animation-duration](https://link.jianshu.com?t=http://www.runoob.com/cssref/css3-pr-animation-duration.html)* | 动画运行时间(1s)                                             | 参数num(1s or 0.5s)                                          |
| timing-function | *[animation-timing-function](https://link.jianshu.com?t=http://www.runoob.com/cssref/css3-pr-animation-timing-function.html)* | 设置动画将如何完成一个周期                                   | - linear [动画从头到尾的速度是相同的]   - ease [默认。动画以低速开始，然后加快，在结束前变慢]   - ease-in [动画以低速开始]   - ease-out [动画以低速结束]   - ease-in-out [动画以低速开始和结束]   - cubic-bezier(n,n,n,n) [在 cubic-bezier 函数中自己的值。可能的值是从 0 到 1 的数值] |
| delay           | *[animation-delay](https://link.jianshu.com?t=http://www.runoob.com/cssref/css3-pr-animation-delay.html)* | 设置动画在启动前的延迟间隔                                   | time [可选。定义动画开始前等待的时间，以秒或毫秒计。默认值为0] |
| iteration-count | *[animation-iteration-count](https://link.jianshu.com?t=http://www.runoob.com/cssref/css3-pr-animation-iteration-count.html)* | 定义动画的播放次数                                           | - n [一个数字，定义应该播放多少次动画]   - infinite [指定动画应该播放无限次（永远] |
| direction       | *[animation-direction](https://link.jianshu.com?t=http://www.runoob.com/cssref/css3-pr-animation-direction.html)* | 指定是否应该轮流反向播放动画                                 | - normal [默认值。动画按正常播放]   - reverse [动画反向播放]   - alternate [动画在奇数次（1、3、5...）正向播放，在偶数次（2、4、6...）反向播放]   - alternate-reverse [动画在奇数次（1、3、5...）反向播放，在偶数次（2、4、6...）正向播放]   - initial [置该属性为它的默认值。请参阅[*initial*](https://link.jianshu.com?t=http://www.runoob.com/cssref/css-initial.html)]   - inherit [从父元素继承该属性。请参阅[*inherit*](https://link.jianshu.com?t=http://www.runoob.com/cssref/css-inherit.html)] |
| fill-mode       | *[animation-fill-mode](https://link.jianshu.com?t=http://www.runoob.com/cssref/css3-pr-animation-fill-mode.html)* | 规定当动画不播放时（当动画完成时，或当动画有一个延迟未开始播放时），要应用到元素的样式 | 例如                                                                                                                                                   none ：默认值。动画在动画执行之前和之后不会应用任何样式到目标元素。                        forwards：在动画结束后（由 animation-iteration-count 决定），动画将应用该属性值。 |
| play-state      | *[animation-play-state](https://link.jianshu.com?t=http://www.runoob.com/cssref/css3-pr-animation-play-state.html)* | 指定动画是否正在运行或已暂停                                 |                                                              |

- ### 示例：

  创建一个动画名叫“changecolor”，在“0%”时背景色为red,在20%时背景色为blue，在40%背景色为orange，在60%背景色为green，在80%时背景色yellow，在100%处时背景色为red。

```xml
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>css3动画</title>
<style>
@keyframes changecolor{
  0%{
    background: red;
  }
  20%{
    background:blue;
  }
  40%{
    background:orange;
  }
  60%{
    background:green;
  }
  80%{
    background:yellow;
  }
  100%{
    background: red;
  }
}
div {
  width: 300px;
  height: 200px;
  background: red;
  color:#fff;
  margin: 20px auto;
}
div:hover {
  animation: changecolor 5s ease-out .2s;
}
</style>
</head>
<body>
<div>hover颜色改变</div>
 
</body>
</html>
```

## transition 和 animation 的区别

1. 触发条件不同。transition是过度属性，强调过度，他的实现需要触发一个事件（比如鼠标移动上去，焦点，点击等）才执行动画。animation是动画属性，他的实现不需要触发事件，设定好时间之后可以自己执行，且可以循环一个动画。

2. 循环。 animation可以设定循环次数。

3. 精确性。 animation可以设定每一帧的样式和时间。tranistion 只能设定头尾。 animation中可以设置每一帧需要单独变化的样式属性， transition中所有样式属性都要一起变化。

4. 与javascript的交互。animation与js的交互不是很紧密。tranistion和js的结合更强大。js设定要变化的样式，transition负责动画效果。