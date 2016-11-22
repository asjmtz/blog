title: "Aurelia-文档小翻译（二）"
date: 2015-06-13 14:07:27
categories: Aurelia
description: Aurelia 文档翻译下部分，from Routing part
---


## Routing

你可能会需要创建许多不同的应用类型。从 navigation apps、dashboards，到 MDI interfaces，Aurelia 都能完全掌控。这些架构中大多数的关键组件都是一个客户端路由，用来将 url 解析到应用状态中。

如果你读过了开始向导，你会知道完成路由需要两步。首先，在你的 view-model 中有一个 `Router`，我们通过添加一些路由信息和控制向导来配置他。之后，view 中 的一个 `router-view` 负责展示路由中定义的当前状态指向的模块。

例子：

```
export class App {
  configureRouter(config, router){
    this.router = router;

    config.title = 'Aurelia';
    config.map([
      { route: ['', 'home'],       name: 'home',       moduleId: './home/index' },
      { route: 'users',            name: 'users',      moduleId: './users/index',                      nav: true },
      { route: 'users/:id/detail', name: 'userDetail', moduleId: './users/detail' },
      { route: 'files*path',       name: 'files',      moduleId: './files/index',     href:'#files',   nav: true }
    ]);
  }
}
```

我们开始先实现 `configureRouter` 方法。我们可以有选择的设置下 `title`，这个属性用来构建文档的标题，但是更重要的部分是设置路由。路由的 `map` 方法用一个简单的 JSON 数据来展示路由表。

两个最重要的属性是 `route` （一个数组或字符串），定义 route pattern；`moduleId`，指向你的 view-model 的相对 module id 路径。你也可以设置 `name`属性，用来生成一个路由的链接，`title`属性用来生成文档的 title，`nav`属性表明了向导模型（navigation model）是否包含该路由，在向导模型中 `href`用来绑定。

> Note: Any properties that you leave off will be conventionally determined by the framework based on what you have provided.

So，对于 route pattern 你有那些选择哪：

- 静态路由
	- 例如：'home' - 直接匹配字符串
- 有参数的路由
	- 例如：'users/:id/detail' - 匹配字符串并且解析一个 `id` 参数。你的 view-model 的 `activate` 回调会被调用，并将会传入一个带着 `id` 的对象，`id` 的值是从 url 中提取的
- 通配符路由（wildcard routes）
	- 例如：'files*path' - 匹配字符串和其他符合他的。你的 view-model 的 `activate` 回调会被调用，且传入一个带着 `path` 属性的对象，`path` 的值是给通配符的值。

所有的路由实际都有一个 `nav` 属性被组合进 `navigation` 数组。这使得用数据绑定来生成菜单结构变得很简单。另一个对绑定很重要的属性是 `isNavigating`。下面是一个简单的例子，向你展示如何使用上面的路由构建一个 view：

```
<template>
  <ul>
    <li class="loader" if.bind="router.isNavigating">
      <i class="fa fa-spinner fa-spin fa-2x"></i>
    </li>
    <li repeat.for="item of router.navigation">
      <a href.bind="item.href">${item.title}</a>
    </li>
  </ul>

  <router-view></router-view>
</template>

```

### The Screen Activation Lifecycle

无论路由何时处理一个向导，在路由来往的这两个 view-model，都会创建一个准确的生命周期（Whenever the router processes a navigation, it enforces a strict lifecycle on the view-models that it is navigating to and from）。这个生命周期有四个阶段。通过的在 view-model 的类中实现对应的方法，你可以有选择使用这些阶段， Here's a list of the lifecycle callbacks:

- `canActivate(params, routeConfig, navigationInstruction)` - 控制是否能路由进入（ navigate to）到你的 view-model。返回一个 boolean 值、一个返回boolean 值的 promise，或者一个向导命令（navigation command）。
- `activate(params, routeConfig, navigationInstruction)` - 在 view-model 展示之前执行你的自定义逻辑。可以有选择的返回一个 promise 来告诉路由等待直到你完成你的逻辑，才进行数据和视图的绑定。
- `canDeactivate()` -  控制是否能路由离开（ navigate away）你的 view-model。返回一个 boolean 值、一个返回boolean 值的 promise，或者一个向导命令（navigation command）。
- `deactivate()` - 在离开 view-model 之后执行你的自定义逻辑。可以有选择的返回一个 promise 来告诉路由等待直到你完成你的逻辑。

`param` 是由路由解析的每个参数组成的对象。`routeConfig` 是你设置的路由配置参数。`routeConfig`  也会有一个新的 `navModel` 属性，他可以用你的 view-model 中加载的数据来改变 document 标题。例如：

```
activate(params, routeConfig) {
  this.userService.getUser(params.id)
    .then(user => {
      routeConfig.navModel.setTitle(user.name);
    });
}
```

> Note: 一个向导的命令是一个带有 `navigate(router)` 方法的对象。 When one is encountered, the navigation will be cancelled and control will be passed to the navigation command. One navigation command is provided out of the box: `Redirect`.

### Child Routers

如果你已经阅读了 “Get Started” 向导，我们建议你现在特别注意这个章节  "Bonus: Leveraging Child Routers"。

即使你已经设置一个路由映射到view-model，这个 view-model 也还可以包含他自己的路由，然后子路由的 view-model 也可以包含自己的子路由，如此循环……路由模式（route patterns）相对于父级路由，而模块和视图 id 是相对于view-model。他让你可以简单的封装特性或者子应用，以及控制复杂的分层状态（hierarchical state）。

子路由与其他路由并无差异。So，我们上面所讨论的路由相关都适用于他。添加子路由只需要再次实现 `configureRouter` 方法。上面讨论的  screen activation lifecycle 也适用于他。Each phase of the lifecycle is run against the entire router hierarchy before moving on to the next phase. The activate hooks run from top to bottom and the deactivate hooks run from bottom to top.

### Conventional Routing

As with everything in Aurelia, we have strong support for conventions. 你可以选择配置动态路由而不是一个预先配置的路由。动态路由的栗子：

```
export class App {
  configureRouter(config){
    config.mapUnknownRoutes(instruction => {
      //check instruction.fragment
      //set instruction.config.moduleId
    });
  }
}
```

你只需要设置 `config.moduleId` 属性，也可以从 `mapUnknownRoutes` 返回一个 promise 来异步设置路由目标。

> Note: Though not necessarily related to conventional routing, you may sometimes have a need to asynchronously configure your router. For example, you may need to call a web service to get user permissions before setting up routes. To do this, return a promise from `configureRouter`.


### Customizing the Navigation Pipeline

The router pipeline is composed out of separate steps that run in succession. Each of these steps has the ability to modify what happens during routing, or stop the routing altogether. The pipeline also contains a few extensibility points where you can add your own steps. These are authorize and modelbind. authorize happens before modelbind. These extensions are called route filters.

下面是一个添加授权（authorization）到应用中的例子：

```
import {Redirect} from 'aurelia-router';

export class App {
	configureRouter(config){
		config.title = 'Aurelia';

		//Add a route filter to the authorize extensibility point
		config.addPipelineStep('authorize', AuthorizeStep); 

		config.map([
			{ 
				route: ['welcome'],
				name: 'welcome',
				moduleId: 'welcome',
				nav: true,
				title: 'Welcome'
			},
			{
				route: 'flickr',
				name: 'flickr',
				module: 'flickr',
				nav: true,
				auth: true
			},
			{
				route: '',
				redirect: 'welcome'
			}
		]);
	}
}

class AuthorizeStep{
	run(routingContext, next){
		// Check if the route has an 'auth' key
		// The reason for using 'nextInstructions' is because
		// this includes child routes

		if( routingContext.nextInstructions.some(i => i.config.auth) ) {
			var isLoggedIn = /* insert magic here */false;
			if ( !isLoggedIn ) {
				return next.cancel(new Redirect('login'));
			}
		}

		return next();
	}
}

```
这些扩展点也有自己的小管道线，每个小管道都能添加多步骤。例如，上面栗子中的 `AuthorizeStep` 会检查用户是否登录，你可以在 `authorize` 的扩展点加入 `IsAdminStep` ——是否 Admin 的步骤，他们将会按顺序进行。

你也可以创建自己的 filters，只要在 `addPipelineStep` 中传入一个不同的名字，具体是这样的：

```
config.addPipelineStep('myname', MyFirstStep); // Transparently creates the pipeline "myname" if it doesn't already exist.
config.addPipelineStep('myname', MySecondStep); // Adds another step to it.
config.addPipelineStep('modelbind', 'myname'); // Makes the entire `myname` pipeline run as part of the `modelbind` pipeline.
```

### Configuring PushState

如果你不喜欢 `#` 的哈希标识出现在你的 URLs，你就需要在你的应用中使用 `pushState`。为了让他正常跑起来，你还需要在 server 端做点工作。让我们先从 Aurelia 这边开始码。

首先你需要在 `router` 的 `config` 中告诉 Aurelia ，你想使用 `pushState` ：

```
export class App {
	configureRouter(config){
		config.title = 'Aurelia';
		config.options.pushState = true; // <-- this is all you need here
		config.map([
	      { route: ['welcome'],    name: 'welcome',     moduleId: 'welcome',      nav: true, title:'Welcome' },
	      { route: 'flickr',       name: 'flickr',      moduleId: 'flickr',       nav: true, auth: true },
	      { route: 'child-router', name: 'childRouter', moduleId: 'child-router', nav: true, title:'Child Router' },
	      { route: '',             redirect: 'welcome' }
	    ]);
	}
}

```

你还需要添加一个 [base 标签](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base) 在html 文档的 head。这个很重要，请别忘记他。

接下来， server 端需要配置：不管发送什么请求都需要返回同个 `index.html`，因为所有的路由操作都在客户端。所以，如果你在依照栗子用 `gulp watch` 和 `browsersync`，你可以将设置改为：

npm 安装 依赖包：

```
npm install --save connect-history-api-fallback
```

这是在下载安装你需要的中间件。之后打开 _build/tasks_ 文件夹的 _Server_ 任务，将下面这句放在顶部与其他引用的语句一起：

```
var historyApiFallback = require('connect-history-api-fallback');
```

下面就可以用新的 `middleware` 修改 `serve` task“

```
gulp.task('serve', ['build'], function(){
	browserSync({
		open: false,
		port: 9000,
		serve: {
			baseDir: ['.'],
			middleware: [historyApiFallback, function(req, res, next){
				res.setHeader('Access-Control-Allow-Origin','*');
				next();
			}]
		}
	}, done);
});

```

现在，你的 node 服务器就会将路由控制权交给 Aurelia 了。

如果你在使用 ASP.NET 或 MVC 等 .NET 服务端框架，可以像这样配置：

- 创建一个控制器叫他 `ApplicationController` 或者其他你想叫的:

```
	
public class ApplicationController : Controller {
  public ActionResult Index() {
    return View();
  }
}

```

- 创建一个 "index.cshtml" 视图在视图文件夹
- 设置路由配置为：

```
context.MapRoute(
  name: "AureliaRouting",
  url: "{*.}",
  defaults: new { controller = "Application", action = "Index" }
);
```

注意上面的配置将强制使用一个 Razor 视图文件。如果你想使用一个普通 HTML 文件，将使用不同的方法：[ This SO article might help you.](http://stackoverflow.com/questions/20871938/render-html-file-in-asp-net-mvc-view)

If you are using [Nancy FX](http://nancyfx.org/), then the config is just as simple. Locate your `IndexModule.cs` or whatever you called it and make sure it looks something like this and all will be well:

```
public class IndexModule : NancyModule {
  public IndexModule()     {
    this.Get["/robots.txt"] = p => this.Response.AsFile("robots.txt");
    this.Get["/sitemap.xml"] = p => this.Response.AsFile("sitemap.xml");
    this.Get["/"] = x => this.View["index"];
    this.Get["/{path*}"] = x => this.View["index"];
  }
}
```

Similar techniques can be used in other server environments - you just need to make sure that whatever server you're using, it needs to send back the same `index.html` regardless of the request being made. All server side frameworks should be able to achieve this. Aurelia will figure out which page to load based on its own route data.

### Reusing an existing VM

有时你会想要在不同的路由中重用已存在的 View-Model。Aurelia 默认会将那 些路由作为同个 View-Model 的别名，然后只会 build 生命周期、绑定一次。可能这并不是你想要的。看下面的栗子：

```
export class App {
  configureRouter(config) {
    config.title = 'Aurelia';
    config.map([
      { route: 'product/a',    moduleId: './product',     nav: true },
      { route: 'product/b',    moduleId: './product',     nav: true },
    ]);
  }
}
```
