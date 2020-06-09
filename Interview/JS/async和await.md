# async和await

## 1.async 和 await语法糖详解

async用于声明一个function是异步的，不管function是普通函数还是异步函数，通过async修饰之后，返回的都是一个promise对象；而 await 用于等待一个异步方法执行完成。

await只能出现在async函数中或者 let value = await promise这种在环境的顶层作用域中使用，比如在window作用顶层函数中可以使用;但是await可以用来修饰任何函数，通过await修饰的任何函数，表示等待该行数执行之后再往后执行，表明是一个同步的过程。

### async用法: 

async函数（包含函数语句、函数表达式、Lambda表达式）会返回一个Promise对象，如果在asyn声明的一个函数中直接return 一个直接量，async 会把这个直接量通过Promise.resolve()封装成 Promise 对象。

### async 起什么作用

这个问题的关键在于，async 函数是怎么处理它的返回值的！

我们当然希望它能直接通过 `return` 语句返回我们想要的值，但是如果真是这样，似乎就没 await 什么事了。所以，写段代码来试试，看它到底会返回什么：

```
async function testAsync() {
    return "hello async";
}

const result = testAsync();
console.log(result);
```

看到输出就恍然大悟了——输出的是一个 Promise 对象。

```
Promise {<resolved>: "hello async"}
```

所以，async 函数返回的是一个 Promise 对象。从[文档](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function)中也可以得到这个信息。async 函数（包含函数语句、函数表达式、Lambda表达式）会返回一个 Promise 对象，如果在函数中 `return` 一个直接量，async 会把这个直接量通过 `Promise.resolve()` 封装成 Promise 对象。

async 函数返回的是一个 Promise 对象，所以在最外层不能用 await 获取其返回值的情况下，我们当然应该用原来的方式：`then()` 链来处理这个 Promise 对象，就像这样

```
testAsync().then(v => {
    console.log(v);    // 输出 hello async
});
```

现在回过头来想下，如果 async 函数没有返回值，又该如何？很容易想到，它会返回 `Promise.resolve(undefined)`。

联想一下 Promise 的特点——无等待，所以在没有 `await` 的情况下执行 async 函数，它会立即执行，返回一个 Promise 对象，并且，绝不会阻塞后面的语句。这和普通返回 Promise 对象的函数并无二致。

那么下一个关键点就在于 await 关键字了。

### await 到底在等啥

### await用法：如下图，如果await调用不是在异步函数作用就会出错

![img](https://user-gold-cdn.xitu.io/2019/4/26/16a59a944028abd8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1、await用在async函数中

![img](https://user-gold-cdn.xitu.io/2019/4/26/16a59a81c145d77c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

2、await用在顶层函数中

![img](https://user-gold-cdn.xitu.io/2019/4/26/16a59a8bf75bf4a7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)









```
function getSomething() {
    return "something";
}
function testPromise() {
    return Promise.resolve("hello Promise");
}
async function testAsync() {
    return "hello async";
}
async function testNormal() {
    const v1 = await getSomething();  //await用来修饰一个普通函数
    const v2 = await testPromise();  //await用来修饰一个返回promise对象的函数
    const v3 = await testAsync(); //await用来修饰一个通过async构成的异步函数
    //上面的await这种写法等价于下面这种promise的写法
   console.log(getSomething()) 
    let vv=testPromise().then(res=>{
            console.log(res)
            testAsync().then(res=>{
              console.log(res)
            })
         })
    console.log(v1,v2,v3);
}
testNormal()复制代码
```

async和await的优点用于处理Promise的.then处理，之前我们想在异步函数中达到同步的想过，所以异步执行之后的要执行的代码都会放在Promise的then方法中执行，达到同步的效果，但是async和await的出现，让js中的异步函数可以按顺序写代码的方式，达到代码顺序执行的效果。



一般来说，都认为 await 是在等待一个 async 函数完成。不过按[语法说明](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/await)，await 等待的是一个表达式，这个表达式的计算结果是 Promise 对象或者其它值（换句话说，就是没有特殊限定）。

因为 async 函数返回一个 Promise 对象，所以 await 可以用于等待一个 async 函数的返回值——这也可以说是 await 在等 async 函数，但要清楚，它等的实际是一个返回值。注意到 await 不仅仅用于等 Promise 对象，它可以等任意表达式的结果，所以，await 后面实际是可以接普通函数调用或者直接量的。所以下面这个示例完全可以正确运行

```
function getSomething() {
    return "something";
}

async function testAsync() {
    return Promise.resolve("hello async");
}

async function test() {
    const v1 = await getSomething();
    const v2 = await testAsync();
    console.log(v1, v2);
}

test();
```

### await 等到了要等的，然后呢

await 等到了它要等的东西，一个 Promise 对象，或者其它值，然后呢？我不得不先说，`await` 是个运算符，用于组成表达式，await 表达式的运算结果取决于它等的东西。

如果它等到的不是一个 Promise 对象，那 await 表达式的运算结果就是它等到的东西。

如果它等到的是一个 Promise 对象，await 就忙起来了，它会**阻塞后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值**，作为 await 表达式的运算结果。

> 看到上面的阻塞一词，心慌了吧……放心，这就是 await 必须用在 async 函数中的原因。async 函数调用不会造成阻塞，它内部所有的阻塞都被封装在一个 Promise 对象中异步执行。

## 2. async/await 帮我们干了啥

### 2.1. 作个简单的比较

上面已经说明了 async 会将其后的函数（函数表达式或 Lambda）的返回值封装成一个 Promise 对象，而 await 会等待这个 Promise 完成，并将其 resolve 的结果返回出来。

现在举例，用 `setTimeout` 模拟耗时的异步操作，先来看看不用 async/await 会怎么写

```
function takeLongTime() {
    return new Promise(resolve => {
        setTimeout(() => resolve("long_time_value"), 1000);
    });
}

takeLongTime().then(v => {
    console.log("got", v);
});
```

如果改用 async/await 呢，会是这样

```
function takeLongTime() {
    return new Promise(resolve => {
        setTimeout(() => resolve("long_time_value"), 1000);
    });
}

async function test() {
    const v = await takeLongTime();
    console.log(v);
}

test();
```

眼尖的同学已经发现 `takeLongTime()` 没有申明为 `async`。实际上，**`takeLongTime()` 本身就是返回的 Promise 对象，加不加 `async` 结果都一样**

又一个疑问产生了，这两段代码，两种方式对异步调用的处理（实际就是对 Promise 对象的处理）差别并不明显，甚至使用 async/await 还需要多写一些代码，那它的优势到底在哪？

### 2.2. async/await 的优势在于处理 then 链

单一的 Promise 链并不能发现 async/await 的优势，但是，如果需要处理由多个 Promise 组成的 then 链的时候，优势就能体现出来了（很有意思，**Promise 通过 then 链来解决多层回调的问题，现在又用 async/await 来进一步优化它**）。

假设一个业务，分多个步骤完成，每个步骤都是异步的，而且依赖于上一个步骤的结果。我们仍然用 `setTimeout` 来模拟异步操作：

```
/**
 * 传入参数 n，表示这个函数执行的时间（毫秒）
 * 执行的结果是 n + 200，这个值将用于下一步骤
 */
function takeLongTime(n) {
    return new Promise(resolve => {
        setTimeout(() => resolve(n + 200), n);
    });
}

function step1(n) {
    console.log(`step1 with ${n}`);
    return takeLongTime(n);
}

function step2(n) {
    console.log(`step2 with ${n}`);
    return takeLongTime(n);
}

function step3(n) {
    console.log(`step3 with ${n}`);
    return takeLongTime(n);
}
```

现在用 Promise 方式来实现这三个步骤的处理

```
function doIt() {
    console.time("doIt");
    const time1 = 300;
    step1(time1)
        .then(time2 => step2(time2))
        .then(time3 => step3(time3))
        .then(result => {
            console.log(`result is ${result}`);
            console.timeEnd("doIt");
        });
}

doIt();

// c:\var\test>node --harmony_async_await .
// step1 with 300
// step2 with 500
// step3 with 700
// result is 900
// doIt: 1507.251ms
```

输出结果 `result` 是 `step3()` 的参数 `700 + 200` = `900`。`doIt()` 顺序执行了三个步骤，一共用了 `300 + 500 + 700 = 1500` 毫秒，和 `console.time()/console.timeEnd()` 计算的结果一致。

如果用 async/await 来实现呢，会是这样

```
async function doIt() {
    console.time("doIt");
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time2);
    const result = await step3(time3);
    console.log(`result is ${result}`);
    console.timeEnd("doIt");
}

doIt();
```

结果和之前的 Promise 实现是一样的，但是这个代码看起来是不是清晰得多，几乎跟同步代码一样

### 2.3. 还有更酷的

现在把业务要求改一下，仍然是三个步骤，但每一个步骤都需要之前每个步骤的结果。

```
function step1(n) {
    console.log(`step1 with ${n}`);
    return takeLongTime(n);
}

function step2(m, n) {
    console.log(`step2 with ${m} and ${n}`);
    return takeLongTime(m + n);
}

function step3(k, m, n) {
    console.log(`step3 with ${k}, ${m} and ${n}`);
    return takeLongTime(k + m + n);
}
```

这回先用 async/await 来写：

```
async function doIt() {
    console.time("doIt");
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time1, time2);
    const result = await step3(time1, time2, time3);
    console.log(`result is ${result}`);
    console.timeEnd("doIt");
}

doIt();

// c:\var\test>node --harmony_async_await .
// step1 with 300
// step2 with 800 = 300 + 500
// step3 with 1800 = 300 + 500 + 1000
// result is 2000
// doIt: 2907.387ms
```

除了觉得执行时间变长了之外，似乎和之前的示例没啥区别啊！别急，认真想想如果把它写成 Promise 方式实现会是什么样子？

```
function doIt() {
    console.time("doIt");
    const time1 = 300;
    step1(time1)
        .then(time2 => {
            return step2(time1, time2)
                .then(time3 => [time1, time2, time3]);
        })
        .then(times => {
            const [time1, time2, time3] = times;
            return step3(time1, time2, time3);
        })
        .then(result => {
            console.log(`result is ${result}`);
            console.timeEnd("doIt");
        });
}

doIt();
```

有没有感觉有点复杂的样子？那一堆参数处理，就是 Promise 方案的死穴—— 参数传递太麻烦了，看着就晕！

## 3. 洗洗睡吧

就目前来说，已经理解 async/await 了吧？但其实还有一些事情没提及——Promise 有可能 reject 啊，怎么处理呢？如果需要并行处理3个步骤，再等待所有结果，又该怎么处理呢？

[阮一峰老师已经说过了](http://www.ruanyifeng.com/blog/2015/05/async.html)

## 注意点

await 命令后面的 Promise 对象，运行结果可能是 rejected，所以最好把 await 命令放在 try...catch 代码块中。

> ```javascript
> async function myFunction() {
>   try {
>     await somethingThatReturnsAPromise();
>   } catch (err) {
>     console.log(err);
>   }
> }
> 
> // 另一种写法
> 
> async function myFunction() {
>   await somethingThatReturnsAPromise().catch(function (err){
>     console.log(err);
>   });
> }
> ```