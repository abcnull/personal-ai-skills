---
name: knl-lan-typescript
description: 当用户想要了解 typescript 语法，这篇文档可能可以提供帮助
disable-model-invocation: true
---

# knl-lan-typescript

此文主要讲解了 typescript 语法的一部分容易遗忘的知识内容

## 基本

TypeScript 完全兼容 JavaScript 语法的基础上，增加了可选的静态类型系统和基于类的面向对象编程能力。代码最终会被编译为纯 JavaScript，可以运行在任何支持 JavaScript 的环境中，包括浏览器、Node.js 和移动端

```typescript
// 注释
/*
注释
*/

// 数字类型，包括整数，浮点数
let age: number = 20;

// 数组
let numbers: number[] = [1, 2, 3, 4, 5];
let names: Array<string> = ["张三", "李四", "王五"];

// tuple 元组，已知数量和类型的数组
let person: [string, number] = ["Alice", 25];

// |
let id: string | number;
id = "123";
id = 456;

// 类型推断 typeof
```

## for 循环

```typescript
// for 循环
let j: any;
let n: any = "a b c";

for (j in n) {
  console.log(n[j]);
}

// for of 循环
let someArray = [1, "string", false];

for (let entry of someArray) {
  console.log(entry); // 1, "string", false
}

// for each
let list = [4, 5, 6];
list.forEach((val, idx, array) => {
  // val: 当前值
  // idx：当前index
  // array: Array
});

// for every
let list = [4, 5, 6];
list.every((val, idx, array) => {
  // val: 当前值
  // idx：当前index
  // array: Array
  return true; // Continues
  // Return false will quit the iteration
});
```

## 函数

```typescript
// 可选参数。可选参数必须跟在必需参数后面
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

// 剩余参数。我们不知道要向函数传入多少个参数
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
}
let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");

// 支持重载

// 普通函数创建自己的 this，箭头函数不会创建自己的 this，而是继承父作用域的 this
function Person1() {
    this.name = "Alice";
    // 普通函数会创建自己的 this
    // 在 setTimeout 回调中，this 指向 window（浏览器）或 undefined（严格模式）
    setTimeout(function() {
        console.log("普通函数: " + this.name);  // this.name 为 undefined
    }, 100);
}
function Person2() {
    this.name = "Bob";
    // 箭头函数不创建自己的 this
    // 它捕获外层的 this，所以能正确访问到 name
    setTimeout(() => {
        console.log("箭头函数: " + this.name);  // this.name 为 "Bob"
    }, 100);
}

// 构造函数
constructor(parameters) {
    // 初始化代码
}
```

## 泛型

```typescript
// 泛型
class Box<T> {
    value: T;
}
function identity<T>(arg: T): T {
    return arg;
}
```

## 装饰器
装饰器本质上是一个函数，它可以在运行时被调用，以修改目标对象的行为。装饰器目前是实验性功能，需要在 tsconfig.json 中显式启用

```typescript
function sealed(target: Function) {
    // 打印装饰器被应用到的类名
    console.log("装饰器 applied to: " + target.name);
    // 使用 Object.seal 锁定构造函数和原型
    // 防止在运行时添加或删除属性
    Object.seal(target);
    Object.seal(target.prototype);
}

// 使用 @ 语法将装饰器应用到类上
@sealed
class Person {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
}
```

## 模块
```typescript
// 导出
export var name = "Alice";
export { name as userName, age as userAge };

// 默认导出：一个模块只能有一个默认导出
export default function add(a: number, b: number): number {
    return a + b;
}

// 导入
import { name, age, greet } from "./user"; // 从模块中导入指定的内容
import User from "./user"; // 从模块中导入默认导出的内容
```

## 异步

```typescript
// async 函数自动返回 Promise
async function greet(): Promise<string> {
  return "Hello, World!";
}
greet().then(function (result) {
  console.log("结果: " + result);
});

// await 等待 Promise 完成并获取结果
```

## tsconfig.json

tsconfig.json 是 TypeScript 项目的配置文件，用于指定编译选项和项目设置

基础配置如下：

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "strict": true,
    "outDir": "./dist"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

- target：编译目标 JavaScript 版本
- module：使用的模块系统
- strict：启用所有严格类型检查
- outDir：输出目录
