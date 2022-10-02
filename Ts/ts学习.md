[toc]

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
  npm install -g typescript
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

TypeScript 中的常见的类型很丰富，包括 ：

undefined | null | boolean | number | bigint | string | symbol | void | object | unknown | never | any

如果定义的时候没有指定，ts会根据上下文自动进行判断并指定类型

其中：

+ undefined | null | boolean | number | bigint | string | symbol | void 是原始类型，字面量类型（literal type）和枚举类型（enum type）

+ object 代表了所有非原始类型

+ unknown | never 则是类型论中顶和底类型

+ any 则代表了动态类型。

## 原始类型

### 1.undefined，null

这两个类型都只有一个值，而且值的名称和类型的名称相同

+ 默认情况下`null`和`undefined`是所有类型的子类型。 就是说你可以把 `null`和`undefined`赋值给`number`类型的变量。

+ 然而，在`tonfig.json`文件中`--strictNullChecks`设置为 true 标记（默认是 false），`null`和`undefined`只能赋值给`void`和它们各自

### 2.boolean

只有true和false

```
let isDone: boolean = false;
```

### 3.number，bigint

和JavaScript一样，TypeScript里的所有数字都是浮点数。 这些浮点数的类型是 `number`。 除了支持十进制和十六进制字面量，TypeScript还支持ECMAScript 2015中引入的二进制和八进制字面量。

```
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

使用 `BigInt` 可以安全地存储和操作大整数

```
let res: BigInt = BigInt(Number.MAX_SAFE_INTEGER)
```

### 4.string

和js的string基本一致，可以是普通字符串，也可以是模板字符串

```
let name: string = `Gene`;
let sentence: string = `Hello, my name is ${ name }.
```

### 5.Symbol

是es6支持的新的原始类型，如果要使用必须添加es6的编译库

```
let only: Symbol = Symbol(18)
```

symbol 是一种特殊的类型。初级使用建议先不了解。
其中还有一个特殊的语法 [unique symbol](https://link.zhihu.com/?target=https%3A//www.typescriptlang.org/docs/handbook/release-notes/typescript-2-7.html)

### 6.void

对变量赋值为 void 类型用途不大，这时候只相当于 undefined 类型的别名

void 一般用于函数的返回值声明

声明一个`void`类型的变量没有什么大用，因为你只能为它赋予`undefined`和`null`：

### 7.字面量（Literal Type）

主要分为 

+ 真值字面量类型（boolean literal types）
+ 数字字面量类型（numeric literal types）
+ 枚举字面量类型（enum literal types） 
+ 大整数字面量类型（bigInt literal types）
+ 字符串字面量类型（string literal types

```tsx
const a : false = false
const b: 2333 = 2333
const c : 0x1919n = 6425n
const d : 'hello' = 'hello'
const foo: 'foo' = 'foobar' 
// Type '"foobar"' is not assignable to type '"foo"'.
```

### 8.枚举

使用枚举类型可以为一组数值赋予友好的名字,枚举类型提供的一个便利是你可以由枚举的值得到它的名字

+ 数字枚举

```tsx
enum Direction {
    Up= 1,
    Down,
    Left,
    Right,
}
```

`Up`使用初始化为 `1`。 其余的成员会从 `1`开始自动增长。 换句话说， `Direction.Up`的值为 `1`， `Down`为 `2`， `Left`为 `3`， `Right`为 `4`。

如果没有初始化，会从0开始

通过枚举的属性来访问枚举成员，和枚举的名字来访问枚举类型

+ 计算枚举

```tsx
enum FileAccess {
    // constant members
    None,
    Read    = 1 << 1,
    Write   = 1 << 2,
    ReadWrite  = Read | Write,
    // computed member
    G = "123".length
}
```

若常数枚举表达式求值后为 `NaN`或 `Infinity`，则会在编译阶段报错。

+ 字符串枚举

```tsx
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```

+ 异构枚举(不建议这样做)

```
enum BooleanLikeHeterogeneousEnum {
    No = 0,
    Yes = "YES",
}
```

+ 外部枚举

？？？

运行时枚举是在运行时真正存在的对象

外部枚举用来描述已经存在的枚举类型的形状。

```tsx
declare enum Enum {
    A = 1,
    B,
    C = 2
}
```

外部枚举和非外部枚举之间有一个重要的区别，在正常的枚举里，没有初始化方法的成员被当成常数成员。 对于非常数的外部枚举而言，没有初始化方法时被当做需要经过计算的。

## 非原始类型

### 1.Object

在t s中，使用接口来定义对象的类型，对象相对于接口参数多了或者少了都会报错，但是

```tsx
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```

+ 可选属性

```
interface SquareConfig {
  color?: string;
  width?: number;
}
```

可选属性的好处之一是可以对可能存在的属性进行预定义，好处之二是可以捕获引用了不存在的属性时的错误。

+ 只读属性

```
interface Point {
    readonly x: number;
    readonly y: number;
}
```

+ 额外的属性检查

表示的是`SquareConfig`可以有任意数量的属性，并且只要它们不是`color`和`width`，那么就无所谓它们的类型是什么。

```
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

像 [propName: string]: any;其实是可索引的类型，可以限制比如`a[10]`或ageMap["daniel"]对应的类型时什么

```tsx
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```

### 2.array数组

数组是一个变长的，每一项的类型都相同的列表

有两种定义方法

```
//第一种，直接在后边加[]
let list: number[] = [1, 2, 3];
//第二种，使用数组泛型
let list: Array<number> = [1, 2, 3];
```

<font color="red">数组泛型是什么？</font>

### 3.tuple元组 

元组类型允许表示一个<font color="red">已知元素数量和类型的数组，</font>各元素的类型不必相同。

```tsx
let x: [string, number] = ['hello', 10]; 
```

#### 获取元组的子项

当访问一个已知索引的元素，会得到正确的类型：

```tsx
console.log(x[1].substr(1)); 
// 报错，number类型不能使用substr
```

当访问一个越界的元素，会使用联合类型替代：

越界的元素能不能访问，会不会报错

```
x[3] = 'world'; // OK, 字符串可以赋值给(string | number)类型
```

通过泛型获取

```
[K in T[number]]: K
```

#### 获取元组长度

```
T['length']
```

#### 不可变元组

元组类型的变量内的元素值是不可变的，也就是不可再 **增加元素**、**删除元素**、**修改元素值**。

+ 在 Tuple Type **字面量** 前加 `readonly` 关键字

  ```
  type tupleTypeDemmo = readonly [string, number];
  ```

+ 给数组类型的变量（注意：不是类型）后加 `const` 断言

  ```
  let demo = [{
      item: {
          name: 'guoshi'
      }
  }, 2] as const;
  ```

  





### 4.函数

#### 为函数定义类型

```js
function add(x: number, y: number): number {
    return x + y;
}
let myAdd = function(x, y) { return x + y; };
```

+ 函数的完整类型(参数类型是匹配的即可)

```tsx
let myAdd: (baseValue: number, increment: number) => number = function(x: number, y: number): number { return x + y; };
// 如果赋值类型没有完全指定的话，可以进行类型推断，比如
let myAdd: (baseValue: number, increment: number) => number = function(x, y) { return x + y; };
```

+ 也可以用接口描述函数类型,

```tsx
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

```tsx
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
}
```

#### 参数配置

+ 可选的参数

```tsx
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}
```

可选参数必须跟在必须参数后面。 如果上例我们想让first name是可选的，那么就必须调整它们的位置，把first name放在后面。

+ 设置默认值

```js
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}
```

带默认值的参数不需要放在必须参数的后面

+ 剩余参数

如果想同时操作多个参数，或者并不知道会有多少参数传递进来。 在JavaScript里，你可以使用 `arguments`来访问所有传入的参数。

在TypeScript里，可以把所有参数收集到一个变量里

```tsx
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}
```

#### 函数多态

```tsx
function plusNumber(x: number, y: number) {
  return x + y;
}

function plusNumberArray(xs: number[], ys: number[]) {
  return [...xs, ...ys];
}
```

## unknown | never

### unknown

+ unknown type 是 TypeScript 中的 [Top Type](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Top_type)。符号是(⊤)

+ 任何值都可以赋值给类型是 unkown 的变量，与其对应的是，我们不能把一个 unkown 类型的值赋值给任意非 unkown 类型的值。

```tsx
let a: unknown = undefined
a = Symbol('deep dark fantasy')
a = {}
a = false
a = '114514'
let b : bigint = a;
//Type 'unknown' is not assignable to type 'bigint'.
```

### never

+ never 是 TypeScript 中的 [Bottom Type](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Bottom_type)。符号是(⊥)

+ 就是类型是 never 的值都可以赋值给任何类型的变量
+ `never`类型表示的是那些永不存在的值的类型。某个变量的类型是 never 一般表示程序不会执行到这里, 某个函数的返回值类型是 never，一般表示这个函数会 抛出异常，或者永不停机，不会正常返回。

## any

有时候，我们会想要为那些在编程阶段还不清楚类型的变量指定一个类型。 这些值可能来自于动态的内容，比如来自用户输入或第三方代码库。 这种情况下，我们不希望类型检查器对这些值进行检查而是直接让它们通过编译阶段的检查。 那么我们可以使用 `any`类型来标记这些变量：

```
let notSure: any = 4;
notSure = "maybe a string instead";
```

# 高级类型

## 联合类型

联合类型表示一个值可以是几种类型之一。 我们用竖线（ `|`）分隔每个类型，所以 `number | string | boolean`表示一个值可以是 `number`， `string`，或 `boolean`。

如果一个值是联合类型，我们只能访问此联合类型的所有类型里共有的成员

```tsx
interface Bird {
    fly();
    layEggs();
}

interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors
```

## 交叉类型

交叉类型是通过使用`&`符号，将多个类型合并为一个类型,合并的多个接口类型 可以将多个接口类型求并集得到的结果

```tsx
  type IntersectionTypeConfict = { id: number; name: string; } & { age: number; sex: string; };
  const mixedConflict: IntersectionTypeConfict = {
    id: 1,
    sex: '男',
    age: 2,
    name: '123'
  };
```

合并的多个接口类型中存在同名属性会是什么效果?

可以分为两种情况:

- 同名属性兼容: 兼容的同名属性 合并后会是两者类型的子类型 (同 type name = string & '2' // '2' 类型 )
- 同名属性不兼容: 不兼容的合并后会得到 never类型 (同 type name = string & number //never类型)

这里的意思是

联合类型，string | number 表示就是string或者number, 如果是接口，就是反而取交集

交叉类型，string & number表示取string 并且number，得到的是never类型，如果是接口，就是取并集？

## 类类型

明确的强制一个类去符合某种契约,可以在接口里描述一种方法，描述了类的公共部分，不检查私有部分

```tsx
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
          this.currentTime = d;
      }
    constructor(h: number, m: number) { }
}
```

### 继承接口

能够从一个接口里复制成员到另一个接口里

```tsx
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

也可以继承多个接口

```tsx
interface Square extends Shape, PenStroke {
    sideLength: number;
}
```

### infer关键字

`infer`是一个只能在 `extends`出现的时候 才能使用的关键字， 可以推断一个类型变量，但是，这个类型变量只能在true的分支中使用。

+ extends的参数位置
+ extends返回位置
+ 

```tsx
// 解读: 如果泛型变量T是 () => infer R的`子集`，那么返回 通过infer获取到的函数返回值，否则返回boolean类型
type Func<T> = T extends () => infer R ? R : boolean;
```

infer X 就相当于声明了一个变量，这个变量随后可以使用

```tsx
// 当a、b为不同类型的时候，返回不同类型的联合类型
type Obj<T> = T extends {a: infer VType, b: infer VType} ? VType : number;
let obj4: Obj<{a: number, b: () => void}>; // => number | () => void
```



# 类型断言

类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构，`as`关键字来声明变量类型，强制将参数转换为具体类型。

```js
function getLength(input: string | number): number {
  const str = input as string;
  if (str.length) {
    return str.length;
  } else {
    const number = input as number;
    return number.toString().length;
  }
}
```

# 类型别名

- 类型别名就是给类型起一个别名，让它可以更方便的被重用，起别名不会新建一个类型，而是创建了一个新的名字来引用那个类型

```js
type NameOrResolver = Name | NameResolver; 
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    }
    else {
        return n();
    }
}
```

## type和interface

存在既可以用type也可以用interface的场景

```tsx
// interface
interface Person {
    name: string
    age: number
}

const person: Person = {
    name: 'lin',
    age: 18
}

// type
type Person = {
    name: string
    age: number
}

const person: Person = {
    name: 'lin',
    age: 18
}
```

+ 相同点：

  + 都可以定义一个对象或函数
  + 都允许继承（extends）：interface 使用 extends 实现继承， type 使用交叉类型实现继承

+ 不同点

  + 只有type可以：声明基本类型、联合类型、交叉类型、元组

  + 只有interface可以：

    + 合并重复声明(type会报错)

      ```tsx
      interface Person {
          name: string
      }
      
      interface Person {         // 重复声明 interface，就合并了
          age: number
      }
      
      const person: Person = {
          name: 'lin',
          age: 18
      }
      ```

# 类型守卫（类型保护）

http://t.zoukankan.com/goloving-p-15422728.html

其**主要思想是尝试检测属性、方法或原型，以确定如何处理值**,简言之，类型守卫就是加一层判断，对于不同的判断进行不同的业务处理。主要判断包括：

+ in 关键字：可以遍历目标类型的公开属性名

```js
type Property = 'name' | 'age' | 'phoneNum'; 
type PropertyObject = { [key in Property]: string; }
```

+ typeof 关键字：不能检测更为复杂的数据类型，也难以区分Array。在使用过程中不好用，所以一般用typeof，而是使用它的替代方案intanceof

```js
function func(a: string| number): string {
    if (typeof a === 'string') {
        return a.toLocaleLowerCase();
    } else {
        return a.toFixed(1);
    }
}
```

+ instanceof 关键字

+ 自定义类型保护， ==， ！==等

# 泛型

## 参数设置

表示一般是如下的占位表示，也可以自己设置

+ `T` 代表 **Type**
+ K（Key）：表示对象中的键类型；
+ V（Value）：表示对象中的值类型；
+ E（Element）：表示元素类型。

### 多个参数（交换元组的两个元素）

```tsx
function swap<T, U>(tuple: [T, U]): [U, T]{
    return [tuple[1], tuple[0]]
}
```

### 默认参数(默认传入的是number)

```tsx
interface Iprint<T = number> {
    (arg: T): T
}
```

### 约束一定要有某些属性(继承)

```tsx
interface ILength {
    length: number
}

function printLength<T extends ILength>(arg: T): T {
    console.log(arg.length)
    return arg
}
```

## 基础概念

我们需要一种方法使<font color="red">返回值的类型与传入参数的类型是相同的</font>,T是类型变量*，它是一种特殊的变量，只用于表示类型而不是值。

```js
function identity<T>(arg: T): T {
    return arg;
}
// 使用的时候
identity<number>(1) 显式规定传入的T就是number
identity(1) 不显式规定，自行判断出来传入的是number,T自动捕获为number类型
```

这里的T就是一个占位的功能，传进来是number，然后所有T的位置都是number, 其实这个类型是需要用户传入进来的

把这个identity`函数叫做泛型，因为它可以适用于多个类型。 不同于使用 `any`，它不会丢失信息

# 装饰器

## 类装饰器

- target表示装饰的目标类

```tsx
function helloWorld(target){
    // target === 目标类
    target.prototype.sayHello = function(){
        console.log('hello world');
    }
    return target
}

@helloWorld
class Router {
    sayHello() {
        throw new Error("Method not implemented.");
    }

}

const r = new Router()
r.sayHello() // hello world
```

## 函数装饰器

```tsx
function expired(target, name, descriptor): any {
    console.log('fun:',name, 'is expired');
    return descriptor.value
}

@helloWorld
class Router {
    @expired
    hello() {
        // ...code
    }
    @expired
    hello2(){
        // ...code
    }
}
// fun: hello is expired
// fun: hello2 is expired
```

- target: 目标类的原型
- name: 属性名
- descriptor：属性描述符号
  - value：属性值
  - writable：是否可以被重写
  - enumerable：是否可枚举
  - configurable：是否可配置

## get/set装饰器

基本与函数装饰器一样

## 函数参数装饰器

装饰对象是函数中的一个参数

```tsx
function printParamPos(target, name, idx) {
    console.log('paramCount:', target[name].length, 'paramPos:', idx);
}

class Router {
    hello3(@printParamPos name: string) {
        console.log('hello3', name);
    }
    static hello4(@printParamPos name: string) {
        console.log('hello4', name);
    }
}

const r = new Router()
Router.hello4('123')
r.hello3('456')
```

- target: 静态方法指向类的构造函数，实例方法指向类的原型
- name：属性名
- idx：参数在函数中所处位置



## TS中的一些符号

### ?. 链判断运算符

```
const test = a.b.c.d
```

链式较长，中间可能报错，取不到值

```
const test = a?.b?.c?.d
```

中间一旦有一个为undefined或者null，就不继续运算，直接返回undefined

### `!` 断言操作符

`!` 的作用是断言某个变量不会是 `null/undefined`，告诉编辑器停止报错。

# 参考材料

1. https://www.tslang.cn/docs/release-notes/typescript-3.1.html
2. https://zhuanlan.zhihu.com/p/60231338
3. https://blog.csdn.net/frontend_frank/article/details/111148526
4. https://juejin.cn/post/7063521133340917773
5. 轻松拿下 TS 泛型https://juejin.cn/post/7064351631072526350
6. TypeScript装饰器学习https://juejin.cn/post/6977730054780354590
7. 白话typescript中的【extends】和【infer】https://juejin.cn/post/6844904146877808653

