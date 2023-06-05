# 一个开源的Springboot项目
>
> 写着玩的一个项目，本来打算整一个管理系统，但是发现索然无味。
> 于是我就写了一个抽卡模拟器，根据我概率计算游戏中的概率不太对劲。
> 但是我感觉概率最为奇葩的是掉线城，策划暗调概率，不得不说这个游戏中
> 策划也是一大特色。
>

## 玩了什么&怎么玩的

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

6. 记得**禁用你的虚拟机网卡**，然后访问<http://localhost:8088>

7. 但是你的日志没有保存下来

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration scan="true" scanPeriod="60 seconds" debug="false">
       <contextName>logback</contextName>
       <!--输出到控制台-->
       <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
           <encoder>
               <!--<pattern>%d{HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>-->
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger - %msg%n</pattern>
               <!--            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} -%5p ${PID:-} [%15.15t] %-30.30C{1.} : %m%n</pattern>-->
           </encoder>
       </appender>
   
       <!--按天生成日志-->
       <appender name="logFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
           <file>logs/output.log</file>
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <FileNamePattern>
                   logs/%d{yyyy-MM-dd}/%d{yyyy-MM-dd}.log
               </FileNamePattern>
               <maxHistory>14</maxHistory>
           </rollingPolicy>
           <encoder>
               <!-- pattern节点，用来设置日志的输入格式 ps:日志文件中没有设置颜色,否则颜色部分会有ESC[0:39em等乱码-->
               <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level --- [%15.15(%thread)] %-40.40(%logger{40}) : %msg%n</pattern>
               <!-- 记录日志的编码:此处设置字符集 - -->
               <charset>UTF-8</charset>
           </encoder>
   
    <!--       <layout class="ch.qos.logback.classic.PatternLayout">
               <Pattern>
                   %d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger - %msg%n
               </Pattern>
           </layout>-->
       </appender>
   
       <root level="INFO">
           <appender-ref ref="console"/>
           <appender-ref ref="logFile"/>
       </root>
   
   </configuration>
   ```

8. 配置application.yml

   ```yml
   logging:
     file:
       name: logs/output.log
   ```

   

### Redis

1. 创建工作目录`mkdir -p /apps/redis/{data,conf,logs}`

2. 准备docker-compose文件

   ```yml
   version: '3'
   services:
     redis:
       image: redis:6.2.6
       container_name: redis
       volumes:
         - /apps/redis/data:/data
         - /apps/redis/conf/redis.conf:/usr/local/etc/redis/redis.conf
         - /apps/redis/logs:/logs
       command: redis-server --requirepass yourpass
     #   #  两个写入操作 只是为了解决启动后警告 可以去掉
     #    /bin/bash -c "echo 511 > /proc/sys/net/core/somaxconn
     #    && echo never > /sys/kernel/mm/transparent_hugepage/enabled
     #    && redis-server /usr/local/etc/redis/redis.conf"
       ports:
         # 使用宿主机的端口映射到容器的端口
         # 宿主机：容器
         - 6379:6379
   ```

   

### MySQL

> MYSQL version8.0

|  手动编译(非氪金)   | 宝塔一键安装(非氪金)  | docker-compose(非氪金)  | （氪金）云MYSQL |
|  ----  | ----  | ----  | ----  |
| 不省事  | 省事 | 不太省事 | 很省事  |

### Springboot

1. 常用的

   ```xml
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.7.7</version>
           <relativePath/> <!-- lookup parent from repository -->
       </parent>
       <dependencies>
        <!--SpringBoot框架web项目起步依赖，通过该依赖自动关联其它依赖，不需要我们一个一个去添加了-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <!--SpringBoot框架的测试起步依赖，例如：junit测试，如果不需要的话可以删除-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-test</artifactId>
               <scope>test</scope>
           </dependency>
           <!--Springboot aop-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-aop</artifactId>
           </dependency>
           </dependencies>
   ```

   

### ~~jsch~~

> 这个依赖就是引入的ssh连接 我写了一个备份数据库的脚本（可以是其他任意脚本，给脚本设置定时任务不就需要这大费周章了，感觉配置很麻烦，速度也很慢）

```bash
        <dependency>
            <groupId>com.jcraft</groupId>
            <artifactId>jsch</artifactId>
            <version>0.1.55</version>
        </dependency>
```
