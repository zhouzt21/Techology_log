---
title: Git使用
date: 2023-04-15
tags: 技术
---

# Git使用

## 版本控制

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



- 要有的习惯：
  - 及时备份重要文件再进行风险操作
  - 对于未知的命令先弄清楚，不要想当然



## 分支管理

checkout

站在某一个分支上合并另一个分支？

[(15条消息) Git学习（一）：Git介绍、仓库和分支等基本概念解释_代码仓库和分支有什么区别_sevenjoy007的博客-CSDN博客](https://blog.csdn.net/qq_37772475/article/details/107140061)

[(15条消息) Git使用入门，使用原理解读及如何在GitLab、GitHub或者Stash上管理项目（一）_小轩腾空的博客-CSDN博客](https://blog.csdn.net/xiaoxuantengkong/article/details/45231331)