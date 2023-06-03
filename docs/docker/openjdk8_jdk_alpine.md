# Dockerfile使用openjdk:8-jdk-alpine出现……

>我的Springboot项目出现了问题。当我请求线上验证码的时候出现了空指针异常（后期断点排查的出来的），刚开始我试了试本地的项目是完全好用的，我怀疑是拦截器的问题（jdk怎么可能背叛我呢！！！:smile:），从度娘上搜索了一堆答案，但是没有解决我的问题。无意间我发现我Dockerfile使用的是 **openjdk:8-jdk-alpine**  我尝搜索了一下**openjdk:8-jdk-alpine**。

![](http://typecho.128x.cn/2023/04/202304134511_8153.png)

现在Dockerfile使用的镜像

```
FROM  williamyeh/java8:latest
```

完整的Dockerfile文件
>包含远程调试

```sh
# 基础镜像
FROM  williamyeh/java8:latest
VOLUME /tmp/springboot
# 作者信息
MAINTAINER zhanglaodi
# 容器目录分配数据卷
# VOLUME /tmp
# 设置时区
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN echo 'Asia/Shanghai' >/etc/timezone
# 拷贝jar 把可执行jar包复制到基础镜像的根目录下
ADD target/onepiece-1.0-SNAPSHOT.jar app.jar
ADD setup.sh setup.sh
RUN chmod -x setup.sh
# 设置暴露的端口号
EXPOSE 19999 1005
RUN sh -c 'touch /app.jar'
# 在镜像运行为容器后执行的命令
ENTRYPOINT ["sh","./setup.sh"]
```

#### 脚本`setup.sh` 
```sh
#!/bin/bash

java  -Djava.security.egd=file:/dev/./urandom -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=1005 -jar app.jar
```