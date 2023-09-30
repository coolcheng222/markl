# Vue-cli

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

## 