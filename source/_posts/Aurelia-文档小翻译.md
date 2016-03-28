title: "Aurelia 文档小翻译"
date: 2015-06-09 15:52:02
categories: Aurelia
description: 在学习 Aurelia，整篇的英文文档很容易分神，顺手翻译一下文档，还有很多不懂的地方啊 
---


##Browser Support

Aurelia 支持现代浏览器，包括 Chrome，Firefox，IE11，Safari 8。但是我们也提供兼容 IE9 及以上的方法。

为了需要兼容 IE9 及以上，我们需要为 MutationObservers 添加一个额外的 polyfill。实现方法，用 `jspm` 安装 `github:polymer/mutationobservers`。接着在 `aurelia-bootstrapper` 的外层这样调用它：

```
<script src="jspm_packages/github/polymer/mutationobservers@0.4.2/MutationObserver.js"></script>
<script src="jspm_packages/system.js"></script>
<script src="config.js"></script>
<script>
  // Loads WeakMap polyfill needed by MutationObservers
  System.import('core-js').then( function() {
    // Imports MutationObserver polyfill
    System.import('mutationobservers').then( function() {
      // Ensures start of Aurelia when all required IE9 dependencies are loaded
      System.import('aurelia-bootstrapper');
    })
  });
</script>
```

> Note: Aurelia 本身不需要 WeakMap，但是上面的 MutationObserver ployfill 会用到。 

## Startup & Configuration

大多数的平台都为代码执行设置有一个 "main" 或者入口点，Aurelia 也不例外。如果你已经看过了 [Get Started](http://aurelia.io/get-started.html)，你会发现这个 `aurelia-app` 属性。把它简单的放到一个 HTML 元素中，Aurelia 的引导器会加载一个 `app.js` 和 `app.html`，将他们通过数据绑定在一起，然后将他们注册到 `aurelia-app` 属性放置的地方。

有时，我们想要配置框架，或者在向用户展示信息之前优先跑些代码，又或者，在项目进行中时，向需要 startup 配置的方向发展。因此，我们可以给 `aurelia-app` 一个值，这个值指向一个配置模块。该模块 export 一个单独的函数 `configure`。Aurelia 引用 `configure` 函数，向他传入 Aurelia 对象，之后我们可以用来 配置框架的这些那些东西 : ) 。

实例：
```
import {LogManager} from 'aurelia-framework';
import {ConsoleAppender} from 'aurelia-logging-console';

LogManager.addAppender(new ConsoleAppender());
LogManager.setLevel(LogManager.logLevel.debug);

export function configure(aurelia) {
  aurelia.use
    .defaultBindingLanguage()
    .defaultResources()
    .router()
    .eventAggregator()
    .plugin('./path/to/plugin');

  aurelia.start().then(a => a.setRoot('app', document.body));
}
```

除了自定义插件的代码，以上的代码都是 `aurelia-app` 默认会帮你完成的工作。如果你使用配置文件的方法，你需要自己配置这些文件，但是你也可以安装自定义的插件，用一些 services 设置依赖注入的 container，安装视图模版中用到的全局资源等。

如果你使用配置文件的方法，你可以只写一个简单的文件，他包含了所有我们上面栗子的标准选项，然后这么写：
```
export function configure(aurelia) {
  aurelia.use
    .standardConfiguration()
    .developmentLogging();

  aurelia.start().then(a => a.setRoot());
}
```

### Logging

Aurelia 框架有一个简单的 logging abstraction，默认是无操作的。上面的配置展示了如何安装一个附件( appender )来向控制台 console 输出 log 数据。我们还可以设置 log level，`logLevel` 的值可以为：`none`, `error`, `warn`, `info` and `debug`。

你可以简单的创建自己的 `appender`。配合 appender 接口来实现一个 类。最好的栗子是看源码：[console log appender's source](https://github.com/aurelia/logging-console/blob/master/src/index.js)

### Plugins

**插件** 是只有一个 exported `configure` 函数的  module 。当启动时，Aurelia 会 load 所有插件模块 ，将 Aurelia 的实例传入插件们的 `configure` 函数并执行。

插件可从 配置函数 返回一个 `Promise` 来完成异步的配置任务。在写插件时，要确保明确的提供所有的 metadata，包括给 Custom Elements 使用的 View Strategy。

为了 配置你的插件的来源，在 app 中你可以设置一个函数或者对象作为配置函数的第二个参数，在插件中可以用到，你的插件的使用者可以这么用：

```
aurelia.use.plugin('./path/to/plugin', config => { /* configuration work */ });
```

> Note:  插件不能依赖 Aurelia 的命名约定。因为 Aurelia 的命名约定是可配置的，所以第三方插件应该明确，确保在不同的上下文环境下函数的执行。

#### Promises

Aurelia 默认使用 ES6 原生的 Promises 或者 polyfill。你也可以使用这个不错的 Promise 库 [Bluebird](https://github.com/petkaantonov/bluebird) 来替换。使用它只需要将它放到页面上其他引用的脚本前面，他回使用自己标准的 Promise 实现，这个方法比原生的 Promise 要快，并且拥有更好的 debugging 支持。另外，在使用 Babel 编译器的时候，可以使用 [coroutines](http://babeljs.io/docs/usage/transformers/other/bluebird-coroutines/) 来改进异步代码的书写。

### The Aurelia Object

因为自定义配置模块和插件都需要用到 Aurelia Object，下面是一个简单的 API 的解释：

```
export class Aurelia {
  loader:Loader; //the module loader
  container:Container; //the app-level dependency injection container
  use:Plugins; //the plugins api (see above)

  withInstance(type, instance):Aurelia; //DI helper method (pass through to container)
  withSingleton(type, implementation):Aurelia; //DI helper method (pass through to container)
  globalizeResources(...resourcePaths):Aurelia; //module ids of resources relative to the configuration/plugin module
  renameGlobalResource(resourcePath, newName); //renames a globally available resource to avoid naming conflicts

  start():Promise; //starts the framework, causing plugins to be installed and resources to be loaded
  setRoot(root, applicationHost):Promise; //set your "root" or "app" view-model and display it
}
```

## Views and View Models

在 Aurelia 中，用户交互元素由 View 和 View-model 对来实现。View 由 HTML 完成，会被渲染到 DOM 中，View-model 由 JavaScript 完成， 向 View 提供数据和交互行为。模版引擎和 DI（依赖注入）负责创建配对之间的联系，并且为进程执行一个可预言的生命周期？（不理解）

在连接成功之后， Aurelia 强大的数据绑定就能将 View 和 View-model 的数据和视图联合起来，实现数据双向绑定。

- 这样的分隔方法有利于 开发和设计 的合作: ) 
- 可维护性、灵活的架构、甚至代码控制……

### Dependency Injection (DI)

View-model 和其他的交互元素：Custom Elements、Customs Attributes，都被作为 类来创建，然后框架中使用依赖注入容器来实例化。 这种代码容易模块化和测试。

你可以将需求拆分为小的对象，合作来完成一个目标，而不是写一个巨大的类。DI 可以配合完成拼接。

使用 DI时，在你的 类中的 constructor 中传入，下面是栗子：
```
import {inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@inject(HttpClient)
export class CustomerDetail{
    constructor(http){
        this.http = http;
    }
}

```

在 ES7 或者 TypeScript 中可以使用 Decrators，这时你只需要加一个 `inject` decorator，每注入一个类型传一个参数。如果你使用的语法没有支持 Decrators，或者是不想用，也可以添加一个叫 `inject` 的静态属性或者方法到你的类中。这样必须返回一个可注入的类型数组（array of injectable types）。下面是一个在 CoffeeScript 结合 CommomJS 模块的栗子：

```
HttpClient = require('aurelia-http-client').HttpClient;

class Flickr
  constructor: (@http) ->
  @inject:[HttpClient]
```

如果你正在使用　TypeScript 开发，可以用 `--emitDecoratorMetadata` 编译标识配合 Aurelia 的 `autoinject()` decorator 来让框架读取标准 TS 类型信息。这样做之后，就不需要去复制类型了。代码实例：

```
import {autoinject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@autoinject()
export class CustomerDetail{
    constructor(http:HttpClient){
        this.http = http;
    }
}
```

> Note: TypeScript 实现这个编译选项的方式中有个有趣的细节，他适用于任何decorator。所以，如果你在自己的 TS 类中拿到一些其他的 Decorator ，就不需要引入 `autoinject` decorator，那些 Type information 仍然能被 Aurelia 的依赖注入框架发现。

当明确的声明依赖时，他们不一定非得是 constructor types，还可以是 resolvers 的实例。例如：

```
import {Lazy, inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@inject(Lazy.of(HttpClient))
export class CustomerDetail{
    constructor(getHTTP){
        this.getHTTP = getHTTP;
    }
}
```

这样的 `Lazy` resolver 实际上没有提供一个 `HttpClient` 的实例，它提供一个可以随时返回 `HttpClient` 实例的函数。你可以通过写个类继承 Resolver 来创建 resolvers，或者使用我们提供的一些方便的 resolvers：

-  `lazy` -  Injects a function for lazily evaluating the dependency.
	- ex . `Lazy.of(HttpClient)`
- `All` - Injects an array of all services registered with the provided key
	- ex. `All.of(Plugin)`
- `Optional` -  Injects an instance of a class only if it already exists in the container; null otherwise.
	- ex. `Optional.of(LoggedInUser)`

另外关于 resolvers，你也可以用 `Registration` decorators 来指定一个默认的注册(registration)，或者一个实例的生命周期。默认情况下，DI 容器假设每个实例都是单例？（everything is a singleton instance）one instance for the app。但是你可以用一个registration decorator 来修改，示例：

```
import {transient, inject} from 'aurelia-framework';
import {HttpClient} from 'aurelia-http-client';

@transient()
@inject(HttpClient)
export class CustomerDetail{
    constructor(http){
        this.http = http;
    }
}
```

如上所写，每次请求 DI 容器返回 CustomerDetail 的时候会返回一个新的实例，而不是一个 singleton。`singleton` 和 `transient` registrations 都提供给外部使用，但是你也可以通过构建一个实现 `register(container, key, fn)` 函数的类，来创建 registration 。之后，就可以简单了用 `registration` decorator 来把它的实例添加到你的类中。

如果你不能或不想用 decorators。don't worry! 我们准备了后备方案。只要提供一个静态的 `decorators` 属性或方法，然后使用我们 链式的 `Decorators` helper。 这位 helper 有对应我们所有 decorators 的方法。So，无论你使用何种语言都十分简单使用。下面是 CoffeeScript 的实例：

```
HttpClient = require('aurelia-http-client').HttpClient;
Decorators = require('aurelia-framework').Decorators;

class CustomerDetail
  constructor: (@http) ->
  @decorators:Decorators.transient().inject(HttpClient);
```

### Parent View Models

By default a View-model's access is limited to injected objects as well as children of the class. Sometimes it may be desirable to refer to objects and methods on a parent View-model, which can be achieved by storing the parent during the bind method of the view lifecycle:

```
class ChildViewModel {
  bind(bindingContext) {
    this.$parent = bindingContext;
  }
}
```

## Tamplating

Aurelia 的模版引擎负责加载视图和他们所需要的资源，以及完美编译 HTML 和渲染 UI 到屏幕上。创建一个视图，你仅仅需要一个HTML文件，在里面加上`HTMLTemplate` 即可，下面是个简单的视图：

```
<template>
    <div>Hello World!</div>
</template>
```

所有在 `template` 标签中的内容都由 Aurelia 管理。但是，因为 Aurelia 使用 HTMLImport 技术来加载视图，所以你也可以写入 `links` ，他们也会被正确的加载，还可以用相对路径的写法哦，具体是指：

```
<link rel="stylesheet" type="text/css" href="./hello.css">

<template>
    <div class="hello">Hello World!</div>
</template>
```

这样你就可以为每个视图选择需要加载的样式，还可以使用 Web components on the fly。

当你需要引入一个 Aurelia 特制 (Aurelia-specific) 的资源时，比如一个 Aurelia Custom Element，Custom Attribute or Value Converter，你会在视图中用到一个 `require` 元素。栗子：

```
<template>
  <require from='./nav-bar'></require>

  <nav-bar router.bind="router"></nav-bar>

  <div class="page-host">
    <router-view></router-view>
  </div>
</template>

```

在这个例子中 `nav-bar` 是一个我们需要使用的 Aurelia Custom Element。在 我们使用 `require` element 时，框架的资源管道会处理输入的元件。这么做有以下好处：
- 去重 - 资源只会在 app 中被 download 一次，即使其他的视图需要相同的元素，也只会被下载一次。
- 一次编译 - 模版中这样引入自定义元素在整个应用周期只会被编译一次
- Local Scope( 本地作用域？ ) - 引入的资源只能在引入他的视图中可见，通过这样排除全局作用域的方法，可以有效减少命名冲突的可能性、加强可维护性和易懂性
- 重命名 - 资源可以被重命名，这样当你在同一视图，引入两个名字相同或相似的第三方资源时，就可以使用这个特性
	- ex. `<require from="./nav-bar" as="foo-bar"></require>` - 现在你可以使用 `foo-bar` 元素来替代 `nav-bar` 元素。（这是基于 ES6 的import 语法，重命名被认为是代替 Alias 的方法，因为他完全的重命名了type ）
- 包 - require 可以指向一个包括多资源的模块，包中的所有的资源会被导入同个视图
- 可扩展性 - 我们可以定义新的资源类型，You can define new types of resources which, when required in this way, can execute custom loading (async one-time) and registration (once per-view). This is a declarative, extensible resource loading pipeline.
- ES6 - 代码是由 ES6 loader 加载而不是 HTMLImport 机制， enabling all the features and extensibility of your loader。这么设计完全统一了所有app资源的加载方法，无论是 JS 还是 HTML

在你的视图中，你会用到上面提到的各种不同的资源以及数据绑定

>Note: 你可能会觉得每个视图中都要引入东西很无聊╮(╯▽╰)╭。Remember ，在引导（ bootstrapping）阶段，你可以配置 Aurelia 可以让所有视图使用的全局资源。Just use `aurelia.globalizeResources(...resourcePaths)`。

Aurelia  polyfills browsers 不包括支持 templates。但是，模版的一些特性可以被 polyfilled ，需要 workarounds。特别当 `template` 元素在 `select` 和 `table` 元素中时，下面的代码在不原生支持 templates 的浏览器中不能实现：

```
  <table>
    <template repeat.for="customer of customers">
      <tr>
        <td>${customer.fullName}</td>
      </tr>
    </template>
  </table>
```

为了重复输出 `tr` 元素，可以简单的修改为：

```
  <table>
    <tr repeat.for="customer of customers">
      <td>${customer.fullName}</td>
    </tr>
  </table>

```

### Databinding

数据绑定让你可以将一个 JS 对象中的状态和行为连接到 HTML 视图中。当连接建立之后，任何的连接的属性改动都会在一个或两个方向上同步。

JS 对象中的改变能反应在视图上，视图上的修改能映射到 JS 对象中。

要建立这样连接，可以在 HTML 中利用 `binding commands` 。通过使用一个绑定操作符 `.` 来声明绑定命令。当 HTML 的属性中包含一个 `.`，编译器会将属性的名字和值传到绑定语言（binding language）来编译，当视图被创建时，通过建立的连接，我们就能使用一个或更多的绑定表达式了。

可以自己扩展绑定命令，但是 Aurelia 提供了一个覆盖大多数常用使用案例的命令集合。

#### bind, one-way, two-way & one-time

最常用的绑定命令是 `.bind`。这样会让所有的属性使用单向绑定来绑定，而元素的值使用双向绑定。

这意味着神马呢？

**单向绑定**是说改变JS 的 view-models 会反映到 view 上，而反方向不会反应。
**双向绑定**是指每个方向改变都会影响到对方。

`.bind` 会假设你在绑定表单元素的时候会希望 view 上的 form 改变反应到 view-model 上，而其他的情况下就使用单向绑定，这是因为，在许多的时候，双向绑定到一个非表单元素上是无意义的。下面是 `.bind` 的小栗子：

```
<input type="text" value.bind="firstName">
<a href.bind="url">Aurelia</a>
```

在上面的例子中，`input` 元素会将他的值绑定到 view-model 的 `firstName` 属性上。改变 `firstName` 的属性会更新 `input.value` ，而改变 `input.value` 会更新 `firstName` 的值。另一边，`a` 标签的 `href` 属性绑在了 view-model 的 `url` 属性上。只有改变 `url` 的值会影响 `href`，反方向则不通。

我们也可以明确的使用 `.one-way` 或者 `.two-way` 来代替 `.bind`。举一个常见的栗子：当需要引入一个 Web Components 作为输入类型的控件时，你可以这样使用：

```
<markdown-editor value.two-way="markdown"></markdown-editor>
```

为了优化性能和最小化 CPU 与内存的使用，你可以选择利用 `.one-time` 绑定命令来在初始绑定阶段，将 View-model 的数据“一次性”输出到 view，这之后就不会再有任何同步行为了。

#### delegate, trigger & call

绑定命令不只是能连接 View-model 和 view 的属性，还可以触发行为操作。例如，如果你想要在点击按钮的时候执行一个方法，你可以像这么使用 `trigger` 命令：

```
<button click.trigger="sayHello()">Say Hello</button>
```

当按钮被点击之后， view-model 的 `sayhello` 方法就会被执行。即便如此，像这样往每个单独的元素中绑定事件控制器并不是很有效率，所以通常你会比较倾向于使用事件委托。我们可以用 `.delegate` 命令来使用事件委托。例子如下：

```
<button click.delegate="sayHello()">Say Hello</button>
```

当你需要访问事件对象时，`$event` 可以作为参数传到 delegate/trigger 的函数中。

```
<button click.delegate="sayHello($event)">Say Hello</button>
```

> Note: 事件委派，是一个使用 DOM 事件冒泡特性的技术。当使用 `.delegate`，一个单独的事件控制器被绑定到 document，而不是一个绑定到单独的元素。当元素的事件被触发，它沿着 DOM 冒泡到 有绑着控制器的 document。这是一个能更有效利用内存来处理事件的方式，默认机制中推荐使用这个方法。

All of this works against DOM events in some way or another. 有时，你在用 Aurelia Custom Attribute or Element 时，会想要一个函数直接的引用，然后在之后什么时候手动的执行它。用 `.call` 绑定可以传一个函数引用（since the attribute will call it later）：

```
<button touch.call="sayHello()">Say Hello</button>
```

如上，Custom Attribute `touch` 会获得一个函数，他可以执行你的 `sayhello()` 代码。依赖于 implementor 的特性，你也可以从 caller 得到传值。就像从 trigger/delegate 传 `$event` 对象。

#### string interpolation

有时你需要将属性直接绑定到 document 的内容中或插入属性值中。这时，你可以使用 string interpolation 语法 `${expression}`。 String interpolation 是单向绑定，他的输出被转化为字符串，这时例子：

```
<span>${fullName}</span>
```

`fullName` 属性会直接被插入 span 的内容。你还可以将这个用在 css class 的绑定上：

```
<div class="dot ${color} ${isHappy ? 'green' : 'red'}"></div>
```

在上面例子中，“dot” 是一个静态的类，`isHappy` 为 true 时是 “green”，false 时是 “red”。还有 `color` 的值会被作为一个类被加入。

> Note: 你可以在绑定中使用简单的表达式。但是不要试图做其他的更多的，因为在试图中最好不要涉及过多代码。你应该只想建立 view 和 view-model 的链接。

#### ref

绑定语言还识别一个特别属性的用法：`ref`。使用 `ref` 你可以为一个元素创建 local name，然后可以在其他绑定表达式中使用。他也可以设置为 view-model 的属性，之后你就能在代码中访问到。

```
<input type="text" ref="name"> ${name.value}
```

You can also use ref as a binding command to get the view-model instance that backs an Aurelia Custom Element or Custom Attribute. By using this technique, you can connect different components to each other:
通过这个技术，可以将不同的组件联系在一起：

```
<producer producer.ref="producerVM"></producer>
<consumer input.bind="producerVM.output"></consumer>
```

`producer.ref = "producerVM"` 为 view-model 的 `producer` custom element 创建个假名，然后在别的地方用。下面是一些 `ref` 的用法：

- `attribute-name.ref="someIndetifier"` - 给 custom attribute 类的实例创建引用

- `element-name.ref="someIndetifier"` - 给 custom element 类实例创建引用

- `ref="someIndetifier"` - 给 DOM 中的 HTMLElement
创建一个引用


#### select elements

HTMLSelectElement( HTML select 元素 ) 上的 `value.bind` 有一个特别的操作来支持单选或多选模式。

经典的 select 元素使用一个 `value.bind` 和 `repeat` 结合来渲染时，可以这么写：

```
<select value.bind="favoriteColor">
    <option>Select A Color</option>
    <option repeat.for="color of colors" value.bind="color">${color}</option>
</select>
```

但是，有时你想用对象实例而不是字符串。下面是用一个假设的雇员数组来构建 select 元素的栗子：

```
<select value.bind="employeeOfTheMonth">
  <option>Select An Employee</option>
  <option repeat.for="employee of employees" model.bind="employee">${employee.fullName}</option>
</select>
```

与前个栗子主要的区别在于，我们将 option 的值储存在一个特别的属性 `model` 中，而不是只能接受字符串的 `value`。

#### multi select elements

在多选情景下，你可以将 select 元素的值绑定到一个数组。下面的栗子展示了如何绑定一个字符串数组 `favoriteColors`：

```
<select value.bind="favoriteColors" multiple>
    <option repeat.for="color of colors" value.bind="color">${color}</option>
</select>
```

同样适用于对象数组：

```
<select value.bind="favoriteEmployees" multiple>
  <option repeat.for="employee of employees" model.bind="employee">${employee.fullName}</option>
</select>
```

#### radio

在 HTMLInputElement （HTML input 元素）中使用  `checked.bind` 也有特殊的用法来支持绑定字符串或是对象这样的非 boolean 值。

一个经典的 radio 按钮组渲染栗子：

```
<label repeat.for="color of colors">
  <input type="radio" name="clrs" value.bind="color" checked.bind="$parent.favoriteColor" />
  ${color}
</label>
```

但是，有时你想使用对象实例而不是字符串。下面是用一个假设的雇员数组来构建 radio 按钮组的栗子：

```
<label repeat.for="employee of employees">
  <input type="radio" name="emps" model.bind="employee" checked.bind="$parent.employeeOfTheMonth" />
  ${employee.fullName}
</label>
```

与前个栗子主要的区别在于，我们将 option 的值储存在一个特别的属性 `model` 中，而不是只能接受字符串的 `value`。

你也可以将 boolean 属性绑定到一个 radio 组。

```
<label><input type="radio" name="tacos" model.bind="null" checked.bind="likesTacos" />Unanswered</label>
<label><input type="radio" name="tacos" model.bind="true" checked.bind="likesTacos" />Yes</label>
<label><input type="radio" name="tacos" model.bind="false" checked.bind="likesTacos" />No</label>
```

#### checkboxes

为了更好支持 多选 的情景，Aurelia 可以将 input 元素的 checked 属性绑定到数组。 下面是如果绑定字符串数组 `favoriteColors`:

```
<label repeat.for="color of colors">
  <input type="checkbox" value.bind="color" checked.bind="$parent.favoriteColors" />
  ${color}
</label>
```

同样可以绑定对象数组：

```
<label repeat.for="employee of employees">
  <input type="checkbox" model.bind="employee" checked.bind="$parent.favoriteEmployees" />
  ${employee.fullName}
</label>
```

还可以绑定 boolean 类型的属性：

```
<li><label><input type="checkbox" checked.bind="wantsFudge" />Fudge</label></li>
<li><label><input type="checkbox" checked.bind="wantsSprinkles" />Sprinkles</label></li>
<li><label><input type="checkbox" checked.bind="wantsCherry" />Cherry</label></li>
```

#### innerHTML

你还可以用 `innerhtml` 属性绑定元素的 `innerHTML` 值：

```
<div innerhtml.bind="htmlProperty"></div>
<div innerhtml="${htmlProperty}"></div>
```

Aurelia 提供一个简单的 html sanitization 转化器：

```
<div innerhtml.bind="htmlProperty | sanitizeHtml"></div>
<div innerhtml="${htmlProperty | sanitizeHtml}"></div>
```

我们鼓励你使用一个更加完善的 html sanitizer 就像 [sanitize-html](https://www.npmjs.com/package/sanitize-html)。下面是使用这个包创建一个转换器的用法：

```
jspm install npm:sanitize-html
```

```
import sanitizeHtml from 'sanitize-html';

export class MySanitizeHtmlValueConverter {
  toView(untrustedHtml) {
    return sanitizeHtml(untrustedHtml);
  }
}
```

> Note: 使用 `innerhtml` 属性绑定仅仅是设置了元素的 `innerHTML` 属性。这些 HTML 标签不会传到 Aurelia 的模版系统。 Binding expressions and require elements will not be evaluated. 已经有一个问题在 track 这个问题了 [aurelia/templating#35](https://github.com/aurelia/templating/issues/35)。

#### textContent

我们可以用 `textcontent` 属性绑定 元素的 `textContent`：

```
<div textcontent.bind="stringProperty"></div>
<div textcontent="${stringProperty}"></div>
```

双向绑定可以用在 `contenteditable` 元素上：

```
<div textcontent.bind="stringProperty" contenteditable="true"></div>
```

#### style

我们可以还可以绑定 css 字符串或对象到一个元素的 `style` 属性：

```
export class Foo {
  constructor() {
    this.styleString = 'color: red; background-color: blue';

    this.styleObject = {
      color: 'red',
      'background-color': 'blue'
    };
  }
}
```

```
<div style.bind="styleString"></div>
<div style.bind="styleObject"></div>
```

当使用插值语法时，为了兼容 IE 可以使用 `style` 属性的别名`css` 来替代：

```
<!-- good: -->
<div css="width: ${width}px; height: ${height}px;"></div>

<!-- incompatible with Internet Explorer: -->
<div style="width: ${width}px; height: ${height}px;"></div>
```

#### Adaptive Binding

Aurelia 有一个可适应绑定（adaptive binding） 系统，当需要决定如何最有效的观察变化时，他会从大量的策略中选择一个。更多关于这个系统如何运作的信息请看[这篇文章](http://blog.durandal.io/2015/04/03/aurelia-adaptive-binding/)。大多时候你都不需要考虑这些繁枝细节，然而了解他将有助于你了解使用绑定系统时应该避免的一些低效的方法。

**首先需要了解：computed properties ( properties with getter functions ) 使用脏检查来观察变化。** 更有效的策略，例如：Object.observe 和 property rewriting （属性重写）并不适用于 computed property 类型。

当前的浏览器环境下，脏检查是一个必要的麻烦（necessary evil）。在写这边文章时，只有极少浏览器支持 Object.observe 。Aurelia 的脏检查机制与 [Polymer](https://www.polymer-project.org/)使用的很相似。他利用 Aurelia 的 micro-task-queue 来处理更新的DOM 是蛮有效的。

在你的应用中有一些绑定使用脏检查时并不会造成性能问题，大量使用才会有问题。幸运的是，我们有个方法可以避免脏值检查简单的 computed properties。来看看下面的 `fullName` 属性：

```
export class Person {
  firstName = 'John';
  lastName = 'Doe';

  @computedFrom('firstName', 'lastName')
  get fullName(){
    return `${this.firstName} ${this.lastName}`;
  }
}
```

我们使用 `@computedFrom` decorator 来向 Aurelia 绑定系统供一个提示，这时系统就知道只有 `firstName` 或 `lastName` 改变时，`fullName` 才会改变。

留意脏检查是怎么工作的也是很重要的。当一个属性使用了脏检查，绑定系统就定期检查现在属性值是否与原来的观察值有所不同。检测时间差默认为 120 毫秒。这就意味着你的属性的 getter 函数有可能被十分频繁的调用，也就不能尽可能的高效了。我们还需要避免不必要的返回对象或者数组的新实例，例如下面：

```
<template>
  <label for="search">Search Issues:</label>
  <input id="search" type="text" value.bind="searchText" />
  <ul>
    <li repeat.for="issue of filteredIssues">${issue.abstract}</li>
  </ul>
</template>
```

原生的视图模型实现：

```
export class IssueSearch {
  searchText = '';

  constructor(allIssues) {
    this.allIssues = allIssues;
  }

  // this returns a new array instance on every call which will in-turn result in a lot of DOM updates.
  get filteredIssues() {
    if (this.searchText === '')
      return [];
    return this.allIssues.filter(x => x.abstract.indexOf(this.searchText) !== -1);
  }
}
```

改进的视图模型实现：

```
export class IssueSearch {
  filteredIssues = [];
  _searchText = '';

  constructor(allIssues) {
    this.allIssues = allIssues;
  }

  get searchText() {
    return this._searchText;
  }
  set searchText(newValue) {
    this._searchText = newValue;
    if (newValue === '') {
      this.filteredIssues = [];
    } else {
      this.filteredIssues = this.allIssues.filter(x => x.abstract.indexOf(this.searchText) !== -1);
    }
  }
}
```















