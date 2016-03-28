title: Grunt 安装笔记
date: 2014-06-23 11:06:59
categories: Grunt
description: Grunt 初学之 Grunt 项目的基本介绍
---


#grunt

[start-en]:http://gruntjs.com/getting-started
[start-zn]:http://www.gruntjs.net/docs/getting-started/
[grunt-cmd-src]:https://github.com/gruntjs/grunt-cli/blob/master/bin/grunt
[devDepend-src]:https://www.npmjs.org/doc/json.html#devDependencies
[package-doc]:https://www.npmjs.org/doc/json.html
[grunt.initConfig]:http://www.gruntjs.net/api/grunt/#grunt.initconfig
[grunt-contrib-uglify]:http://github.com/gruntjs/grunt-contrib-uglify
>[官网英文教程:getting-started][start-en]
[官网中文教程:快速入门][start-en]

###安装 CLI
---
```
npm install -g grunt-cli
```
安装 `grunt-cli` 并不等于安装了Grunt！

>Grunt CLI的任务很简单：调用与 `Gruntfile` 在同一目录中Grunt。这样带来的好处是，允许在同一机器同一系统上安装多个版本的Grunt。


###CLI 是如何工作的
---
每次运行 `grunt` 时，他就利用node提供的 `require()` 系统查找本地安装的 Grunt。正是由于这一机制，你可以在项目的任意子目录中运行`grunt` 。

如果找到一份本地安装的 Grunt，CLI就将其加载，并传递 `Gruntfile` 中的配置信息，然后执行你所指定的任务。

_为了更好的理解 Grunt CLI的执行原理，请[参考源码][grunt-cmd-src]。其实代码很短！_

###准备一份新的 Grunt 项目
---
一般需要在你的项目中添加两份文件：`package.json` 和 `Gruntfile`。

**package.json**: 此文件被npm用于存储项目的元数据，以便将此项目发布为npm模块。你可以在此文件中列出项目依赖的grunt和Grunt插件，放置于[devDependencies][devDepend-src]配置段内。

**Gruntfile**: 此文件被命名为 `Gruntfile.js` 或 `Gruntfile.coffee`，用来配置或定义任务（task）并加载Grunt插件的。

###package.json
---
 `package.json` 应当放置于项目的根目录中，与 `Gruntfile` 在同一目录中，并且应该与项目的源代码一起被提交。在上述目录(package.json所在目录)中运行 `npm install` 将依据 `package.json` 文件中所列出的每个依赖来自动安装适当版本的依赖。

下面列出了几种为你的项目创建 `package.json` 文件的方式：

- 大部分 grunt-init 模版都会自动创建特定于项目的package.json文件。

- npm init命令会创建一个基本的package.json文件。

- 复制下面的案例，并根据需要做扩充，参考此[说明][package-doc].

```
{
  "name": "my-project-name",
  "version": "0.1.0",
  "devDependencies": {
    "grunt": "~0.4.1",
    "grunt-contrib-jshint": "~0.6.0",
    "grunt-contrib-nodeunit": "~0.2.0",
    "grunt-contrib-uglify": "~0.2.2"
  }
}
```

###安装Grunt 和 grunt插件
---
向已经存在的 `package.json` 文件中添加Grunt和grunt插件的最简单方式是通过`npm install <module> --save-dev` 命令。此命令不光安装了 `<module>` ，还会自动将其添加到devDependencies 配置段中，遵循tilde version range格式。

例如，下面这条命令将安装Grunt最新版本到项目目录中，并将其添加到devDependencies内：
```
npm install grunt --save-dev
```
同样，grunt插件和其它node模块都可以按相同的方式安装。安装完成后一定要记得将被修改的 `package.json` 文件提交到源码管理器中。

###Gruntfile
---
 `Gruntfile.js` 或 `Gruntfile.coffee` 文件是有效的 JavaScript 或 CoffeeScript 文件，应当放在你的项目根目录中，和 `package.json` 文件在同一目录层级，并和项目源码一起加入源码管理器。

Gruntfile由以下几部分构成：

- "wrapper" 函数
- 项目与任务配置
- 加载grunt插件和任务
- 自定义任务

###Gruntfile文件案例
---
在下面列出的这个 `Gruntfile` 中，`package.json`文件中的项目元数据（metadata）被导入到 Grunt 配置中， `grunt-contrib-uglify` 插件中的`uglify` 任务（task）被配置为压缩（minify）源码文件并依据上述元数据动态生成一个文件头注释。当在命令行中执行 `grunt` 命令时，`uglify` 任务将被默认执行。
```
module.exports = function(grunt) {

  // Project configuration.
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });

  // 加载包含 "uglify" 任务的插件。
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // 默认被执行的任务列表。
  grunt.registerTask('default', ['uglify']);

};
```
前面已经向你展示了整个 Gruntfile，接下来将详细解释其中的每一部分。

###"wrapper" 函数
---
每一份 `Gruntfile` （和grunt插件）都遵循同样的格式，你所书写的Grunt代码必须放在此函数内：
```
module.exports = function(grunt) {
  // Do grunt-related things in here
};
```
###项目和任务配置
---
大部分的Grunt任务都依赖某些配置数据，这些数据被定义在一个object内，并传递给[grunt.initConfig][grunt.initConfig] 方法。

在下面的案例中，`grunt.file.readJSON('package.json')`  将存储在 `package.json` 文件中的JSON元数据引入到grunt config中。 由于 `<% %>` 模板字符串可以引用任意的配置属性，因此可以通过这种方式来指定诸如**文件路径**和**文件列表类型**的配置数据，从而减少一些重复的工作。

你可以在这个配置对象中(传递给initConfig()方法的对象)存储任意的数据，只要它不与你任务配置所需的属性冲突，否则会被忽略。此外，由于这本身就是JavaScript，你不仅限于使用JSON；你可以在这里使用任意的有效的JS代码。如果有必要，你甚至可以以编程的方式生成配置。

与大多数task一样，[grunt-contrib-uglify][grunt-contrib-uglify] 插件中的uglify 任务要求它的配置被指定在一个同名属性中。在这里有一个例子, 我们指定了一个banner选项(用于在文件顶部生成一个注释)，紧接着是一个单一的名为build的uglify目标，用于将一个js文件压缩为一个目标文件。
```
// Project configuration.
grunt.initConfig({
  pkg: grunt.file.readJSON('package.json'),
  uglify: {
    options: {
      banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
    },
    build: {
      src: 'src/<%= pkg.name %>.js',
      dest: 'build/<%= pkg.name %>.min.js'
    }
  }
});
```
###加载 Grunt 插件和任务
---
[concatenation]:https://github.com/gruntjs/grunt-contrib-concat
[minification]:http://github.com/gruntjs/grunt-contrib-uglify
[linting]:https://github.com/gruntjs/grunt-contrib-jshint
[grunt插件]:https://github.com/gruntjs
像 [concatenation]、[grunt-contrib-uglify] 和[linting]这些常用的任务（task）都已经以[grunt插件]的形式被开发出来了。只要在 `package.json` 文件中被列为dependency（依赖）的包，并通过 `npm install` 安装之后，都可以在 `Gruntfile` 中以简单命令的形式使用：
```
// 加载能够提供"uglify"任务的插件。
grunt.loadNpmTasks('grunt-contrib-uglify');
```
注意： `grunt --help` 命令将列出所有可用的任务。

###自定义任务
---
[grunt.loadTasks]:http://www.gruntjs.net/api/grunt/#grunt.loadtasks
通过定义 `default` 任务，可以让Grunt默认执行一个或多个任务。在下面的这个案例中，执行 `grunt` 命令时如果不指定一个任务的话，将会执行`uglify`任务。这和执行 `grunt uglify` 或者 `grunt default` 的效果一样。`default` 任务列表数组中可以指定任意数目的任务（可以带参数）。
```
// Default task(s).
grunt.registerTask('default', ['uglify']);
```
如果Grunt插件中的任务（task）不能满足你的项目需求，你还可以在Gruntfile中自定义任务（task）。例如，在下面的 Gruntfile 中自定义了一个default 任务，并且他甚至不依赖任务配置：
```
module.exports = function(grunt) {

  // A very basic default task.
  grunt.registerTask('default', 'Log some stuff.', function() {
    grunt.log.write('Logging some stuff...').ok();
  });

};
```
特定于项目的任务不必在 Gruntfile 中定义。他们可以定义在外部.js 文件中，并通过[grunt.loadTasks] 方法加载。

---

以上是grunt中文网上的get-started教程的抄写版，下面是我自己的流程表

1. 全局安装 `grunt-cli` ，即 `npm install -g grunt-cli`
2. 在项目文档的根目录，写 `pacakage.json` ,里面包括项目名称，版本号，还有`devDependencies` 即一些grunt和grunt插件，还可以定义**文档的目录**等之后可能会用到的属性
3. 根据package.json中写的的 `Dependencies` ,用 `npm install <grunt-plungin> --save-dev ` 安装 `grunt`及其他需要的插件。插件安装完后，还会根据安装的版本，依照这个tilde version range格式，更改 `package.json`里面的值
4. 接下来写gruntfile.js文件，放在 `package.json`一起的根目录。里面是grunt插件的配置与使用，还可以自定义任务，具体看上面