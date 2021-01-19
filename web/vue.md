# TypeScript

## 一. 介绍



### TypeScript 的介绍

### 1. 介绍

> TS是JS的超集

TypeScript是一种由微软开发的开源、跨平台的编程语言。它是JavaScript的超集，最终会被编译为JavaScript代码。

2012年10月，微软发布了首个公开版本的TypeScript，2013年6月19日，在经历了一个预览版之后微软正式发布了正式版TypeScript

TypeScript的作者是安德斯·海尔斯伯格，C#的首席架构师。它是开源和跨平台的编程语言。

TypeScript扩展了JavaScript的语法，所以任何现有的JavaScript程序可以运行在TypeScript环境中。

TypeScript是为大型应用的开发而设计，并且可以编译为JavaScript。

TypeScript 是 JavaScript 的一个超集，主要提供了类型系统和对 ES6+ 的支持**，它由 Microsoft 开发，代码开源于 GitHub 上

* TS一般需要编译形成JS才能在浏览器使用,可以编译出纯净简介的js代码,包括Node.js
* TS建立在ES6之上
* TS包含了类型系统等先进特性

### 2. 安装

```java
npm install -g typescript
```

检查

```java
tsc -v
```

### 3. 编写

文件名后缀`.ts`

编译: `tsc 文件名`

如果ts文件中直接书写js语法,则html可以直接引入

如果ts文件有了ts代码则需要先编译成js再引入

### 4. 自动编译

1. 生成tsconfig.json,在文件夹的目录下使用`tsc -init`
2. 修改json中的outDir输出目录,不用严格模式(看喜好)

## 二. 简单语法demo

### 1.类型注解

轻量级的为函数或者变量添加的约束

```javascript
function showMsg(str:string){
   return "aaa" + str;
}
let msg = [10,20,30];
showMsg(msg);//报错,主要是提示信息,但是不影响它编译
```

> Error:(6, 13) TS2345: Argument of type 'number[]' is not assignable to parameter of type 'string'.

### 2. 接口

主要是一种约束(规范),跟类型注解搭配使用

若使用的变量没有满足约束就会编译报错

```typescript
interface IPerson{
    firstName:string
    lastName:string
}
function showFullName(person:IPerson){
    return person.firstName + "_" + person.lastName;//确保这里不会出错
}
```

### 3. 类

```javascript

class Person{
        firstName:string
        lastName:string
        fullName:string

        constructor(firstName:string,lastName:string) {
            this.firstName = firstName;
            this.lastName = lastName;
            this.fullName = firstName + "_" + this.lastName;
        }
    }
```

