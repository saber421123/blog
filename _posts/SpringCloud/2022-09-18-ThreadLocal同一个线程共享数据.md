---
layout: post

title: ThreadLocal同一个线程共享数据

category: SpringCloud

tags: SpringCloud

description: ThreadLocal同一个线程共享数据

keywords: SpringCloud

score: 5.0

coverage: libra_coverage.png

published: true






---

# ThreadLocal同一个线程共享数据

同一个线程共享数据

![image.png](/assets/imgs/1621737371778-7e86b003-ce7f-42b9-b546-d233797ff7d9.png)

```java
public static ThreadLocal<UserInfoTo> toThreadLocal = new ThreadLocal<>();

// 赋值
toThreadLocal.set(userInfoTo);

// 取值
UserInfoTo userInfoTo = CartInterceptor.toThreadLocal.get();
```

- 参考博客：[ThreadLocal同一个线程共享数据](https://www.cnblogs.com/xbhog/p/15411167.html)

