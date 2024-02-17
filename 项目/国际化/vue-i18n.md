[toc]

https://juejin.cn/post/6844903497817653262?searchId=20231121164227DBA767560D88A99C0674

https://zhuanlan.zhihu.com/p/110112552?utm_id=0

# 翻译

基础使用方法：

```js
// 准备翻译的语言环境信息
const messages = {
  en: {
    message: {
      hello: 'hello world'
    }
  },
  ja: {
    message: {
      hello: 'こんにちは、世界'
    }
  }
}

// 方法1：通过选项创建 VueI18n 实例
const i18n = new VueI18n({
  locale: 'ja', // 设置地区
  messages, // 设置地区信息
})

// 通过 `i18n` 选项创建 Vue 实例
new Vue({ i18n }).$mount('#app')


// 方法2： 通过模块化
import Vue from 'vue'
import VueI18n from 'vue-i18n'
Vue.use(VueI18n)
```

主要工作：

+ 创建VueI18n对象，指定语言和资源
+ 作为插件挂到全局Vue上

==》VueI18n做了什么， 怎么挂载到全局vue上

## VueI18n对象

文件位置： 入口文件src/index， 导出了VueI18n

 new VueI18n， 并且内部创建了this._vm = new Vue()对象

```js
export default class VueI18n {
  xxxxxxx
  constructor (options: I18nOptions = {}) {
    // 初始化，如果有全局Vue，则传入window.Vue，自动去安装
    if (!Vue && typeof window !== 'undefined' && window.Vue) {
      install(window.Vue)
    }
    xxxxxxx
    // 实例化一个vue对象，将传入的参数变成vue中的响应式数据，大部分vue的插件都使用这种做法，比如vuex
    //  this._vm = new Vue({ data })
    this._initVM({
      locale,
      fallbackLocale,
      messages,
      dateTimeFormats,
      numberFormats
     })
  }
// 在constructor里声明实例方法和在class里声明方法，前者的方法是挂在了实例上，后者的方法是挂在了prototype上

// VueI18n对象上暴露出去的方法、属性，都关联于this._initVM这个方法里创建的Vue对象(this._vm)
getLocaleMessage (locale: Locale): LocaleMessageObject {
    return looseClone(this._vm.messages[locale] || {})
 }
// VueI18n监听自己的数据对象$data(是个深度监听，即deep为true，常见的修改就是动态改变了语言设置locale了)，如果发生了改变，就通知到各个Vue实例去强制更新(调用的是$forceUpdate())
watchI18nData (): Function {
     const self = this
     return this._vm.$watch('$data', () => {
       let i = self._dataListeners.length
       while (i--) {
         Vue.nextTick(() => {
           self._dataListeners[i] && self._dataListeners[i].$forceUpdate()
         })
       }
     }, { deep: true })
 }
  
}

VueI18n.install = install
```



## install 绑定在全局Vue上

文件位置：src/install

```js
export function install (_Vue, options = { bridge: false }) {
  Vue = _Vue
  // 判断是否安装过
  // 判断版本
  // 对全局Vue做了扩展，新增了方法，在extend.js里
  extend(Vue)
  // 将一个混入对象应用到全局Vue上, Vue.mixin() 是全局注册混入对象，影响之后创建的所有 vue 实例
  Vue.mixin(defineMixin(options.bridge))
  // 注册一个t的自定义指令
  Vue.directive('t', { bind, update, unbind })
  // 注册Vue组件
  Vue.component(interpolationComponent.name,   interpolationComponent)
  Vue.component(numberComponent.name, numberComponent)
  // 合并Vue 实例的 i18n 选项。具体来说，如果子组件有定义 i18n 选项，则使用子组件的值，否则使用父组件的值。
  const strats = Vue.config.optionMergeStrategies
  strats.i18n = function (parentVal, childVal) {
    return childVal === undefined
      ? parentVal
      : childVal
  }
}
```



## **extend.js**

把vueI18n创建的this._vm上的方法绑定在全局Vue上

```js
// 代理的线路是VueI18n创建的Vue对象 → VueI18n对象(.vm) → 全局Vue对象(.[$]i18n)。
export default function extend (Vue: any): void {
  // Vue实例原型上加上$i18n属性，使用的时候调用this._i18n， 也就是vue.prototype._i18n 
  if (!Vue.prototype.hasOwnProperty('$i18n')) {
    Object.defineProperty(Vue.prototype, '$i18n', {
      get () { return this._i18n }
    })
  }

  // 在Vue实例上添加$t方法，实际上调用的是Vue.$i18n.$t, 也就是Vuei18n._i18n.$t
  // 在 Vue 的原型中挂载 $t 方法，这是我们为什么能够直接在模板中使用的原因
// 把 VueI18n 对象实例的方法都注入到 Vue 实例上
  Vue.prototype.$t = function (key: Path, ...values: any): TranslateResult {
    const i18n = this.$i18n
    return i18n._t(key, i18n.locale, i18n._getMessages(), this, ...values)
  }
 // 同理$tc,$te,$d,$n
}

```

// 其中，所用到的_t在new VueI18n()的时候定义了

```js
_t (key: Path, _locale: Locale, messages: LocaleMessages, host: any, ...values: any): any {
  if (!key) { return '' }
  const parsedArgs = parseArgs(...values)
  // 如果 escapeParameterHtml 被配置为 true，那么插值参数将在转换消息之前被转义。
  if(this._escapeParameterHtml) {
    parsedArgs.params = escapeParams(parsedArgs.params)
  }
  const locale: Locale = parsedArgs.locale || _locale
  // 翻译
  let ret: any = this._translate(
    messages, locale, this.fallbackLocale, key,
    host, 'string', parsedArgs.params
  )
}

//  _translate
_translate (){
   ...this._interpolate(step, messages[step], key, host, interpolateMode, args, [key])
}

// _interpolate
this._render(ret, interpolateMode, values, key)

// _render, 可以调用自定义方法去处理插值对象，或者是默认的方法处理插值对象。
_render () {
   // 默认的插值对象
    ret = defaultFormatter.interpolate(message, values, 
}
                                       
// defaultFormatter
export default class BaseFormatter {
  // 实现缓存效果
  _caches: { [key: string]: Array<Token> }

  constructor () {
    this._caches = Object.create(null)
  }

  interpolate (message: string, values: any): Array<any> {
    // 没有插值对象的话，就直接返回
    if (!values) {
      return [message]
    }
    // 如果存在 tokens，则组装值返回
    let tokens: Array<Token> = this._caches[message]
    if (!tokens) {
      // 没有存在 tokens，则拆分 tokens
      tokens = parse(message)
      this._caches[message] = tokens
    }
    return compile(tokens, values)
  }
}
```



以下为一个示例：

```
<p>{{ $t('message.sayHi', { name: 'Gopal' })}}</p>
```

主要涉及两个方法，我们先来看 parse，代码比较直观，可以看到本质上是遍历字符串，然后遇到有 {} 包裹的，把其中的内容附上类型拿出来放入到 tokens 里返回。

```
if (char === '{') {
      if (text) {
        tokens.push({ type: 'text', value: text })
      }
      
// 结果
[
    {
        "type": "text",
        "value": "hi, I am "
    },
    {
        "value": "name",
        "type": "named"
    }
]
```

还有 parse，就是将上述的组装起来

```
最后返回 ["hi, I am ", "Gopal"]
```



## mixin

文件位置： src/mixin

```js
export default function defineMixin (bridge: boolean = false) {
  function mounted (): void {
    XXX
    }
  }
  return bridge
    ? { mounted } 
    : { 
    beforeCreate (): void {
    // 把_i18n和_i18nWatcher都准备好, 考虑多种场景, new Vue({ i18n }).$mount('#app')的时候this.$options有i18n
      const options: any = this.$options
      XXX
      this._i18n = options.i18n
      this._i18nWatcher = this._i18n.watchI18nData()
    },

    beforeMount (): void {
    },

    mounted,

    beforeDestroy (): void {
    }
  }
}

```





#  涉及到的知识点

## 1.怎么判断是不是有html标签

```JS
const htmlTagMatcher = /<\/?[\w\s="/.':;#-\/]+>
```

1. `/<` 和 `>/`: 匹配标签的开始 `<` 和结束 `>`。
2. `\/?`: 匹配零个或一个 `/`，表示标签可能是自闭合标签（如 `<img />`）或普通标签（如 `<div></div>`）。
3. `[\w\s="/.':;#-\/]+`: 匹配标签名和标签属性。具体解释如下：
   - `[\w\s="/.':;#-\/]`: 字符集，包含字母（`\w`）、空白字符（`\s`）、斜杠（`\/`）、双引号（`"`）、单引号（`'`）、冒号（`:`）、分号（`;`）、井号（`#`）、点号（`.`）和减号（`-`）。
   - `+`: 表示前面的字符集可以出现一次或多次，确保匹配标签名和属性中的一个或多个字符。