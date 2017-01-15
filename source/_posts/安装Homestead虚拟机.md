---
title: 安装Homestead虚拟机
date: 2016-09-17 08:28:21
tags:
  - Laravel
  - PHP
copyright: true
---
Laravel 环境的搭建有点繁琐，为了减少开发者的时间消耗，官方将 Laravel 的标配环境打包成了一个 Vagrant box —— Homestead。我们只要安装好这个 Homestead 虚拟机，就能直接投入 Laravel 项目的开发，非常方便。Homestead 虚拟机也是官方推荐的开发环境，因为在隔离的虚拟机环境下完成开发也可以避免对计算机的日常使用环境造成污染。
Homestead 很好用，不过[官方的文档](https://laravel.com/docs/5.3/homestead)却并不够友好。我查阅了很多资料，整理出以下步骤，记录于此，作为备忘。

<!-- more -->

## 安装虚拟机软件和 Vagrant
虚拟机推荐安装 virtualbox，毕竟简单免费。以下步骤以 virtualbox 为准，vmware 系的其实也类似。

## 安装 Homestead 的 box
当前最新版本为 0.5.0，以下以这个版本为例。
官方直接通过 vagrant 在线安装的方法由于**下载速度太慢**，不太适合国情。我推荐单独下载 box，然后手动安装。以下为步骤：

### 取得 box 下载地址，用下载工具下载
执行命令`vagrant box add laravel/homestead`，等到开始下载 box 时按 Ctrl + C 中止。
从终端输出结果中取出 box 地址。目前最新地址为 `https://atlas.hashicorp.com/laravel/boxes/homestead/versions/0.5.0/providers/virtualbox.box`。注意地址里的数字`0.5.0`，它表示 box 的版本号。
用下载工具单独下载 box，比如可以用 aria2 下载。下载下来的文件名可能是 `hc-download`，可以改成 virtual.box 或 homestead.box 以方便管理。

### 安装 Homestead box
执行命令 `vagrant box add laravel/homestead path/to/box` 在 Vagrant 中注册 homestead box。注意 `path/to/box` 要换成下好的 homestead box 的具体路径。
注册完成后，执行命令 `vagrant box list` 你将看到 `laravel/homestead (virtualbox, 0)`。这表示 box 名为 laravel/homestead，虚拟机提供商为 virtualbox，box 版本号为 `0`。
为什么下载的是 `0.5.0` 的版本，而这里显示版本号为 `0` 呢？原来手动安装 box 时，由于 Vagrant 没法同时获取 box 的元数据，不知道安装的是哪个版本，所以就统一将 box 版本号为视为 `0`。

### 修改 Homestead box 版本号
由于后面我们要用到版本号大于等于 `0.4.0` 的 Homestead box，所以我们下面先把版本号给提上去。
首先我们进入 homestead box 的安装路径 `cd ~/.vagrant.d/boxes/laravel-VAGRANTSLASH-homestead/`。我们会看到一个目录 `0`，我们将其改为当前的版本号 `0.5.0`。
然后我们再一次执行 `vagrant box list`，我们将看到 `laravel/homestead (virtualbox, 0.5.0)`。看上去好像成功了呢。其实这只是第一步。因为我们只是简单地将目录的名字改了，而 Vagrant 内部有它的注册机制，它需要验证我们的修改是否合法。
我们在当前目录添加文件 `metadata_url`，并写入官方地址 `https://atlas.hashicorp.com/laravel/homestead`。这样 Vagrant 就会到指定的地址里寻找 box 的元数据以验证 box 的合法性。
这样下来，当前目录下应该有一个子目录 `0.5.0` 和一个文件 `metadata_url`。我们 Homestead box 的注册才终于完成。

## 克隆最新的官方库，并执行初始化脚本
```bash
cd ~
git clone https://github.com/laravel/homestead.git Homestead
cd Homestead
bash init.sh
```
以上命令将在家目录下生成一个隐藏目录 `.homestead`，里面有 Homestead 虚拟机的关键配置文件 `Homestead.yaml`

## 配置 Homestead 虚拟机
配置 `~/.homestead/Homestead.yaml` 文件，里面的各项属性[官方文档](https://laravel.com/docs/5.3/homestead)给出了较为详细的说明，可以作为参考。
注意这里的共享文件夹需要选择一个已存在的目录。如果依照默认配置，你需要在家目录里添加 `Code` 目录。

## 初始化并启动虚拟机
在 Homestead 目录下，执行 `vagrant up` **尝试**安装并启动 Homestead 虚拟机。如果虚拟机正常运行了，那么恭喜你，你已顺利完成了 Homestead 虚拟机的安装。如果初始化失败了，下面给出两种参见错误的“解决方案”（如果读者还遇到什么奇葩的方案，可以在评论区提出，我们一起探讨）。

### 虚拟机 IP 冲突错误
<!--
{% asset_img IP冲突错误.png IP冲突错误 %}
-->
![IP冲突错误](/images/IP冲突错误.png)

解决步骤如下：
 1. 打开 virtualbox ，依次点击：管理 --> 全局设定 --> 网络 --> 仅主机网络
 2. 看看这里面有没有现成的网卡。如果有，比如 `vboxnet0`，直接双击打开配置明细。若没有，新建一个网卡（右边带加号的按钮），然后双击打开明细。
 3. 点击 `DHCP 服务器` 选项卡。记下地址池的范围，比如默认的一般是：192.168.56.101～192.168.56.254
 4. 将 `~/.homestead/Homestead.yaml` 配置文件的 `ip` 属性改为地址池范围内的值，比如 `192.168.56.101`。

### 难以避免的初次启动错误
<!--
{% asset_img 难以避免的初次启动错误.png 难以避免的初次启动错误 %}
-->
![难以避免的初次启动错误](/images/难以避免的初次启动错误.png)

这个错误我处理不了，如果有大神解决了这个错误，请不吝赐教。目前只知道这个错误似乎并不影响虚拟机的使用，并且它只在虚拟机初次启动时会出现，重启将不再出现。比如你可以执行 `vagrant halt && vagrant up` 重启一下虚拟机，重启完成后你会发现这个错误已经没了。


## 附言
至此，Homestead 虚拟机安装完成，终于可以在上面练习或开发 Laravel 项目了。

个人安装 Homestead 时遇到不少坑，感谢以下资源给我指引：

- [Homestead离线安装踩坑记录](https://quericy.me/blog/827/)
- [Box 'laravel/homestead' could not be found](http://stackoverflow.com/questions/34946837/box-laravel-homestead-could-not-be-found)
- [how to download vagrant box manually?](http://laravel.io/forum/05-06-2015-how-to-download-vagrant-box-manually)
- [Homestead up - The specified host network collides with a non-hostonly network!](https://laracasts.com/discuss/channels/general-discussion/homestead-up-the-specified-host-network-collides-with-a-non-hostonly-network)
