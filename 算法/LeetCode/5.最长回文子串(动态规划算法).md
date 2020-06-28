# 动态规划算法

## 什么是动态规划？
​     和分治法一样，动态规划（dynamic programming）是通过组合子问题而解决整个问题的解。

     分治法是将问题划分成一些独立的子问题，递归地求解各子问题，然后合并子问题的解。
    
     动态规划适用于子问题不是独立的情况，也就是各子问题包含公共的子子问题。
    
     此时，分治法会做许多不必要的工作，即重复地求解公共的子问题。动态规划算法对每个子问题只求解一次，将其结果保存起来，从而避免每次遇到各个子问题时重新计算答案。

## 应用动态规划求解的问题的特点

1. 求一个问题的最优解
2. 整题问题的最优解是依赖各个子问题的最优解
3. 大问题分解成若干个小问题，这些小问题之间还有相互重叠的更小的子问题
4. 从上往下分析问题，从下往上求解问题

在应用过程中，为了避免重复求解子问题，通常把已经解决的子问题的最优解存储下来（大部分面试题都是存储在一维或者二维的数组里）

## 适用范围

​     最优性原理体现为问题的最优子结构特性。当一个问题的最优解中包含了子问题的最优解时，则称该问题具有最优子结构特性。

 最优性原理是动态规划的基础。任何一个问题，如果失去了这个最优性原理的支持，就不可能用动态规划设计求解。

1. 问题中的状态满足最优性原理。

2. 问题中的状态必须满足无后效性。

 所谓无后效性是指：“**下一时刻的状态只与当前状态有关，而和当前状态之前的状态无关，当前状态是对以往决策的总结**”。

## 面试题14：剪绳子

### 题目要求：
 给你一根长度为n的绳子，请把绳子剪成m段，记每段绳子长度为k[0],k[1]...k[m-1],求k[0]*k[1]...*k[m-1]的乘积最大值。已知绳子长度n为整数，m>1(至少要剪一刀，不能不剪)，k[0],k[1]...k[m-1]均要求为整数。
 例如，绳子长度为8时，把它剪成3-3-2，得到最大乘积18；绳子长度为3时，把它剪成2-1，得到最大乘积2。

### 解题思路：

 本题有动态规划算法的几个明显特征：
 （1）是求最优解问题，如最大值，最小值；
 （2）该问题能够分解成若干个子问题，并且子问题之间有重叠的更小子问题。
 通常按照如下4个步骤来设计一个动态规划算法：
 （1）刻画一个最优解的结构特征；
 （2）递归地定义最优解的值；
 （3）计算最优解的值，通常采用自底向上的方法；
 （4）利用计算出的信息构造一个最优解。
 以此题为例，我们定义长度为n的绳子剪切后的最大乘积为f(n),剪了一刀后,f(n)=max(f(i)*f(n-i));假设n为10，第一刀之后分为了4-6，而6也可能再分成2-4（6的最大是3-3，但过程中还是要比较2-4这种情况的），而上一步4-6中也需要求长度为4的问题的最大值，可见，各个子问题之间是有重叠的，所以可以先计算小问题，存储下每个小问题的结果，逐步往上，求得大问题的最优解。

### 参考代码

```c
int maxProductAfterCutting_solution1(int length){
    if(length<2)
        return 0;
    if(length==2)
        return 1;
    if(length==3)
        return 2;
    
    int* products = new int[length+1];
    products[0]=0;
    products[1]=1;
    products[2]=2;
    products[3]=3;
    
    int max=0;
    for(int i=4;i<=length;++i){
        max=0;
        for(int j=1;j<=i/2;++j){
            int product=products[j]*products[i-j];
            if(max<product)
                max=product;
            products[i]=max;
        }
    }
    
    max=products[length];
    delete[] products;
    
    return max
}
```

### 代码分析

1. 代码一开始进行了三个if判断，这是针对绳子长度小于2，为2，为3这三种情况，因为题干要求至少剪一刀，而最小单位是1
   - 所以绳子小于2时剪不下去，返回0
   - 绳子为2时，只能剪成长度为1的两段，乘积为1，1<2，还不如不剪，但因为至少剪一刀，所以长度为2这种情况直接返回1
   - 绳子为3时，情况同2类似，剪下去，要么是1x2，要么是1x1x1，最后只能返回2
2. 当绳子长度大于等于4后，进入了一般情况，这里先创建了一个products数组用于存储各个子问题的最优解，并先赋了几个值，例如products[2]=2，表明在绳子为2时，综合考虑了剪下去的情况和不剪的情况，最后得出最大值是2，也就是不剪才是最好的，products[3]=3同理。
3. 举例：绳子为4时，循环中比较了剪成1和3、剪成2和2这两种情况，拿1和3看看，现在我们要做的就是想办法让1和3这两段绳子都达到各自的最优解法，而之前存下来的products[1]=1和products[3]=3已经告诉了我们答案，综合考虑剪和不剪的情况后，长度为1的绳子最大值就是1，长度为3的绳子最大值就是3，所以剪成1和3时最大值就是products[1]xproducts[3]=3，同理剪成2和2时最大值就是products[2]xproducts[2]=4，4>3，因此最后products[4]=4，之后当一些绳子剪出长度为4的子段时，我们可以同样可以根据products[4]=4直接得出长度为4的绳子的最大值就是4，不需要重新求一遍长度为4的绳子的最优解
4. 之所以循环中没有考虑不剪的情况，同样是因为至少要剪一刀的要求。再说，其实就算没这要求，长度大于4后，剪下去的各子段乘积的最大值肯定不会比不剪小
5. 循环中j限定条件为j<=i/2是因为比如当i=5时，2x3和3x2结果是一样的，如此限定能减少循环次数
6. 综上所述，当输入长度后，会在循环中从小到大计算最优解，后面的循环可能会用到前面的循环计算出来的一些最优解，避免了重复计算

## LeetCode最长回文子串

[最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

### 解法一（动态规划）

```javascript
时间复杂度O（n^2）,空间复杂度O（n^2）,如果用f[i][j] 保存子串从i 到j是否是回文子串，那么在求f[i][j] 的时候如果j-i>=2时，如果 f[i][j] 为回文，那么f[i+1][j-1],也一定为回文。否则f[i][j]不为回文。如下图：
```



![这里写图片描述](https://img-blog.csdn.net/20170425171617065?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

因此可得到动态规划的递归方程：

![这里写图片描述](https://img-blog.csdn.net/20170425171935522?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 具体代码

```javascript
状态定义
dp[i,j]：字符串s从索引i到j的子串是否是回文串
true： s[i,j] 是回文串
false：s[i,j] 不是回文串
转移方程
dp[i][j] = dp[i+1][j-1] && s[i] == s[j]
s[i] == s[j]：说明当前中心可以继续扩张，进而有可能扩大回文串的长度
dp[i+1][j-1]：true
说明s[i,j]的**子串s[i+1][j-1]**也是回文串
说明，i是从最大值开始遍历的，j是从最小值开始遍历的
特殊情况
j - i < 2：意即子串是一个长度为0或1的回文串
总结
dp[i][j] = s[i] == s[j] && ( dp[i+1][j-1] || j - i < 2)
```



```javascript
/**
 * @param {string} s
 * @return {string}
 */
var longestPalindrome = function(s) {
    let n = s.length;
    let res = '';
    let dp = Array.from(new Array(n),() => new Array(n).fill(0));
    for(let i = n-1;i >= 0;i--){
        for(let j = i;j < n;j++){
            dp[i][j] = s[i] == s[j] && (j - i < 2 || dp[i+1][j-1]);
            if(dp[i][j] && j - i +1 > res.length){
                res = s.substring(i,j+1);
            }
        }
    }
    return res;
};
```



### 解法二：中心扩展法

**思路**
回文串一定是对称的
每次选择一个中心，进行中心向两边扩展比较左右字符是否相等
中心点的选取有两种
aba，中心点是b
aa，中心点是两个a之间
所以共有两种组合可能
left：i，right：i
left：i，right：i+1
图解

![截屏2019-12-06上午7.54.28.png](https://pic.leetcode-cn.com/533a8538f42e6a6495b87d0c054224fdaa2e6da1cd9158f3e9042894137961fc-%E6%88%AA%E5%B1%8F2019-12-06%E4%B8%8A%E5%8D%887.54.28.png)

```javascript
 * @param {string} s
 * @return {string}
   */
   var longestPalindrome = function(s) {
   if(!s || s.length < 2){
       return s;
   }
   let start = 0,end = 0;
   let n = s.length;
   // 中心扩展法
   let centerExpend = (left,right) => {
       while(left >= 0 && right < n && s[left] == s[right]){
           left--;
           right++;
       }
       return right - left - 1;
   }
   for(let i = 0;i < n;i++){
       //针对第一种中心点情况
       let len1 = centerExpend(i,i);
       //针对第二种中心点情况
       let len2 = centerExpend(i,i+1);
       // 两种组合取最大回文串的长度
       let maxLen = Math.max(len1,len2);
       if(maxLen > end - start){
           // 更新最大回文串的首尾字符索引
           start = i - ((maxLen - 1) >> 1);
           end = i + (maxLen >> 1);
       }
   }
   return s.substring(start,end+1);
   };


```





