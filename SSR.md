# SSR
## 安装依赖
```
npm install vue vue-server-renderer express -D
```
## 启动脚本
创建一个express服务器，将vue ssr集成进来
```javascript
// 导入express作为渲染服务器
const express = require('express')
// 导入Vue用于声明待渲染实例
const Vue = require('vue')
// 导入createRenderer用于获取渲染器
const { createRenderer } = require('vue-server-renderer')
// 创建express实例
const app = express()
// 获取渲染器
const renderer = createRenderer()
// 待渲染vue实例
const vm = new Vue({
	data: {
		foo: ''
	},
	template: `
		<div>
			<h1>{{ foo }}</h1>
		</div>
	`
})

app.get('/', async function(req, res) {
	// renderToString可以将vue实例转换为html字符串
	// 若未传递回调函数，则返回Promise
	try {
		const html = await renderer.renderToString(vm);
		res.send(html)
	} catch(error) {
		res.status(500).send('Internal Server Error')
	}
})

app.listen(3000, () => {
	console.log('server is running')
})
自动启动nodemon
```