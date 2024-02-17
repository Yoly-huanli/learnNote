[toc]

# CSS3新特性

+ 颜色：新增RGBA、HSLA模式
+ 文字阴影(text-shadow)
+ 边框：圆角（border-radius）边框阴影：box-shadow，border-image
+ 背景：background-size设置背景图片的尺寸，background-origin设置背景图片的原点，background-clip设置背景图片的裁剪区域，以“，”分隔可以设置多背景，用于自适应布局

```
background-size：规定背景图片的尺寸，background-size:63px 100px;
background-origin：规定背景图片的定位区域，背景图片可以放置于 content-box、padding-box 或 border-box 区域。background-origin:content-box;
CSS3 允许元素使用多个背景图像。background-image:url(bg_flower.gif),url(bg_flower_2.gif);

```
+ CSS3 @font-face 规则可以自定义字体。
+ 渐变：linear-gradient、radial-gradient
+ 过渡：transition可实现动画，自定义动画
+ 在CSS3中唯一引入的伪元素是::selection
+ 多媒体查询

# 屏幕适配

## 浏览器屏幕适配

版心布局

- 当屏幕大于版心宽度时，版心居中显示
- 当屏幕小于版心宽度时，屏幕出现一个横向的滚动条，这种方案几乎所有的PC端网站都在采用。

```css
 main{
      width: 1200px;
      height: 100%;
      margin: 0 auto;
      font-size: 40px;
      background-color: pink;
    }
```

## 移动端适配

### 视口viewport

移动端有三种类型的 viewport: layoutviewport、visualviewport、idealviewport。具体解释如下：

- layoutviewport: 大于实际屏幕， 元素的宽度继承于 layoutviewport，用于保证网站的外观特性与桌面浏览器一样。layoutviewport 到底多宽，每个浏览器不同。iPhone 的 safari 为 980px，通过 document.documentElement.clientWidth 获取。
- visualviewport: 当前显示在屏幕上的页面，即浏览器可视区域的宽度。
- idealviewport: 为浏览器定义的可完美适配移动端的理想 viewport，固定不变，可以认为是设备视口宽度。比如 iphone 7 为 375px, iphone 7p 为 414px。

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0,maximum-scale=1,minimum-scale=1,user-scalable=no">
```

width 设置的是 layoutviewport 的宽度 initial-scale 设置页面的初始缩放值，并且这个初始缩放值是相对于 idealviewport 缩放的，最终得到的结果不仅会决定 visualviewport，还会影响到 layoutviewport user-scalable 是否允许用户进行缩放的设置,只要 layoutviewport === visualviewport，页面下面不会出现滚动条，默认只是把页面放大或缩小。

- flexible + rem

```
flexible是指把根节点字体改为当前屏幕的当前屏幕的十分之一大小，rem处理字体大小，相对于根节点
```

rem是根元素字体的单位，比如 html{font-size:16px;} ，1rem相当于16px。

使用方法

```
html{font-size:100px;}
body{font-size:14px;}

.menu li{
	display: table-cell;
	padding: .1rem .3rem;/*相当于10px 30px*/
}

//dom操作
//改变font-size
(function(doc,win){
    var docEI = doc.documentElement,
    resizeEvt = 'orientationchange' in window?'orientataionchange':'resize',
    recalc = function(){
        var clientWidth = docEI.clientWidth;
        if(!clientWidth) return;
        //100是字体大小，1536是开发时浏览器窗口的宽度，等比计算
        docEI.style.fontSize = 100*(clientWidth/1536)+'px';
    }

    if(!doc.addEventListener) return;
    win.addEventListener(resizeEvt, recalc, false);
    doc.addEventListener('DOMContentLoaded', recalc, false);
})(document,window);
```

为什么不用百分比,em:相对于父元素，不利于计算 为什么不用viewport等比缩放：缩放过大可能会失真

- 小版心：对最外层容器加了一个最大宽度的设置

```
main{
	max-width:540px;
}
```

## 判断手机横屏还是竖屏

```
window.orientation事件
//判断手机横竖屏状态：
function hengshuping(){
if(window.orientation==180||window.orientation==0){
alert("竖屏状态！")
}
if(window.orientation==90||window.orientation==-90){
alert("横屏状态！")
}
}
window.addEventListener("onorientationchange" in window ? "orientationchange" : "resize", hengshuping, false)
```

## 小程序

rpx

## Vh,vw,px,%,em,rem

+ px是绝对单位
+ %是相对于父元素的宽度比例，可以用来做元素垂直居中
+ <font color="red">em是相对于当前元素的font-size，如果父元素设置了font-size, 并且当前元素继承了父元素的font-size, 这时候em才是相对于父元素的font-size</font>
+ rem是相对于根节点的font-size, 可以通过媒体查询进行响应式配置（@media only screen and (max-width:375px){xxx样式}）
+ vw 100vw 等于视口的宽度 ，vh 100vh 等于 视口的高度，vMax vw 和 vh 中的较大的那个， vMin vw 和 vh 中的较小的那个

## 响应式布局

排版，字体图片大小，侧边栏自动隐藏

### 后端响应式

后台服务器根据前端浏览器的User-Agent来判断来源请求是PC端还是移动端，然后服务器动态返回PC端页面或者移动端页面。nginx中很容易就出现该功能。

### 前端响应式

```
 @media screen and (min-width:800px) {
```

# 1px产生原因

设备像素比，1px在移动端比1px要粗，如果DPR为2，定义了1px，显示为2px

```
window.devicePixelRatio=物理像素 /CSS像素
```

## 1px解决方案

### (1)viewport+rem

```
<script>
  var viewport = document.querySelector("meta[name=viewport]");
  //下面是根据设备像素设置viewport
  if (window.devicePixelRatio == 1) {
      viewport.setAttribute('content', 'width=device-width,initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no');
  }
  if (window.devicePixelRatio == 2) {
      viewport.setAttribute('content', 'width=device-width,initial-scale=0.5, maximum-scale=0.5, minimum-scale=0.5, user-scalable=no');
  }
  if (window.devicePixelRatio == 3) {
      viewport.setAttribute('content', 'width=device-width,initial-scale=0.3333333333333333, maximum-scale=0.3333333333333333, minimum-scale=0.3333333333333333, user-scalable=no');
  }
```

### (2)伪元素

```
div{
   width:200px;
   height:200px;
}
.div1{
   margin-bottom:80px;
   background:red;
   position: relative;
}
.div1::after{
    position: absolute;
    content: '';
    background-color: black;
    display: block;
    width: 100%;
    height: 1px; /*no*/
    transform: scale(1, 0.5);
    bottom: 0;
    left: 0;
}
```

四条都设置

- 先设置一个伪元素，边框为1px
- 高和宽设置为原有元素的两倍
- 按左上角为标准，缩小0.5， transform-origin
- border-box需要设置一下

```
.div1:after{
   content:" ";
   position:absolute;
   top: 0;
   left: 0;
   width: 200%;
   height: 200%;
   transform: scale(0.5);
   transform-origin: left top;
   box-sizing: border-box;
   border: 1px solid black;
   border-radius: 4px;
}
```

### (3)box-shadow实现

https://www.runoob.com/try/try.php?filename=trycss3_box-shadow

```
box-shadow: 0  -1px 1px -1px #e5e5e5,   //上边线
            1px  0  1px -1px #e5e5e5,   //右边线
            0  1px  1px -1px #e5e5e5,   //下边线
            -1px 0  1px -1px #e5e5e5;   //左边线
```

- 前两个定位置
- 阴影半径为1px
- 扩展半径为-1px，可以保证只有上边的边，其他边没有

### (4)border-image

不推荐,颜色，圆角不容易处理

```
  border: 1px solid transparent;
  border-image: url('./../../image/96.jpg') 2 repeat;
```

### (5)直接设置

不推荐

```
border:0.5px solid #E5E5E5
```



# 块级元素与行内元素

## inline和inline-block间隔问题

```HTML
<style>
li{
 display:inline-block;
 width:50px;
 background:red;
}
</style>
<ul>
<li>hhh</li><li>hhh</li>
<li>hhh</li>
<li>hhh</li>
</ul>
```
+ 如li等元素写为同一行

+ 有空格时候会有间隙 解决：移除空格 

+ margin正值的时候 解决：margin使用负值 

  ```
  margin:0px -3px;
  ```

+ 使用font-size时候 解决：font-size:0、letter-spacing、word-spacing
+ float:left

# 属性

## 属性继承

+ 可继承属性

>字体相关：line-height, font-family, font-size, font-style, font-variant, font-weight, font<br/>
>
>文本相关： letter-spacing, text-align, text-indent, text-transform, word-spacing<br/>
>
>列表相关：list-style-image,  list-style-position, list-style-type, list-style<br/>
>
>颜色：color


```
//font-variant
normal 	默认值。浏览器会显示一个标准的字体。
small-caps 	浏览器会显示小型大写字母的字体。
inherit 	规定应该从父元素继承
```

+ 不可继承属性

```
border
width,height
margin,padding
display
opacity
```

# 文字

## 如何使英文单词发生断行

```css
<style>
h1{
  width:50px;
  overflow:hidden;
  white-space:wrap;
  word-wrap: break-word;
}
</style>
<h1>hhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhh</h1>  
```
## 在网页中的应该使用奇数还是偶数的字体？

使用偶数字体。偶数字号相对更容易和 web 设计的其他部分构成比例关系。
Windows 自带的点阵宋体只提供 12、14、16 px 这三个大小的点阵，而 13、15、17 px时用的是小一号的点。（即每个字占的空间大了 1 px，但点阵没变），于是略显稀疏。

## line-height设置margin,padding

```css
<span style="margin:100px;padding:100px"> 点击链接</span>
<p style="margin:0;padding:0">hh</p>
   <span style="margin:100px;line-height:50px"> 点击链接2 </span>
```
+ 对inline元素，width,height不起作用
+ inline元素的padding的left,right,bottom起作用，margin的left,right起作用
+ 添加line-height之后，padding,margin全都起作用

# @import与link

+ @import只能加载css文件，link可以加载多种文件
+ link是页面加载时同步加载，@import是异步加载，页面全部加载完毕后才加载
+ link没有兼容性问题，是XMHTML标签，@import是CSS2.1才提出的，有兼容问题
+ link支持js操作，它是DOM元素，@import不能DOM操作



# 元素不可见

```
display:none/visibility:hidden
width=0/height=0;
left，top为负值
margin为负值
opacity/rgba颜色设置
z-index较小
```
补充

```
position: absolute;
zoom: 0.001;
-moz-transform: scale(0);
-webkit-transform: scale(0);
-o-transform: scale(0);
transform: scale(0); 
```

## 设置透明度的方法与缺点

rgba()只是单纯的可以设置颜色透明度，比如：让背景出现透明效果，但上面的文字不透明。opacity,如果父元素定义了opacity，那么子元素在定义一个opacity，那么子元素的效果其实是两者的乘积…

## display:none、visibility:hidden 和 opacity:0 区别

共同点：隐藏

不同点：

+ 是否占据空间：display：none不占据
+ 是否可继承：display:none不可继承，visibility:hidden的子元素可以设置visibility:visible重新显示，但是opacity:1不可以重新显示
+ 事件绑定：display:none元素不存在，无法触发绑定的事件，visibility:hidden不会触发绑定的事件，opacity:0可以触发绑定的事件，可以点击
+ 过渡动画：transition只对于opacity是有效的

性能：
+ displaynone : 修改元素会造成文档回流,性能消耗较大
+ visibility:hidden: 修改元素只会造成本元素的重绘,性能消耗较少
+ opacity: 0 ： 

```
在一般情况下，opacity 会触发重绘,不管是否开启GPU提升为合成层与否
如果利用 animation 动画，对 opacity 做变化（animation会默认触发GPU加速），则只会触发 GPU 层面的 composite，不会触发重绘。
```

# 层叠规则

样式可能受到多个样式的影响， 层叠规则用于解决样式冲突，确定优先级，获知最终的样式： 

## 优先级

作者样式表（也就是开发自己写的）的!important > 浏览器默认的!important > 作者样式表 > 默认样式表

优先级低的不会起作用

## 同一优先级比较

也就是权重比较

```
(a,b,c,d)
+ a: style样式为1， 否则为0
+ b: 有多少个ID选择器
+ c: 类选择器、属性选择器、伪类选择器有多少个
+ d: 多少个元素选择器和伪元素选择器
vscode有提示，然后进行从高位到低位的比较
```

## 优先级与同级完全一致比较

按照样式在源代码出现的顺序决定， 靠后的覆盖靠前的

## 伪类与伪元素
伪类选择器

```
a:hover
:checked
:nth-child(an+b)
```
伪元素选择器

伪元素选择器选中的并不是真实的 DOM 元素，所以叫伪元素选择器。伪元素选择器常用的也就下面 5 个：
+ ::first-line，为某个元素的第一行文字使用样式。
+ ::first-letter，为某个元素中的文字的首字母或第一个字使用样式。
+ ::before，在某个元素之前插入一些内容。
+ ::after，在某个元素之后插入一些内容。
+ ::selection，对光标选中的元素添加样式。

> 伪元素选择器构造的元素是虚拟的，所以不能用 JS 去操作它。
>
> 2.如果同时使用了 before 和 first-letter 两个伪类，第一个字是要从 before 里的内容开始算起的，如果 before里面的内容是一个非文本元素，那 first-letter 也会作用在这个非文本元素上，但是不一定能生效。
>
> 3.first-line 和 first-letter 不适用于内联元素，在内联元素中这两个选择器都会失效。
>
> 4.在 CSS3 中，规定了伪类用一个冒号（:）表示，伪元素用两个冒号表示（::）。但除了 selection，其余四个伪元素选择器已经在 CSS2 中存在且和伪类用的是一样的单冒号表示的。为了向下兼容，现在的浏览器中伪元素选择器用单冒号和双冒号都可以。

# 文字

## text-shadow属性的四个值分别是什么？
text-shadow: h-shadow v-shadow blur color;
+ 必需,水平阴影的位置,允许负值;  
+ 必需,垂直阴影的位置,允许负值;  
+ 可选,模糊的距离;  
+ 可选,阴影的颜色;

## 省略号

```css
 <style>
h1{
  width:100px;
  overflow:hidden;
  white-space:nowrap;
  text-overflow:ellipsis;
}
</style>
<h1>哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈</h1>      
```





# border-shadow

```
box-shadow: 12px 12px 2px 1px rgba(0, 0, 255, .2);
//左，上，模糊半径，扩展半径，颜色
```
# display及相关属性

```
none
flex/table
block/inline-block/inline
inherit
list-item
```

# 不改变原有内容修改宽度

```js
已知如下代码，如何修改才能让图片宽度为 300px ？注意下面代码不可修改。

<img src="1.jpg" style="width:480px!important;”>
```
+ border-box
+ transform缩方，注意不能写300/480，要写计算结果
+ zoom
+ max-width覆盖

```html
<img src="1.jpg" style='width:480px!important;box-sizing:border-box;padding:0 90px;'/>
<img src="1.jpg" style='width:480px!important;transform: scale(0.625)'/>
<img src="1.jpg" style='width:480px!important;max-width:300px'/>
<img src="1.jpg" style='width:480px!important;zoom:0.625;'/> 
```
Zoom

```
zoom：normal | <number> | <percentage>
缩放
```