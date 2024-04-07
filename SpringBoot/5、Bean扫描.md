# Bean扫描

## Spring基于注解扫描Bean

- `xml`方式

  ```xml
  <content:component-scan base-package="com.markus(PackagePath)" />
  ```

- `Annotation`方式

  ```java
  @ComponentScan(basePackages="com.markus(PackagePath)")
  ```

## SpringBoot自动扫描Bean

### 启动类

```java
@ComponentScan(basePackages="${PackagePath}")
@SpringBootApplication
public class ServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServerApplication.class, args);
	}

}
```

`@SpringBootApplication`注解会自动扫描`ServerApplication.class`所在包下的所有Bean

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication{
    /..../
}
```

会发现`@SpringBootApplication`源码下已包含`@ComponentScan`注解

