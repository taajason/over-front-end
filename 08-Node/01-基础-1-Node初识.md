## 一 HelloWorld

#### 1.1 普通函数的helloworld

新建`helloworld.js`，编写代码如下：
```js
console.log("hello world");
```
对该js文件执行:`node helloworld.js`

#### 1.2 服务端helloworld

新建`helloworld.js`，编写代码如下：
```js
let http = require('http');
let server = http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain;charset=UTF8'});
    res.end('hello world');
}).listen(8000);
```
对该js文件执行:`node helloworld.js`，此刻即启动了一个基于node的web服务器，访问`localhost:8000`，即可看到网页输出`hello world`

## 二 模块机制

#### 2.1 简介模块

在上述web服务器版的helloworld中，使用`require('http')`引入了一个名称为`http`的模块，且使用了该模块提供的`createServer`方法。  

#### 2.2 变量导入导出

Node认为模块即文件，所以每个文件都可以依据模块规则进行导入，导出。  

变量导出：
```js
// 新建foo.js，导出msg这个变量
var msg1 = "你好";
var msg2 = "hello world";
exports.zhText = msg1;
exports.enText = msg2;
```

变量导入并使用：
```js
//新建一个main.js，输入以下代码
var foo = require("./foo.js");      // 核心模块（node本身的模块）在导入时无须路径，如 var http = require('http');
console.log(foo.zhText);
console.log(foo.enText);
```
一个JavaScript文件，可以向外exports无数个变量、函数。但是require的时候，仅仅需要require这个JS文件一次。使用的它的变量、函数的时候，用点语法即可。所以，无形之中，增加了一个顶层命名空间。  

变量导出的第二种方式：
```js
//修改foo.js内容：
var msg1 = "你好";
var msg2 = "hello world";
module.exports = msg1;
module.exports = msg2;
```

导入module.exports：
```js
//main.js内容：
var foo = require("./foo.js");      // 核心模块（node本身的模块）在导入时无须路径，如 var http = require('http');
console.log(foo);
```

注意：
- `module.exports`是直接导出了变量本身，`exports`是将导出的变量挂载到了`var foo`这个变量内，因为exports只是module对象下的一个属性。所有exports都是通过module.exports传递的，类似于每个模块头部都有：`var exports = module.exports;`
- 多个`module.exports`，只会导出最后一个，前面的都会被忽略
- `module.exports`和`exports`不能共用
- require并不依赖于exports，可以加载一个没有暴露任何方法的模块，这相当于执行一个模块内部的代码

#### 2.3 模块加载机制

- 首先按照加载的模块的文件名进行查找；
- 没找到，在模块文件名后加上 .js 后缀进行查找；
- 没找到，在文件名后加上 .json 后缀查找；
- 没找到，在文件名后加上 .node 后缀查找；
- 抛出错误。

## 三 Node全局对象

#### 3.1 全局对象global

Node中常见的全局对象有：global、__filename、__dirname、process等。

js中定义全局变量，浏览器中可以通过window顶层对象访问（即全局变量是顶层对象的属性）：
```js
var a = 100;
console.log(window.a);
```

Node没有window这个说法，也就是其没有这个顶层对象，Node自身的顶层对象是 global，定义全局变量方式：
```js
a = 3;            // 第一种定义全局变量方式，如果使用var a = 3; global是无法访问到的。
global.b = 3;     // 第二种定义全局变量方式
```

#### 3.2 模块属性 __filename  __dirname  

- __filename：返回当前模块的文件的解析后的绝对路径，该属性并非全局的（不能global.__filename），而每个模块都有该属性，所以直接可以使用。
- __dirname：返回当前模块文件所在目录解析后的绝对路径，返回的是文件夹。同__filename一样，其也非全局对象，而是每个模块都有的属性。  
