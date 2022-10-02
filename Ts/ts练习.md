[toc]

练习题目

https://github.com/type-challenges/type-challenges

## 1.第一个元素

实现一个通用`First<T>`，它接受一个数组`T`并返回它的第一个元素的类型。

```tsx
type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type head1 = First<arr1> // expected to be 'a'
type head2 = First<arr2> // expected to be 3
```

实现：

```
type First<T extends any[]> = T extends never[] ? never: T[0]
```

如何判断空数组

```
如何判断空数组？有2种办法：T extends []，T["length"] extends 0。
```

## 2.实现内置的Pick

**从类型 `T` 中选择出属性 `K`，构造成一个新的类型**。

```ts
interface Todo {
  title: string
  description: string
  completed: boolean
}

type TodoPreview = MyPick<Todo, 'title' | 'completed'>

const todo: TodoPreview = {
    title: 'Clean room',
    completed: false,
}
```



```tsx
type MyPick<T, K extends keyof T> = { 
  [P in K]: T[P]; 
}
```

### keyof与typeof

+ keyof 可以获取一个对象接口的所有 key 值

  ```
  type a = {
    b: string,
    c: string
  }
  type foo =  keyof a
  // type foo = 'b'| 'c'
  ```

+ in 可以遍历枚举类型

  ```tsx
  type keys = 'a' | 'b'| 'c'
  type Obj = {
    [T in keys] : string
  }
  //
  type Obj = {
    'a' : string,
    'b' : string,
    'c' : string
  }
  //
  [P in T[number]]  从数组里读取
  ```

+ typeof用来获取一个变量或对象的类型

  ```
  interface Person{
      name:string,
      age:number
  }
  const t1:Person = {
      name:'111',
      age:11
  }
  
  type P1  = typeof t1  // type P1 = Person
  ```

  

## 3.实现 Readonly

### (1)只有一个参数

```tsx
interface Todo {
  title: string
  description: string
}

const todo: MyReadonly<Todo> = {
  title: "Hey",
  description: "foobar"
}

todo.title = "Hello" // Error: cannot reassign a readonly property
todo.description = "barFoo" // Error: cannot reassign a readonly property
```

实现

```
type MyReadonly<T> = {
  readonly [K in keyof T]: T[K]
}
```

### (2)两个参数

实现一个通用`MyReadonly2<T, K>`，它带有两种类型的参数`T`和`K`。

`K`指定应设置为Readonly的`T`的属性集。如果未提供`K`，则应使所有属性都变为只读，就像普通的`Readonly<T>`一样。

```tsx
interface Todo {
  title: string
  description: string
  completed: boolean
}

const todo: MyReadonly2<Todo, 'title' | 'description'> = {
  title: "Hey",
  description: "foobar",
  completed: false,
}

todo.title = "Hello" // Error: cannot reassign a readonly property
todo.description = "barFoo" // Error: cannot reassign a readonly property
todo.completed = true // OK
```

实现

```tsx
type MyReadonly2<T, K extends keyof T = keyof T> = { readonly [key in K]: T[key] } & { [key in Exclude<keyof T, K>]: T[key] }
// ↓ { [key in Exclude<keyof T, K>]: T[key] } = Omit<T, K>
type MyReadonly2<T, K extends keyof T = keyof T> = Omit<T, K> & { readonly [key in K]: T[key] }
```

### (3)深度 Readonly

将对象的每个参数及其子对象递归地设为只读。

```tsx
type X = { 
  x: { 
    a: 1
    b: 'hi'
  }
  y: 'hey'
}

type Expected = { 
  readonly x: { 
    readonly a: 1
    readonly b: 'hi'
  }
  readonly y: 'hey' 
}

type Todo = DeepReadonly<X> // should be same as `Expected`
```

实现

```tsx
type DeepReadonly<T> = {
    readonly [K in keyof T]: T[K] extends object ? DeepReadonly<T[K]> : T[K];
}
```





## 4. 元组转换为对象 

传入一个元组类型，将这个元组类型转换为对象类型，这个对象类型的键/值都是从元组中遍历出来。

```tsx
const tuple = ['tesla', 'model 3', 'model X', 'model Y'] as const

type result = TupleToObject<typeof tuple> // expected { tesla: 'tesla', 'model 3': 'model 3', 'model X': 'model X', 'model Y': 'model Y'}
```

实现

```tsx
type TupleToObject<T extends readonly ( string | number | symbol )[]> = {
  [P in T[number]]: P
}
```

## 5.获取元组长度

```tsx
type tesla = ['tesla', 'model 3', 'model X', 'model Y']
type teslaLength = Length<tesla> // expected 4
```

实现

```tsx
type Length<T extends readonly any[]> = T['length']
```

## 6.实现内置的Exclude

从联合类型T中排除U的类型成员，来构造一个新的类型。

```tsx
type Result = MyExclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
```

错误解法：

```
type MyExclude<T, K extends keyof(T)> = {
  [P in keyof(T) && K !== P]: T[P]
}
```

正确解法：

```
type MyExclude<T, U> = T extends U ? never : T
```

如果T中元素在U中有，则通过never移除

### 差集和交集

```
type Diff<T, U> = T extends U ? never : T; // 找出T的差集
type Filter<T, U> = T extends U ? T : never; // 找出交集
```



## 7. Promise 返回的类型

<font color="red">不太理解</font>



在 TS 中，我们用 Promise 中的 T 来描述这个 Promise 返回的类型。实现一个类型，可以获取这个类型。

```tsx
type ExampleType = Promise<string>
type Result = MyAwaited<ExampleType> // string
```

解法：

```tsx
type MyAwaited<T> = T extends Promise<infer P> 
  ? P extends Promise<any>
    ? MyAwaited<P>
    : P
  : never;
```

+ 对于普通的类型，例如type X = Promise<string>，用T extends Promise<infer P> 即可判断出,不是promise直接返回never, 是promise的话，进一步判断是否嵌套型
+ 对于嵌套类型，例如type Z = Promise<Promise<string | number>>，需要再判断一下P是否为Promise类型，若是的话，递归判断

## 8.实现IF

实现一个 `IF` 类型，它接收一个条件类型 `C` ，一个判断为真时的返回类型 `T` ，以及一个判断为假时的返回类型 `F`

```tsx
type A = If<true, 'a', 'b'>  // expected to be 'a'
type B = If<false, 'a', 'b'> // expected to be 'b'
```

实现：

```tsx
type IF(T extend boolean, P, Q){
  if(T){
    return P
  }else{
    return Q
  }
}
```

正确解法：

```tsx
if<C extends boolean, T, F> = C extends true ? T : F
```

## 9.concat

在类型系统里实现 JavaScript 内置的 `Array.concat` 方法

```TS
type Result = Concat<[1], [2]> // expected to be [1, 2]
```

实现

```tsx
type Concat(T extends array, K extends array){
  [...T, ...K]
}
```

正确解法：

+ 数组类型入参：T extends any[]

```tsx
type Contact<T extends any[], U extends any[]> = [...T, ...U]
```

## 10.Includes

<font color="red">没看懂</font>

在类型系统里实现 JavaScript 的 `Array.includes` 方法，这个类型接受两个参数，返回的类型要么是 `true` 要么是 `false`

```tsx
type isPillarMen = Includes<['Kars', 'Esidisi', 'Wamuu', 'Santana'], 'Dio'> // expected to be `false`
```

实现

```
type Includes(T, P){
  return P extends T
}
```

正确解法：

```tsx
type MyEqual<X, Y> =
  (<T>() => T extends X ? 1 : 2) extends
  (<T>() => T extends Y ? 1 : 2) ? true : false

type Includes<T extends readonly any[], U> = T extends [infer P, ...infer Rest] 
  ? MyEqual<P, U> extends true 
    ? true
    : Includes<Rest, U>
  : false;
```

+ 递归取T中第一项与U进行判断是否相等

## 11.push

在类型系统里实现通用的 `Array.push`

```tsx
type Result = Push<[1, 2], '3'> // [1, 2, '3']
```

实现

```tsx
type Push<T extends any[], U> = [...T, U]
```

## 12.unshift

实现类型版本的 `Array.unshift`。

```tsx
type Result = Unshift<[1, 2], 0> // [0, 1, 2,]
```

实现

```tsx
type Push<T extends any[], U> = [U,...T]
```

## 13.实现内置的parameters

```tsx
const foo = (arg1: string, arg2: number): void => {}

type FunctionParamsType = MyParameters<typeof foo> // [arg1: string, arg2: number]
```

实现：

```tsx
type MyParameters<T extends (...args: any[]) => any> = T extends (...args: infer P) => unknown ? P : never;
```

+ 使用infer表示待推断的类型变量。

  由于...args本身已经是元组类型，因此infer P最终推导出的，也是元组类型。

## 14.实现内置的ReturnType<T>

```tsx
const fn = (v: boolean) => {
  if (v)
    return 1
  else
    return 2
}

type a = MyReturnType<typeof fn> // 应推导出 "1 | 2"
```

实现：

```

type MyReturnType<T extends (...args: any[]) => unknown> = T extends (...args: any[]) => infer R ? R : never;
```

## 15.实现内置的Omit<T, K>

`Omit` 会创建一个省略 `K` 中字段的 `T` 对象

```tsx
interface Todo {
  title: string
  description: string
  completed: boolean
}

type TodoPreview = MyOmit<Todo, 'description' | 'title'>

const todo: TodoPreview = {
  completed: false,
}
```

实现

```tsx
// 第三种
type MyOmit<T, K extends keyof T> = {
  [P in keyof T as P extends K ? never : P]: T[P]
}
```

### as的用法

断言、强制类型转换

## 16.元组转合集

实现泛型`TupleToUnion<T>`，它返回元组所有值的合集。

```tsx
type Arr = ['1', '2', '3']
type Test = TupleToUnion<Arr> // expected to be '1' | '2' | '3'
```

实现

```tsx
type TupleToUnion<T>{
  return T[number]
}
```

正确实现

```tsx
type TupleToUnion<T extends readonly (number | string)[]> = T[number]

```

## 17.可串联构造器

需要提供两个函数 `option(key, value)` 和 `get()`。在 `option` 中你需要使用提供的 key 和 value 扩展当前的对象类型，通过 `get` 获取最终结果。

```tsx
declare const config: Chainable

const result = config
  .option('foo', 123)
  .option('name', 'type-challenges')
  .option('bar', { value: 'Hello World' })
  .get()

// 期望 result 的类型是：
interface Result {
  foo: number
  name: string
  bar: {
    value: string
  }
}
```

实现

```tsx
type Chainable<T = {}> = {
    option<K extends string, V>(
      key: K,
      value: V
    ): Chainable<T & Record<K, V>>
    get(): T
}
```

### Record

定义一个对象的 key 和 value 类型

## 18.最后一个元素

实现一个通用`Last<T>`，它接受一个数组`T`并返回其最后一个元素的类型。

```tsx
type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type tail1 = Last<arr1> // expected to be 'c'
type tail2 = Last<arr2> // expected to be 1
```

实现

```
typeof Last<T> = T['length']? T[-1] : never 
```

正确实现

```tsx
type Last<T extends any[]> = T extends [...infer R, infer L] ? L : never
```

## 19.出堆pop

实现一个通用`Pop<T>`，它接受一个数组`T`，并返回一个由数组`T`的前length-1项以相同的顺序组成的数组。

```tsx
type arr1 = ['a', 'b', 'c', 'd']
type arr2 = [3, 2, 1]

type re1 = Pop<arr1> // expected to be ['a', 'b', 'c']
type re2 = Pop<arr2> // expected to be [3, 2]
```

实现

```
type Last<T extends any[]> = T extends [...infer R, infer L] ? R : never
```

## Promise.all

<font color="red">promise系列不懂</font>

键入函数`PromiseAll`，它接受PromiseLike对象数组，返回值应为`Promise<T>`，其中`T`是解析的结果数组。

```tsx
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise<string>((resolve, reject) => {
  setTimeout(resolve, 100, 'foo');
});

// expected to be `Promise<[number, 42, string]>`
const p = PromiseAll([promise1, promise2, promise3] as const)
```

实现

```tsx
declare function PromiseAll<T extends readonly any[]>(
  values: readonly [...T]
): Promise<{ [P in keyof T]: T[P] extends Promise<infer R> ? R : T[P] }>;
```



## 21.Type Lookup

根据某个属性在联合类型中查找类型, 通过在联合类型`Cat | Dog`中搜索公共`type`字段来获取相应的类型

```tsx
interface Cat {
  type: 'cat'
  breeds: 'Abyssinian' | 'Shorthair' | 'Curl' | 'Bengal'
}

interface Dog {
  type: 'dog'
  breeds: 'Hound' | 'Brittany' | 'Bulldog' | 'Boxer'
  color: 'brown' | 'white' | 'black'
}

type MyDog = LookUp<Cat | Dog, 'dog'> // expected to be `Dog`
```

实现

```tsx
// type LookUp<U, T> = U extends { type: string } ? U['type'] extends T ? U: never :never

type LookUp<U, T> = U extends { type: T } ? U: never 
```

## 22.Trim

### (1)trim left

其中新返回的字符串删除了原字符串开头的空白字符串。

```tsx
type trimed = TrimLeft<'  Hello World  '> // 应推导出 'Hello World  '
```

实现

```tsx
type Empty = ' ' | '\n' | '\t';
type TrimLeft<S extends string> = S extends `${Empty}${infer R}` ? TrimLeft<R> : S;
```

### (2)trim

```tsx
type trimed = TrimLeft<'  Hello World  '> // 应推导出 'Hello World  
```

实现

```tsx
type Trim<S extends string> = S extends `${' ' | '\n' | '\t'}${infer U}` | `${infer U}${' ' | '\n' | '\t'}` ? Trim<U> : S

```

## 23.字符串大小写转换

### (1)首字母大写

```tsx
type capitalized = Capitalize<'hello world'> // expected to be 'Hello world'
```

实现

```tsx

type MyCapitalize<S extends string> = S extends `${infer F}${infer R}` ? `${Uppercase<F>}${R}` : S

```

### (2)KebabCase 

```tsx
type FooBarBaz = KebabCase<"FooBarBaz">;
const foobarbaz: FooBarBaz = "foo-bar-baz";

type DoNothing = KebabCase<"do-nothing">;
const doNothing: DoNothing = "do-nothing";
```

实现:Uncapitalize<Other>大写转换

```tsx
type KebabCase<Str extends string> =
  Str extends `${infer First}${infer Other}`
    ? Other extends Uncapitalize<Other>
      ? `${Lowercase<First>}${KebabCase<Other>}`
      : `${Lowercase<First>}-${KebabCase<Other>}`
    : Str;
```



## 24.replace

### (1)replace第一个字符串

实现 `Replace<S, From, To>` 将字符串 `S` 中的第一个子字符串 `From` 替换为 `To` 。

```tsx
type replaced = Replace<'types are fun!', 'fun', 'awesome'> // 期望是 'types are awesome!'
```

实现

```tsx
type Replace<S extends string, From extends string, To extends string> = From extends '' ? S : S extends `${infer Before}${From}${infer After}` ? `${Before}${To}${After}` : S
```

### (2)replace all

实现 `ReplaceAll<S, From, To>` 将一个字符串 `S` 中的所有子字符串 `From` 替换为 `To`。

```tsx
type replaced = ReplaceAll<'t y p e s', ' ', ''> // 期望是 'types'
```

实现

```tsx

type ReplaceAll<S extends string, From extends string, To extends string> = From extends '' ? S : ( S extends `${infer Left}${From}${infer Right}` ?`${Left}${To}${ReplaceAll<Right, From, To>}` : S)

```

## 25.追加参数

### (1)函数追加参数

实现一个泛型 `AppendArgument<Fn, A>`，对于给定的函数类型 `Fn`，以及一个任意类型 `A`，返回一个新的函数 `G`。`G` 拥有 `Fn` 的所有参数并在末尾追加类型为 `A` 的参数。

```tsx
type Fn = (a: number, b: string) => number

type Result = AppendArgument<Fn, boolean> 
// 期望是 (a: number, b: string, x: boolean) => number
```

实现

```tsx
type AppendArgument<Fn extends (...args: any) => any, A> = Fn extends  (...args: infer Args ) => infer Res ? (...arg: [...Args, A] ) =>  Res : never

```

### (2)对象追加

```tsx
type Test = { id: '1' }
type Result = AppendToObject<Test, 'value', 4> // expected to be { id: '1', value: 4 }
```

实现

```tsx
type AppendToObject<Obj, Key extends string, Value> = {
    [i in keyof Obj | Key]: i extends keyof Obj 
     ? Obj[i] : Value;
};
```



## 26.实现全排列

```tsx
type perm = Permutation<'A' | 'B' | 'C'>; 
// ['A', 'B', 'C'] | ['A', 'C', 'B'] | ['B', 'A', 'C'] | ['B', 'C', 'A'] | ['C', 'A', 'B'] | ['C', 'B', 'A']
```

实现

```tsx
// 官方文档中，介绍了一种操作，叫 Distributive conditional types
// 简单来说，传入给T extends U中的T如果是一个联合类型A | B | C，则这个表达式会被展开成
// (A extends U ? X : Y) | (B extends U ? X : Y) | (C extends U ? X : Y)
// [U] extends [never] 而不是 U extends never 因为  U是联合类型 条件类型会走分配得到的是一个联合类型  不符合期望
type Permutation<T, U = T> = [U] extends [never] ? [] : (T extends U  ? [T, ...Permutation<Exclude<U, T>>] : [])
```

## 27.Length of String

计算字符串的长度，类似于 `String#length` 。

```tsx
type StringToTuple<T extends string> = T extends `${infer First}${infer Rest}` ? [First, ...StringToTuple<Rest>] : [];

type Length<T extends string> = StringToTuple<T>["length"];
```

## 28.Flatten

返回扁平化的数组类型

```tsx
type flatten = Flatten<[1, 2, [3, 4], [[[5]]]]> // [1, 2, 3, 4, 5]
```

实现：

```tsx
type Flatten<T extends unknown[]> = T extends [infer First, ...infer Rest] ? [ ...(First extends any[] ? Flatten<First> : [First]), ...Flatten<Rest> ] : [];
```

## 29.Absolute

实现一个接收string,number或bigInt类型参数的`Absolute`类型,返回一个正数字符串。

```tsx
type Test = -100;
type Result = Absolute<Test>; // expected to be "100"
```

实现：

```tsx
//  返回的是字符串 ；另外`${T}`可以将数字转换成字符串
type Absolute<T extends number | string | bigint> = `${T}` extends `-${infer R}` ? `${R}` : `${T}`
```

## 30.类型转换

### (1)String to Union

```tsx
type Test = '123';
type Result = StringToUnion<Test>; // expected to be "1" | "2" | "3"
```

实现：

```tsx
type StringToUnion<S extends string> = S extends `${infer F}${infer R}` ? // 字面量 + infer定义，递归
    [F, ...StringToUnion<R>] :
    [];

```

## 31.Merge

将两个类型合并成一个类型，第二个类型的键会覆盖第一个类型的键。

```tsx
type foo = {
  name: string;
  age: string;
}

type coo = {
  age: number;
  sex: string
}

type Result = Merge<foo,coo>; // expected to be {name: string, age: number, sex: string}
```

实现

```tsx
type Merge<First, Second> = {
  [i in keyof First | keyof Second]:
    i extends keyof Second
      ? Second[i]
      : i extends keyof First ? First[i] : never;
}
```

## 32.diff

获取两个接口类型中的差值属性。

```tsx
type Foo = {
  a: string;
  b: number;
}
type Bar = {
  a: string;
  c: boolean
}

type Result1 = Diff<Foo,Bar> // { b: number, c: boolean }
type Result2 = Diff<Bar,Foo> // { b: number, c: boolean }
```

实现

```tsx
type Diff<O, O1> = {
  [K in Exclude<keyof (O & O1), keyof (O | O1)>]: (O & O1)[K]
}
```





感想：

+ 没涉及到很多，keyof, infer, T[number], readonly，T['length']
+ extends用的地方很多，用做判断
+ infer和typeof的用法不熟练
+ 有很多ts内置的方法，比如pick, parameters
+ 自己的解法不是最优的写法
