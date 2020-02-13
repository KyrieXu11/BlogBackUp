---
title: c++中string类型和数字类型转换
date: 2019-07-14 17:01:01
tags: c++
categories: study
---
# 关于思考这个问题的原因
   我在做`pat`的题目的时候碰上一个题目是乙级`1054 求平均值`的时候遇上了一个问题，那就是`string`类型的内容是数字的变量怎么转换成纯数字的变量。

# 解决办法
**我在网上查找了不同的办法，所以我自己来总结并记录一下我理解的。**
## 1.使用标准库中stringstream头文件中的流处理
### 代码
```c++
#include<iostream>
#include<vector>
#include<map>
#include<algorithm>
#include<string>
#include<sstream> 
using namespace std;

int main() {
	string a="-3.2";
	stringstream ss;
	ss<<a;
	double b;
	ss>>b;
	cout<<b;
	return 0;
}
```
### 运行结果
![7.14.1.png](https://i.loli.net/2019/07/14/5d2af398a47af21375.png)

1.当然前面**库**只需要`iostream`和`sstream`,主要还是`sstream`中的`stringstream`
2.说说具体的用法，就是使用`stringstream`创建一个对象，然后这个对象的用法就是和标准输入输出流中的`cin`以及`cout`用法差不多(废话，都是流处理，肯定是差不多的)
3.这样应该能满足使用需求吧，如果不满足以后再添加。

## 2.使用ato'x'()函数
**这个`x`是`int`、`double`、`long`的第一个字母，意思就是把对应的字符串转化成对应的数据类型**
### 代码
```c++
#include<iostream>
#include<stdlib.h>
#include<string>
using namespace std;

int main(){
	string a="-3.2";
	double b=atof(a.c_str());
	cout<<b<<endl;
	string c="+1";
	double d=atof(c.c_str());
	cout<<d<<endl;
	return 0;
} 
```
### 运行结果
![7.14.2.png](https://i.loli.net/2019/07/14/5d2af5a3a26b487259.png)

有几个需要注意的地方：
1.首先是头文件一定要包含`stdlib.h`，因为这些库函数都是从标准库中调用的，所以一定要加`stdlib.h`.
2.函数参数一定要是一个指针，那`string`类型的指针怎么得来的？没错，`string`类中有一个函数`c_str()`就是获取`string`类的指针.
3.别的字符串比如`aaa`转换的结果是0，这个和**前面的那种方法运行结果是一样的**
4.其他要值得注意的地方应该就是数字数据类型转换的问题

# 做个总结
1.关于两种方式的缺点，我没有遇上什么缺点啊什么的，缺点比较，等我以后使用的时候如果有不顺手再补充吧。
2.关于我喜欢哪种方式，我个人觉得我更加喜欢第二种，因为比较的简洁，不过选择因人而异吧，第一种也挺好用的。


---
# 2019.7.14 19:55更新
上面两个方法都有BUG，之前不是说了别的字符串转换的结果都是0🐴？如果是类似`2.1.3` `2.1-3`之类的转换的结果是`2.1`(具体例子具体分析)总而言之就是这种类型的字符串转换结果不是0....我被坑了....😭
具体的运行结果如下：
![7.14.3.png](https://i.loli.net/2019/07/14/5d2b193ade77261206.png)