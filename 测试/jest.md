[toc]

# 简介

是一个测试工具

官网：https://www.jestjs.cn/docs/getting-started

# 基础使用

官网：https://jestjs.io/zh-Hans/docs/getting-started

安装方法：

## 匹配器

可以前边搭配not使用.not.toBe(4)

+ toBe：相等.toBe(4)

+ toBeCloseTo：浮点数相等toBeCloseTo(0.3)

+ toEqual：对象或者数组.toEqual({one: 1, two: 2})

+ toMatch：正则匹配toMatch(/stop/)

+ toContain：是否包含特定项toContain('milk')

+ toThrow：调用特定函数时是否抛出错误

  ```
  expect(() => compileAndroidCode()).toThrow('you are using the wrong JDK');
  ```

+ toBeNull,toBeUndefined,toBeDefined:检测是否为null,undefined...
+ toBeTruthy,toBeFalsy: if语句所判断的真或者假