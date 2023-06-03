# Ubuntu

> 其实ubuntu可以很快速的安装的，保持轻量化。
>
> 换源可以大大节省你的等待的时间

## 修改源（换源）

setting->about->software updates


## 修改root密码

```sh
sudo passwd root
```

## 启动root账号

> 可以先下载一个vim

```sh
apt install vim
```

### 注释文件中的代码

第一个文件是`/etc/pam.d/gdm-password`

第二个文件是`/etc/pam.d/gdm-autologin`

```sh
#auth required pam_succeed_if.so user != root quiet_success
```

### 修改profile文件

注释掉或者删除行(光标移动那一行，dd就删除了)

```sh
mesg n 2＞ /dev/null || true
```

插入新的行

```sh
tty -s && mesg n || true
```

## ssh远程

### 安装openssh

```sh
#!/bin/bash

apt install openssh-server
```

### 修改配置文件

```text
#!/bin/bash

sudo vim /etc/ssh/sshd_config
```

把下面的代码修改为

```sh
#PermitRootLogin prohibit-password
```

```sh
PermitRootLogin yes
```

### 重启服务

```sh
sudo systemctl restart ssh
```

### 测试

```sh
ssh root@你的ip -p 端口(默认22)
```

## 一键配置脚本

以下是一键配置脚本，直接新建rootlogin.sh脚本文件，打开后把以下命令粘贴进去然后，运行脚本文件即可。

```sh
#!/bin/bash

 #set root password
sudo passwd root
 
#notes Document content
sudo sed -i "s/.*root quiet_success$/#&/" /etc/pam.d/gdm-autologin
sudo sed -i "s/.*root quiet_success$/#&/" /etc/pam.d/gdm-password
 
#modify profile
sudo sed -i 's/^mesg.*/tty -s \&\& mesg n \|\| true/' /root/.profile
 
#install openssh
sudo apt install openssh-server
 
#delay
sleep 1
 
#modify conf
sudo sed -i 's/^#PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config
 
#restart server
sudo systemctl restart ssh
```