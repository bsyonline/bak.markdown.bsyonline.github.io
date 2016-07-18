---
title: 编译源代码安装node.js和gulp
toc: true
date: 2016-07-16 15:53:39
tags:
categories:
---

标签：linux
###1. 下载最新版本源代码

###2. 解压安装

	$ sudo cp ~/Downloads/node-v5.11.0.tar.gz /usr/local/src/
    $ cd node-v5.11.0
    $ sudo ./configure
    $ sudo make
    $ sudo make install
    $ node -v

###3. 安装gulp
####3.1 全局安装gulp命令行工具
	$ npm install -g gulp
####3.2 项目下安装开发环境
	$ sudo npm install -save-dev gulp
    $ sudo npm install -g cnpm --registry=https://registry.npm.taobao.org
    $ sudo npm install
    $ gulp
