---
title: 飞牛Nas安装桌面及应用市场教程
date: 2026-02-08 19:32:24
tags: [飞牛,Nas,科技,Linux,教程]
cover: https://image.starsfire.top/blog/2026/2/8/cover.png
---

# 飞牛Nas安装桌面及应用市场教程

当我们装完飞牛后看着这个画面是不是非常难受，于是主包准备给他装个桌面。

![飞牛初始界面](https://image.starsfire.top/blog/2026/2/8/0.jpg)

## 安装肉桂桌面

### 1. 开启SSH服务

首先网页打开飞牛：

![打开飞牛](https://image.starsfire.top/blog/2026/2/8/1.png)

在设置中找到SSH并打开：

![开启SSH](https://image.starsfire.top/blog/2026/2/8/2.png)

### 2. 连接NAS

电脑打开终端SSH连接上NAS，（为什么用终端呢？很简单，怕有的人不会用ssh软件）

（Windows终端总是人手一个吧）

连接命令：
```bash
ssh <用户名>@<Nas地址>
```

（注意：<>中的内容根据实际情况填写）

![连接NAS](https://image.starsfire.top/blog/2026/2/8/3.png)

### 3. 更新软件包列表

更新一下软件包列表：

更新命令：
```bash
sudo -i
# 然后输入密码
apt update
```

![更新软件包](https://image.starsfire.top/blog/2026/2/8/4.png)

### 4. 安装肉桂桌面

安装肉桂桌面，这时候又有人会问了，为什么不用gnome或kde，很简单，肉桂轻量，老设备也带得动

况且主包后台还挂了机器人、MC服务器等，如需其他桌面等待博客后续更新：

安装命令：
```bash
apt install cinnamon -y
```

![安装肉桂桌面](https://image.starsfire.top/blog/2026/2/8/5.png)

### 5. 更换语言

接下来换语言：

更换语言命令：
```bash
dpkg-reconfigure locales
```

![更换语言](https://image.starsfire.top/blog/2026/2/8/6.png)

↓键下去找到中文：

![找到中文](https://image.starsfire.top/blog/2026/2/8/7.png)

按空格勾选然后enter：

![勾选中文](https://image.starsfire.top/blog/2026/2/8/8.png)

选中文然后enter：

![选择中文](https://image.starsfire.top/blog/2026/2/8/9.png)

### 6. 重启NAS

reboot重启一下：

重启命令：
```bash
reboot
```

![重启NAS](https://image.starsfire.top/blog/2026/2/8/10.png)

### 7. 登录桌面

ok了，登上nas设置的号：

![登录桌面](https://image.starsfire.top/blog/2026/2/8/11.jpg)

## 安装应用市场

### 1. 传输安装包

接下来安装应用市场，下载deb后在当前文件夹打开终端：

![打开终端](https://image.starsfire.top/blog/2026/2/8/12.png)

输指令将文件用sftp传到nas上：

传输命令：
```bash
scp <星火应用市场>.deb <用户名>@<Nas地址>:/home/<用户名>
```

（注意：<>中的内容根据实际情况填写）

![传输文件](https://image.starsfire.top/blog/2026/2/8/13.png)

### 2. 安装依赖

再次ssh连上nas安装依赖，注意用root用户，或者指令前加sudo：

安装依赖命令：
```bash
apt install libdtkcore5 libdtkgui5 libdtkwidget5 libqt5concurrent5 libqt5positioning5 libqt5printsupport5 libqt5webchannel5 libqt5webenginecore5 libqt5webenginewidgets5 dde-qt5integration libnotify-bin dpkg-dev
```

![安装依赖](https://image.starsfire.top/blog/2026/2/8/14.png)

### 3. 安装星火应用市场

用dpkg安装星火应用市场，注意用root用户，或者指令前加sudo：

安装命令：
```bash
dpkg -i /home/<用户名>/<星火应用市场>.deb
```

（注意：<>中的内容根据实际情况填写）

![安装应用市场](https://image.starsfire.top/blog/2026/2/8/15.png)

### 4. 打开应用市场

菜单中可以找到星火应用市场，打开，大功告成，然后安装你想要的软件吧：

![打开应用市场](https://image.starsfire.top/blog/2026/2/8/16.jpg)

## 一键安装脚本

为了方便用户，我写了一个一键安装脚本，只需一行代码即可完成所有安装步骤：

```bash
wget -O install.sh https://image.starsfire.top/blog/2026/2/8/install.sh && chmod +x install.sh && sudo bash install.sh
```

## 总结

通过以上步骤，我们成功为飞牛Nas安装了肉桂桌面和星火应用市场，让NAS的使用体验更加友好。肉桂桌面轻量高效，适合各种设备使用，而星火应用市场则为我们提供了丰富的软件资源。

如果你有任何问题或建议，欢迎在评论区留言讨论！
