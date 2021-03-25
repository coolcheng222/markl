# WebPack

WebPack是前端静态文件构建工具

基于Node

由每个文件的import/require依赖形成依赖树,称为**Chunk**;处理打包输出为**bundle**

## 一. hello world

### 1. 安装

```bash
npm init -y
npm install webpack webpack-cli --save-dev
```

### 2. 基本打包

最基本的打包命令,将ES6模块变异成浏览器能识别的模块化

```bash
webpack ./src/index.js -o ./build/built.js --mode=development
# webpack 入口(entry) -o 输出(Output) 配置(比如--mode)

# 以一个入口,会打包其及其依赖; 没有loader的话只能处理js/json依赖
```

### 3. 配置文件

有了配置以后,webpack不需要任何参数就行

默认名`webpack.config.js`放在项目目录下

> 配置文件是一个Node模块,可以使用node的内置变量
>
> exports, require, module, __filename, __dirname

详见wevoacj.md

* 语法: **commonJS**

  ```javascript
  module.exports = {
      
  }
  ```

* 大概架构

  ```javascript
  //resolve: 用来拼接绝对路径的方法
  const {resolve} = require('path');
  module.exports = {
      entry: './src/index.js',
      output: {
          filename: 'built.js',
          //__dirname是nodejs的内置变量,即当前文件目录的绝对路径
          path:resolve(__dirname,'build')
      },
      //loader配置
      module:{
          rules: []
      },
      plugins: [],
      mode: "development"
  }
  ```

* loader配置:

  * 基本

  ```javascript
  module:{
      rules: [
          //需要用npm下载loader
          //符合正则表达式的,用use后面的loader来处理
          //css-loader: 转换为commonjs模块,内容为样式字符串
          //style-loader: 创建style标签,将js中的样式资源插入,并放在head中生效
          {test: /\.css/,use:['style-loader','css-loader']}
      ]
  }
  ```

## 二. 万物打包

### 1. 样式

见上一节的配置

### 2. html

使用plugin,插件也要用npm安装,并且应该先commonjs引入

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin');
//返回一个构造函数
```

```javascript
const {resolve} = require("path");
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    entry:'./src/index.js',
    output: {
        filename: 'built.js',
        path: resolve(__dirname,'build')
    },
    module:{
        rules: []
    },
    plugins:[new HtmlWebpackPlugin()],
    mode: 'development'
}
```

成果: 生成一个自动引入打包资源的index.html

* 当然没这么简单,我们可以将现存的html和生成的js打包在一起

  * 只需要给HtmlWebpackPlugin构造器添加选项即可

  ```javascript
  plugins:[new HtmlWebpackPlugin({template: "./src/index.html"})],
      //给打包的html添加模板
  ```