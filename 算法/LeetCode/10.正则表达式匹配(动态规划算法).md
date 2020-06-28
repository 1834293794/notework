# [正则表达式匹配](https://leetcode-cn.com/problems/regular-expression-matching/)

## 题目

给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。

```javascript
'.' 匹配任意单个字符
'*' 匹配零个或多个前面的那一个元素
```

所谓匹配，是要涵盖 整个 字符串 s的，而不是部分字符串。

说明:

- s 可能为空，且只包含从 a-z 的小写字母。
- p 可能为空，且只包含从 a-z 的小写字母，以及字符 . 和 *。

示例 1:

```javascript
输入:
s = "aa"
p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。
```

示例 2:

```javascript
输入:
s = "aa"
p = "a*"
输出: true
解释: 因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
```

示例 3:

```javas
输入:
s = "ab"
p = ".*"
输出: true
解释: ".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。
```

示例 4:

```javascript
输入:
s = "aab"
p = "c*a*b"
输出: true
解释: 因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。
```

示例 5:

```javascript
输入:
s = "mississippi"
p = "mis*is*p*."
输出: false
```

## 题解

**动态规划 **

### 思路分析

- 状态定义

  ```javascript
  dp[i][j]：表示s的前i个是否能被p的前j个匹配，布尔类型
  ```

- 状态方程

  - p[j] == s[i]

    ```javascript
    dp[i][j] = dp[i-1][j-1]
    当前同一位置的字符匹配，则判断上一位的字符是否匹配
    ```

  - p[j] == "."

    ```javascript
    dp[i][j] = dp[i-1][j-1]
    "."可以换成和s[i]相同的字符，则情况同第一种一样
    ```

  - p[j] == " * "

    - " * "匹配前面0个或n个字符，因此要越过" * "，匹配规则至少退到上一步，即比较p[j-1]和s[i]

      - p[j-1] != s[i]

        ```javascript
        dp[i][j] = dp[i][j-2]
        对应于匹配前一个字符0次
        例如：s = 'aa'，p = 'aac*'，那么此时，就要将匹配规则后退2步，即用'aa'去匹配'aa'
        ```

      - p[j-1] == s[i]

        ```javascript
        1.dp[i][j] = dp[i][j-2]
        对应于匹配前一个字符0次，或者说不匹配
        例如：s = 'ab'，p = 'abb*'，那么此时，就要将匹配规则后退2步，即用'ab'去匹配'ab'
        ```

        ```javascript
        2.dp[i][j] = dp[i][j-1]
        对应于匹配前一个字符1次
        例如：s = 'ac'，p = 'ac*'，那么此时，就要将匹配规则后退1步，即用'ac'去匹配'ac'
        ```

        ```javascript
        3.dp[i][j] = dp[i-1][j]
        对应于匹配前一个字符n次
        例如：s = 'abbbb'，p = 'ab*'，
        	那么此时，就要将匹配规则前进n-1步，即复制n-1份前一个字符b
        	相当于s后退n-1步
        		又因为动态规划属于递推，所以每次s后退一步就可以了
        ```

      - p[j] == " . "
        同p[j-1] == s[i]，所以将此条件归并但上一步的条件中即可

- 初始化
  s[i]的前0个字符 和 p[j]的前j个字符能否匹配

## 代码

```javascript
var isMatch = function(s, p) {
    let n = s.length;
    let m = p.length;
    let dp = Array.from(new Array(n+1),() => new Array(m+1).fill(false));
    dp[0][0] = true;
    for(let i = 0;i <= m;i++){
        if(p[i] == '*' && dp[0][i-1]){
            dp[0][i+1] = true;
        }
    }
    for(let i = 1;i <= n;i++){
        for(let j = 1;j <= m;j++){
            if(p[j-1] == s[i-1] || p[j-1] == '.'){
                dp[i][j] = dp[i-1][j-1]
            }else if(p[j-1] == '*'){
                if(p[j-2] != s[i-1]){
                    dp[i][j] = dp[i][j-2];
                }
                if(p[j-2] == s[i-1] || p[j-2] == '.'){
                    dp[i][j] = dp[i-1][j] || dp[i][j-1] || dp[i][j-2];
                }
            }
        }
    }
    return dp[n][m];
};

```

