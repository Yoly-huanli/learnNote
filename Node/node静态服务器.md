[toc]

### 1.介绍

在任意目录下输入特定命令，启动服务，可以打开浏览器url，显示此url对应的目录，本质上是http请求，客户端发起请求，服务端响应

### 2.例子

##### 写服务器

```js
const http = require('http');
const conf = require('./config/defaultConfig.js');

const server = http.createServer((req,res)=>{
   res.statusCode = 200;
   res.setHeader('Content-Type', 'text/plain');
   res.end('Hello HTTP');
});

server.listen(conf.port,conf.hostname, () => {
	const addr = `http://${conf.hostname}:${conf.port}`;
	console.info(`server started at ${addr}`)
})
module.exports = {
	hostname:'127.0.0.1',
	port:'9527'
}
```

引入http模块，用createServer创建服务器，req表示请求，res表示回复，listen监听主机与端口。
整体流程：node A.js启动服务器 ---> 浏览器输入http://127.0.0.1:9527/发起请求 ---> 服务器监听端口，发现有请求，给出回复，回复的状态码为200，文件类型为text，内容为hello HTTP

```
$ node A.js
server started at http://127.0.0.1:9527
```

##### 另一个例子，html

```js
const http = require('http');
const conf = require('./config/defaultConfig.js');

const server = http.createServer((req,res)=>{
   res.statusCode = 200;
   res.setHeader('Content-Type', 'text/html');
   res.write('<html>');
   res.write('<body>');
   res.write('Hello world');
   res.write('</body>');
   res.write('</html>');
   res.end();
});

server.listen(conf.port,conf.hostname, () => {
	const addr = `http://${conf.hostname}:${conf.port}`;
	console.info(`server started at ${addr}`)
})
```

注意：

- Content-Type需要改为text/html
- 结尾必须有res.end()

### 3. 自动重启服务工具supervisor

- 安装 ：$ npm i -g supervisor
- 使用： supervisor A.js

文件改变后不用重启node A.js,直接在网页刷新即可，其原理是watch文件夹，检测到改变自动启动命令node A.js

### 4.获取用户工作目录

```js
const path = require('path');
...
const filePath = path.join(conf.host, req.url);
  host:process.cwd(),
```

- process.cwd表示当前执行node语句所在目录
- req.url是用户浏览器输入，请求的路径

浏览器输入http://127.0.0.1:9527/e.b.js，得到。。。

```
E:\宸ヤ綔鍐查腑\nodejs\static_manager\static_manager\src\e.b.js
```

### 5.判断是不是文件夹

根据本地所在目录与浏览器输入拼接而成新的url，判断这个url对应的是一个文件还是一个文件夹，是文件，则显示出来，是文件夹，则显示它当中的文件列表

```js
const http = require('http');
const path = require('path');
const fs = require('fs');
const conf = require('./config/defaultConfig.js');

const server = http.createServer((req,res)=>{
   const filePath = path.join(conf.host, req.url);
   fs.stat(filePath,(err,stats)=>{
     if(err){
       res.statusCode = 404;
       res.setHeader('Content-Type', 'text/plain');
       res.end(`${filePath} is not a directory or file`);
       return;
     }
     if(stats.isFile()){
       res.statusCode = 200;
       res.setHeader('Content-Type', 'text/plain');
       fs.createReadStream(filePath).pipe(res);
     } else if(stats.isDirectory()){
       fs.readdir(filePath,(err,files)=>{
           res.statusCode = 200;
           res.setHeader('Content-Type', 'text/plain');
           res.end(files.join(','))
       });
     }

   })
});

server.listen(conf.port,conf.hostname, () => {
  const addr = `http://${conf.hostname}:${conf.port}`;
  console.info(`server started at ${addr}`)
})
supervisor app.js
http://127.0.0.1:9527/config/defaultConfig.js
module.exports = {
  host:process.cwd(),
  hostname:'127.0.0.1',
  port:'9527'
}
```

### 6.将回调改造为async写法

新建文件夹helper,新建文件route.js

```js
const fs = require('fs');
const promisify=require('util').promisify;

const stat = promisify(fs.stat);
const readdir = promisify(fs.readdir);

module.exports = async function(req, res, filePath){
  try{
    const stats = await stat(filePath);
    if(stats.isFile()){
       res.statusCode = 200;
       res.setHeader('Content-Type', 'text/plain');
       fs.createReadStream(filePath).pipe(res);
     } else if(stats.isDirectory()){
         const files = await readdir(filePath);
         res.statusCode = 200;
         res.setHeader('Content-Type', 'text/plain');
         res.end(files.join(','));
       }
  } catch (ex) {
    res.statusCode = 404;
    res.setHeader('Content-Type', 'text/plain');
    res.end(`${filePath} is not a directory or file`);
  }
}
```

在app.js

```js
const http = require('http');
const path = require('path');
const fs = require('fs');
const conf = require('./config/defaultConfig.js');
const route = require('./helper/route');

const server = http.createServer((req,res)=>{
   const filePath = path.join(conf.host, req.url);
   route(req, res, filePath);
});

server.listen(conf.port,conf.hostname, () => {
  const addr = `http://${conf.hostname}:${conf.port}`;
  console.info(`server started at ${addr}`)
})
```

### 7.将读出来的文件夹里文件名改为a标签的形式，使得点击文件名可以跳转

##### 模板字符串handerbars

- 安装 ：npm i handlebars
- 新建文件夹template-- 新建文件dir.tpl

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>{{title}}</title>
</head>
<body>
  {{#each files}}
    <a href="{{../dir}}/{{this}}">{{this}}</a>
  {{/each}}
</body>
</html>
```

- this是当前文件名，dir是当前文件夹相对地址，但是这个模板字符串默认是当前文件的位置，故../dir

##### tips

- 路径相关，最好使用绝对路径，除非包引用等，因为不确定在那个文件夹下打开
- __dirname是当前所在的物理磁盘位置,route所在位置再拼接相对路径
- readFileSync默认读出来是buffer
- title: path.basename(filePath),title设为当前文件夹的名字，传进来的全路径的basename
- path.resolve(config.root,filePath);取出相对于一个路径的相对路径
- text/html

```js
const fs = require('fs');
const path = require('path');
const Handlebars = require('handlebars');
const promisify=require('util').promisify;
const conf = require('../config/defaultConfig.js');
const stat = promisify(fs.stat);
const readdir = promisify(fs.readdir);
const tplPath = path.join(__dirname,'../template/dir.tpl');
const source = fs.readFileSync(tplPath);
const template = Handlebars.compile(source.toString());

module.exports = async function(req, res, filePath){
  try{
    const stats = await stat(filePath);
    if(stats.isFile()){
       res.statusCode = 200;
       res.setHeader('Content-Type', 'text/plain');
       fs.createReadStream(filePath).pipe(res);
     } else if(stats.isDirectory()){
         const files = await readdir(filePath);
         res.statusCode = 200;
         res.setHeader('Content-Type', 'text/html');
         const data = {
           title: path.basename(filePath),
           dir: path.resolve(conf.root, filePath),
           files
         };
         res.end(template(data));
       }
  } catch (ex) {
    res.statusCode = 404;
    res.setHeader('Content-Type', 'text/plain');
    res.end(`${filePath} is not a directory or file${ex}`);
  }
}
```

------

- process.cwd() 是当前执行node命令时候的文件夹地址 ——工作目录，保证了文件在不同的目录下执行时，路径始终不变，比如再src目录下supervisor app.js，那么process.cwd()始终为E:\hash\nodejs\static_manager\static_manager\src，在static_manager下supervisor src/app.js,始终输出E:\hash\nodejs\static_manager\static_manager
- req.url为浏览器里自己输入的路径，const filePath = path.join(conf.host, req.url);拼接为文件所在的绝对路径
- __dirname当前执行的js文件所在的绝对的完整路径
- const tplPath = path.join(__dirname,'../template/dir.tpl');获得tplPath的绝对路径
- path.basename(filePath)获取文件名
- const dir = path.relative(conf.host, filePath);获取B路径相对于A路径的相对路径
- path.extname(filePath).split('.').pop().toLowerCase();获取文件名后缀，小写

##### 在模板引擎里改样式

```
  body{
      margin:30px;
   }
   a{
      display:block;
      font-size:30px;
   }
</style>
```

supervisor无法检测tpl文件的变化，需要重启服务

### 8. MIME

根据文件类型改变MIME，使得可以识别不同的文件，进行不同的解析方法

- 新建 helper--> mime.js

```
const path = require('path');
const mimeTypes = {
  'css': 'text/css',
  'gif': 'image/gif',
  'html': 'text/html',
  'js': 'text/javascripts',
  'png':'image/png',
  'txt': 'text/plain'
}

module.exports = (filePath) => {
  let ext = path.extname(filePath).split('.').pop().toLowerCase();
  if (! ext) {
    ext = filePath
  }
  return mimeTypes[ext] || mimeTypes['txt'];
}
```

输入路径，获取文件后缀，返回对应MIME类型

```
 const contentType= mime(filePath);
 res.setHeader('Content-Type', contentType);
```

在router.js里引用，如果是文件，那么获取相应mime

##### 文件夹也可以处理一下

```
 <a href="{{../dir}}/{{file}}">【{{icon}}】{{file}}</a>
const data = {
           title: path.basename(filePath),
           dir: dir?`/${dir}`: '',
           files: files.map(file=>{
              return {
                file,
                icon: mime(file)
              }
           })
         };
```

显示效果：

```
【text/plain】.editorconfig
【text/plain】.eslintignore
【text/javascripts】.eslintrc.js
.....
```

### 9.压缩文件

新建helper下compress.js

```
const {createGzip,createDeflate} = require('zlib');
module.exports = (rs,req,res) =>{
  const acceptEncoding = req.headers['accept-encoding'];
  // 如果请求里没有带压缩，或者不是gzip和deflate，直接把文件返回，不压缩
  if(!acceptEncoding || !acceptEncoding.match(/\b(gzip|deflate)\b/)){
    return rs
  } else if(acceptEncoding.match(/\b(gzip)\b/)){
    res.setHeader('Content-Encoding','gzip');
    return rs.pipe(createGzip());
    //请求用gizp压缩，响应res压缩并在头部说明
  } else if(acceptEncoding.match(/\b(deflate)\b/)){
    res.setHeader('Content-Encoding','deflate');
    return rs.pipe(createDeflate());
  }
}
```

在helper的route.js

```
let rs = fs.createReadStream(filePath);
       if (filePath.match(conf.compress)){
         rs = compress(rs,req,res);
       }
rs.pipe(res);
```

在defaultConfig.js

```
compress:/\.(html|css|js)/
```

### 10 部分请求

- range.js

```
module.exports=(totalSize,req,res)=>{
  const range=req.headers['range'];
  if(!range){
    return {code:200};
  }
  //一般是5001-10000这种形式，需要正则取出
  const sizes = range.match(/bytes=(\d*)-(\d*)/);
  const end = sizes[2] || totalSize - 1;//正则匹配三个结果，在数组里
  const start = sizes[1] || totalSize - end;
  // 无法处理的情况
  if(start > end || start < 0 || end > totalSize){
    return {code:200};
  }
  //可以处理
  res.setHeader('Accept-Ranges','bytes');
  res.setHeader('Content-Range',`bytes $(start)-$(end)/$(totalSize)`)
  res.setHeader('Content-Length',end-start);
  return {
    code:206,
    start: parseInt(start),// 转成数字
    end: parseInt(end)
  }

}
```

在route.js

```
//部分读取的判断
       const {code, start, end} = range(stats.size,req,res);
       if(code === 200){
          res.statusCode = 200;
          rs = fs.createReadStream(filePath);
       } else {
          res.statusCode = 206;
          rs = fs.createReadStream(filePath,{start,end});
       }
```

### 11.缓存

在config里设置支持的缓存类型

```
 cache: {
    expires:true,
    cacheControl:true,
    maxAge:600,
    lastModified:true,
    etag:true
  }
```

几种状态

- Status Code: 200 OK (from disk cache),直接浏览器输入url再次打开，显示结果
- Status Code: 304 Not Modified当前页面刷新，向浏览器校验结果

cache.js

```
const {cache} = require('../config/defaultConfig.js');
function refreshRes(stats,res){
  const {expires,cacheControl,maxAge,lastModified,etag} = cache;
  //判断是否支持某种判断缓存形式，并设置响应
  if(expires){
    //设置为当前时间加十分钟
     res.setHeader('Expires',(new Date(Date.now()+maxAge)).toUTCString());
  }
  if(cacheControl){
    res.setHeader('Cache-Control',`public,max-age=${maxAge}`);
  }
  if(lastModified){
    res.setHeader('Last-Modified',stats.mtime.toUTCString());
  }
  if(etag){
    res.setHeader('Etag',`${stats.size}-${stats.mtime}`)
  }

}

module.exports=function isFresh(stats,req,res){
   refreshRes(stats,res);//设置respons
   //读请求
   const lastModified = req.headers['if-modified-since'];
   const etag=req.headers['if-none-match'];
   //两种信息都没有，是第一次请求
   if(!lastModified && ! etag){
      return false;
   }
   //如果存在lastModified且和设置的lastModified不一样，表示不能使用缓存
   if(lastModified && lastModified !== res.getHeader('Last-Modified')){
     return false;
   }
   if(etag && etag !== res.getHeader('Etag')){
     return false;
   }
   return true;
};
```

在route.js,先判断是否使用缓存，再判断是否部分读取

```
   //判断是否使用缓存
   if(isFresh(stats,req,res)){
     res.statusCode = 304;
     res.end();
     return;
   }

   let rs;
   //部分读取的判断
```

### 12.命令行工具

实现效果：自定义命令行语句

```
#### 安装教程
npm i -g cutedoor

#### 使用说明
1. cutedoor # 把当前文件夹作为静态资源管理器根目录
2. cutedoor -p 8080 # 自定义端口号
3. cutedoor -h localhost # 设置主机
4. cutedoor -d/usr # 设置根目录为/usr
```

- 安装 npm install yargs --save
- 作用：读取命令行

app.js：将默认与命令行输入的融合,封装为class,conf改为现在的this.conf,将route中涉及使用默认配置的改为从app传入配置

```
const http = require('http');
const path = require('path');
const fs = require('fs');
const conf = require('./config/defaultConfig.js');
const route = require('./helper/route');

class Server{
  constructor(config){
    this.conf = Object.assign({},conf,config);
  }

  start(){
     const server = http.createServer((req,res)=>{
     const filePath = path.join(this.conf.host, req.url);
     route(req, res, filePath, this.conf);
     });

     server.listen(this.conf.port,this.conf.hostname, () => {
     const addr = `http://${this.conf.hostname}:${this.conf.port}`;
     console.info(`server started at ${addr}`)
    })
  }
}

module.exports=Server;
module.exports = async function(req, res, filePath,conf){
```

新建index.js，设置命令，启动服务

```
const yargs = require('yargs');
const Server = require('./app');

const argv = yargs
   .usage('cutedoor [options]')
   .option('p',{
    alias:'port',
    describe:'端口号',
    default:9527
   })
   .option('h',{
    alias:'hostname',
    describe:'主机名字',
    default:'127.0.0.1'
   })
   .option('d',{
    alias:'host',
    describe:'根目录',
    default:process.cwd()
   })
   .version()
   .alias('v','version')
   .help()
   .argv;

const server = new Server(argv);
server.start();
```

- 使用node命令行设置端口号

```
$ node index.js -p 9999
server started at http://127.0.0.1:9999
```

### 13.改写为curl工具

package.json

```
"bin":{
    "cutedoor":"bin/cutedoor"
 },
```

bin当全局安装npm包的时候，会自动在系统的path里注册的几个命令，可以直接写为src/index.js,但是一般不这么写，因此新建文件夹bin，新建cutedoor.js

为bin/cutedoor增加执行权限，

```
$ chmod +x bin/cutedoor.js
```

执行

```
$ bin/cutedoor.js -p 9999
server started at http://127.0.0.1:9999
```

### 14.自动打开网页

新建open.js

```
//系统的自动命令
const {exec} = require('child_process');

module.exports=url=>{
  //判断当前的平台，使用不同的系统命令打开
  switch(process.platform){
    case 'darwin':
      exec(`open ${url}`);
      break;
    case 'win32':
      exec(`start ${url}`);
  }
}
```

在app.js

```
const openUrl = require('./helper/open');
...
server.listen(this.conf.port,this.conf.hostname, () => {
     const addr = `http://${this.conf.hostname}:${this.conf.port}`;
     console.info(`server started at ${addr}`);
     openUrl(addr);
```

### 15 npm发布到全局，别人可以使用

- npm 官方注册账号
- npm login
- npm publish发布
- npm -i -g cutedoor安装到全局

（github用于托管代码，npm帮助我们使用）