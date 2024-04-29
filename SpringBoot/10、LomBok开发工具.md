# `LomBok`

## 简介

`Lombok`是一个`Java`库，能自动插入编辑器并构建工具，简化`Java`开发。通过添加注解的方式，不需要为类编写`getter`或`eques`方法，同时可以自动化日志变量。

## `Lombok`使用

### 引入`Maven`坐标

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.32</version>
    <scope>provided</scope>
</dependency>
```

### 安装插件

使用`Lombok`需要的开发环境**`Java+Maven+IntelliJ IDEA`或者`Eclipse(安装Lombok Plugin)`**

![安装lombok插件](../images/format,png)

### 解决编译时出错问题

编译时出错，可能是没有enable注解处理器。`Annotation Processors > Enable annotation processing`。设置完成之后程序正常运行。

![开启注解配置](../images/formart,png)

### 实例

不用`LomBok`

```java
public class User implements Serializable {

    private static final long serialVersionUID = -8054600833969507380L;

    private Integer id;

    private String username;

    private Integer age;

    public User() {
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", age=" + age +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        User user = (User) o;
        return Objects.equals(id, user.id) &&
                Objects.equals(username, user.username) &&
                Objects.equals(age, user.age);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, username, age);
    }

}
```

使用`LomBok`

```java
@Data
public class User implements Serializable {

    private static final long serialVersionUID = -8054600833969507380L;

    private Integer id;

    private String username;

    private Integer age;

}
```

自动化日志配置

```java
@Slf4j
@RestController
@RequestMapping(("/user"))
public class UserController {

    @GetMapping("/getUserById/{id}")
    public User getUserById(@PathVariable Integer id) {
        User user = new User();
        user.setUsername("风清扬");
        user.setAge(21);
        user.setId(id);

        if (log.isInfoEnabled()) {
            log.info("用户 {}", user);
        }

        return user;
    }

}
```

### 常用注解

`@Setter `注解在类或字段，注解在类时为所有字段生成setter方法，注解在字段上时只为该字段生成`setter`方法。
`@Getter` 使用方法同上，区别在于生成的是`getter`方法。
`@ToString` 注解在类，添加`toString`方法。
`@EqualsAndHashCode `注解在类，生成`hashCode`和`equals`方法。
`@NoArgsConstructor` 注解在类，生成无参的构造方法。
`@RequiredArgsConstructor `注解在类，为类中需要特殊处理的字段生成构造方法，比如final和被@NonNull注解的字段。
`@AllArgsConstructor `注解在类，生成包含类中所有字段的构造方法。
`@Data` 注解在类，生成`setter/getter、equals、canEqual、hashCode、toString`方法，如为final属性，则不会为该属性生成`setter`方法。
`@Slf4j `注解在类，生成log变量，严格意义来说是常量。`private static final Logger log = LoggerFactory.getLogger(UserController.class);`

## `Lombok`工作原理

在`Lombok`使用的过程中，只需要添加相应的注解，无需再为此写任何代码。自动生成的代码到底是如何产生的呢？

核心之处就是对于注解的解析上。`JDK5`引入了注解的同时，也提供了两种解析方式。

    运行时解析

运行时能够解析的注解，必须将`@Retention`设置为`RUNTIME`，这样就可以通过反射拿到该注解。`java.lang.reflect`反射包中提供了一个接口`AnnotatedElement`，该接口定义了获取注解信息的几个方法，`Class、Constructor、Field、Method、Package`等都实现了该接口，对反射熟悉的朋友应该都会很熟悉这种解析方式。

    编译时解析

编译时解析有两种机制，分别简单描述下：

1）`Annotation Processing Tool`

`apt`自`JDK5`产生，`JDK7`已标记为过期，不推荐使用，`JDK8`中已彻底删除，自`JDK6`开始，可以使用`Pluggable Annotation Processing API`来替换它，apt被替换主要有2点原因：

    api都在com.sun.mirror非标准包下
    没有集成到javac中，需要额外运行

2）`Pluggable Annotation Processing API`

`JSR 269`自`JDK6`加入，作为`apt`的替代方案，它解决了`apt`的两个问题，`javac`在执行的时候会调用实现了该`API`的程序，这样我们就可以对编译器做一些增强，`javac`执行的过程如下：

![img](../images/formats,png)

`Lombok`本质上就是一个实现了“`JSR 269 API`”的程序。在使用`javac`的过程中，它产生作用的具体流程如下：

    javac对源代码进行分析，生成了一棵抽象语法树（AST）
    运行过程中调用实现了“JSR 269 API”的Lombok程序
    此时Lombok就对第一步骤得到的AST进行处理，找到@Data注解所在类对应的语法树（AST），然后修改该语法树（AST），增加getter和setter方法定义的相应树节点
    javac使用修改后的抽象语法树（AST）生成字节码文件，即给class增加新的节点（代码块）

通过读`Lombok`源码，发现对应注解的实现都在`HandleXXX`中，比如`@Getter`注解的实现在`HandleGetter.handle()`。还有一些其它类库使用这种方式实现，比如`Google Auto、Dagger`等等。

## `Lombok`的优缺点

优点：

    能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法，提高了一定的开发效率
    让代码变得简洁，不用过多的去关注相应的方法
    属性做修改时，也简化了维护为这些属性所生成的getter/setter方法等

缺点：

    不支持多种参数构造器的重载
    虽然省去了手动创建getter/setter方法的麻烦，但大大降低了源代码的可读性和完整性，降低了阅读源代码的舒适度