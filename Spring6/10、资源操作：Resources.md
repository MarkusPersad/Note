# 资源操作：`@Resource`

## Spring Resources概述

![image-20221218154945878](../images/image-20221218154945878.png)

Java的标准java.net.URL类和各种URL前缀的标准处理程序无法满足所有对low-level资源的访问，比如：没有标准化的 URL 实现可用于访问需要从类路径或相对于 ServletContext 获取的资源。并且缺少某些Spring所需要的功能，例如检测某资源是否存在等。**而Spring的Resource声明了访问low-level资源的能力。**

## `Resource`接口

`Spring` 的 `Resource` 接口位于 `org.springframework.core.io` 中。 旨在成为一个更强大的接口，用于抽象对低级资源的访问。以下显示了`Resource`接口定义的方法

```java
public interface Resource extends InputStreamSource {

    boolean exists();

    boolean isReadable();

    boolean isOpen();

    boolean isFile();

    URL getURL() throws IOException;

    URI getURI() throws IOException;

    File getFile() throws IOException;

    ReadableByteChannel readableChannel() throws IOException;

    long contentLength() throws IOException;

    long lastModified() throws IOException;

    Resource createRelative(String relativePath) throws IOException;

    String getFilename();

    String getDescription();
}
```

`Resource`接口继承了`InputStreamSource`接口，提供了很多`InputStreamSource`所没有的方法。`InputStreamSource`接口，只有一个方法：

```java
public interface InputStreamSource {

    InputStream getInputStream() throws IOException;

}
```

**其中一些重要的方法：**

- `getInputStream()`: 找到并打开资源，返回一个`InputStream`以从资源中读取。预计每次调用都会返回一个新的`InputStream()`，调用者有责任关闭每个流
- `exists()`: 返回一个布尔值，表明某个资源是否以物理形式存在
- `isOpen()`: 返回一个布尔值，指示此资源是否具有开放流的句柄。如果为`true`，`InputStream`就不能够多次读取，只能够读取一次并且及时关闭以避免内存泄漏。对于所有常规资源实现，返回`false`，但是`InputStreamResource`除外。
- `getDescription()`: 返回资源的描述，用来输出错误的日志。这通常是完全限定的文件名或资源的实际URL。

**其他方法：**

- `isReadable()`: 表明资源的目录读取是否通过getInputStream()进行读取。
- `isFile()`: 表明这个资源是否代表了一个文件系统的文件。
- `getURL()`: 返回一个URL句柄，如果资源不能够被解析为URL，将抛出`IOException`
- `getURI()`: 返回一个资源的URI句柄
- `getFile()`: 返回某个文件，如果资源不能够被解析称为绝对路径，将会抛出`FileNotFoundException`
- `lastModified()`: 资源最后一次修改的时间戳
- `createRelative()`: 创建此资源的相关资源
- `getFilename()`: 资源的文件名是什么 例如：最后一部分的文件名 `myfile.txt`

## `Resource`的实现类

### `UrlResource`访问网络资源

- `Resource`的一个实现类，用来访问网络资源，它支持`URL`的绝对路径。

- `http`:------该前缀用于访问基于`HTTP`协议的网络资源。

- `ftp`:------该前缀用于访问基于`FTP`协议的网络资源

- `file`: ------该前缀用于从文件系统中读取资源

#### 访问基于HTTP协议的网络资源

```java
package com.markus.Resource;

import org.springframework.core.io.UrlResource;


public class MyUrlResource {
    public static void loadAndReadUrlResource(String path){
        UrlResource urlResource = null;
        try {
            urlResource = new UrlResource(path);
            System.out.println(urlResource.getFilename());
            System.out.println(urlResource.getURI());
            System.out.println(urlResource.getDescription());
            System.out.println(urlResource.getInputStream().read());
        }catch (Exception e){
            throw new RuntimeException(e);
        }
    }
    public static void main(String[] args){
        loadAndReadUrlResource("http://www.baidu.com");
    }
}
```

#### 在项目根路径下创建文件，从文件系统中读取资源

```java
   public static void main(String[] args){
        //loadAndReadUrlResource("http://www.baidu.com");
        loadAndReadUrlResource("file:/home/markus/Documents/test.txt");
    }
}
```

### `ClassPathResource`访问类路径下资源

ClassPathResource 用来访问类加载路径下的资源，相对于其他的 Resource 实现类，其主要优势是方便访问类加载路径里的资源，尤其对于 Web 应用，ClassPathResource 可自动搜索位于 classes 下的资源文件，无须使用绝对路径访问。

#### 使用`ClassPathResource` 访问类路径下文件

```java
package com.markus.Resource;

import org.springframework.core.io.ClassPathResource;

import java.io.InputStream;

public class MyClassResource {
    public static void loadAndReadUrlResource(String path){
        ClassPathResource classPathResource =null;
        try {
            classPathResource = new ClassPathResource(path);
            System.out.println(classPathResource.getFilename());
            System.out.println(classPathResource.getDescription());
            InputStream in = classPathResource.getInputStream();
            byte[] bytes = new byte[1024];
            //=-1意味着已读完
            while (in.read(bytes)!=-1){
                System.out.println(new String(bytes));
            }
        }catch (Exception e){
            throw new RuntimeException(e);
        }
    }
    public static void main(String[]args){
        loadAndReadUrlResource("test.txt");
    }
}
```

`ClassPathResource`实例可使用`ClassPathResource`构造器显式地创建，但更多的时候它都是隐式地创建的。当执行`Spring`的某个方法时，该方法接受一个代表资源路径的字符串参数，当`Spring`识别该字符串参数中包含`classpath:`前缀后，系统会自动创建`ClassPathResource`对象。

### `FileSystemResource` 访问文件系统资源

`Spring` 提供的 `FileSystemResource` 类用于访问文件系统资源，使用 `FileSystemResource` 来访问文件系统资源并没有太大的优势，因为 `Java` 提供的 `File `类也可用于访问文件系统资源。

#### 使用FileSystemResource 访问文件系统资源

```java
package com.markus.Resource;

import org.springframework.core.io.FileSystemResource;

import java.io.InputStream;

public class MyFileResource {
    public static void loadAndReadFileResource(String path){
        FileSystemResource fileSystemResource =null;
        try {
            fileSystemResource = new FileSystemResource(path);
            System.out.println(fileSystemResource.getFilename());
            System.out.println(fileSystemResource.getDescription());
            InputStream in = fileSystemResource.getInputStream();
            byte[] bytes = new byte[1024];
            while (in.read(bytes)!=-1){
                System.out.println(new String(bytes));
            }
        }catch (Exception e){
            throw new RuntimeException(e);
        }
    }
    public static void main(String[]args){
        //相对路径
        loadAndReadFileResource("test.txt");
        //绝对路径
        loadAndReadFileResource("/home/markus/Project/Java/Spring6/test.txt");
    }
}
```

`FileSystemResource`实例可使用`FileSystemResource`构造器显示地创建，但更多的时候它都是隐式创建。执行`Spring`的某个方法时，该方法接受一个代表资源路径的字符串参数，当`Spring`识别该字符串参数中包含`file:`前缀后，系统将会自动创建`FileSystemResource`对象。

### `ServletContextResource`

这是`ServletContext`资源的Resource实现，它解释相关`Web`应用程序根目录中的相对路径。它始终支持流(`stream`)访问和`URL`访问，但只有在扩展`Web`应用程序存档且资源实际位于文件系统上时才允许`java.io.File`访问。无论它是在文件系统上扩展还是直接从`JAR`或其他地方（如数据库）访问，实际上都依赖于`Servlet`容器。

### `InputStreamResource`

`InputStreamResource `是给定的输入流(`InputStream`)的`Resource`实现。它的使用场景在没有特定的资源实现的时候使用(感觉和`@Component` 的适用场景很相似)。与其他`Resource`实现相比，这是已打开资源的描述符。 因此，它的`isOpen()`方法返回true。如果需要将资源描述符保留在某处或者需要多次读取流，请不要使用它。

### `ByteArrayResource`

字节数组的`Resource`实现类。通过给定的数组创建了一个`ByteArrayInputStream`。它对于从任何给定的字节数组加载内容非常有用，而无需求助于单次使用的`InputStreamResource`。

## Resource类图

上述`Resource`实现类与`Resource`顶级接口之间的关系可以用下面的`UML`关系模型来表示

![image-20221206232920494](../images/image-20221206232920494.png)

## `ResourceLoader` 接口

### `ResourceLoader` 概述

`Spring `提供如下两个标志性接口：

**（1）`ResourceLoader `：** 该接口实现类的实例可以获得一个`Resource`实例。

**（2） `ResourceLoaderAware` ：** 该接口实现类的实例将获得一个`ResourceLoader`的引用。

在`ResourceLoader`接口里有如下方法：

（1）**`Resource getResource（String location）`** ： 该接口仅有这个方法，用于返回一个`Resource`实例。`ApplicationContext`实现类都实现`ResourceLoader`接口，因此`ApplicationContext`可直接获取`Resource`实例。

### 使用演示

#### `ClassPathXmlApplicationContext`获取`Resource`实例

```java
 @Test
    public void testClassPathXMLApplication()
    {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext();
        Resource resource = context.getResource("test.txt");
        System.out.println(resource.getFilename());
    }
```

#### `FileSystemApplicationContext`获取`Resource`实例

```java
 @Test
    public void testFileSystemXMLApplication()
    {
        FileSystemXmlApplicationContext context = new FileSystemXmlApplicationContext();
        Resource resource = context.getResource("test.txt");
        System.out.println(resource.getFilename());
        System.out.println(resource.getDescription());
    }
```

### `ResourceLoader `总结

`Spring`将采用和`ApplicationContext`相同的策略来访问资源。也就是说，如果`ApplicationContext`是`FileSystemXmlApplicationContext`，`res`就是`FileSystemResource`实例；如果`ApplicationContext`是`ClassPathXmlApplicationContext`，`res`就是`ClassPathResource`实例

当`Spring`应用需要进行资源访问时，实际上并不需要直接使用`Resource`实现类，而是调用`ResourceLoader`实例的`getResource()`方法来获得资源，`ReosurceLoader`将会负责选择`Reosurce`实现类，也就是确定具体的资源访问策略，从而将应用程序和具体的资源访问策略分离开来

另外，使用`ApplicationContext`访问资源时，可通过不同前缀指定强制使用指定的`ClassPathResource`、`FileSystemResource`等实现类

```java
Resource res = ctx.getResource("calsspath:bean.xml");
Resrouce res = ctx.getResource("file:bean.xml");
Resource res = ctx.getResource("http://localhost:8080/beans.xml");
```

## `ResourceLoaderAware` 接口

`ResourceLoaderAware`接口实现类的实例将获得一个`ResourceLoader`的引用，`ResourceLoaderAware`接口也提供了一个`setResourceLoader()`方法，该方法将由`Spring`容器负责调用，`Spring`容器会将一个`ResourceLoader`对象作为该方法的参数传入。

如果把实现`ResourceLoaderAware`接口的`Bean`类部署在`Spring`容器中，`Spring`容器会将自身当成`ResourceLoader`作为`setResourceLoader()`方法的参数传入。由于`ApplicationContext`的实现类都实现了`ResourceLoader`接口，`Spring`容器自身完全可作为`ResorceLoader`使用。

### 演示`ResourceLoaderAware`使用

#### 创建实现该接口的类

```java
package com.markus.Resource;

import org.springframework.context.ResourceLoaderAware;
import org.springframework.core.io.ResourceLoader;
import org.springframework.stereotype.Component;

@Component
public class BeanTest implements ResourceLoaderAware {
    private ResourceLoader resourceLoader;
    @Override
    //实现ResourceLoaderAware接口必须实现的方法
    //如果把该Bean部署在Spring容器中，该方法将会有Spring容器负责调用。
    //SPring容器调用该方法时，Spring会将自身作为参数传给该方法。
    public void setResourceLoader(ResourceLoader resourceLoader) {
        this.resourceLoader = resourceLoader;
    }
    public ResourceLoader getResourceLoader(){
        return this.resourceLoader;
    }

}
```

#### 创建配置类

```java
package com.markus.Config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

@Configuration
@ComponentScan("com.markus")
@PropertySource("classpath:application.properties")
public class MyConfig {

}
```

#### 测试

```java
@Test
public void test()
{
    ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
    BeanTest bean = context.getBean("beanTest", BeanTest.class);
    ResourceLoader resourceLoader = bean.getResourceLoader();
    System.out.println("Spring容器将自身注入到ResourceLoaderAware Bean 中 ？ ：" + (resourceLoader == context));//true
    Resource resource = resourceLoader.getResource("test.txt");
    System.out.println(resource.getFilename());
    System.out.println(resource.getDescription());
    //注解配置context是类路径
}
```

## 使用`Resource` 作为属性