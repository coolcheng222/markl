# WebPack配置
默认webpack配置文件为`webpack.config.js`
## 一. 概述
### 1. 基本语法
```javascript
module.exports = {options}
```
Entry: 作为webpack打包的入口,默认值为`./src/index.js`
output: 默认输出的创建的bundle，默认主文件为`./dist/main.js`,其他文件放在./dist下
```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js',
  },
};
```
loader: webpack默认支持js和json文件,其他文件需要用loader转换为有效模块,后面细说
plugin: 插件,后面细说
mode: 分为`development`,`production`或`none`
## 二. Entry
### 1. 简单Enrty语法
* 单个文件入口
```javascript
module.exports = {
  entry: './path/to/my/entry/file.js',
};
//或者
module.exports = {
  entry: {
    main: './path/to/my/entry/file.js',
  },
};
```
* 多个文件(传递数组)
module.exports = {
  entry: ['./src/file_1.js', './src/file_2.js'],
  output: {
    filename: 'bundle.js',
  },
};
### 2. 对象语法
当对entry复制对象时,遵从__属性为chunkName,值为路径或者对象__的规律
```javascript
module.exports = {
  entry: {
    app: './src/app.js',
    adminApp: './src/adminApp.js',
  },
};
```
* 当值为对象时,可以使用这些属性:
  * dependOn: 当前入口所依赖的入口。它们必须在该入口被加载前被加载。
    * 不可循环依赖
  * filename: 指定要输出的文件名称。
  * import: 启动时需加载的模块。
  * library: library 的相关选项。
  * runtime: 运行时 chunk 的名字。如果设置了，就会创建一个以这个名字命名的运行时 chunk，否则将使用现有的入口作为运行时。
    * runtime的入口名称不能指向已存在的入口
    * runtime和dependOn不应该在同一个入口使用