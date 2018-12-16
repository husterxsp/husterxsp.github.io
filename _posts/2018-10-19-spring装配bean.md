---
layout: post
title: Spring装配bean
date: 2018-10-19
author: "Xsp"
catalog: true
tags:
    - Java
---



### Spring 配置方案

在Spring中，对象无需自己查找或创建与其所关联的其它对象。相反，容器负责把所需要相互协作的对象引用赋予各个对象。（**控制反转IOC**）


创建应用对象之间关系的行为成为**装配（wiring）**，也就是**依赖注入（DI）**的本质。

Spring 的三种主要的装配机制：

- XML显式配置
- Java显式配置
- 隐式bean发现机制和自动装配

一般来说，尽可能用自动装配，少用显式配置。

如果需要显式配置，可以用JavaConfig配置。

如果想用XML的命名空间，才用XML。



### 自动化装配bean

Spring 如何实现自动装配：

- **组件扫描（component scan）**：Spring自动发现应用上下文创建的bean。

- **自动装配（autowring）**：Spring 自动满足bean之间的依赖。


##### 创建可被发现的bean



**@Component** 注解表明该类作为组件类，并告知Spring要为该类创建bean。



**@ComponentScan** 注解启用组件扫描。

- 默认扫描与配置类相同的包。



用XML实现组件扫描，用Spring context 命名空间的`<context:component-scan>`元素：

```xml
<context:component-scan base-package="package-name" />
```



创建Spring的应用上下文，加载有 **@ComponentScan** 的注解的config 类，即可在上下文中包含相应的bean。



如下demo所示：

```java
// src/test/java/soundsystem/CDPlayerTest.java
package soundsystem;

import static org.junit.Assert.*;

import org.junit.Rule;
import org.junit.Test;
import org.junit.contrib.java.lang.system.StandardOutputStreamLog;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=CDPlayerConfig.class)
public class CDPlayerTest {

    @Rule
    public final StandardOutputStreamLog log = new StandardOutputStreamLog();

    @Autowired
    private MediaPlayer player;

    @Autowired
    private CompactDisc cd;

    @Test
    public void cdShouldNotBeNull() {
        assertNotNull(cd);
    }

    @Test
    public void play() {
        player.play();
        assertEquals(
                "Playing Sgt. Pepper's Lonely Hearts Club Band by The Beatles\n",
                log.getLog());
    }

}
```

```java
// src/main/java/soundsystem/CDPlayerConfig.java
package soundsystem;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

// 这里的Configuration 不必要
// @Configuration
@ComponentScan
public class CDPlayerConfig {
}
```



```java
package soundsystem;
import org.springframework.stereotype.Component;

@Component
public class SgtPeppers implements CompactDisc {

  private String title = "Sgt. Pepper's Lonely Hearts Club Band";  
  private String artist = "The Beatles";
  
  public void play() {
    System.out.println("Playing " + title + " by " + artist);
  }
  
}
```

```java
package soundsystem;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
  private CompactDisc cd;

  @Autowired
  public CDPlayer(CompactDisc cd) {
    this.cd = cd;
  }

  public void play() {
    cd.play();
  }

}
```





##### bean 命名

Spring给所有的bean一个默认的ID，就是类名首字母小写。

也可以设置ID ，`@Component("设置的名字")`。

另外，可以用 **Java依赖注入规范（Java Dependency Injection）** 中的 `@Named` 替换 `@Component` 。

bean ID有啥用？哦，可以设置引用。比如 xml中的 ref="bean id"



##### 设置要扫描的包



`@ComponentScan`  默认扫描当前配置类所在的，但是也可以指定扫描的包：

```java
// 扫描指定的包
@ComponentScan("package-name")
// 也可以这么写。
@ComponentScan(basePackages="package-name")
// 扫描多个包
@ComponentScan(basePackages={"package-name", "package-name"})
```

上述扫描设置的包是以String类型表示的。这种是 **类型不安全的（not type-safe）**  ???

所以还有另外一种方法：指定包中所含包含的某个类或接口。

```java
// basePackageClasses 属性所设置的数组中包含的类所在的包会作为组件扫描的基础包
@ComponentScan(basePackageClasses = {Test.class})
```



##### 自动装配

通过 **@Autowired** 注解，Spring会在应用上下文中寻找匹配某个bean需求的其它bean。

**@Autowired** 注解可以用在 **任何** 方法或者属性上。

Spring在初始化bean之后，就会去尽可能满足bean的依赖。这里 依赖是通过@Autowired注声明。如下代码所示。

```java
@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc cd;

    @Autowired
    public CDPlayer(CompactDisc cd) {
        this.cd = cd;
    }

    public void play() {
        cd.play();
    }
}
```



需要**注意**：如果没有匹配的bean，会报错。所以可以设置如下：

```java
// 即设置不一定非要找到，但这个时候，如果没找到，代码中的bean就是NULL了，所以需要做下检查。
@Autowired(required=false)
// 不能这样写。。
// @Autowired(false)
```

另外，`@Autowired`  注解可以用 java依赖注入规范中的 `@Inject`注解替换。



### JavaConfig 装配

一般来说，自动装配最方便，但是如果是想加载第三方库，没法在它的类上添加`@Component` 和`@Autowired` 注解，这时还是要用 Java 或者XML 来显式装配。



**创建配置类**

添加 `@Configuration` 注解

```java
@Configuration
public class CDPlayerConfig {
}
```

**声明bean**

```java
@Bean
public CompactDisc compactDisc() {
    return new SgtPeppers();
}
```

`@Bean` 注解告知Spring 返回一个对象，该对象注册为Spring应用上下文的bean。

也可以指定bean-id：`@Bean(name="aaa")`

**实现注入**

```java
@Configuration
public class CDPlayerConfig {

    @Bean
    public CompactDisc compactDisc() {
        return new SgtPeppers();
    }

    @Bean
    public CDPlayer cdPlayer() {
        return new CDPlayer(compactDisc());
    }
}
```

这里通过调用 compactDisc 来传入bean。

因为 compactDisc 方法加了 `@Bean` 注解，Spring会拦截所有对它的调用，并确保直接返回该方法创建的bean，而不是每次都对其进行实际的调用。

默认情况下，Spring 中的bean都是**单例**的。如下代码，两个cdPlayer bean得到的是相同的bean实例。

```java
@Bean
public CompactDisc compactDisc() {
    return new SgtPeppers();
}

@Bean
public CDPlayer cdPlayer() {
    return new CDPlayer(compactDisc());
}
@Bean
public CDPlayer anotherCDPlayer() {
    return new CDPlayer(compactDisc());
}
// 但是这样会报错？
// expected single matching bean but found 2: cdPlayer,anotherCDPlayer
```



其他注入方式，**传入构造器参数**。

```java
@Bean
public CDPlayer cdPlayer(CompactDisc compactDisc) {
    return new CDPlayer(compactDisc);
}
```



**Setter**

```java
@Bean
public CDPlayer cdPlayer(CompactDisc compactDisc) {
    CDPlayer cdPlayer = new CDPlayer(compactDisc);
    cdPlayer.setCompactDisc(compactDisc);
    return cdPlayer;
}
```

总之，加了 `@Bean`注解后，方法体力可以用任何java功能来实现bean。



### XML装配

















