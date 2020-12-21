

### FTP服务的安装与启动

1.rpm -qa|grep vsftpd:检查是否安装ftp服务，若没有安装，则接下来采用yum的方式安装软件包

- 首先挂接系统光盘
- 创建挂载点 mkdir /mnt/cdrom
- 挂载 mount /dev/cdrom /mnt/cdrom
- 若挂载不成功，则从设置中检查cd/DVD是否配置了ISO映像文件

2.创建yum源文件dvd.repo

- vim /etc/yum/repos.d/dvd.repo

- 然后加入以下代码

  ```
  [dvd]
  name=soft
  baseurl=file:///mnt/cdrom
  gpgcheck=0
  enabled=1
  ```

3.安装前使用清空缓存

```
yum clean all
```

4.安装ftp服务

```
yum install vsftpd 
```

5.启动ftp服务

```
systemctl start vsftpd
```

6.查看ftp服务的状态

```
systemctl status vsftpd
```

7.启动vsftpd服务

```
systemctl enable vsftpd
```

8.设置selinux允许ftp

- 临时设置

  setenforce 0

- 查看

  getenforce

- 临时设置重启系统后失效

- 永久设置，修改配置文件

```
vim /etc/sysconfig/selinux

```

修改 SELINUX=enforcing

为：SELINUX=disabled,保存退出即可。



