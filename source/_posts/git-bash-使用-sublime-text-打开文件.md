title: "git bash 使用 sublime text 打开文件"
date: 2015-04-30 10:09:46
categories: git
description: git bash 使用 sublime text 打开文件
---

用 hexo 写博客时，使用 `hexo new postname` 新建了一个新的文件，每次想直接打开都要从文件夹打开，实在不符合我们这些 Geek（懒人）的做派。纠结很久之后，终于找到这篇大大的[博客](https://danlimerick.wordpress.com/2014/01/07/git-for-windows-tip-opening-sublime-text-from-bash/)，他的解决办法如下。

### 1. 新建一个文件命名为你想要的命令，比如 `subl` 没有后缀（重要），内容如下：

```
#!/bin/sh
"C:\Program Files\Sublime Text 2\sublime_text.exe" $1 &
```

- 第一行是说这是个 shell 脚本
- 第二行的字符串是sublime 的安装目录
- 第二行的$1 是取的命令之后输入的参数
- 第二行的&是此命令在后台打开，这样sublime打开之后，就不会阻塞你的git bash

### 2. 保存到 `C:\Program Files (x86)\Git\bin` 目录下

### 3. 使用方法

```
subl text.txt
```
在当前目录打开一个新文件

```
subl .
```


是不是 so easy ！shell 脚本麻吉强大，此时墙裂后悔上课时(～﹃～)~zZ啊。
