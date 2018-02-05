---
title: 搭建 Shadowsocks 服务器并开启 TCP BBR 加速
date: 2017-05-03
tags:
---

最近感觉自己搭的SS延迟高而且速度慢，尽管我装了一个Net-Speeder开启了无脑双倍TCP包发送，然而还是没有得到太多的缓解。

然后今天就买了一个新的VPS来搭SS服务器，顺手把BBR加速给开起来，测试了一下延迟确实有明显的缓解。

#### TCP BBR

先来简单说一下这个东西吧，就是Google开发的新的拥塞控制算法，据说是用在YouTube上，并且在去年9月开源并且现在已经集成到Linux 4.9-rc8之后版本 的内核中。

[官方论坛](https://groups.google.com/forum/#!forum/bbr-dev)

[官方Start Guide](https://github.com/google/bbr/blob/master/Documentation/bbr-quick-start.md)

因此，我们这次很重要的一项步骤就是更换Linux的内核。这里要提醒的是，如果你的VPS使用的是*OpenVZ*的虚拟技术，是**不能**使用BBR的。并且，系统要求在 CentOS 6+，Debian 7+，Ubuntu 12+。

**开始前先说一声，我使用的系统是Ubuntu Server 14.04 x86-64，并且使用root用户操作**

## 更换Linux内核

按照惯例我们先更新一下apt源：
```bash
apt-get update
```

然后我们要下载Ubuntu的内核，在这里我选择了比较新的4.10.13版本。
如果你要选择别的内核版本，可以浏览[完整列表](http://kernel.ubuntu.com/~kernel-ppa/mainline/)

我们把内核文件先下载到 `/tmp` 文件夹中

```bash
cd /tmp
wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.10.13/linux-image-4.10.13-041013-generic_4.10.13-041013.201704290147_amd64.deb
```

然后使用`dpkg -i`命令执行安装

> 文件名你可以只输入前几个字母然后按tab键自动补齐 

```bahs
dpkg -i linux-image-4.10.13-041013-generic_4.10.13-041013.201704290147_amd64.deb
```

装完以后，我们查看一下已安装的内核列表

```bash
dpkg -l | grep linux-image
```

如果列表中出现了你刚才安装的内核，那么证明已经安装成功了。

然后我们执行内核更新，完成后重启。

```bash
update-grub
## 等待输出 done 后重启系统
reboot
```

重启以后，我们检查一下系统内核是否正确切换

```	bash
uname -a
```

如果输出的结果是你刚才安装的系统内核，则表示安装成功了。

## 开启BBR

执行下面这两条脚本

```bash
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
```

保存生效

```bash
sysctl -p
```

执行脚本

```bash
sysctl net.ipv4.tcp_available_congestion_control
```

如果刚才的保存和执行脚本结果都有 `bbr` 那么代表你的内核已经开启了BBR

执行脚本

```bash
lsmod | grep bbr
```

如果结果中有`tcp_bbr` ，则说明bbr已经启动。

## 安装Shadowsocks

首先检查一下python运行环境，并且安装pip。

```bash
python -V
apt-get install python-pip
```

然后安装Shadowsocks和可能用到的加密库

```bash
apt-get install python-m2crypto
pip install shadowsocks
```

在`/etc`文件夹下新建一个配置文件

```bash
mkdir /etc/shadowsocks
cd /etc/shadowsocks
vim config.json
```

在`config.json`中输入配置信息，例如

```json
{
    "server": "my_server_ip",
    "server_port": 8388,
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "password": "mypassword",
    "timeout": 300,
    "method": "aes-256-cfb",
    "fast_open": false
}
```

编辑完后，保存退出。执行命令即可在后台开启Shadowsocks服务。为了避免由root用户启动带来的无法预知的错误，用nobody用户启动。

```bash
ssserver -c config.json --user nobody -d start
```

如果想要设置开机启动，可以把上面那一行命令添加到`/etc/rc.local`文件中，注意要复制到`exit 0`之前。