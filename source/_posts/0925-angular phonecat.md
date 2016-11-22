title: Angular Lesson 1
date: 2014-09-25 11:06:59
categories: Angular
description: Angular phonecat 教程 part 1 —— 介绍
---

## angular lesson 1

据说是官方的lesson教程，angular-phonecat

### 环境安装
首先你得安装有下面两个东西，就像烤面包先得有烤箱。
	1. git
	2. node

然后打开git bush，进入你的工程目录，输入
```
git clone https://github.com/angular/angular-phonecat.git
```

讲这个项目从github弄下面后，`cd angular-phonecat` 进入这个目录，npm安装依赖包 `node install`。node_modules目录下面会安装以下工具：

- Bower - client-side code package manager
- Http-Server - simple local static web server
- Karma - unit test runner
- Protractor - end to end (E2E) test runner

这里有些启动他们的命令：

-  `npm start`  : start a local development web-server
-  `npm test` : start the Karma unit test runner
-  `npm run protractor`  : run the Protractor end to end (E2E) tests
-  `npm run update-webdriver`  : install the drivers needed by Protractor

如果前面的步骤都顺利的完成了，现在就可以通过 `npm start` 启动node的 http server 了。
打开浏览器，进入http://localhost:8000/app/index.html，看到一句Nothing here yet!  说明成功了。



> Written with [StackEdit](https://stackedit.io/).