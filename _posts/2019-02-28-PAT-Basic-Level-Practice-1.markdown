---
layout:     post
title:      "PAT Basic Level Practice (I)"
subtitle:   "PAT Basic Level Practice (I)"
date:       2019-02-28
author:     "Thistledown"
header-img: "img/posts/post-bg-js-study-notes.jpg"
catalog: true
tags:
    - PAT
    - C++
    - 算法
---

> PAT Basic Level 刷题（一）   
> [PTA | 程序设计类实验辅助教学平台](https://pintia.cn/problem-sets/994805260223102976/problems)

## [PAT B 1001](https://pintia.cn/problem-sets/994805260223102976/problems/994805325918486528)

![PAT_B_1001](https://s2.ax1x.com/2019/02/28/kTzBOf.png)

**解题思路**

题目比较基础，利用循环语句和条件语句实现对目标整数的不断缩小，直至 `n == 1`，通过变量 `step` 来计算步数（砍了多少次）

**参考代码**

```c++
#include <stdio.h>
int main(){
    int n, step = 0;
    scanf("%d", &n);  // 输入题目给出的 n
    while (n != 1) {
        if (n % 2 == 0) n /= 2;
        else n = (3 * n + 1) / 2;
        step++;
    }
    printf("%d\n", step);
    return 0;
}
```

## [PAT B 1002](https://pintia.cn/problem-sets/994805260223102976/problems/994805324509200384)

![PAT_B_1002](https://s2.ax1x.com/2019/02/27/kTpVYj.png)

**解题思路**  
1. 题目要求读入一个小于 10e100 的整数 （如，1234567890987654321123456789），它已经超出了 `int` (-2^31 ~ 2^31 - 1) 或者 `long int` (-2^63 ~ 2^63 - 1) 的范围，因此需要将其当做字符串读入。
2. 将读入字符串的每一位“相加”即可得到 `n` 的各位数字之和 `sum`
3. 用 `/` 和 `%` 方法获取 `sum` 的每一位

**参考代码**  
```c++
#include <stdio.h>

int main() {
    char n;
    const char *pinyin[] = { "ling", "yi", "er", "san", "si", "wu", "liu", "qi", "ba", "jiu" };

    int sum = 0;
    while ((n = getchar()) != '\n') {
        sum += n - '0';
    }

    // printf("%d\n", sum);

    if (sum / 100)  // 根据题目条件，sum 不会超过 1000                      
        printf("%s ", pinyin[sum / 100]);
    if (sum / 10)                            
        printf("%s ", pinyin[sum / 10 % 10]);
    printf("%s\n", pinyin[sum % 10]);        

    return 0;
}
```


## [PAT B 1022](https://pintia.cn/problem-sets/994805260223102976/problems/994805299301433344)

![PAT_B_1022](https://s2.ax1x.com/2019/02/28/kTzyTg.png)

**解题思路**

十进制 a （整数）转换为 D 进制 (1 < D <= 10) 数 b，采用“除基取余法”：将 a 除以 D，然后将所得的余数作为低位存储；而商则继续除以 D 并重复之前的操作，最后当商为 0 时，将所有位从高到低输出就可以得到 b。

示例，将十进制数 11 转换为二进制数：
- 11 除以 2，得商为 5，余数为 1；
- 5 除以 2，得商为 2，余数为 1；
- 2 除以 2，得商为 1， 余数为 0；
- 1 除以 2，得商为 0，余数为 1；
- 商为 0，终止。将余数从后向前输出，得到 1011，即为 11 的二进制数。

**参考代码**

```c++
#include <stdio.h>

int main() {
    int A, B, D;
    scanf("%d%d%d", &A, &B, &D);
    int sum = A + B;
    int ans[31], num = 0;
    do {
    	ans[num++] = sum % D;	// ans[num] 存放 D 进制数的第 num 位
    	sum /= D;
    } while (sum != 0);
    for (int i = num - 1; i >= 0; i--) {
    	printf("%d", ans[i]);   // 将所有位从高到低输出
    }

    return 0;
```

## [PAT B 1032](https://pintia.cn/problem-sets/994805260223102976/problems/994805299301433344)

![PAT_B_1032](https://s2.ax1x.com/2019/02/28/kTzvX6.png)

**解题思路**

将每一回输入分数加在数组对应索引（学校编号）的元素值上，最后通过比较数组内部元素大小判断总得分最高的学校。

**参考代码**

```c++
#include <stdio.h>

const int maxN = 100001;
int school[maxN] = { 0 };

int main() {
    int n, schID, score;	// n 为学校数目，schID 为学校编号，score 为学校分数
    scanf("%d", &n);
    for (int i = 0; i < n; i++) {
        scanf("%d%d", &schID, &score);
        school[schID] += score;
    }
    int k = 1, MAX = -1;	// 总分最高的学校 ID 及其分数
    for (int i = 0; i <= n; i++) {
        if (school[i] > MAX) {
            MAX = school[i];
            k = i;
        }
    }
    printf("%d %d", k, MAX);

    return 0;
}
```

## [PAT B 1036](https://pintia.cn/problem-sets/994805260223102976/problems/994805285812551680)

![PAT_B_1036](https://s2.ax1x.com/2019/02/28/k7SA1I.png)

**解题思路**

1. 根据题目要求，对行数进行四舍五入：`row = (col % 2 == 0) ? (col / 2) : ((col + 1) / 2)`
2. 首尾两行输出 `col` 个字符
3. 其他行输出格式为：`字符 + 空格 * (col - 2) + 字符`

**参考代码**

```c++
#include <stdio.h>

int main() {
    int col, row;
    char str;
    scanf("%d %c", &col, &str);
    row = (col % 2 == 0) ? (col / 2) : ((col + 1) / 2);
    for (int i = 1; i <= row; i++) {
        if (i == 1 || i == row) {
            for (int j = 0; j < col; j++) {
                printf("%c", str);
            }
            printf("\n");
        }
        else {
            printf("%c", str);
            for (int j = 1; j < col - 1; j++) {
                printf(" ");
            }
            printf("%c\n", str);
        }
    }
    return 0;
}
```

> 未完待续。。。

<!--

## [PAT B ]()

![PAT_B_]()

**解题思路**

...

**参考代码**

```c++

```

-->
