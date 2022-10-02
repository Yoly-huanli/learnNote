+ Promise<T> 

  可以代表Promise的resolve值是T类型
  
+ keyof

  ```
  // 可以获取一个对象接口的所有 key 值
    type a = {
      b: string,
      c: string
    }
    type foo =  keyof a
    // type foo = 'b'| 'c'
  ```
  
  
  
+ **A spread argument must either have a tuple type or be passed to a rest parameter.**

  剩余参数

```js
 const res = await numberToCurrencyPlus(...arg)
 
const numberToCurrencyPlus: numberToCurrencyPlus = function(numberParam, isHighlight){
  return numberToCurrency(numberParam, isHighlight)
}
```

+ Parameter 'arg' implicitly has an 'any' type, but a better type may be inferred from usage.

```
应该为变量指定类型
```

+ Accessors are only available when targeting ECMAScript 5 and higher.

```
tsc  array-rotate.ts -t es5
```

+ 注释

```
# 注释
// @ts-ignore
```
