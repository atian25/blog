title: angular性能优化心得
categories:
  - angular
tags:
  - angular
  - javascript
---

> 最近读了几本angular相关的书籍和文章，结合过去半年的项目经验，总结为学习心得，分享给后来者, 希望可以给大家带来启示，少踩点坑。
<!-- more -->
> 阅读本文前，建议：
> - 具备JavaScript性能优化的相关知识（`雅虎14条性能优化原则`、`《高性能网站建设指南》`、`《高性能网站建设进阶指南》`）
> - 拥有angular实战经验。

## 脏数据检查 != 轮询检查更新
谈起angular的`脏检查机制(dirty-checking)`, 常见的误解就是认为： ng是定时轮询去检查model是否变更。
其实，ng只有在指定事件触发后，才进入`$digest cycle`： 
- DOM事件，譬如用户输入文本，点击按钮等。
- XHR响应事件
- 浏览器location变更事件
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