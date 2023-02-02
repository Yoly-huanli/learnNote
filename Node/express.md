[toc]

express 是一个轻量级的 Node Web 服务端框架，同样是一个人气超高的项目，它可以帮助我们快速搭建基于 Node 的 Web 应用

官网：https://www.expressjs.com.cn/zh-tw/guide/routing.html

# 中间件

中间件是一个函数，在请求和响应周期中被顺序调用

```js
const myLogger = function(req, res, next) {
  console.log('myLogger')
  next()
}

app.use(myLogger)
```

+ 必须有next(), 否则不会向下执行
+ 一般情况中间件需要放在请求之后，请求响应之前被调用

# 路由

主要分为两部分

- 请求方法：get、post

```
// 监听 / 路径的 get 请求
app.get('/', function(req, res) {
  res.send('hello node')
})
//post请求
app.post('/', function (req, res) {
  res.send('POST request to the homepage')
})
```

+ 请求路径

  ```
  '/random.text'
  '/ab?cd'
  '/ab+cd'
  /a/
  /.*fly$/
  .......
  ```

+ 请求参数

  ```
  Route path: /users/:userId/books/:bookId
  Request URL: http://localhost:3000/users/34/books/8989
  req.params: { "userId": "34", "bookId": "8989" }
  ```

# 异常处理

通过自定义中间件进行异常处理: 此时需要请求发生之后再通过中间件进行异常处理

```js
app.get('/', function(req, res) {
  throw new Error('something has error...')
})

const errorHandler = function (err, req, res, next) {
  console.log('errorHandler...', err)
  res.status(500).json({
    error: '--',
    msg: '服务端报错'
  })
  res.send('down...')
}

app.use(errorHandler)
```



# 项目示例

参考 https://segmentfault.com/a/1190000025187742

## 初始化

```
npm init -y
npm i -S express
```

## web服务器

```js
const express = require('express')

// 创建 express 应用
const app = express()

// 监听 / 路径的 get 请求
app.get('/', function(req, res) {
  res.send('hello node')
})

// 使 express 监听 5000 端口号发起的 http 请求
const server = app.listen(9999, function() {
  const { address, port } = server.address()
  console.log('Http Server is running on http://%s:%s', address, port)
})
```

启动

```
node app.js
```

浏览器打开监听的端口 localhost:9999进行请求

## 模块化处理路由

```js
const router = require('./router')
app.use('/', router)
```

router文件如下

```js
const express = require('express')
const boom = require('boom')
const userRouter = require('./user')
const {
  CODE_ERROR
} = require('../utils/constant')

// 注册路由
const router = express.Router()

router.get('/', function(req, res) {
  res.send('欢迎学习管理后台')
})

// 通过 userRouter 来处理 /user/... 路由，对路由处理进行解耦
router.use('/user', userRouter)

/**
 * 集中处理404请求的中间件
 * 注意：该中间件必须放在正常处理流程之后
 * 否则，会拦截正常请求
 */
// boom.notFound('接口不存在')和自定义的异常throw new Error('something has error...')是一样的
router.use((req, res, next) => {
  next(boom.notFound('接口不存在'))
})

/**
 * 自定义路由异常处理中间件
 * 注意两点：
 * 第一，方法的参数不能减少
 * 第二，方法的必须放在路由最后
 */
router.use((err, req, res, next) => {
  const msg = (err && err.message) || '系统错误'
  const statusCode = (err.output && err.output.statusCode) || 500;
  const errorMsg = (err.output && err.output.payload && err.output.payload.error) || err.message
  res.status(statusCode).json({
    code: CODE_ERROR,
    msg,
    error: statusCode,
    errorMsg
  })
})

module.exports = router
```

