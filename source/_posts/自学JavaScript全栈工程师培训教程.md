---
title: 自学JavaScript全栈工程师培训教程
date: 2016-12-06 12:23:07
tags:
  - JavaScript
  - React
  - Node
  - 自学
---
自学内容来自[阮一峰的网络日志](http://www.ruanyifeng.com/blog/2016/11/javascript.html)

[Github地址](https://github.com/ruanyf/jstraining)

偶然在订阅的RSS上看到阮一峰老师的一个博文介绍这个Javascript全栈工程师培训的教程。正好想接触一下JavaScript前后端的东西，正好拿来一试。阮老师亲自讲的课自然是上不起的，只能苦哈哈地自学一下了。

要是抱着学完就成为全栈工程师的目的，同学还是算了。毕竟几个小时就可以把教程过一遍了。不过强力推荐作为JavaScript的前后端开发的Overview来看。起码本人来说是获益匪浅的。
<!--more-->

---
## 目录
以下是整个教程的目录。在开始教程之前要[准备好实验环境](https://github.com/ruanyf/jstraining/blob/master/docs/preparation.md)，毕竟实践出真知，动手动脑才能记得牢。
> ### 第一讲：[前端开发的历史和趋势](https://github.com/ruanyf/jstraining/blob/master/docs/history.md)

>1. 前端开发的历史演变
>2. 前端 MVC 框架的兴起
>3. 前后端分离
>4. 全栈工程师
>5. 前端开发的未来

>### 第二讲：[React 技术栈](https://github.com/ruanyf/jstraining/blob/master/docs/react.md)

>1. React 的基本用法
>2. React 应用的架构

>### 第三讲：[Node 应用开发](https://github.com/ruanyf/jstraining/blob/master/docs/node.md)

>1. Node 的基本用法
>2. Restful API
>3. Express 框架搭建 Web 应用

>### 第四讲：[前端工程简介](https://github.com/ruanyf/jstraining/blob/master/docs/engineering.md)

>1. 持续集成
>2. 静态代码检查
>3. 单元测试
>4. 功能测试
>5. 持续集成服务 Travis CI

如目录所示，整个教程就是关于JavaScript全栈开发的简介。先是讲前端开发 _是什么_，_从哪来_，_到哪去_ 等终极问题，并介绍了几个知名的前端框架(`Backbone.js`，`Angular.js`，`Vue.js`)；然后着重地简单介绍了前端`React.js`的使用，应用架构的解决方案；接着就是介绍后端的`Node.js`搭建Web应用；最后就是工程相关的代码测试，质量检测，持续集成等概念以及相关工具。需要自己动手的小时贯穿始终，保证能亲自感受一下使用体验。

## 体会
这个教程虽然不能让人马上成为全栈工程师，但是能让人体会一下the meaning of _FULL STACK_,堪称短小精悍的教程。精炼的语言概括了各种框架，各种工具，各种服务的用途。简单来说就是，从前
> - 这些名字我都听过，就是不知道它们都是干嘛的。
> - 上网查一查吧。怎么介绍里又出现了新的不知道干嘛的东西。
> - 这个介绍太长了，看不下去了……

现在
> - 啊～原来是是这么回事啊！早这么说不就完了么！

举个例子, 关于React的核心思想

{% cq %}
View 是 State 的输出，`view = f(state)`
只要 State 发生变化，View 也要随之变化
React 的本质是将图形界面（GUI）函数化
{% endcq %}
