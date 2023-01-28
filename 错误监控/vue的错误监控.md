[toc]

搬运https://www.jb51.net/article/245438.htm

# window.onerror

能监听到全局的错误，一般放在APP.vue里

- message： 错误信息
- source：发生错误的资源
- line：发生错误的行号
- column：发生错误的列数
- error：Error错误对象

```js
window.onerror = function(message,source,line,column,error) {
    // do something
};
```

# element.onerror

针对一些资源加载失败的情况，例如img、script，将会触发该元素的onerror()处理函数，并且error不会冒泡到window。遇到这种情况，可以手动抛出异常，就可以被全局异常监听到是资源加载失败了。

```js
// child.vue
// 图片资源加载失败
<img src="123" alt="" @error="event => handleError(event)" />
...
handleError(event) {
    console.log('handleError-----', event);
    throw new Error('图片加载失败了'); // 手动抛出异常，以便全局事件可以监听到
}
```

# errorHandler

Vue全局错误监听处理，所有组件的错误信息默认都会汇总到此。
由于errorHandler是全局配置的，因此window.onerror将会“失效”，即errorHandler能捕获的错误，onerror将不能捕获；

```js
// main.js
...
const app = createApp(App)
app.config.errorHandler = (err, vm info) => {
    console.log('errorHandle', err, vm, info);
    // err，错误对象
    // vm，发生错误的组件实例
    // info，Vue特定的错误信息，例如错误发生的生命周期、错误发生的事件
}
```

# errorCaptured

errorCaptured是Vue生命周期中的一个，用于捕获当前组件的所有后代组件产生的错误。函数如果返回为false，则会阻止error继续上传，全局的错误监听将不能捕获该error；否则，全局的错误监听也会再处理error。



# 总结

+ 在Vue环境中，大多数错误可使用errorHandler配置和errorCaptured钩子解决，但是不能处理异步错误
+ 监听异步错误，多采用window.onerror
+ 默认情况下，如果定义了全局的errorHandler，所有的error都将最终汇总到errorHandler中做统一处理
+ 如果一个组件的继承链或父链存在多个errorCaptured钩子，则这些钩子将会被相同的错误逐级唤起。

