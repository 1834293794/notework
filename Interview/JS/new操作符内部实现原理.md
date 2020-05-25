# new操作符内部实现原理

简单来说，分为四步： ① JS内部首先会先生成一个对象； ② 再把函数中的this指向该对象； ③ 然后执行构造函数中的语句； ④ 最终返回该对象实例。

**`new` 关键字会进行如下操作：**

1. 创建一个空的`JavaScript`对象（即`{}`）；
2. 将函数的 `prototype` 赋值给对象的 `__proto__`属性 ；
3. 调用函数，并将步骤1新创建的对象作为函数的`this`上下文 ；
4. 如果该函数没有返回值或者返回值不是对象，则返回创建的对象，如果返回值是对象，则直接返回该对象。

**示例：**

```
function Person(name) {
    this.name = name;
}
function Person2(name) {
    this.name = name;
    return this.name;
}
function Person3(name) {
    this.name = name;
    return new Array();
}
function Person4(name) {
    this.name = name;
    return new String(name);
}
function Person5(name) {
    this.name = name;
    return function() {};
}


var person = new Person('John');  // {name: 'John'}
var person2 = new Person2('John');  // {name: 'John'}
var person3 = new Person3('John');  // []
var person4 = new Person4('John');  // 'John'
var person5 = new Person5('John');  // function() {}
复制代码
```

`Person`本身是一个普通函数，当通过`new`来创建对象时，`Person`就是构造函数了。

JS引擎执行这句代码时，在内部做了很多工作，用**伪代码模拟其工作流程**如下：

```
new Person("John") = {
    var obj = {};
	obj.__proto__ = Person.prototype; // 此时便建立了obj对象的原型链： obj->Person.prototype->Object.prototype->null
	var result = Person.call(obj,"John"); // 相当于obj.Person("John")
	return typeof result === 'object' ? result : obj; // 如果无返回值或者返回一个非对象值，则将obj返回作为新对象
}
```

