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