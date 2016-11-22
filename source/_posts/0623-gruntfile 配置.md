title: Grunt 配置文件
date: 2014-06-23 11:06:59
categories: Grunt
description: 如何使用 Grunt 的配置文件 gruntfile 来管理前端项目开发
---

##Grunt配置
Grunt的task配置都是在 `gruntfile` 中的 `grunt.initConfig` 方法中指定的。此配置主要是以任务名称命名的属性，也可以包含其他任意数据。一旦这些代表任意数据的属性与任务所需要的属性相冲突，就将被忽略。

此外，由于这本身就是JavaScript，因此你不仅限于使用JSON；你可以在这里使用任何有效的JavaScript。必要的情况下，你甚至可以以编程的方式生成配置。

```
grunt.initConfig({
  concat: {
    // 这里是concat任务的配置信息。
  },
  uglify: {
    // 这里是uglify任务的配置信息
  },
  // 任意数据。
  my_property: 'whatever',
  my_src_files: ['foo/*.js', 'bar/*.js'],
});
```

##文件
[fs.Stats方法名]:http://nodejs.org/docs/latest/api/fs.html#fs_class_fs_stats
[node-glob]:https://github.com/isaacs/node-glob
[minimatch]:https://github.com/isaacs/minimatch
由于大多的任务都是执行文件操作，Grunt有一个强大的抽象层用于声明任务应该操作哪些文件。这里有好几种定义**src-dest**(源文件-目标文件)文件映射的方式，均提供了不同程度的描述和控制操作方式。任何一种多任务（multi-task）都能理解下面的格式，所以你只需要选择满足你需求的格式就行。

所有的文件格式都支持 `src` 和 `dest` 属性，此外"Compact"[简洁]和"Files Array"[文件数组]格式还支持以下一些额外的属性：

* `filter` 它通过接受任意一个有效的[fs.Stats方法名]或者一个函数来匹配 `src` 文件路径并根据匹配结果返回true或者false。
* `nonull` 如果被设置为 true，未匹配的模式也将执行。结合Grunt的`--verbore`标志, 这个选项可以帮助用来调试文件路径的问题。
* `dot` 它允许模式模式匹配句点开头的文件名，即使模式并不明确文件名开头部分是否有句点。
* `matchBase` 如果设置这个属性，缺少斜线的模式(意味着模式中不能使用斜线进行文件路径的匹配)将不会匹配包含在斜线中的文件名。 例如，a?b将匹配`/xyz/123/acb`但不匹配 `/xyz/acb/123` 。
* `expand` 处理动态的`src-dest`文件映射，更多的信息请查看**动态构建文件对象**。
其他的属性将作为匹配项传递给底层的库。 请查看[node-glob] 和[minimatch] 文档以获取更多信息。

## 文件配置

###**Files - 通配符**

- `*` 匹配任何字符，除了 `/`
- `?` 匹配任何字符，除了 `/`
- `**` 匹配任何字符，包括 `/`，所以用在目录的路径里面
- `{}` 逗号分割的“或”操作（逗号后面不要有空格）
- `!` 排除某个匹配
```
//所有th开头的js文件
{ src: 'foo/th*.js', dest: ... }

//所有a或b开头的js文件
{ src: 'foo/{a,b}*.js', dest: ... }
//也可以分开写成这样
{ src: ['foo/a*.js', 'foo/b*.js'], dest: ... }

//所有的js文件
{ src: 'foo/*.js', dest: ... }

//所有的js文件,除了bar.js
{ src: ['foo/*.js','!foo/bar.js'], dest: ... }

```
###Files - 简洁格式 - Compact Format
每个target只能指定一对 `src-dest`
```
grunt.initConfig({
  jshint: {
    foo: {
      src: ['src/aa.js', 'src/aaa.js']
    },
  },
  concat: {
    bar: {
      src: ['src/bb.js', 'src/bbb.js'],
      dest: 'dest/b.js',
    },
  },
});
```

###Files - 文件对象格式 - Files Object Format
每个目标对应多个`src-dest`形式的文件映射，属性名就是目标文件，源文件就是它的值(源文件列表则使用数组格式声明)
```
grunt.initConfig({
  concat: {
    foo: {
      files: {
        'dest/a.js': ['src/aa.js', 'src/aaa.js'],
        'dest/a1.js': ['src/aa1.js', 'src/aaa1.js'],
      },
    },
    bar: {
      files: {
        'dest/b.js': ['src/bb.js', 'src/bbb.js'],
        'dest/b1.js': ['src/bb1.js', 'src/bbb1.js'],
      },
    },
  },
});
```

###Files - 文件数组格式 - Files Array Format
支持每个目标对应多个`src-dest`文件映射，同时也允许每个映射拥有**额外**属性

```
grunt.initConfig({
   concat: {
    foo: {
      files: [
        {src: ['src/aa.js', 'src/aaa.js'], dest: 'dest/a.js'},
        {src: ['src/aa1.js', 'src/aaa1.js'], dest: 'dest/a1.js'},
      ],
    },
    bar: {
      files: [
        {src: ['src/bb.js', 'src/bbb.js'], dest: 'dest/b/', nonull: true},
        {src: ['src/bb1.js', 'src/bbb1.js'], dest: 'dest/b1/', filter: 'isFile'},
      ],
    },
  },
});
```


[gruntfileConfig]:http://www.gruntjs.net/docs/configuring-tasks/
##下面还有好多栗子在[这里][gruntfileConfig]，不写了