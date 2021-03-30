

# Vue

[TOC]

## 零. Vue-cli

[vue-cli文档](https://cli.vuejs.org/zh/guide/)

### 1. 安装

```bash
npm install -g @vue/cli
npm install -g @vue/cli-service-global
```

### 2. 创建打包

```bash
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

1. 在项目目录下使用`vue serve(端口8080调试)` `vue build(构建)`即可运行
2. 使用`vue create 名字`即可创建vue工程,或者使用`vue ui`图形化创建并操作
3. 

## 一. 介绍

Vue是前端**渐进式js框架**

优点: 体积小,运行效率高,不需要dom操作,学习成本低,生态丰富

## 二. 安装和helloworld

### 1. 方式1: 命令行工具cli

待补充

### 2. 方式2: 直接用\<script\>引入

官方文档提供了开发和生产两个版本,可以下载并获得vue.js文件

我们可以使用html引入vue.js即可(后续可能用到.vue文件)

```html
<script src="https://unpkg.com/vue@3.0.0-beta.12/dist/vue.global.js"></script>
```



### 3. 声明式渲染体验

这是Vue2,用构造器创建

```html
<div id="app">
    {{message}} <!-- 表达式,用双大括号,显示数据 -->
</div>
<script>
    var app = new Vue({ // vue构造器开始,参数为对象(实际上就是option)
        el:'#app', //el元素,值为选择器
        data: {
            message: 'hello vue' //填充信息
        }
    })
</script>
```

这是Vue3,用方法创建,赋值给const

```javascript
const app = Vue.createApp({ //createApp静态方法
    data(){ //ES6简写函数
        return { //返回数据
            product: 'Socks'
        }
    }

})
const mountedApp = app.mount("#app");//挂载到dom上

```

## 三. Vue3简单使用

### 1. 创建和挂载

第一步: 使用Vue.createApp创建应用对象,并传入option对象

第二步: 使用app的mount方法挂载dom,传入选择器

想要修改值,用mountApp.product这种就行,操作挂载返回的对象

```html
<div id="app">
    {{message}} <!-- 表达式,用双大括号,显示数据 -->
</div>
<script>
const app = Vue.createApp({ //createApp静态方法
    data(){ //ES6简写函数
        return { //返回数据
            product: 'Socks'
        }
    }

})
const mountedApp = app.mount("#app");//挂载到dom上
</script>
```

### 2. 表达式简单介绍

使用`{{}}`括起的是表达式,其内部写的其实就是**Javascript代码**,可以拼串,三元运算等

甚至可以调用函数

```html
<h1>{{product + ()=>{return "aaa"}()}}</h1>
```

当然,表达式中的变量可以调用其属性或者方法

### 2. 属性绑定

使用`v-bind:属性`来绑定属性和数据

直接使用变量名,不用括号

```html
<img v-bind:src="image" />
```

v-bind可以省略,只剩一个`:`开头即可

条件属性绑定:

```html
:class="{disabledButton:!inStock}"
```

一个对象{},里面写`属性名:布尔`

### 3. 条件渲染

相信大家都猜到了,就是`v-if`和`v-else`(`v-else-if`),如果对应条件不成立,vue会将标签移出dom树

另一种是`v-show`,内容一样,但是只会将标签display:none而不会移除

最简单用法:

```html
<!-- if的值为变量名,返回的是布尔,如果不是布尔类型就根据js的语法来转换 -->
<p v-if="inStock">In Stock</p>
<p v-else>Out of Stock</p>
```

> 注意,v-if/show属性值也是javascript代码,可以写各种逻辑运算比较运算三元运算

```html
<p v-if="inventory > 9">In Stock</p>
<p v-if="(inventory => inventory > 9)(10)">In Stock</p>

```

### 4. 循环渲染

`v-for`进行循环渲染,语法:

```html
<xxx v-for='变量名 in 列表'>
	{{变量名}}
</xxx>
```

跟模板引擎一样,遍历一次就创建一个标签

同时绑定的属性中也可以使用遍历项

### 5. 事件监听

`v-on:事件名`或者`@事件名`属性直接事件绑定

```html
<button class="button" v-on:click="cart += 1">Add to Cart</button>
```

值: 

* 可以是js代码表达式

* 可以是保存在Vue中的方法名(methods)

  ```javascript
  const app = Vue.createApp({
      data() {
          return {
              cart:0,
              product: 'Socks',
              image: './assets/images/socks_blue.jpg',
              inStock: true,
              details: ['50% cotton', '30% wool', '20% polyester'],
          }
      },
      methods:{
          addToCart(){
              this.cart += 1;
          }
      }
  })
  ```

  

### 6. 样式绑定

最简单的就是用v-bind绑定style属性

不过值得一提的是内容是js代码而不是css

```javascript
:style="{backgroundColor:variant.color}"
或者
:style="{'background-color':variant.color}"
```

也可以把样式对象写在data里,属性值为样式对象(如果有引用对象就算了)

### 7. computed

在option中添加__computed属性__,在内部加上方法,来返回你期待的computed value

```javascript
computed: {
    title(){
        return this.product + this.brand; //data的返回内容
    }
}//使用时用{{title}}即可
```

这个属性让我们填写的"标识符"不止可以指向data()方法的数据,而且可以指向computed中方法的返回值

### 8. component应用子组件

通过调用应用的component方法可以创建组件

```javascript
app.component(组件名,选项);
//写在app下面,挂载上面
```

组件名: component的标识符

选项: 

可以通过template属性关联html代码

剩下的data,methods,computed和之前学的一样

* 使用: 使用vue定制标签可以将组件对应的html代码和数据填充进html文件中

  ```html
  <组件名></组件名>
  <product-display></product-display>
  ```

  该标签的属性可以传入组件

  在component的option中添加`props属性`,以获取标签属性到组件内部

  ```javascript
  app.component('product-display',{
      props:{
          premiun:{// 这样组件中就可以使用premium了
              type: Boolean,
              required: true
          }
      },
  ```

  ```html
  <product-display premiun="true"></product-display>
  
  ```

  

* 意义: 可以灵活地抽取html代码及其数据

### 9. 组件事件冒泡

组件中发生的事件想要冒泡传递到主应用,应当做一些操作

在组件对应方法中,使用`$emit`方法进行冒泡,即可将事件交给主应用触发

```javascript
this.$emit(//触发上一级的事件
    'add-to-cart' //事件名,
    //可以添加其他参数args,都是给上一级应用事件发生时传入的参数
)
```

然后用v-on将事件给对应html标签绑定

```html
<product-display premiun="true" @add-to-cart="addCart"></product-display>
<!-- 对应调用主应用的addCart方法 -->
```

### 10. 表单双向绑定

表单的数据和事件可以直接和vue中的数据和事件关联

使用`v-model`即可将表单的value和数据绑定,对于input,textarea,select都有效

```html
<select id="rating" v-model.number="rating">
    <!-- 后面的rating是data中的变量,number表示强制类型转换 -->
```

> 双向响应式绑定: **数据随着表单value改变,表单value随着数据改变**

给form绑定事件函数可以修改绑定的值

```javascript
<form class="review-form" @submit.prevent="onSubmit">
 <!-- 绑定事件并阻止默认行为-->
    
methods:{
     onSubmit(){
         let productReview = {
             name: this.name,
             review: this.review,
             rating: this.rating
         }
         this.$emit('review-submitted',productReview); //冒泡事件并提交表单
         this.name = '';//将表单置空
         this.review = '';
         this.rating = null;
     }
 }
```

## 四. 应用实例和组件实例

### 1. 应用实例

由`Vue.createApp`__创建并返回__的实例称为__应用实例__; 可以用来注册Vue"全局内容"

### 2. 组件实例

__根组件实例__由Vue.createApp配置,由`mount`返回;

一个应用中的组件实例可以构成组件树,根组件即组件树的根

一个应用中的所有组件实例共享一个应用实例

### 3. 组件实例属性和方法

#### 3.1 属性

组件实例的**属性**是通过option中的`data方法`定义并向外暴露的. data方法的用法我们之前提及过

```javascript
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4
```

实际上这些属性是组件的`$data`属性的属性,为了方便才作为组建的属性暴露

当且仅当组件创建时data会确定属性,所以data方法return的属性就是所有属性.通过其他方法加入的属性可能不在响应式系统中

#### 3.2 方法

在option中加入`methods属性`来为组件添加方法

this指向组件实例,使用实例属性时应带上this,**不建议定义箭头函数**

在模板中使用方法,并且会获取其返回值或其本身. 最常见用法是作为监听器存在; 

> 不写括号返回函数本身.写括号返回返回值
>
> 对于类似v-on,应该不写括号

可以在html属性中,也可以在{{}}中,本质一样,但<u>不建议有任何副作用(比如修改数据,开启进程)</u>

### 4. 组件生命周期挂钩

下图是组件实例的生命周期,红框是生命周期对应的钩子方法,到对应生命周期时钩子方法会触发

<img src="https://v3.vuejs.org/images/lifecycle.svg" alt="Instance lifecycle hooks" style="zoom:50%;" />

钩子方法定义: 以方法形式定义在option中,方法名见图片中的内容

* 钩子方法中的`this`即为组件实例,所以不推荐使用箭头函数

```javascript
let app = Vue.createApp({
    data(){
        return {
            cont:1
        }
    },created(){
        console.log("creaing");
    },updated(){
        console.log("updating");
    },beforeMount(){
        console.log("haha")
    }
})
```

## 五. 模板语法

### 1. html插值

最基本的插值类似于mustache的插值方式,即双大括号

```html
<span>Message: {{ msg }}</span>
```

* 去除标签内容的响应式变化,`v-once`,(加在挂载的元素上没用)

  ```html
  <span v-once>{{msg}}</span>
  <!-- 只渲染一次不会变化,对于标签中所有内容有效 -->
  ```

* 原生html

  * {{}}中的内容解释为纯文本而不是html,如果需要解释为html,需要使用`v-html`属性并将变量传入

    ```html
    <span>{{htm}}</span>
    <span v-html="htm"></span>
    ```

* html属性:

  * 正如之前学的`v-bind`可以绑定属性
  * 若值为null或者undefined,则该属性不会被渲染
  * 特别的,若传入的变量为布尔,则视为布尔属性,为true则属性存在,为false则不存在

* 表达式: 表达式其实是javascript代码,但是只能写**表达式**而非**语句**

  可以使用的库在白名单中:

  ```javascript
  const GLOBALS_WHITE_LISTED =
    'Infinity,undefined,NaN,isFinite,isNaN,parseFloat,parseInt,decodeURI,' +
    'decodeURIComponent,encodeURI,encodeURIComponent,Math,Number,Date,Array,' +
    'Object,Boolean,String,RegExp,Map,Set,JSON,Intl,BigInt'
  ```

  

### 2. v-指令

`指令`是以v-为前缀的特殊的html属性,大多数v-指令的值都为javascript表达式(除了v-on和v-for),指令可以响应式的将副作用施加到dom元素上

* **指令参数**: 在指令后添加参数,以`指令:参数`形式声明

  * 举例: `v-bind:href`,href就是参数

* **动态参数**: 参数可以是一个Javascript表达式,但是需要`[]`扩起

  * 举例: `v-bind:[attributeName]`,attributeName可以被组件赋值
  * 如果动态值为null,整个属性被移除
  * 如果v-bind属性出现空格或者引号,则视为无效

* __修饰符__: 形如`v-on:submit.prevent`的prevent即为修饰符,意为阻止默认行为.

  后面展开

### 3. 一些指令缩写

`v-bind:xxx`可以简写为`:xxx`

`v-on:xxx`可以简写为`@xxx`

## 六. 计算属性

在模板中写表达式虽然方便但是会让项目显得臃肿. 模板中的语句应该显得更接近声明性

注意: 计算属性作为组件的属性存在,当定义setter时,其可以被赋值

### 1. 使用

在option中添加computed属性给组件,一般为包含空参方法的属性. 使用时不需要加括号即可获取其返回值

> 当computed方法定义时,vue会意识到computed属性<u>依赖</u>的组件属性,响应式的进行变化

```javascript
let app = Vue.createApp({
    data() {
        return {
            cont: 1,
            msg: 'aaaa',
            htm: '<span style="color:red">hahaa</span>',
            bar: 'aaa'
        }
    },
},methods:{
  clickBtn(){
    console.log("aaa")
},
},computed:{
    compp(){
        return "aaaa" + msg;
    }
}
})
```

使用computed属性时不需要加括号

### 2. 和methods对比

我们调用methods中的方法一样能获取计算后的返回值,computed对于此有什么优势?

1. 拥有依赖于组件属性的**缓存**,只有在依赖属性改变时会重新计算; 而如果没有依赖属性,对应值不会响应式改变

### 3. computed中的setter

computed中定义方法默认视为getter,可以定义属性来包含getter和setter

```javascript
computed: {
  fullName: {
    // getter
    get() {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

这样做的意义就是可以直接使用`组件.计算属性名=xxx`进行赋值, 该setter会改变data中的属性,从而也影响了computed的属性

## 七.watcher

option中还可以指定`watch属性`,在监视的属性变化时会触发对应方法

```javascript
 data() {
     return {
         question:''
     }
 },watch:{
     //每当属性question改变时,该方法就会触发
     //参数: 旧值,新值
     question(old,neww){
         console.log(old);
         console.log(neww)
     }
 },
```

## 八. 类型和属性绑定

class和style本都可以用v-bind来处理,但是因为其拥有较为特殊的语法,所以vue提供了更丰富的方式来操作之

### 1. class

* **对象语法**

  * 我们可以向`:class`传一个对象来动态的toggle class

  * 属性名=class名,值为boolean或其他可以强转为boolean的类型,如果为true就有这个class,为false就没有

  * 可以和class属性共存

  * 可以传入data或computed指向的对象

    ```html
    <div
      class="static"
      :class="{ active: isActive, 'text-danger': hasError }"
    ></div>
    ```

* __数组语法__

  ```html
  <div :class="[activeClass, errorClass]"></div>
  ```
  * 数组中的可以是属性名,也可以是表达式,也可以是上面的对象语法

* 组件class
  * 当组件只有一个dom根元素时,给组件标签添加class会将该class添加到根dom上
  * 可以用`$attr.class`指定属性,后面详细展开

### 2. style

对象语法和css很类似,直接写就完了

传data或者computed也行

* 驼峰命名和连接符都能用,连接符表示法要加上引号

* 数组: 包含很多指定css的对象就行

* 多值样式: 用数组

  ```html
  <div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
  ```

## 九. 条件渲染(细节)


### 1. template

在需要一个条件判断一组标签时,可以将它们放进template标签

和其他标签不同的是,template永远不会被渲染

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### 2. v-else用法

v-else必须紧邻v-if/v-else-if的元素

### 3. 不建议将v-for和v-if一起用

v-if优先于v-for

## 十. v-for

### 1. 数组元素映射

使用`item in items`即可完成一个简单的数组映射

> in可以替换成of,适配迭代器语法

items为元数据数组,而item为被迭代到的元素的别名

> 使用时都需要用{{}}或者v指令

```html
<div v-for="item in items">
    {{item.id}}
</div>
let config = {
	data(){
		return {
			items: [
				{id:1,name:'aaa'},
				{id:2,name:'bbb'}
			]
		}
	}
}
```

额外参数: 可以通过`(item,index) in items`类似语法同时获取index和item别名

```html
<ul id="array-with-index">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

### 2. 对象属性映射

v-for=`value in myObject`只获取属性值

v-for=`(value,name)in myObject`获取值,名

v-for=`(value,name,index) in myObject`获取属性值,名,索引

### 3. 维持状态

Vue默认采取了一种in-place patch策略,当数据项顺序改变时,Vue不会移动DOM元素来匹配数据项顺序,而是就地更新每个元素，并且确保它们在每个索引位置正确渲染。

这种策略有效,但是只有你的渲染结果不依赖于子组件或者跟当前dom状态无关时才能用

* 为了使Vue更轻松地跟踪节点身份,我们可以给v-for的元素指定主键,采用`:key`方式指定,值一般应该为遍历的元素的属性

  * 这样vue不会采用默认的reorder策略
  * 建议绑定number或者string

  ```html
  <div v-for="item in items" :key="item.id">
    <!-- content -->
  </div>
  ```

### 4. 响应式数组变化

数组api的方法副作用也会引起数组变化,所以Vue也会监听数组调用方法并响应地改变dom

- `push()`
- `pop()`
- `shift()`: 移除第一个元素并返回
- `unshift()`: 添加第一个元素
- `splice(x,n,[])`: 从x开始去除n个元素并替换成第三个参数的元素
- `sort()`: 排序,可以传入比较函数
- `reverse()`: 

### 5. 取整数range

v-for=`n in 10`取1-10

### 6. template

v-for也可以和template一起用,详见v-if说的

### 7. 组件上用v-for

组件上可以用v-for

```html
<my-component v-for="item in items" :key="item.id"></my-component>
```

但是组件内部只有通过props才能获取遍历的元素

```html
<my-component
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
></my-component>
```

## 十一. 事件绑定

我们可以通过`v-on`或者`@`来监听dom事件并响应执行一些js代码

### 1. v-on

v-on标注的属性的值可以是js代码或者实例的方法名,本质就是`addEventListener("事件名",方法)`

当然不用太死板,写方法名传递的是方法,写**调用方法的表达式**传递的是js代码,其中,可以传入`$event`参数指向event本身

```html
<div id="inline-handler">
  <button @click="say('hi')">Say hi</button>
  <button @click="say('what')">Say what</button>
</div>
```

使用逗号运算符可以一次调用多个方法/执行多个语句

### 2. 事件修饰符

修饰符用于对事件的修饰,比如`@click.prevent`. 我们希望方法只关乎数据,所以一些常用的行为提取放在修饰符里

修饰符可以链式使用,但是链式调用的顺序会对行为造成影响

> 使用修饰符时顺序很重要，因为相关代码是按相同顺序生成的。因此，使用`@click.prevent.self`将阻止**所有单击，**而`@click.self.prevent`只会阻止对元素本身的单击。

- `.stop`: stopPropagation,阻止事件传播

- `.prevent`: 防止默认行为

- `.capture`: 捕获子元素事件,并在子元素事件处理前执行

- `.self`: 只有元素自身被触发事件才执行

  > 以上4个为原生dom事件特有
  >
  > 以下2个也可以用于组件事件

- `.once`: 只用一次,调用后立即删除

- `.passive`:对应addEventListener的passive参数;当事件发生后,其默认行为会立即执行; 不建议和prevent一起用

  > passive如果`true`，指示由指定的函数`listener`永远不会调用 [`preventDefault()`](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault)。如果一定要`preventDefault()`，则用户代理将不执行任何操作，只生成控制台警告。
  >
  > 对于移动端比较有用

基于此,可以只使用修饰符而不给事件加方法

```html
<!-- just the modifier -->
<form @submit.prevent></form>
```

### 3. 按键修饰符

一些特殊的按键也可以被用在修饰符,显然只能针对keyup/keydown事件

按键一览: [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values),注意把驼峰命名法改成连接符

```html
<input @keyup.page-down="onPageDown" />
```

一些常用键简写

- `.enter`
- `.tab`
- `.delete` (captures both "Delete" and "Backspace" keys)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

### 4. 系统按键修饰符

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`: 即windows的win,mac的command

```html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

* `.exact`: 当只有指定的按键按下去才有效,如果有额外的按键则无效

```html
<!-- this will only fire when Ctrl and no other keys are pressed -->
<button @click.ctrl.exact="onCtrlClick">A</button>
```

### 5. 鼠标修饰符

- `.left`
- `.right`
- `.middle`

## 十二. 表单输入绑定

`v-model`是用户输入事件更新数据的语法糖,可以在input,textarea,select创建双向绑定

> v-model忽略dom中value,checked,selected等默认值,只将数据作为参考来源

### 1. 属性和事件

`v-model`对应不同html元素会绑定其不同事件及属性

- 文本和textarea元素使用`value`属性和`input`事件；
- 复选框和单选按钮使用`checked`属性和`change`事件；
- select采用`value`和`change`事件。

### 2. 一些特别的用法

绑定到同一数组的多个复选框：

```html
<div id="v-model-multiple-checkboxes">
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
  <label for="jack">Jack</label>
  <input type="checkbox" id="john" value="John" v-model="checkedNames" />
  <label for="john">John</label>
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
  <label for="mike">Mike</label>
  <br />
  <span>Checked names: {{ checkedNames }}</span>
</div>

Vue.createApp({
  data() {
    return {
      checkedNames: []
    }
  }
}).mount('#v-model-multiple-checkboxes')
```

2. 如果表达式初始值和select选项不撇撇,则可能出现问题,应该提供默认的不能选的选项
3. selected也可以绑定数组,在selected上加multiple属性
4. 但是有时我们可能想将值绑定到当前活动实例上的动态属性。我们可以`v-bind`用来实现这一目标。另外，使用`v-bind`允许我们将输入值绑定到非字符串值。

### 3. 修饰符

`.lazy`: 让input/textarea在change事件后响应而不是input事件

`.number`: 将输入转换为数字

`.trim`: 修剪前后空格

# 组件

## 零. 前情提要

### 8. component应用子组件

通过调用应用的component方法可以创建组件

```javascript
app.component(组件名,选项);
//写在app下面,挂载上面
```

组件名: 组件的**标识符**

选项: 

可以通过template属性关联html代码

剩下的data,methods,computed和之前学的一样

* 使用: 使用vue定制标签可以将组件对应的html代码和数据填充进html文件中

  ```html
  <组件名></组件名>
  <product-display></product-display>
  ```

  该标签的属性可以传入组件

  在component的option中添加`props属性`,以获取标签属性到组件内部

  ```javascript
  app.component('product-display',{
      props:{
          premiun:{// 这样组件中就可以使用premium了
              type: Boolean,
              required: true
          }
      },
  ```

  ```html
  <product-display premiun="true"></product-display>
  
  ```

  

* 意义: 可以灵活地抽取html代码及其数据

### 9. 组件事件冒泡

组件中发生的事件想要冒泡传递到主应用,应当做一些操作

在组件对应方法中,使用`$emit`方法进行冒泡,即可将事件交给主应用触发

```javascript
this.$emit(//触发上一级的事件
    'add-to-cart' //事件名,
    //可以添加其他参数args,都是给上一级应用事件发生时传入的参数
)
```

然后用v-on将事件给对应html标签绑定

```html
<product-display premiun="true" @add-to-cart="addCart"></product-display>
<!-- 对应调用主应用的addCart方法 -->
```

## 一. 组件基础

### 1. 组件注册

组件使用在templates中之前,必须先由Vue进行注册. 

__全局注册__即用app.component注册vue组件

__局部注册__后面讲

### 2. props

props选项让组件可以接受外部传入的参数

> 该参数可以使用vbind 也可以不用

将值传给props属性时,该prop属性成为组件的属性

* props选项的值形式:
  * 数组: 简简单单,指定属性名字的字符串

```js
app.component('blog-post', {
  props: ['title'],
  template: `<h4>{{ title }}</h4>`
})
```

### 3. emits

使用$emit()可以让父组件感受到事件

在option中emits属性可以列出所有emit的事件,方便管理

### 4. 在组件标签上使用v-model

v-model的本质:

```
<input v-model="searchText">
```

等价于：

```html
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```

所以我们需要在组件的prop中指定value属性,然后

```javascript
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

### 5. slot

组件标签体中的内容会被自动装填到组件template中的slot标签中

### 6. tab切换简化/动态组件

组件可以动态的切换,使用`<component>`标签和`v-bind:is="返回组件名/或选项对象的方法"`

### 7. dom与组件组合问题

一些HTML元素，比如`<ul>`，`<ol>`，`<table>`和`<select>`有什么元素可以在其内部出现的限制，以及一些元素，如`<li>`，`<tr>`和`<option>`只能出现某些其他元素中。

在将组件与具有此类限制的元素一起使用时，这将导致问题。例如：

```html
<table>
  <blog-post-row></blog-post-row>
</table>
```

自定义组件`<blog-post-row>`将作为无效内容悬挂，导致最终渲染的输出中出现错误。我们可以使用特殊`v-is`指令作为解决方法：

```html
<table>
  <tr v-is="'blog-post-row'"></tr>
</table>
```

 ## 二. 组件注册

### 1. 局部注册

很多时候不应该注册全局性的组件,我们希望一些组件具有局部的特征

```javascript
let app = Vue.createApp({
});
const local = { // 声明option变量
    template:`
        <h1>hihi</h1>
     `
}
app.component('global',{
    template:`
        <h1>haha</h1>
        `,
    components:{ // option注册到另一个组件中,上一层(app)看不到local组件
        'local': local
    }
});
let vm = app.mount("#app");
```

## 三. props

### 1. 类型指定

在之前我们用数组形式给props赋值过,我们可以用对象给props赋值,最简单的形式就是指明类型

```js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // or any other constructor
}
```

这样可以使代码更规范,并对其他js使用者以提示作用

传递数组和对象都用json形式

* 另: 传递对象的所有属性

  有时候需要传递对象的所有属性而不是对象本身进props,则可以用`v-bind属性`(不需要后缀)来传递

  ```html
  <blog-post v-bind="post"></blog-post>
  ```

  等价于

  ```html
  <blog-post v-bind:id="post.id" v-bind:title="post.title"></blog-post>
  ```

### 2. 父子共享数据

这是一条`单向数据流`

父组件和子组件共享一个数据时,**父组件修改会影响到子组件,而子组件不能修改数据**

若修改数组或对象中的数据,则子组件会影响到父组件

### 3. 更多校验信息

type: 类型

validator:一个返回bool的校验方法

default: 默认值

required: 是否必须



```js
app.component('my-component', {
  props: {
    // Basic type check (`null` and `undefined` values will pass any type validation)
    propA: Number,
    // Multiple possible types
    propB: [String, Number],
    // Required string
    propC: {
      type: String,
      required: true
    },
    // Number with a default value
    propD: {
      type: Number,
      default: 100
    },
    // Object with a default value
    propE: {
      type: Object,
      // Object or array defaults must be returned from
      // a factory function
      default: function() {
        return { message: 'hello' }
      }
    },
    // Custom validator function
    propF: {
      validator: function(value) {
        // The value must match one of these strings
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    },
    // Function with a default value
    propG: {
      type: Function,
      // Unlike object or array default, this is not a factory function - this is a function to serve as a default value
      default: function() {
        return 'Default function'
      }
    }
  }
})
```

### 4. 非prop属性

例如class,id,style这种属性称为非prop属性,可以通过`$attrs`访问

## 四. emit

#### `v-model` 参数

默认情况下，组件上的 `v-model` 使用 `modelValue` 作为 prop 和 `update:modelValue` 作为事件。我们可以通过向 `v-model` 传递参数来修改这些名称：

```html
<my-component v-model:title="bookTitle"></my-component>
```

在本例中，子组件将需要一个 `title` prop 并发出 `update:title` 要同步的事件：

```js
app.component('my-component', {
  props: {
    title: String
  },
  emits: ['update:title'],
  template: `
    <input
      type="text"
      :value="title"
      @input="$emit('update:title', $event.target.value)">
  `
})
```

```html
<my-component v-model:title="bookTitle"></my-component>
```

#### [#](https://v3.cn.vuejs.org/guide/component-custom-events.html#多个-v-model-绑定)多个 `v-model` 绑定

通过利用以特定 prop 和事件为目标的能力，正如我们之前在 [`v-model` 参数](https://v3.cn.vuejs.org/guide/component-custom-events.html#v-model-参数)中所学的那样，我们现在可以在单个组件实例上创建多个 v-model 绑定。

每个 v-model 将同步到不同的 prop，而不需要在组件中添加额外的选项：

```html
<user-name
  v-model:first-name="firstName"
  v-model:last-name="lastName"
></user-name>
```

```js
app.component('user-name', {
  props: {
    firstName: String,
    lastName: String
  },
  emits: ['update:firstName', 'update:lastName'],
  template: `
    <input 
      type="text"
      :value="firstName"
      @input="$emit('update:firstName', $event.target.value)">

    <input
      type="text"
      :value="lastName"
      @input="$emit('update:lastName', $event.target.value)">
  `
})
```



<iframe allowfullscreen="true" allowpaymentrequest="true" allowtransparency="true" class="cp_embed_iframe " frameborder="0" height="300" width="100%" name="cp_embed_1" scrolling="no" src="https://codepen.io/Vue/embed/preview/GRoPPrM?theme-id=39028&amp;preview=true&amp;editable=true&amp;height=300&amp;default-tab=html%2Cresult&amp;user=Vue&amp;slug-hash=GRoPPrM&amp;pen-title=Multiple%20v-models&amp;name=cp_embed_1" title="Multiple v-models" loading="lazy" id="cp_embed_GRoPPrM" style="width: 740px; overflow: hidden; display: block;"></iframe>

### 处理 `v-model` 修饰符

在 2.x 中，我们对组件 `v-model` 上的 `.trim` 等修饰符提供了硬编码支持。但是，如果组件可以支持自定义修饰符，则会更有用。在 3.x 中，添加到组件 `v-model` 的修饰符将通过 `modelModifiers` prop 提供给组件：

当我们学习表单输入绑定时，我们看到 `v-model` 有[内置修饰符](https://v3.cn.vuejs.org/guide/forms.html#修饰符)——`.trim`、`.number` 和 `.lazy`。但是，在某些情况下，你可能还需要添加自己的自定义修饰符。

让我们创建一个示例自定义修饰符 `capitalize`，它将 `v-model` 绑定提供的字符串的第一个字母大写。

添加到组件 `v-model` 的修饰符将通过 `modelModifiers` prop 提供给组件。在下面的示例中，我们创建了一个组件，其中包含默认为空对象的 `modelModifiers` prop。

请注意，当组件的 `created` 生命周期钩子触发时，`modelModifiers` prop 会包含 `capitalize`，且其值为 `true`——因为 `capitalize` 被设置在了写为 `v-model.capitalize="myText"` 的 `v-model` 绑定上。

```html
<my-component v-model.capitalize="myText"></my-component>
```

```js
app.component('my-component', {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  template: `
    <input type="text"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)">
  `,
  created() {
    console.log(this.modelModifiers) // { capitalize: true }
  }
})
```

现在我们已经设置了 prop，我们可以检查 `modelModifiers` 对象键并编写一个处理器来更改发出的值。在下面的代码中，每当 `<input/>` 元素触发 `input` 事件时，我们都将字符串大写。

```html
<div id="app">
  <my-component v-model.capitalize="myText"></my-component>
  {{ myText }}
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      myText: ''
    }
  }
})

app.component('my-component', {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  emits: ['update:modelValue'],
  methods: {
    emitValue(e) {
      let value = e.target.value
      if (this.modelModifiers.capitalize) {
        value = value.charAt(0).toUpperCase() + value.slice(1)
      }
      this.$emit('update:modelValue', value)
    }
  },
  template: `<input
    type="text"
    :value="modelValue"
    @input="emitValue">`
})

app.mount('#app')
```

对于带参数的 `v-model` 绑定，生成的 prop 名称将为 `arg + "Modifiers"`：

```html
<my-component v-model:description.capitalize="myText"></my-component>
```

```js
app.component('my-component', {
  props: ['description', 'descriptionModifiers'],
  emits: ['update:description'],
  template: `
    <input type="text"
      :value="description"
      @input="$emit('update:description', $event.target.value)">
  `,
  created() {
    console.log(this.descriptionModifiers) // { capitalize: true }
  }
})
```

## 五. slot

### 1. 基本html成分

在组件标签的标签体内的内容会被渲染进组件的`slot`标签,最基本的可以是html或者普通字符串

```html
<sloting> <!-- 这是个组件标签,内部的h1会渲染到template的slot中-->
   <h1>
      hahaha
   </h1>
</sloting>
```



* 作用域: 标签中可以使用`{{}}`语法,但是slot作为子组件渲染,只拥有子组件作用域,**只能访问子组件的数据**

```html
<todo-button>
  Delete a {{ item.name }}
</todo-button>
```

### 2. 默认内容

在template中的slot标签体中添加html成分,会成为slot渲染的默认内容

当组件标签体中没有内容时会生效(没有内容指的是空白,不一定是空字符串)

### 3. 命名插槽

我们希望在一个组件中,将不同插槽放到不同的地方,可以用命名来对应

* **组件标签方面**

  * 将要区分的插槽分别放进template标签中,并对template标签使用`v-slot:名字`来命名插槽

  * 可以用v-slot:[]方式动态命名

  * 缩写为`#`

    ```html
    <sloting>
        <template v-slot:head>
            <h1>hahaha</h1>
        </template>
        <template v-slot:body>
            <h1>body</h1>
        </template>
        <template v-slot:foot>
            <h1>foot</h1>
        </template>
    </sloting>
    ```

* **组件内部使用**

  * 依然使用slot标签,添加`name属性`指定插槽名,可以添加对应默认内容
  * 不添加name的默认名字为`default`

  ```html
  <slot name="head">ooooo</slot>
  <slot name="body">ooooo</slot>
  <slot name="foot">ooooo</slot>
  ```

### 4. 作用域插槽

这里的意思是, 将子组件的数据抛给父组件,以便父组件在子组件的标签体中操作子组件数据. 最终操作的结果都将渲染在子组件插槽中

* __子组件数据上抛__: 

  * 利用slot标签的`v-bind`属性将数据抛给父组件

    ```html
    <ul>
        <li v-for="(item, index) in items">
            <slot :item="item" :index="index"></slot>
            <!-- v-bind的属性名即父组件会用到的属性名 -->
        </li>
    </ul>
    ```

* __父组件接收并使用__:

  * 在template上给对应v-slot传值,接收拥有属性的数据对象

  ```html
  <todo-list>
      <!-- 父组件获得了ploting对象(命名随意),该对象拥有slot指定的属性和数据,可以在标签体内使用 -->
      <template v-slot:default="ploting">
          <i class="fas fa-check"></i>
          <span style="color:green">{{ ploting.item }}</span>
      </template>
  
  </todo-list>
  ```

> 当组件只有默认插槽时,可以将v-slot写在标签体

### 5. v-slot的值

v-slot其实并没有这么简单: 比如说我写了个`v-slot:default="abc"`,那底层就会声明一个这样的函数:

```javascript
function(abc){
	//abc作为形式参数名存在,而使用的实参是由slot属性构成的对象
    //在标签体中使用{{abc.item}}实际就是在函数中使用
}
```

这样我们可以用__解构赋值__来对形参形式进行操作

```html
<todo-list v-slot="{ item }">
    <!-- 结构赋值参数item -->
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
    <!-- 对应的,可以直接使用参数-->
</todo-list>
```

或者其他的解构操作

```html
<todo-list v-slot="{ item: todo }"><!-- 将参数名改为todo -->
<todo-list v-slot="{ item = 'Placeholder' }"> <!-- 默认值 -->
```

## 五. provide和inject

我们遇到一个问题: 当组件树深度够高,我们想要向下传递数据就会遇到链式prop的问题,太麻烦

<hr>

所以vue提供了一个api来让父组件提供(provide)数据,子组件直接获得(inject)数据

* 父组件provide:

  * 在option中声明`provide(){}`方法,和data类似

  * 返回值即provide出去的数据, key-value形式

    ```javascript
    provide(){
      return {
          user: this.msg
      }
    }
    ```

* 子组件inject

  * 最简单形式: string数组指定接受的名字

    ```javascript
    inject: ['user']
    ```

### 2. 响应式

默认provide/inject不带响应式,如果一定要响应式

可以给provide赋值`响应式对象`或者`ref属性`

比如:

```javascript
import {computed as com} from 'vue'
user: com(()=>this.msg)
```

## 七. 动态组件+异步组件

### 1. 动态组件 keep-alive

这里解决的问题主要是组件切换会刷新组件的状态,我们希望组件保持它离开时候的样子

`keep-alive`是一个标签,可以将想要保存状态的动态组件放进其标签体中

```vue-html
<!-- 失活的组件将会被缓存！-->
<keep-alive>
  <component :is="currentTabComponent"></component>
</keep-alive>
```

注意: keep-alive会触发`deactived` `deactivate`d

属性:

* include/exclude: 有条件的缓存,可以用逗号分隔字符串,数组,正则表达式表示
* max: 最大缓存组件数量,超过就把最早的排除

### 2. 异步组件(懒加载)

不理解,有需要可以看[文档](https://v3.cn.vuejs.org/guide/component-dynamic-async.html#%E5%BC%82%E6%AD%A5%E7%BB%84%E4%BB%B6)