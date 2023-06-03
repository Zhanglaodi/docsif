# docker快速部署code-server

> 前提是你已经安装了docker

## 安装code-server

```sh
#密码就是PASSWORD后面的参数 8080是code-server容器端口 5172是vue项目的端口
docker run -itd --name coder-server -p 8082:8080 -p 5172:5172 -e PASSWORD=123 codercom/code-server:latest
```

## 安装nodejs

```sh
#!/bin/bash
# 安装更新Node.js和Npm所需要的软件
echo "update apt packge ..."
sudo apt update
# 添加NodeSource APT存储库和用于验证软件包的PGP密钥
echo "add NodeSource APT is PGP"
sudo apt install apt-transport-https curl ca-certificates software-properties-common
echo "add apt-get nodejs16.x and PGP" # 该行命令完成了apt-get存储库的添加并添加了PGP密钥
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
echo "install nodejs and npm...." # 安装Node
sudo apt-get install -y nodejs
```

## 安装yarn

```sh
sudo npm install --global yarn
```

