---
layout: post
title: SpringMVC发送一个请求直接跳转到另一个页面(不使用controller)
category: SpringMVC
tags: SpringMVC
description: SpringMVC发送一个请求直接跳转到另一个页面(不使用controller)
keywords: SpringMVC
score: 5.0
coverage: libra_coverage.png
published: true
---

## SpringMVC viewcontroller
> 直接将请求和页面映射过来
```
@Configuration
    public class GulimallWebConfig implements WebMvcConfigurer {
        @Override
        public void addViewControllers(ViewControllerRegistry registry) {
            /**
            * @GetMapping("/login.html")
            *     public String index() {
            *         return "index";
            *     }
            */
            registry.addViewController("/login.html").setViewName("login");
        }
    }
```