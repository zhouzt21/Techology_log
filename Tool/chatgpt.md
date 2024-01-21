---
title: Chatgpt配置
date: 2023-02-10 23:38:42
tags: 技术操作
---
# Chatgpt注册(2023.2)

### 步骤

1.需要有一个国外邮箱
2.需要有一个注册chatgpt不限制的国家的VPN（这次使用的日本流媒体线路）
3.注册时需要通过收验证码网站
一开始尝试：https://sms-activate.org/，要收费，但是网站设计太差，找不到注册短信接收服务的地方；后换成https://sms-man.com/cn?ref=vubD3Bq3Ij2f
（参考的这篇https://freebrid.com/index.php/2023/02/08/chatgpt-2/#:~:text=1%E3%80%81%20%E7%82%B9%E5%87%BB%E8%BF%9B%E5%85%A5sms-man%E6%8E%A5%E7%A0%81%E5%B9%B3%E5%8F%B0,%E5%90%8E%EF%BC%8C%E7%82%B9%E5%87%BB%E5%8F%B3%E4%B8%8A%E8%A7%92%E7%9A%84%E5%85%85%E5%80%BC%202%E3%80%81%E5%85%85%E5%80%BC%E5%AE%8C%E6%88%90%E5%90%8E%EF%BC%8C%E5%9C%A8-%E6%8E%A5%E6%94%B6%E7%9F%AD%E4%BF%A1-%E9%80%89%E6%8B%A9%E5%9B%BD%E5%AE%B6%EF%BC%88usa%EF%BC%89-%E9%80%89%E6%8B%A9%E6%9C%8D%E5%8A%A1%EF%BC%88openai%EF%BC%89%E5%90%8E%EF%BC%8C%E7%82%B9%E5%87%BB-%E8%B4%AD%E4%B9%B0%E7%9F%AD%E4%BF%A1）
两者的注册我都使用的邮箱1，并且充了一定值；但因为这个邮箱somehow与nova 4e绑定了，没法使用网页的形式验证gmail，所以采用的手机直接登录gmail，在手机上成功注册了chatgpt。



### 后续使用
每次使用还是要翻墙，还是一个手机网页版的形式，可以看看有没有app，或者把这个网页转为PC端常用网页进行管理。

### 潜在问题
1.clash在超过时区的地区检测总是超时，导致可用的线路是局限的。但是如果手动更改PC时间则会所有都超时，完全没有办法使用。

### 中间遇到的小问题
1.人机验证没法连接，解决：使用Chrome已经装好的Header Editor插件；
2.网页全部不能加载，显示远程计算机或设备将不接受连接；解决：控制中心，internet选项，连接，局域网设置，关闭里面的自动检测设置、为LAN使用代理服务器选项。