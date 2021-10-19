# Easy

*summary*

**Easy**是简单题，这里涵盖一些基本的操作和容易粗心的小错误

---

*pinned*

### 常见技巧

- C++ string与int互转

  ```cpp
  to_string(1);
  //-------------
  s="1"
  atoi(s.c_str());
  ```

- 句子中按空格分割单词时，可以预先在string最后加一个空格，这样可以避免最后一个单词因为没有空格在后面而没有处理。

- 复杂类型数组自定义排序（使用sort+匿名函数）：

  ```cpp
  class Solution {
  public:
      vector<string> reorderLogFiles(vector<string>& logs) {
          auto func = [](const string& str)->string
          {
              auto pos = str.find(' ');
              if(isalpha(str.at(pos + 1))) 
                  return "0" + str.substr(pos) + str.substr(0, pos);
              return "1";
          };
  
          auto cmp = [=](const string& str1, const string& str2)
          { 
              return func(str1) < func(str2);
          };
  
          stable_sort(logs.begin(), logs.end(), cmp);
          return logs;
      }
  };
  
  作者：you-yuan-de-cang-qiong
  链接：https://leetcode-cn.com/problems/reorder-data-in-log-files/solution/c-stable_sort-by-you-yuan-de-cang-qiong-ujob/
  来源：力扣（LeetCode）
  著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
  ```

  

  ```cpp
  //先按second升序，再按first降序
  vector<pair<int, int>> temp;
  sort(temp.begin(), temp.end(), [](const auto &a, const auto &b)
       {
           if (a.second < b.second)
               return true;
           else if (a.second == b.second)
               return a.first > b.first;
           else return false;
       });
  ```

- 环形字符串找子串：将环形字符串写两遍，`HISabT => HISabTHISabT`，`THIS`就出现了

- A和B看作一个整体

  - [884. 两句话中的不常见单词](https://leetcode-cn.com/problems/uncommon-words-from-two-sentences/)：在一句话中出现一次，另一句话中不出现 => 两句话中恰好出现一次
  - [1768. 交替合并字符串](https://leetcode-cn.com/problems/merge-strings-alternately/)：交替打印两个字符串中的字符 => 一次循环中，打印A字符串一次+打印B字符串一次

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

---

*2021.07.27*

### 约瑟夫环

> [剑指 Offer 62. 圆圈中最后剩下的数字 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)

- 40个人排成一个圈，分别编号0~39。现在每数5个号淘汰一个人，问哪个人会活到最后？

- 简单的正向思维进行模拟

  - 维护一个40长度的出局者数组，每次确定淘汰者时，必须确认他是否已经出局，向后直到找到未出局者。最后循环39次淘汰过程，可以知道唯一存活者的编号。

- 逆向思维，从终局倒推

  - 我们假设每一轮的开始都需要重新编号。比如0 1 2 3中淘汰了1，后面的人就会剩下2 3 0，此时把2/3/0会重新编成0/1/2号。

  - 然后定义当前人数为$N$​​​​​​​，数的间隔为$5$​​​​​​​​，活到最后的那个人此时所在的编号为$f(N,5)$​

  - 剩下一个人的时候，唯一的0号玩家就是答案。$f(1,5)=0$​​

    | 0    |
    | ---- |
    | 👑    |

  - 剩下两个人的时候，他在哪里？此时淘汰了0号。所以他是站在1号位的。$f(2,5)=1$​​​

    | 0    | 1    |
    | ---- | ---- |
    | ❌    | 👑    |

  - 剩下三个人的时候，我们知道淘汰了1号，那他在哪里？其实由两个人的序列知道，他需要站在此时淘汰者的后两位。$f(3,5)=0$​​​

    | 0    | 1    | 2    |
    | ---- | ---- | ---- |
    | 👑    | ❌    |      |

  - 剩下四个人的时候，我们知道淘汰了0号，由三个人的序列知道，他需要站在此时淘汰者的后一位。$f(4,5)=1$​

    | 0    | 1    | 2    | 3    |
    | ---- | ---- | ---- | ---- |
    | ❌    | 👑    |      |      |

  - 剩下五个人的时候。$f(5,5)=1$​​

    | 0    | 1    | 2    | 3    | 4    |
    | ---- | ---- | ---- | ---- | ---- |
    |      | 👑    |      |      | ❌    |

  - 此时就会发现，我们每次推理存活者编号的步骤是：

    - 已知$f(n-1,m)$

    - 计算$out(n,m)=(m-1)\bmod n$​

    - $f(n-1,m)$​**代表了存活者站在$out(n,m)$​后面的第$f(n-1,m)+1$​​​位**。但此时有可能溢出数组，所以仍然记得对数组长度取余

    - 得出最终递推公式：
      $$
      f(n,m)=((m-1)\bmod n+f(n-1,m)+1)\bmod n \\
      =((m-1)+f(n-1,m)+1) \bmod n\\
      =(m+f(n-1,m)) \bmod n
      $$
      

---

*2021.07.31*

### 分治法

> [1763. 最长的美好子字符串](https://leetcode-cn.com/problems/longest-nice-substring/)
>
> [53. 最大子序和 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/maximum-subarray/)

