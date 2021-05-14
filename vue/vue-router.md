# Vue-router

## 一. 引入和使用

一般来说需要引入vue-router依赖,但是vue-cli只需要安装插件即可

### 1. 简单使用

#### 1.1 html

```
 <div id="app">
     <h1>Hello App!</h1>
     <p>
       <router-link to="/">Go to Home</router-link>
       <router-link to="/about">Go to About</router-link>
     </p>
     <router-view></router-view>
 </div>
```

`router-link`: 内部渲染一个<a>, 带有"正确的href"

`router-view`: router指向的组件会渲染在这个标签里

#### 1.2 JS

使用一个数组,内部元素是指定路径和对应组建的对象(在插件中,这放在@/router/index.js中)

```
 const routes = [
   { path: '/', component: Home }, // component: 对应的组件对象
   { path: '/about', component: About },
 ]
```

创建路由并配置(在插件中,这放在@/router/index.js中)

```
 // 3. 创建路由实例并传递 `routes` 配置
 // 你可以在这里输入更多的配置，但我们在这里
 // 暂时保持简单
 const router = VueRouter.createRouter({
   // 4. 内部提供了 history 模式的实现。为了简单起见，我们在这里使用 hash 模式。
   history: VueRouter.createWebHashHistory(),
   routes, // `routes: routes` 的缩写
 })
```

app用上就行

```
 app.use(router)
```

#### 1.3 router和路由对象的使用方式

在任意组件使用`this.$router`即可获得router对象,免去了导入的麻烦

在组件中使用`this.$route`获取当前路由

#### 1.4 使用js切换路由

```
 //使用router的push方法
 this.$router.push('/dashboard')
```



## 二. 路由动态参数

我们可以向路由传递一些参数

### 1. 基本步骤

- 在配置路径时指定要传递的参数,称为**路径参数**

  - 从`:`开始的标识符即为动态参数,比如`/user/:id`,`/user/:id/:password`,能传入参数id和id password; 与requestMapping的`{}`十分类似,动态段也不需要独占一级路径,类似`/user/a:id`也能正常支持

- 在组件中访问参数

  - 所有参数在组件中都作为`$route.params`的属性存在

    ```
     <div>{{$route.params.id}}</div>
    ```

- 其他参数:
  - 使用`$route.hash`可以访问url中以#开头的hash段
  - 使用`$route.query`可以访问url中的query段(以键值对即对象的方式存在)

### 2. 监控参数变化

如果路由不变而参数改变,组件不会被重新渲染,可以对params进行监控变化(但其本身会响应式变化)

### 3. 匹配所有路径

我们可以用简单的参数来匹配所有路径`/:paths`

## 三. 路由匹配

动态路由匹配可以提供正则表达式作为匹配依据，以此可以根据参数内容区分不同的url

- 实例：
  - `/user/:id(\\d+)`匹配数字而`/user/:name`匹配其他内容 参数还可以匹配零级一级或多级路径
- 实例
  - `/:user+` 匹配一级或多级目录，$route.params.user表现形式为数组
  - `/:user*` 匹配零级或多级目录
  - `/:user?` 匹配零级或一级目录 多级路径匹配可以与正则共用
- 实例
  - `/:user(\\d+)+`匹配一级或多级有数字目录
  - `/:id+/posts`可以这么用

## 附. 获取对应名字+参数的url路径

```js
 // 给定 { path: '/:chapters*', name: 'chapters' },
 router.resolve({ name: 'chapters', params: { chapters: [] } }).href
 // 产生 /
 router.resolve({ name: 'chapters', params: { chapters: ['a', 'b'] } }).href
 // 产生 /a/b
 
 // 给定 { path: '/:chapters+', name: 'chapters' },
 router.resolve({ name: 'chapters', params: { chapters: [] } }).href
 // 抛出错误，因为 `chapters` 为空 
```

## 四. 嵌套路由

官方教程的图搬过来,这是一个嵌套的组件结构,以及其对应的路由,我们如何来实现呢?

```
 /user/johnny/profile                     /user/johnny/posts
 +------------------+                  +-----------------+
 | User             |                  | User            |
 | +--------------+ |                  | +-------------+ |
 | | Profile      | |  +------------>  | | Posts       | |
 | |              | |                  | |             | |
 | +--------------+ |                  | +-------------+ |
 +------------------+                  +-----------------+
```

- 根app:

  依然是使用router-view标签

- User组件: 使用router-view

  ```
   <template>
       <div>
           <div>User</div>
           <div>{{$route.params.id}}</div>
           <router-view></router-view>
       </div>
   </template>
  ```

- 配置路径: children属性也是路由数组,可以继续嵌套

  > 注意: 如果children的目录以`/`开头,则默认从根路径匹配,即`/posts`时渲染user内部的posts,但/user/id/posts不会匹配当前配置的任何路径

  ```
   const routes = [
     {
       path: '/user/:id',
       component: User,
       children: [
         {
           // 当 /user/:id/profile 匹配成功 
           // UserProfile 将被渲染到 User 的 <router-view> 内部
           path: 'profile',
           component: UserProfile,
         },
         {
           // 当 /user/:id/posts 匹配成功
           // UserPosts 将被渲染到 User 的 <router-view> 内部
           path: 'posts',
           component: UserPosts,
         },
       ],
     },
   ]
  ```

- 默认渲染

  当没有指定children级的url时,可以指定一个默认的组件

  ```
   {
     path:'',
     component: Home
   }
  ```

## 五. 编程式导航

就是用js代码来切换路由,之前那种点击切换的叫声明式,这种叫编程式

所有导航方法都会返回一个Promise

- 核心方法`this.$router.push`:

### 1. push

- 传入字符串url

  ```
   // 字符串路径
   router.push('/users/eduardo')
  ```

- 传入对象:

  - 只有`path`属性,跟传入字符串url差不多

    ```
     router.push({ path: '/users/eduardo' })
    ```

  - `name`属性,根据路由名称定位url,优先级大于path

    ```
     this.$router.push({path:"/user/1/profile",name:'Home'});//Home生效
    ```

  - `query`: 对象,给传入的path或者name的url附加query串

    ```
     this.$router.push({path:"/user/1/profile",query: {a: 'haha',b : 'haha'}});
    ```

  - `hash`: 对象,给url后面添加hash字符串,建议以#开头

  - `params`: 给name的url中对应参数赋值; 若提供了path会忽略params

### 2. replace

和push就一样,只不过不会添加历史记录

### 3. 横跨历史

该方法采用一个整数作为参数，表示在历史堆栈中前进或后退多少步，类似于 `window.history.go(n)`。

例子

```js
// 向前移动一条记录，与 router.forward() 相同
router.go(1)

// 返回一条记录，与router.back() 相同
router.go(-1)

// 前进 3 条记录
router.go(3)

// 如果没有那么多记录，静默失败
router.go(-100)
```

## 六. 命名视图

有时候想要并列的渲染多个路由视图,就得给router-view起名字

```js
<router-view name="a"></router-view>
<router-view name="b"></router-view>
<router-view></router-view>
```

默认名字为`default`

### 1. 说明

这里并列三个router-view,并不是每个router-view对应一个路由;

而是本层 <u>一个路由 一次性指定 三个router-view对应的组件</u>

```js
{
  path: '/',
  name: 'Home',
  components: { // 注意: 这里加了s
    default:Home,
    a: User,
    b: User
  }
}
```

## 七. 重定向+别名

### 1. 重定向

在配置中指定路由的重定向路径(可以是相对路径)

本质: 替换url,匹配别的路由

语法1:

```js
{
    path: '/',
    redirect: '/home' // path字符串
}
```

语法2: 

```js
{
    path: '/',
    redirect: {path: '/home'} // 跟push的参数一致,也可以用name,query,params之类的
}
```

语法3: 方法

```js
{
    // /search/screens -> /search?q=screens
    path: '/search/:searchText',
    redirect: to => {
      // 方法接收目标路由作为参数
      // return 重定向的字符串路径/路径对象
      return { path: '/search', query: { q: to.params.searchText } }
    },
  },
```

> 导航守卫不应用于中间的路由
>
> 如果一个路由记录有 `children` 和 `redirect` 属性，它也应该有 `component` 属性。

### 2. 别名

就是把路由起别的名字,注意应当确保params包含在别名中

本质: 换名字

```js
const routes = [{ path: '/', component: Homepage, alias: '/home' }]
```

将嵌套路由简化: 

```js
const routes = [
  {
    path: '/users',
    component: UsersLayout,
    children: [
      // 为这 3 个 URL 呈现 UserList
      // - /users
      // - /users/list
      // - /people
      { path: '', component: UserList, alias: ['/people', 'list'] },
    ],
  },
]
```



## 八. props

params固然好用,但不能不让我们用props

### 1. 布尔

在routes中指定props可以被使用

```js
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const routes = [{ path: '/user/:id', component: User, props: true }]
```

当props为true时,**params**对应组件的props中的属性

对于命名视图,应该指定每一个视图对应的props的true/false

```js
const routes = [
  {
    path: '/user/:id',
    components: { default: User, sidebar: Sidebar },
    props: { default: true, sidebar: false }
  }
]
```

### 2. 对象

对props传入对象,就类似于直接对组件传入

```js
{
    path: '/user/:id/posts',
    name: 'Home',
    component: User,
    props:{
      aaa:'aaaaaaa' // 使用前提是组件定义了aaa这个props
    }
  }
```

### 3. 函数

函数返回一个对象,传入route,返回一个props对应的对象

```js
{
  path: '/user/:id/posts',
  name: 'Home',
  component: User,
  props:route=>{
    return {
      aaa: route.query.q
    }
  }
},
```

## 九. 历史模式

之前使用的历史模式都是hash历史模式

```js
const router = createRouter({
  history: createWebHashHistory(),
  routes
})
```

特点就是url中间会有#分隔,不太好

### 1. Html5模式

```
const router = createRouter({
  history: createWebHistory(),
  routes
})
```

这样会直接将路由拼到端口号/域名后边,对于部署在服务器上会出现问题

## 十. 导航守卫

导航守卫类似组件的钩子,在特定时机执行

守卫一般守在执行之前

### 1. 全局前置守卫

全局守卫由router定义

**全局前置守卫**: 是Promise,全局前置守卫按照顺序链式调用,最后才进行导航; 类似于前置的拦截器

```js
router.beforeEach((to, from) => {
  // ...
  // 返回 false 以取消导航
  return false
})
```

* 参数:
  * `to`: 要前往的route
  * `from`: 要离开的route
  * `next`: 回调函数(后面细说)
* 返回值:
  * `false`: 回滚到from的地址
  * `一个路由地址`: 跳转到别的地址,可以传一个传给push的对象,行为一样

next是第三个 可选的 参数,应当确保next只被调用一次

### 2. 全局解析守卫

你可以用 `router.beforeResolve` 注册一个全局守卫。这和 `router.beforeEach` 类似，因为它在 **每次导航**时都会触发，但是确保在导航被确认之前，**同时在所有组件内守卫和异步路由组件被解析之后，解析守卫就被正确调用**

### 3. 全局后置钩子

不接受next也不改变导航

第三个参数为failure

```js
router.afterEach((to, from) => {
  sendToAnalytics(to.fullPath)
})
```

### 4. 路由级别的守卫

`beforeEnter`是路由独享的守卫.

它应该定义在router中

```js
const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: (to, from) => {
      // reject the navigation
      return false
    },
  },
]
```

只会在进入路由时触发,不会在其他参数改变时触发; 

可以接受函数数组

### 5. 组件级别守卫

官方解释的挺到位了

```js
const UserDetails = {
  template: `...`,
  beforeRouteEnter(to, from) {
    // 在渲染该组件的对应路由被验证前调用
    // 不能获取组件实例 `this` ！
    // 因为当守卫执行时，组件实例还没被创建！
  },
  beforeRouteUpdate(to, from) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 `/users/:id`，在 `/users/1` 和 `/users/2` 之间跳转的时候，
    // 由于会渲染同样的 `UserDetails` 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 因为在这种情况发生的时候，组件已经挂载好了，导航守卫可以访问组件实例 `this`
  },
  beforeRouteLeave(to, from) {
    // 在导航离开渲染该组件的对应路由时调用
    // 与 `beforeRouteUpdate` 一样，它可以访问组件实例 `this`
  },
}
```

beforeRouteEnter不能使用this,若想用组件中的内容,应当调用next()并传入带有组件实例参数的回调函数

```js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
```

另外两个不支持这样做

##  完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫(2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫(2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。

## 十一. 路由元信息

定义在router中,跟path同级,保存一个元信息对象

使用方法: 当**路径匹配多个路由时**,可以用元信息判断前往何方

```js
const routes = [
  {
    path: '/posts',
    component: PostsLayout,
    children: [
      {
        path: 'new',
        component: PostsNew,
        // 只有经过身份验证的用户才能创建帖子
        meta: { requiresAuth: true }
      },
      {
        path: ':id',
        component: PostsDetail
        // 任何人都可以阅读文章
        meta: { requiresAuth: false }
      }
    ]
  }
]
```

一个路由匹配到的所有路由记录会暴露为 `$route` 对象(还有在导航守卫中的路由对象)的`$route.matched` 数组。我们需要遍历这个数组来检查路由记录中的 `meta` 字段，但是 Vue Router 还为你提供了一个 `$route.meta` 方法，它是一个非递归合并**所有 `meta`** 字段的（从父字段到子字段）的方法。这意味着你可以简单地写

```js
router.beforeEach((to, from) => {
  // 而不是去检查每条路由记录
  // to.matched.some(record => record.meta.requiresAuth)
  if (to.meta.requiresAuth && !auth.isLoggedIn()) {
    // 此路由需要授权，请检查是否已登录
    // 如果没有，则重定向到登录页面
    return {
      path: '/login',
      // 保存我们所在的位置，以便以后再来
      query: { redirect: to.fullPath },
    }
  }
})
```

