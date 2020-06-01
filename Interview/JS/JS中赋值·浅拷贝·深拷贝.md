# JS中赋值·浅拷贝·深拷贝

我们所说的浅拷贝和深拷贝都是对于JS中的引用类型而言的，对于基本数据类型的拷贝，并没有深浅拷贝的区别。

## 数据类型与堆栈的关系

### 基本类型与引用类型

- 基本类型：undefined,null,Boolean,String,Number,Symbol
- 引用类型：Object,Array,Date,Function,RegExp等

### 存储方式

- 基本类型：基本类型值在内存中占据固定大小，保存在`栈内存`中（不包含`闭包`中的变量）



![img](https://user-gold-cdn.xitu.io/2019/7/8/16bd2282836bad2c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



- 引用类型：引用类型的值是对象，保存在`堆内存`中。而栈内存存储的是对象的变量标识符以及对象在堆内存中的存储地址(引用)，引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。



![img](https://user-gold-cdn.xitu.io/2019/7/8/16bd228c2ad68a18?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



**注意：**

闭包`中的变量并不保存在栈内存中，而是保存在堆内存中。这一点比较好想，如果`闭包`中的变量保存在了`栈内存`中，随着外层中的函数从调用栈中销毁，变量肯定也会被销毁，但是如果保存在了堆内存中，内存函数仍能访问外层已销毁函数中的变量。看一段对应代码理解下：

```
function A() {
  let a = 'koala'
  function B() {
      console.log(a)
  }
  return B
}
```

## 赋值操作

### 基本数据类型赋值

看一段代码

```
let a ='koala';
let b = a;
b='程序员成长指北'；
console.log(a); // koala
赋值代码
```

基本数据类型赋值配图：



![img](https://user-gold-cdn.xitu.io/2019/7/8/16bd22955385ae3e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



**结论**：在栈内存中的数据发生数据变化的时候，系统会自动为新的变量分配一个新的之值在栈内存中，两个变量相互独立，互不影响的。

### 引用数据类型赋值

看一段代码

```
let a = {x:'kaola', y:'kaola1'}
let b = a;
b.x = '程序员成长指北';
console.log(a.x); // 程序员成长指北
复制代码
```

引用数据类型复制配图：



![img](https://user-gold-cdn.xitu.io/2019/7/8/16bd22a0d45de903?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**结论**：引用类型的复制，同样为新的变量b分配一个新的值，报错在栈内存中，不同的是这个变量对应的具体值不在栈中，栈中只是一个地址指针。两个变量地址指针相同，指向堆内存中的对象，因此b.x发生改变的时候，a.x也发生了改变。


## 浅拷贝

浅拷贝是按位拷贝对象，**它会创建一个新对象**，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值；如果属性是内存地址（引用类型），拷贝的就是内存地址 ，因此如果其中一个对象改变了这个地址，就会影响到另一个对象。即默认拷贝构造函数只是对对象进行浅拷贝复制(逐个成员依次拷贝)，即只复制对象空间而不复制资源。

看两个例子，对比赋值与浅拷贝会对原对象带来哪些改变？

```
// 对象赋值
 var obj1 = {
    'name' : 'zhangsan',
    'age' :  '18',
    'language' : [1,[2,3],[4,5]],
};
var obj2 = obj1;
obj2.name = "lisi";
obj2.language[1] = ["二","三"];
console.log('obj1',obj1)
console.log('obj2',obj2)
复制代码
```



![img](https://user-gold-cdn.xitu.io/2018/12/23/167da6385719783f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



```
// 浅拷贝
 var obj1 = {
    'name' : 'zhangsan',
    'age' :  '18',
    'language' : [1,[2,3],[4,5]],
};
 var obj3 = shallowCopy(obj1);
 obj3.name = "lisi";
 obj3.language[1] = ["二","三"];
 function shallowCopy(src) {
    var dst = {};
    for (var prop in src) {
        if (src.hasOwnProperty(prop)) {
            dst[prop] = src[prop];
        }
    }
    return dst;
}
console.log('obj1',obj1)
console.log('obj3',obj3)
复制代码
```

**for in**

for...in语句以任意顺序遍历一个对象自有的、继承的、`可枚举的`、非Symbol的属性。对于每个不同的属性，语句都会被执行。

**hasOwnProperty**

> 语法：obj.hasOwnProperty(prop)
>  prop是要检测的属性`字符串`名称或者`Symbol`

该函数返回值为布尔值，所有继承了 Object 的对象都会继承到 hasOwnProperty 方法，和 in 运算符不同，该函数会忽略掉那些从原型链上继承到的属性和自身属性。




![img](https://user-gold-cdn.xitu.io/2018/12/23/167da6b049c1616a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

上面例子中，obj1是原始数据，obj2是赋值操作得到，而obj3浅拷贝得到。我们可以很清晰看到对原始数据的影响，具体请看下表：





![img](https://user-gold-cdn.xitu.io/2018/12/23/167da74d45d3103b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

### 浅拷贝的实现方式

#### 1.Object.assign()

Object.assign() 方法可以把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象。但是 Object.assign()进行的是浅拷贝，拷贝的是对象的属性的引用，而不是对象本身。

```
var obj = { a: {a: "kobe", b: 39} };
var initalObj = Object.assign({}, obj);
initalObj.a.a = "wade";
console.log(obj.a.a); //wade
复制代码
```

注意：**当object只有一层的时候，是深拷贝**

```
let obj = {
    username: 'kobe'
    };
let obj2 = Object.assign({},obj);
obj2.username = 'wade';
console.log(obj);//{username: "kobe"}
复制代码
```

#### 2.Array.prototype.concat()

```
let arr = [1, 3, {
    username: 'kobe'
    }];
let arr2=arr.concat();    
arr2[2].username = 'wade';
console.log(arr);
复制代码
```

修改新对象会改到原对象:

![img](https://user-gold-cdn.xitu.io/2018/7/30/164e6c558688eb85?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



#### 3.Array.prototype.slice()

```
let arr = [1, 3, {
    username: ' kobe'
    }];
let arr3 = arr.slice();
arr3[2].username = 'wade'
console.log(arr);
复制代码
```

同样修改新对象会改到原对象:



![img](https://user-gold-cdn.xitu.io/2018/7/30/164e6cc82022ba02?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**关于Array的slice和concat方法的补充说明**：Array的slice和concat方法不修改原数组，只会返回一个浅复制了原数组中的元素的一个新数组。



原数组的元素会按照下述规则拷贝：

- 如果该元素是个对象引用(不是实际的对象)，slice 会拷贝这个对象引用到新的数组里。两个对象引用都引用了同一个对象。如果被引用的对象发生改变，则新的和原来的数组中的这个元素也会发生改变。
- 对于字符串、数字及布尔值来说（不是 String、Number 或者 Boolean 对象），slice 会拷贝这些值到新的数组里。在别的数组里修改这些字符串或数字或是布尔值，将不会影响另一个数组。

## 深拷贝操作

说了赋值操作和浅拷贝操作，大家是不是已经能想到什么是深拷贝了，下面直接说深拷贝的定义。

### 深拷贝定义

> 深拷贝会另外拷贝一份一个一模一样的对象,从堆内存中开辟一个新的区域存放新对象,新对象跟原对象不共享内存，修改新对象不会改到原对象。

### 深拷贝实例

#### JSON.parse(JSON.stringify())

JSON.stringify()是前端开发过程中比较常用的深拷贝方式。

用JSON.stringify将对象转成JSON字符串，再用JSON.parse()把字符串解析成对象，一去一来，新的对象产生了，而且对象会开辟新的栈，实现深拷贝。

- 举例说明：

```
let arr = [1, 3, {
    username: ' koala'
}];
let arr4 = JSON.parse(JSON.stringify(arr));
arr4[2].username = 'smallKoala'; 
console.log(arr4);// [ 1, 3, { username: 'smallKoala' } ]
console.log(arr);// [ 1, 3, { username: ' koala' } ]
复制代码
```

实现了深拷贝，当改变数组中对象的值时候，原数组中的内容并没有发生改变。JSON.stringify()虽然可以实现深拷贝，但是还有一些弊端比如不能处理函数等。

- JSON.stringify()实现深拷贝注意点

1. 拷贝的对象的值中如果有函数,undefined,symbol则经过JSON.stringify()序列化后的JSON字符串中这个键值对会消失
2. 无法拷贝不可枚举的属性，无法拷贝对象的原型链
3. 拷贝Date引用类型会变成字符串
4. 拷贝RegExp引用类型会变成空对象
5. 对象中含有NaN、Infinity和-Infinity，则序列化的结果会变成null
6. 无法拷贝对象的循环应用(即obj[key] = obj)

#### 自己实现一个简单深拷贝

深拷贝，主要用到的思想是递归，遍历对象、数组直到里边都是基本数据类型，然后再去复制，就是深度拷贝。 实现代码：

```
    //定义检测数据类型的功能函数
    function isObject(obj) {
	    return typeof obj === 'object' && obj != null;
    }
   function cloneDeep(source) {

    if (!isObject(source)) return source; // 非对象返回自身
      
    var target = Array.isArray(source) ? [] : {};
    for(var key in source) {
        if (Object.prototype.hasOwnProperty.call(source, key)) {
            if (isObject(source[key])) {
                target[key] = cloneDeep(source[key]); // 注意这里
            } else {
                target[key] = source[key];
            }
        }
    }
    return target;
}


复制代码
```

该简单深拷贝未考虑内容： 遇到循环引用，会陷入一个循环的递归过程，从而导致爆栈

#### 第三方深拷贝库

该函数库也有提供_.cloneDeep用来做 Deep Copy（lodash是一个不错的第三方开源库，有好多不错的函数，也可以看具体的实现源码）

```
var _ = require('lodash');
var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};
var obj2 = _.cloneDeep(obj1);
console.log(obj1.b.f === obj2.b.f);
// false
```




