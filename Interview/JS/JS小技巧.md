# 小技巧

## 1.**将数字强制转换为32位有符号整数**

### 按位操作符

**按位操作符（Bitwise operators）** 将其操作数（operands）当作32位的比特序列（由0和1组成），而不是十进制、十六进制或八进制[数值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)。例如，十进制数9，用二进制表示则为1001。按位操作符操作数字的二进制形式，但是返回值依然是标准的JavaScript数值。

下面的表格总结了JavaScript中的按位操作符：

| 运算符                                                       | 用法      | 描述                                                         |
| :----------------------------------------------------------- | :-------- | :----------------------------------------------------------- |
| [按位与（ AND）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_AND) | `a & b`   | 对于每一个比特位，只有两个操作数相应的比特位都是1时，结果才为1，否则为0。 |
| [按位或（OR）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_OR) | `a | b`   | 对于每一个比特位，当两个操作数相应的比特位至少有一个1时，结果为1，否则为0。 |
| [按位异或（XOR）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_XOR) | `a ^ b`   | 对于每一个比特位，当两个操作数相应的比特位有且只有一个1时，结果为1，否则为0。 |
| [按位非（NOT）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Bitwise_NOT) | `~ a`     | 反转操作数的比特位，即0变成1，1变成0。                       |
| [左移（L](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Left_shift)[eft shift）](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Left_shift) | `a << b`  | 将 `a` 的二进制形式向左移 `b` (< 32) 比特位，右边用0填充。   |
| [有符号右移](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Right_shift) | `a >> b`  | 将 a 的二进制表示向右移` b `(< 32) 位，丢弃被移出的位。      |
| [无符号右移](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#Unsigned_right_shift) | `a >>> b` | 将 a 的二进制表示向右移` b `(< 32) 位，丢弃被移出的位，并使用 0 在左侧填充。 |

### 32位整数运算

首先要清楚，在JS中无论是**整数还是小数**都是按照**64位的浮点数形式存储**，而**整数运算**会自动**转化为32位有符号整数**。

有符号整数使用 31 位表示整数的数值，用第 32 位表示整数的符号，0 表示正数，1 表示负数。数值范围为 [-2^31 , 2^31-1]， 即[ -2147483648 , 2147483647 ]。

JavaScript 进行位操作时，采用32位有符号整型，这意味着其转换的结果也是32位有符号整型。

**所有的按位操作符的操作数都会被转成补码（two's complement）形式的有符号32位整数。**

也就是说我们可以通过类似以下操作，**将result中保存的数字强制转换为32位有符号整数**

```javascript
result | 0
```



## 2.在JS中，对字符串类型进行减法操作，可以将得到一个数值型（Number）的值

```javas
let s="123"
let b=s-0 //typeof b === "number"
```

