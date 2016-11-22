title: Grunt 常用插件
date: 2014-06-24 11:06:59
categories: Grunt
description: 使用 Grunt 时常用的几个插件的配置方法
---

[grunt中文网]:http://www.gruntjs.net/docs/sample-gruntfile/
[《Grunt.js 在前端项目中的实战》][1]文章的实战部分也有介绍一些常用的插件
[grunt插件in Github][2]
##前期准备
通过[grunt中文网]上的案例，memo一下常用的grunt插件的使用
首先从`package.json`文件读入配置信息，并存入`pkg`属性内。这样就可以让我们访问到 `package.json` 文件中列出的属性了，如下：
```
module.exports = function(grunt) {
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json')
  });
};
```

##grunt-contrib-concat【合并文件】
###代码
```
concat: {
  options: {
    // 定义一个用于插入合并输出文件之间的字符
    separator: ';'
  },
  dist: {
    // 将要被合并的文件
    src: ['src/**/*.js'],
    // 合并后的JS文件的存放位置
    dest: 'dist/<%= pkg.name %>.js'
  }
}
```

###说明
Grunt自带的有一个简单的模板引擎用于输出配置对象的属性值(这里是指 `package.json` 中的配置对象)。这里是让`concat`任务将所有存在于`src/`目录下以`.js`结尾的文件合并起来，然后存储在`dist`目录中，并以项目名 `package.json` 来命名。

##grunt-contrib-uglify【压缩JS】
###代码
```
uglify: {
  options: {
    // 此处定义的banner注释将插入到输出文件的顶部
    banner: '/*! <%= pkg.name %> <%= grunt.template.today("dd-mm-yyyy") %> */\n'
  },
  dist: {
    files: {
      'dist/<%= pkg.name %>.min.js': ['<%= concat.dist.dest %>']
    }
  }
}
```
###说明
上面是配置uglify插件的代码，它的作用是压缩（minify）JavaScript文件。

这里我们让`uglify`在`dist/`目录中创建了一个包含压缩结果的JavaScript文件。注意这里我使用了 `<%= concat.dist.dest %>`，因此 `uglify` 会自动压缩 `concat` 任务中生成的文件。

###扩展
* 这里可以用 `concat.attr` 这种写法取到 `contact `配置的值，是不是说明在`grunt.initConfig({})`声明的属性,可以使用比自己先声明的配置的属性呢？
或是后声明的也能取到值呢？
* 这里可以交换顺序试一试


##grunt-contrib-cssmin【压缩JS】
###代码
```
//压缩成为一个css
cssmin: {
  options: {
    // 此处定义的banner注释将插入到输出文件的顶部
    banner: '/*! <%= pkg.name %> <%= grunt.template.today("dd-mm-yyyy") %> */\n'
  },
  dist: {
    files: {
      'dist/<%= pkg.name %>.min.css': ['<%= path/to/**/*.css  %>']
    }
  }
}

//每个分别压
cssmin: {
  minify: {
    expand: true,
    cwd: 'release/css/',
    src: ['*.css', '!*.min.css'],
    dest: 'release/css/',
    ext: '.min.css'
  }
}

```
###说明
压缩CSS文件

##grunt-contrib-imagemin【压缩图片】
###代码
```
  imagemin: {                          // Task
    static: {                          // Target
      options: {                       // Target options
        optimizationLevel: 3,
        use: [mozjpeg()]
      },
      files: {                         // Dictionary of files
        'dist/img.png': 'src/img.png', // 'destination': 'source'
        'dist/img.jpg': 'src/img.jpg',
        'dist/img.gif': 'src/img.gif'
      }
    },
    dynamic: {                         // Another target
      files: [{
        expand: true,                  // Enable dynamic expansion
        cwd: 'src/',                   // Src matches are relative to this path
        src: ['**/*.{png,jpg,gif}'],   // Actual patterns to match
        dest: 'dist/'                  // Destination path prefix
      }]
    }
  }
```

###说明
压缩图片，对图片进行优化处理，根据以下几个优化器

- [gifsicle][3] — Compress GIF images
- [jpegtran][4] — Compress JPEG images
- [optipng][5] — Compress PNG images
- [svgo][6] — Compress SVG images




##grunt-contrib-qunit【测试任务】
###代码
```
qunit: {
  files: ['test/**/*.html']
},
```
###说明
QUnit插件的设置非常简单。 你只需要给它提供用于测试运行的文件的位置，注意这里的QUnit是运行在HTML文件上的

###扩展
他是`jQuery`家族的一员
>QUnit is a powerful, easy-to-use JavaScript unit testing framework. It's used by the jQuery, jQuery UI and jQuery Mobile projects and is capable of testing any generic JavaScript code, including itself!

测试框架也没有常用过，详细的另外写个memo，叫做《JA Unit Testing学习笔记》吧！

##grunt-contrib-jshint
###代码
```
jshint: {
  // define the files to lint
  files: ['gruntfile.js', 'src/**/*.js', 'test/**/*.js'],
  // configure JSHint (documented at http://www.jshint.com/docs/)
  options: {
      // more options here if you want to override JSHint defaults
    globals: {
      jQuery: true,
      console: true,
      module: true
    }
  }
}
```
###说明
[Jshint][7]是一个JS代码书写风格检测器，具体的在线[demo][8],使用一些参数可以让Jhint用某种规则来检测你的代码。

###扩展
[《一些达成共识的JavaScript编码风格约定》][9]
[《JSHint 使用说明》][10]

##grunt-contrib-watch
###代码
```
watch: {
  files: ['<%= jshint.files %>'],
  tasks: ['jshint', 'qunit']
}
```
###说明
`grunt-contrib-watch` 是开发必备插件，用来监控文件的修改，然后自动运行`grunt`任务，省去一遍遍手动执行Grunt命令，
###扩展
[《Grunt.js 在前端项目中的实战》][11]文章的实战部分也有介绍一些常用的插件

##结语
整个实例请看[grunt中文网]这个网页


  [1]: http://www.tuicool.com/articles/zQ7jmm
  [2]: https://github.com/gruntjs
  [3]: https://github.com/kevva/imagemin-gifsicle
  [4]: https://github.com/kevva/imagemin-jpegtran
  [5]: https://github.com/kevva/imagemin-optipng
  [6]: https://github.com/kevva/imagemin-svgo
  [7]: http://www.jshint.com/docs/
  [8]: http://www.jshint.com/
  [9]: http://www.iteye.com/news/28028-JavaScript-code-style-guide
  [10]: http://zhang.zipeng.info/vimwiki/Entries/Reference/Tools/jshint.html
  [11]: http://www.tuicool.com/articles/zQ7jmm