# file browser

1. 下载filebrowser镜像

```bash
docker pull filebrowser/filebrowser
```

2. 创建filebrowser挂载所需要的目录

```bash
mkdir filebrowser
```

3. 启动(默认账户密码:admin)

```bash
docker run -d -v /root/filebrowser/sites/root:/srv -v /root/filebrowserconfig.json:/etc/config.json -v /root/filebrowser/database.db:/etc/database.db -p 8080:80 filebrowser/filebrowser
```
