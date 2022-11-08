[toc]

# 目录配置

+ 新建文件夹micro-test
+ 新建vue3项目base作为基座
+ 新建vue2项目learnjs和learnts作为子应用
+ 在base里新建micro目录，用来存放所有微前端相关的操作

# 子项目改造

以learnjs为例子

+ Webpack.base.js

  ```json
  const { name } = require('./../package');
  ...
  module.exports = {
    context: path.resolve(__dirname, '../'),
    entry: {
      app: './src/main.js'
    },
    output: {
      path: config.build.assetsRoot, //打包到dist目录
      filename: '[name].[hash].js',         //打包后文件名:learnjs，配置为哈希模式
      publicPath: process.env.NODE_ENV === 'production'
        ? config.build.assetsPublicPath
        : config.dev.assetsPublicPath, //publicpath设置为 http://localhost:9091
      libraryTarget: 'umd',            //打包为umd格式
      library: 'learnjs',              //允许全局获取，比如window.learnjs可以拿到
      jsonpFunction: `webpackJsonp_${name}`, //用来按需加载chunk的JSONP函数
    },
  ```

  重点：需要配置libraryTarget和library,  使用的是webpack4，配置jsonpFunction,  如果使用的是webpack5，配置chunkLoadingGlobal

+ webpack.dev.js

  ```json
  devServer: {
      xxx
      headers: {
        'Access-Control-Allow-Origin': '*', //本地服务的跨域内容
      },
  ```

  重点：配置可允许跨域

+ Main.js里修改

  ```js
  //便于卸载时使用
  let instance = null;
  
  //不是微前端环境，直接加载
  if (!window.__MICRO_WEB__) {
    instance = new Vue({
      el: '#app',
      router,
      components: { App },
      template: '<App/>'
    })
  }
  
  //以下是子应用暴露的生命周期函数，便于主应用控制
  export async function bootstrap() {
    console.log('开始加载')
    console.log('vue app bootstraped');
  }
  
  export async function mount() {
    console.log('渲染成功')
    instance = new Vue({
      router,
      store,
      render: h => h(App)
    }).$mount('#app')
  }
  export async function unmount(ctx) {
    instance = null;
    console.log('卸载', instance)
  }
  ```

# 基座开发

作用：中央控制器，公共依赖都放在主应用里，并且控制子应用的加载

## 页面逻辑书写

+ 设置头部导航区，点击之后可以跳转到子应用
+ 设置中部内容区，用于显示子应用
+ 设置底部区域

```vue
//app.vue
<template>
  <HeaderNav/>
  <SubContainer/>
  <FooterText/>
</template>
```

```vue
//header是头部区域，menu点击切换，NAV_LIST是头部导航列表
<template>
  <div>
    <el-menu
      :default-active="activeIndex"
      class="el-menu-demo"
      mode="horizontal"
      @select="handleSelect"
      background-color="#545c64"
      text-color="#fff"
      active-text-color="#ffd04b">
      <el-menu-item
        v-for="(item, index) in NAV_LIST"
        :index = "item.value"
        :key="index"
      >
        {{ item.name }}
      </el-menu-item>
    </el-menu>
  </div>
</template>
// tab栏
 const NAV_LIST = [
        {
          name: 'js资料',
          status: true,
          value: 0,
          url: '/learnjs#/index',
          hash: '',
        },
        {
          name: 'ts资料',
          status: false,
          value: 1,
          url: '/learnts#/index',
        }
    ]
```

```vue
//SubContainer用于显示子应用的内容
<template>
  <div class="main-con">
    <div class="main-loading"
      v-loading="loading">
    </div>
    <div id="sub-container" v-show="!loading">子应用</div>
  </div>
</template>
```

## 子应用注册

注册子应用信息，使得全局可以获取到子应用的详细信息

+ 子应用信息

  ```js
    export const SUB_CHILD_LIST  = [
    {
      name: 'learnjs',              // 子应用名称
      entry: '//localhost:9091/',   // 子应用加载的入口
      container: '#sub-container',  // 子应用加载到哪个基座的容器内
      activeRule: '/learnjs',   //子应用激活逻辑
    },
    {
      name: 'learnts',
      entry: '//localhost:9092/',
      container: '#sub-container',
      activeRule: '/learnts',
    }
  ];
  ```

+ 子应用信息全局都能访问配置(不用window)

  ```js
  let sublist = []
  
  export function setSubList(list){
    sublist = list
  }
  export function getSubList(){
    return sublist
  }
  ```

  然后通过registerSub在main的之后执行，初始化把子应用信息配置好

  ```js
  import {setSubList, getSubList} from './../store/sublist'
  
  export function registerSub(list){
    setSubList(list)
    console.log('getSubList--', getSubList())
  }
  ```
  
  在main.js里调用

  ```js
  import {registerSub} from './micro/start'
  import {SUB_CHILD_LIST} from './const/subChildList'
  registerSub(SUB_CHILD_LIST)
  ```



## 路由变化监听

+ 点击导航，路由切换为当前规定的url
+ 监听url的变化
+ 根据当前url里的关键信息拼接出子应用的加载路径

### 路由基础配置

```js
//main.js
import router from './router'
app.use(router)
//router.js
import { createRouter, createWebHistory } from 'vue-router';
const routes = [
    {
      path: '/learnjs',
      component: () => import('../components/FooterText.vue')             
    },
    // {
    //   path: '/learnjs',
    //   component: () => import('../APP.vue')    
    // },
    // {
    //   path: '/learnts',
    //   component: () => import('../APP.vue')    
    // }
]

const router = createRouter({
  history: createWebHistory(),
  routes,
});
export default router;
```

### 导航切换时路由改变

```js
import { useRouter, useRoute } from 'vue-router'
setup(){
    const router = useRouter();
    const route = useRoute();
    ...
    function handleSelect (key){
      router.push(NAV_LIST[key].url)
    }
    return{
      handleSelect
    } 
  },
```

### 监听路由的变化

新建自定义事件，监听到自定义事件执行时，可以执行window.history.pushState的同时可以执行自定义的方法

+ 监听

```js
import { turnApp, patchRouter } from './routerHandler';

export const rewriteRouter = () => {
  window.history.pushState = patchRouter(window.history.pushState, 'micro_push');
  window.history.replaceState = patchRouter(window.history.replaceState, 'micro_replace');

  // 添加路由跳转事件监听
  window.addEventListener('micro_push', turnApp);
  window.addEventListener('micro_replace', turnApp);
  window.onpopstate = async function () {
    await turnApp()
  }
}
```

+ patchRouter包装原有的window.history.pushState方法， 执行window.history.pushState的时候，创建自定义事件new Event(), 触发执行，然后正常执行原有的window.history.pushState方法

```js
export const patchRouter = (event, name) => {
  return function () {
    // 创建一个自定义事件
    const e = new Event(name);
    // 让event来代替本函数执行
    event.apply(this, arguments);
    // 通过dispatchEvent来触发自定义事件
    window.dispatchEvent(e);
  };
};
```

+ 这样，触发自定义事件micro_push执行时，可以执行自定义的事件turnApp()

  ```js
  export const turnApp = async () => {
    console.log('路由变化-----')
  }
  ```

+ 基座初始化，加载完子应用后，开始监听路由的变化

  ```js
  rewriteRouter()
  export const start = () =>  {
    // 获取子应用列表
    const appList = getSubList()
    if (!appList.length) {
      throw Error('子应用列表为空，请查看是否正确注册');
    } else {
      const app = currentApp();
      // 初始化的时候url为 http://172.24.221.18:8080/learnjs/#/index
      // 根据url获取到第一个子应用的信息为 {name: 'learnjs', entry: '//localhost:9091/', container: '#sub-container', activeRule: '/learnjs'}
      if (app) {
        const { pathname, hash } = window.location
        const url = pathname + hash
        // url为/learnjs/#/index
        // 强行触发一次路由变化监听
        window.history.pushState(url, app.name, url || app.activeRule)
      }
    }
  }
  
  // 获取当前应用
  export const currentApp = () => {
    const currentRouter = window.location.pathname.match(/(\/\w+)/)[0];
    return filterApp('activeRule', currentRouter);
  };
  
  export const filterApp = (key, rule) => {
    const currentApp = getSubList().filter(app => app[key] === rule);
    return currentApp[0]
  };
  ```

+ 基座的main初始化时候

  ```js
  import {registerSub, start} from './micro/start'
  registerSub(SUB_CHILD_LIST)
  start()   
  ```

+ 优化

  切换路由的时候，可以看到多次触发了turnApp，因为push、replace都要触发，因此可以加一个标识位，标识当前的tab位置，如果在执行turnApp的时候，发现当前的url没变，那么就不再执行(利用window标识状态)

  ```js
  //start补充
  window.__CURRENT_SUB_APP__ = app.activeRule;
  // turnApp调试
  export const turnApp = async () => {
    if(window.__CURRENT_SUB_APP__ === window.location.pathname){
      console.log('只触发一次')
      return
    }
    console.log('路由变化-----')
  }
  ```

## 主应用生命周期

### 注册主应用生命周期

主应用生命周期应该是初始化的时候把需要的操作传递进去

```js
//main.js
registerSub(SUB_CHILD_LIST, {
    beforeLoad: [
      ()=>{
        console.log('开始加载')
      }
    ],
    mounted: [
      ()=>{
        console.log('渲染完成')
      }
    ],
    destoryed: [
      ()=>{
        console.log('卸载完成')
      }
    ],
  }
)
```

然后在registerSub函数内部增加设置生命周期函数的操作

```js
import {setMainLifecycle} from './store/mainLifeCycle'
export function registerSub(list, lifecycle){
  setSubList(list)
  //在这里可以调用主应用的生命周期
   lifecycle.beforeLoad[0]()
   setTimeout(()=>{
      lifecycle.mounted[0]()
    },1000)
  setMainLifecycle(lifecycle)
}
  setMainLifecycle(lifecycle)
}
```

同理，生命周期函数应该是全局都能获取到，所以设置一个变量来存储与操作

```js
let mainLifecycle = {}
export const getMainLifecycle = () => mainLifecycle
export const setMainLifecycle = (data) => {
  mainLifecycle = data
}
```

### 利用生命周期函数改造loading

便于控制加载子应用之前和之后的loading显示

+ 将loading状态抽离出来

  ```js
  import { ref } from 'vue';
  
  export const loadingStatus = ref(false);
  
  // 开启loading
  export const openLoading = () => {
    loadingStatus.value = true;
  };
  
  // 关闭loading
  export const closeLoading = () => {
    loadingStatus.value = false;
  };
  ```

+ 改变页面loading控制

  ```vue
  <template>
    <div class="main-con">
      <div class="main-loading"
        v-loading="loadingStatus">
      </div>
      <div id="sub-container" v-show="!loadingStatus">子应用
        <router-view/>
      </div>
    </div>
  </template>
  <script>
  import {loadingStatus} from './../store/loadingStatus'
  export default {
    name: 'SubContainer',
    props: {
     
    },
    data() {
      return {
        tableData: []
      };
    },
    setup(){ 
      return{
        loadingStatus
      } 
    },
  }
  </script>
  ```

+ main生命周期函数不同阶段就可以调用改变loading的状态

  ```js
  import {openLoading, closeLoading} from './store/loadingStatus'
  
  registerSub(SUB_CHILD_LIST, {
      beforeLoad: [
        ()=>{
          openLoading()
          console.log('开始加载')
        }
      ],
      mounted: [
        ()=>{
          closeLoading()
          console.log('渲染完成')
        }
      ],
      destoryed: [
        ()=>{
          console.log('卸载完成')
        }
      ],
    }
  )
  ```

  

## 触发生命周期

+ 改写路由改变时的自定义函数方法, 并且获取上一个子应用和当前子应用

  ```js
  export const turnApp = async () => {
    if(!isTurnChild()){
      console.log('路由变化-----')
    }
  }
  
  // 查看当前路由是否有变化
  export const isTurnChild = () => {
    //将CURRENT_SUB_APP暂存起来
    window.__ORIGIN_APP__ = window.__CURRENT_SUB_APP__;
    // 如果当前的子应用路由没变，直接返回
    if(window.__CURRENT_SUB_APP__ === window.location.pathname){
      return false
    }
    // 如果当前的路由变了，那么当前的子应用就是当前路由
    window.__CURRENT_SUB_APP__ = window.location.pathname
    console.log('---',  window.__ORIGIN_APP__, window.__CURRENT_SUB_APP__) // /learnjs /learnts
    return true
  };
  ```

+ 触发生命周期函数:检测到子应用变化的时候触发主应用和子应用的生命周期

  ```js
  export const turnApp = async () => {
    if(!isTurnChild()){
      await lifecycle()
    }
  }
  ```

  ```js
  import {findAppByRoute} from '../utils/index';
  import {getMainLifecycle} from '../store/mainLifeCycle'
  
  // 改变了路由，重新装载新的子应用
  export const lifecycle = async () => {
    const prevApp = findAppByRoute(window.__ORIGIN_APP__); // 获取上一个子应用
    const nextApp = findAppByRoute(window.__CURRENT_SUB_APP__); // 获取跳转后的子应用
    if (!nextApp) {
      return
    }
    if (prevApp) {
      // 卸载上一个应用
      await unmount(prevApp);
    }
    //加载下一个应用
    await boostrap(nextApp);
    await mount(nextApp);
  }
  
  // 装载应用
  export const boostrap = async (app) => {
    await runMainLifeCycle('beforeLoad', app)
    app && app.bootstrap && await app.bootstrap();
  }
  
  // 渲染应用
  export const mount = async (app) => {
    app && app.bootstrap && await app.mount(app);
    await runMainLifeCycle('mounted', app)
  }
  
  // 卸载
  export const unmount = async (app) => {
    app && app.unmount && await app.unmount(app);
    await runMainLifeCycle('destoryed', app)
  }
  
  // 执行主应用生命周期
  export const runMainLifeCycle = async (type, app) => {
    const mainLife = getMainLifecycle();
    // 因为主应用里配置的生命周期是一个数组，所以需要执行数组中的所有内容
    await Promise.all(mainLife[type].map(item => item(app)))
  }
  ```

  

# 子应用渲染

