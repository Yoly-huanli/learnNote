# composition API

# Fragment

```
<template>
  <div>
    <div>Hello</div>
    <div>World</div>
  </div>
</template>
```

在vue2中，每个组件只能有一个根节点，如果有多个节点，必须外层使用一个div进行全部的包裹，导致多渲染了一个div，现在可以使用一个类似dom的标签元素`<Fragment></Fragment>`进行包裹，它不会渲染为真实的dom

# Teleport(portal)

Teleport 提供了一种干净的方法，允许我们控制在 DOM 中哪个父节点下呈现 HTML，而不必求助于全局状态或将其拆分为两个组件。

比如有一个遮罩层,那么这个遮罩层的组件是写在div下的

```vue
<body>
  <div style="position: relative;">
    <h3>Tooltips with Vue 3 Teleport</h3>
    <div>
      <modal-button></modal-button>
    </div>
  </div>
</body>
```

如果使用Teleport，可以直接将遮罩层的组件<modal-button放在body下

```vue
<teleport to="body">
      <div v-if="modalOpen" class="modal">
        <div>
          I'm a teleported modal! 
          (My parent is "body")
          <button @click="modalOpen = false">
            Close
          </button>
        </div>
      </div>
    </teleport>
  `,
```

如果 `<teleport>` 包含 Vue 组件，则它仍将是 `<teleport>` 父组件的逻辑子组件

```
app.component('parent-component', {
  template: `
    <h2>This is a parent component</h2>
    <teleport to="#endofbody">
      <child-component name="John" />
    </teleport>
  `
})
```

这里child-component是parent-component的子组件

##  在同一目标上使用多个 teleport

挂载多个teleport，则是按顺序挂载

# Suspense

常常与异步组件配合使用

## 异步组件

在大型应用中，我们可能需要将应用分割成小一些的代码块，并且只在需要的时候才从服务器加载一个模块。

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() =>
  import('./components/AsyncComponent.vue')
)

app.component('async-component', AsyncComp)
```

## suspense作用

suspense包裹异步组件的时候，异步组件的加载由suspense控制，一部组件无法控制自身的加载、错误、延迟和超时选项，它自带两个 `slot` 分别为 `default、fallback`。顾名思义，当要加载的组件不满足状态时,`Suspense` 将回退到 `fallback`状态一直到加载的组件满足条件，才会进行渲染。

异步组件可以设置suspensible:false摆脱suspense的控制

```
<template>
  <button @click="loadAsyncComponent">点击加载异步组件</button>
  <Suspense v-if="loadAsync">
    <template #default>
      <AsyncComponent></AsyncComponent>
    </template>
    <template #fallback>
      <div class="loading"></div>
    </template>
  </Suspense>
</template>

<script>
import { defineAsyncComponent } from "vue";
export default {
  name: "App",
  data() {
    return {
      loadAsync: false
    };
  },
  components: {
    HelloWorld,
    AsyncComponent: defineAsyncComponent(() => {
      return import("./components/async-component.vue");
    })
  },
  methods: {
    loadAsyncComponent() {
      this.loadAsync = true;
    }
  }
};
</script>
```

## 其他

还可以suspense配合路由加载使用,使得在路由加载出来之前，显示为loading状态

```
<router-link to="/home">Home</router-link>|
<router-link to="/about">About</router-link>
<Suspense>
  <template #default>
    <router-view />
  </template>
  <template #fallback>
    <div class="loading"></div>
  </template>
</Suspense>
```

# ts支持

webpack中配置ts-loader进行转换

```js
module: {
    rules: [
      {
        test: /\.tsx?$/,
        loader: 'ts-loader',
        options: {
          appendTsSuffixTo: [/\.vue$/],
        },
        exclude: /node_modules/,
      },
```

参考：https://juejin.cn/post/6854573214547312654