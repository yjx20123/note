检查服务器地址

```
ifconfig ens33
```

一、编辑配置文档vsftpd.conf,开启匿名用户相关权限

```
vim /etc/vsftpd/vsftpd.conf
```

vim的末行输入`:set nu`

```
12行表示已经开启匿名用户

19行允许执行写操作

29行去掉注释，允许匿名用户上传

33行去掉注释，允许匿名用户创建和写文件。

加上一行，允许用户更名、删除文件

34行写：anon_other_write_enable=yes

保存退出
```

二、创建匿名上传目录，并修改权限。

`mkdir /var/ftp/incoming`

为了保证其他人对文件夹有写的权限

```
chmod o+w /var/ftp/incoming
     ll /var/ftp
```

三、设置selinux为允许，设置防火墙放行ftp服务,重启vsftpd服务

```
setenforce 0
firewall-cmd --permanet --add-service=ftp
firewall-cmd --reload:重新加载防火墙规则
firewall-cmd --list-all:查看防火墙设置是否成功
重启ftp服务：systemctl restart vsftpd
```

四、进行客户端测试

打开一台Linux客户端，保证ip地址和服务器地址在同一网段

```
S1:ifconfig ens33:查看客户机IP地址
S2：ftp (ftp服务器IP地址)
注意：如果客户机没有安装ftp客户端，则执行yum -y install ftp 
S3：输入匿名账号ftp
S4:进入ftp命令下开始操作
```

```
1.ls查看ftp服务器，发现incoming目录
2.cd incoming
3.mkdir mydir
4.ls
5.rename mydir,修改为yourdir
6.ls
以上匿名用户在ftp服务器上创建、修改目录成功
```

