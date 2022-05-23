[toc]

学习文档：https://www.tslang.cn/docs/handbook/generics.html

# 介绍

typescript的重点在于type，表示类型，会进行强制类型的限制，有这样一些好处：

+ 可以完全兼容js，基本没有迁移成本
+ 节约了重复开发的工作，因为之前写函数或者其他部分，需要手动进行变量类型判断

```
function(a,b){
  if(typeof a === 'String'){
     console.log('a是字符转')
  }
}
```

+ 可以在开发编译阶段就进行类型检查与报错，而不是像js一样到代码实际运行的时候才会报错

# 基本使用

+ 安装

  ```
  sudo npm install -g typescript
  ```

+ 新建项目

  ```
  npm init 
  ```

+ 编写ts文件:app.ts

  ```
  let a:string= 'hello'
  console.log('a', a)
  ```

+ 编译运行

  ```
  tsc app.ts==>生成app.js文件
  node app.js
  //如果安装失败，可以全局安装一次
  sudo npm install typescript -g 
  ```

# 基本数据类型

包括number、string、boolean、number、array、tuple、enum、null、undefined、object、void、never、any

如果定义的时候没有指定，ts会根据上下文自动进行判断并指定类型

## number

和js的number基本一致，可以表示所有的数字

## string

和js的string基本一致，可以是普通字符串，也可以是模板字符串

## boolean

和js的boolean一致

## array

数组类型

+ 定义方法

  ```
  let a:number[] = [1,2,3]
  或者
  let b:Array<number>=[4,5,6]
  ```

## tuple

有固定长度与固定类型的数组



# 高级数据类型

包括union组合类型，nullable可空类型、literal预定义类型等



# 泛型

```js
function identity<T>(arg: T): T {
    return arg;
}
或者：let myIdentity: <T>(arg: T) => T = identity;
```

限制输入和输出的类型必须是一致的，identity<T>这里是捕获输入的类型

# 注释

// @ts-ignore



# 遇到的问题

## 编译问题

```
Accessors are only available when targeting ECMAScript 5 and higher.
```

解决

```
tsc  array-rotate.ts -t es5
```



