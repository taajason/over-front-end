## 一 常用加载器
#### 1.1 常见 module rules
注意：loader是从后往前处理
```js
// 处理 CSS 文件的 loader
{ test: /\.css$/, use: ['style-loader', 'css-loader'] }, 
// 处理 less 文件的 loader
{ test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] },
// 处理 scss 文件的 loader
{ test: /\.scss$/, use: ['style-loader', 'css-loader', 'sass-loader'] },
// 处理 图片路径的 loader
{ test: /\.(jpg|png|gif|bmp|jpeg)$/, use: 'url-loader?limit=7631&name=[hash:8]-[name].[ext]' }, 
// 处理 字体文件的 loader 
{ test: /\.(ttf|eot|svg|woff|woff2)$/, use: 'url-loader' }, 
// 配置 Babel 来转换高级的ES语法
{ test: /\.js$/, use: 'babel-loader', exclude: /node_modules/ }
```
#### 1.2 处理CSS style-loader css-loader
CSS的编译与打包有两种处理方式，常用的处理方式是使用：style-loader与css-loader。
- style-loader：负责在加载的页面创建样式标签，引入css
- css-loader：让JS支持import css模块
配置：
```
rules: [
	{ 
		test: /\.css$/, 
		use: [
			{loader: 'style-loader'}, 
			{loader: 'css-loader'}
		]
	}
]
```
打包结果：
我们在app.js中可以直接使用import引入css，css样式被以 style 标签的形式直接写入了html中，这样的好处是CSS 完全包含在合并的应用中，再也不需要重新下载。

还有一种方式是使用file-loader，配置如下：
```
{ 
	test: /\.css$/, 
	use: [
		{loader: 'style-loader/url'}, 
		{loader: 'file-loader'}
	]
}
```
此时import的css不会被以标签形式直接加入html，而是额外打包成了css文件，通过link标签引入该css,如果import了多个css，那么会生成多个link，这样会造成多次请求，所以很少使用。
style-loader也可以控制样式的显示与否，配置如下：
```
use:[
    {loader: 'style-loader/useable'}, 
    {loader: 'css-loader'}
]
```
这样配置后，我们可以在js中控制CSS的显示与否，比如在app.js中加入如下代码：
```js
import baseCSS from './css/index.css';
baseCSS.use();
setInterval(function(){
	baseCSS.unuse();
},300);
```

style-loader也有大量的可配置选项：
```
	insertAt：		插入位置
	insertInto：		插入到某个dom节点
	singleton：		是否只使用一个style标签
	transform：		浏览器中转化，插入也面前执行一些列操作
```
案例：
```
{
	loader: 'style-loader',
	options: {
		insertInfo: '#box',
		singleton: true
	}
}, 
```
上述配置案例中style会直接插入到 #box这个标签中，singleton为true代表多个不同的style将会被合并到一个style中。

css-loader选项：
```
	alias：			解析的别名
	importLoader：	也作 @import 
	minimize：		是否压缩，true为要压缩
	modules：		是否启用css-modules，true为启用css模块化
	localIdentName:	CSS的class名重新生成，常见设定为：
                    [path][name]_[local]_[hash:base64:5]
```
#### 1.3 加载资源文件 file-loader url-loader
webpack无法处理background中的url地址等等类似的资源文件都需要加载器支持。
安装：
```
npm i -D file-loader
```
配置：
```
{
	test: /\.(png|jpg|jpeg|gif)$/,
	use: [
		{ 
			loader: 'file-loader',
			options: {
				publicPath: '',
				outputPath: 'dist/',
				useRelativePath: true
			}
		}
	]
}
```
url-loader具备和file-loader一样的图片打包功能，但是额外多了base64压缩功能。
```
{
	test: /\.(png|jpg|jpeg|gif)$/,
	use: [
		{ 
			loader: 'url-loader',
			options: {
				limit: 10000 //小于该大小的图片转变为base64
			}
		}
	]
}
```
#### 1.4 压缩图片 img-loader
```
rules: [
    {
      test: /\.(jpe?g|png|gif|svg)$/i,
      use: [
        'url-loader?limit=10000',
        'img-loader'
      ]
    }
  ]
```
如果需要配置压缩率，可以添加可选项：
```
pngquant:{
    quality: 80
}
```
#### 1.5 图片合并 postcss-loader
```
    rules: [
      {
        test: /\.css$/,
        use: [ 'style-loader', 'postcss-loader' ]
      }
    ]
```
retina:true时候可以支持视网膜屏，比如有些图是**@2x.png
#### 1.6 处理img标签 html-loader
```
{
    test: /\.html$/,
    use: [
        {
            loader: 'html-loader',
            options: {
                attrs: ['img:src', 'img:data-src']
            }
        }
    ]
}
```
#### 1.7 处理typescript 
推荐使用官方的loader：
```
npm i -D ts-loader 
```
tsconfig.json配置：
```js
{
    "compilerOptions": {
        "module": "commonjs",
        "target": "es5",
        "allowJs": true
    },
    "include": [
        "./src/*"
    ],
    "exclude": [
        "./node_module"
    ]
}

```
webpack配置：
```
{
    test: /\.tsx?$/,
    use: {
        loader: 'ts-loader'
    }
}
```
注意：
ts虽然对类型进行了限制，但是不会制动提示哪个地方出现了类型问题。
```
npm i @types/lodash
//如果使用了vue还需要安装：
npm i @types/vue

//使用
import * as _ from 'lodash';
```
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



