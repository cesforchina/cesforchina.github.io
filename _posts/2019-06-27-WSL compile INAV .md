---
title: "WIN10下用WSL编译INAV"
tags:
- WSL
- 飞控
- INAV
keywords: pages, authoring, exclusion, frontmatter
permalink: WIN10下用WSL编译INAV.html
summary: "This is some summary frontmatter for my sample post."
---
# 在Windows 10中使用Linux子系统构建

用于Windows 10的Linux子系统可能是在Windows 10下构建INAV的最简单方法。

1. 使用来自Internet的任何指南启用WSL（Windows Subsystem for Linux）
1. 从Windows应用商店安装 `Ubuntu`
1. 打开`Ubuntu`并运行:
1. `git clone https://github.com/cesforchina/inav.git`克隆我的inav-2.2.0储存库
1. `cd inav`进入项目文件夹
1. `sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa`
1. `sudo apt-get update`
1. `sudo apt-get install gcc-arm-embedded make ruby`
1. `arm-none-eabi-gcc -v`查询版本号(非必需操作)

它将安装 `gcc-arm-none-eabi` gcc version 6.3.1 20170620

从这时起，可以使用以下命令构建INAV

`make TARGET={TARGET_NAME}`

当然，将`{TARGET_NAME}`替换为您要编译的目标，编译后的固件在WSL文件区中  
`C:\用户\你的WIN10用户名\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_***\LocalState\rootfs\home\你的WSL用户名\inav\obj`  

同理也可以对你的储存库进行以上操作，可以避开因为不熟悉github操作却使用官方储存库导致的各种问题。有关项目的修改建议在WSL外部进行，通过`GitHub Desktop`克隆到本地后，进行修改，上传合并等，然后在WSL中`cd inav***`进入你的项目文件夹，进行`git pull`操作，即从储存库中拉取更新到WSL项目文件夹中。在编译过某目标板之后，下次编译时应清除  
`C:\用户\你的WIN10用户名\AppData\Local\Packages\CanonicalGroupLimited.UbuntuonWindows_***\LocalState\rootfs\home\你的WSL用户名\inav\obj`  
文件夹中的内容，即上次编译相关的文件。

{% include image.html file="WSL编译F4BY.png" %}
