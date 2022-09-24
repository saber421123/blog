---
layout: post

title: MyBatisPlus逻辑删除

category: Mybatis

tags: Mybatis

description: MyBatisPlus逻辑删除

keywords: Mybatis

score: 5.0

coverage: libra_coverage.png

published: true


---

##  MyBatisPlus逻辑删除

- 物理删除：真实删除，将对应数据从数据库中删除，之后查询不到此条被删除数据
- 逻辑删除：假删除，将对应数据中代表是否被删除字段状态修改为“被删除状态”，之后在数据库中仍
  旧能看到此条数据记录

1. 数据库中添加 `deleted`字段

   ```mysql
   ALTER TABLE `user` ADD COLUMN `deleted` boolean
   ```

   ![image-20220924152632068](/assets/imgs/image-20220924152632068.png)

2. 实体类添加deleted 字段

   并加上 `@TableLogic` 注解 和 `@TableField(fill = FieldFill.INSERT)` 注解

   ```java
   @TableLogic
   @TableField(fill = FieldFill.INSERT)
   private Integer deleted;
   ```

3. 元对象处理器接口添加deleted的insert默认值

   ```java
   @Override
   public void insertFill(MetaObject metaObject) {
   ......
   this.setFieldValByName("deleted", 0, metaObject);
   }
   ```

4. application.properties 加入配置

   此为默认值，如果你的默认值和mp默认的一样,该配置可无

   ```java
   mybatis-plus.global-config.db-config.logic-delete-value=1
   mybatis-plus.global-config.db-config.logic-not-delete-value=0
   ```

5. 在 MybatisPlusConfig 中注册 `Bean`

   ```java
   @Bean
   public ISqlInjector sqlInjector() {
   return new LogicSqlInjector();
   }
   ```

6. 测试逻辑删除

   - 测试后发现，数据并没有被删除，deleted字段的值由0变成了1
   - 测试后分析打印的sql语句，是一条update
   - 注意：被删除数据的deleted 字段的值必须是 0，才能被选取出来执行逻辑删除的操作

   ```java
   @Test
   public void testLogicDelete() {
   int result = userMapper.deleteById(1L);
   System.out.println(result);
   }
   ```

7. 测试逻辑删除后的查询

   MyBatis Plus中查询操作也会自动添加逻辑删除字段的判断

   ```java
   @Test
   public void testLogicDeleteSelect() {
       User user = new User();
       List<User> users = userMapper.selectList(null);
       users.forEach(System.out::println);
   }
   ```

   测试后分析打印的sql语句，包含 WHERE deleted=0
   ```mysql
   SELECT id,name,age,email,create_time,update_time,deleted FROM user WHERE deleted=0
   ```

   