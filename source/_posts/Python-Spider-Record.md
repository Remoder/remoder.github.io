---
title: Python_Spider_Tutorials
date: 2022-10-08 22:55:27
tags: [Python, Spider]
description: 爬虫学习过程的笔记。
abbrlink: python-spider-tutorials
cover: /img/python-spider-tutorials.png
categories: 
--- 

声明：学习资料来源于[wistbean's github](https://github.com/wistbean/learn_python3_spider)

# Part 1: 理论基础

## 请求方式

### GET 请求
`www.xxxxx.com/s?xx=yyyyy&xx=yyyyy`，`?`后即为**键值对**形式的请求参数。  

### POST 请求
以`Form`表单方式提交参数的请求，参数一般在`request body`里面。  
- 请求头 Request Header
包含一些 `HTTP请求` 的头部信息，如`Accept`, `Host`, `cookie`, `User-Agent`。  
- 响应头 Response Header
- 响应体 Response Body
服务器返回的数据。  


# Part 2: 通过 Fiddler 进行手机抓包
## 设置
1. 配置浏览器代理  
浏览器 - 设置 - 高级 - 打开代理设置 - IP`127.0.0.1` 端口`8888`  
2. 安装 HTTPs 证书  
Fiddler - Tools - Options - HTTPS - 勾选 `Decrypt HTTPS traffic` - Actions - `Reset All Certificates`
3. 下载证书
浏览器输入`localhost:8888`，点击`FiddlerRoot certificate`下载证书。
4. 设置 Fiddler  
设置`Fiddler Classiclistens onport: 8888`；    
勾选`Act as system proxy on startup`, `Monitor all connections`, `Allow remote computers to connect`, `Reuse client connections`, `Reuse server connections` ,`DefaultLan及本框所有`。
5. **重启电脑**
6. 手机连接统一 WIFI，浏览器进入`[IPv4地址]:8888`，点击`FiddlerRoot certificate`下载证书。
7. **仅针对iPhone，Android跳过此步。**设置 - 通用 - 关于本机 - 证书信任设置 - 勾上刚下载的证书。  

<font color="red">此节暂时终止，原因为手机添加代理后无法上网，且未解决`Tunnel To 443`问题。</font>

# Part 3: 第三方库

## Urllib 库的使用
- requset 模块
> 主要作用：发起请求  
   - `urllib.request.urlopen(url, data=None, [timeout,]*)`
默认是 `Get` 请求，传入参数 `data` 后成为 `POST` 请求。 
  - `urllib.request.Request(url, data=None, headers={}, method=None)`
此函数可以传入`请求头信息`了。

- error 模块
> 主要作用：异常处理

- parse 模块
> 解析 URL 地址

- robotparser
> 解析 robot.txt

## Request 库的使用
比 `Urllib` 简单得多，推荐首选。

使用自行查找(官方文档)[https://requests.readthedocs.io/en/latest/]。

# Part 4: 正则表达式

(Github 教程)[https://github.com/ziishaned/learn-regex/blob/master/translations/README-cn.md#learn-regex]
(re 库)[https://docs.python.org/3/library/re.html]
