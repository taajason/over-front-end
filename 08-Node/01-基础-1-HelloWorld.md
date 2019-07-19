## 一 开发工具

#### 1.1 VSCode

笔者推荐的NodeJS开发工具是VScode/WebStorm。  

如果是使用VSCode开发，推荐安装插件：
- Code Runner：用于运行node代码
- Search node_modules：用于node包快速提示

#### 1.2 WebStorme

打开WebStorme，在设置中界面中选择：`Languages&Frameworks`，对比下图进行勾选：  

![](/../images/node/idea.jpg)


## 二 HelloWorld

#### 1.1 普通函数的helloworld

新建`helloworld.js`，编写代码如下：
```js
console.log("hello world");
```

如果是VSCode或者Webstorme此时就可以直接右键运行，但是笔者仍然推荐命令行方式，在当前目录打开命令，输入以下命令：
```
node helloworld.js      # 查看输出结果
```

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
