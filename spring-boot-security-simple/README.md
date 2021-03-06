## 项目模块结构

- spring-boot-security-simple: 父项目, 主模块
- spring-boot-security-core: 核心业务逻辑
- spring-boot-security-browser: 浏览器安全特定代码
- spring-boot-security-app: app相关特定代码
- spring-boot-security-demo: 样例程序

# 学习记录

## Restful API拦截

- 过滤器(implements Filter)  
    优点: 能拿到原始的HTTP请求  
    局限性: 不能获取对应的请求处理器(controller)的信息
    
- 拦截器(implements HandlerInterceptor)   
    优点: 既能拿到原始HTTP请求信息, 也能获取对应的请求处理器(controller)的信息
    局限性: 不能获取对应的请求处理器的**值**(方法入参)
    
- 切片(Aspect)  
    优点: 能拿到请求处理器信息以及请求处理器的**参数值**  
    局限性: 拿不到原始的HTTP请求信息

## 请求的处理过程

正常情况下按照下图流程  
异常情况下, 如果Aspect没有做异常处理, 将会向上抛, ControllerAdvice没有处理, 则一直向上抛. 直至Filter没有处理, 则会抛出给Tomcat展示到前端
![请求处理](https://i.loli.net/2019/06/04/5cf5e0bb91cbe93775.jpg)


## WireMock的使用

### 引入依赖
```xml
<dependency>
    <groupId>com.github.tomakehurst</groupId>
    <artifactId>wiremock-jre8</artifactId>
    <version>2.23.2</version>
    <scope>test</scope>
</dependency>
```

### 下载服务器代码

[http://wiremock.org/docs/download-and-installation/](http://wiremock.org/docs/download-and-installation/)

### 运行服务器

`java -jar wiremock-standalone-2.23.2.jar --port=8062`

### 编写接口. 模拟测试


## 个性化用户认证流程

### 自定义登录页面
```
http.formLogin().loginPage("/authentication/require")
```

### 自定义登录成功处理与登录失败处理

登陆成功后处理  
org.springframework.security.web.authentication.SavedRequestAwareAuthenticationSuccessHandler  
登录失败后处理  
org.springframework.security.web.authentication.SimpleUrlAuthenticationFailureHandler  


# 图形验证码过滤器的设计模式

1. 验证码基本参数可配置, 通过配置文件
    - 默认配置: 配置值写在security-core中
    - 应用级配置: 配置值写在对应子系统中
    - 请求级配置: 配置值在调用接口时传递
2. 验证码拦截的接口可配置
3. 验证码的生成逻辑可配置, 通过抽象接口提供默认实现, 子类实现接口即可定制自己的验证码实现
    - 提供统一行为接口, 子应用需要时重写对应接口即可

![验证码处理器代码结构](https://i.loli.net/2019/06/11/5cff98b57bce850327.jpg)  
![SpringSecurity基本流程图](https://i.loli.net/2019/06/11/5cff98b57e5e687071.jpg)
![自定义短信登录](https://i.loli.net/2019/06/13/5d0222103ae1657066.jpg)


# 社交登录

## social登录实现

![继承关系](https://i.loli.net/2019/06/20/5d0b2afe261e228608.png)
![Spring Social基本原理](https://i.loli.net/2019/06/20/5d0b2d28cea1e97008.png)
![Spring Social处理流程](https://i.loli.net/2019/06/21/5d0c7204d2bbb95274.png)



UserConnection.sql文件位置: org/springframework/social/connect/jdbc/JdbcUsersConnectionRepository.sql


# SpringSecurity登录核心流程
 一个过滤器(AuthenticationFilter)拦截某一个特定的请求,拦截到请求之后,将请求中做身份认证所需要的信息, 包装到一个Authentication
 的实现里面,然后将Authentication交给AuthenticationManager,AuthenticationManager根据传进来的类型不同,从它所管理的
 AuthenticationProvider实现里面,挑一个Provider处理传进来的Authentication,在处理的过程中,他会调用我们自己写的UserDetailService
 接口的实现, 获取业务系统中用户的信息,然后将信息封装到一个UserDetail的实现里面,然后做一系列的检查和校验,如果都通过,它会将信息放到我们
 实现的Authentication里,然后将Authentication标记成经过认证的,然后放到SecurityContext里面,完成整个登录 

## about

这个是学习spring-security的代码汇总, 学习的是慕课视频[Spring Security开发安全的REST服务](https://coding.imooc.com/class/consult/134.html)