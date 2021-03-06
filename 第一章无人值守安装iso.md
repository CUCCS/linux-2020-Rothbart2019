# 无人值守安装iso
## 软件环境
### Virtualbox
### Ubuntu 18.04 Server 64bit

## 实验配置
### 1.在https://ubuntu.com/download/desktop下载ubuntu-16.04.1-server-amd64.iso
### 2.新增一块网卡，实现NAT+host-only双网卡配置
### 3.配置网卡，编辑/etc/netplan/01-netcfg.yaml,使网卡配置生效
```
sudo netplan apply
```
### 4.查询并记住第二块网卡IP地址
```
ifconfig
```
### 5.开启ssh服务
```
sudo apt-get install openssh-server
sudo service ssh open
```
### 6.通过putty连接linux机，hostname处填入第二块网卡的IP地址
### 7.通过psptf向linux机传输ubuntu-16.04.1-server-amd64.iso文件
```
put
```
## 实验过程
### 1.在当前用户目录下创建一个用于挂载iso镜像文件的目录
```
mkdir loopdir
```
### 2.挂载iso镜像文件到该目录
```
mount -o loop ubuntu-16.04.1-server-amd64.iso loopdir
```
### 3.创建一个工作目录用于克隆光盘内容
```
mkdir cd
```
### 4.同步光盘内容到目标工作目录
```
rsync -av loopdir/ cd
```
### 5.卸载iso镜像
```
umount loopdir
```
### 6.进入目标工作目录
```
cd cd/
```
### 7.编辑Ubuntu安装引导界面增加一个新菜单项入口
```
vim isolinux/txt.cfg
```
### 8.添加以下内容到该文件后保存退出
```
label autoinstall
    menu label ^Auto Install Ubuntu Server
    kernel /install/vmlinuz
append  file=/cdrom/preseed/ubuntu-server-autoinstall.seed
 debian-installer/locale=en_US
 console-setup/layoutcode=us
 keyboard-configuration/layoutcode=us
 console-setup/ask_detect=false
 localechooser/translation/warn-light=true
 localechooser/translation/warn-severe=true
 initrd=/install/initrd.gz root=/dev/ram rw quiet
```
### 9.提前阅读并编辑定制Ubuntu官方提供的示例preseed.cfg，并将该文件保存到刚才创建的工作目录,将编辑好的.seed文件传给LINUX机
```
psftp> put ubuntu-server-autoinstall.seed cd/preseed/ubuntu-server-autoinstall.seed
```
### 10.修改isolinux/isolinux.cfg，增加内容timeout 10（可选，否则需要手动按下ENTER启动安装界面）
### 11.重新生成md5sum.txt
```
cd ~/cd && find . -type f -print0 | xargs -0 md5sum > md5sum.txt
```
### 12.封闭改动后的目录到.iso
```
IMAGE=custom.iso
BUILD=~/cd/
mkisofs -r -V "Custom Ubuntu Install CD" \
        -cache-inodes \
        -J -l -b isolinux/isolinux.bin \
        -c isolinux/boot.cat -no-emul-boot \
        -boot-load-size 4 -boot-info-table \
        -o $IMAGE $BUILD
```
### 13.将生成的custom.iso文件下载至本机,就可在虚拟机中进行无人值守安装
```
psftp get custom.iso
```
## 实验问题

### 在很多步骤会报错permission denied 
### 解决：使用命令chmod命令修改权限



#### 201811123019
#### 黄赢