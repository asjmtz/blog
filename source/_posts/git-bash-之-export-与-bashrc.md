title: "git bash 之 export 与 ~/.bashrc"
date: 2015-04-30 10:05:35
categories: git
description: git bash 之 export 与 ~/.bashrc
---

在 git bash 里面，用 export 命令可以显示出当前命令行工具的变量。常见的如 `PATH` 或 `PWD` 等。

### 1. 环境变量 in Windows
- 在环境变量里面增加变量，需要重启电脑才会在生效之后，才能 `export` 的时候读到新变量。

### 2. ~/.bashrc 文件
- ~/.bashrc 文件会在每次打开 git bash 时候被执行
- 在 ~/.bashrc 文件中写入函数，可以在 git bash 中用函数名执行函数中的命令
- ~/.bashrc 文件中用 `export http_proxy=http://proxy.XXX.com:8080` 的形式，可以向命令行添加变量，在每次打开 git bash 后会执行。
