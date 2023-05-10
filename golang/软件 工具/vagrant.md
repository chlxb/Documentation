# Vagrant 文档

安装地址：http://www.vagrantup.com/downloads.html 

## step1 添加box

box 是 Vagrant 的操作系统环境，可以通过 vagrant 官网提供的列表去下载 [vagrantbox](http://www.vagrantbox.es)
vagrant box add [title(本地box的名字，一般可以使用base)] [url/filename（远端box的地址或本地box的文件名）]

`vagrant box add base http://files.vagrantup.com/lucid64.box`

`vagrant box add ubuntu_base http://files.vagrantup.com/lucid64.box`

## step2 初始化:

执行初始化命令，会在当前路径生成一个VagrantFile 文件，这个是虚拟机环境的配置文件，里面有各种配置信息，具体配置可以看官网文档介绍

`vagrant init`

如果添加的box名字不是base,那么需要在初始化的时候指定名称，如：

`vagrant init "ubuntu_base"`

## step3 启动：

在有 VagrantFile 的文件夹执行启动命令：

`vagrant up`

## step4 连接虚拟机：

虚拟机启动之后，可以通过ssh来连接到虚拟机：

`vagrant ssh [name(如果VagrantFile中有多个虚拟机Provider，可以按provider配置定义的name来连接指定的虚拟机)]`

`vagrant ssh web // 连接名称为web的虚拟机`

连接成功后，可以在web虚拟机上通过如下命令尝试连接db虚拟机，连接密码默认是 "vagrant"

`ssh 192.168.33.11`

## 修改了VagrantFile后可以重启虚拟机：

`vagrant reload`

## 关机

`vagrant halt`

## 打包命令：

将当前运行的虚拟机环境进行打包，方便快速移植环境

`vagrant package`

## 插件管理

`vagrant plugin`

## 恢复虚拟机

恢复当前被挂起的状态

`vagrant resume`

## 输出用于ssh连接的一些信息

`vagrant ssh-config`

## 获取当前虚拟机的状态

`vagrant status`

## 挂起当前的虚拟机

`vagrant suspend`




