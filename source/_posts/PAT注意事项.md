---
title: 经验总结
date: 2019-09-08 10:55:28
tags: PAT
categories: study
---


# PAT平时做题的总结

1. 输入：如果是一个字符一个字符不停的输入直到按下回车键的话输入方式采用以下方式：

```c++
#include<stdio.h>

while((n=getchar())!='\n'){
    ......
}

//当然上面的是C++的，下面是C的

while(scanf("%d",&x)!=EOF){
    .......
}
```

2. `Greater<int>()` 和 `ess<int>()`都是`algorithm`库中`sort()`函数的第三个参数的内置参数，表示的排序方式分别是降序和升序

3. 判断素数的快速方法是以下的代码：

```c++
bool isPrime(int n){
    if(n<=1) return false;
    else if(n==2||n==3) return true;
    else if(n%6!=1&&n%6!=5) return false;
    else{
        for(int i=5;i*i<n;i+=6){
            if(n%i==0||n%(i+2)==0) return false;
        }
    }
    return true;
}
```

4. 在写程序的时候可以使用题目中给的一些数据作为数组下标，比如题目给了一个数据的地址是多少多少，比如***1075 链表元素分类***
5. 注意编程的时候，可能会考察运算的实质，就是模拟运算，比如***1017 A除以B***，这个题目就是模拟除法的过程。
6. 例如***1018 锤子剪刀布***这个题目的解法，就是利用数字作为数组的下标来表示出拳的类型，这样就很简便的解决了符号的问题。
7. **小数点的位数问题**：

```c++
//c++可以通过包含iomanip库来调用函数
cout<<setprecious(n)<<x<<endl;
//这个n就代表了输出的数字的总位数，如果位数不足的话，就在数字后面补0。这个如果遇到了小数的话会自动四舍五入。

 std::cout << std::setw(10);
 std::cout << 77 << std::endl;
//这个的输出结果是
        77   没错就是前面有10个字符的宽度
cout<<setfill('0')<<x;
//这个就是在数字的前面补0，当然要么搭档setw()，要么搭档setprecious()
```

```c
//C里面就直接使用printf()
printf("%.2f",x);
//这个就是保持小数点后2位小数，这个也是自动四舍五入的。
```

8. 再一个就是`STL`容器的遍历问题，直接使用相应的迭代器进行遍历，像如果遇到**行尾不能有多余空格**的要求的话

```c++
#include<vector>
//假设v中有数据
vector<int> v;
//构造迭代器
for(vector<int>::iterator it=v.begin();it!=v.end();it++){
    if(it!=(--v.end())){
        printf(" ");
        这样就满足了行尾不能有空格的情况
    }
}
```

9. 还有一个老生常谈的话题，**进制转换**，直接贴代码，d是进制，n是十进制的数字，res是结果

```c++
#include<iostream>
#include<cmath>
using namespace std;

int main(){
	int n,d,res=0,i=0;
	cin>>n>>d;
	while(n!=0){
		res+=(n%d)*pow(10,i);
		n/=d;
		i++;
	} 
	cout<<res;
	return 0;
} 
```

10. 临时变量每次重新定义的时候都没有值的...参见 ***1084 外观数列*** 
11. 如果看见输入字符串的话，不用局限于使用`string`来接受输入，可以参考`第一条的输入方式`，参考**1043** **输出PATest**，可以在输入的时候使用输入的字符作为数组的下标来解决问题。
12. 如果需要输入含有空格字符的字符串的话，使用`getline(cin,str)`函数，但是使用这个函数的话**最好**在前面加上一个`cin.ignore()`来忽略上一个输入的`\n`，当然如果只需要这一个输入的话，就不用加上这条语句。
