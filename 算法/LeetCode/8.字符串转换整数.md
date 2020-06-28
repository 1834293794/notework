# 字符串转换整数

#### [字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

## 一、投机取巧法

### 题意分析
官方的主要规则可以概况为：

- 无视开头空格
- 返回有符号整数
- 无视整数部分后的字符
- 范围在32位内（含）
- 其他情况返回0
  各位JSer，你品，你细品，这个转换规则，是不是很眼熟？

谜底揭晓，**它就是JavaScript世界中的parseInt()这一API的转换规则**。

### 逻辑梳理
现在来简要阐述下parseInt()的转换规则，关于这个API的具体描述，可以查看MDN上的这篇文档。

parseInt(string, radix)：

string：要被解析的值。如果参数不是一个字符串，则将其转换为字符串。字符串开头的空白符将会被忽略。
radix（可选）：需要转换的进制，介于 2 到 36。
返回值： 如果被解析参数的第一个字符无法被转化成数值类型，则返回NaN。

**对比下题意**，发现

- 无视开头空格（满足）
- 返回有符号整数（满足）
- 无视整数部分后的字符（满足）
- 范围在32位内（含）（不满足）
- 其他情况返回0（不满足）
  那么只要有针对性的处理下不满足的条件即可。

**范围在32位内（含）**
只需简单地将API转换后的值与临界值进行对比就行。

if (number < Math.pow(-2, 31) || number > Math.pow(2, 31) - 1) {
    return number < Math.pow(-2, 31) ? Math.pow(-2, 31) : Math.pow(2, 31) - 1;
}
**其他情况返回0**
很显然，API的返回值如果是NaN，则说明无法正常转换，所以只需将返回值和NaN进行比较即可。

注意，NaN和NaN并不全等，所以各位JSer不能使用全等操作符（===），而该使用isNaN()函数来比较。

if(isNaN(number)) {
    return 0;
} 
**小细节**
在使用parseInt(string, radix)这一API时，如果不传入radix参数，会有两种特殊情况：

如果字符串 string 以"0x"或者"0X"开头, 则基数是16 (16进制).
如果字符串 string 以"0"开头, 基数是8（八进制）或者10（十进制），那么具体是哪个基数，取决与ECMAScript的版本。
所以，通常建议在使用parseInt()这一API时，都明确给出期望的进制数，这是一个良好的编程习惯。

### 代码实现
以下是具体的代码实现：

```javascript
/**
 * @param {string} str
 * @return {number}
 */
var myAtoi = function(str) {
    const number = parseInt(str, 10);

    if(isNaN(number)) {
        return 0;
    } else if (number < Math.pow(-2, 31) || number > Math.pow(2, 31) - 1) {
        return number < Math.pow(-2, 31) ? Math.pow(-2, 31) : Math.pow(2, 31) - 1;
    } else {
        return number;
    }
};

```

## 二、自动机

### 题意分析
无论你事先有没有阅读过官方题解，这里统一从头分析一遍。

何谓“自动机”？这里引用LeetCode官方的解释：

**我们的程序在每个时刻有一个状态s，每次从序列中输入一个字符c，并根据字符c 转移到下一个状态s'。这样，我们只需要建立一个覆盖所有情况的从s与c映射到s'的表格即可解决题目中的问题。**

是不是觉得这句话有点拗口？没关系，我会用更加通俗易懂的语言来为你讲解一遍。

**状态分析**
首先，从题意中，我们很轻易地可以知道，字符串str中的每个字符，都有可能是以下的四种类型中的一种：

- 空格字符' '（Space）
- 正负号+和-(Sign)
- 字符串型的数值（Number）
- 除以上三种情况外的任何情况（Other）

**阶段分析**
如果想要将字符串转换为整数，那么必然会经历这四个有序的阶段：

1. 开始转换（start）
2. 判断正负（signed）
3. 生成数值（in_number）
4. 结束转换（end）

**生成自动机**
这步是最为关键的一步，它将状态和阶段巧妙地结合了起来。

话不多说，让我们先来看一个表格：

![1585925170(1).png](https://pic.leetcode-cn.com/0ee783ff33682169033d26832e12619ef5186cff4ec46fa7449ab548b458fb56-1585925170(1).png)


现在来说明下这个表格的含义。

**不同的行象征不同执行阶段：**

- 第0行：开始转换阶段
- 第1行：判断正负阶段
- 第2行：生成数值阶段
- 第3行：结束转换阶段

**不同的列象征不同的字符类型：**

- 第0列：字符为空格
- 第1列：字符为正、负号
- 第2列：字符为字符型数值
- 第3列：字符为其他形式

由行、列确定的坐标，象征着下一个字符所处的执行阶段。

**例如，官方的str例子：" -42",就会进行如下的转换流程（请结合表格认真阅读，这对你理解“自动机”的概念很有帮助）：**

1. 一开始，必然是开始阶段，所以从第0行，即[0, ?]
2. 第一个字符是空格，找到空格所在的列，即[?, 0]
3. 结合行、列，即[0, 0],发现将为这个字符执行start阶段
4. 第二个字符还是从第0行开始，即[0, ?]，是空格，空格的列数是[?, 0]，所以执行start阶段(空格的分析不再赘述）
5. 发现字符是负号-,而此时是在第0行（之前空格的原因），坐标是[0, 1]，当前执行阶段从start变为signed，并进行该阶段的相关操作
6. 接下来的字符是字符型的4，所以坐标确定为[1, 2]，执行阶段从signed变为in_number，并进行该阶段的相关操作
7. 这次的字符还是字符型（2），还是执行in_number阶段，并进行该阶段的相关操作
8. 没有字符了，遍历结束
   依据负号和数值,得出转换结果为-42
   现在你理解了如何由状态和阶段构建自动机了吧？

以上的这些步骤描述，其实就是LeetCode官方图例所表达的意思。

![8_fig1.png](https://pic.leetcode-cn.com/38f1d911b9187aad7c823607e7a8b69e886cd14d298d06fc437763661d91e5b2-8_fig1.png)

### 代码实现
以下是具体的代码实现，参考了LeetCode的官方示例C++代码。

```javas
/**
 * @param {string} str
 * @return {number}
 */
var myAtoi = function(str) {
  // 自动机类
  class Automaton{
    constructor() {
      // 执行阶段，默认处于开始执行阶段
      this.state = 'start';
      // 正负符号，默认是正数
      this.sign = 1;
      // 数值，默认是0
      this.answer = 0;
      /*
      关键点：
      状态和执行阶段的对应表
      含义如下：
      [执行阶段, [空格, 正负, 数值, 其他]]
      */
      this.map = new Map([
        ['start', ['start', 'signed', 'in_number', 'end']],
        ['signed', ['end', 'end', 'in_number', 'end']],
        ['in_number', ['end', 'end', 'in_number', 'end']],
        ['end', ['end', 'end', 'end', 'end']]
      ])
    }

    // 获取状态的索引
    getIndex(char) {
      if (char === ' ') {
        // 空格判断
        return 0;
      } else if (char === '-' || char === '+') {
        // 正负判断
        return 1;
      } else if (typeof Number(char) === 'number' && !isNaN(char)) {
        // 数值判断
        return 2;
      } else {
        // 其他情况
        return 3;
      }
    }

    /*
    关键点：
    字符转换执行函数
    */
    get(char) {
      /*
      易错点：
      每次传入字符时，都要变更自动机的执行阶段
      */
      this.state = this.map.get(this.state)[this.getIndex(char)];

      if(this.state === 'in_number') {
        /*
        小技巧：
        在JS中，对字符串类型进行减法操作，可以将得到一个数值型（Number）的值

        易错点：
        本处需要利用括号来提高四则运算的优先级
        */
        this.answer = this.answer * 10 + (char - 0);

        /*
        易错点：
        在进行负数比较时，需要将INT_MIN变为正数
        */
        this.answer = this.sign === 1 ? Math.min(this.answer, Math.pow(2, 31) - 1) : Math.min(this.answer, -Math.pow(-2, 31));
      } else if (this.state === 'signed') {
        /*
        优化点：
        对于一个整数来说，非正即负，
        所以正负号的判断，只需要一次。
        故，可以降低其判断的优先级
        */
        this.sign = char === '+' ? 1 : -1;
      }
    }
  }

  // 生成自动机实例
  let automaton = new Automaton();

  // 遍历每个字符
  for(let char of str) {
    // 依次进行转换
    automaton.get(char);
  }

  // 返回值，整数 = 正负 * 数值
  return automaton.sign * automaton.answer;
};


```



