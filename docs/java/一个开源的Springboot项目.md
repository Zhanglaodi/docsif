# 一个开源的Springboot项目
>
> 写着玩的一个项目，本来打算整一个管理系统，但是发现索然无味。
> 于是我就写了一个抽卡模拟器，根据我概率计算游戏中的概率不太对劲。
> 但是我感觉概率最为奇葩的是掉线城，策划暗调概率，不得不说这个游戏中
> 策划也是一大特色。
>

## 玩了什么

### rabbitmq

> 这里的消息队列处理抽奖记录 一个接口过多的io操作实在是太鳗了,
> rabbitmq可以解决这个问题。

1. docker安装rabbitmq

```bash
docker run -d --hostname my-rabbit --name rabbit -p 15672:15672 -p 5673:5672 rabbitmq
```

2. 进入rabbitmq进行配置（解决500问题）

```bash
docker exec -it [containerId] /bin/bash
cd /etc/rabbitmq/conf.d/ //rabbitmq配置文件
echo management_agent.disable_metrics_collector = false > management_agent.disable_metrics_collector.conf
exit
docker restart [containerId] 
```

### springboot admin

1. IDEA新建一个模块springboot_admin（服务端口8088）

2. 导入依赖 **注意版本**

```xml
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-server</artifactId>
            <version>2.7.7</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

3. springboot_admin启动类添加注解以及配置application.yml**到此admin配置结束**

* 启动类

```java
@EnableAdminServer
```

* application.yml

```yml
server:
    port: 8088
```

4. 需要注册的项目依赖

```xml
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-client</artifactId>
            <version>2.7.7</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
```

5. application.yml配置

```yml
spring:
  boot:
    admin:
      client:
        url: http://localhost:8088
        instance:
          prefer-ip: true

management:
  info:
    env:
      enabled: true
  endpoints:
    web:
      exposure:
        include: '*'
  endpoint:
    health:
      show-details: always
```

6. 记得**关闭你的虚拟机**，然后访问<http://localhost:8088>

### redis

### mysql

### springboot

### jsch
