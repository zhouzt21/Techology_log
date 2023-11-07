---
title: Git使用
date: 2023-04-15
tags: 技术
---

# Git使用


## 分支管理

checkout

站在某一个分支上合并另一个分支？

[(15条消息) Git学习（一）：Git介绍、仓库和分支等基本概念解释_代码仓库和分支有什么区别_sevenjoy007的博客-CSDN博客](https://blog.csdn.net/qq_37772475/article/details/107140061)

[(15条消息) Git使用入门，使用原理解读及如何在GitLab、GitHub或者Stash上管理项目（一）_小轩腾空的博客-CSDN博客](https://blog.csdn.net/xiaoxuantengkong/article/details/45231331)



## git常用指令

1. 本地的git 一定要改分支为main
2. commit必须要有提交信息
3. 必须commit 之后才能push
4. 常用指令
   1. git add -a
   2. git commit -m " "
   3. git branch -m  <原名字> <新名字>
   4. git remote add origin " " 
   5. git push origin main:main
   6. git submodule update --init --recursive


## 2023.3-4 搭建个人博客用github的坑

1. 远程建立的main、master都是不能直接修改的（不知道要怎么操作），建立了一个可以修改的hexo分支(直接在网页版上建立的）
2. 注意在本地和远程github关联的时候，要使用git remote给仓库建立别名（默认是origin，但是不知道为什么不好用，总是显示找不到origin），建立和查看的命令如下——

- 通过git remote -v查看
- git remote remove hexo删除远程仓库
- git remote add 别名 url（例如 git remote add zztrepo https://github.com/zhouzt21/zhozt21.github.io）
- 但是又遇到的问题是，github.com根本无法解析，ping不通，网上建议是在C:\Windows\System32\drivers\etc，hosts里更改加上如下两行（初步尝试了一下，确实可以通过这串IP打开github.com）：
  192.30.255.112  github.com git 
  185.31.16.184 github.global.ssl.fastly.net  

04-15踩坑

- 想拉取远程仓库并且合并本地分支（再push到远程），使用了git pull --rebase zztrepo hexo，结果重定义了这个分支，原来的.md文件都没有了，只剩下了远程已经push部署成功的

  - 解决方案：退回上一个版本

    ```
    git reflog  #查看本地操作记录
    git reset --hard 要返回的版本编号（HEAD前的编码）
    
     git rebase --abort #取消rebase
    ```

- rebase的功能：

- 怀疑的问题：目前的hexo分支与远程的hexo分支不对应，不能直接push
