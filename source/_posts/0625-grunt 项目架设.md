title: grunt 项目架设
date: 2014-06-25 11:06:59
categories: Grunt
description: Grunt 搭建项目的时候的基本流程，只是自己经验的记录
---

>参考文章
[《前端工作流，Grunt上手指南》][1]
[《Grunt+Livereload 搭建本地前端开发环境》][2]

废话就不多说了，直接开始架设一个grunt控制的项目吧！O(∩_∩)O

##1. 假设你已经安装了nodeJS和用npm全局安装了grunt-cli。

##2. 新建package.json和Gruntfile.js
先进入项目文件夹的根目录下
```
$ mkdir pj
$ cd pj
```

###package.json

根据上面的参考文章，直接执行`npm install`会生成一个空的package.js，但是我尝试之后失败了，是版本的问题吗？
顺便一说，我的版本是
```
$ grunt -V 
grunt-cli v0.1.13
grunt v0.4.5

$ npm -v
1.4.9
```
于是从前几篇的grunt笔记里面copy一个现有的`package.json` ，里面写好了要安装的一些常用的grunt项目插件包括grunt本身，大概酱紫。
```
{
  "name": "pj3",
  "version": "0.1.0",
  "devDependencies": {
    "grunt": "^0.4.5",
    "grunt-contrib-concat": "^0.4.0",
    "grunt-contrib-jshint": "^0.10.0",
    "grunt-contrib-nodeunit": "^0.4.1",
    "grunt-contrib-qunit": "^0.5.1",
    "grunt-contrib-uglify": "^0.5.0",
    "grunt-contrib-watch": "^0.6.1"
  }
}

```
好了，现在执行一下 `npm install`就会自动帮你安装以上列出来的依赖。而且安装好之后根据安装的版本，会更改到你的package.json的文件中。所以我上面的package.json其实是已经安装，并被更改过的了。现在文件结构是
```
pj/
|-- node_modules
`-- package.json
```

###Gruntfile.js

可以从之前的笔记上或者官网上手动创建，下面介绍用 `grunt-init`安装一些架构的方法。
1. 全局安装`npm install -g grunt-init`
2. 克隆要使用的模板,下面是clone到一个创建一个包含QUnit单元测试的jQuery插件的模板，模板更多介绍[在这里][3]
```
git clone https://github.com/gruntjs/grunt-init-jquery.git ~/.grunt-init/jquery
```
3. 我用上面的方法clone了一个 `grunt-init-gruntfile` ，然后再执行 
```    
grunt-init  gruntfile
```
好了，现在grunt-init已经根据你的package.json的配置，帮你写好一个Gruntfile.js，可以在此模板上根据需要修改下

然后我手贱安装了下 `grunt-init jquery`，假装我要写一个叫jquery.barAnime的插件，于是在执行安装之前，grunt-init细心的询问了我各种问题，然后生成一个 `jquery.barAnime.jquery.json`来记录。=>（可能是我起的名字问题，json名字略奇葩

目录变成酱紫了，欧也\(^o^)/
```
pj/
|-- node_modules/
|-- libs/
|-- src/
|-- test/
|-- .gitignore
|-- .jshintrc
|-- CONTRIBUTING.md
|-- jquery.barAnime.jquery.json
|-- LICENSE-MIT
|-- README.md
|-- Gruntfile.js
`-- package.json
```

生成了一些奇怪的文件，发现里面还有一个`LICENSE-MIT`，小白的我瞬间觉得UP了(⊙o⊙)哦

##3. 执行grunt命令
根据Gruntfile.js里面的配置，执行 `grunt`命令时，会执行 `default`任务
```
 grunt.registerTask('default', ['jshint', 'qunit', 'clean', 'concat', 'uglify']);
```
我用 `grunt-init jquery` 自动生成的Gruntfile里面主要执行的就是以上几个命令，根目录会多出一个dist文件夹，里面是`concat`合并的 `jquery.barAnime.js` 和 `uglify`压缩的 `jquery.barAnime.min.js`。

##4. 手动建立一个项目，并创建livereload

利用 `grunt watch` 和 `grunt connect` 和 `connect-livereload`的配合就能省去F5与浏览器的livereload插件了，啊哈哈，快点开始吧

参照这个大大的[文章][2]。我重新新建了一个项目，叫做pj4的
按照上面的步骤，我安装了与上面一样的grunt和grunt插件，为了livereload的实现，还得再安装一些东西
```
npm install --save-dev grunt-contrib-connect connect-livereload grunt-open
```
现在的文档目录如下。

```
    pj4/
    |-- node_modules/
    |-- src/
    |    |-- js/
    |    |-- css/
    |    |-- img/
    |    `-- index.html
    |-- Gruntfile.js
    `-- package.json
```
下面是特别重要的Gruntfile.js的内容。

```
/**
 * 自动化脚本定义
 */
module.exports = function (grunt) {
  'use strict';

  //load all grunt tasks
  require('matchdep').filterDev('grunt-*').forEach(grunt.loadNpmTasks);

  //define tasks
  grunt.registerTask('server', ['connect:server', 'open:server', 'watch:server']);

  //env cfg
  var pkg = grunt.file.readJSON('package.json');
  var cfg = {
    src: 'src',
    // Change 'localhost' to '0.0.0.0' to access the server from outside.
    serverHost: 'localhost',
    serverPort: 9000,
    livereload: 35729
  };  

  //grunt config
  grunt.initConfig({
    //======== 配置相关 ========
    pkg: pkg,
    cfg: cfg,

    //======== 开发相关 ========
   //开启服务
    connect: {
      options: {
        port: cfg.serverPort,
        hostname: cfg.serverHost,
        middleware: function(connect, options) {
          return [
            require('connect-livereload')({
              port: cfg.livereload
            }),
            // Serve static files. 
            //In my environment options.base is a type of Object
            connect.static(options.base.toString()),
            // Make empty directories browsable.
             connect.directory(options.base),
          ];
        }
      },
      server: {
        options: {
          // keepalive: true,
          base: cfg.src,
        }
      }
    },

    //打开浏览器
    open: {
      server: {
        url: 'http://localhost:' + cfg.serverPort
      }
    },

    //监控文件变化
    watch: {
      options: {
        livereload: cfg.livereload,
      },
      server: {
        files: [
        '<%= cfg.src %>/{,*/}*.html',
        "<%= cfg.src %>/css/*.css",
        "<%= cfg.src %>/js/*.js",
        "<%= cfg.src %>/img/*"
        ],
        // tasks: [''],       
      },
    }
  });
};
```
然后执行 `grunt server`，就会在你的浏览器打开这个地址 `http://localhost:9000`。



~~的撒大声地~~
  [1]: http://www.hulufei.com/post/grunt-introduction
  [2]: http://my.oschina.net/liuyong25/blog/140110#OSC_h4_6
  [3]: http://www.gruntjs.org/article/project_scaffolding.html