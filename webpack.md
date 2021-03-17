# webpack

## 安装

### 前提条件

在开始之前，请确保安装了`Node.js`的最新版本。

### 本地安装

要安装最新版本或特定版本，请运行以下命令之一：

```
npm install --save-dev webpack
npm install --save-dev webpack@<version>
```

如果使用的是webpack4+版本，还需要安装CLI。

```
npm install --save-dev webpack-cli
```

对于大多数项目，建议本地安装。这样可以使我们在引入破坏式变更的依赖时，更容易分别升级项目。通常，webpack通过运行一个或多个`npm scripts`,会在本地`node_modules`目录中查找安装的webpack:

```json
"scripts": {
	"start": "webpack --config webpack.config.js"
}
```

### 全局安装

以下的NPM安装方式,将使webpack在全局环境下可用:

```
npm install --global webpack
```

## 起步

webpack用于编译JavaScript模块。一旦完成安装,就可以通过webpack的CLI或API与其配合交互.

### 基本安装

首先创建一个目录，初始化npm，然后在本地安装webpack，接着安装webpack-cli（此工具用于命令行中运行）：

```
mkdir webpack-demo && cd webpack-demo
npm init -y
npm install webpack webpack-cli --save-dev
```

> 贯穿整个指南的是，我们将使用`diff`块，来显示我们对目录、文件和代码所做的更改。

现在将创建以下目录结构、文件和内容：

project：创建index.html、src/index.js

src/index.js

```js
function component() {
    var element = document.createElement('div')
    
}
```





