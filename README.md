### wgcf

mkdir wgcf && cd wgcf

wget -O wgcf https://github.com/ViRb3/wgcf/releases/download/v2.1.4/wgcf_2.1.4_linux_amd64 && chmod +x wgcf

#注册WARP账户

./wgcf register

#生成WireGuard配置文件

./wgcf generate

wget https://raw.githubusercontent.com/1219099256/wireguard-go/master/wireguard-go -P /usr/bin/ && chmod +x /usr/bin/wireguard-go

#编译

git clone https://git.zx2c4.com/wireguard-go && cd wireguard-go && make

cp wireguard-go ../wireguard-go-bak && cd ../wireguard-go-bak

#安装

rm /usr/bin/wireguard-go && wget https://github.com/1219099256/wireguard-go-bak/raw/master/wireguard-go-20201118 -O wireguard-go /usr/bin/wireguard-go  && chmod +x /usr/bin/wireguard-go

### debian

echo "deb http://deb.debian.org/debian/ unstable main" > /etc/apt/sources.list.d/unstable.list

printf 'Package: *\nPin: release a=unstable\nPin-Priority: 90\n' > /etc/apt/preferences.d/limit-unstable

apt update && apt install wireguard-tools --no-install-recommends && apt install resolvconf

### centos7

yum install wireguard-tools

### wg0.conf

vim /etc/wireguard/wg0.conf

### systemd

vim /lib/systemd/system/wg-quick@.service

![systemd](https://github.com/1219099256/wireguard-go/raw/master/systemd.png)

Environment=WG_I_PREFER_BUGGY_USERSPACE_TO_POLISHED_KMOD=1

systemctl enable wg-quick@wg0

systemctl start wg-quick@wg0

systemctl status wg-quick@wg0

wg-quick up wg0

wg-quick down wg0

#

# Go Implementation of [WireGuard](https://www.wireguard.com/)

This is an implementation of WireGuard in Go.

## Usage

Most Linux kernel WireGuard users are used to adding an interface with `ip link add wg0 type wireguard`. With wireguard-go, instead simply run:

```
$ wireguard-go wg0
```

This will create an interface and fork into the background. To remove the interface, use the usual `ip link del wg0`, or if your system does not support removing interfaces directly, you may instead remove the control socket via `rm -f /var/run/wireguard/wg0.sock`, which will result in wireguard-go shutting down.

To run wireguard-go without forking to the background, pass `-f` or `--foreground`:

```
$ wireguard-go -f wg0
```

When an interface is running, you may use [`wg(8)`](https://git.zx2c4.com/wireguard-tools/about/src/man/wg.8) to configure it, as well as the usual `ip(8)` and `ifconfig(8)` commands.

To run with more logging you may set the environment variable `LOG_LEVEL=debug`.

## Platforms

### Linux

This will run on Linux; however you should instead use the kernel module, which is faster and better integrated into the OS. See the [installation page](https://www.wireguard.com/install/) for instructions.

### macOS

This runs on macOS using the utun driver. It does not yet support sticky sockets, and won't support fwmarks because of Darwin limitations. Since the utun driver cannot have arbitrary interface names, you must either use `utun[0-9]+` for an explicit interface name or `utun` to have the kernel select one for you. If you choose `utun` as the interface name, and the environment variable `WG_TUN_NAME_FILE` is defined, then the actual name of the interface chosen by the kernel is written to the file specified by that variable.

### Windows

This runs on Windows, but you should instead use it from the more [fully featured Windows app](https://git.zx2c4.com/wireguard-windows/about/), which uses this as a module.

### FreeBSD

This will run on FreeBSD. It does not yet support sticky sockets. Fwmark is mapped to `SO_USER_COOKIE`.

### OpenBSD

This will run on OpenBSD. It does not yet support sticky sockets. Fwmark is mapped to `SO_RTABLE`. Since the tun driver cannot have arbitrary interface names, you must either use `tun[0-9]+` for an explicit interface name or `tun` to have the program select one for you. If you choose `tun` as the interface name, and the environment variable `WG_TUN_NAME_FILE` is defined, then the actual name of the interface chosen by the kernel is written to the file specified by that variable.

## Building

This requires an installation of [go](https://golang.org) ≥ 1.13.

```
$ git clone https://git.zx2c4.com/wireguard-go
$ cd wireguard-go
$ make
```

## License

    Copyright (C) 2017-2020 WireGuard LLC. All Rights Reserved.

    Permission is hereby granted, free of charge, to any person obtaining a copy of
    this software and associated documentation files (the "Software"), to deal in
    the Software without restriction, including without limitation the rights to
    use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
    of the Software, and to permit persons to whom the Software is furnished to do
    so, subject to the following conditions:

    The above copyright notice and this permission notice shall be included in all
    copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
    IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
    FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
    AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
    LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
    OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
    SOFTWARE.




一、安装wireguard-go

Github地址：https://github.com/WireGuard/wireguard-go

编译安装
一些需要的软件yum install -y wget git make
如果是debian/ubuntu：apt install -y wget git make
1.安装golang1.16

wget https://golang.org/dl/go1.16.1.linux-amd64.tar.gz
tar xvf go1.16.1.linux-amd64.tar.gz -C /usr/local
cat <<EOF >> /etc/profile
#golang env config
export GO111MODULE=on
export GOROOT=/usr/local/go 
export GOPATH=~/gopath
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
EOF
source /etc/profile
2.拉取代码并编译

git clone https://git.zx2c4.com/wireguard-go.git
git checkout 0.0.20201118
cd wireguard-go
make
mv wireguard-go /usr/local/sbin
下载编译好的二进制文件安装
一些需要的软件yum install -y wget
如果是debian/ubuntu：apt install -y wget

wget https://github.com/peng4740/wireguard-go-builder/releases/download/0.0.20201118/wireguard-go-linux-amd64.tar.gz
tar zxf wireguard-go-linux-amd64.tar.gz
mv wireguard-go /usr/local/sbin
rm -f wireguard-go-linux-amd64.tar.gz
二、安装wgcf

这个是用来生成warp配置的
Github地址：https://github.com/ViRb3/wgcf

安装
wget https://github.com/ViRb3/wgcf/releases/download/v2.2.2/wgcf_2.2.2_linux_amd64 -O /usr/local/sbin/wgcf
chmod +x /usr/local/sbin/wgcf
三、用wgcf生成配置
echo|wgcf register
wgcf generate
sed -i '/0\.0\.0\.0\/0/d' wgcf-profile.conf
#sed -i '/\:\:\/0/d' wgcf-profile.conf # 如果是IPV6VPS要添加IPV4则改用这个，上一条不要执行
mkdir -p /etc/wireguard
cp -f wgcf-profile.conf /etc/wireguard/wgcf.conf
安装wireguard-tools

CentOS7：yum install -y wireguard-tools
如果提示找不到包安装失败了可能是没有epel源，先安装yum install epel-release -y

其他系统其实也是差不多的

Debian:

echo "deb http://deb.debian.org/debian/ unstable main" > /etc/apt/sources.list.d/unstable.list
printf 'Package: *\nPin: release a=unstable\nPin-Priority: 150\n' > /etc/apt/preferences.d/limit-unstable
apt update
apt install -y wireguard
Ubuntu：apt install -y wireguard

没说到系统可参考官网教程：https://www.wireguard.com/install/

四、运行测试

1.启动

wg-quick up wgcf
如果运行完就失联了(VPS的SSH终端没反应了)。先重启VPS，检查一下配置有没有问题，可以复制评论给嗷嗷看看。
当然，也不用太担心，如果你没漏掉什么步骤，一般是不会有事的。

2.测试

curl ipv6.ip.sb
如果能正常显示ip就正常

五、配置开机自启(务必要运行测试过后再配置)

systemctl enable wg-quick@wgcf
