# portainer

> 安装可视化工具的前提是已经拥有docker

## 拉取镜像

```sh
docker pull portainer/portainer-ce
```

## 创建一个数据卷

```sh
docker volume create portainer_data
```

## 启动portainer容器

```sh
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

## 访问:`http://ip:9000`

> 创建账户密码就好了（密码至少12位）