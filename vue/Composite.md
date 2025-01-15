# 组合式API

所谓的**组合式AP**I,就是将组建的定义集合到一个**setup函数**中

## 一. setup

setup是组件的一个option,只会执行一次,其返回对象的属性和方法,组件可以直接使用

setup是组合API的入口函数

### 1. 实例

对于数据count,我们想要其根据按钮的点击而增加,使用setup来实现

```js
setup(){
    let count = 0;
    function updateCount(){
        count++
    }
    return {
        count,
        updateCount
    }
}
```

但是此时count并不是响应式的,不会随着方法的改变而变化

解决:

<u>引入ref</u>(一个函数,定义一个响应式数据),一般用于定义__基本数据类型__响应式数据

```js
import {ref} from "@vue/reactivity";
```

__响应式数据使用__

```js
setup(){
    const count = ref(0)
    function updateCount(){
        count.value++ // 操作值时需要用.value,html渲染时不需要
    }
    return {
        count,
        updateCount
    }
}
```

* reactive的基本使用
  * reactive和ref类似,对于对象/复杂数据使用
  * 返回的是该对象的__代理对象__,

### 2. 组合式API对应选项式API

#### 2.1 data和methods

data对应setup返回值中的普通属性

methods对应setup返回值中的普通方法

#### 2.2 钩子

选项式中对应的钩子,名称首字母大写并加上on,即可在setup中使用

对于before开头的也一样

`mounted`- `onMounted`

| 选项式 API        | Hook inside `setup` |
| ----------------- | ------------------- |
| `beforeCreate`    | Not needed*         |
| `created`         | Not needed*         |
| `beforeMount`     | `onBeforeMount`     |
| `mounted`         | `onMounted`         |
| `beforeUpdate`    | `onBeforeUpdate`    |
| `updated`         | `onUpdated`         |
| `beforeUnmount`   | `onBeforeUnmount`   |
| `unmounted`       | `onUnmounted`       |
| `errorCaptured`   | `onErrorCaptured`   |
| `renderTracked`   | `onRenderTracked`   |
| `renderTriggered` | `onRenderTriggered` |

> 注意: 所有钩子方法需要从vue引入
>
> ```js
> import {onMounted} from "@vue/runtime-core";
> ```

使用: 在setup中调用并传入回调函数

```js
setup(){
     //....
    onMounted(()=>console.log("aaaa"));
    return {
        count,
        person,
        updateCount
    }
}
```

#### 2.3 watch

watch也作为函数调用,传入:

* 第一个参数: 属性名,响应式对象,返回对象的函数
* 第二个参数: 回调函数function(new,old)

```js
import { ref, watch } from 'vue'

const counter = ref(0)
watch(counter, (newValue, oldValue) => {
  console.log('The new counter value is: ' + counter.value)
})
```

#### 2.4 computed

引入computed函数

```js
import { ref, computed } from 'vue'

const counter = ref(0)
const twiceTheCounter = computed(() => counter.value * 2)

counter.value++
console.log(counter.value) // 1
console.log(twiceTheCounter.value) // 2
```

#### 2.5 props

即对应组件外的props,但是不能废除选项式的props定义 

作为setup的第一个参数传入, prop都是props的属性

>  一般不可以用解构语法,不然破坏响应性
>
> 想要解构可以对props进行`toRefs`操作

```js
// MyBook.vue

export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
      //解构操作:
      var {title} = toRefs(props); // 返回Proxy
      //或者:
      var title = toRef(props,'title');
  }
}
```

#### 2.6 attrs/emit/slots

这三项封装在context中,作为setup的第二个参数传入

因为不是响应式的,所以可以解构

#### 2.7 provide,inject

```vue
setup() {
    provide('location', 'North Pole')
    provide('geolocation', { // 可以选择暴露响应式对象,也可以传递readonly
      longitude: 90,
      latitude: 135
    })
  }
```

```vue
const userLocation = inject('location', 'The Universe')
    const userGeolocation = inject('geolocation')
```

### 3. 执行时机

在beforeCreate之前执行

## 二. 剩下的都是后续补充

前面的是没学明白,现在已经有一定应用认识

