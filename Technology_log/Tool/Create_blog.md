---
title: 个人博客搭建
date: 2023-02-10 23:38:42
tags: 技术操作
---

# 使用hexo搭建个人博客(2023.2)
## 进度&步骤

1. 注册好域名并解析，zhouzt21.github.io能解析到zhouzt21.space上；

2. hexo和github联动：hexo分支都关联了（直接git push zztrepo hexo:hexo，即zztrepo是远程主机别称<连上了github域名>，这里将本地hexo分支push到了github的hexo）；
   在hexo启动的时候是localhost:4000，使用zhouzt21.space也可以不开hexo，把自己的东西发布到网上~

3. 配置了wikitten的主题，但尚还要建立不分类的功能

4. 需要用Markdown来编译post，下载好typora（successful）

5. 文章的本地渲染更新：直接cd ;hexo d;hexo s （直接在_config.yml文件里面改theme）

6. 部署到github，并与域名联动：用git push（-f）强制上传了hexo分支（用来存储.md文件）；对于网站的更新还是要在_config.yml文件里把部署改到github的仓库里（main分支）

   deploy:

    	type: 'git'
    	
    	repo: https://github.com/zhouzt21/zhouzt21.github.io.git
    	
    	branch: main

## 不懂的

1. 是不是zhouzt21.space从github里读取html之后渲染成网页了？（就是配置里直接部署到github有封装)

4. “zhouzt21.space” “www. zhouzt21.space" 会访问同一个网址，是因为在解析的时候同时设置了，某知乎回答如下：

   ​	在域名解析提供商，比如dnspod界面下，先添加一个CNAME，后面记录值写上 http: //xxxx.github.io，然后再添加一个CNAME，主机记录写www，后面记录值也是http: //xxxx.github.io，这样别人用www和不用www都能访问你的网站（其实www的方式，会先解析成http: //xxxx.github.io，而根据CNAME再变成http: //xxx.com，即中间是经过一次转换的）。也有人写的是A记录，然后后面的记录值是写github page里面的ip地址但是有时候ip地址会更改，导致最后解析不正确，所以还是用CNAME别名记录要好些，不建议用ip。（以上为了URL不出错，在http:后面都加上了一个空格）

## 不确定是不是有问题
ping zhouzt21.github.io的时候，如果不连梯子就会出现2606:50c0:8000::153；
如果连上梯子就是正常的IP 185.199.110.153（神奇的是，这个IP貌似相比最开始注册的时候更换过，原来是类似185.199.108.153的东西）
这个过程中装了一些东西：node ？

## 问题的解决
1. 问题：hexo不能上传至gihub:
   解决：要在本地的hexo文件夹中用git管理（应该git init，建立一个.git的文件）。
   另外要重新hexo clean; hexo d（有了域名之后，不用再hexo s预览了，直接刷新网站）

2. 问题：报错说URL无效（另外改了一下config，感觉配置那块还是要check一下；难道是.md格式不能被渲染？但为什么报URL的错？），然后就没办法打开本地渲染

   解决：是因为这篇里"www. zhoutzt21.space"自动连接了域名，加了一个空格解决了；（重新hexo clean；hexo d；hexo s打开的又是之前的配置，没有渲染新的主题）

3. 问题：报错无法解析index.html

   解决：注意每一篇的标签，用---分割，同时变量冒号之后一定要加上空格


2023.10.30 重新搭建网站

安装git   node.js  hexo

```text
hexo init myBlog
```

已经上传了所有项目文件。

配置了新的主题信息。