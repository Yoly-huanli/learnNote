# vue-router原理

**改变视图的同时不会向后端发出请求**

## hash模式

通过url的哈希值来进行路由，加载不同组件

```
http://localhost/#/a
http://localhost/#/b
```

此时#/a，#/b是哈希值，但是url发送请求不会带这个哈希值，所以不会重新加载页面

## history模式

```
http://localhost/a
http://localhost/b
```

利用 HTML5 History Interface 中新增的 `pushState()` 和 `replaceState()` 方法，此时虽然改变了当前的 URL，但浏览器不会立即向后端发送请求。

## MemoryHistory(abstract history)模式

相当于一个tab，一直在当前页面

```
http://localhost
http://localhost
```