# Redux

[TOC]

## 一. 基本概念

### 1. 单向数据流(one-way data flow)

> state---(渲染)->view--(调用)-->actions-(改变)->state

`redux`应用中使用集中式的全局状态来管理，并明确更新状态的模式，以便让代码具有可预测性。

所有state应该是immutable(不可变的)

### 2. 术语

#### 2.1 action

`action`是一个包含`type`字段的__js对象__

`type`是一个字符串,描述action的行为,一般形式为"domain/name",通常设置`payload`字段来描述一些附加行为

```js
const addTodoAction = {
  type: 'todos/todoAdded',
  payload: 'Buy milk'
}
```

#### 2.2 action creator

一个函数返回action,方便创建action对象

```js
const addTodo = text => {
  return {
    type: 'todos/todoAdded',
    payload: text
  }
}
```

#### 2.3 reducer

实际action调用者,是__函数__,形式为

```js
(state,action)=>newState
```

类似于事件监听器,通过接收到的action处理事件转换状态(亦有初始化state的功能)

* 原则:
  * 仅使用state和action计算新值(不受外界影响)
  * 禁止直接修改state,只能用复制(比如拓展运算)来更新并返回
  * 禁止副作用

* 行为:
  * 检测是否关心action的type,如果不关心则返回原来state就行

```js
const initialState = { value: 0 }

function counterReducer(state = initialState, action) {
  // 检查 reducer 是否关心这个 action
  if (action.type === 'counter/increment') {
    // 如果是，复制 `state`
    return {
      ...state,
      // 使用新值更新 state 副本
      value: state.value + 1
    }
  }
  // 返回原来的 state 不变
  return state
}
```

#### 2.4 store

redux中的state存储在store中,拥有`getState`方法

```js
import { configureStore } from '@reduxjs/toolkit'

const store = configureStore({ reducer: counterReducer })

console.log(store.getState())
// {value: 0}
```

#### 2.5 dispatch

`dispatch`是__store的方法__,传入__action__以触发reducer改变state,一般跟action creator配合

```js
const increment = () => {
  return {
    type: 'counter/increment'
  }
}

store.dispatch(increment())

console.log(store.getState())
// {value: 2}
```

#### 2.6 selector

是__函数__,从store的状态树中挑选片段

> 传入state,返回操作后的state片段

```js
const selectCounterValue = state => state.value

const currentValue = selectCounterValue(store.getState())
console.log(currentValue)
// 2
```

### 3. 数据流

![数据流更新动画](https://cn.redux.js.org/assets/images/ReduxDataFlowDiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif)

* 应用启动时:
  * 使用顶层root reducer创建store
  * store调用root reducer,将返回值作为初始state
  * 视图组件访问并监听store
* 更新时:
  * dispatch到store
  * store调用reducer
  * store通知所有__订阅者视图__,发现数据更新的视图重新渲染

## 二. react应用结构

- /src
  - `index.js`: app 入口
  - `App.js`: 顶级 React 组件
  - /app
    - `store.js`: 创建 Redux store 实例
  - /features
    - /counter
      - `Counter.js`: 展示 counter 特性的 React 组件
      - `counterSlice.js`: counter 特性相关的 redux 逻辑

### 1. store.js

```js
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from '../features/counter/counterSlice'

export default configureStore({
  reducer: {
    counter: counterReducer
  }
})
```

configureStore要求我们传入一个至少包含reducer的对象,并用属性给该reducer命名

### 2. slice

`slice`是指应用中__单个功能的reducer和action的逻辑整合__

```js
//counterSlice.js
import { createSlice } from '@reduxjs/toolkit'

export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0,
  },
  reducers: {
    increment: (state) => {
      state.value += 1
    },
    decrement: (state) => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    },
  },
})
export default counterSlice.reducer
```

#### 2.1 slice reducer

如该代码所示,reducers被分解成了函数属性,而实际上`createSlice`会自动组合`name`和`reducer方法名`==`name/reducer method`,并用action type匹配计算

`initialState`初始化了该reducer的state

---

而且这些方法是可以由slice显式调用的,使用actions方法

```js
counterSlice.actions.increment()
```

reducer也可以直接调用

```js
const newState = counterSlice.reducer(
  { value: 10 },
  counterSlice.actions.increment()
)
console.log(newState)
// {value: 11}
```

