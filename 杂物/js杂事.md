## 一. v-for中对变量的处理结果保存

### 1. 情境描述 

假设拥有一个vue component

```vue
<template>
	<div v-for="a in list">
        {{deal(a).name}}
        {{deal(a).id}}
    </div>
</template>
```

可以看到,我们其实需要deal(a)运行一次并保留一个结果,而不是运行多次

如果是在script中,我们可以定义一个变量保存运行结果,但在v-for中,该怎么办?

### 2. 尝试解决方案

参考文献: 

https://stackoverflow.com/questions/43999618/how-to-define-a-temporary-variable-in-vue-js-template#comment100041090_56249209

根据最高人气的回答,可以使用声明语句直接在v-for中定义一个变量

```vue
<li v-for="id in users" :key="id" :set="user = getUser(id)">
  <img :src="user.avatar" />
  {{ user.name }}
  {{ user.homepage }}
</li>
```

后根据评论区反映,如果将该变量放入事件,会导致反复的re-render,所以它与事件一起用的时候,应当将声明放在v-for之外

```html
<ul :set="tmpUsers = []">
  <li v-for="(id, i) in users" :key="id" :set="tmpUsers[i] = getUser(id)" @click="showUser(tmpUsers[i])">
    <img :src="tmpUsers[i].avatar" />
    {{ tmpUsers[i].name }}
    {{ tmpUsers[i].homepage }}
  </li>
</ul>
```

## 二. v-model配合可读写的computed

问题出在props=defineProps在vue4.0.0是不能响应的,也就是必须接props.才能使用,这种事确实比较麻烦

但是gpt推荐了一种可写computed语法

```js
const modelValue = computed({
  get() {
    return props.modelValue;
  },
  set(newValue) {
    emit('update:modelValue', newValue);
  }
});
```

但这是set,也就是赋值的时候变化,测一下是否是这样,然后查到了一个defineModel()语法糖

```js
<script setup>
const model = defineModel()

function update() {
  model.value++
}
</script>

<template>
  <div>Parent bound v-model is: {{ model }}</div>
  <button @click="update">Increment</button>
</template>
```

带名字的:

```js
const title = defineModel('title')
```

## 三. 破解底层为electron的staruml

​	大体步骤: 拆asar,修改,装回去

1. 安装asar

```sh
npm i -g asar
```

2. 解压(复制到其他目录,确保有目录权限)

```sh
asar e app.asar app
```

> 这连源代码都有,感觉以后可以读一下

3. 找到src/engine/licence-manager.js

```js
async checkLicenseValidity() {
    setStatus(this, true);
    // if (packageJSON.config.setappBuild) {
    //   setStatus(this, true);
    // } else {
    //   try {
    //     const result = await this.validate();
    //     setStatus(this, true);
    //   } catch (err) {
    //     const remains = this.checkEvaluationPeriod();
    //     const isExpired = remains < 0;
    //     const result = await UnregisteredDialog.showDialog(remains);
    //     setStatus(this, false);
    //     if (isExpired) {
    //       app.quit();
    //     }
    //   }
    // }
}
```

```js
  getLicenseInfo() {
    return {
      name:"aa",
      product:"StarUML",
      licenseType:"PRO",
      quantity:112,
      timestamp:123123
    };
  }
```

4. app/src/app-context.js

```js
if (!this.config.setappBuild) {
    if (this.preferences.get("checkUpdate.checkUpdateOnStart")) {
        // ipcRenderer.send("check-update");
        // 自动更新禁用
    }
}
```

5. 打包

```js
asar p app app.asar
```

