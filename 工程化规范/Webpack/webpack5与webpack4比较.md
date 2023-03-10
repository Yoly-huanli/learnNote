[toc]

# webpack5相比webpack4内置的

##  tree-shaking

如果我们的项目中引入了 lodash 包，但是我只有了其中的一个方法。此时 tree-shaking 就可以把没有用的那些东西剔除掉，来减少最终的bundle体积。

tree shaking 只支持 esmodule , 所以babel-loader 编译要进行配置调整，保证打包出来的是es，而不是commonjs

```JS
module.exports = {
     optimization: {
       usedExports: true, //只导出被使用的模块
       minimize : true // 启动压缩
     }
  }
```

webpack5的 mode=“production” 自动开启 tree-shaking。

## 压缩代码

+ webpack4需要安装terser-webpack-plugin 插件并进行配置
+ Webpack5开发环境内置了 terser-webpack-plugin

## 缓存

+ webpack需要进行缓存配置 hard-source-webpack-plugin
+ webpack5内置了缓存



# 优化的

## loader的优化

+ webpack4处理文件需要不同的loader
+ Webpack5 改为了type

# 不同点

## 冷启动

+ webpack4使用webpack-dev-server 启动服务
+ webpack4使用webpack server 启动服务



# 参考

+ webpack5 和 webpack4 的区别有哪些 ？https://juejin.cn/post/6990869970385109005





