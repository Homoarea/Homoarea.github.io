---
title: 安装Debian Linux
published: 2025-02-17
description: "Hello Debian"
image: "https://www.debian.org/Pics/debian-logo-1024x576.png"
tags: [linux, tech, 可爱]
category: 教程
draft: false
# lang: ''
---

## 什么是 Linux?

常说的 Linux(GNU/Linux)是免费使用和自由传播的类 Unix 操作系统([详情见 wiki](https://zh.wikipedia.org/zh-cn/Linux "查看Linux的详情")).操作系统相当于手电筒的开关,主要操作计算机底层的硬件.没有操作系统,就没办法使用电子垃圾了,操作系统相当重要呢(笑)

## 安装 Debian

:::caution[注意]
如果是重新安装系统,请提前备份重要数据!!
:::

:::note[需要]
u 盘>4GB,一台个人电脑
:::
Linux 发行版多种多样,我个人喜欢使用 Debian,比较省事.其实安装系统非常简单,跟着 Debian 图形化安装程序点下一步即可,不需要用户从面粉制作蛋糕.最后配置一下就可以使用了.

### 制作并使用 USB 启动盘

先从[官网](https://www.debian.org/distrib/)下载系统映像.建议下载离线版本`64 位 PC DVD-1 iso`,使用网络版本可能因网络问题导致安装时间大大延长,我们可以安装完毕后在使用网络获取软件.

接下来下载软件以制作启动盘(可以用以下软件):

- `Windows`使用[Rufus](https://rufus.ie/zh/).[点击下载](https://github.com/pbatard/rufus/releases/download/v4.9/rufus-4.9p.exe)

- `MacOS`[balena-etcher](https://gh.llkk.cc/https://github.com/balena-io/etcher/releases/download/v2.1.4/balenaEtcher-2.1.4-x64.dmg)

插入 u盘,使用软件向 u盘写入 iso 影像.

1. 关机状态插入启动盘.
2. 开机时按F12(自行搜索电脑品牌BIOS快捷键或快速启动快捷键),进入BIOS或启动菜单
3. 选择U盘启动项,按`Enter`启动U盘.

### 图形化安装
个人推荐使用`KDE Plasma`图形界面.
[使用桌面，勾选 □ Debian desktop environment □ KDE Plasma,这里有不错的文章](https://www.cnblogs.com/hopeblaze/articles/18607142)

## 配置Debian,KDE
### 配置语言
点击设置
到`settings > region and languages > language`
`修改 > 添加更多 > 简体中文`,将简体中文移动到最顶部,点击应用.
登出后重新登录.

### 体验终端模拟器

通过CLI(命令行界面),使用命令行来操作,虽然一开始不习惯,但越使用越方便.
Debian有包管理器,通过其安装应用,不用像Windows一样点下一步和防止流氓软件,解放双手,可喜可贺,可喜可贺.

打开终端(Konsole)
依次输入命令
```bash
ls #列出
ls --help #查询帮助
man ls #查询手册
```

### 添加用户到 sudoers

普通用户有时无法进行一些操作,升级权限到管理员用户吧!

打开终端(Konsole)

**将`username`替换为你的用户名**

> 1. `su -`
> 2. `sudo visudo`
> 3. 末尾添加`username    ALL=(ALL:ALL) ALL` ，保存并退出
> 4. `exit`

或者
```bash
su -
echo "username  ALL=(ALL:ALL) ALL" >> /etc/sudoers
```

现在可以使用 `sudo ls`

### Debian镜像源(以清华源为例)

Debian 默认apt源速度很慢,更换国内镜像后提升速度.

连接网络
[Debian清华源](https://mirrors.tuna.tsinghua.edu.cn/help/debian/)

Debian 12 (bookworm)
- [x] 启用源码源
- [x] 使用非自由软件源
- [x] 强制安全更新使用镜像
```bash
sudo echo "# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free non-free-firmware
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm main contrib non-free non-free-firmware

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free non-free-firmware
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-updates main contrib non-free non-free-firmware

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free non-free-firmware
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bookworm-backports main contrib non-free non-free-firmware

# 以下安全更新软件源包含了官方源与镜像站配置，如有需要可自行修改注释切换
deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bookworm-security main contrib non-free non-free-firmware
deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bookworm-security main contrib non-free non-free-firmware" > /etc/apt/sources.list
```
### 常用软件
```bash
# 更新软件包
sudo apt update && sudo upgrade -y

# 分别为信息查看、图形化密钥管理、音视频播放器、直播录像、下载器、输入法
sudo apt install neofetch seahorse vlc obs-studio qbittorrent fcitx5 fcitx5-chinese-addons
```

### Nvidia驱动(无nvidia显卡跳过)

:::note
KDE 桌面进不去，不想配置启用 Wayland,可以将 Wayland 改为 X11
:::

**[简洁明了的步骤](https://www.bilibili.com/opus/880138871710416980)**

### 外观、主题、KDE插件
按自己喜欢打开设置调节(其实默认就挺好了)。

### END
迎接崭新的 Debian 吧。
