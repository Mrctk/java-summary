# LeetCode第7题 整数反转(Reverse Integer)

[toc]



## 题目地址

题目地址[https://leetcode-cn.com/problems/reverse-integer/](https://leetcode-cn.com/problems/reverse-integer/)



## 题目描述

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

**示例 1**:

```java
输入: 123
输出: 321
```

 **示例 2**:

```java
输入: -123
输出: -321
```

**示例 3**:

```java
输入: 120
输出: 21
```

**注意**:

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−2^31,  2^31 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。



## 代码实现

答案出自[https://leetcode-cn.com/problems/reverse-integer/solution/hua-jie-suan-fa-7-zheng-shu-fan-zhuan-by-guanpengc/](https://leetcode-cn.com/problems/reverse-integer/solution/hua-jie-suan-fa-7-zheng-shu-fan-zhuan-by-guanpengc/)

**思路**

- 标签：数学

- 本题如果不考虑溢出问题，是非常简单的。解决溢出问题有两个思路，第一个思路是通过字符串转换加try catch的方式来解决，第二个思路就是通过数学计算来解决。
- 由于字符串转换的效率较低且使用较多库函数，所以解题方案不考虑该方法，而是通过数学计算来解决。
  通过循环将数字x的每一位拆开，在计算新值时每一步都判断是否溢出。
- 溢出条件有两个，一个是大于整数最大值MAX_VALUE，另一个是小于整数最小值MIN_VALUE，设当前计算结果为ans，下一位为pop。
- 从ans * 10 + pop > MAX_VALUE这个溢出条件来看
  - 当出现 ans > MAX_VALUE / 10 且 还有pop需要添加 时，则一定溢出
  - 当出现 ans == MAX_VALUE / 10 且 pop > 7 时，则一定溢出，7是2^31 - 1的个位数

- 从ans * 10 + pop < MIN_VALUE这个溢出条件来看
  - 当出现 ans < MIN_VALUE / 10 且 还有pop需要添加 时，则一定溢出
  - 当出现 ans == MIN_VALUE / 10 且 pop < -8 时，则一定溢出，8是-2^31的个位数



**代码**

Java

```java
class Solution {
    public int reverse(int x) {
        int ans = 0;
        while (x != 0) {
            int pop = x % 10;
            if (ans > Integer.MAX_VALUE / 10 || (ans == Integer.MAX_VALUE / 10 && pop > 7)) 
                return 0;
            if (ans < Integer.MIN_VALUE / 10 || (ans == Integer.MIN_VALUE / 10 && pop < -8)) 
                return 0;
            ans = ans * 10 + pop;
            x /= 10;
        }
        return ans;
    }
}
```



**复杂度分析**

时间复杂度：O(log(x))，x 中大约有 log 10(x) 位数字。
空间复杂度：O(1)。

