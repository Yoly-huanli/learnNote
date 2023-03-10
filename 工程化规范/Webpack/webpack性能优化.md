[toc]

# webpack性能优化

## 分析工具

speed-measure-webpack-plugin

用于分析构建速度

https://www.cnblogs.com/zhurong/p/12603887.html

安装方法：

```
npm i speed-measure-webpack-plugin -D
```

使用方法：

```
在vue里
const SpeedMeasurePlugin = require('speed-measure-webpack-plugin')
const smp = new SpeedMeasurePlugin({
})
...
module.exports = {
...
 configureWebpack: smp.wrap(
    ...其他插件配置
 )
}
```

关注参数

首次本地构建、 开发中本地构建

## 编译方式

编译方式包括：

- 普通打包
- 后编译
- 后编译 & 按需引入
- 后编译 & 按需引入 & babel-preset-env

后编译：指的是应用依赖的 NPM 包并不需要在发布前编译，而是随着应用编译打包的时候一块编译。



## 1.加快构建速度

### happyPack多进程打包(生产环境)

JS单线程，开启多进程(多核CPU)打包, 项目很大，打包很慢，可以开启多进程，如果项目较小，不要启动多进程打包，这样反而会减慢速度，因为多进程之间需要通信销毁

```js
const HappyPack = require('happypack')
...
 module: {
        rules: [
            {
                test: /\.js$/,
                // 把对 .js 文件的处理转交给 id 为 babel 的 HappyPack 实例
                use: ['happypack/loader?id=babel'],
                exclude: /node_modules/
            },
...

 plugins: [
.......
        // happyPack 开启多进程打包
        new HappyPack({
            // 用唯一的标识符 id 来代表当前的 HappyPack 是用来处理一类特定的文件
            id: 'babel',
            // 如何处理 .js 文件，用法和 Loader 配置中一样
            loaders: ['babel-loader?cacheDirectory']
      }),
```

### paralleIUglifyPlugin压缩(生产环境)

```js
plugins: [
...
// 使用 ParallelUglifyPlugin 并行压缩输出的 JS 代码

new ParallelUglifyPlugin({
    // 传递给 UglifyJS 的参数
    // （还是使用 UglifyJS 压缩，只不过帮助开启了多进程）
    uglifyJS: {
        output: {
            beautify: false, // 最紧凑的输出
            comments: false, // 删除所有的注释
        },
        compress: {
            // 删除所有的 `console` 语句，可以兼容ie浏览器
            drop_console: true,
            // 内嵌定义了但是只用到一次的变量
            collapse_vars: true,
            // 提取出出现多次但是没有定义成变量去引用的静态值
            reduce_vars: true,
        }
    }
})
```

### IgnorePlugin(生产环境)

避免某些模块打包

### DllPlugin(动态链接库插件，开发环境)

+ 前端插件如vue,react体积大，构建慢
+ 版本稳定，不常更新

```
不需要每次都打包vue,react构建
```

webpack已经内置了

```
DllPlugin先对vue,react进行预打包出dll文件
DllReferencePlugin 使用dll文件
一般用在开发环境
```

### 热更新(一定要开发环境)

dev环境的自动刷新：整个页面全部刷新，速度可能较慢

热更新：新代码生效，页面不刷新

```js
//生产环境使用
const HotModuleReplacementPlugin = require('webpack/lib/HotModuleReplacementPlugin');
...
 entry: {
        // index: path.join(srcPath, 'index.js'),
        index: [
            'webpack-dev-server/client?http://localhost:8080/',
            'webpack/hot/dev-server',
            path.join(srcPath, 'index.js')
        ],
        other: path.join(srcPath, 'other.js')
    },
...
plugins: [
...
        new HotModuleReplacementPlugin()
    ],
devServer: {
...
        hot: true,
```

js文件里，意思是'./math'里文件改变才会启动热更新，再执行回调里的代码

```js
// 增加，开启热更新之后的代码逻辑
if (module.hot) {
    module.hot.accept(['./math'], () => {
        const sumRes = sum(10, 30)
        console.log('sumRes in hot', sumRes)
    })
}
```

hot-module-replacement-plugin 包给 webpack-dev-server 提供了热更新的能力，它们两者是结合使用的

+ webpack-dev-server(WDS)的功能可以使得生成的 bundle.js 文件通过 localhost://xxx 的方式去访问，而且实现了浏览器自动更新。
+ hot-module-replacement-plugin 的作用是提供 HMR 的 runtime，并且将 runtime 注入到 bundle.js 代码里面去。一旦磁盘里面的文件修改，那么 HMR server 会将有修改的 js module 信息发送给 HMR runtime，然后 HMR runtime 去局部更新页面的代码。因此这种方式可以不用刷新浏览器。

### uglify消除

原理：把原代码解析为AST，然后压缩为更小的AST，再还原出代码，从而优化

## 缓存

+ cache-loader
+ Threadloader

## 组件异步加载

【1】

就是组件用到的时候再加载，没用到就先不加载，表现为异步加载的组件，在用到的时候才会进行```<script src="xxx.js"></script>```

### 使用方法

+ import按需引入

  ```vue
  const App = () => import('./pages/app')
  const routes = [
      {
          path: '/',
          name: 'app',
          meta: {
              title: '下属报表'
          },
          component: App
      }
  ]
  ```

+ Vue式异步加载组件 - AMD风格

  ```JS
  const App = resolve => require(['./pages/app'], resolve)
  ```

+ webpack的require.ensuire() - CMD风格

       ```js
       const App = r => require.ensure([], () => r(require('./pages/app')), 'app')
       ```

### 实现原理

被异步加载的文件,被 ``` __webpack_require__.e```包裹

```js
 return __webpack_require__.e(/*! import() | HelloWorld */ \"HelloWorld\").then(__webpack_require__.bind(null, /*! ./components/HelloWorld.vue */ \"./src/components/HelloWorld.vue\"));
```

 ``` __webpack_require__.e```函数原理

+ 先判断这个chunkId是否被加载过
+ 没有被加载过就去创建一个Promise，在Promise里面去创建一个script标签，然后设置src的路径
+ 当代码被加载完成，通过`webpackJsonpCallback`执行。

