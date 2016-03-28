title: "git config 常用命令"
date: 2015-04-30 10:11:17
categories: git
description: git config 常用命令
---

P.S:  做全局的设置时，可以这样 `git config --global`

### 帮助

```
git config --help # 打开详细版手册
git config -h     # 简单暴力我喜欢
```

### 查看
```
git config -l 
git config --list
```

### 修改

没有特殊情况下，都可以设置为全局配置。
```
git config --global alias.co checkout
```

### 增加

如果事先没有这个配置名称，git 会提示你在前面增加  `--add` 命令

```
git config --global --add core.editor "'C:\xxx\xxx\sublime_text.ext' --wait"
```


### 删除

```
git config --global --unset alias.co checkout
```

如果提示什么有多个相同的设置。。。的英文时，按照错误指示
```
git config --global --unset-all alias.co checkout
```

不得不说，git 的提示是挺周到的。

## git config 小抄

下面是我现在用的配置

### 1. 设置别名

有些常用命令略长，当 tab 键也不能释放我们的愤怒时，可以试试给他们起外号。

```
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.st status
git config --global alias.mg 'merge --no-ff'
```
### 2.设置默认浏览器

在 git commit 的时候，可以使用浏览器来写提交注释，基本告别了挤在一行的悲惨命运

P.S :  Windows 环境，sublime text 3 版本
```
git config --global --add core.editor "'C:\xxx\xxx\sublime_text.ext' --wait"
```
 