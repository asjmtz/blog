title: 怎样还原已经推送的 git commit？
date: 2016-03-29 22:06:00
categories: git
description: 在 git 中，push 一个 commit 后，如果想要还原文件到之前的 commit 点上，应该怎么做呢？ 
s: git-howto-revert-a-commit-already-pushed-to-a-remote-reposit
---

> 原文：[Git Howto: Revert a Commit Already Pushed to a Remote Repository](http://christoph.ruegg.name/blog/git-howto-revert-a-commit-already-pushed-to-a-remote-reposit.html)

### 怎样还原已经推送的 git commit？

如果当你刚刚推送一个本地分支到远端分支上，结果发现这个 `commit` 有些错误，是不应该的存在。

好吧，`Git` 大法是不会这么轻易的 go die 的，我们来搞定它。不过我们得在别人更新到这个 `commit` 之前消灭他，不然之后就只有呵呵了 <(￣oo,￣)/

首先这介绍两个方法，他们会完整保存已提交的历史记录：

