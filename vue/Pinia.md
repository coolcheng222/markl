# Pinia

## 一. 介绍

Pinia 是 vuex的平替或者上位替代,由vue团队开发,负责状态管理,也拥有Store等要素,不过稍微优化一些

1. 取消Mutation,仅保留Actions
2. 可以用组合式语法定义
3. 取消嵌套结构

## 二. 安装和引入

安装: 

```bash
yarn add pinia
# 或者使用 npm
npm install pinia
```

官方指南还推荐了关于vue-cli的一个非官方插件

```bash
vue add vue-cli-plugin-pinia
```

将pinia注册进app

```js
import { createPinia } from 'pinia'

createApp(App).use(createPinia()).mount('#app')
```

## 三. 基础语法

### 1. 定义Store

使用`defineStore()`方法来定义store

```js
import { defineStore } from 'pinia'
```

#### 1.1 选项式

pinia的定义语法也分选项式和组合式,我们可以看到选项式和vuex极其相似

```js
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0 }),
  getters: {
    double: state => state.count * 2,
  },
  actions: {
    increment() {
      this.count++
    },
  },
})
```

#### 1.2 组合式

变量ref就是state,方法就是action,compouted就是getter

```js
export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const doubleCount = computed(() => count.value * 2)
  function increment() {
    count.value++
  }

  return { count, doubleCount, increment }
})
```

