## 一 webpack常用配置

#### 1.1 配置书写格式

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

#### 1.2 插件 html-webpack-plugin

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

#### 1.3 css-loader

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

#### 1.4 jquery与全局变量

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