# 概述

利用Shadowsocks实现翻墙代理

## 环境准备

### 申购可外墙的服务器主机

主机我选择<a href="https://manage.host1plus.com">Host 1 Plus</a>，注册、选择购买Service套餐，支付后系统会分配主机IP和账号密码

### 安装Linux

进入已购买的服务器主机，选择安装操作系统，这里我安装的是CentOS 7做为我的linux服务器

## 安装配置

### 安装putty

在本地安装putty远程linux的操作工具，输入购买是分配好的IP账号与密码登录linux系统

### 安装Python

因为Shadowsocks基于Python运行，所以必须先安装Python服务
>sudo yum install python-pip

如果发现安装Python会报一下错误，形如“No package python-pip available.Error: Nothing to do”，说没有python-pip软件包可以安装。这是因为像centos这类衍生出来的发行版，他们的源有时候内容更新的比较滞后，或者说有时候一些扩展的源根本就没有。
所以在使用yum来search  python-pip的时候，会说没有找到该软件包。因此为了能够安装这些包，需要先安装扩展源EPEL。
EPEL(<a href="http://fedoraproject.org/wiki/EPEL">http://fedoraproject.org/wiki/EPEL</a>) 是由 Fedora 社区打造，为 RHEL 及衍生发行版如 CentOS、Scientific Linux 等提供高质量软件包的项目。

首先安装epel扩展源：
>sudo yum -y install epel-release

然后安装python-pip:
>sudo yum install python-pip

安装完之后别忘了清除一下cache:
sudo yum clean all

### 安装Shadowsocks

这里就开始安装shadowsocks的服务了
>pip install shadowsocks

### 配置Shadowsocks

进入etc目录新建shadowsocks.json配置
>vim /etc/shadowsocks.json

这时候vim打开了一个编辑文本，输入内容如下：
    {
        "server": "0.0.0.0",
        "local_address": "127.0.0.1",
        "local_port":1080,
        "port_password": {
        	"8000": "password",
        	"9000": "password"
    	},
        "timeout":300,
        "method":"rc4-md5",
        "fast_open": false,
        "workers": 1
    }

然后保存，至此shadowsocks配置已完成。

####配置的说明：

Name------------Explanation

server----------the address your server listens（服务器IP）

local_address---the address your local listens（本地代理地址）

local_port------local port（本地代理端口）

port_password---password used for encryption(自己设定的服务器端口和密码)

timeout---------in seconds（超时断开，以秒为单位）

method----------default: "aes-256-cfb", see Encryption（加密方式）

fast_open-------use TCP_FASTOPEN, true / false（是否使用TCP）

workers---------number of workers, available on Unix/Linux（这个只在Unix和Linux下有用，可不设置）

### 启动Shadowsocks

>ssserver -c /etc/shadowsocks.json -d start

### 开放防火墙端口

根据前面shadowsocks.json里配置的端口来开放防火墙端口，此处开放8000-9000的端口:
>/sbin/iptables -I INPUT -p tcp --dport 8000:9000 -j ACCEPT

保存ip策略表:
>service iptables save

重启ip策略表:
>service iptables restart

## VPN链接客户端

### For Windows

下载客户端工具<a href="https://github.com/shadowsocks/shadowsocks-windows">Shadowsocks工具</a>，对应Shadowsocks服务的配置信息，即可连接。