## 版本

mysql版本：5.1.47

redis版本：2.6.2

mysql和redis可以在docker desktop中创建。







## 前端

```bash
start nginx.exe
```

 然后访问

```
http://127.0.0.1:8080/
```

在空白页面点击鼠标右键，选择检查，即可打开开发者工具：

然后打开手机模式。



## 后端



### 数据库

运行hmdp.sql



### 进后端目录

```
cd D:\program\backend\projects\hmdp\code\hm-dianping
```



### 在 IDEA设置：

顶部菜单：

```
File
 ↓
Project Structure
```

快捷键：

```
Ctrl + Alt + Shift + S
```

------

进入：

```
Project
```

你会看到：

#### SDK

现在可能是：

```
1.25 (missing)
```

点击右侧下拉。

选择：

```
Add SDK
    ↓
JDK
```

------

选择你的 JDK 目录：

应该是：

```
D:\Program Files\Java\jdk1.8.0_181
```

确定。

------

然后设置：

```
Project language level
```

选择：

```
8 - Lambdas, type annotations etc.
```





#### JDK 设置在哪里？

新版 IDEA 通常放到了：

```
Settings
 ↓
Build, Execution, Deployment
 ↓
Build Tools
 ↓
Maven
 ↓
Runner
```

里面。

看有没有：

```
JRE
```

或者：

```
Java runtime
```

设置成：

```
Project SDK (1.8)
```







### yaml改成

```yaml
server:
  port: 8081
spring:
  application:
    name: hmdp
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://127.0.0.1:3306/hmdp?useSSL=false&serverTimezone=UTC
    username: root
    password: 123
  redis:
    host: 127.0.0.1
    port: 6379
    password: 123321
    lettuce:
      pool:
        max-active: 10
        max-idle: 10
        min-idle: 1
        time-between-eviction-runs: 10s
  jackson:
    default-property-inclusion: non_null # JSON处理时忽略非空字段
mybatis-plus:
  type-aliases-package: com.hmdp.entity # 别名扫描包
logging:
  level:
    com.hmdp: debug
  pattern:
    dateformat: mm:ss.SSS
```



### RedissionConfig.java改成

```java
@Configuration
public class RedissonConfig {

    @Bean
    public RedissonClient redissonClient(){
        // 配置
        Config config = new Config();
        config.useSingleServer().setAddress("redis://127.0.0.1:6379").setPassword("123321");
        // 创建RedissonClient对象
        return Redisson.create(config);
    }
}
```







### 手动在 Redis 创建 Stream 和消费者组。

------

#### 第一步：进入 Redis

PowerShell：

```
docker exec -it hmdp-redis redis-cli -a 123321
```

进入：

```
127.0.0.1:6379>
```

------

#### 第二步：创建 stream.orders

执行：

```
XGROUP CREATE stream.orders g1 0 MKSTREAM
```

解释：

```
stream.orders
```

Stream名字

```
g1
```

消费者组名字

```
MKSTREAM
```

如果不存在自动创建

------

成功：

应该返回：

```
OK
```

------

#### 第三步：确认

执行：

```
XINFO GROUPS stream.orders
```

应该看到：

```
1) 1) "name"
   2) "g1"
```

说明成功。