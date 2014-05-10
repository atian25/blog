title: angular-tips
date: 2013-12-03 09:23:39
categories:
  - angular
tags:
  - angular
  - javascript
---

介绍一些关于angular的小技巧
<!-- more -->

### 通过Chrome控制台获取service
```
//先获取element
//var element = angular.element(document.body);  
var element = angular.element(document.getElementById('view'));

//获取injector / scope / controller
var injector = element.injector();
var scope = element.scope();
var controller = element.controller();

//此时就可以拿到任意的service了
var $http = injector.get('$http');
var $rootScope = injector.get('$rootScope');
var $routeParams = injector.get('$routeParams');
console.log($routeParams);

//尽情用吧
$http.get('http://echo.jsontest.com/key1/value1/key2/value2').success(function(data){
  console.log('json:', data);
});

//外部修改值记得放在$apply里面
scope.$apply(function(){
  scope.str = Date.parse('2013-12-25 10:25:00');
});
```
也可以直接用chrome插件:[angularjs-batarang](https://chrome.google.com/webstore/detail/angularjs-batarang/ighdmehidhipcmcojjgiloacoafjmpfk)


### 通过seajs/requirejs动态加载模块
参见我的类库: https://github.com/atian25/angular-lazyload


### 装饰模式
to be continued...