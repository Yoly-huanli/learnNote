[toc]

# Vue3设计思路

## 声明式描述UI

vue3既可以使用模版描述UI， 也可以使用js（虚拟dom）描述UI， js描述更为灵活, h函数是简化了创建虚拟dom的描述语句， 不需要去写props、children这些

```vue
// 模版描述UI
<h1 @click="handler"><span></span></h1>
// js描述UI
const title = {
  tag: 'h1',
  props: {
   onClick: handler
 },
 children: [
 { tag: 'span' }
 ]
}
// h函数
 h('h1', { onClick: handler })
```

## 渲染器

虚拟dom转化为真实的dom，把虚拟dom的描述使用底层js的原生方法进行实现，比如创建creatElement，添加方法addEventListener, 更新的时候diff找到变更点单独进行更新而不是全部进行重新创建

元素直接通过tag渲染， 而组件实际上就是一个对象， 函数执行返回vdom, 再对返回的vdom进行渲染就可以了

```js
// MyComponent 是一个对象 
const MyComponent = {
  render() { 
    return {
     tag: 'div', props: {
     onClick: () => alert('hello') },
      children: 'click me' }
  } 
}

function mountComponent(vnode, container) {
// vnode.tag 是组件对象，调用它的 render 函数得到组件要渲染的内容(虚拟 DOM)  
  const subtree = vnode.tag.render()
// 递归地调用 renderer 渲染 subtree
  renderer(subtree, container)
}
```

## 编译器

把模版的UI描述方式通过编译器转变为vdom

```vue
<template>
  <div @click="handler">
  click me 
  </div>
</template>
<script>
export default {
	data() {/* ... */}, methods: {
	handler: () => {/* ... */} }
} 
</script>
```

编译为

```js
export default {
   data() {/* ... */},
   methods: {
     handler: () => {/* ... */}
   },
   render() {
     return h('div', { onClick: handler }, 'click me')
   }
}
```

编译的阶段可以识别动态的内容并做好标记，编译渲染更新的时候更快找到变更的内容m 可以提升性能

```js
render() {
 return {
  tag: 'div', 
  props: {
    id: 'foo',
    class: cls 
  },
  patchFlags: 1 // 假设数字 1 代表 class 是动态的 }
}
```

将原始的template模版转化为抽象语法树AST， 这个过程要通过有限状态自动机切割出token， 然后根据html树形结构确定层级关系构造出AST， 再把模版AST转化为JS的AST， 最后补全为渲染函数h

# 响应系统的设计与实现

## 副作用函数与响应式数据

effect 函数的执行会直接或间接影响其他函数的执行，这时我们说 effect 函数产生了副作用,  effect函数可以修改body的值，其他函数读取body的值会受到影响，所以effect是副作用函数

```JS
function effect() {
  document.body.innerText = 'hello vue3' 03
}
```

响应式数据是指数据变化的时候副作用函数自动执行: 如果text变化后effect可以自动执行，那么obj就是一个响应式数据

```JS
const obj = { text: 'hello world' } 
function effect() {
// effect 函数的执行会读取 obj.text
  document.body.innerText = obj.text
}
```

## 响应式数据的基本实现

拦截对象属性，读取的时候obj.text把副作用函数effect存起来，设置的时候自动执行effect函数





# 原始值的响应式方案

## ref包裹原始值实现响应式

原始值Boolean、Number等无法通过proxy拦截， 要实现原始值也能响应式，可以把它用object包裹，proxy拦截object

```js
let str = 'vue'
// 无法拦截对值的修改
str = 'vue3'

// 包裹为对象并转化为响应式
 function ref(val) {
   const wrapper = {
     value: val
   }
// 使用 Object.defineProperty 在 wrapper 对象上定义一个不可枚举的属性 __v_isRef，并且值为 true
 Object.defineProperty(wrapper, '__v_isRef', {
  value: true
 })
 return reactive(wrapper)
}
```

普通值包裹的对象和原始的对象要区分开， 所以在包裹对象上添加一个不可枚举也不能修改的属性__v_isRef表示是原始值包裹出来的

##  toRefs解决响应丢失的问题

对响应式数据使用展开运算符的时候，会转换为普通的对象， 不再是响应式了，解决方法： 展开后的数据，对于每一个属性都配置上拦截的get的set方法包裹

```js
// obj 是响应式数据
const obj = reactive({ foo: 1, bar: 2 })

// 将响应式数据展开到一个新的对象 newObj
const newObj = {
 ...obj
}
```

```js
 function toRef(obj, key) {
   const wrapper = {
     get value() {
      return obj[key]
     },
    // 允许设置值
     set value(val) {
     	obj[key] = val
    }
  }
 Object.defineProperty(wrapper, '__v_isRef', {
   value: true
 })
 return wrapper
}
```

使用的时候

```js
// 单个使用
const newObj = {
  foo: toRef(obj, 'foo'),
  bar: toRef(obj, 'bar')
}
// 批量使用
const newObj = { ...toRefs(obj) }
```

## **自动脱** ref

由于给每个对象的属性包裹了一层，现在导致每次访问属性的值必须得```objRef.value```

但是使用的时候希望是直接能访问到， 而不是还要用.value,  这个过程是vue的template模版自动会处理的， 处理原理也是加一层拦截，读取值的时候直接返回值的value

# 非原始值的响应式方案

## proxy和reflect

proxy可以实现对其他对象基本语义的代理， 也就是能代理get, set等操作， 对于for...in或无法直接代理

reflect的第三个参数可以理解为函数调用过程中的 this

```js
Reflect.get(target, key, receiver)
```

## 代理object

### 读取obj.foo

通过get

```JS
get(target, key, receiver) {
  // 建立联系
  track(target, key)
  // 返回属性值
  return Reflect.get(target, key, receiver)
},
```

### for...in

in操作符通过has

```js
const obj = { foo: 1 } 
const p = new Proxy(obj, {
  has(target, key) {
  track(target, key)
  return Reflect.has(target, key)
} })
```

循环通过Reflect.ownKeys间接拦截， 因为循环过程要读取循环对象的key

```js
const obj = { foo: 1 }
const ITERATE_KEY = Symbol()
const p = new Proxy(obj, { ownKeys(target) {
// 将副作用函数与 ITERATE_KEY 关联 
  track(target, ITERATE_KEY) 
  return Reflect.ownKeys(target)
} })
```

### 修改属性

如果是新增，会同时影响set操作和for...in 操作， 如果只是修改属性，只会对set产生影响，隐藏属性修改要分情况进行操作

### 浅响应与深响应

+ 浅响应shallowReactive：只能监控对象的第一层改变
+ 深响应Reactive

### 只读和浅只读

+ 浅只读：isShallow
+ 深只读：isReadonly

```js
 createReactive(obj, isShallow = false, isReadonly = false) 
```

## 代理数组

### 设置数组的索引与length值

+ 设置索引时，小于length, 直接设置， 如果大于了length, 那么还应该触发length操作
+ length属性可能导致元素的删除

### for...in

数组不推荐用，添加新元素arr[10]=1或者修改length会导致数组的长度发生变化， 因此需要额外去建立读取属性和length的依赖关系

### for...of

迭代器模式，实际上是和数组的索引以及length建立依赖

### 查找方法

arr.includes需要重写分情况去调用,因为有可能在原始数据上调用查找， 也可能在代理对象上进行查找， indexOf和lastIndexOf也需要相同的操作

```js
const originMethod = Array.prototype.includes

const arrayInstrumentations = { includes: function(...args) {
// this 是代理对象，先在代理对象中查找，将结果存储到 res 中 
  let res = originMethod.apply(this, args)
if (res === false) {
// res 为 false 说明没找到，通过 this.raw 拿到原始数组，再去其中查找并更新 res 值
  res = originMethod.apply(this.raw, args)
}
// 返回最终结果 
  return res
} }
```

### push等隐式修改数组

push内部实现会多次循环调用length的读写，最终导致调用栈溢出， 所以也需要重写， 把中间的一部分调用length的追踪响应过程屏蔽, pop、shift、unshift 以及 splice 等方法都需要做类似的处理

```js
// 一个标记变量，代表是否进行追踪。默认值为 true，即允许追踪
 let shouldTrack = true
 // 重写数组的 push 方法
;['push'].forEach(method => {
 // 取得原始 push 方法
  const originMethod = Array.prototype[method] 
  //重写
 arrayInstrumentations[method] = function(...args) {
 // 在调用原始方法之前，禁止追踪
 shouldTrack = false
 // push 方法的默认行为
 let res = originMethod.apply(this, args)
 // 在调用原始方法之后，恢复原来的行为，即允许追踪
  shouldTrack = true
  return res
 }
})
```

## 代理Set和Map

## size和delete

Set和Map和对象的不同在于，它们有自己的方法，比如new Map().set('key', 'value'), 要通过原始对象而不是代理对象去使用，而不是obj.key=value这种使用代理对象去使用

```js
// map.size
// map.delete('key')

const s = new Set([1, 2, 3]) const p = new Proxy(s, {
get(target, key, receiver) { if (key === 'size') {
return Reflect.get(target, key, target) }
// 将方法与原始数据对象 target 绑定后返回
return target[key].bind(target) }
} )
```

# 组件实现

组件和普通vdom结构差不多，只不过type是个对象而已， 实现的时候把data变成响应式数据，数据有变化的时候就触发组件的重新渲染， 但是这样渲染太频繁，可以把变更事件都搜集在promise微任务里，然后就可以缓存，过滤掉重复的事件，优化性能。 为了能直接使用this.xxx读取数据，实际上是对组件实例做了proxy拦截，去获取data或者props的数据

## props变化触发更新

props也放到组件的实例上，父组件的props出去的值发生改变的时候， 父组件会重新渲染，这时候需要进一步判断对应的子组件依赖的props值是否发生了变化，有变化则改变后子组件也重新渲染

## setup

setup要判断return的值，如果是个函数，那么会被当成一个渲染函数直接用，这时候template就没用了，如果是一个数组，暴露出去就行了

## emit事件

emit的事件转化的时候要转化事件名称，click==>onClick, 然后默认添加到props监听变化

## 插槽

<slot> 标签则会被编译为插槽函数的调用，通过执行对应的插槽函数，得到外部向槽位填充的内容(即虚拟 DOM)

## 生命周期

在setup中可以定义生命周期函数onMount等，并且可以注册多个同名的生命周期函数onMount钩子，可以在组件实例上建立数组保存这些钩子，然后在渲染函数的适合位置进行执行。

# 异步组件

异步加载组件，需要的时候才去加载组件，不需要的时候不加载， 异步组件在页面性能、拆包以及服务端下发组件等场景中尤为重要。

```js
const loader = () => import('App.vue') 
loader().then(App => {
 createApp(App).mount('#app')
})
```

## **defineAsyncComponent**

它是一个高阶组件，也就是返回组件的组件， 会对异步组件加载考虑各种特殊情况

+ 允许加载不成功时加载指定的错误组件
+ 允许指定loading组件和时间，表示超时XXX久没加载出来异步组件就加载loading组件
+ 允许指定超时的时长提供超时重试的能力

# 函数式组件

没有状态，没有声明周期，接收props然后展示， 比较简单

```js
function MyFuncComp(props) {
  return { type: 'h1', children: props.title } 
}
```







# 渲染器

响应式渲染： 响应式数据发生变化的时候就调用渲染函数进行页面渲染，这样就能自动实现响应式渲染

## 渲染器

渲染器包括渲染函数和激活函数等， 不只是渲染函数一个作用

## 渲染函数

把vnode转化并挂载/更新到对应的DOM节点上，可以在这里实现跨平台的能力



# 编译优化

编译器的作用： 把template模版内容编译为渲染函数

传统diff算法的缺点： 前后两棵dom树进行全部比较==》优化：在编译过程进行分析，静态的变量提到渲染函数外层进行重复利用、函数换成、动态的变量单独搜集到vdom的结构当中， 只进行动态变量的更新

## block

block其实也是vdom, 但是它额外有一个变量dynamicChildren数组，存储它的子代所有的动态节点， BLOCK只有根节点，v-if/v-else以及v-for是block节点

## 静态节点优化

+ 静态提升： 静态节点提升到渲染函数外部，以便于重复利用
+ 预字符串化： 静态节点直接以字符串的形式存在`<P>122</P><P>...`, 而不是为每个静态节点再去创建对应的VDOM
+ 缓存内联事件：避免造成不必要的组件更新
+ v-once指令： 元素带v-once指令，那么会使用缓存，不需要再参与diff

# 内部组件的实现原理

## keep alive

KeepAlive 的本质是缓存管理，再加上特殊的挂载/卸载逻辑。本质上是创建一个虚拟的容器，deActivate的时候把组件搬到虚拟的容器里， active的时候把组件从虚拟容器搬到组件实例上， 实现的时候实际上是在vnode上做keepalive的相关标识，然后再渲染器的mount和unmount的时候，就不会真的去卸载和挂载，而是做deActivate和active的搬运工作

### **include** **和** **exclude**

用来让用户自定义缓存规则， 是正则的类型

### max缓存

通过Map做缓存管理， 因为缓存太多的话可能会占用大量的内存，因此可以设置最大缓存数量，超出的时候按照“最新一次访问”的规则进行缓存删除， 需要把当前访问(或渲染)的组件作为最新一次渲染的组件，并且该组件在缓存修剪过程中始终是安全的，即不会被修剪。用户也可以通过cache属性自定义缓存规则

## teleport

作用： 把teleport的元素渲染到对应的to的元素下

实现原理： 在teleport的vnode上加上teleport组件的标识，然后渲染器单独判断进行渲染， 进行挂载和更新