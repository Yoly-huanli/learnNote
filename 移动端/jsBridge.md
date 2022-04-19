[toc]

# 什么是JS-bridge

目前开发移动端技术选型上基本都是混合开发（Hybrid）,使用多种开发模型开发App，通常会涉及到两大类技术：原生Native、Web H5,但是H5无法直接调用native API，需要通过JS-Bridge来调用native的能力

Native具有的一些能力：比如二维码，支付，拍照等功能只通过web本身无法调用

在移动端，通常会在其操作系统上安装APP，APP本身可能有webview浏览器来展示h5页面

使得webview里的h5页面可以与APP之间进行通信，就需要用到jsbridge

# 如何实现JS-bridge

+ 注册全局API方式

  比如APP可以把一些方法挂载在window上,然后h5去调用，但是如果APP的这个方法需要网络请求，那么h5就得必须去等待APP去请求之后获得的结果

  ```
  const version = window.getVersion()
  ```

+ URL Schema方式

  ```
  以微信为例,"weixin://dl/scan"就可以使用微信的扫一扫功能，具体到实现上就类似：
  <iframe id="test"></iframe>
  ...
  const iframeTest = document.getElementById('test')
  iframeTest.onload=()=>{
      const content = iframeTest.contentWindow.document.body.innerHTML
      console.info('content===',content)
    }
  iframeTest.src = 'weixin://dl/scan'
  
  ```

  实现原理：

  + h5内嵌了iframe，要获取iframe的内嵌的内容，需要发起网络请求，但是h5是在APP内的，首先要经过APP的中间层进行中转，请求的路径是 h5-->APP-->网络
  + 可以不用http协议，用自定义的协议，APP可以拦截到h5发出去的请求，识别出约定的协议，进行自己的功能并返回

  进一步封装,核心是创建隐藏的iframe,是的APP层可以做一次拦截

  ```js
  const SDK ={
      invoke(url,data={},onSuccess,onError){
        const iframe = document.createElement('iframe')
        iframe.style.visibility = 'hidden'
        document.body.appendChild(iframe)
        iframe.onload = () => {
          const content = iframeTest.contentWindow.document.body.innerHTML
          onSuccess(JSON.parse(content)) //将iframe的内容向外传递给onSuccess函数
          iframe.remove()
        }
        iframe.onerror = () => {
          onError() // 加载失败，执行外部指定的错误回调函数
          iframe.remove()
        }
        iframe.src=`winxin://${url}?data=${JSON.stringify(data)}`
      }
    }
  ```

  
