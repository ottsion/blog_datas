---
title: "跨域访问api"
date: 2019-05-08T14:18:53+08:00
draft: false
author: "caicai"
tags: ["api", "ajax", "java"]
categories: ["web"]
---

主程序使用java构建了完整的算法，想快速通过简易前端实现效果可视化的时候产生了跨域访问失败问题，java部分api接口采用springboot快捷提供，前端采用python的flask快速开发。

前端使用ajax访问其他web服务的后端接口时很容易出现因跨域访问而导致接口调用失败的问题，以下介绍如何使用CORS解决这两个框架的跨域访问问题。

CORS是一种允许当前域（domain）的资源（比如html/js/web service）被其他域（domain）的脚本请求访问的机制，通常由于同域安全策略（the same-origin security policy）浏览器会禁止这种跨域请求。

我们需要在两者分别修改代码实现跨域访问

## SpringBoot框架解决方案

在Application类(启动类)里增加CorsFilter的Bean的定义：
```java
package com.ecm;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

@SpringBootApplication
@MapperScan("com.ecm.dao")
public class EcmApplication {
    public static void main(String[] args) {
        SpringApplication.run(EcmApplication.class, args);
    }

    @Bean
    public CorsFilter corsFilter() {
        final UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        final CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true); // 允许cookies跨域
        config.addAllowedOrigin("*");// #允许向该服务器提交请求的URI，*表示全部允许，在SpringMVC中，如果设成*，会自动转成当前请求头中的Origin
        config.addAllowedHeader("*");// #允许访问的头信息,*表示全部
        config.setMaxAge(18000L);// 预检请求的缓存时间（秒），即在这个时间段里，对于相同的跨域请求不会再预检了
        config.addAllowedMethod("OPTIONS");// 允许提交请求的方法，*表示全部允许
        config.addAllowedMethod("HEAD");
        config.addAllowedMethod("GET");// 允许Get的请求方法
        config.addAllowedMethod("PUT");
        config.addAllowedMethod("POST");
        config.addAllowedMethod("DELETE");
        config.addAllowedMethod("PATCH");
        source.registerCorsConfiguration("/**", config);
        return new CorsFilter(source);
    }
}
```
## Flask框架解决方案
### 安装flask_cors包
`pip install flask-cors`

### 使用flask_cors包
```python
-*- coding: utf-8 -*-
from flask import Flask
from flask_cors import *
app = Flask(__name__)
CORS(app, supports_credentials=True)
```

