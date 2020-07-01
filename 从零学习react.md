# React
## React开发环境的搭建
1. 安装nojs，直接到nojs官网下载安装即可，安装完成后打开cmd命令行窗口，输入  node -v，如果安装成功，则返回nodejs安装版本号；输入  npm -v，如果安装成功，则返回npm安装版本号
```
// 表示安装成功
C:\Users\user>node -v
v12.14.1
C:\Users\user>npm -v
6.14.2
```
2. 安装React脚手架，react的脚手架有很多，此处使用 create-react-app，也是官方推荐的一款脚手架, 安装命令很简单，只需要在命令行窗口中输入  npm install -g create-react-app 全局安装， 等待安装完成即可，安装时间跟网速有关。
```
npm install -g create-react-app
```
## 使用脚手架工具创建一个react项目
- 使用  create-react-app  创建react项目只需一步，即 create-react-app my-react
```
create-react-app my-react
// 创建完成之后，进入到项目目录，执行一下命令
npm start
或
yarn start
```
## 项目目录说明
```
node_modules
  ......  // 依赖包
public
  favicon.ico // 项目图标
  index.html  // 模版文件
  logo192.png // 图片
  logo512.png // 图片
  mainifest.json  // 移动端配置文件
  robots.txt  // 
src
  App.css // App引入的css
  App.js  // 方法模块
  App.test.js // 测试
  index.css // index引入的css文件
  index.js  // 项目入口文件
  logo.svg  // 图片
  serviceWorker.js  // 用于移动端开发
  setupTest.js  // 测试
.gitignore  // git配置文件
package.json  // webpack配置和项目包管理文件
README.md // 这个文件是对项目的说明
yarn.lock // 锁定安装时的版本号，保证每一个人在yarn install时大家的依赖一致，这里使用的是yarn安装，所以文件名为yarn.lock，如果使用npm安装，文件名为package-lock.json
```
## 第一个hello world程序
<strong>把src目录下的文件全部删除，开始编写hello world程序</strong>
- 
1. 创建入口文件index.js
```javascript
// 引入react和react-dom
import React from 'react'
import ReactDOM from 'react-dom'
import Fruits from './Fruits'

ReactDOM.render(<Fruits />, document.getElementById('root'))
```