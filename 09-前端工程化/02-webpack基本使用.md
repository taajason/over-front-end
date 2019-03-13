## 一 webpack初识

#### 1.1 webpack简介

网页引入过多的静态资源会造成网页加载速度变慢（多次请求），且需要处理复杂的依赖关系。一般采用合并、压缩、精灵图等方式来解决上述问题，比如require.js、sea.js。但是ES6已经原生支持模块化了，使用原生开发，然后编译、打包更能体验ES6带来的便捷。
但是ES6、Less却不能直接被浏览器支持！WebPack可以看做是模块打包机，可以分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其转换和打包为合适的格式供浏览器使用。  

webpack的优点： 
- 1. 对 CommonJS 、 AMD 、ES6的语法规范都做了兼容
- 2. 对js、css、图片等资源文件都支持打包
- 3. 串联式模块加载器以及插件机制，使用更加灵活，扩展性更强
- 4. 有独立的配置文件webpack.config.js
- 5. 可以将代码切割成不同的chunk，实现按需加载，降低了初始化时间
- 6. 支持 SourceUrls 和 SourceMaps，易于调试
- 7.  webpack使用异步 IO 并具有多级缓存。这使得 webpack 很快且在增量编译上更快。

#### 1.2 webpack与gulp区别  

Gulp 的定位是 Task Runner, 用来跑一个一个任务，但是没有解决 js module 的问题。其工作方式是：指明对某些文件进行类似编译、组合、压缩等任务的具体步骤，之后gulp工具可以自动替你完成这些任务。
![](/images/JavaScript/webpack-01.png)
Webpack工作方式：把项目当做一个整体，通过一个给定的主文件（如index.js），Webpack将从这个文件开始找到项目的所有依赖文件，使用loaders处理它们，最后打包为一个（或多个）浏览器可识别的JavaScript文件。
![](/images/JavaScript/webpack-02.png)  

#### 1.3 webpack安装
```
在项目根目录下 npm init 后：
npm i -D webpack webpack-cli 			# 注意：只有webpack4版本才需要安装webpack-cli

# 查看安装的webpack版本，默认会安装最新版，可以使用 npm i -D webpack@3 确立版本
npx webpack -v							  
```

贴士：很多人使用全局安装webpack的方式，这样在命令行直接就可以启用webpack命令了，（无需像1.5章节中使用.\node_modules\.bin\webpack test.js --mode production）。笔者不推荐，因为不同的项目可能使用的webpack版本不同，全局安装后会影响对不同版本项目的支持。

#### 1.4 工程目录
![](/images/JavaScript/webpack-03.png)

#### 1.5 使用webpack打包
在工程根目录下输入打包命令：  
```
# 方式一
.\node_modules\.bin\webpack test.js 	# webpack4 需要带上参数：--mode production

# 方式二
npx webpack test.js
```

打包完毕后会在根目录下生成`dist`文件夹，内部包含一个`test.js`文件被打包后生成的`main.js`文件

webpack常用命令参数：
```
--open				打包后自动打开浏览器
--port 				设置端口  
--contentBase 		打开目的文件目录
--hot				浏览器异步更新  主要针对样式的更改
--config a.js		指定配置文件，默认指定根目录下的 webpack.config.js
```

#### 1.6 npm脚本运行
反复输入上述命令很麻烦，可以配置一个npm脚本来替代：
```
//注意 webpack3及其以下版本不需要 --mode 参数
"dev": "webpack --mode development"

//配置完成后启动webpack打包命令
npm run dev
```

#### 1.7 webpack.config.js

在实际开发中，webpack的打包命令需要配置大量的命令参数，这些参数如果都写在npm脚本中也会引起npm脚本的臃肿，我们可以指定一个webpack的配置文件，让npm脚本运行的webpack命令去该配置文件查找webapck的命令行参数：

```
"dev": "webpack --config webpack.config.js --mode development"
```

在项目根目录下创建webpack.config.js配置文件：
```js
const path = require('path');

module.exports = {
	entry:path.resolve(__dirname,'src/index.js'), 	 //入口
	output: {
		path: path.resolve(__dirname, 'dist'),      //输出文件夹
		filename: 'bundle.js'          				 //输出文件名
	}
};
```

贴士：在webpack4.0时，打包需要设置mode，默认值为production，也可以设置为development，二者分别用于生产环境（会压缩）和开发环境

## 二 webpack常用配置

#### 2.0 前言

我们需要注意的是：webpack本身只是一个打包工具，不具备任何功能。各类打包、编译工具都是由加载器或者插件完成的，比如编译ES6,react都是由babel这个第三方工具完成的，webpack可以使用babel-loader加载这个第三方工具。webpack具备很好的可扩展性，在编译方面，可配置大量的加载器（loader），在打包方面，可以配置大量的插件（plugins）。  

加载器的配置书写案例：
```js
module:{
	rules: [
		{
            test: /(\.jsx|\.js)$/,    //正则匹配 哪些文件 需要该加载器
	        use: {                    //匹配到的文件使用哪个加载器
		        loader: "babel-loader",
		        options: {                      //加载器的参数
			        presets: ["env"] 
		        }
	        },
	        exclude: /node_modules/  //忽略掉哪些文件不走该加载器 
        },
        {
            //后续的加载器
        }
	]
}
```

插件的配置书写案例：
```js
plugins: [
	new htmlWebpackPlugin({
		template: path.resolve(__dirname,'src/index.html'),
		filename: 'index.html'
	}),
	//热更新插件
	new webpack.HotModuleReplacementPlugin()
]
```

注意：webpack配置支持书写格式五花八门（字符串，数组，json），给入门者造成了相当的困扰，笔者推荐按照上面最复杂的格式书写。

#### 2.1 插件 html-webpack-plugin

安装插件：
```
npm i -D html-webpack-plugin
```
配置插件：
```js
const path = require('path');
const htmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
	entry:path.resolve(__dirname,'src/index.js'), 
	output: {
		path: path.resolve(__dirname, 'dist'),     
		filename: 'bundle.js'                      
    },
    plugins: [
        new htmlWebpackPlugin({
            template: path.resolve(__dirname,'src/index.html'), 
            filename: 'index.html'
        })
    ]    
};
```
此时我们在项目根目录创建index.html,无需引入index.js，打包后index.js自动被引入了index.html中。

#### 2.2 css-loader

解决了js、html的打包问题，现在还有css需要编译打包，css可以像模块一样被引入：
```js
import './index.css';
```
但是webpack只能处理JS文件，处理CSS文件需要第三方加载器css-loader
安装：
```
npm i -D style-loader css-loader 
```
配置：
```js
const path = require('path');
const htmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
	entry:path.resolve(__dirname,'src/index.js'), 
	output: {
		path: path.resolve(__dirname, 'dist'),     
		filename: 'bundle.js'                      
    },
    module:{
        rules: [
            {
                test:  /\.css$/,  
                use:['style-loader', 'css-loader'] 
            }
        ]
    },
    plugins: [
        new htmlWebpackPlugin({
            template: path.resolve(__dirname,'src/index.html'), 
            filename: 'index.html'
        })
    ]    
};
```
在上述配置中，css-loader会遍历 CSS 文件，然后找到 url() 表达式然后处理他们，style-loader 会把原来的 CSS 代码插入页面中的一个 style 标签中。
案例：

```js
// index.js 代码：
import './index.css';
const a = 3;
alert(a);

// index.html是一个空的h5页面

// index.css内设置了一些样式。
```
打包后，生成了 index.html和bundle.js两个文件。
打开页面，alert后样式被修改为index.css设置的样式。
查看index.html的源码，CSS生成方式：直接在html的 head 标签添加了index.css的样式。

#### 2.3 jquery与全局变量

jquery可以使用传统的script标签形式引入，但有了webpack，可以使用更加模块化的方式：
```js
//先安装jquery： npm i -S jquery
import 'jquery';
$("#div").click(()=>{
    alert("jquery");
});
```
此时打包后$不能被浏览器识别，需要webpack配置如下：
```js
const webpack = require('webpack');
//插件数组添加如下元素
new webpack.ProvidePlugin({
	$: 'jquery',
	jQuery: 'jquery'
})

```





