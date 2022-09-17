---
layout: post

title: SpringCloud整合SpringSession 

category: SpringCloud

tags: SpringCloud

description: SpringCloud整合SpringSession 

keywords: SpringCloud

score: 5.0

coverage: libra_coverage.png

published: true









---

# SpringCloud整合SpringSession
> 官网地址：[SpringSession官网地址](https://docs.spring.io/spring-session/docs/2.5.0/reference/html5/#samples)
auth 服务、product 服务、 search 服务 pom文件

```java
<!-- 整合 spring session 实现 session 共享-->
<dependency>
  <groupId>org.springframework.session</groupId>
  <artifactId>spring-session-data-redis</artifactId>
</dependency>
```

配置文件 application.yaml

```java
spring:
  session:
    store-type: redis
```

主启动类增加注解：@EnableRedisHttpSession 

配置类：

```java
@Configuration
public class GulimallSessionConfig {
    @Bean
    public CookieSerializer cookieSerializer() {
        DefaultCookieSerializer cookieSerializer = new DefaultCookieSerializer();
        //放大作用域
        cookieSerializer.setDomainName("gulimall.com");
        cookieSerializer.setCookieName("GULISESSION");
        return cookieSerializer;
    }

    @Bean
    public RedisSerializer<Object> springSessionDefaultRedisSerializer() {
        return new GenericJackson2JsonRedisSerializer();
    }
}
```

## SpringSession 核心原理

@EnableRedisHttpSession 导入 RedisHttpSessionConfiguration 配置

1. 给容器中添加了一个组件 RedisOperationsSessionRepository：Redis操作session，session的增删改查封装类；

2. 继承 SpringHttpSessionConfiguration 初始化了一个 SessionRepositoryFilter：session 存储过滤器；每个请求过来都必须经过 Filter 组件；

   创建的时候，自动从容器中获取到了 SessionRepository；

    SessionRepositoryFilter：

   - 将原生的 HttpServletRequest Response 包装成 SessionRepositoryRequestWrapper ResponseWrapper；包装后的对象应用到了后面整个执行链；

   - 以后获取 request.getSession(); 都会调用 wrappedRequesr.getSession(); 从SessionRepository获取;

     

3. 装饰者模式

   ```java
   protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
       request.setAttribute(SESSION_REPOSITORY_ATTR, this.sessionRepository);
       SessionRepositoryFilter<S>.SessionRepositoryRequestWrapper wrappedRequest = new SessionRepositoryFilter.SessionRepositoryRequestWrapper(request, response);
       SessionRepositoryFilter.SessionRepositoryResponseWrapper wrappedResponse = new SessionRepositoryFilter.SessionRepositoryResponseWrapper(wrappedRequest, response);
   
       try {
           filterChain.doFilter(wrappedRequest, wrappedResponse);
       } finally {
           wrappedRequest.commitSession();
       }
   
   }
   ```

   

   

   

   
