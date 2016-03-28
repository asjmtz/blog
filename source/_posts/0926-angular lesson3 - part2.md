title: Angular 模版学习2
date: 2014-09-26 11:06:59
categories: Angular
description: Angular phonecat 教程 part 3 —— 模版（2）
---

# Filtering Repeaters

## step 3

```
git checkout -f step-3
...
npm start
```

### controller
没啥改变

### template
`app/index.html` 

```
<div class="container-fluid">
  <div class="row">
    <div class="col-md-2">
      <!--Sidebar content-->

      Search: <input ng-model="query">

    </div>
    <div class="col-md-10">
      <!--Body content-->

      <ul class="phones">
        <li ng-repeat="phone in phones | filter:query">
          {{phone.name}}
          <p>{{phone.snippet}}</p>
        </li>
      </ul>

    </div>
  </div>
</div>

```

使用`<input>` 标签和Angular 的filter函数来处理ngRepeat指令的输入。用户可以实时看到搜索的结果：

- 数据绑定：当页面load时，Angular绑定input的名字到一个在数据模型里的同名变量，然后保持两个同步。
- 使用filter：filter函数使用`query` 的值来创建一个新的数组，数组为匹配query的结果。即输入input的是一个过滤用的关键词，什么我输入了一个正则？？

ngRepeater 自动更新视图以回应由 filter 返回的phones数组的变化

## 测试

单元测试完美适合于控制器和其他组件的测试，但是没法测试DOM 操作或其他app操作。为此，使用一个end-to-end端对端的测试是更好的choice。

下面是个end-to-end test来验证我们的搜索功能，`test/e2e/scenarios.js:`

```
describe('PhoneCat App', function() {

describe('Phone list view', function() {

  beforeEach(function() {
    browser.get('app/index.html');
  });


  it('should filter the phone list as a user types into the search box', function() {

    var phoneList = element.all(by.repeater('phone in phones'));
    var query = element(by.model('query'));

    expect(phoneList.count()).toBe(3);

    query.sendKeys('nexus');
    expect(phoneList.count()).toBe(1);

    query.clear();
    query.sendKeys('motorola');
    expect(phoneList.count()).toBe(2);
  });
});
});
```

这个end-to-end使用的是 Protractor 的 API。更多语法相关[点这里 ](https://github.com/angular/protractor/blob/master/docs/api.md).

使用 `npm run protractor` 命令开始测试。需要能翻墙到google，ou no !!! T^T !!!
 
 
> Written with [StackEdit](https://stackedit.io/).