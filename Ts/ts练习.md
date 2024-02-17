[toc]

练习题目

https://github.com/type-challenges/type-challenges



# 实战

## 1.类型拓展

ts实现一个类型，该类型必须拥有某个类型的所有属性，并对它进行拓展，使之可以添加任意类型的键和值

+ 使用交叉类型

```tsx
type ExtendType<T, U extends Record<string, any>> = T & U;

// 用法示例
interface Base {
  id: number;
  name: string;
}

const extendedObject: ExtendType<Base, { additionalKey: any }> = {
  id: 1,
  name: "Example",
  additionalKey: "Some Value",
};

console.log(extendedObject);

```



# 练习题目

## 1.排除&挑选&融合

### (1)Pick

#### 实现内置的pick

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

实现

```tsx
type MyPick<T, K extends keyof T> = { 
  [P in K]: T[P]; 
}
```

#### PickByType

实现 `PickByType<P, Q>`，将对象 `P` 中类型为 `Q` 的 key 保留：

```tsx
type OnlyBoolean = PickByType<{
  name: string
  count: number
  isReadonly: boolean
  isEnable: boolean
}, boolean> // { isReadonly: boolean; isEnable: boolean; }
```

实现

```tsx
type PickByType<P, Q> = {
  [K in keyof P as P[K] extends Q ? K : never]: P[K]
}
```

#### OmitByType

实现 `OmitByType<T, U>` 根据类型 U 排除 T 中的 Key：

```tsx
type OmitBoolean = OmitByType<
  {
    name: string
    count: number
    isReadonly: boolean
    isEnable: boolean
  },
  boolean
> // { name: string; count: number }
```

实现

```tsx
type OmitByType<T, U> = {
  [K in keyof T as T[K] extends U ? never : K]: T[K]
}
```

### (2)实现内置的Exclude

从联合类型T中排除U的类型成员，来构造一个新的类型。

```tsx
type Result = MyExclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
```

正确解法：

```tsx
type MyExclude<T, U> = T extends U ? never : T
```

如果T中元素在U中有，则通过never移除

#### 差集和交集

```
type Diff<T, U> = T extends U ? never : T; // 找出T的差集
type Filter<T, U> = T extends U ? T : never; // 找出交集
```

### (3)实现内置的Omit<T, K>

`Omit` 会创建一个省略 `K` 中字段的 `T` 对象， 可以用于extend的时候有属性冲突的时候，这时候只继承其中一部分就可以了

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

as的用法

断言、强制类型转换

### (4)Merge

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

### (5)diff

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

A 与 B 的 Diff 是找到 A 存在 B 不存在，与 B 存在 A 不存在的值，那么正好可以利用 `Exclude<X, Y>` 函数，它可以得到存在于 `X` 不存在于 `Y` 的值，我们只要用 `keyof A`、`keyof B` 代替 `X` 与 `Y`，并交替 A、B 位置就能得到 Diff

```tsx
type Diff<A, B> = {
  [K in Exclude<keyof A, keyof B> | Exclude<keyof B, keyof A>]:
    K extends keyof A ? A[K] : (
      K extends keyof B ? B[K]: never
    )
```



## 2. 类型转换

### (1)对象或数组转联合类型

用 `[number]` 作为下标

```tsx
['1', '2', '3']['number'] // '1' | '2' | '3'
```

对象的方式则是 `[keyof T]` 作为下标：

```tsx
type ObjectToUnion<T> = T[keyof T]
```

### (2)元组转换为对象 

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

### (3)元组转循环对象

实现 `TupleToNestedObject<T, P>`，其中 `T` 仅接收字符串数组，`P` 是任意类型，生成一个递归对象结构，满足如下结果：

```tsx
type a = TupleToNestedObject<['a'], string> // {a: string}
type b = TupleToNestedObject<['a', 'b'], number> // {a: {b: number}}
type c = TupleToNestedObject<[], boolean> // boolean. if the tuple is empty, just return the U type
```

实现

```tsx
type TupleToNestedObject<T, U, R = U> = T extends [] ? R : (
  T extends [...infer Rest, infer Last extends PropertyKey] ? (
    TupleToNestedObject<Rest, U, {
      [P in Last]: R
    }>
  ) : never
)
```

### (4) 把对象转换为联合类型

实现 TS 版本的 `Object.entries`：

联合类型每一项都是数组

```tsx
interface Model {
  name: string;
  age: number;
  locations: string[] | null;
}
type modelEntries = ObjectEntries<Model> // ['name', string] | ['age', number] | ['locations', string[] | null];
```

实现：

```tsx
type ObjectEntries<T> = {
  [K in keyof T]: [K, T[K]]
}[keyof T]
```

让 Key 位置和value位置不出现 `undefined`

```tsx
type RemoveUndefined<T> = [T] extends [undefined] ? T : Exclude<T, undefined>

type ObjectEntries<T> = {
  [K in keyof T]-?: [K, RemoveUndefined<T[K]>]
}[keyof T]
```

### (4)元组转合集

实现泛型`TupleToUnion<T>`，它返回元组所有值的合集。

```tsx
type Arr = ['1', '2', '3']
type Test = TupleToUnion<Arr> // expected to be '1' | '2' | '3'
```

正确实现

```tsx
type TupleToUnion<T extends readonly (number | string)[]> = T[number]

```

## 3.数组array操作

### (1)push

在类型系统里实现通用的 `Array.push`

```tsx
type Result = Push<[1, 2], '3'> // [1, 2, '3']
```

实现

```tsx
type Push<T extends any[], U> = [...T, U]
```

### (2)unshift

实现类型版本的 `Array.unshift`。

```tsx
type Result = Unshift<[1, 2], 0> // [0, 1, 2,]
```

实现

```tsx
type Push<T extends any[], U> = [U,...T]
```

### (3)shift

```tsx
type Result = Shift<[3, 2, 1]> // [2, 1]
```

实现

```tsx
type Shift<T> = T extends [infer First, ...infer Rest] ? Rest : never
```

### (4)concat

```tsx
type Result = Concat<[1], [2]> // expected to be [1, 2]
```

实现

```tsx
type Contact<T extends any[], U extends any[]> = [...T, ...U]
```



### (5)Reverse

```tsx
type a = Reverse<['a', 'b']> // ['b', 'a']
type b = Reverse<['a', 'b', 'c']> // ['c', 'b', 'a']
```

实现

```tsx
type Reverse<T extends any[]> = T extends [...infer Rest, infer End] ? [End, ...Reverse<Rest>] : T
```

### (6)Flatten

返回扁平化的数组类型

```tsx
type flatten = Flatten<[1, 2, [3, 4], [[[5]]]]> // [1, 2, 3, 4, 5]
```

实现：

```tsx
type Flatten<T extends unknown[]> = T extends [infer First, ...infer Rest] ? 
   [ ...(First extends any[]
       ? Flatten<First> 
       : [First]),
     ...Flatten<Rest> ]
   : [];
```

### (7)FlattenDepth

实现指定深度的 Flatten：

```tsx
type a = FlattenDepth<[1, 2, [3, 4], [[[5]]]], 2> // [1, 2, 3, 4, [5]]. flattern 2 times
type b = FlattenDepth<[1, 2, [3, 4], [[[5]]]]> // [1, 2, 3, 4, [[5]]]. Depth defaults to be 1
```

实现

```tsx
type FlattenOnce<T extends any[], U extends any[] = []> = T extends [infer X, ...infer Y] ? (
  X extends any[] ? FlattenOnce<Y, [...U, ...X]> : FlattenOnce<Y, [...U, X]>
) : U


type FlattenDepth<
  T extends any[],
  U extends number = 1,
  P extends any[] = []
> = P['length'] extends U ? T : (
  FlattenOnce<T> extends T ? T : (
    FlattenDepth<FlattenOnce<T>, U, [...P, any]>
  )
```

备注：如果打平后已经是平的，就不用再继续递归了，此时可以用 `FlattenOnce<T> extends T` 判断：

```tsx
// FlattenOnce
type FlattenDepth<
  T extends any[],
  U extends number = 1,
  P extends any[] = []
> = P['length'] extends U ? T : (
  FlattenDepth<FlattenOnce<T>, U, [...P, any]>
)
```

### (8)第一个元素

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

### (9)获取元组长度

```tsx
type tesla = ['tesla', 'model 3', 'model X', 'model Y']
type teslaLength = Length<tesla> // expected 4
```

实现

```tsx
type Length<T extends readonly any[]> = T['length']
```

### (10)Includes

在类型系统里实现 JavaScript 的 `Array.includes` 方法，这个类型接受两个参数，返回的类型要么是 `true` 要么是 `false`

```
type isPillarMen = Includes<['Kars', 'Esidisi', 'Wamuu', 'Santana'], 'Dio'> // expected to be `false
```

正确解法：

```
type MyEqual<X, Y> =
  (<T>() => T extends X ? 1 : 2) extends
  (<T>() => T extends Y ? 1 : 2) ? true : false

type Includes<T extends readonly any[], U> = T extends [infer P, ...infer Rest] 
  ? MyEqual<P, U> extends true 
    ? true
    : Includes<Rest, U>
  : false;
```

- 递归取T中第一项与U进行判断是否相等

### (11)最后一个元素

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

### (12)anyof

类型接收一个数组，如果数组中任一个元素为真，则返回 `true`，否则返回 `false`。如果数组为空，返回 `false`。

```
type Sample1 = AnyOf<[1, '', false, [], {}]> // expected to be true.
type Sample2 = AnyOf<[0, '', false, [], {}]> // expected to be false.
```

实现

```
//Record<string, never>空对象
type FalseType = 0 | '' | false | [] | Record<string, never>

type AnyOf1<T extends readonly any[]> = T[number] extends FalseType ? false : true
```

## .字符串大小写转换

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

### (3)StartsWith

实现 `StartsWith<T, U>` 判断字符串 `T` 是否以 `U` 开头：

```go
type a = StartsWith<'abc', 'ac'> // expected to be false
type b = StartsWith<'abc', 'ab'> // expected to be true
type c = StartsWith<'abc', 'abcd'> // expected to be false
```

实现：

+ 可以用 `${string}` 匹配任意字符串进行 `extends` 判定, 也可以用 `${infer X}`

```tsx
type StartsWith<T extends string, U extends string> = T extends `${U}${string}`
  ? true
  : false
```

### (4)EndsWith

实现：

```tsx
type EndsWith<T extends string, U extends string> = T extends `${string}${U}`
  ? true
  : false
```

## 5.Trim

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

## 6.replace

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

### (3)从字符串中移除指定字符

```tsx
type Butterfly = DropChar<' b u t t e r f l y ! ', ' '> // 'butterfly!'
```

实现

```tsx
type DropChar<S, C extends string> = S extends `${infer A}${C}${infer B}` ? 
  `${A}${DropChar<B, C>}` : S
```

### (4)ReplaceKeys

实现 `ReplaceKeys<Obj, Keys, Targets>` 将 `Obj` 中每个对象的 `Keys` Key 类型转化为符合 `Targets` 对象对应 Key 描述的类型，如果无法匹配到 `Targets` 则类型置为 `never`：

```go
type NodeA = {
  type: 'A'
  name: string
  flag: number
}
 
type NodeB = {
  type: 'B'
  id: number
  flag: number
}
 
type NodeC = {
  type: 'C'
  name: string
  flag: number
}
 
 
type Nodes = NodeA | NodeB | NodeC
 
type ReplacedNodes = ReplaceKeys<Nodes, 'name' | 'flag', {name: number, flag: string}> // {type: 'A', name: number, flag: string} | {type: 'B', id: number, flag: string} | {type: 'C', name: number, flag: string} // would replace name from string to number, replace flag from number to string.
 
type ReplacedNotExistKeys = ReplaceKeys<Nodes, 'name', {aa: number}> // {type: 'A', name: never, flag: number} | NodeB | {type: 'C', name: never, flag: number} // would replace name to never
```

实现

```tsx
type ReplaceKeys<Obj, Keys, Targets> = {
  [K in keyof Obj] : K extends Keys ? (
    K extends keyof Targets ? Targets[K] : never
  ) : Obj[K]
}
```

## 7.类型判断

### (1)IsNever

判断是否为never类型

```tsx
type A = IsNever<never>  // expected to be true
type B = IsNever<undefined> // expected to be false
type C = IsNever<null> // expected to be false
type D = IsNever<[]> // expected to be false
type E = IsNever<number> // expected to be false
```

实现

+ never不会触发 `extends` 判断，而是直接终结，致使判断无效，绕过 `never` 这个特性即可。

```
type IsNever<Value> = [Value] extends [never] ? true : false;
```

### (2)IsUnion

```tsx
type case1 = IsUnion<string>  // false
type case2 = IsUnion<string|number>  // true
type case3 = IsUnion<[string|number]>  // false
```

实现

+ 联合类型的特征只有两个：

1. 在 TS 处理泛型为联合类型时进行分发处理，即将联合类型拆解为独立项一一进行判定，最后再用 `|` 连接。

   ```
   A extends A
   // 如果 A 是 1 | 2，分发结果是：
   (1 extends 1 | 2) | (2 extends 1 | 2)
   ```

2. 用 `[]` 包裹联合类型可以规避分发的特性。

```tsx
type IsUnion<A> = A extends A ? (
  [A] extends [A] ? false : true
) : false
```

但这段代码依然不正确，因为在第一个三元表达式括号内，`A` 已经被分发，所以 `[A] extends [A]` 即便对联合类型也是判定为真的，

修改为

```
type IsUnion<A, B = A> = A extends A ? (
  [B] extends [A] ? false : true
) : false
```

最终实现

```tsx
type IsUnion<A, B = A> = IsNever<A> extends true ? false : (
  A extends A ? (
    [B] extends [A] ? false : true
  ) : false

```

## 

## 8.追加参数

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

## 9.改变key的属性

### (1)变为可选

实现一个通用的`PartialByKeys<T, K>`，它接收两个类型参数`T`和`K`，使得K变为可选的

```tsx
interface User {
  name: string
  age: number
  address: string
}

type UserPartialName = PartialByKeys<User, 'name'> // { name?:string; age:number; address:string }
```

实现

```tsx
type Merge<T> = {
  [K in keyof T]: T[K]
}
type PartialByKeys<T, K extends PropertyKey = keyof T> = Merge<
  Partial<T> & Omit<T, K>
>
//  Omit<T, K>省略T中K字段
//
//Merge<{
//  a: number
//} & {
//  a?: number
//}> 结果：{ a: number }，同一个 Key 可选与必选同时存在时，合并结果是必选
```

### (2)变为必选

实现 `RequiredByKeys<T, K>`，使 `K` 匹配的 Key 变成必选的定义，如果不传 `K` 效果与 `Required<T>` 一样：

```tsx
interface User {
  name?: string
  age?: number
  address?: string
}
 
type UserRequiredName = RequiredByKeys<User, 'name'> // { name: string; age?: number; address?: string }
```

实现：

+ 还要兼容 `Pick` 访问不存在的 Key，用 `extends` 躲避一下即可：

```tsx
type Merge<T> = {
  [K in keyof T]: T[K]
}
type RequiredByKeys<T, K extends PropertyKey = keyof T> = Merge<
  T & Required<Pick<T, K extends keyof T ? K : never>>
>
```

### (3)将对象 `T` 的所有 Key 变得可写

```tsx
interface Todo {
  readonly title: string
  readonly description: string
  readonly completed: boolean
}
 
type MutableTodo = Mutable<Todo> // { title: string; description: string; completed: boolean; }
```

实现：

```tsx
type Mutable<T extends object> = {
  -readonly [K in keyof T]: T[K]
}
```

### (4)实现 Readonly

#### 只有一个参数

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
```

实现

```tsx
type MyReadonly<T> = {
  readonly [K in keyof T]: T[K]
}
```

#### 两个参数

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
```

#### 深度 Readonly

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

### (5)把对象 `<T>` 中 Index 下标移除

```tsx
type Foo = {
  [key: string]: any;
  foo(): void;
}
 
type A = RemoveIndexSignature<Foo>  // expected { foo(): void }
```

实现

+ 如何将对象字符串 Key 识别出来，可以用 `${infer P}` 是否能识别到 `P` 来判断当前是否命中了字符串 Key：

```tsx
type RemoveIndexSignature<T> = {
  [K in keyof T as K extends `${infer P}` ? P : never]: T[K]
}
```

## 

## 10.数字操作

### (1)Absolute

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

### (2)减一

给定一个正整数作为类型的参数，要求返回的类型是该数字减 1。

```tsx
type Zero = MinusOne<1> // 0
type FiftyFour = MinusOne<55> // 54
```

实现：

+ TS 没有 “普通” 的运算能力，但涉及数字却有一条生路，即 TS 可通过 `['length']` 访问数组长度，几乎所有数字计算都是通过它推导出来的。

```tsx
type MinusOne<T extends number, arr extends any[] = []> = [
  ...arr,
  ''
]['length'] extends T
  ? arr['length']
  : MinusOne<T, [...arr, '']>
```

## 

## 11.类型转换

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

## 

## 12.函数相关

### ()实现内置的parameters

```tsx
const foo = (arg1: string, arg2: number): void => {}

type FunctionParamsType = MyParameters<typeof foo> // [arg1: string, arg2: number]
```

实现：

```tsx
type MyParameters<T extends (...args: any[]) => any> = T extends (...args: infer P) => unknown ? P : never;
```

+ 使用infer表示待推断的类型变量。

  由于...args本身已经是元组类型，因此infer P最终推导出的，也是元组类型

### (2)实现内置的ReturnType<T>

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

```tsx
type MyReturnType<T extends (...args: any[]) => unknown> = T extends (...args: any[]) => infer R ? R : never;
```

### (3)将函数 `T` 的参数类型反转

```tsx
type Flipped = FlipArguments<(arg0: string, arg1: number, arg2: boolean) => void> 
// (arg0: boolean, arg1: number, arg2: string) => void
```

实现

```tsx
type Reverse<T extends any[]> = T extends [...infer Rest, infer End] ? [End, ...Reverse<Rest>] : T
 
type FlipArguments<T> =
  T extends (...args: infer Args) => infer Result ? (...args: Reverse<Args>) => Result : never
```

## 13. Promise 

### (1)获取promise返回的类型

在 TS 中，我们用Promise<T> 中的 T 来描述这个 Promise 返回的类型。实现一个类型，可以获取这个类型。

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

### (2)Promise.all

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



## 其他

### (1)实现IF

实现一个 `IF` 类型，它接收一个条件类型 `C` ，一个判断为真时的返回类型 `T` ，以及一个判断为假时的返回类型 `F`

```tsx
type A = If<true, 'a', 'b'>  // expected to be 'a'
type B = If<false, 'a', 'b'> // expected to be 'b'
```

正确解法：

```tsx
if<C extends boolean, T, F> = C extends true ? T : F
```

### (2)可串联构造器

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

### ()出堆pop

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

### (4)Type Lookup

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

### ()实现全排列

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

### ()Length of String

计算字符串的长度，类似于 `String#length` 。

```tsx
type StringToTuple<T extends string> = T extends `${infer First}${infer Rest}` ? [First, ...StringToTuple<Rest>] : [];

type Length<T extends string> = StringToTuple<T>["length"];
```

### (7)正则匹配

实现类型 PercentageParser。根据规则 `/^(\+|\-)?(\d*)?(\%)?$/` 匹配类型 T。

匹配的结果由三部分组成，分别是：[`正负号`, `数字`, `单位`]，如果没有匹配，则默认是空字符串。

```TS
type PString1 = ''
type PString2 = '+85%'
type PString5 = '85'

type R1 = PercentageParser<PString1> // expected ['', '', '']
type R2 = PercentageParser<PString2> // expected ["+", "85", "%"]
type R5 = PercentageParser<PString5> // expected ["", "85", ""]
```

实现：

+ TS 没有正则能力，尽量还是不要做正则的事情，只能用枚举

  ```tsx
  type PercentageParser<A extends string> = 
    // +/-xxx%
    A extends `${infer X extends '+' | '-'}${infer Y}%`? [X, Y, '%'] : (
      // +/-xxx
      A extends `${infer X extends '+' | '-'}${infer Y}` ? [X, Y, ''] : (
        // xxx%
        A extends `${infer X}%` ? ['', X, '%'] : (
          // xxx 包括 ['100', '%', ''] 这三种情况
          A extends `${infer X}` ? ['', X, '']: never
        )
      )
    )
  ```

### (8)BEM

```tsx
type BEM<B extends string, E extends string[], M extends string[]> = 
  `${B}__${E[number]}--${M[number]}`
```

实现

当传入值不存在时返回空字符串，保证安全的跳过

```tsx
// IsNever, SafeUnion
type IsNever<TValue> = TValue[] extends never[] ? true : false;
type SafeUnion<TUnion> = IsNever<TUnion> extends true ? "" : TUnion;

type BEM<B extends string, E extends string[], M extends string[]> = 
  `${B}${SafeUnion<`__${E[number]}`>}${SafeUnion<`--${M[number]}`>}`
```

### (9)二叉树遍历

#### 中序遍历

```tsx
const tree1 = {
  val: 1,
  left: null,
  right: {
    val: 2,
    left: {
      val: 3,
      left: null,
      right: null,
    },
    right: null,
  },
} as const
 
type A = InorderTraversal<typeof tree1> // [1, 3, 2]
```

实现

```
//JS版
function inorderTraversal(tree) {
  if (!tree) return []
  return [
    ...inorderTraversal(tree.left),
    res.push(val),
    ...inorderTraversal(tree.right)
  ]
}
```

```tsx
// 本题答案
interface TreeNode {
  val: number
  left: TreeNode | null
  right: TreeNode | null
}
type InorderTraversal<T extends TreeNode | null> = [T] extends [TreeNode] ? (
  [
    ...InorderTraversal<T['left']>,
    T['val'],
    ...InorderTraversal<T['right']>
  ] 
): []
```

### (10)Flip

实现 `Flip<T>`，将对象 `T` 中 Key 与 Value 对调：

```tsx
Flip<{ a: "x", b: "y", c: "z" }>; // {x: 'a', y: 'b', z: 'c'}
Flip<{ a: 1, b: 2, c: 3 }>; // {1: 'a', 2: 'b', 3: 'c'}
Flip<{ a: false, b: true }>; // {false: 'a', true: 'b'}
```

实现

+ Key 位置只能是 String or Number,所以需要限制value的类型
+ true必须转换为字符串`true`

```tsx
type Flip<T extends Record<string, string | number | boolean>> = {
  [K in keyof T as `${T[K]}`]: K
}
```

### (11)实现斐波那契数列计算

```tsx
type Result1 = Fibonacci<3> // 2
type Result2 = Fibonacci<8> // 21
```

实现

+ TS 版我们只能用数组长度模拟计算

```tsx
type Fibonacci<T extends number, N = [1]> = N['length'] extends T ? (
  // xxx
) : Fibonacci<T, [...N, 1]>
```

```ts
type Fibonacci<
  T extends number,
  N extends number[] = [1],
  Prev extends number[] = [1],
  Cur extends number[] = [1]
> = N['length'] extends T
  ? Prev['length']
  : Fibonacci<T, [...N, 1], Cur, [...Prev, ...Cur]>
```



### (12)Partial

统一把接口的属性都变味可选的，不是必填的









参考：

+ 精读《MinusOne, PickByType, StartsWith...》：https://blog.csdn.net/ascoders/article/details/125734435

+ 精读《Diff, AnyOf, IsUnion...》：https://blog.csdn.net/ascoders/article/details/125611339



