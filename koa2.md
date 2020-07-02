# Koa2
## koa的安装
- 使用koa的前提是要安装node，并且版本不能太低
```
npm install koa --save
```

## koa的入门使用
```javascript
// 引入koa
const Koa = require('koa')
// 创建koa实例
const app = new Koa

app.use(async(ctx) => {
  console.log('hello koa2')
})

// 设置服务监听端口
app.listen(3000, () => {
  console.log('监听端口回调函数')
})
```

## GET请求的接受
- 在app.use中可以请求携带的参数
```javascript
app.use(async(ctx) => ) {
  // ctx里面包含各种参数
  const url = cxt.url // url为请求的路径

  // 可以从request中获取请求的参数
  const request = ctx.request
  const req_query = request.query // 对象参数
  const req_querystring = request.querystring // 字符串参数

  // 也可以直接从ctx中直接获取请求的参数
  const ctx_query = ctx.query // 对象参数
  const ctx_querystring = ctx.querystrig  // 字符串参数

  // 直接使用body输出内容
  ctx.body = {
    url,
    req_query,
    req_querystring,
    ctx_query,
    ctx_querystring
  }
}
```