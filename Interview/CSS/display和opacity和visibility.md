# display、visibility、opacity隐藏元素的区别

## display:none

1. 会让元素完全从dom中消失，但html源码中该节点还是存在的，渲染的时候不占据任何空间, 其他元素可以占用它原有位置，**不能点击**
2. display不是继承属性，display属性设为none其后整棵子树都不可见。
3. **不响应事件**，但真正原因是鼠标无法真正接触到被设置成隐藏的元素！！通过代码是可以直接调用click事件的
4. display: none;不占据空间，动态改变此属性时会触发重排和重绘

## visibility: hidden

1. 不会让元素从dom中消失，渲染元素继续占据空间，只是内容不可见，其他元素不能占用它原有位置，**不能点击**
2. visibility: hidden会被子类继承，子类也可以通过显示的设置visibility: visible;来反隐藏。
3. visibility: hidden，不会触发该元素已经绑定的事件。
4. visibility: hidden只触发重绘，因为没有几何变化。

5. transition无效。(会延迟n秒后hidden，但不会渐变)

## opacity:0

1. 不会让元素从dom中消失，渲染元素继续占据空间，只是内容不可见，**可以点击**

2. opacity:0 **会被子元素继承**,且子元素并不能通过opacity=1，进行反隐藏
3. opacity:0的元素**依然能触发已经绑定的事件**
4. **transition有效**



v-show将元素隐藏相当于是在dom节点上加style='display:none'