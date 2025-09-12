---
title: 从Debian12过渡到13
published: 2025-08-31
description: "从 Debian 12 Bookworm 升级到 Debian 13 Trixie"
image: ""
tags: [linux]
category: Linux
draft: false
# lang: ''
---

:::caution
备份重要数据和配置文件!!!
:::

升级需要管理员权限,请使用`sudo`或`su -`

### 升级到最新 Bookworm 系统

```bash
apt update
apt upgrade -y
apt full-upgrade -y
apt autoclean
apt autoremove -y
```

### 更换`apt`源

```bash
sed -i 's/bookworm/trixie/g' /etc/apt/sources.list
sed -i 's/bookworm/trixie/g' /etc/apt/sources.list.d/*.list
sed -i 's/bookworm/trixie/g' /etc/apt/sources.list.d/*.sources
```

### 再次更新

```bash
apt update
apt upgrade -y
apt full-upgrade -y
```

然后选择`yes`,和按`q`退出

### 清理依赖

```bash
apt autoclean
apt autoremove -y
```

`reboot`重启系统

### 查看版本

```bash
cat /etc/debian_version
```
完成了系统更新.