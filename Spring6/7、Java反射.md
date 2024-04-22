# `Java`反射

## 前言

反射是Java底层框架的灵魂技术，学习反射非常有必要，本文将从入门概念，到实践，再到原理讲解反射，希望对大家有帮助。

## 反射理解

### 官方解释

`Java` 的**反射机制**是指在运行状态中，对于任意一个类都能够知道这个类所有的属性和方法； 并且对于任意一个对象，都能够调用它的任意一个方法；这种动态获取信息以及动态调用对象方法的功能成为`Java`语言的反射机制。

### 白话理解

#### 正射

万物有阴必有阳，有正必有反。既然有反射，就必有“正射”。

那么**正射**是什么呢？

我们在编写代码时，当需要使用到某一个类的时候，都会先了解这个类是做什么的。然后实例化这个类，接着用实例化好的对象进行操作，这就是正射。

```java
Student student = new Student();
student.doHomeWork("数学");
```

#### 反射

反射就是，一开始并不知道我们要初始化的类对象是什么，自然也无法使用 new 关键字来创建对象了。

```java
Class clazz = Class.forName("packagePath.Student");
Method method = clazz.getMethod("doHomework",String.class);
Constructor constructor = clazz.getConstructor();
Object object = constructor.newInstance();
method.invoke(object,"语文");
```

测试代码

```java
package com.markus;

import com.markus.Reflection.Student;
import org.junit.jupiter.api.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.Method;

public class ReflectionTest {
    @Test
    public void testReflection() throws Exception{
        Class clazz = Class.forName("com.markus.Reflection.Student");
        Method method = clazz.getMethod("doHomeWork", String.class);
        Constructor constructor = clazz.getConstructor();
        Object object = constructor.newInstance();
        method.invoke(object,"java");
        
        Student student = new Student();
        student.doHomeWork("java");
    }
}
```

执行结果一样，但是也略有不同

- 第一段代码则是到整个程序运行的时候，从字符串`reflection.Student`，才知道要操作的类是`Student`

- 第二段代码在未运行前就已经知道了要运行的类是`Student`；

**结论**

反射就是在运行时才知道要操作的类是什么，并且可以在运行时获取类的完整构造，并调用对应的方法。

### `Class`对象理解

要理解Class对象，我们先来了解一下**RTTI**吧。 **RTTI（Run-Time Type Identification）运行时类型识别**，其作用是在运行时识别一个对象的类型和类的信息。

Java是如何让我们在运行时识别对象和类的信息的？主要有两种方式： 一种是传统的**RRTI**，它假定我们在编译期已知道了所有类型。 另一种是反射机制，它允许我们在运行时发现和使用类的信息。

**每个类都有一个Class对象**，每当编译一个新类就产生一个Class对象（更恰当地说，是被保存在一个同名的.class文件中）。比如创建一个Student类，那么，JVM就会创建一个Student对应Class类的Class对象，该Class对象保存了Student类相关的类型信息。