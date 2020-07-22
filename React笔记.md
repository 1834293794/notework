#  React

## React是什么

React是用于构建用户界面的JavaScript库。由FaceBook开发的，能简单，快速，高效地开发复杂和交互式的Web和移动UI。

### React的基本使用

- 安装：create-react-app my-app（使用react脚手架帮我们创建项目）
- react：react 是React库的入口点
- react-dom：提供了针对DOM的方法，比如：把创建的虚拟DOM，渲染到页面上

```
// 1. 导入 react
import React from 'react'
import ReactDOM from 'react-dom'

// 2. 创建 虚拟DOM
const divVD = React.createElement('div', {
  title: 'hello react'
}, 'Hello React！！！')

// 3. 渲染
ReactDOM.render(divVD, document.getElementById('app'))
复制代码
```

createElement()的问题，代码编写繁琐，推荐使用JSX

## JSX

```
import React from 'react'
import ReactDOM from 'react-dom'

const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

- 概述：JavaScript的扩展语法。JSX用来声明React当中的元素。

  基本解析规则：遇到HTML标签（以<开头），就用HTML规则解析，遇到代码块（以{开头的），就用javascript规则解析。

- 注意：JSX语法，最终会被编译为 createElement() 方法

- 推荐：使用 JSX 的方式创建组件

JSX语法有如下一些规则：

1. 自定义组件使用是必须首字母大写
2. 属性名称用camelCase来定义
3. 对于字符串值，用引号
4. 对于表达式，用大括号，可以在大括号内放置任何有效的 JavaScript 表达式。
5. 一个标签里面没有内容，可以使用 /> 来闭合标签

### React组件

- 组件是由一个个的HTML元素组成的
- 组件就像JS中的函数。它们接受用户输入（props），并且返回一个React对象，用来描述展示在页面中的内容

### React创建组件的两种方式

1. 通过 JS函数 创建（无状态组件）
2. 通过 class 创建（有状态组件）

**函数组件**

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

**class组件**

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

### 给组件传递数据 - 父子组件传递数据

- 组件中有一个 只读的对象 叫做 props
- 获取方式：函数参数 props
- 作用：将传递给组件的属性转化为 props 对象中的属性

### props和state

##### props

- 作用：给组件传递数据，一般用在父子组件之间
- 说明：React把传递给组件的属性转化为一个对象并交给 props
- 特点：props是只读的，无法给props添加或修改属性

##### state

状态即数据

- 作用：用来给组件提供组件内部使用的数据
- 注意：只有通过class创建的组件才具有状态
- 注意：状态是私有的，完全由组件来控制

## React组件事件处理

React元素同html标签元素一样，可以响应事件，其语法要求是

1. React事件的命名采用小驼峰
2. 传入一个函数作为事件处理函数
3. 需要显示调用preventDefault方法来阻止默认行为
4. 在 JavaScript 中，类的方法默认不会绑定 this, 所以如果直接在React元素的响应事件方法中访问this，得到的值是undefined，因此，需要通过bind绑定。 在构造函数中绑定

### 列表&keys

- 渲染多个组件：
   可以通过使用{}在JSX内构件一个元素集合

```javascript
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
// 把整个listItems插入到ul元素中，然后渲染DOM：
ReactDOM.render(
    <ul>{listItems}</ul>
     document.getElementById('root')
)
```

