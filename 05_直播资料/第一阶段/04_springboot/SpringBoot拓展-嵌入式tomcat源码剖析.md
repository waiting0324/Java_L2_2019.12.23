# SpringBoot拓展

## 嵌入式Tomcat源码剖析

1、进入SpringBoot启动类，点进@SpringBootApplication源码，如下图

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218165203584.png" alt="image-20200218165203584" style="zoom:50%;" />

------------------------



<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218165233922.png" alt="image-20200218165233922" style="zoom:50%;" />



2、继续点进@EnableAutoConfiguration,进入该注解，如下图

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218165352083.png" alt="image-20200218165352083" style="zoom:50%;" />



3、上图中使用@Import注解对AutoConfigurationImportSelector 类进行了引入，该类做了什么事情呢？进入源码，首先调用selectImport()方法，在该方法中调用了getAutoConfigurationEntry（）方法，在之中又调用了getCandidateConfigurations()方法，getCandidateConfigurations()方法就去META-INF/spring.factory配置文件中加载相关配置类
![image-20200218165718781](/Users/ericsun/Library/Application Support/typora-user-images/image-20200218165718781.png)

这个spring.factories配置文件是加载的spring-boot-autoconfigure的配置文件

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218165827431.png" alt="image-20200218165827431" style="zoom:50%;" />

继续打开spring.factories配置文件，找到tomcat所在的类，tomcat加载在ServletWebServerFactoryAutoConfiguration配置类中

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218170001683.png" alt="image-20200218170001683" style="zoom:50%;" />

进入该类，里面也通过@Import注解将EmbeddedTomcat、EmbeddedJetty、EmbeddedUndertow等嵌入式容器类加载进来了，springboot默认是启动嵌入式tomcat容器，如果要改变启动jetty或者undertow容器，需在pom文件中去设置。如下图

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218170113075.png" alt="image-20200218170113075" style="zoom:50%;" />

继续进入EmbeddedTomcat类中，见下图：

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218170414296.png" alt="image-20200218170414296" style="zoom:50%;" />

进入TomcatServletWebServerFactory类，里面的getWebServer（）是关键方法，如图：

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218170829834.png" alt="image-20200218170829834" style="zoom:50%;" />

继续进入getTomcatWebServer()等方法，一直往下跟到tomcat初始化方法，调用tomcat.start()方法，tomcat就正式开启运行，见图

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218170937061.png" alt="image-20200218170937061" style="zoom:50%;" />

走到这里tomcat在springboot中的配置以及最终启动的流程就走完了，相信大家肯定有一个疑问，上上图中的getWebServer()方法是在哪里调用的呢？上面的代码流程并没有发现getWebServer()被调用的地方。因为getWebServer()方法的调用根本就不在上面的代码流程中，它是在另外一个流程中被调用的

## 源码解析之调用getWebServer()

首先进入SpringBoot启动类的run方法：

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218171122889.png" alt="image-20200218171122889" style="zoom:50%;" />

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218171220881.png" alt="image-20200218171220881" style="zoom:50%;" />



进入refreshContext()方法，如图：

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218171306055.png" alt="image-20200218171306055" style="zoom:50%;" />

一直点击refresh()方法，如图：

![image-20200218171351544](/Users/ericsun/Library/Application Support/typora-user-images/image-20200218171351544.png)



![image-20200218171528863](/Users/ericsun/Library/Application Support/typora-user-images/image-20200218171528863.png)



![image-20200218171815505](/Users/ericsun/Library/Application Support/typora-user-images/image-20200218171815505.png)



<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218171916673.png" alt="image-20200218171916673" style="zoom:50%;" />

继续进入getWebServer（）方法，如图：

<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218172123083.png" alt="image-20200218172123083" style="zoom:50%;" />



<img src="/Users/ericsun/Library/Application Support/typora-user-images/image-20200218172314563.png" alt="image-20200218172314563" style="zoom:50%;" />

最终就调用了TomcatServletWebServerFactory类的getWebServer()方法。



