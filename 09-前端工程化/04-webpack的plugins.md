## 二 插件 html-webpack-plugin

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
## 二 常用插件
#### 2.1 去除注释
uglifyjs-webpack-plugin
注意：新版webpack4 在打包时候如果使用了 mode为 production，则无需该插件自动去除注释。
#### 2.2 打包html插件
```
npm i html-webpack-plugin -D
```
如果有多个html，每个html入口不一致，需要配置多个new htmlWebpackPlugin。
#### 2.3 压缩插件
webpack自带，配置即可：
```
new webpack.optimize.UglifyJsPlugin({
    compress: {
        		warnings: false
   	}
})
```
webpack4 配置了 生产mode后无需配置。
#### 2.4 提取公共代码
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
#### 2.5 提取公共CSS插件
旧版webpack使用插件：extract-text-webpack-plugin 。
新版推荐使用插件：mini-css-extract-plugin

## 三 其他
```
postcss-loader          自动加入浏览器前缀加载器
clean-webpack-plugin    删除目录插件 			
CommonsChunkPlugin      提取公共JS插件		
copy-webpack-plugin     拷贝文件插件			

```



