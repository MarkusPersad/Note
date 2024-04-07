# SpringBoot配置文件

## application.properties

```properties
spring.application.name={PROJECT_NAME}
server.port=8080 //服务端口号
server.servlet.context-path=/start //服务的内容起始路径
/..../
```

## application.yml/application.yaml

```yaml
server:
	port: 8080
	servlet:
		context-path: /start 
/..../
```

## 比较

更推荐yaml格式

- 结构清晰
- 层次分明
- 更关注数据
- 键重复度低

## yaml配置文件的读和写

### 三方技术配置信息



### 自定义信息

**用以数据与程序的解耦合，以便在不修改源代码的情况下灵活的配置数据，无需重新打包**

以EMAIL发送为例子

![](..\\images\\屏幕截图 2024-04-07 112448.png)

`MailUtils.java`

```java
package com.markus.server.service.utils;

import com.markus.server.service.pojo.EmailProperties;
public class MailUtil {
    /**
     * @param emailProperties 发件人信息（邮箱和授权码）以及邮件服务器信息（服务器域名和身份认证开关）
     * @param to
     * @param title
     * @param content
     * @return
     */
    public static boolean sendMail(EmailProperties emailProperties, String to, String title, String content) {
        System.out.println(emailProperties.toString());
        System.out.println(to);
        System.out.println(title);
        System.out.println(content);
        return true;
    }
}
```

`EmailProperties.java`

```java
package com.markus.server.service.pojo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
public class EmailProperties {
    @Value("${email.user}")
    public String user;
    @Value("${email.code}")
    public String code;
    @Value("${email.host}")
    public String host;
    @Value("${email.auth}")
    public boolean auth;

    public String getUser() {
        return user;
    }

    public String getCode() {
        return code;
    }

    public String getHost() {
        return host;
    }

    public boolean isAuth() {
        return auth;
    }

    @Override
    public String toString() {
        return "EmailProperties{" +
                "user='" + user + '\'' +
                ", code='" + code + '\'' +
                ", host='" + host + '\'' +
                ", auth=" + auth +
                '}';
    }
}
```

`EmailService.java`

```java
package com.markus.server.service;

public interface EmailService {
    boolean send(String to,String title,String content);
}
```

`EmailServiceImpl.java`

```java
package com.markus.server.service.impl;


import com.markus.server.service.EmailService;
import com.markus.server.service.pojo.EmailProperties;
import com.markus.server.service.utils.MailUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class EmailServiceImpl implements EmailService {
    @Autowired
    private EmailProperties emailProperties;
    /**
     * @param to 收件人邮箱
     * @param title 邮件标题
     * @param content 邮件正文
     * @return
     */
    @Override
    public boolean send(String to, String title, String content) {
        MailUtil.sendMail(emailProperties,to,title,content);
        return true;
    }
}
```

`EmailController.java`

```java
package com.markus.server.controller;

import com.markus.server.service.EmailService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class EmailController {
    @Autowired
    private EmailService emailServiceervice;
    @RequestMapping("/send")
    public boolean send(){
        emailServiceervice.send("222","test","SSS");
        return true;
    }
}
```

`application.yaml`

```yaml
spring:
  application:
    name: server
server:
  servlet:
    context-path: /start
  port: 8080
email:
  user: 2904644926@qq.com
  code: sdadrfasfadsa
  host: stmq.qq.com
  auth: true
```

#### 写配置

```yaml
email:
  user: 2904644926@qq.com
  code: sdadrfasfadsa
  host: stmq.qq.com
  auth: true
```

`email`为配置信息的第一层级，为的是**可阅读性和避免键名冲突**

**键和值之间有`:`和**` `

`application.yaml`也可以写**数组**信息

```yaml
hobbies:
  - playgames
  - rap
  - basketball
  - football
```

**数组的每一项以`-`注明，值前有一个缩进**

#### 读配置

①`@Value(${KEY})`注解

```java
@Component
public class EmailProperties {
    @Value("${email.user}")
    public String user;
    @Value("${email.code}")
    public String code;
    @Value("${email.host}")
    public String host;
    @Value("${email.auth}")
    public boolean auth;

    public String getUser() {
        return user;
    }

    public String getCode() {
        return code;
    }

    public String getHost() {
        return host;
    }

    public boolean isAuth() {
        return auth;
    }

    @Override
    public String toString() {
        return "EmailProperties{" +
                "user='" + user + '\'' +
                ", code='" + code + '\'' +
                ", host='" + host + '\'' +
                ", auth=" + auth +
                '}';
    }
}
```

②`ConfigurationProperties(prefix="email(键名前缀)")`

```java
package com.markus.server.service.pojo;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@ConfigurationProperties(prefix = "email")
@Component
public class EmailProperties {
//    @Value("${email.user}")
    private String user;
//    @Value("${email.code}")
    private String code;
//    @Value("${email.host}")
    private String host;
//    @Value("${email.auth}")
    public boolean auth;

    public String getUser() {
        return user;
    }

    public String getCode() {
        return code;
    }

    public String getHost() {
        return host;
    }

    public boolean getAuth() {
        return auth;
    }

    @Override
    public String toString() {
        return "EmailProperties{" +
                "user='" + user + '\'' +
                ", code='" + code + '\'' +
                ", host='" + host + '\'' +
                ", auth=" + auth +
                '}';
    }

    public void setUser(String user) {
        this.user = user;
    }

    public void setCode(String code) {
        this.code = code;
    }

    public void setHost(String host) {
        this.host = host;
    }

    public void setAuth(boolean auth) {
        this.auth = auth;
    }
}
```

**`class`的成员属性名应该与配置文件中的键名保持一致**

**注意`ConfigurationProperties(prefix="{prefixName}")`是通过`Setter`和`Getter`对`Property`进行赋值操作的所以`class`中的`Setter`和`Getter`要有都有，要没有都没有**

