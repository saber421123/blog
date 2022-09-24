---
layout: post

title: MyBatisPlus性能分析
category: Mybatis

tags: Mybatis

description: MyBatisPlus性能分析

keywords: Mybatis

score: 5.0

coverage: libra_coverage.png

published: true


---

##  MyBatisPlus性能分析

性能分析拦截器，用于输出每条 SQL 语句及其执行时间
SQL 性能执行分析,开发环境使用，超过指定时间，停止运行。有助于发现问题

1. 配置插件

   - 参数说明
     参数：maxTime： SQL 执行最大时长，超过自动停止运行，有助于发现问题。
     参数：format： SQL是否格式化，默认false。

   - 在 `MybatisPlusConfig` 中配置

     ```java
     /**
     * SQL 执行性能分析插件
     * 开发环境使用，线上不推荐。 maxTime 指的是 sql 最大执行时长
     */
     @Bean
     @Profile({"dev","test"})// 设置 dev test 环境开启
     public PerformanceInterceptor performanceInterceptor() {
         PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
         performanceInterceptor.setMaxTime(100);//ms，超过此处设置的ms则sql不执行
         performanceInterceptor.setFormat(true);
         return performanceInterceptor;
     }
     ```

   - Spring Boot 中设置dev环境

     ```yaml
     #环境设置：dev、test、prod
     spring.profiles.active=dev
     ```

     可以针对各环境新建不同的配置文件`application-dev.properties、application-test.properties、application-`
     `prod.properties`
     也可以自定义环境名称：如test1、test2

2. 测试

   ```java
   @Test
   public void testPerformance() {
       User user = new User();
       user.setName("我是Helen");
       user.setEmail("helen@sina.com");
       user.setAge(18);
       userMapper.insert(user);
   }
   ```

   ![image-20220924154355274](/assets/imgs/image-20220924154355274.png)

   