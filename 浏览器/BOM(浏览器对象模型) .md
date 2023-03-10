# 浏览器

## BOM(浏览器对象模型)

### 1.navigator

```
var us = navigator.userAgent //判断浏览器类型
var isChrome = us.indexOf('Chrome')
console.log(isChrome)
```

uerAgent是一个很长的字符串

```
"Mozilla/5.0 (iPhone; CPU iPhone OS 10_3_1 like Mac OS X) AppleWebKit/603
```

### location对象

既是window的对象，也是argument的对象, window.location与document.location是等价的

#### location的属性

完整url:[http://www.wrox.com:80/files?q=java](http://www.wrox.com/files?q=java)

| 属性              | 例子                                    |
| ----------------- | --------------------------------------- |
| location.hash     | #test                                   |
| location.host     | [www.wrox.com:80](http://www.wrox.com/) |
| location.hostname | [www.wrox.com](http://www.wrox.com/)    |
| location.port     | 80                                      |
| location.protocol | http                                    |

不太熟悉的

| 属性              | 例子                                                         |
| ----------------- | ------------------------------------------------------------ |
| location.search   | ?q=java                                                      |
| location.pathname | /files                                                       |
| location.href     | /[http://www.wrox.com:80/files?q=java](http://www.wrox.com/files?q=java) |

其它

```
1.window.location.toString():作用与window.location.href相同，获取完整url
2.window.location.origin:http://localhost:3000,协议，域名，端口号
```

#### 查询字符串参数

//http://localhost:3000/boss?q=k&m=9

- 问号之后的内容
- 1.window.location.search (?q=k&m=9问号以及之后的内容)
- 2.window.location.search.substring(1) (q=k&m=9)=====>字符串不能用split

### history对象

```
history.go(-2) 后退两页
history.back()
history.forward()
```

### window对象

是浏览器的一个实例，既是访问浏览器窗口的接口，又是Global对象

#### 系统对话框

是同步的，显示这些对话框，代码停止执行，关闭之后代码继续执行

```
//alert
alert('hello')
//confirm 选择，按了确认和取消键进行不同的操作
if(confirm('你确定吗')){
    alert('你点击了确认')
}else{
    alert('你点击了取消')
}
//prompt 提示信息与用户文本输入
var result=prompt('你的名字是什么')
if(result!==null){
    console.log('你好',result)
}
```

### 