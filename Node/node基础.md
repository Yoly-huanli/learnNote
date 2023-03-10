[toc]

Node 是一个基于 V8 引擎的 Javascript 运行环境，它使得 Javascript 可以运行在服务端，直接与操作系统进行交互，与文件控制、网络交互、进程控制等

# 路径相关

## 格式规范化path.normalize

```js
const {normalize}=require('path');
//const normalize=require(path).normalize
console.log(normalize('/user//my/../kk'))
========
$ node A.js
\user\kk
```

## 拼接格式化路径path.join

```js
const {join}=require('path')
console.log(join('/use','hello','hello/big','../big','small'))
=======
 $ node A.js
\use\hello\hello\big\small
```

## 解析为绝对路径path.resolve()

把相对路径解析为绝对路径

```js
const {resolve}=require('path')
console.log(resolve('./'))
========
$ node A.js
C:\Users\Lenovo\Desktop\nodejsss
```

## name相关

- path.basename():获取文件名
- path.dirname():文件所在文件夹
- path.extname():文件拓展名

```js
const {basename,dirname,extname}=require('path')
console.log(basename('user/nn/hh.js'))
console.log(dirname('user/nn/hh.js'))
console.log(extname('user/nn/hh.js'))
=========
$ node A.js
hh.js
user/nn
.js
```

## 路径拆分path.parse()path.format()

- path.parse():将路径拆分成根目录，文件目录，文件名，拓展名
- path.format():相反操作，进行拼接，且当文件目录与根目录冲突的时候，优先根目录，文件名与拓展名冲突的时候，优先文件名

```js
const {parse,format}=require('path')
console.log(parse('user/nn/hh.js'))
console.log(format(
{ root: '/nn', dir: 'user/nn', base: 'hh.js', ext: '.js', name: 'hh' }
	))
==========
$ node A.js
{ root: '', dir: 'user/nn', base: 'hh.js', ext: '.js', name: 'hh' }
user/nn/\hh.js
```

## 分隔符

- path.sep():路径的分隔符
- path.delimiter()：多个路径之间的分隔符
- path.win32.xx:看windows下的分隔符
- path.posix.xx

```JS
const {sep,
delimiter,
win32,
posix
}=require('path');

console.log('sep',sep)
console.log('path',process.env.PATH)
console.log('delimiter',delimiter)
console.log('win32',win32.sep)
```

```
$ node A.js
sep \
path C:\Users\Lenovo\bin;D:\git\Git\mingw64\bin;D:\git\Git\usr\local\bin;D:\git\Git\usr\bin;D:\git\Git\usr\bin;D:\git\Git\mingw64\bin;D:\git\Git\usr\bin;C:\Users\Lenovo\bin;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0;C:\Windows\System32\OpenSSH;D:\git\Git\cmd;D:\node.js;D:\matlab\runtime\win64;D:\matlab\bin;C:\Users\Lenovo\AppData\Local\Microsoft\WindowsApps;C:\Users\Lenovo\AppData\Roaming\npm;D:\git\Git\usr\bin\vendor_perl;D:\git\Git\usr\bin\core_perl
delimiter ;
win32 \
```

## 其他

+ dirname和_filename永远返回当前文件在物理磁盘上的绝对路径

- process.cwd():返回node命令程序执行的路径，表示在哪启动的脚本
- path.resolve('./'):在require中调用，为当前所在文件夹，与_dirname类似

# fs文件系统

## 介绍

所有文件系统操作都具有同步和异步的形式。

异步的形式总是将完成回调作为其最后一个参数。 传给完成回调的参数取决于具体方法，但第一个参数始终预留用于异常。 如果操作成功完成，则第一个参数将为 null 或 undefined。

## 同步与异步方法读文件readFile/readFileSync

fs.readFile/fs.writeFile

同步会在异步回调之前执行，对单个用户来说，同步与异步没有特别大的差别，但是在web场景下，高并发，同步会导致进程堵塞，服务员必须等待大厨完毕再接受新的请求

+ 读文件

```js
const fs=require('fs');
fs.readFile('./A.js','utf8',(err,data)=>{
  if(err) throw err;
  console.log(data)
})

const sync=fs.readFileSync('./B.js','utf8');
console.log(sync)
```

utf8指定编码方式，否则读出来是Buffer的二进制形式，也可以用buf.toString()

+ 写文件(路径，内容（也可以是Buffer）,回调)

```js
const fs=require('fs');
fs.writeFile('./test','this is a text',{
	encoding:'utf8'
},(err)=>{
    if(err) throw err
})
```

## 读取文件状态fs.stat

fs.stat()方法用于返回有关给定文件或目录的信息。它返回一个fs.Stat对象，该对象具有几个属性和方法来获取有关文件或目录的详细信息。

+ 检查文件是否存在,是否是文件夹

  ```js
  const fs=require('fs');
  fs.stat('./A.js',(err,stats)=>{
  	if(err){
  		console.log(err);
  		return
  	};
  	console.log(stats.isFile())//是否文件
  	console.log(stats.isDirectory())//是否文件夹
  })
  ```

## 修改文件名fs.rename

```js
const fs=require('fs');
fs.rename('./test','./teet.txt',err=>{
	if(err) throw err
})
```

## 文件夹操作

+ fs.readdir()

```js
const fs=require('fs');
fs.readdir('./',(err,file)=>{
	console.log(file)
})

$ node A.js
[ 'A.js', 'B.js', 'C.js', 'node_modules', 'package-lock.json' ]
```

- fs.mkdir():创建文件夹
- fs.rmdir():删除文件夹

## 监视变化

```js
const fs=require('fs');
fs.watch('./',{
	recursive:true //是否监视其内的子文件
},(eventType,filename)=>{
	//打印改变的类型，改变的文件
	console.log(eventType)
	console.log(filename)
})
```

```
$ node A.js
rename
新建文本文档.txt
change
新建文本文档.txt
rename
新建文本文档.txt
rename
2.txt
```

## 文件流

介绍：不是直接保存在内存中，而是需要一点用一点，写一点放一点

+ createReadStream

  ```js
  const fs=require('fs');
  //创建流
  const rs=fs.createReadStream('./A.js');
  //流导向，输出到控制台
  rs.pipe(process.stdout)
  ```

+ 以流的方式写入

  ```js
  const fs=require('fs');
  //创建流
  const ws=fs.createWriteStream('./test.txt');
  const tid=setInterval(()=>{
  	const num=parseInt(Math.random()*10)//生成10以内的随机数
  	console.log(num)
  	if(num<8){
  		ws.write(num+'')//随机数小于8则写入
  	}else{
  		clearInterval(tid);//否则结束，触发finish事件
  		ws.end();
  	}
  },200)
  
  ws.on('finish',()=>{
  	console.log('done')
  })
  ```

# 回调地狱

介绍：回调嵌套，可读性差

解决方案：

promise化：require('util').promisify

```js
const fs=require('fs');
//引入包
const promisify=require('util').promisify;
//创建promise对象
//then成功之后的操作，catch捕捉异常
const read=promisify(fs.readFile);
read('./A.js').then(data=>{
	console.log(data.toString())
}).catch(ex=>{console.log(ex)})
```

```js
const fs=require('fs');
//引入包
const promisify=require('util').promisify;
//创建promise对象
//then成功之后的操作，catch捕捉异常
const read=promisify(fs.readFile);

async function test(){
	try{
		const content= await read('./A.js');
		console.log(content.toString())
	}catch(ex){
       console.log(ex)
	}
}
test()
```

# Buffer

- 用于处理二进制数据流
- 类似于整数数组，但是大小在创立时确定，不能更改
- C++代码在V8堆外分配物理内存
- Buffer类在全局变量中，不用require('buffer').Butter

```js
console.log(Buffer.alloc(5))//创建一个长度为 5、且用零填充的 Buffer。
console.log(Buffer.alloc(5,1))//创建一个长度为 5、且用1填充的 Buffer。
console.log(Buffer.allocUnsafe(5,1))//创建一个长度为 10、随机填充的 Buffer。
console.log(Buffer.from(['1','2']))//创建指定内容的Buffer
console.log(Buffer.from('test'))//utf8（默认编码）的字符串Buffer
===========
 $ node A.js
<Buffer 00 00 00 00 00>
<Buffer 01 01 01 01 01>
<Buffer 00 00 00 00 00>
<Buffer 01 02>
<Buffer 74 65 73 74>
```

## 静态方法

- Buffer.byteLength():buffer所占字节数，其中每个中文3个字节
- Buffer.isBuffer():是否buffer对象
- Buffer.concat()：拼接Buffer,接收一个参数

```js
console.log(Buffer.byteLength('我'));
console.log(Buffer.isBuffer({}));
const btf1=Buffer.from('my');
const btf2=Buffer.from('name');
const btf3=Buffer.from('is');
console.log(Buffer.concat([btf1,btf2,btf3]).toString())
=========
$ node A.js
3
false
mynameis
```

## 实例类

const buf=Buffer.from('test')

- buf.length:申请空间的大小，用from一般与内容长度相同
- buf.toString():转换为字符串，默认为utf8，也可以自己指定
- buf.fill():allocUnsafe申请的空间填充自定义内容
- buf.equals():Buffer的内容是否相同
- buf.indexOf():类似数组，有内容返回下标，没有返回-1
- buf.copy():复制

```js
const buf1=Buffer.from('its a test');
const buf2=Buffer.allocUnsafe(10);
console.log(buf1.toString('base64'))
console.log(buf1.length)
console.log(buf2.fill(10,2,6))//从buf2的第三项到第七项填充10
console.log(buf2.equals(buf1))
console.log(buf1.indexOf('a'))

buf1.copy(buf2,2,4,6);//将buf1中4<=xx<6的内容复制到buf2第三个开始的位置
console.log(buf2.toString())

=============
$ node A.js
aXRzIGEgdGVzdA==
10
<Buffer 70 4a 0a 0a 0a 0a 00 00 20 4b>
false
4
pJa

   K
```



## 中文乱码

```js
const buf=Buffer.from('我是中国人！');
console.log(buf.length)
for(let i=1;i<buf.length;i+=5){
	const buf2=Buffer.allocUnsafe(5);
	buf.copy(buf2,0,i);
	console.log(buf2.toString())
}
==========
$ node A.js
18
��是
中�
�人�
��r��
```

 解决方案：string_decoder

```js
const { StringDecoder } = require('string_decoder');
const decoder = new StringDecoder('utf8');
const buf=Buffer.from('我是中国人！');
console.log(buf.length)
for(let i=1;i<buf.length;i+=5){
	const buf2=Buffer.allocUnsafe(5);
	buf.copy(buf2,0,i);
	console.log(decoder.write(buf2))
}
============
18
��是
中
国人
！
```

# events事件

介绍

能够触发事件的对象都是EventEmitter 的实例，都是继承自EventEmitter类，eventEmitter.on() 用于绑定事件， eventEmitter.emit() 用于触发事件。（在js中是由用户操作，比如点击，浏览器触发）

```js
const EventEmitter=require('events');
class myEventEmitter extends EventEmitter {

}
//实例化
const mytest=new EventEmitter();
//绑定事件
mytest.on('testfun',()=>{
	console.log('hello')
})
//触发事件
mytest.emit('testfun')
```

触发时传值

```js
const EventEmitter=require('events');
class myEventEmitter extends EventEmitter {

}
//实例化
const mytest=new EventEmitter();
//绑定事件
mytest.on('testfun',(err,nowtime)=>{
	console.log(err);
	console.log(nowtime)
})
//触发事件,醋无触发以及当前时间的传递
mytest.emit('testfun',new Error('oops'),Date.now())
```

触发多次只执行一次

```js
const EventEmitter=require('events');
class myEventEmitter extends EventEmitter {

}
//实例化
const mytest=new EventEmitter();
//绑定事件
mytest.once('testfun',()=>{
	console.log('once')
})
setInterval(()=>{
	mytest.emit('testfun')
},500)


```

移除方法

- xx.removeListener('testfun',fn1)
- xx.removeAllListeners('testfun')\

```js
const EventEmitter=require('events');
class myEventEmitter extends EventEmitter {

}
//实例化
const mytest=new EventEmitter();
function fn1(){
	console.log('fn1')
}
function fn2(){
	console.log('fn2')
}
//绑定两个事件
mytest.on('testfun',fn1)
mytest.on('testfun',fn2)
//每500ms触发一次
setInterval(()=>{
	mytest.emit('testfun')
},500)
//1500ms后移除事件fn1
setTimeout(()=>{
	mytest.removeListener('testfun',fn1)
},1500)

```

