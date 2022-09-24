---
layout: post

title: MyBatisPlus乐观锁

category: Mybatis

tags: Mybatis

description: MyBatisPlus乐观锁

keywords: Mybatis

score: 5.0

coverage: libra_coverage.png

published: true


---

##  MyBatisPlus乐观锁

主要适用场景：当要更新一条记录的时候，希望这条记录没有被别人更新，也就是说实现线程安全的数据更新
乐观锁实现方式：

- 取出记录时，获取当前version
- 更新时，带上这个version
- 执行更新时， set version = newVersion where version = oldVersion
- 如果version不对，就更新失败

1. 数据库中添加version字段

   ```mysql
   ALTER TABLE `user` ADD COLUMN `version` INT
   ```

   ![image-20220924150529207](/assets/imgs/image-20220924150529207.png)

2. 实体类添加version字段

   并添加 @Version 注解

   ```java
   @Version
   @TableField(fill = FieldFill.INSERT)
   private Integer version;
   ```

3. 元对象处理器接口添加version的insert默认值

   ```java
   @Override
   public void insertFill(MetaObject metaObject) {
   ......
   this.setFieldValByName("version", 1, metaObject);
   }
   ```

   特别说明:

   - 支持的数据类型只有 `int,Integer,long,Long,Date,Timestamp,LocalDateTime`
   - 整数类型下 `newVersion = oldVersion + 1`
   - `newVersion` 会回写到 `entity` 中
   - 仅支持 `updateById(id)` 与 `update(entity, wrapper)` 方法
   - 在 `update(entity, wrapper)` 方法下, `wrapper` 不能复用!!!

4. 在 `MybatisPlusConfig` 中注册 `Bean`

   创建配置类

   ```java
   @EnableTransactionManagement
   @Configuration
   @MapperScan("com.atguigu.mybatis_plus.mapper")
   public class MybatisPlusConfig {
   /**
   * 乐观锁插件
   */
   @Bean
   public OptimisticLockerInterceptor optimisticLockerInterceptor() {
   	return new OptimisticLockerInterceptor();
   	}
   }
   ```

5. 测试乐观锁可以修改成功

   测试后分析打印的`sql`语句，将`version`的数值进行了加1操作

   ```java
   /**
   * 测试 乐观锁插件
   */
   @Test
   public void testOptimisticLocker() {
   	//查询
   	User user = userMapper.selectById(1L);
   	//修改数据
   	user.setName("Helen Yao");
   	user.setEmail("helen@qq.com");
   	//执行更新
   	userMapper.updateById(user);
   }
   ```

6. 测试乐观锁修改失败

   ```java
   /**
   * 测试乐观锁插件 失败
   */
   @Test
   public void testOptimisticLockerFail() {
       //查询
       User user = userMapper.selectById(1L);
       //修改数据
       user.setName("Helen Yao1");
       user.setEmail("helen@qq.com1");
       //模拟取出数据后，数据库中version实际数据比取出的值大，即已被其它线程修改并更新
       了version
       user.setVersion(user.getVersion() - 1);
       //执行更新
       userMapper.updateById(user);
   }
   ```

   