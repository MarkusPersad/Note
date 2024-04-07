# Bean注册

## 第三方（非自定义）`jar`包注册

- `@Bean`
- `@import`

### 安装`jar`包

以本地安装为示例，可以在网络下载失败时使用

```shell
#mvn install:install-file -Dfile={本地路径}/XXX.jar -DgroupId=cn.itcast -DartifactId=common-pojo -Dversion=1.0 -Dpackaging=jar
mvn install:install-file -Dfile=D:\Code\Java\common-pojo-1.0-SNAPSHOT.jar -DgroupId=com.markus -DartifactId=common-pojo -Dversion=1.0 -Dpackaging=jar
```

### `pom.xml`配置

```xml
<dependency>
 	<groupId>com.markus</groupId>
 	<artifactId>common-pojo</artifactId>
 	<version>1.0</version>
</dependency>
```

### 在`Configure`类里导入

```java
@Configuration
public class CommonConfigure {
    @Bean
    public Country country(){
        return new Country();
    }
}
```



### `@Bean`

```java
//默认name是方法名
//修改name可通过
//@Bean("Name")
//如果方法内要使用IoC容器内已存在的Bean对象，直接声明即可，Spring会自动注入
@Bean
public Country country(){
  return new Country();
}
@Bean
public Province province(Country country){
    System.out.println(country)
    return new Province()
}
```

### `@import`

#### 导入配置类

`CommonConfigure.java`

```java
@Configuration
public class CommonConfigure {
    @Bean
    public Country country(){
        return new Country();
    }
}
```



```java
@Import(CommonConfigure.class)
@SpringBootApplication
public class BeanApplication {

    public static void main(String[] args) {

        ApplicationContext context = SpringApplication.run(BeanApplication.class, args);
        Country country = context.getBean(Country.class);
        System.out.println(country);
        Province province = (Province) context.getBean("province");
        System.out.println(province);

    }
}
```

#### 导入`ImportSelector`实现类

`CommonImportSelector.java`

```java
package com.markus.CommonConfig;

import org.springframework.context.annotation.ImportSelector;
import org.springframework.core.type.AnnotationMetadata;

public class CommonImportSelector implements ImportSelector {

    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{"com.markus.CommonConfig.CommonConfigure"};
    }
}
```

##### 导入解耦合

`common.imports`

```imports
com.markus.CommonConfig.CommonConfigure
/..一个全类名一行../
```

`CommonImportSelector.java`

```java
package com.markus.CommonConfig;

import org.springframework.context.annotation.ImportSelector;
import org.springframework.core.type.AnnotationMetadata;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;

public class CommonImportSelector implements ImportSelector {

    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        List<String> selectImports = new ArrayList<>();
        //用本类的类加载器读取ClassPath下的common.imports文件为一个输入流
        InputStream inputStream =            	  CommonImportSelector.class.getClassLoader().getResourceAsStream("common.imports");
        //用一个Buffereader缓冲流包裹InputStream
        BufferedReader br =new BufferedReader(new InputStreamReader(inputStream));
        String line = null;
        try {
            while ((line=br.readLine())!=null){
                selectImports.add(line);
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                //无论如何也要在最后关闭流
                br.close();
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }
        return selectImports.toArray(new String[0]);
    }
}
```



### 获取对象

```java
package com.markus.bean;

import cn.itcast.pojo.Country;
import cn.itcast.pojo.Province;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;


@SpringBootApplication
public class BeanApplication {

    public static void main(String[] args) {

        ApplicationContext context = SpringApplication.run(BeanApplication.class, args);
        //byClass
        Country country = context.getBean(Country.class);
        System.out.println(country);
        //byName
        Province province = (Province) context.getBean("province");
        System.out.println(province);

    }
}
```

