[toc]



# webpack概念

## webpack的作用

静态资源模块打包

+ 静态资源：本地资源，而通过链接获取的是动态资源，只可以打包静态资源
+ 模块：既包括js模块，也包括css模块，图片模块等

### 前端代码为什么要打包

```js
优化压缩代码，编译转换语法,减少io请求，前端流程化
```
+ 文件的压缩，合并，tree Shaking,会使得打包出来的文件体积更小，加载速度更快
+ 可以编译高级的语言与语法，如ES6
+ 兼容性，语法检查
+ 前端的流程化，团队合作，标准化，在统一的生产环境

## module chunk bundle的区别

- module各个源码文件，webpack里一切皆模块，src里除了html为输出模板，其它都是模块
- chunk是code splitting 后的产物，也就是按需加载的分块，装载了不同的 module，相当于是在内存中的处理

- bundle最终的输出文件

# webpack的使用

## 1.基本使用

+ 新建文件夹，初始化项目

  ```js
  npm init
  ```

+ 安装webpack（开发需要，上线不需要）

  ```js
  npm install --save-dev webpack
  npm install --save-dev webpack-cli
  可以看到package.json里已经显示安装包和版本
   "devDependencies": {
      "webpack": "^5.68.0",
      "webpack-cli": "^4.9.2"
    }
  ```

+ 配置文件（默认为webpack.config.js），制定入口出口文件

  ```js
  const path = require('path');
  
  module.exports = {
    mode: 'development', // 开发模式
    entry: './index.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'my-first-webpack.bundle.js'
    }
  };
  ```

+ 配置启动命令

  ```js
  package.json
  "scripts": {
      "dev": "webpack --config webpack.config.js"
    },
  ```

+ 运行npm run dev 生成打包后的文件，新建index.html引入打包后的文件进行验证

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
  </head>
  <body>
    <script src="./dist/my-first-webpack.bundle.js"></script>
  </body>
  </html>
  ```

##  2.配置入口与出口

### 单入口出口

```js
entry: './src/index.js',
module.exports = {
  mode: 'development',
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'main.js'
  }
};
```

path.resolve:拼接路径，__dirname，当前文件的绝对路径（不包含当前文件）

### 多入口多出口

```js
module.exports = {
  mode: 'development',
  entry: {
    main: './src/index.js',
    test: './src/test.js'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contentHash:8].js'
  }
};
```

这里的配置：```filename: "[name][hash:8][ext]```

[name].[contentHash:8]是webpack提供的方法，这样打包后dist下生成的是main.15df1f46.js文件,这样做的好处是每次打包生成新的文件，不会受到缓存的影响

[hash]有三种

- **hash** ：任何一个文件改动，整个项目的构建 hash 值都会改变；
- **chunkhash**：文件的改动只会影响其所在 chunk 的 hash 值；
- **contenthash**：每个文件都有单独的 hash 值，文件的改动只会影响自身的 hash 值；

配置多入口的好处：

多页面应用程序，分别打包，一些公共的部分可以共用

## 3.loader

将文件从一种形式转换为另一种形式，本来webpack本身是只能处理js的，loader可以让webpack去处理其他各种各样的其他模块，比如css，图片等，使用方法：

+ 安装对应loader
+ webpack.config.js

```js
module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader'
      }
    ]
  }
```

## 4.plugin

扩展webpack的功能，执行更多的功能，使用方法：

+ 安装对应plugin
+ webpack.config.js

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');
...
plugins: [
        new HtmlWebpackPlugin({
            template: 'src/index.html',
            filename: 'index.html'
        })
    ]
```

## 5.resolve

resolve里可以进行别名的配置

```
resolve: {
    alias: {
        componets: './src/components/'
    }
}
```

mainFields:有一些第三方模块会针对不同环境提供几份代码。例如分别提供采用ES5 和 ES6的2份代码

```
mainFields: ['browser', 'main']
```

webpack会按照数组里的顺序去package.json文件里面找，只会使用找到的第一个

extensions:在导入语句没带文件后缀时，webpack会自动带上后缀去尝试访问文件是否存在。

```
extensions: ['.js', '.vue', '.json'],
```



# webpack应用

## 1.处理入口文件

### html-webpack-plugin

因为需要查看打包完成后的最终文件，也就是需要一个打包过后生成好的html文件，且把生成的js文件以script的方式引入，每次手动引入非常耗时

安装

```
npm install --save-dev html-webpack-plugin
```

配置，需要在src下新建index.html作为模板

```js
plugins: [
        new HtmlWebpackPlugin({
            template: 'src/index.html',
            filename: 'index.html'
        })
    ]
```

dist下生成一个新的index.html，<script src="main.js">引入了打包完成后的js文件

+ 多入口:需要设置chunks，否则生成的index.html会把两个js文件都引入进去，chunks可以配置引入哪个

```js
entry: {
    index: './src/index.js',
    search: './src/search.js'
  },
 ...
  plugins: [
    // 多入口
    new HtmlWebpackPlugin({
      template: './index.html',
      filename: 'index.html',
      chunks: ['index'],
      minify: {
        // 删除 index.html 中的注释
        removeComments: true,
        // 删除 index.html 中的空格
        collapseWhitespace: true,
        // 删除各种 html 标签属性值的双引号
        removeAttributeQuotes: true
      }
    }),
    new HtmlWebpackPlugin({
      template: './search.html',
      filename: 'search.html',
      chunks: ['search']
    })
  ]
```

## 2.处理js

### babel-loader

babel本身可以编译各种es6语法，比如箭头函数转换，但是对于es6新引入的API，比如promise，Set, Object.assign等不能直接去编译，需要借助其他模块

```js
// 安装 babel-loader
npm install --save-dev babel-loader@8.1.0
// 安装 Babel
npm install --save-dev @babel/core@7.11.0 @babel/preset-env@7.11.0
//编译es6新增的API
npm install --save-dev core-js@3.6.5
代码中import "core-js/stable";
```

配置

```js
module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel-loader'
      }
    ]
  }
```

### 格式校验eslint-loader

配置eslint-loader

## 3.处理css

比如在index.js里引入了css文件, webpack无法识别css模块

```js
import './index.css';
```

### css-loader

让webpack可以识别css模块

```js
module: {
    rules: [
      {
        test: /\.css$/,
        loader: 'css-loader',
      }
    ]
  },
```

### style-loader

webpack识别css模快后，如何去处理，一种方法是识别的css模块以```<style>...</style>```的方式被引入到html的title里去

```js
module: {
    rules: [
      {
        test: /\.css$/,
        //多个loader，从右到左执行，先识别css模块，再引入
        use: ['style-loader', 'css-loader']
      }
    ]
  },
```

### postcss-loader

用来给CSS文件加一些前缀

###  MiniCssExtractPlugin

识别的css文件以link的形式引入，并且css模块单独打包生成，防止把CSS文件打包到js文件中，导致打包文件过大

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
...
module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader']
      }
    ]
  },
...
plugins: [
    new HtmlWebpackPlugin({
      template: './index.html',
      filename: 'index.html'
    }),
    new MiniCssExtractPlugin({
      filename: 'css/[name].css'
    })
  ]
```

###  terser-webpack-plugin

css文件压缩

```js
npm install terser-webpack-plugin --save-dev
// css文件压缩，自动去除console.log
npm install optimize-css-assets-webpack-plugin --save-dev
```

```js
const TerserJSPlugin = require('terser-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')
...
plugins: [
...
optimization: {
        // 压缩 css
     minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
 }
```

## 4.处理图片

外部资源，以链接形式引入的，那么可以正常显示，只有本地图片，才需要webpack进行处理

### file-loader

可以处理CSS里的图片,首先把背景图片复制一份到打包后的dist目录，然后将源码中的图片路径转换为打包后的路径,默认是认为图片在生成的CSS相同目录下，但是这里CSS打包完成后在css目录下，img打包后在img目录下，所以需要为CSS配置一个路径，上移一层，便于找到图片文件

```js
 {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: '../'
            }
          },
          'css-loader'
        ]
},

{
        test: /\.(jpg|png|gif)$/,
        use: {
          loader: 'file-loader',
          options: {
            name: 'img/[name].[ext]'
            esModule: false  //处理js里的图片
          }
        }
  }
```

### html-withimg-loader

处理html里的图片,主要用于解析与发现html里的图片，但是发现后的处理还是需要依靠file-loader，而且处理后设置不导出为模块

```js
{
        test: /\.(jpg|png|gif)$/,
        use: {
          loader: 'file-loader',
          options: {
            name: 'img/[name].[ext]',
            esModule: false
          }
        }
 },

{
   test: /\.(htm|html)$/,
   loader: 'html-withimg-loader'
}
```



###  url-loader

file-loader功能单一，所以需要url-loader,url-loader底层还是可能会使用file-loader,url-loader可以生成base64的图片并且直接嵌入html或者js文件中

```js
{
        test: /\.(jpg|png|gif)$/,
        use: {
          loader: 'url-loader',
          options: {
            name: 'img/[name].[ext]',
            esModule: false,
            // 小于 50kb 的图片用 base64 格式产出
            // 否则，依然延用 file-loader
            limit:  50 * 1024
          }
        }
      }
```

## 5.webpack-dev-server搭建开发环境

webpack-dev-server主要是启动了一个使用express的Http服务器。它的作用主要是,原始文件作出改动后，webpack-dev-server会实时的编译，但是最后的编译的文件并没有输出到目标文件夹，而是保存在了内存当中

+ 安装

```
npm  install webpack-dev-server
npm install cross-env 
```
+ 配置修改package.json

```
"dev": "cross-env NODE_ENV=development webpack-dev-server --config webpack.dev.js"
```
+ npm run dev,成功，默认端口8080，可以在浏览器打开localhost:8080查看编译之后的页面

+ devserver的其它配置

```js
devServer: {
        port: 8080,
        progress: true,  // 显示打包的进度条
        contentBase: 'owndist',  // 根目录
        open: true,  // 自动打开浏览器
        compress: true,  // 启动 gzip 压缩

        // 设置代理
        proxy: {
            // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
            '/api': 'http://localhost:3000',

            // 将本地 /api2/xxx 代理到 localhost:3000/xxx
            '/api2': {
                target: 'http://localhost:3000',
                pathRewrite: {
                    '/api2': ''
                }
            }
        }
    }
```

## 6.代码拆分

定义3个文件，一个用于公共配置，一个用于开发环境配置，一个用于生产环境配置，通过smart来将公共配置引入开发和生产环境

公共配置webpack.common.js

本地环境的配置webpack.dev.js

```js
const webpack = require('webpack')
const webpackCommonConf = require('./webpack.common.js')
const WebpackMerge = require('webpack-merge')

module.exports = WebpackMerge.merge(webpackCommonConf, {
    mode: 'development',
    plugins: [
        new webpack.DefinePlugin({
            // window.ENV = 'development'
            ENV: JSON.stringify('development')
        })
    ],
})
```

```js
"dev": "cross-env NODE_ENV=development webpack-dev-server --config webpack.dev.js"
```

生产环境的配置webpack.pro.js

+ webpack.pro.js配置

```js
const webpack = require('webpack')
const webpackCommonConf = require('./webpack.common.js')
const WebpackMerge = require('webpack-merge')

module.exports = WebpackMerge.merge(webpackCommonConf, {
    mode: 'production',
    plugins: [
        new webpack.DefinePlugin({
            ENV: JSON.stringify('production')
        })
    ]

})
```

## 7.清空之前的输出信息

+ 安装
```
npm i clean-webpack-plugin --save-dev
```
+ webpack.pro.js

```
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
..
plugins: [
        new CleanWebpackPlugin(), // 会默认清空输出文件夹
        new webpack.DefinePlugin({
            ENV: JSON.stringify('production')
        })
    ]
```

## 8.抽离公共的部分与第三方代码

之前把所有js打包到了一个js文件里，现在把第三方代码，公共代码，其它部分代码分别打包到三个js文件里

```js
optimization: {
    // 分割代码块
    splitChunks: {
        chunks: 'all',
        /**
         * initial 入口 chunk，对于异步导入的文件不处理
           async 异步 chunk，只对异步导入的文件处理
           all 全部 chunk
         */

        // 缓存分组
        cacheGroups: {
            // 第三方模块
            vendor: {
                name: 'vendor', // chunk 名称
                priority: 1, // 权限更高，优先抽离，重要，先抽离第三方代码！！！
                test: /node_modules/,
                minSize: 0,  // 大小限制，较小的js公共或者第三方代码可以不抽离
                minChunks: 1  // 最少复用过几次
            },

            // 公共的模块
            common: {
                name: 'common', // chunk 名称
                priority: 0, // 优先级
                minSize: 0,  // 公共模块的大小限制
                minChunks: 2  // 公共模块最少复用过几次
            }
        }
    }
}
```







# webpack面试题目

## 1.常用的loader和plugin

loader

```
处理css： css-loader,style-loader,stylus-loader，MiniCssExtractPlugin
处理图片：file-loader，html-withimg-loader，url-loader
处理js: babel-loader
优化性能：
```

plugin

```
html文件：html-webpack-plugin
优化性能：
terser-webpack-plugin
paralleIUglifyPlugin
IgnorePlugin
```

## 2.loader和plugin的区别

loader: 只能接收源文件之后进行一些处理和转化

plugin：扩展功能, 能够使用到webpack提供的一些钩子进行处理

  loader运行在打包文件之前（loader为在模块加载时的预处理文件）  plugins在整个编译周期都起作用。

参考：https://juejin.cn/post/6844903544756109319