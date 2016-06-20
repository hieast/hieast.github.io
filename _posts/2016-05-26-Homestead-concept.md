--- 
layout: post 
title: Homestead安装流程及注意点
category: Tech  
tags: Homestead laravel
---

安装环境：64位win10，虚拟化软件为virtualbox

整个安装流程为：

- 安装git（git bash）
- 安装VirtualBox（除非确定最新版有问题无法安装，否则安装最新版）
- 安装 Homestead Vagrant box
- git clone Homestead
- 配置 Homestead.yaml 和 启动 Vagrant box  


安装主要流程主要依照：  

[Laravel Homestead](http://laravel-china.org/docs/5.1/homestead)  
[Vgrant安装配置](https://github.com/astaxie/Go-in-Action/blob/master/ebook/zh/01.2.md)  


我在安装过程中遇到的问题和应对方法:
-
1. 系统环境。由于我此前使用的VMware等其他软件，破坏了一些系统环境配置，导致安装最新版的virtualbox一直失败。重装系统后再安装就没有报错。经验就是以后新的win10系统只安装虚拟机和windows平台的IDE，过于复杂的环境在虚拟机中配置。
2. vagrant box add laravel/homestead  
该命令的意思是在线下载laravel/homestead的box，网络稍有不好，很容易下载失败。  
根据提示信息可以找到virtualbox.box的真实下载地址，使用其他工具可以下载，然后导入。
这里我遇到两个问题
 - bash当前目录与virtualbox.box存放在不同分区时会报错，提示Couldn't open file /virtualbox.box，疑似权限问题。将virtualbox.box放在Homestead目录所在的分区即可避免此问题。
 - 在~\.vagrant.d\boxes\laravel-VAGRANTSLASH-homestead中可以看到，直接add该box会默认版本号为0，而vagrant up时对box的版本号有要求，至少为0.4.0，因此可以参照[导入盒子](https://phphub.org/topics/2090)的方法，写一个metadata.json，然后导入。下面评论还有其他方法解决这个问题，我觉得这个方法是最为稳妥的。

