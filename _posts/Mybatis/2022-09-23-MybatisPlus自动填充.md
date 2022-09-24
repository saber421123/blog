---
layout: post

title: MyBatisPlus自动填充

category: Mybatis

tags: Mybatis

description: MyBatisPlus自动填充

keywords: Mybatis

score: 5.0

coverage: libra_coverage.png

published: true


---

##  MyBatisPlus自动填充

项目中经常会遇到一些数据，每次都使用相同的方式填充，例如记录的创建时间，更新时间等。
我们可以使用MyBatis Plus的自动填充功能，完成这些字段的赋值工作：

1. 数据库表中添加自动填充字段
   在User表中添加datetime类型的新的字段 create_time、update_time

2. 实体上添加注解

   ```java
   @Data
   public class User {
   ......
   @TableField(fill = FieldFill.INSERT)
   	private Date createTime;
   	//@TableField(fill = FieldFill.UPDATE)
   	@TableField(fill = FieldFill.INSERT_UPDATE)
   	private Date updateTime;
   }
   ```

3. 实现元对象处理器接口

   注意：不要忘记添加 @Component 注解

   ```java
   @Component
   public class MyMetaObjectHandler implements MetaObjectHandler {
   	private static final Logger LOGGER =
   		LoggerFactory.getLogger(MyMetaObjectHandler.class);
   @Override
   public void insertFill(MetaObject metaObject) {
   	LOGGER.info("start insert fill ....");
   	this.setFieldValByName("createTime", new Date(), metaObject);
   	this.setFieldValByName("updateTime", new Date(), metaObject);
   }
   @Override
   public void updateFill(MetaObject metaObject) {
   	LOGGER.info("start update fill ....");
   	this.setFieldValByName("updateTime", new Date(), metaObject);
   }
   }
   ```

   

