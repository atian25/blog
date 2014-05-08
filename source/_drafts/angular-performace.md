title: angular性能优化心得
categories:
  - angular
tags:
  - angular
  - javascript
---

> 不知不觉，在项目中用angular已经半年多了，踩了很多坑。
> 趁着放假，把angular的3本书都看了遍，结合这半年的经验，是该做个总结了。
> 希望可以给大家带来启示，少踩点坑。
<!-- more -->
> 本文针对的读者：
> - 具备JavaScript性能优化的相关知识（`雅虎14条性能优化原则`、`《高性能网站建设指南》`等）
> - 拥有angular实战经验。

## 脏数据检查 != 轮询检查更新
谈起angular的`脏检查机制(dirty-checking)`, 常见的误解就是认为： ng是定时轮询去检查model是否变更。
其实，ng只有在指定事件触发后，才进入`$digest cycle`： 
- DOM事件，譬如用户输入文本，点击按钮等。
- XHR响应事件
- 浏览器Location变更事件
- Timer事件(setTimeout, setInterval)

> 参考《mastering web application development with angularjs》 P294

## $digest后批量更新UI
传统的JS MVC框架, 数据变更是通过setter去触发事件，然后立即更新UI。
而angular则是进入`$digest cycle`，等待所有model都稳定后，才批量一次性更新UI。
这种机制能减少浏览器repaint次数，从而提高性能。
> 参考《mastering web application development with angularjs》 P296

## $apply vs $digest
- $apply会使ng进入`$digest cycle`, 并从$rootScope开始遍历(深度优先)检查数据变更。
- $digest仅会检查该scope和它的子scope，当你确定当前操作仅影响它们时，用$digest可以稍微提升性能。
> 参考《mastering web application development with angularjs》 P308

## ng-repeat

track by
限制个数
2000节点
无限滚动

## 慎用filter

## bindonce

## 事件
scope广播事件
事件限速, 避免触发太频繁


## directive
跟数据无关的操作可以放在compile阶段，它只执行一次
controller里面不要操作dom， 尤其是绑定到scope后，便是灾难
ng-if vs ng-show

## scope
unwatch
深度watch即浪费内存，也影响速度。
在放入scope之前处理数据

要尽少触发$digest, 且尽快执行。
减少进入$digest的次数。该章讲解了触发的时机。

双括号应该被span包裹，因为watch的是外部element

$evalAsync()的执行时机， $timeout

相关文章

http://angularjs.cn/A0lr
http://www.codelord.net/2014/04/15/improving-ng-repeat-performance-with-track-by/?utm_source=ng-newsletter&utm_campaign=a9233dab29-AngularJS_Newsletter_4_22_144_22_2014&utm_medium=email&utm_term=0_fa61364f13-a9233dab29-96532701/