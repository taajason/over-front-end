## 一 常见webpack本身配置

#### 1.1 入口与出口

入口与出口可以分别配置：
```js
const path = require('path');
const htmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	entry: {                //多个entry              
        main1: path.resolve(__dirname, './src/index.js'),
        main2: path.resolve(__dirname, './src/index.js')
    },
	output: {                //name变成了上述的入口名   
		path: path.resolve(__dirname, 'dist'),     
		filename: '[name].js'                        
    },
    module:{
        rules: [
            {
                test:  /\.(css|scss)$/,  
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
注意：此时html-webpack-plugin会将多个入口都注入到html中。

output的其他配置：
- publicPath:   会给注入到html中的JS的src添加该前缀

#### 1.2 模式
mode:

### 1.3 sourceMap

sourceMap可以映射打包后的代码错误点与源码错误点位置，方便调试。  
配置方式：
```
# 开发环境推荐配置： eval意思是eval方式打包，打包速度快，提示也全
devtool: 'cheap-module-eval-source-map'     

# 生产环境推荐配置：因为依赖多，eval容易出问题
devtool: ''cheap-module-source-map'
```

## 二 webpack-dev-server

#### 2.1 -watch参数

webpack的打包命令如果添加了`watch`参数，则可以监听源码文件，当源码文件有改动时，则自动重新打包。

#### 2.2 webpack-dev-server

如果我们要实现watch的效果，且能自动打开浏览器，刷新浏览器，那么需要使用webpack-dev-server,该工具会使contentBase配置中的目录成为服务器静态文件目录。  

第一步：安装
```
cnpm i webpack-dev-server -D
```

第二步：脚本修改
```js
"dev": "webpack-dev-server --config webpack.config.js"
```

第三步：修改webpack配置
```js
    devServer: {
        contentBase:  path.join(__dirname, "dist"),
        open: true,                 //启动时，会打开浏览器并渲染页面
        port: 3000,                 //默认是3000
        hot: true,                   //开启hotModule功能
        hotOnly: true               //html生效，则浏览器不刷新
    },
```

注意：
- webpack-dev-server打包的dist目录内是没有文件的，文件位于内存中。
- 当开启hot与hotOnly时，需要引入webpack自带的插件，如下所示:

```js
 new webpack.HotModuleReplacementPlugin()
```
这样配置：可以更方便的调试css，js，因为某块地方的更改不会改变其他模块已经产生的数据结果（即不会因为改变文件而全局重新生成）。

比如有模块a与模块b，模块c引用了模块a，现在模块a改变了，只会改变模块a内部的数据，模块c不会变化，如果想要模块c也跟着改变，可以在模块c中的源码这样书写：
```js
if(module.hot) {
    moduel.hot.accept('a', ()=>{
        //业务代码
    })
}
```