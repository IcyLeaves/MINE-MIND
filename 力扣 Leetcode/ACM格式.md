# ACM格式

*summary*

当参与ACM比赛或者机试时，需要额外注意在算法模板之外还有很多事等待我们去处理。

---

*2022.03.04*

### 头文件

> [C++ Standard Library headers - cppreference.com](https://en.cppreference.com/w/cpp/header)

```cpp
#include <vector>
#include <iostream>
#include <sstream> 
#include <map>
#include <queue>
#include <cstdlib>
#include <string>
#include <algorithm> 
#include <set>
#include <cmath>
#include <list>
#include <stack>
#include <unordered_set>
#include <unordered_map>
#include <iomanip>
using namespace std;
#define LL long long
```

#### STL数据结构

- 能够使用动态数组`vector`，哈希表`map`，队列`queue`和优先队列`priority_queue`，集合`set`，链表`list`，栈`stack`，非排序集合`unordered_set`，非排序哈希表`unordered_map`

#### iostream

- `cin`：标准的输入流
- `cout`：标准的输出流

#### iomanip

- 输出流操纵算子：如`fixed`，`setprecision()`

#### sstream

- `stringstream`：字符串输入流

#### cstdlib

- 有`size_t`和`#define NULL nullptr`的声明
- （算法题中已罕见）`malloc()`和`free()`：对一段空间的分配和对指针指向空间的释放
- `atoi/atol()`：ASCII转int/long，参数是`char*`。
- `rand()`：随机返回一个`[0,RAND_MAX]`之间的int
- （已被sort取代）`qsort()`：对指针指向的空间排序

#### string

- 能够使用字符串`string`
- `stoi/stol()`：string转int/long，参数是`string`
- `to_string()`：数字转string，参数是各种类型的数字
- `getline()`：可以从`cin/istringstream`等输入流（第一个参数）获取一整行并转化为字符串（第二个参数）

#### algorithm

- 以下均为函数

- `find`

- `swap`
- `reverse`
- `sort`
- `nth_element`
- `lower_bound`
- `upper_bound`
- `min/max`
- `min_element/max_element`

#### cmath

- 基本都是数学函数

### 输入

> [ACM标准输入输出C/C++版_Carey_Lu的博客-CSDN博客_acm c++输入输出](https://blog.csdn.net/Carey_Lu/article/details/80199631)

以下示例都是摘自洛谷ACM原题

#### 输入固定个数

> [P1001 A+B Problem - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/P1001)

**格式要求：**

> 两个以空格分开的整数。

**格式样例：**

```txt
40 50
```

```cpp
int main() 
{
     int a,b; 
     cin >> a >> b;
     return 0; 
}
```

#### 先输入个数，再按个数输入

> [P2637 第一次，第二次，成交！ - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/P2637)

**格式要求：**

> 第一行：两个被空格隔开的整数，`n`和`m`。
>
> 第二行到第`m+1`行：第`i+1`行只包含一个整数：`p_i`。

**格式样例：**

```txt
5 4
2
8
10
7
```

```cpp
int main()
{
	int m, n;
	cin >> n >> m;
	for(int i=0;i<m;i++)
	{
		int pi;
		cin >> pi;
	}
    return 0;
}
```

#### 字符串输入

> [P1597 语句解析 - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/P1597)

**格式要求：**

> 一串(<255)PASCAL语言，只有a,b,c 3个变量，而且只有赋值语句，赋值只能是一个一位的数字或一个变量，未赋值的变量值为0。

**格式样例：**

```txt
a:=3;b:=4;c:=5;
```

```cpp
int main()
{
	//看作是多组数据，每组数据有五个字符
	//当读到整行输入结束时，即可退出while
	char ch;
	while (cin >> ch) {
		char a, b, c, d;
		cin >> a >> b >> c >> d;
	}
	return 0;
}
```

> cin会以空格分隔每一次的输入，因此上面的方式不会读入空格，但下面的方式就会按空格分割字符串

```cpp
int main()
{
    //对于输入"abcde 12345"来说，s只会读到"abcde"
	string s;
	cin >> s;
	return 0;
}
```

> 如果想整行读入，可以使用getline

```cpp
int main()
{
    //对于输入"abcde 12345"来说，s可以读到"abcde 12345"
	string s;
	getline(cin, s);//注意：在while中要用s!=""判结束
	return 0;
}
```

### 输出

#### 四舍五入保留小数后x位

> [B2070 计算分数加减表达式的值 - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/B2070)

**格式要求：**

> 输出为一行，为 S_n的值，结果保留小数点后4位小数。

**格式样例：**

```txt
0.5000
```

> [C++ cout格式化输出（输出格式）完全攻略 (biancheng.net)](http://c.biancheng.net/view/275.html)

```cpp
int main()
{
	cout << fixed << setprecision(4) << 1948.458973 << endl;//1948.4590
	return 0;
}
```

>  四舍五入保留有效数字x位

```cpp
int main()
{
	cout << setprecision(6) << 1948.458973 << endl;//1948.46
	return 0;
}
```

