## 一 Node.js介绍
#### 1.1 简介
Ryan Dahl将Chrome浏览器的JS解释引擎V8抽离了出来，补充了一些API，实现了可以在服务端运行的JS。
他的主要特点是：
- 单线程：
在Java、PHP或者.net等服务器端语言中，会为每一个客户端连接创建一个新的线程。而每个线程需要耗费大约2MB内存。也就是说，理论上，一个8GB内存的服务器可以同时连接的最大用户数为4000个左右。要让Web应用程序支持更多的用户，就需要增加服务器的数量，而Web应用程序的硬件成本当然就上升了。
- 非阻塞I/O：
当在访问数据库取得数据的时候，需要一段时间。在传统的单线程处理机制中，在执行了访问数据库代码之后，整个线程都将暂停下来，等待数据库返回结果，才能执行后面的代码。也就是说，I/O阻塞了代码的执行，极大地降低了程序的执行效率。
由于Node.js中采用了非阻塞型I/O机制，因此在执行了访问数据库的代码之后，将立即转而执行其后面的代码，把数据库返回结果的处理代码放在回调函数中，从而提高了程序的执行效率。
- 事件驱动：
在Node中，客户端请求建立连接，提交数据等行为，会触发相应的事件。在Node中，在一个时刻，只能执行一个事件回调函数，但是在执行一个事件回调函数的中途，可以转而处理其他事件（比如，又有新用户连接了），然后返回继续执行原事件的回调函数，这种处理机制，称为“事件环”机制。
Node.js底层是C++（V8也是C++写的）。底层代码中，近半数都用于事件队列、回调函数队列的构建。
#### 1.2 适合开发什么？
Node善于I/O，不善于计算。即CPU密集型Node不擅长，Node擅长的是IO密集型。
因为Node.js最擅长的就是任务调度，如果你的业务有很多的CPU计算，实际上也相当于这个计算阻塞了这个单线程，就不适合Node开发。
当应用程序需要处理大量并发的I/O，而在向客户端发出响应之前，应用程序内部并不需要进行非常复杂的处理的时候，Node.js非常适合。Node.js也非常适合与web socket配合，开发长连接的实时交互应用程序。
比如：用户表单收集、聊天室、考试系统、图文直播、提供RestfulAPII。
当然Node从10版本开始支持worker_threads,也能强有力的支持CPU密集型运算。
#### 1.3 Node结构
![](/images/JavaScript/node-01.png)
如图所示，binging一层是JS与底层C++沟通的关键，前者通过bindings调用后者，相互交换数据。
libuv为Node提供了跨平台、线程池、事件池、异步IO能力，是Node核心。
## 二 Node安装
#### 2.0 安装说明
偶数位版本为稳定版，奇数位版本为非稳定版。
初学者可以在Node官网下载安装包，下一步下一步安装即可，并（可能）需要配置环境变量。
nvm是一款可以管理node版本的工具，所以在企业级开发中，我们可以使用nvm来安装node，这样可以方便我们控制node的版本。
#### 2.1 安装nvm
```
win安装地址：
https://github.com/coreybutler/nvm-windows/releases

Linux安装地址：
https://github.com/creationix/nvm

安装后查看：
nvm version
```
Linux中命令会自动向~/.bashrc添加环境命令，此时如果输入nvm 无法出现nvm命令，打开 .bash_profile 后面添加 source ~/.bashrc  即可


#### 2.2 安装node
```
设置下载镜像：
nvm node_mirror:https://npm.taobao.org/mirrors/node/
nvm npm_mirror:https://npm.taobao.org/mirrors/npm/
```

安装Node：
```
# 安装最新版
nvm install latest
# 安装指定版本
nvm install 8.11.3
# 切换版本
nvm use 8.11.3
# 查看当前使用版本
nvm use ls
# 指定默认版本
nvm alias default 8.5.0
```

安装完node后，可以使用node -v 查看node版本；

安装完node后，打开webstorme，会自动识别node路径，如果没有识别：
可以打开Settings-搜索node-配置；
但是此时webstorme 仍然没Node的代码提示功能，解决步骤：
File-setting-Languages&Frameworks-Node-

#### 2.3 npm的使用
npm是node的包管理工具，用来下载各种node模块、第三方文件
```
npm install jQuery
```
在空文件夹中使用 npm init 可以初始化一个项目。
支持的命令行参数有：-g 全局安装， -S 以生产依赖形式本地安装，-D 以开发依赖形式本地安装

设置npm镜像：
```
npm config rm proxy
npm config rm https-proxy
npm config set registry https://registry.npm.taobao.org
```
也可以直接使用淘宝开发的cnpm：
```
npm install cnpm -g
cnpm install jQuery
```
恢复npm镜像办法：
```
npm config set proxy=http://127.0.0.1:1080
npm config set registry=http://registry.npmjs.org
```
## 三 PM2管理工具
由于Node是单进程，很容易服务器死去，无法重启，pm2可以有效的监控服务器状况，并让node服务器自动重启。
npm install pm2 -g

开启：pm2 start ***.js		（node关闭窗口仍然运行，且会自动重启）
关闭：pm2 stop **.js
重启：pm2 restart **.js

参数启动：pm2 start **.js --name=”test” 		//启动应用程序并命名为 "api"
日志开启：进入项目文件目录，输入命令 pm2 logs 项目名
## 四 Node全局对象
#### 4.1 全局对象global
js中定义全局变量，可以通过window顶层对象访问（即全局变量是顶层对象的属性）：
```JavaScript
var a = 100;
console.log(window.a);
```
Node没有window这个说法，也就是其没有这个顶层对象，Node自身的顶层对象是 global。
定义全局变量方式：
方式一： a = 3;
方式二： global.a = 3;
如果使用var a = 3; global是无法访问到的。
#### 4.2 模块属性 __filename  __dirname
__filename：
返回当前模块的文件的解析后的绝对路径，该属性并非全局的（不能global.__filename），而每个模块都有该属性，所以直接可以使用。
__dirname：
返回当前模块文件所在目录解析后的绝对路径，返回的是文件夹。同__filename一样，其也非全局对象，而是每个模块都有的属性。
#### 4.3 process对象
全局对象process在任何地方都能被访问到，提供对当前运行的程序的进程进行访问和控制。

访问：process   或者  global.process

常用参数：
argv：		一组包含命令行参数的数组，可以让进程中带入一些参数
execPath：	开启当前进程的绝对路径
env：		返回用户的环境信息
pid：		返回当前进程的pid
title：		当前进程的显示名称（Getter/Setter）
exit():		退出进程

stdin标准输入流：
```JavaScript
//默认情况下，输入流失关闭的，要监听处理的话需要先开启输入流
process.stdin.resume();
process.stdin.on('data',function (chunk) {   //on是监听函数   console.log('用户输入了：' + chunk); });
```

stdout标准输出流：
```
function log(data) {
   process.stdout.write(data);
}
```
## 五 Node模块
#### 5.1 初步使用模块
```JavaScript
let http = require('http');
let server = http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/plain;charset=UTF8'});
    res.end('hello world');
}).listen(8000);
```
#### 5.2 变量到导入导出

```JavaScript
// export 导出变量，在foo.js中有如下代码：
var msg = "你好";
exports.text= msg;

//导出一个类
module.exports = Person;

//require 导入变量
var foo = require("./foo.js"); 
console.log(foo.text);

//require 导入核心模块不需要路径
var http = require('http');

```
一个JavaScript文件，可以向外exports无数个变量、函数。但是require的时候，仅仅需要require这个JS文件一次。使用的它的变量、函数的时候，用点语法即可。所以，无形之中，增加了一个顶层命名空间。
exports不能代替module.exports，因为exports只是module对象的下的一个属性而已。其实所有的exports都是通过module.exports传递的。
module.exports 可以单独返回一个数据类型如：数组、字符串、数字，而exports不行。
如果创建了一个既有exports又有module.exports的模块，那么返回module.exports，expot会被忽略。因为exports只是对module.exports的一个全局引用，最初被定义为一个可以添加属性的孔对象。

#### 5.2 模块加载机制
- 首先按照加载的模块的文件名进行查找；
- 没找到，在模块文件名后加上 .js 后缀进行查找；
- 没找到，在文件名后加上 .json 后缀查找；
- 没找到，在文件名后加上 .node 后缀查找；
- 抛出错误。


