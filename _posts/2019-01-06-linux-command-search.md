---
layout: post
title: "Linux Command"
date: "2019-01-06 20:18"
author: "Danfei"
---
2019-01-06 20:18

Linux Command

1.Search

Linux系统中，利用grep打印匹配的上下几行
	$ grep -5 'parttern' inputfile //打印匹配行的前后5行
	$ grep -C 5 'parttern' inputfile //打印匹配行的前后5行
	$ grep -A 5 'parttern' inputfile //打印匹配行的后5行
	$ grep -B 5 'parttern' inputfile //打印匹配行的前5行

2.Process
命令行输入top回车，然后按大写M按照memory排序，按大写P按照CPU排序
根据关键字查询正在运行的进程
	ps -ef|grep 'parttern'
结束某个进程
	kill -9 pid #pid为相应进程号