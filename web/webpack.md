# WebPack

WebPack是前端静态文件构建工具

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

