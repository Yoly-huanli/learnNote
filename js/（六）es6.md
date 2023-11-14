[toc]

# es6新特性概览

+ 语法：...操作符、对象和数组解构、for...of 和 for...in、模版字符串
+ 类型：const、let
+ 原型和继承： 类
+ 内置对象
+ 函数的标准库： 箭头函数、函数参数的默认值

# ES6转换为ES5的思路是什么

ES6 转 ES5 目前行业标配是用 Babel，转换的大致流程如下：

```
解析：解析代码字符串，生成 AST；
转换：按一定的规则转换、修改 AST；
生成：将修改后的 AST 转换成普通代码。
```

如果不用工具，纯人工的话，就是使用或自己写各种 polyfill 了。

# 1.let与const

const 和 let 声明的变量不在 window 上, 只是一个块级作用域（Script）中.

```js
let aa = 1;
const bb = 2;
console.log(window.aa); // undefined
console.log(window.bb); // undefined

相当于：
(function(){
  var  a = 1;
  var b = 2;
})()
```

其他

```js
if(!("a" in window)) {
    var a = 1;
}
alert(a);    //    undefiend
```



## var let const的区别

共同点

```js
都符合作用域链，都可以访问上一层作用域链的变量
```

不同点

```
变量提升：let没有变量提升
能不能重复声明
暂时性死区
块级作用域
```

### (1)let没有变量提升

var存在变量提升，提前完成了声明和初始化， 赋值为undefined， 可以在赋值之前使用，let没有变量提升， 必须先声明，再赋值， 再去使用

```
var：提前完成了声明和初始化，赋值为undefined
function： 声明、初始化、赋值一开始就全部完成，函数的变量提升优先级更高
let：解析器进入一个块级作用域，发现let关键字，变量只是先完成声明，并没有到初始化那一步。此时如果在此作用域提前访问，则报错xx is not defined，这就是暂时性死区的由来。等到解析到有let那一行的时候，才会进入初始化阶段。如果let的那一行是赋值操作，则初始化和赋值同时进行
const、class都是同let一样的道理
```

### (2)let不能重复声明

在全局作用域下使用var，变量会挂载到window上，而let不会。在同一个作用域下，var可以重复声明，let不行。

### (3)let const存在暂时性死区

let const存在暂时性死区，也就是，在同一个作用域内内，只要用到了let，const，在它们声明之前使用了它们，就会报错

```js
function hello(){
   hello1='hello'
  //ReferenceError: hello1 is not defined,
   console.log('hello',hello1)                        
   //虽然看起来是定义了一个全局变量应该可以输出，但是在这个块级作用域使用了let，那么由于暂时性死区，在声明之前不能使用，因此会报错
   let hello1
}
hello()
```

不同作用域可以重复声明

```js
const web='aa';
function(){
    const web='bb'
}
```

### (4)let有块级作用域

- var没有块级作用域，let，const有块级作用域，避免变量污染

var会直接在栈里分配一个内存空间，等实际执行到语句时，再保存变量。如果遇到引用类型的变量，会去堆里开辟一个空间来保存对象，然后在栈里存储指向对象的指针。 let不会直接去栈里分配内存空间，而是做一个**预检查**，如果有同名变量就会报错。

## let const的区别

### (1)const声明时必须赋值

const时必须已经赋值，let不需要

```
const保证的时引用地址不变，
因此如果用const定义基本类型值，如果改变会重新在内存空间开辟地址，引用指针改变，报错
如果const定义的是引用类型的值，那么改变里边的属性引用指针不变，是可以改变的
```

### (2)const声明的常量不能更改

const声明的常量不能更改，let可以

```js
const obj={
    name:'lili',
    ageP:'17'
};
obj.name='hello';
console.log(obj)//age: "17" name: "hello"
```

### 补充Object.freeze()

在严格模式下使用Object.freeze,会使得引用类型也完全无法改变

```js
"use strict"
const obj={
    name:'lili',
    age:'17'
};
Object.freeze(obj);
obj.name='hello';
console.log(obj);
```

***********
# 2.模板字符串

- 用反引号
- 变量用${}
- 支持空格，回车

```
//${}也支持函数和计算
function showname(){
    return 'miaomiao'
}
let str=`hello ${showname()}` //hello miaomiao
console.log(str)
console.log(`hello ${5+6}`) //hello 11
```

模板字符串内可以再次嵌套字符串





# 新数据结构

------

### symbol

它的功能类似于一种标识唯一性的ID,之前的解决方案是字符串加前缀使得其为唯一的。相当于一个唯一的字符串

#### 定义方式

- 第一种定义方式

```
let s1=Symbol('name')
let s2=Symbol('age')
console.log(typeof s1) //symbol
console.log(s1.description) //name
console.log(s1===s2) //false
```

- 第二种定义方式

```
let s1=Symbol.for('name')
let s2=Symbol.for('name')
console.log(s1.description) //name
console.log(s1===s2) //true
```

- Symbol类型可以作为object的key值，但是Object.keys,values,JSON.stringify无法获取到它的值，需要使用新增的API,可以用于类内部属性

```
// 使用Object.keys()无法获取Symbol类型
//只使用Object.getOwnPropertySymbols()只能获取Symbol属性的值
let key of Object.getOwnPropertySymbols(hd)
//Reflect.ownkeys全可以获取
let key of Reflect.ownkeys(hd)
```

- 不能为Symbol设置key，它本质上接近字符串

#### 使用场景

- 字符串耦合

```
//被覆盖
let user1="李四";
let user2="李四";
let obj={
    [user1]:'40',
    [user2]:'50'
}
console.log(obj) //{李四: "50"}
let user1={
    'name':'李四',
    'key':Symbol()
}
let user2={
    'name':'李四',
    'key':Symbol()
}
let obj={
    [user1.key]:'40',
    [user2.key]:'50'
}
console.log(obj) //Symbol(): "40" Symbol(): "50"
console.log(obj[user1.key]) //40
```

解决可能重复的情况





# 新的数据结构

## Set()不可重复类型

不能去重引用类型，只能去重基本类型

### 主要方法

```js
constructor：构造函数，返回Set
size：返回实例成员总数

add()：添加值，返回实例
delete()：删除值，返回布尔,true或者false
has()：检查值，返回布尔
clear()：清除所有成员,返回undefined
keys()：返回以属性值为遍历器的对象
values()：返回以属性值为遍历器的对象
entries()：返回以属性值和属性值为遍历器的对象
forEach()：使用回调函数遍历每个成员
```

其中，entries()属性，以及对应的values(),key()属性

```js
let set = new Set(['a', 'b', 'c']);
console.log(set.entries()); // SetIterator {"a", "b", "c"}
console.log([...set.entries()]); // [["a", "a"], ["b", "b"], ["c", "c"]]
```

### 模拟实现Set

```js
function Set() {
    var items = {};
    this.has = function(value){
        return items.hasOwnProperty(value);
    },
    this.add = function(value){
        if (!this.has(value)) {
            items[value] = value;
            return true;
        }
        return false;
    },
    this.remove = function(value){
        if (this.has(value)) {
            delete value;
            return true;
        }
        return false;
    },
    this.clear = function(){
        items={};
    },
    this.size = function(){
        var count = 0;
        for(var prop in items){
            if (items.hasOwnProperty(prop)) {
                ++count;
            }
        }
        return count;
    },
    this.values= function(){
        var values = [];
        for(var value in items){
            if (items.hasOwnProperty(value)) {
                values.push(value);
            }
        }
        return values;
    },
```

### 与数组关系

```js
const a= new Set([1,2,3,1,2,3])
console.log(a)//去重，Set(3){1, 2, 3}
a.add(4) //a.delete(),a.keys,a.values,a.entries
console.log(a) //Set(4){1, 2, 3, 4}
const b=new Set([1,2,5,7,7])
console.log(new Set([...a,...b]))//并集{1, 2, 3, 4, 5,7}
```

使用

```js
//数组去重
[...new Set(arr)]或Array.from(new Set(arr))
//数组取并集
new Set([...a, ...b])
//字符串去重
[...new Set(str)].join("")
//如
var str='hhhhhhhh';
var thestr=new Set(str);
console.log(thestr); //Set(1){"h"}
console.log([...thestr].join('')); //h
```

### 与对象的区别

对象的key都会被转换为字符串,对象作为key，会使用objkey.toString()，结果是'[object Object]'

```js
let objkey={
    'mimi':'nini'
}
let obj={
    '1':'hello',
     1:'hi',
     [objkey]:'miao'
}
console.log(obj)
// {1: "hi", [object Object]: "miao"}
```

### 类型转换

- Array.from()

- [...]

*********

## 2.WeakSet()

- WeakSet的成员只能是对象,Set的成员不仅可以是对象还可以是其他类型的值。
- 成员都是弱引用，垃圾回收机制不考虑WeakSet结构对此成员的引用,其他对象不再引用成员时，垃圾回收机制会自动回收此成员所占用的内存，不考虑此成员是否还存在于WeakSet结构中
- 只能使用add(),delete(),has()
- 不能遍历

```js
// 储存DOM节点：DOM节点被移除时自动释放此成员，不用担心这些节点从文档移除时会引发内存泄漏
let hd={'hello':'hi'}; //引用次数1
let hd2=hd; ////引用次数2
let newweakset=new WeakSet();
newweakset.add(hd); //引用次数不变
hd=null; //引用次数减1
hd2=null;//引用次数减1
console.log(newweakset) //[[Entries]] 0:value: {hello: "hi"}__proto__: WeakSet
//WeakSet还认为自己有值，会显示出来，实际上没有
console.log(newweakset.has(hd)) //false
```

*******

## 3.Map类型

键值对，键可以是对象

```js
const map1 = new Map()
const objkey = {p1: 'v1'}

map1.set(objkey, 'hello')
console.log(map1.get(objkey)) //hello
```
+ 可以接收数组作为参数，与Set相同，需要外边[]包裹

```js
const map2 = new Map([
  ['name', 'Aissen'],
  ['age', 12]
])
```
+ map.size获取大小长度
+ 可以用set设置键值对且支持链式调用

```js
map4.set('k2', 2).set('k3', 4).set('k4', 5)
```
+ map.has('k1')判断指定的键是否存在，delete删除，clear清空
+ map1.keys,values,entries

*****

## 4.Set、Map、WeakSet 和 WeakMap 的区别

    Set
        成员唯一、无序且不重复
        [value, value]，键值与键名是一致的（或者说只有键值，没有键名）
        可以遍历，方法有：add、delete、has
    WeakSet
        成员都是对象
        成员都是弱引用，可以被垃圾回收机制回收，可以用来保存DOM节点，不容易造成内存泄漏
        不能遍历，方法有add、delete、has
    Map
        本质上是键值对的集合，类似集合
        可以遍历，方法很多可以跟各种数据格式转换
    WeakMap
        只接受对象作为键名（null除外），不接受其他类型的值作为键名
        键名是弱引用，键值可以是任意的，键名所指向的对象可以被垃圾回收，此时键名是无效的
        不能遍历，方法有get、set、has、delete， 不能使用clear

# 函数
## rest参数
+ 在函数参数中收集剩余的参数，将它们放入一个数组中
+ rest参数放在最后一个位置
```js
function fun1(a, b, c, ...args) {}
```
## 箭头函数

### 与普通函数的区别

```js
1.箭头函数没有arguments，可以访问外围函数的 arguments 对象
2.箭头函数没有prototype属性，不能用作构造函数（不能用new关键字调用）
3.箭头函数没有自己this，它的this是词法的，引用的是上下文的this，即在你写这行代码的时候就箭头函数的this就已经和外层执行上下文的this绑定了，要通过作用域链来确定，不能用 call()、apply()、bind() 这些方法改变 this 的指向
4.不可以使用 yield 命令，因此箭头函数不能用作 Generator 函数。
```

JavaScript函数两个内部方法: [[Call]]和[[Construct]]

```
直接调用时执行[[Call]]方法, 直接执行函数体
new调用时执行[[Construct]]方法, 创建一个实例对象
```

### 正确写法

- 支持默认值
- 展开运算符

```
const sum=(x1,x2)=>{console.log(x1+x2)}
arr=[3,2]
sum(...arr)
```

- 例子(字节面经)

```
let a = (x) => x
let b = (x) => {x;}
let c = (x) => {{x;}}
console.log(a(1), b(1), c(1));//1，undefined,undefined

babel解析结果
var a = function a(x) {
  return x;
};

var b = function b(x) {
  x;
};

var c = function c(x) {
  {
    x;
  }
};
```



# proxy

代理，拦截器，当obj.count会触发get，obj.count=1会触发set,proxy可能会有this的指向问题，Reflect的作用则是可以获取到内部的方法，可以用Reflect.get替换原本的get

```javascript
var obj = new Proxy({}, {
  get: function (target, propKey, receiver) {
    console.log(`getting ${propKey}!`);
    return Reflect.get(target, propKey, receiver);
  },
  set: function (target, propKey, value, receiver) {
    console.log(`setting ${propKey}!`);
    return Reflect.set(target, propKey, value, receiver);
  }
});
```

或者

```
var proxy = new Proxy(target, handler);
```
+ 可取消proxy： Proxy.revocable()返回一个对象， revoke执行完后，取消了Proxy实例，当再次访问Proxy实例时会报错， 使用场景：目标对象不允许直接访问，必须通过代理访问，一但访问结束，就是收回代理权，不允许再次访问。
```js
let target = {}
let handler = {}
let {proxy, revoke} = Proxy.revocable(target , handler );

console.log(proxy.foo = 100);  // 100
revoke()  // 取消Proxy实例
console.log(proxy.foo); //  Cannot perform 'get' on a proxy that has been revoked
```







# qita

```

// 补充，es6里super()用来继承，static静态方法表示该方法是直接通过类的方法来调用的，不能被实例继承
class Father {
    static testMethod() {
        return 'hello';
    }
}
 
Father.testMethod() // 'hello'
var Child = new Father();
Child.testMethod()
// TypeError: Child.testMethod is not a function，这是因为Father中的testMethod方法是静态方法(有static关键字)，不会被实例化出来的Child继承

//父类的静态方法可以被子类继承
class Father {
  static testMethod() {
    return 'hello';
  }
}
class Child extends Father {
 
}
Child.classMethod(); // 'hello'
```

