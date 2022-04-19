[toc]

# 简介

是一个测试工具

官网：https://www.jestjs.cn/docs/getting-started

# 基础使用

## 安装

```
npm install --save-dev jest
```

书写文件

sum.js文件

```
function sum(a, b) {
  return a + b;
}
module.exports = sum;
```

sum.test.js文件

```
const sum = require('./sum');

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

配置指令

```
{
  "scripts": {
    "test": "jest"
  }
}

jest --detectOpenHandles  参数是为了当句柄未正常关闭，显式报错给用户；
```

开始测试

```
npm run test
```

+ 适配import和es语法

```
https://juejin.cn/post/6844903888512876558
npm install --save-dev babel-jest @babel/core @babel/preset-env
```

```
// tsconfig.json
{
  "compilerOptions": {
    "target": "es5",
    "module": "ES2015",
    "lib": [
      "es6",
      "dom",
      "esnext"
    ],
    "declaration": true,
    "sourceMap": true,
    "outDir": "dist",
    "strict": true,
    "noImplicitAny": true,
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "moduleResolution": "node",
    "forceConsistentCasingInFileNames": true,
    "jsx": "react",
    "jsxFactory": "jsx",
    "downlevelIteration": true
  },
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "node_modules",
    "build"
  ]
}
```

```
// jest.config.ts
{
  "compilerOptions": {
    "target": "es5",
    "module": "ES2015",
    "lib": [
      "es6",
      "dom",
      "esnext"
    ],
    "declaration": true,
    "sourceMap": true,
    "outDir": "dist",
    "strict": true,
    "noImplicitAny": true,
    "resolveJsonModule": true,
    "esModuleInterop": true,
    "moduleResolution": "node",
    "forceConsistentCasingInFileNames": true,
    "jsx": "react",
    "jsxFactory": "jsx",
    "downlevelIteration": true
  },
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "node_modules",
    "build"
  ]
}
```



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