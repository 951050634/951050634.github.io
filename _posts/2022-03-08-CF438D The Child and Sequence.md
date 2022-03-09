---
layout:     post
title:      CF438D The Child and Sequence
subtitle:   题解报告
date:       2022-03-08
author:     951050634
header-img: img-post/header1.png
catalog: false
tags:
    - 做题记录
---



# **CF438D The Child and Sequence**

## **思路**

线段树

题目要求：1.查询区间和 2.对给定区间取模 3.单点修改

考虑到 $10^9$ 内的数字，理想状况下最多进行 $log(n)$ 次取模就可以实现

但是 时间复杂度很高

考虑开一个数组 $mx[i]$ 表示该段的最大数值，如果大于模数，那么进行取模操作

可以极大优化时间复杂度

并且，这是第一次接触单点修改，注意单点修改的方法即可

---

小tips：非常有意思

---

`CPP`

``` cpp
#include <bits/stdc++.h>
#define int long long
//#define mid (l+r)>>1
using namespace std;
const int N=1e5+10;
int n,m;
int a[N];
int tree[N<<2],mx[N<<2];

inline int max(int a,int b) {
	return a>b?a:b;
}

inline int ls(int x) {
	return x<<1;
}

inline int rs(int x) {
	return x<<1|1;
}

inline void pushup(int rt) {
	tree[rt]=tree[ls(rt)]+tree[rs(rt)];
	mx[rt]=max(mx[ls(rt)],mx[rs(rt)]);
}

/*
inline void build(int rt,int l,int r) {
	if(l==r) {
		tree[rt]=mx[rt]=a[l];
		return;
	}
	build(ls(rt),l,mid);
	build(rs(rt),mid+1,r);
	pushup(rt);
}
*/

inline void build(int rt,int l,int r) {
	if(l==r) {
		tree[rt]=mx[rt]=a[l];
		return;
	}
	int mid=l+r>>1;
	build(ls(rt),l,mid);
	build(rs(rt),mid+1,r);
	pushup(rt);
}

inline void m_mod(int rt,int l,int r,int s,int t,int x) {
	if(mx[rt]<x) return;
	//if(t<l||s>r) return;
	if(l==r) {
		mx[rt]%=x;
		tree[rt]%=x;
		return;
	}
	int mid=l+r>>1;
	if(s<=mid) m_mod(ls(rt),l,mid,s,t,x);
	if(t>mid) m_mod(rs(rt),mid+1,r,s,t,x);
	pushup(rt);
}

inline void m_pos(int rt,int l,int r,int pos,int val) {
	if(l==r) {
		tree[rt]=mx[rt]=val;
		return;
	}
	int mid=l+r>>1;
	if(pos<=mid) m_pos(ls(rt),l,mid,pos,val);
	else m_pos(rs(rt),mid+1,r,pos,val);
	pushup(rt);
}

inline int query(int rt,int l,int r,int s,int t) {
	if(t<l||s>r) return 0;
	if(s<=l&&r<=t) return tree[rt];
	int mid=l+r>>1;
	int ans=0;
	if(s<=mid) ans+=query(ls(rt),l,mid,s,t);
	if(t>mid) ans+=query(rs(rt),mid+1,r,s,t);
	return ans;
	//return query(ls(rt),l,mid,s,t)+query(rs(rt),mid+1,r,s,t);
}

inline int read() {
	int x, f = 1;
	char c;
	while (!((c = getchar()) >= '0' && c <= '9')) if (c == '-') f = -1;
	x = c - '0';
	while ((c = getchar()) >= '0' && c <= '9') (x *= 10) += c - '0';
	return x * f;
}

inline void write(int x) {
	if (x < 0) putchar('-'), x = -x;
	if (x > 9) write(x / 10);
	putchar(x % 10 ^ 48);
}

signed main() {
	n=read(),m=read();
	for(int i=1; i<=n; i++) a[i]=read();
	build(1,1,n);
	while(m--) {
		int opt=read();
		if(opt==1) {
			int l=read(),r=read();
			int ans=query(1,1,n,l,r);
			write(ans);
			putchar('\n');
		}
		if(opt==2) {
			int l=read(),r=read(),x=read();
			m_mod(1,1,n,l,r,x);
		}
		if(opt==3) {
			int k=read(),x=read();
			m_pos(1,1,n,k,x);
		}
	}
	return 0;
}
```