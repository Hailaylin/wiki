---
title: 黑马顺义校区Java生产实训 day3 笔记
description: 
published: true
date: 2023-08-30T01:21:03.978Z
tags: 北京, 黑马
editor: markdown
dateCreated: 2023-08-30T01:15:08.373Z
---

昨天特地安装了wiki.js

# 1.SpringBoot整合Mybatis完成表单CRUD

## 1.1 REST风格
强调以资源为中心，在URL中部出现动词；通过post、put、get、delete请求来区分是保存、修改、查询、删除操作。

## 1.2 @ResquestMapping子注解
`` @GetMapping`` GET请求。同理其余请求。

``@RestController``