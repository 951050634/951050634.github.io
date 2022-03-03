---
layout:     post
title:      AtCoder Beginner Contest 240
subtitle:   题解报告
date:       2022-03-03
author:     951050634
header-img: img-post/2022-03-03-AtCoder Beginner Contest 240/header.png
catalog: false
tags:
    - 比赛
---

# **AtCoder Beginner Contest 240**

## **A - Edge Checker**

手动模拟一遍就好

```cpp
/*
 * @Author: 951050634 
 * @Date: 2022-02-21 08:28:32 
 * @Last Modified by:   951050634 
 * @Last Modified time: 2022-02-21 08:28:32 
 */
#include <bits/stdc++.h>
using namespace std;

inline int read() {
    int x, f = 1;
    char c;
    while (!((c = getchar()) >= '0' && c <= '9')) if (c == '-') f = -1;
    x = c - '0';
    while ((c = getchar()) >= '0' && c <= '9') (x *= 10) += c - '0';
    return x * f;
}

int main() { 
    int a = read(), b = read();
    if (b == a + 1 || a == b + 1)
        cout << "Yes" << endl;
    else if (a == 10 && b == 1)
        cout << "Yes" << endl;
    else if (a == 1 && b == 10)
        cout << "Yes" << endl;
    else
        cout << "No" << endl;
    return 0;
}
```

## B - Count Distinct Integers

离散化的最简单的板子

```cpp
/*
 * @Author: 951050634 
 * @Date: 2022-02-21 08:30:05 
 * @Last Modified by:   951050634 
 * @Last Modified time: 2022-02-21 08:30:05 
 */
#include <bits/stdc++.h>
using namespace std;
const int N = 1e4 + 10;
int n;
int a[N];

inline int read() {
    int x, f = 1;
    char c;
    while (!((c = getchar()) >= '0' && c <= '9')) if (c == '-') f = -1;
    x = c - '0';
    while ((c = getchar()) >= '0' && c <= '9') (x *= 10) += c - '0';
    return x * f;
}

int main() {
    n = read();
    for (int i = 1; i <= n; i++)
        a[i] = read();
    sort(a + 1, a + 1 + n);
    int sum = unique(a + 1, a + n + 1) - a - 1;
    cout << sum << endl;
    return 0;
}
```

## C - Jumping Takahashi

可以多种方法实现本题

1.考虑每次只选 $a[i]$ 或 $b[i]$ ，可以构成一个完全二叉树，然后dfs即可

预期时间复杂度 $O(2^n)$ ，考虑 $n_{max}=100$ 会爆。

```cpp
/*
 * @Author: 951050634 
 * @Date: 2022-02-21 08:40:53 
 * @Last Modified by:   951050634 
 * @Last Modified time: 2022-02-21 08:40:53 
 */
#include <bits/stdc++.h>
using namespace std;
const int N = 105;
int n, e;
int a[N], b[N];
bool f;

inline int read() {
    int x, f = 1;
    char c;
    while (!((c = getchar()) >= '0' && c <= '9')) if (c == '-') f = -1;
    x = c - '0';
    while ((c = getchar()) >= '0' && c <= '9') (x *= 10) += c - '0';
    return x * f;
}

inline void dfs(int ans, int i) {
    if (f)
        return;
    if (ans == e && i == n + 1) {
        f = 1;
        return;
    }
    if (i > n || ans > e)
        return;
    dfs(ans + a[i], i + 1);
    dfs(ans + b[i], i + 1);
}

int main() {
    n = read(), e = read();
    for (int i = 1; i <= n; i++)
        a[i] = read(), b[i] = read();
    dfs(0, 0);
    if (f)
        cout << "Yes" << endl;
    else
        cout << "No" << endl;
    return 0;
}
```

2.模拟 实际上感觉也很像搜索，将记录每次操作的结果 $\rightarrow$ 记录每次操作后在 $ans\in[0,10000]$ 上的结果

预期时间复杂度 $O(10000n)$ ，大概不会爆 ~~qwq~~

```cpp
/*
 * @Author: 951050634 
 * @Date: 2022-02-21 08:40:53 
 * @Last Modified by:   951050634 
 * @Last Modified time: 2022-02-21 08:40:53 
 */
#include<bits/stdc++.h>
using namespace std;
int vis[10200];
int n, x;

inline int read() {
    int x, f = 1;
    char c;
    while (!((c = getchar()) >= '0' && c <= '9')) if (c == '-') f = -1;
    x = c - '0';
    while ((c = getchar()) >= '0' && c <= '9') (x *= 10) += c - '0';
    return x * f;
}

int main() {
    n = read(), x = read();
    vis[0] = 1;
    for (int i = 1; i <= n; i++) {
        int a = read(), b = read();
        for (int j = 10000; j >= 0; j--) {
            if (vis[j] == 1) {
                vis[j + a] = 1;
                vis[j + b] = 1;
                vis[j] = 0;
            }
        }
    }
    if (vis[x]) cout << "Yes" << endl;
    else cout << "No" << endl;
    return 0;
}
```

## D - Strange Balls

手动模拟一下可以发现类似于栈，可以用 **STL** 库中的 **stack** 来解决

可以复习一下stack的用法

**插 查 删 改**

> 1. st.pop() 弹出栈顶元素
>
> 2. st.top() 栈顶元素
> 3. st.push() 将某一元素入栈

将每一个球的信息记录为 $(a,b)$ ，其中 $a$ 表示当前小球的数字， $b$ 表示栈顶有多少连续的数字

这一目的可以通过开一对 $pair$ 来实现

如果 $a=b$ 则删除这个小球

如果 $a\ne b$ 则将 $(a,b)$ 改为 $(a,b+1)$ ，并且要删除 $(a,b)$ 这个小球，不然会出错

最后简单统计一下小球的 $b$ 的和，输出即可

预期时间复杂度 $O(n)$ 

```cpp
/*
 * @Author: 951050634 
 * @Date: 2022-02-21 09:37:38 
 * @Last Modified by:   951050634 
 * @Last Modified time: 2022-02-21 09:37:38 
 */
#include <bits/stdc++.h>
using namespace std;
typedef pair <int, int> P;
stack <P> st;
int cnt;

inline int read() {
    int x, f = 1;
    char c;
    while (!((c = getchar()) >= '0' && c <= '9')) if (c == '-') f = -1;
    x = c - '0';
    while ((c = getchar()) >= '0' && c <= '9') (x *= 10) += c - '0';
    return x * f;
}

int main() {
    int n = read();
    st.push(P(0, 0));
    for (int i = 1; i <= n; i++) {
        int a = read();
        int fi = st.top().first;
        int se = st.top().second;
        st.push(P(a, 1));
        cnt += st.top().second;
        if (a == fi) {
            st.pop();
            st.pop();
            st.push(P(a, se + 1));
            if (a == se + 1) {
                cnt -= st.top().second;
                st.pop();
            }
        }
        printf("%d\n", cnt);
    }
    return 0;
}

```

## 总结

A B 轻松过，C 题考虑到错误的方向，D 题~~浅看了一下题解~~

大概是600分+1次错误的罚时

$4130/7234$    $+28分$