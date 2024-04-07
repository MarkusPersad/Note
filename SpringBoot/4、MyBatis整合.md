# SpringBoot整合Mybatis

## 依赖配置

`pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.2.4</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.markus</groupId>
	<artifactId>server</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>server</name>
	<description>server</description>
	<properties>
		<java.version>22</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>3.0.3</version>
		</dependency>

		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter-test</artifactId>
			<version>3.0.3</version>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.postgresql</groupId>
			<artifactId>postgresql</artifactId>
			<scope>runtime</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```

**我选择的是PostgreSQL数据库，还有许多其他数据库例如`MySQL`更换数据库时只需要更改数据库依赖即可**

## 测试数据准备

```postgresql
create table "user"
(
    id     serial
        primary key,
    name   varchar(100),
    age    smallint,
    gender smallint
        constraint user_gender_check
            check (gender = ANY (ARRAY [1, 2])),
    phone  varchar(11)
);

comment on column "user".id is 'ID';

comment on column "user".name is '姓名';

comment on column "user".age is '年龄';

comment on column "user".gender is '性别, 1:男, 2:女';

alter table "user"
    owner to postgres;
INSERT INTO "user"(name, age, gender, phone) VALUES ('白眉鹰王',55,1,'18800000000');
INSERT INTO "user"(name, age, gender, phone) VALUES ('金毛狮王',45,1,'18800000001');
INSERT INTO "user"(name, age, gender, phone) VALUES ('青翼蝠王',38,1,'18800000002');
INSERT INTO "user"(name, age, gender, phone) VALUES ('紫衫龙王',42,2,'18800000003');
INSERT INTO "user"(name, age, gender, phone) VALUES ('光明左使',37,1,'18800000004');
INSERT INTO "user"(name, age, gender, phone) VALUES ('光明右使',48,1,'18800000005');
```

## 项目结构

![](D:\Note\images\屏幕截图 2024-04-07 150014.png)

## 数据库JDBC配置

`application.yaml`

```yaml
spring:
  datasource:
    driver-class-name: org.postgresql.Driver #依据数据库的不同更改
    #url: jdbc:{SQLNAME}://localhost:{Port}/{database}
    url: jdbc:postgresql://localhost:5432/mybatis
    username: postgres
    password: 123456
```

## 数据库映射

`User.java`

```java
package com.markus.userDao;

public class User {
    private Integer id;
    private String name;
    private short ahe;
    private short gender;
    private String phone;

    public User(Integer id, String name, short ahe, short gender, String phone) {
        this.id = id;
        this.name = name;
        this.ahe = ahe;
        this.gender = gender;
        this.phone = phone;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAhe(short ahe) {
        this.ahe = ahe;
    }

    public void setGender(short gender) {
        this.gender = gender;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    public Integer getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public short getGender() {
        return gender;
    }

    public short getAhe() {
        return ahe;
    }

    public String getPhone() {
        return phone;
    }
}
```

**注意`User`类里的`属性`必须和表里的`字段`一致**

`USerMapper.java`

```java
package com.markus.mapper;

import com.markus.userDao.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

@Mapper
public interface UserMapper {
    @Select("select * from \"user\" where id=#{id}")
    public User findById(Integer id);
    @Insert("SQL语句")
    public User insert(/..../);
    @Delete("SQL语句")
    public User deleteUser(/..../);
    @Update("SQL语句")
    public User(/..../);
}

```

一系列的`SQL`在`@Mapper`的类下执行

## 业务代码

`UserService.java`

```java
package com.markus.userService;

import com.markus.userDao.User;

public interface UserService {
    public User findById(Integer id);
}
```

`UserServiceImpl.java`

```java
package com.markus.userService.impl;

import com.markus.mapper.UserMapper;
import com.markus.userDao.User;
import com.markus.userService.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserMapper userMapper;
    @Override
    public User findById(Integer id) {
       return userMapper.findById(id);
    }
}
```

`UserController.java`

```java
package com.markus.Controller;

import com.markus.userDao.User;
import com.markus.userService.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {
    @Autowired
    private UserService userService;
    @RequestMapping("findById")
    public User findById(Integer id){
        return userService.findById(id);
    }
}
```

**默认是`History`模式的传参,直接以`http://localhost:8080/start/findById?id=?`发起请求**

`SpringBootApplication.java`

```java
package com.markus;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class ServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServerApplication.class, args);
	}

}
```

