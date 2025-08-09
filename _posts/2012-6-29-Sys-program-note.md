---
layout: post
title: 亚嵌就业班linux系统编程课堂笔记
---

#shell脚本 socket编程 tcp/ip协议
==========================

#Chap 4: shell脚本

##4.1 第一行的#!

当一个文件中开头是#!时，内核会扫描改行的剩余部分，看是否存在可用来执行程序的解释器的完整路径，此外内核还会扫描是否有一个选项要传给解释器，内核会以指定的选项来引用解释器，在搭配命令行的其他部分。  
	例如:  
	
	有一个csh脚本，名为/usr/ucb/whizprog  
	它的第一行为#!/bin/csh -f，且/usr/ucb位于PATH中，  
	于是当用户输入whizprog -q /dev/tty01时，内核解释#！命令行后就会以如下方式引用csh：  
	/bin/csh -f /usr/ucb/whizprog -q /dev/tty01  

*	在实际编写脚本时，脚本是否具有可移植性取决于是否有完整的路径名称。  
*	在书写脚本时不使用任何选项，也可以在脚本后面添加" -" ，基于安全性的考虑，这可以避免某种程度上得欺骗是攻击。  
例：  

	\#! /bin/sh -  

##4.2 位运算
四种位运算符：&或，|与，^异或, ～取反

基本技能：0x0 -> 0xF 的二进制(4-bit)表示，要求非常熟悉，正反都行

	& => 得0 
	| => 得1
	^ => 加法 (^0 则不变；^1 则反转)
	~ => 得0 (构造掩码)

重要结论：

	位运算不能对8bit操作，会被提升为32bit，同时会根据数据类型来扩展符号位
	（无符号数扩展前面补0；有符号数扩展前面补符号位)

##4.3 常见操作
###1 不要把 &, | 混淆为 &&， ||

	1 & 1 == 1 && 1
	1 & 2 == 0 但是 1 && 2 == ture	同理类似的 5 & 10 == 0

###2 取反操作用来构造数

	0xFFFFFFFF == ~0x0
	0xFFFFFFE0 == ~0x1F

###3 运算符&,^,| 的优先级比<,>关系运算符和判等运算符== 要低
举例：

	int status = 0;
	if (status & 0x4000 == 0)	// 条件成立，还是不成立？

重要结论：上述语句等价于 if (status & (0x4000 == 0))

##4.4 移位运算
###1 左移 <<
	左移 1位 等价于 乘2
	左移时数字右边补0

###2 右移 >>
	右移 1位 等价于 除2
	右移时数字左边会补符号位 （补符号位的结果是新的负数是原来负数的一半）

##4.5 范例代码
###1 设置位 set bit
	a |= 1<<4;
	
###2 清除位 clear bit
	a &= ~(1<<4);
	
###3 测试位 test bit
	if (a & (1<<31))
	
###4 设置位域 set bit-field
	a &= ~(0x7<<28);
	a |= 0x5<<28;
	
###5 获取位域 get bit-field
	if (((a>>28) & 0x7) == 0x5)

#练习题
##1 计算一个任意、输入整数a中有多少个bit 1?
要求：int sum_bit_1(unsigned int a);

##2 用位运算实现字符的大小写转换 （两种方法：异或，测试后修改）
要求：输入大写的字符转为小写，输入小写的字符转为大写；

##3 用位运算实现对一个无符号整型的二进制打印，八进制打印，十六进制打印；
要求：
	
	int print_bin(int a);
	int print_oct(int a);
	int print_hex(int a);

	a = 31
	二进制打印 000000000000.. 01 11 11
	八进制打印 000... 0 3 7
	十六进制打印 00 00 00 1F

##4 用位运算实现随机生成无重复的26个字母，要求不允许使用数组
提示：随机数用 random() 函数，用一个整型数的bit0-bit25来记录已经产生的字母

用位运算实现随机生成无重复的10个数字(0-9)，要求不允许使用数组

##5 汉字的字符显示
请参考以下2篇技术文档， <a href="http://wenku.baidu.com/view/cb9fe505cc17552707220865.html"> 漫谈字符集和编码 </a> 和
<a href="http://wenku.baidu.com/view/395f895d312b3169a451a489.html"> 汉字点阵字库原理 </a> 

在终端 Console 下显示 ”汉“ 字和自己的中文姓名。
 

#Chap 9: tcp/ip协议
##9.1 指针概念
预备知识
###1 内存
总线：地址线（64M=26根) 	数据线(32根)	控制线(读写)

地址：最小单位（0地址-0字节，1地址-1字节）	对齐（指令是按4字节对齐的）

###2 指令
访存指令： 可以读写内存，用2个寄存器，一个存地址，一个放数据

	LDR r0, [r1]	// mem(r1) -> data(r0)
	STR r0, [r1]	// data(r0) -> mem(r1)

怎么区分内存里面的数据和指令？ 	

	PC 程序计数器，一般初值为0，指向第一条指令
	除非碰到跳转指令，否则通常情况下，pc = pc + 4	

###3 变量
变量的本质是什么？	变量是C语言出现之后才有的

2个属性：变量名	int a = 100；	
	变量值	a + 2;

变量的本质是：在C语言中，由C编译器分配的，表示内存地址和存储单元内容的一种联系。

###4 指针变量
指针的本质是什么？	指针也是一种变量，但这个变量值它就是内存地址。

0xbfxxxxxx	函数栈内，局部变量

0x8048xxx	数据段，全局变量

##9.2 指针用法
###1 指针变量定义：	
	int * p;

###2 指针变量赋值：	
	p = &a;		// good
	p = 0x8048xxx;	// no error, not good.

补充：
	int * p = &a;
	应该看待为 int* p;	同时 p = &a;	而不是 *p = &a;
	
###3 指针变量使用：	
	*p = 200;	// 赋值
	printf("%d", *p);	// 读内容 
	p--;		// 指针调整

###4 指针变量类型：	
	char * p;
	short * p;
	int * p;
	指针变量的类型，决定了用 *p 去访问内存时候的读取的字节数

###5 易混淆的错误
1) &a 的用法

	&a 是一个常量（由编译器决定），代表内存地址
	&a 是一个32位整型，与a是什么类型无关
	&a = 100; 	试图修改a的地址，这样的用法是错误的

2) *p 的用法

	int * p;
	*p = 1;		// wrong!
	p = 1;		// no error
	给指针本身赋值p=1是允许，但如果用指针取内容 *p=1 这是有风险的，会出段错误。

###6 指针的重要用法-传参
用于获取用户输入

	int a;
	scanf("%d", &a);

	int * p = &a;
	scanf("%d", p);

用于交换 swap 函数

	void swap(int * a, int * b);
	// 可以用于修改上一级主调函数内部的变量的值

也可以通过传指针来获取多于1个以上的返回值

	void decompose(double x, long * int_part, double * frac_part);

课堂练习： 请使用 char * 指针，对一个整型数 a = 0x12345678 修改为大端存储
提示：	void swap(char * p1, char * p2);

###7 指针的重要用法-const保护

	int main(int argc, const char * argv[]);
	int printf(const char * format, ...);
	int scanf(const char * format, ...);
	char * strcpy(char * dst, const char * src);

	const int * p;	// const 修饰 *p
	*p = 100;	// error
	*(p+1) = 100;	// next pos is error too
	a = *p;		// ok
	p++;		// ok

对比学习 const 的另一种用法

	int * const p;	// const 修饰 p
	*p = 100;	// ok
	a = *p;		// ok
	p++;		// error

###8 指针的重要用法-用作返回值
用传入的指针作为函数返回的指针，这是允许的。

重要结论：
如果是函数内部局部变量的地址作为指针返回，这是有风险的。（编译没错，但不好）

##9.3 指针和数组
###1 指针的算术运算
	1) 加一个整数 p+1 	(地址的增加值取决于指针的类型，整型指针则加4)
	2) 减一个整数 p-1	(地址的减少值取决于指针的类型，整型指针则减4)
	3) 两个指针相减	p1-p2	(结果是两个指针之间的元素个数，而不是字节数)
		只有相同类型的指针才能作减法，不同类型不能相减，编译器会报error
	4) 自增加 p++, ++p
	5) 自减少 p--, --p
	6) 指针的比较	(p1 > p2)

###2 特殊类型指针 void * p
	void * p = &a;	// ok
	p = 100;	// ok
	a = *p;		// error
	*p = b;		// error
	p+1 (整数加1)	// ok
	p++/++p	(加1)	// ok
	void * malloc(size_t);

###3 指针名和数组名
	int a[10];
	int * p = a;
	有何异同？
		a[0]	vs	p[0]	=> 相同的
		*p	vs 	*a	=> 相同的
		*(p+1)	vs 	*(a+1)	=> 相同的
		p+1	vs	a+1	=> 相同的
	
		p++	vs 	a++	=> 不同的（数组名是一个常量，不能修改）

###4 指针和二维数组
	int a[5][6];
	int * p = &a[0][0];
	int * p = a;
	int * p = a[0];
	int * p = a[2];

课堂作业： 请用指针实现对一个数组的调整，要求奇数在左边，偶数在右边。
要求： 尽可能不占用额外的存储空间。

### 编程方法
最重要的就是分解（设计好的函数），更重要的，在于设计程序时，从何种角度考虑解法？
	
	判定标准：
	1） 逻辑简单 （嵌套少一些）
		能用1个while，就不用2个
		能不用else，就不用
		while, if, for 之间的递进逻辑少
		代码行数来作为参考
	
	2） 学会设计函数
		原则上，能够复用的函数是好的设计，能多次复用最好
		函数的功能最好少一些，一个函数只完成一个单一功能
		函数内部除了调试语句打印外，最好不打印
	
	3） 数据驱动编程
		《Unix 编程艺术》 -> Data Drive
		精心考虑所谓算法处理过程中要发生变化的数据是什么，以及何种条件下变化？


###5 动态内存分配 allocate
堆heap 和 栈stack 的概念

	&a = 0xbf9cb86c 	栈空间 3G 向下

	p = 0x8129008 		堆空间 向上

	&b = 0x804a018 		数据段（全局变量）跟着代码段
	main = 0x8048414 	代码段最小

常用函数	

	void * malloc(size_t size);
	void free(void * ptr);

	void * calloc(size_t nmemb, size_t size);	
	void realloc(void * ptr, size_t size);
	
	void * alloca(size_t size);


#Chap 10: socket编程
##10.1 基本概念
###1 字符串字面量  	"hello, world"

	char str[] = "hello, world";

###2 不要将字符常量和字符串常量混淆

	易混淆的 'a' 和 "a" (由两个字符’a’和’\0’组成的)
	char c = 'a';
	char c = "a";
	
	"abc" 字符串本身是一个常量，它的类型是字符指针类型 const char *
	const char * p = "abc";

###3 转义用法

	\123 	八进制表示
	\x18 	十六进制表示
	\	连接作用	(推荐使用 "hello" "world")


##10.2 字符串的存储
易混淆的知识点
	'\0' 	vs	'0' 
	0x00	vs	0x30	(ASCII 码值不一样) 

	'0'	vs	"0"	
	0x30	vs 	0x30 0x00	(字节数不一样)

	'\0'	vs 	0
	0x00	vs	0x00 00 00 00	(字节数不一样)

	'\0'	vs 	'\x0'		
	0*8	vs	0*16		(权重不一样，字节数一样)

经典题目

	#define	CHAR(hex)	"0123456789ABCDEF"[hex]

	printf('\n');	为什么会出现段错误？ 请结合指针传参解释一下。
	

##10.3 初始化字符串数组
几种方法比较	

	char buf[8] = "012";		// ok, the left is = '\0'
	char buf[8] = "0123456";	// ok, is a string
	char buf[8] = "01234567";	// ok，not a string
	char buf[8] = "012345678";	// ok，not a string, warning

	char buf[] = "0123456789";	// good, size is 11

易混淆的错误

	char buf1[] = "hello";
	char * buf2 = "HELLO";

	不同点1 - 类型不同：buf1是数组 vs buf2是指针
	不同点2 - 大小不同：buf1占6字节 vs buf2占4字节
	不同点3 - 用法不同：buf1不能++ vs buf2可以++
	不同点4 - 属性不同：buf1是地址常量 vs buf2是指针变量
	不同点5 - 存储不同： "hello"是数据段（全局） vs "HELLO"是常量数据段
	不同点6 - 赋值不同：字符串拷贝赋值（局部） vs 常量字符串的地址赋值
	不同点7 - 写权限不同： buf1[0]='H' 修改可以 vs buf2[0]='h'修改错误
	不同点8 - 效率不同： 全局buf1 > 局部buf2(常用) > 局部buf1

##10.4 字符串的读写
	printf("%s\n", p);	-> printf("%.5s\n", p);
	puts(p)			

	scanf("%s", p);		-> scanf("%5s", p);
	gets(p);		-> fgets

### 课堂练习题1
	用户输入一个字符串，把这个字符串转换为小写输出 （或者大小写互换）
	'A' 	0x41	-	'Z'	0x5A
	'a'	0x61	-	'z'	0x7A	提示： man ascii
	
	01000001 'A'	01011010 'Z' [
	01100001 'a'	01111010 'z'

### 课堂练习题2
	猜数游戏：电脑随机产生4位数，然后用户输入4位数，电脑告诉你是?A?B
	请你最后猜出电脑的4位数是多少？	
	(A表示位置和数字都对，B表示位置不对，数字对)

	设计函数：	
	void comp_str(char * pa, char * pb, int * nA, int * nB);
	
	printf("%dA %dB \n", *nA, *nB);

	// 主程序关键参考代码
	while (1)
	{
		scanf("%4s", strb);
		comp_str(stra, strb, &na, &nb);
		printf("%dA %dB \n", na, nb);

		if (na == 4)
			break;
	}

### ”猜数游戏“参考代码 － 用户猜数

	#include <stdio.h>
	
	#define		N	3
	
	void get_rand_num(int n, char * guess)
	{
		int counter = 0;
		int digit = 0;
		int flag = 0;
	
		srand(time(NULL));
	
		while (counter < n)
		{
			// get a rand digit in 0-9
			digit = rand() % 10;
	
			if (!(flag & (1<<digit)))
			{
				*guess = digit + '0';
	
				guess++;
				counter++;
	
				// set flag bit_n = 1
				flag |= 1<<digit;
			}
		}
		
		return;
	}
	
	void get_input_num(int n, char * input)
	{
		scanf("%s", input);
	
		*(input+N) = '\0';
	
		return;
	}
	
	void comp_str(char * pa, char * pb, int * na, int * nb)
	{
		int i, j;
	
		*na = 0;
		*nb = 0;
	
		for (i = 0; i < N; i++)
			for (j = 0; j < N; j++)
			{
				if (pa[i] == pb[j])
				{
					if (i == j)
						(*na)++;
					else
						(*nb)++;
				}
			}
	
		return;
	}
	
	int main(void)
	{
		char guess[N+1] = "39";
		char input[N+1];
		int na = 0, nb = 0;
		int counter = 0;
	
		printf("hello, guess num\n");
	
		get_rand_num(N, guess);
	//	printf("guess num = %s\n", guess);
	
		while (1)
		{
			counter++;
	
			printf("please input %d digits:", N);
			get_input_num(N, input);
			printf("input num = %s\n", input);
	
			// compare two string, return nA, nB;
			comp_str(guess, input, &na, &nb);
	//		printf("str1: %s\n", guess);
			printf("%d str2: %s\n", counter, input);
			printf("%dA, %dB\n", na, nb);
	
			if (na == N)
				break;
		}
	
		printf("You win! congratulation! game over!\n");
	
		return 0;
	}

		
### ”猜数游戏“参考代码 － 电脑猜数

	#include <stdio.h>
	
	//#define	N	2
	//#define	ALL	(10*10)
	//#define	N	3
	//#define	ALL	(10*10*10)
	#define	N	4
	#define	ALL	(10*10*10*10)
	//#define	N	5
	//#define	ALL	(10*10*10*10*10)
	
	void get_rand_num(int n, char * guess)
	{
		int counter = 0;
		int digit = 0;
		int flag = 0;
	
		srand(time(NULL));
	
		while (counter < n)
		{
			// get a rand digit in 0-9
			digit = rand() % 10;
	
			if (!(flag & (1<<digit)))
			{
				*guess = digit + '0';
	
				guess++;
				counter++;
	
				// set flag bit_n = 1
				flag |= 1<<digit;
			}
		}
		
		return;
	}
	
	void num_to_str(int num, char * str, int n)
	{
		int rest;
		int i = 0;
		char * p = str;
		
		while (num > 0)
		{
			rest = num % 10;
			*p++ = rest + '0';
			num /= 10;
		}
	
		while (p - str < n)
			*p++ = '0';
		*p= '\0';
	
		for (i = 0; i < n / 2; i++)
		{
			char tmp;
			tmp = str[i];
			str[i] = str[n-1-i];
			str[n-1-i] = tmp;
		}
	
		return;
	}
	
	int check_same_digit(char * str, int n)
	{
		int i, j;
	
		for (i = 0; i < n - 1; i++)
			for (j = i + 1; j < n; j++)
			{
				if (str[i] == str[j])
					return 1;
			}
	
		return 0;
	}
	
	int find_first_num(int p[])
	{
		int i = 0;
	
		for (i = 0; i < ALL; i++)
			if (p[i] == 1)
				return i;
	
		return -1;
	}
	
	void comp_str(char * pa, char * pb, int * na, int * nb)
	{
		int i, j;
	
		*na = 0;
		*nb = 0;
	
		for (i = 0; i < N; i++)
			for (j = 0; j < N; j++)
			{
				if (pa[i] == pb[j])
				{
					if (i == j)
						(*na)++;
					else
						(*nb)++;
				}
			}
	
		return;
	}
	
	int check_all_possible(char * src, int na, int nb, int p[])
	{
		int i = 0;
		char dst[N+1];
		int new_na, new_nb;
	
		for (i = 0; i < ALL; i++)
		{
			if (p[i] == 0)
				continue;
	
			// i -> string dst;
			num_to_str(i, dst, N);
	
			comp_str(src, dst, &new_na, &new_nb);
	
			if (new_na != na || new_nb != nb)
				p[i] = 0;
		}
	
		return 0;
	}
	
	int main(void)
	{
		int i = 0;
		char str[N+1];
		int possible[ALL];
		int counter = 0;
	
		char your_num[N+1] = "1234";
	
		printf("hello, pc guess num \n");
		//scanf("%s", your_num);
		get_rand_num(N, your_num);
		printf("your num: %s\n", your_num);
		
		for (i = 0; i < ALL; i++)
		{
			//printf("i = %d, ", i);
	
			num_to_str(i, str, N);
	
			if (check_same_digit(str, N))
				possible[i] = 0;
			else
				possible[i] = 1;
	
			//printf("str = %s, p = %d \n", str, possible[i]);
		}
	
		while (1)
		{
			int guess = find_first_num(possible);
			char guess_str[N+1];
			int na = 0, nb = 0;
	
			printf("guess = %d\n", guess);
			num_to_str(guess, guess_str, N);
			printf("guess str = %s\n", guess_str);
	
			comp_str(guess_str, your_num, &na, &nb);
			printf("hint: %da, %db\n", na, nb);
	
			printf("please tell me ?A?B \n");
			//scanf("%da%db", &na, &nb);
			counter++;
			printf("%d: na = %d, nb = %d \n", counter, na, nb);
			if (na == N)
				break;
	
			check_all_possible(guess_str, na, nb, possible);
		}
	
		printf("haha, I win!\n");
		
		return 0;
	}

