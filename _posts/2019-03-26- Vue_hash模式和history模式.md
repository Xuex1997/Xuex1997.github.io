---
layout: post
title: Vue-router中hash模式和history模式的区别
date: 2019-03-26
categories: 前端
tags: Vue router
---

# Vue-router中hash模式和history模式的区别
> hash模式url里面永远带着#号，Vue开发当中默认使用hash模式。但是当用户考虑URL的规范时就需要使用history模式，在history模式下没有#号，正常的url适合推广宣传。使用history模式访问二级页面的时候，做刷新操作，会出现404错误，那么就需要和后端人配合让他配置一下apache或是nginx的url重定向，重定向到你的首页路由上。


做到多路由跳转并按需加载页面代码，以往的做法都是通过锚点来定位对应的页面代码，而这种古老的操作方式最大的问题就是首屏加载缓慢，一次性加载了所有页面代码。而Vue一个单页面应用可以通过`Vue-router`实现

* hash与history的区别

|  | hash|	history|
|---|-----|-----|
|url显示|	有#	|无#|
|回车刷新|	可以加载到hash值对应页面|	一般就是404掉了|
|支持版本|	支持低版本浏览器和IE浏览器|	HTML5新推出的API|
--------------------- 

## hash模式


* hash模式: (前端路由，单页应用的标配)
	* `hash` —— 即地址栏 URL 中的 `#` 符号。比如这个 URL：`http://www.abc.com/#/hello`，`hash`的值为 `/hello`。
	* 特点：hash 虽然出现在 URL 中，但不会被包括在 HTTP 请求中，对后端完全没有影响，因此改变 hash 不会**重新加载页面**。hash 模式下，仅 hash 符号**之前**的内容会被包含在请求中，因此对于后端来说，即使没有做到对路由的全覆盖，也不会返回 404 错误。
	* hash模式的工作原理: `window`可以监听`onhashchange`事件，也就是URL中的哈希值（#后面的值）如果有变化，前端是可以做到监听并做一些响应，这么一来，即使前端并没有发起http请求他也能够找到对应页面的代码块进行按需加载。

## history模式

> 利用了 HTML5 History Interface 中新增的 `pushState()` 和 `replaceState()` 方法。这两个方法应用于浏览器的历史记录栈，在当前已有的 `back`、`forward`、`go` 的基础之上，它们提供了对历史记录进行**修改**的功能。只是当它们执行修改时，虽然改变了当前的 URL，但浏览器**不会立即向后端发送请求**。

简而言之，这两个神器的作用就是可以将url替换并且不刷新页面，好比挂羊头卖狗肉，http并没有去请求服务器该路径下的资源，一旦刷新就会暴露这个实际不存在的“羊头”，显示404。那么如何去解决history模式下刷新报404的弊端呢，这就需要**服务器端**做点手脚，将不存在的路径请求**重定向**到入口文件（index.html），前后端联手，齐心协力做好“挂羊头卖狗肉”的完美特效。

* HTML5新增的API
	* `history.pushState(data, title [, url])`：往历史记录堆栈顶部添加一条记录； data会在`onpopstate`事件触发时作为参数传递过去；`title`为页面标题，当前所有浏览器都会忽略此参数；`url`为页面地址，可选，缺省为当前页地址；
	* `history.replaceState(data, title [, url])` ：更改当前的历史记录，参数同上； 
	* `history.state`：用于存储以上方法的data数据，不同浏览器的读写权限不一样；
	* `window.onpopstate`：响应pushState或replaceState的调用；有了这几个新的API，针对支持的浏览器，

##  history模式应用

* 1 在`router/index.js`文件中，添加`history`模式

	```
	export default new Router({
		mode: 'history',
		base: '/project-name/', //如果项目根目录不为域名，则添加该行
		routes: [{}]
	})
	```
* 2 修改`config/index.js`文件

	```
	build: {
		// Paths
		assetsRoot: path.resolve(__dirname, '../dist'),
		assetsSubDirectory: 'static',
		assetsPublicPath: '/project-name/', //添加根目录，如果域名为根目录，则为 '/'
	}
	```

3、后端配置

## 后端配置
### Apache

```
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /
  RewriteRule ^index\.html$ - [L]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule . /index.html [L]
</IfModule>
```
除了 `mod_rewrite`，你也可以使用 `FallbackResource`。

### nginx

```
location / {
  try_files $uri $uri/ /index.html;
}
```
### 原生 Node.js

```
const http = require('http')
const fs = require('fs')
const httpPort = 80

http.createServer((req, res) => {
  fs.readFile('index.htm', 'utf-8', (err, content) => {
    if (err) {
      console.log('We cannot open "index.htm" file.')
    }

    res.writeHead(200, {
      'Content-Type': 'text/html; charset=utf-8'
    })

    res.end(content)
  })
}).listen(httpPort, () => {
  console.log('Server listening on: http://localhost:%s', httpPort)
})

```
### 基于 Node.js 的 Express
对于 `Node.js/Express`，请考虑使用 `connect-history-api-fallback` 中间件。

