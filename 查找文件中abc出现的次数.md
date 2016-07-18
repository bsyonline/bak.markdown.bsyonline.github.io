---
title: 查找文件中abc出现的次数
toc: true
date: 2016-07-16 15:53:20
tags:
categories:

---

### 1.

	grep -o abc file | wc -l

### 2.
	awk -v RS='abc' 'END {print --NR}' file

### 3.
	awk -v RS="@#$j" '{print gsub(/abc/,"&")}' NOTICE

### 4.
	grep -c "abc" NOTICE
