# Easy

*summary*

**Easy**是简单题，这里涵盖一些基本的操作和容易粗心的小错误

---

*pinned*

### 常见技巧

- 环形字符串找子串：将环形字符串写两遍，`HISabT => HISabTHISabT`，`THIS`就出现了

- A和B看作一个整体

  - [884. 两句话中的不常见单词](https://leetcode-cn.com/problems/uncommon-words-from-two-sentences/)：在一句话中出现一次，另一句话中不出现 => 两句话中恰好出现一次

- 进制转换别忘记0。`if(num==0) return "0";`

- 算术溢出格式转换：

  ```cpp
  int num = 1;
  long long res = num - 1;
  ```

  - 此时num先和1在4字节上计算减法，然后转为8字节，有溢出风险。应该提前转化成高字节减法，方法是把所有操作数转为高字节

    ```cpp
    int num = 1;
    long long res = (long long)num - 1;
    ```
  

---

*2021.07.16*

### 989.数组形式的整数加法

> [989. 数组形式的整数加法 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/add-to-array-form-of-integer/)

粗心点

- 逢10进位：判断条件应该是`>=10`而不是`>10`
- 想提前结束节省步骤：加数`k`归零不代表进位结束，比如`[9999]+1`。
- 情况没考虑全：之前只想到扩充**一次**数组（留给进位），但可能出现**多次**扩充数组的情况，比如`[0]+23`

---

*2021.07.19*

### Boyer-Moore 投票算法

> [面试题 17.10. 主要元素 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/find-majority-element-lcci/)

- 寻找数组中个数超过半数的元素
  - 关键点：他的个数超过了其他所有元素个数之和。因此可以想到即使其他所有元素联合起来，也打不过这个主要元素。
- 时间复杂度：$O(N)$。空间复杂度：$O(1)$。
- 把每个元素看作一个帮派成员，战斗机制是：
  - 如果当前元素和当前帮派相同，那么加入帮派，使其人数+1。
  - 如果当前元素和当前帮派不同，那么会消灭一个帮派成员，使其人数-1。
    - 如果帮派被消灭了，当前的挑战者成立新的帮派，人数为1。
- 活到最后的帮派要再经历一次考验，重新扫描数组检查是否真的是主要元素。

![image-20210719185819770](Easy.assets/2222.gif)

---

*2021.07.20*

### 最大公约数(GCD)

> [C++求最大公约数 - 世子抱瑜玉 - 博客园 (cnblogs.com)](https://www.cnblogs.com/ziyuwang/p/10391037.html)
>
> [求最大公约数的4种算法（C++）_Elf.筱焱的博客-CSDN博客_最大公约数算法](https://blog.csdn.net/qq_42302831/article/details/88587052)

- 辗转相除法：
  - 先确定大数和小数
  - 如果此时大数已经除尽了小数，那么小数就是最大公约数
  - 否则将**小数**和**大数除以小数的余数（一定小于小数）**继续递归

---

*2021.07.21*

### 二分查找

> [面试题 10.05. 稀疏数组搜索 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/sparse-array-search-lcci/)
>
> [69. x 的平方根 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/sqrtx/)
>
> [1385. 两个数组间的距离值 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/find-the-distance-value-between-two-arrays/)
>
> [34. 在排序数组中查找元素的第一个和最后一个位置 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

- 二分查找要求一个有序数组，这个数组可能隐藏在题目中，比如**1~n的自然数**

- 二分查找的mid防溢出：

  ```cpp
  int mid = (low + high) / 2;
  ```

  - 如果low和high都很大，就直接溢出了。但实际上mid一定是在low和high中间，所以从逻辑上是没有溢出的，更好的写法是：

    ```cpp
    int mid = low + (high - low) / 2
    ```

#### 稀疏值

#### 重复数字

#### 邻近的左右元素
