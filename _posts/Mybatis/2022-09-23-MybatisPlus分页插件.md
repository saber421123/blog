---
layout: post

title: MyBatisPlus分页插件

category: Mybatis

tags: Mybatis

description: MyBatisPlus分页插件

keywords: Mybatis

score: 5.0

coverage: libra_coverage.png

published: true


---

##  MyBatisPlus分页插件

MyBatis Plus自带分页插件，只要简单的配置即可实现分页功能

1. 创建配置类
   此时可以删除主类中的 `@MapperScan` 扫描注解

   ```java
   @Bean
   public PaginationInterceptor paginationInterceptor() {
   	return new PaginationInterceptor();
   }
   ```

2. 测试selectPage分页

   测试：最终通过page对象获取相关数据

   ```java
   public void testSelectPage() {
       Page<User> page = new Page<>(1,5);
       userMapper.selectPage(page, null);
       page.getRecords().forEach(System.out::println);
       System.out.println(page.getCurrent());
       System.out.println(page.getPages());
       System.out.println(page.getSize());
       System.out.println(page.getTotal());
       System.out.println(page.hasNext());
       System.out.println(page.hasPrevious());
   }
   ```

   