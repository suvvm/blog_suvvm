---
title: Session与Cookie
date: 2020-05-02 18:08:59
categories: 
- 理论归纳
tags:
- 计算机网络
---

# Session与Cookie

## Session

由于HTTP协议是无状态的，其两次请求之间没有任何关联，所以出现了Session，Session是服务器用来记录用户信息的工具，保存在服务器中，每个Session都有一个独立标识SessionID，服务器会通过响应报文将其通知客户端，一般来说客户端都会将其保存为cookie，若客户端禁用cookie也可以通过在url里附加SessionID参数来识别用户。

## Cookie

Cookie由W3C提出，受所有主流浏览器支持，其出现原因也是为了解决HTTP协议无状态的问题，与Session将数据存储在服务器不同，Cookie数据由浏览器存储在用户的本地终端，用于保存用户信息，每次发送请求时cookie都将被携带在HTTP请求headers中发送给服务器，服务器可以通过cookie中的信息简便的获取用户状态。

由于其保存在客户端的特性，伪造cookie也变得容易了很多。

