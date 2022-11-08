[toc]

# 1.通过spawn 同时启动多个vue项目

背景说明：微前端想同时把子应用启动起来

步骤：

+ 新建build/run.js
+ 编写

```js
const spawn = require('child_process').spawn;
const path = require('path')

const filePath = {
  learnts: path.join(__dirname, '../learnts'),
  learnjs: path.join(__dirname, '../learnjs'),
}
 
function runChild(){
  Object.values(filePath).forEach(item=>{
  spawn(`cd ${item} && npm start`, {
   stdio: 'inherit',
   shell: true
  });
})
}
runChild()
```

+ 在build下运行 node run.js



spawn模块的原理

+ node是单进程，child_process的spawn创建子进程
+ 子进程调用spawn
+ spawn接收两个参数，第一个参数是command命令，第二个参数是options配置
+ stdio: 'inherit' 的配置选项，当我们执行代码的时候，子进程将会继承主进程的 stdin，stdout，stderr。这回导致子进程数据事件处理器在主进程的 process.stdout 流中被触发，让脚本有正确的输出方式。 shell: true 的配置选项，我们可以在传递的命令中使用 shell 语法