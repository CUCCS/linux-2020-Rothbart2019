# 第六章：shell脚本编程练习进阶实验报告
### yum安装的配置
### 编辑文件
```
#vi /etc/yum.repos.d/packagekit-media.repo
 #mv /etc/yum.repos.d/packagekit-media.repo /etc/yum.repos.d/yum.repo
```
### 文件内容如下：
```
[cdrom]
name=cdrom
baseurl=file:///mnt/cdrom
gpgcheck=0
enabled=1
```
### 修改一下文件权限
```
#chmod 777 /etc/yum.repos.d/yum.repo
```
## 一、安装与配置Samba服务器
 
### 1、安装服务 #yum install samba -y
 
### 2、重启服务    #service smb restart
 
### 3、创建共享目录 /var/samba/shared   
```
#mkdir /var/samba/shared -p
#chmod 777 /var/samba/shared
```
### 4、添加登陆用户  qhm （需为系统用户）
```
#smbpasswd -a qhm
``` 
### 5、配置  /etc/samba/smb.conf  文件
### 建议先把原文件删除或备份，然后新建smb.conf文件
```
#vi /etc/samba/smb.conf
``` 
### 文件内容如下：
```
[globa]
workgroup=WORKGROUP
server string=Samba Server Version %v
netbios name=MYSERVER
security=user
passdb backend=tdbsam
encrypt passord=yes
username map=/etc/samber/smbusers
 
[shared]
comment=Public Stuff
browseable=yes
path=/var/samba/shared
public=yes
writable=yes
``` 
### 6、最后
### 重启服务  #service smb restart
### 关防火墙  #service iptables stop
### 清规则    #setenforce 0
 
 
 
## 二、安装与配置DHCP服务器
 
### 1、安装服务
```
#rpm -ivh dhcp-4.1.1-12.P1.el6.i686.rpm
#rpm -qa|grep dhcp
``` 
### 2、替换配置文件
```
#cp /usr/share/doc/dhcp-4.1.1/dhcpd.conf.sample /etc/dhcp/dhcpd.conf
``` 
### 3、编辑主文件
```
#vi /etc/dhcp/dhcpd.conf
```
文件内容如下（改好自己对应的IP）：
```
option domain-name "example.org";
option domain-name-servers 114.114.114.114, ns2.exaAmple.orgA;
 
default-lease-time 600;
max-lease-time 7200;
 
ddns-update-style none;
ignore client-updates;
log-facility local7;
 
subnet 192.168.30.0 netmask 255.255.255.0 {
  range 192.168.30.200 192.168.30.220;
  option routers 192.168.30.254, rtr-239-0-2.example.org;
}
```
### 4、最后
### 重启服务    
```            
            #service dhcpd restart
            #service iptables stop
            #setenforce 0
``` 
## 三、安装与配置DNS服务器
### 1、安装服务
```
# rpm -ivh bind-9.7.0-5.P2.el6.i686.rpm 或
#yum install bind
``` 
### 2、重启服务
```
#service named restart
``` 
### 3、配置主文件
```
#vi /etc/named.conf
```
### 需要添加或修改的内容如下：
``` 
listen-on port 53 {any;};
listen-on-v6 port 53 { any; };
allow-query     { any; };
 
zone "qhm.com" IN {
        type master;
        file "named.qhm.com";
        allow-update { none; };
};
 
zone "192.168.30.in-addr.arpa" IN{
        type master;
        file "named.192.168.30";
        allow-update { none; };
};
```
 
### 4、编辑正向区域文件
```
#vi /var/named/named.qhm.com
```
### 文件内容如下：
```
$TTL 1D
@       IN SOA  @ rname.invalid. (
                                0       ; serial
                                1D      ; refresh
                                1H      ; retry
                                1W      ; expire
                                3H )    ; minimum
                        NS      @
                        A       127.0.0.1
                        AAAA    ::1
www             IN      A       192.168.30.66
``` 
### 5、编辑反向区域文件
```
#vi /var/named/named.192.168.30
```
### 文件内容如下：
```
$TTL 1D
@       IN SOA  @ rname.invalid. (
                                0       ; serial
                                1D      ; refresh
                                1H      ; retry
                                1W      ; expire
                                3H )    ; minimum
                        NS      @
                        A       127.0.0.1
                        AAAA    ::1
66                     IN      PTR      www.qhm.com
``` 
 
### 6、 修改named.qhm.com属性  
```
#chgrp named named.qhm.com
``` 
### 7、修改dns解析
```
#vi /etc/resolv.conf
```
       
 
### 8、最后
### 重启服务  #service named restart
### 关防火墙  #service iptables stop
### 清规则    #setenforce 0
 
 
 
## 四、安装与配置 FTP 服务器
 
### 1、 安装FTP服务
```
#yum install vsftpd
``` 
### 2、 创建共享文件 
```
#chmod 777 /var/ftp/pub/
``` 
### 3、 编辑主文件
```
#vi /etc/vsftpd/vsftpd.conf
``` 
### 需要修改或添加的内容如下：
``` 
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
chroot_local_user=YES
userlist_enable=YES
userlist_deny=NO
``` 
### 4、将允许登陆用户名加入文件
```
#vi /etc/vsftpd/user_list
``` 
### 5、最后
```
#setsebool -P ftp_home_dir=1
```
### 重启服务  #service vsftpd restart
### 关防火墙  #service iptables stop
### 清规则    #setenforce 0
 
 
 
## 五、安装与配置 Web服务器
 
### 1 、先把原来的服务卸载
```
#yum remove httpd
```
### 2、安装httpd服务
```
#yum install httpd -y
``` 
```
#yum install mod_ssl -y
```
### 查看是否安装成功 #yum info httpd
 
### 3 、启动或重启服务
```
#service httpd start
#service httpd restart
``` 
### 4 、主文件配置
```
#vi /etc/httpd/conf/httpd.conf
``` 
### 后面添加内容如下：
```
#网站1：对应IP192.168.30.66，根目录qhm1，里面的网页自己去新建
<VirtualHost 192.168.30.66:80>               #换成自己的IP
    DocumentRoot /var/www/html/qhm1      #换成自己的目录
    ServerName qhm1                      #换成自己的目录
</VirtualHost>
#网站2：对应IP192.168.30.55，根目录qhm2，里面的网页自己去新建
<VirtualHost 192.168.30.55:80>                 #换成自己的IP
    DocumentRoot /var/www/html/qhm2        #换成自己的目录
    ServerName qhm2                        #换成自己的目录
</VirtualHost>
```
### 到这里可以去浏览器打开自己的网站是否开启成功！
 
### 5、安装MySQL数据库
### 先把原来的卸载
```
#yum remove mysql
``` 
### 开始安装
```
#yum install mysql-server -y
``` 
### 开启服务
```
#service mysqld start
``` 
### 进入数据库命令模式，测试是否成功
```
#mysql -u root
``` 
 
### 6、php语言环境的安装
 
### 7、配置安装管理系统
### 进入MySQL命令模式
```
#mysqladmin -u root passwoed 'password'
#mysql -uroot -ppassword
``` 
### 创建joomla数据库
```
#grant all on joomla.* to joomla@localhost identified by'joomlapwd';
``` 
### 下载管理系统

### 然后测试
### 在浏览器进入系统的目录，如果出现系统安装界面说明成功
