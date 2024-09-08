---
layout: post
title: linux系统编程笔记---第一章---文件与io
---
###课程要求:
========
本章要求熟练掌握系统函数：open()/close()/read()/write()/lseek()/creat()/fcntl()/ioctl()/mmap()。掌握他们的原型、用法,并且了解文件I/O和标准I/O的差异和各自的用途。  

###参考资料：
####亚嵌课本《linux系统编程》
	第一章 文件与I/O
####W.Richard Stevens 《unix环境高级编程》
	第一章 unix基础知识
	第二章 unix标准化及实现
	第三章 文件I/O
	第五章 标准I/O库
####《unix编程艺术》

####建议学习方法
	结合linux内核的工作原理来理解
	系统内核正是内核提供给应用程序的接口
####熟练掌握C语言
	linux内核正是用C语言写的
####Man pages会帮助你更有效率的学习，但前提是你的英文水平要足够读懂它
####参考W.Richard Stevens 《unix环境高级编程》
