# Vue 强行读源码

## 附. 备忘板

> 有些全局性的或者额外性的东西记在这里

### 1. JS技术待研究

Vite: 打包工具

Piana: 状态管理工具

Vitest: 测试工具

Loadash: 万能工具箱

Prettier: 格式化代码工具,待研究

Electron: 桌面应用

PolyFill: 术语,用来代指使得代码能在旧浏览器上运行的代码

### 2. HoistStatic

setup的语法糖,就是

```
<script setup>
```



## 一. 依赖观察(过一遍)

Vue/core是一个大框架,内部包含很多小包.

一些元信息,版本,私有,包管理,还有esmodule指定

```js
"private": true,
"version": "3.5.13",
"packageManager": "pnpm@9.15.2",
"type": "module",
```

* 没有Dependencies,只有DevDependencies
* `Vite`依赖着`rollup`(生产)和`esbuild`(dev)打包
* `Babel`和`swc`负责编译(主要是源代码转换,考虑兼容性)
* 使用`typescript`
* `vitest`测试,`jsdom`在测试中模拟dom环境,`puppeteer `提供chromium

其它工具:

* `loadash`,**`enquirer`** (用于构建命令行交互,比如vue create)
* `npm-run-all2`: 能同时运行多个npm命令而不用一直|,有顺序和并行模式,如果要用就去查文档
* `serve`: 提供静态资源服务器,可以研究一下实现

既然没有dependencies,那应该可以直接分析代码了



