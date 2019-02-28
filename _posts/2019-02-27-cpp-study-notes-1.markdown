---
layout:     post
title:      "C++ 学习笔记（一）：初学者的小窍门"
subtitle:   "C++ Study Notes (1) : Tips for beginner"
date:       2019-02-27
author:     "Thistledown"
header-img: "img/posts/post-bg-cpp.png"
catalog: true
tags:
    - C++
    - C++ 学习笔记
---

> 一个多月依赖，确定好了考研的计划。现在开始准备刷题学习一些算法的基础，也是从头开始学习C/C++，当然在学习过程中踩了不少坑，在这里进行一些总结。

## C++ 数据类型及其表示范围
这一部分是基础，不过我之前都没有系统性的搞清楚，所以现在还是先把这些知识点列出来，该部分参考[C++ 数据类型 - 菜鸟教程](http://www.runoob.com/cplusplus/cpp-data-types.html)。
#### 1. 基本的内置类型
C++ 为程序员提供了种类丰富的内置数据类型和用户自定义的数据类型。下表列出了七种基本的 C++ 数据类型：

|   类型   | 关键字  |
| :------: | :-----: |
|  布尔型  |  bool   |
|  字符型  |  char   |
|   整型   |   int   |
|  浮点型  |  float  |
| 双浮点型 | double  |
|  无类型  |  void   |
| 宽字符型 | wchar_t |

其中，wchar_t 实际上是：
```c++
typedef wchar_t short int;
```
所以 wchar_t 实际上的空间是和 short int 一样。
一些基本类型可以使用一个或多个类型修饰符进行修饰：
- signed
- unsigned
- short
- long

#### 2. 各种变量类型的大小

|      数据类型      | 字节数 |         表示范围         |
| :----------------: | :----: | :----------------------: |
|        char        |   1    | -128 ~ 127 \|\| 0 ~ 255  |
|   unsigned char    |   1    |         0 ~ 255          |
|    signed char     |   1    |        -128 ~ 127        |
|        int         |   4    | -2147483648 ~ 2147483647 |
|    unsigned int    |   4    |      0 ~ 4294967295      |
|     signed int     |   4    | -2147483648 ~ 2147483647 |
|     short int      |   2    |      -32768 ~ 32767      |
| unsigned short int |   2    |        0 ~ 65535         |
|  signed short int  |   2    |      -32768 ~ 32767      |
|      long int      |   8    |     -2^63 ~ 2^63 - 1     |
| unsigned long int  |   8    |       0 ~ 2^64 - 1       |
|  signed long int   |   8    |     -2^63 ~ 2^63 - 1     |
|       float        |   4    |  -3.40e+38 ~ +3.40e+38   |
|       double       |   8    | -1.79e+308 ~ +1.79e+308  |

注：不同系统可能会有所差异。

## 浮点数的比较
> 在学习[《算法笔记》](https://book.douban.com/subject/26827295/)一书时，作者介绍了“极小数”用以修正浮点数在计算机中存储与计算产生的误差。这一点是我在之前学习中所没有意识到的，于是在此归纳一下相关的内容。

由于计算机中采用有限位的二进制编码，因此浮点数在计算机中的存储并不总是精确的。例如在经过**大量运算**后，一个浮点型的数 3.14 在计算机中就可能存储成 3.1400000000001，也有可能存储成 3.1399999999999，这种情况下会对**比较操作**带来极大的干扰（因为C/C++中的 `==` 操作是完全相同才能判定为 `true`）。于是在文中，作者引入了一个极小数 `eps` 来对这种误差进行修正。

经验表明，eps 取 1e-8（10的-8次方） 在大多数情况下既不会漏判，也不会误判。因此可将 eps 定义为常量 1e-8：

```c++
const double eps = 1e-8;
```

#### 1. 等于运算符 `==`
下图为*等于区间示意图*：

![等于区间示意图](https://s2.ax1x.com/2019/02/27/koITfg.png)

如图所示，如果一个数 a 落在了 [b - eps, b + eps] 的区间中，就判断 `a == b` 成立。为了使**比较**更为方便，把比较操作写成宏定义的形式：
```c++
#include <math.h>
#define Equ(a, b) ((fabs((a) - (b))) - (eps)) // 括号是为防止宏定义可能带来的错误
```


#### 2. 大于运算符 `>`
下图为*大于区间示意图*：

![大于区间示意图](https://s2.ax1x.com/2019/02/27/koIzkT.png)

如图所示，如果一个数 a 要大于 b，那么就必须在误差 eps 扰动范围之外大于 b，因此只有大于 b + eps 的数才能判定为大于 b（也即 a - b > eps）。
```c++
#define More(a, b) (((a) - (b)) > (eps))
```

#### 3. 小于运算符 `<`
下图为*小于区间示意图*：

![小于区间示意图](https://s2.ax1x.com/2019/02/27/kooCp4.png)

如图所示，如果一个数 a 要小于 b，那么就必须在误差 eps 扰动范围之外小于 b，因此只有小于 b - eps 的数才能判定为小于 b（也即 a - b < -eps）。
```c++
#define Less(a, b) (((a) - (b)) < (-eps))
```

#### 4. 大于等于运算符 `>=`
下图为*大于等于区间示意图*：

![大于等于区间示意图](https://s2.ax1x.com/2019/02/27/kooGHP.png)

如图所示，由于大于等于运算符可以理解为大于运算符和等于运算符的结合，于是需要让一个数 a 在误差扰动范围内大于或者等于 b，因此大于 b - eps 的数都应当判定为大于等于 b（也即 a - b > -eps）。
```c++
#define MoreEqu(a, b) (((a) - (b)) > (-eps))
```

#### 5. 小于等于运算符 `<=`
下图为*小于等于区间示意图*：

![小于等于区间示意图](https://s2.ax1x.com/2019/02/27/kood3Q.png)

如图所示，由于小于等于运算符可以理解为小于运算符和等于运算符的结合，于是需要让一个数 a 在误差扰动范围内小于或者等于 b，因此小于 b + eps 的数都应当判定为小于等于 b（也即 a - b < eps）。
```c++
#define LessEqu(a, b) (((a) - (b)) < (eps))
```

#### 6. 圆周率 `π`
圆周率 π 可由 arccos(-1) 计算得到：
```c++
#include <math.h>
const double PI = acos(-1.0);
```

汇总结果为如下代码：
```c++
#include <math.h>
const double eps = 1e-8;
const double PI = acos(-1.0);

#define Equ(a, b) ((fabs((a) - (b))) - (eps))
#define More(a, b) (((a) - (b)) > (eps))
#define Less(a, b) (((a) - (b)) < (-eps))
#define MoreEqu(a, b) (((a) - (b)) > (-eps))
#define LessEqu(a, b) (((a) - (b)) < (eps))
```

## 读取“大数”
有的时候，我需要读入一个很大的**整数**（例如，1234567890987654321123456789），并计算出它的各位数字之和。用 `int` (-2^31 ~ 2^31 - 1) 或者 `long int` (-2^63 ~ 2^63 - 1) 都不满足要求。这时可以将这样一个“大数”作为字符串（或是字符数组）读入。

> 字符串实际上是使用 null 字符 `'\0'` 终止的**一维字符数组**。因此，一个以 null 结尾的字符串，包含了组成字符串的字符。

我们可以使用 `getchar()` 函数，将该数字的每一位作为字符读入到我们的字符串数组中。

> C 库函数 int getchar(void) 从标准输入 stdin 获取一个字符（一个无符号字符）。这等同于 getc 带有 stdin 作为参数。

用程序可以简单地表示为：
```c++
#include <stdio.h>

int main() {
    char n[100];
    int i = 0;
    printf("Input:  ");
    while ((n[i] = getchar()) != '\n') {    // 逐位读入数字
        i++;
    }

    printf("Output: ");
    for (int k = 0; k < i; k++) {
        printf("%c", n[k]);     // 逐位输出数字
    }

    printf("\n\n");
    return 0;
}
```
当我们输入数字 1234567890987654321123456789，也会得到相应的输出：

![I&O](https://s2.ax1x.com/2019/02/28/k7pVPJ.png)

此时，存储字符的数组 `n` 为：

![Array_n](https://s2.ax1x.com/2019/02/28/k7pFVU.png)

