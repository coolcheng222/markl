# React 

[TOC]

https://react.dev/learn/describing-the-ui

## 一. 介绍和安装

`React`也是一种渲染ui的js库,它使用`components`来组成ui

* 特点:
  * 组件化
  * 声明式
  * 虚拟dom

* `babel`的用途:
  * es6转es5
  * jsx转js

## 二. quick start: JSX

### 1. 简易介绍

内嵌在html中的朴素方法

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';

const VDOM = <h1>Hello world</h1> //jsx
// 可以在外套小括号然后换行写

ReachDOM.render(VDOM,document.getElementById('test'))
// jsx dom渲染在某个dom容器中
```

创建虚拟dom的本质:

> 少用的方式(也就是**jsx的本质**): js创建
>
> ```javascript
> const VDOM = React.createElement(
>     'h1',{title:'123'},'hello world'
> )// 标签名 属性 内容
> ```

### 2. JSX

`JSX(Javascript XML)`是一种类似xml的js拓展语法

* 基本语法规则:

1. 不写引号

2. **引入变量**:单层大括号,对于内容和属性都有用

```jsx
let title = "123";
let haha = "123"
const VDOM = (
    <h1 title={haha}>
        {title}
    </h1>
)
```

3. **class**: class在jsx中被称为`className`
4. **内联css**: 依然是`style属性`,但是只能传入对象

```jsx
const VDOM = (
    <h1 title={haha} style={{color:'red',fontSize:`${enen}px`}}>
        {title}
    </h1>
)
```

5. __不能有多个根标签__: 需要在外面包一层`<></>`
6. 标签必须有显式的结束`<br/>`,`<input/>`

7. 标签识别:
   * 小写开头: 转为html元素
   * 大写开头: 转为组件

### 3. 遍历练习(对标v-for)

> 直接把list放进对象中,react能够遍历直接展开(指拼串),但这不是我们想要的遍历(object不能这样拼)

我们需要明确一点,标签中使用的是__js表达式__,换句话说,__一个返回jsx对象的函数表达式__也是能写在大括号中的

于是我们引入一个内置函数`arr.map()`

> forEach和for..of没有返回值不能用

```jsx
arr.map(i=><li>{i}</li>)
```

```jsx
<>
    <h1> 前段js列表</h1>
    <ul>
        {
            list.map(i=><li>{i}</li>)
        }
    </ul>
</>
```

但是会报给我说每个list item都需要key属性,且唯一

先暂时用index来代一下

```jsx
<>
<h1> 前段js列表</h1>
<ul>
    {
        list.map((i,index)=><li key={index}>{i}</li>)
    }
</ul>
</>
```

## 三. 组件Component

Component就是个向外导出特定功能的js代码,表现为单个js文件,但实际上其涵盖了html+js+css提供一个完整功能

react中的组件:

1. 函数式组件
2. 类式组件

### 1. 函数式组件(简单组件)

组件放在一个js文件里,定义一个函数然后export

```java
// 一个简单组件
function App() {
    return <h1>hahaha</h1>
}

export default App;

```

在使用时直接用组件名且__首字母__大写放入jsx中

```jsx
import Try from './component/try1.js'
function App() {
    return <Try></Try>
}
```

过程:

1. 寻找组件
2. 发现函数并调用,将返回的虚拟dom转为真实dom呈现在页面中

### 2. 类式组件(复杂组件)

首先应当继承`React.Component`且至少必须定义`render()`方法,然后导出用首字母大写使用就行



```jsx
class Try2 extends React.Component{
    render(){
        return <h1>haha</h1>
    }
}
export default Try2;
```

过程:

1. 发现类,new一个实例并调用render
2. render中的this即该实例对象

## 四. state+简单事件绑定

有state就是复杂组件,否则就是简单组件

在react中,state里面**存储数据**,且为<u>组件实例对象</u>的属性,类型为对象(键值对)

### 1. 初始化state并使用

使用构造器,其默认的参数为`props`

```jsx
constructor(props) {
    super(props);
    this.state = {abc:"haha",wind:"大风"}
}
```

读取state:用this指针即可

```java
render() {
    const {abc} = this.state
    return <h1>今天{abc}</h1>
}
```

### 2. 点击事件绑定

实际上可以用dom获取并加事件,但是可以直接在**标签属性**里写

实际要写什么呢: 

1. `on+大写首字母事件`,比如onClick,onBlur
2. 并且传入一个函数

```jsx
render() {
    const {abc} = this.state
    return <h1 onClick={this.demo}>今天{abc}</h1>
}
demo(){
    console.log("hahaha")
}
```

### 3. this指向

但是绑定事件最核心的问题在**this**的指向

> 只有render是实例对象调用的,所以this可用,但其他方法不是
>
> 一般情况下,用实例对象调用,this会指向实例对象,但是作为单独函数调用,没有this指向
>
> 所以当我们写下`onClick={this.changeWeather}`的时候,只是将函数扔了过去,并没this

简单解决方案: 在构造器中重构一个绑定this的函数

```jsx
constructor(props) {
    super(props);
    this.state = {abc:"haha",wind:"大风"}
    this.demo = this.demo.bind(this)
    // 重新生成一个函数,其this指向bind参数
}
```

但是用起来并不是响应式的,我们还得用setState才能修改页面

### 4. setState

__状态不可随意更改,只有setState可以更改__,更新的动作是覆盖而不是替换,每次更改都会调用render

`setState(键值对对象)`

```js
demo(){
  this.setState({abc:`${this.state.abc}ha`})
  console.log(`${this.state.abc}hahaha`)
}
```

### 5. 简化: 优化掉this问题和state声明

对于没有上面传下来state的情况,可以把state抽取出属性

对于方法也一样,赋值箭头函数可以解决this的问题

```jsx
class Try2 extends React.Component{

    // 赋值属性和方法(注意用=)
    state = {abc:`haha`,wind:`大风`}
    demo = ()=>{
        this.setState({abc:`${this.state.abc}ha`})
        console.log(`${this.state.abc}hahaha`)
    }
    render() {
        const {abc} = this.state
        return <h1 onClick={this.demo}>今天{abc}</h1>
    }
}
```

## 五. props

从父组件读入信息

### 1. 基本使用和批量传递

props作为constructor的参数传递进来,__只读__

在父组件使用子组件的标签中,以属性写在标签中

```jsx
function App() {
    return <Try2 name={"haha"}></Try2>
}
```

```jsx
render() {
    return <h1>今天{this.props.name}</h1>
}
```

* 批量引入

可以使用__拓展运算符__的语法给组件添加props

```java
function App() {
    const p = {name: "haha",age: 18}
    return <Try2 {...p}></Try2>
}
```

这里的...p是babel和react共同作用允许展开的结果,只能用于props

### 2. props的限制和默认值

默认的props显式值一定是字符串(拓展运算符的对象不是)

我们需要对props进行一定的限制,使用static属性

依赖: `prop-types`

```js
import PropTypes from "prop-types";
```

```js
static propTypes = {
    name: PropTypes.string.isRequired
}
```

> 对于函数,传propTypes.func

默认值:

```js
static defaultProps = {
    name: 'haha'
}
```

### 3. 函数式的props

```js
function Try(props){
    return <h1>{props.name}</h1>
}
```



## 六. refs

### 1. (过时)使用字符串ref

refs是实例对象的属性

引入: 拿到input的值

在vue中也有类似的用法,也就是在input(dom)上增加__属性ref__并添加一个字符串标识名,在实例中用`this.refs["标识名"]`进行dom的使用

```js
<input ref="input1" type="text" placeholder="haha"/>
```

```js
clicking = ()=>{
    console.log(this.refs["input1"].value)
}
```

### 2. 回调的refs

回调形式也就是这样:

```jsx
<input ref={()=>{}}/>
```

其参数也就是该节点

```jsx
<input ref={(a)=>{this.input1 = a}} />
```

直接把ref挂在组件实例上

```js
clicking = ()=>{
    console.log(this.input1.value);
}
```

> 如果ref是以内联函数定义的,更新(重新render)过程会调用两次,第一次传入null,第二次传入dom

### 3. createRef的refs

`React.createRef()`返回一个容器(**每个标识对应一个容器**),存储被ref标识的节点

```jsx
myRef = React.createRef()

<input ref={this.myRef} type="text" placeholder="haha"/>
```

当发生回调时,在对应方法中使用`this.myRef.current`即可

```jsx
clicking = ()=>{
    console.log(this.myRef.current);
}
```

## 七. 事件处理

### 1. 底层流程

1. 委托给组件最外层的元素(冒泡)
2. 通过event.target得到发生事件的dom对象

```jsx
clicking = (e)=>{
    console.log(e.target);
}
```

### 2. 非受控组件

> 所有输入类的节点都是现用现取为非受控组件

引例: 一个form提交

```jsx
<form onSubmit={this.submit}>
    <input  type="text" name="username" placeholder="haha"/>
    <input  type="text" name="password" placeholder="hengheng"/>
    <button>点我</button>
</form>
```

阻止默认行为(复习)

```js
submit = (event)=>{
    event.preventDefault()
}
```

### 3. 受控组件

用onChange更新state就能存

```jsx
<form onSubmit={this.submit} >
    <input onChange={this.change1}  type="text" name="username" placeholder="haha"/>
    <input onChange={this.change2}   type="text" name="password" placeholder="hengheng"/>
    <button type="submit">点我</button>
    </form>
```

### 4. 技巧: 高阶函数

> 高阶函数就是传入或者返回函数
>
> 函数柯里化: 以返回函数的方式连续调用

onChange等回调可以接受一个函数,我们可以传入一个__返回函数的函数__,由此我们可以__指定参数__

```jsx
<form onSubmit={this.submit} >
    <input onChange={this.change("username")}  type="text" name="username" placeholder="haha"/>
    <input onChange={this.change("password")}   type="text" name="password" placeholder="hengheng"/>
    <button type="submit">点我</button>
</form>

// 该函数返回一个函数,内部动态指定State
change = (e)=>{
    return (event)=>{
    this.setState({[e]:event.target.value})
    }
}
```



## 八. 生命周期

>mount
>
>unmount

内置方法:(钩子函数)

`componentDidMount`挂载回调

`componentWillUnmount`卸载回调

### 1. 旧生命周期(不是过时)

组件创建:

```
constructor->
componentWillMount->
render->
componentDidMount
```

更新流程:(比如setState)

```js
componentWillReceiveProps(第一次不算)->
shouldComponentUpdate(setState从这里)->
componentWillUpdate(forceUpdate从这里)->
render->
componentDidUpdate
```

返回中间两个返回**false**则不会更新

> forceUpdate就用this.forceUpdate()就行了

