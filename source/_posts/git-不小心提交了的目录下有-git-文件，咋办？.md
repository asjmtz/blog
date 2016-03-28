title: git 不小心提交了的目录下有 .git 文件，咋办？
date: 2015-04-22 17:31:15
categories: git
description: git submodule 错误
---

## git 文件目录下有 `.git` 文件

不小心把子目录下一个 .git 文件给 add commit 了，于是没有办法再跟踪 这个目录下文件的变化。
submodules 什么的真是

发现强制删除次文件夹之后，然后再添加就可以了，记得再添加的时候，把文件夹里面的.git 文件删除掉先。
1、 强制删除

```
rm -rf xxx/needtodelete/
```

2、添加没有 .git 文件的文件夹 

3、重新加到主 git 库
```
git add . && git commit -m 'add dir'
```

这样又能重新管理这个文件夹的版本控制了，o(∩_∩)o 哈哈

> Written with [StackEdit](https://stackedit.io/).