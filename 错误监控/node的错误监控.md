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

