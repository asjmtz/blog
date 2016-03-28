title: Yeoman 安装
date: 2014-06-11 11:06:59
categories: Yeoman
description: Yeoman 安装过程介绍
---


官网：http://yeoman.io/learning/index.html
中文官网：http://yeomanjs.org/index.html

##安装yo

安装yo之前，注意先有node环境和git。
在windows下，安装完git程序后，还要将他的bin目录添加到PATH中，才能全局调用git命令。

```
npm install -g yo
```
我的yo版本是1.1.2

一般会自动安装`grunt`和`bower`，如果是老版本，需手动安装他们，类似

```
npm install -g grunt-cli bower
```

##架设生成器

安装`generator-webapp`生成器

```
npm install -g generator-webapp
```

建立目录`mkdir my-yo-project cd my-yo-project`

在工程目录下，命令

```
yo webapp
```

Each project created with yo will also pull in relevant Grunt tasks which the community feels is needed or useful for your workflow.

整个工作流程，应该是
```
yo webapp
grunt server
```
和中文官网的例子有些不同，就是中间的`bower install <dep>`那步是可以选择的，即如果webapp安装的时候，没有自动安装的依赖的库可以之后，手动添加，bower介绍看下面

#Bower
---
Bower包管理工具，部署JS、HTML、css、images等。

```
# Search for a dependency in the Bower registry.
# 搜包
bower search <dep>

# Install one or more dependencies.
# 安装依赖
bower install <dep>..<depN>

# List out the dependencies you have installed for a project.
# 显示已安装的依赖
bower list

# Update a dependency to the latest version available.
# 更新依赖到最新版本
bower update <dep>

```

在yo架设的项目里面使用Bower管理

以下是安装jQuery插件的例子

```
# Scaffold a new application.
yo webapp

# Search Bower's registry for the plug-in we want.
bower search jquery-pjax

# Install it and save it to bower.json
bower install jquery-pjax --save

# If you're using RequireJS...
# (be aware that currently the webapp generator does not include RequireJS and the following command only applies to generators that do)
grunt bower
> Injects your Bower dependencies into your RequireJS configuration.

# If you're not using RequireJS...
grunt bowerInstall
> Injects your dependencies into your index.html file.
```


