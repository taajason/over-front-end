## 一 常用插件

插件可以使webpack的打包更加便捷，对打包提供了一些额外支持。

#### 1.1 移动html插件 html-webpack-plugin

在之前的loader章节，html文件并未被移动，对开发造成了很大困扰，使用插件`html-webpack-plugin`会将源码中的html页面从src拷贝到dist下，且会自动将入口文件打包的js文件插入html页面的script标签中。

安装插件：
```
cnpm i -D html-webpack-plugin
```
配置插件：
```js
const path = require('path');
const htmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
	entry: {                        //entry是可以有多个的
        main: path.resolve(__dirname, './src/index.js')
    },
	output: {
		path: path.resolve(__dirname, 'dist'),     
		filename: 'index.js'                      
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
此时我们在项目根目录创建index.html,无需引入index.js，打包后index.js自动被引入了index.html中。

#### 1.2 去除注释插件

去除注释插件为：`uglifyjs-webpack-plugin`,但是webpack4 不再需要该插件，因为在打包时候如果使用了`mode`为`production`，则自动去除注释。

#### 1.3 其他常用插件

```
clean-webpack-plugin    删除目录插件 			
CommonsChunkPlugin      提取公共JS插件		
copy-webpack-plugin     拷贝文件插件			

```

## 二 提取公共代码

#### 2.1 提取公共代码
webpack内置了提取公共代码的插件，主要针对多页面配置：
```
entry: {
    'pageA': './src/pageA',
    'pageB': './src/pageB',
    'vendor': ['lodash']
},
output: {
    path: path.resolve(__dirname, './dist'),
    filename: '[name].bundle.js',
    chunkFilename: '[name].chunk.js'
},
plugins: [
    new webpack.optimize.CommonsChunkPlugin({
        name: 'vendor',
        minChunks: Infinity
    })
]
```
此时 入口pageA和入口pageB引入的共同文件将会被打包到一个公共js中，名为插件配置中的name，如果name的值和入口中某个文件相同，那么会被打包到该入口文件中，如上所示：
vendor是默认该项目引入的一系列第三方包，其他入口的功用代码也会被打包到该文件中。
如果不想被打包如某个入口文件，可以在plugins中修改name，或者再配置一个optimize。
也可以这样配置：
```
    new webpack.optimize.CommonsChunkPlugin({
        name: ['vendor','manifest'],
        minChunks: Infinity
    })

    new webpack.optimize.CommonsChunkPlugin({
        name: 'common',
        minChunks: 2,
        chunks: ['pageA', 'pageB']
    })
```
#### 2.2 提取公共CSS插件
旧版webpack使用插件：extract-text-webpack-plugin 。
新版推荐使用插件：mini-css-extract-plugin




