---
title: PAT-B 1043 输出PATest
date: 2019-07-11 11:24:21
tags: PAT
categories: study
---
# 题目
给定一个长度不超过 10<sup>4</sup> 的、仅由英文字母构成的字符串。请将字符重新调整顺序，按 `PATestPATest....` 这样的顺序输出，并忽略其它字符。当然，六种字符的个数不一定是一样多的，若某种字符已经输出完，则余下的字符仍按 PATest 的顺序打印，直到所有字符都被输出
## 输入格式：
输入在一行中给出一个长度不超过10<sup>4</sup>的、仅由英文字母构成的非空字符串。
## 输出格式：
在一行中按题目要求输出排序后的字符串。题目保证输出非空。
## 输入样例：
redlesPayBestPATTopTeePHPereatitAPPT
## 输出样例：
PATestPATestPTetPTePePee
## 代码
```c++
#include<iostream>
using namespace std;

int array[256]= {0};

int calTime(char patest[]) {
	int sum=0,length=sizeof(patest)/sizeof(patest[0]);
	for(int i=0; i<length; i++) {
		sum+=patest[i];
	}	
	return sum;
}

int main() {
	char input,pointer;
	char patest[]= {'P','A','T','e','s','t'};
	int j=0,time=calTime(patest);
	while((input=getchar())!='\n') {
		array[input]++;
	}
	while(j<time) {
		for(int i=0; i<6; i++) {
			pointer=patest[i];
			if(array[pointer]!=0) {
				cout<<pointer;
				array[pointer]--; 
			}
		}
		j++;
	}
	return 0;
}
```
## 一遍通过截图
![Snipaste_2019-07-11_11-30-44.png](https://i.loli.net/2019/07/11/5d26ad8be72be84939.png)
## 思路
主要通过下标的思想来做这道题