**compositon api提供了以下几个函数：**

- `setup`
- `ref`
- `reactive`
- `watchEffect`
- `watch`
- `computed`
- `toRefs`
- 生命周期的`hooks`



- composition和options如何做到和谐共存？
- setup中的this指向哪？
- setup的参数是什么？
- setup的执行时刻
- setup中的生命周期钩子是怎么实现的？
- 

创建组件实例，然后初始化`props`，紧接着就调用`setup`函数。 从生命周期的角度来看，它会在`beforeCreate`之前执行。也就是创建组件先执行`setup`、`beforeCreate`、`create`。



由于不能在`setup`函数中使用`data`、`methods`，为了避免使用`Vue`出错，所以把`setup`函数中`this`修改为了`undefined`。



#### props

接收组件传递过来的所有数据，并且都是响应式的。

#### context

该参数提供一个上下文对象，从原来的2.x中选择性的暴露了一些属性。

- attrs
- slots
- emit

### 返回值

可以将`setup`函数返回值渲染到页面上。但前提是，`setup`返回值必须是一个对象，否则返回其它值则渲染无效。