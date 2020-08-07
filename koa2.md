# Koa2
## koa的安装
- 使用koa的前提是要安装node，并且版本不能太低
```
npm install koa --save
```

### 常用中间件

1. koa-bodyparser  请求体解析

便于获取请求参数

```
npm i koa-bodyparser --save
```

2. jsonwebtoken	koa-jwt	token鉴权

用户生成和验证token

```
npm i jsonwebtoken --save
npm i koa-jwt --save
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

## 常用组件

1. 

## GET请求的接收

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
* GET请求的方式由两种，一种是从request中获取，一种是直接从上下文中获取。获取的值的格式有两种，字符串和对象。

## POST请求的接收
- 对于POST请求，Koa2没有方便的获取参数的方法，需要通过解析上下文context中的原生node.js请求对象req来获取。

### 获取Post请求的步骤：
1. 解析上下文ctx中的原生node.js对象req；
2. 将POST表单数据解析成query string-字符串（例如：user=zhangsan&age=18）；
3. 将字符串转换成JSON格式。

### ctx.request和ctx.req的区别
- ctx.request: 是Koa2中context经过封装的请求对象，它用起来更加直观和简单；
- ctx.req: 是contex提供的node.js原生HTTP请求对象。这个虽然不是很直观，但是可以得到更多的内容，更适合深度编程。

### ctx.method得到请求类型
- Koa2中提供了ctx.method属性，可以轻松的得到请求的类型，然后根据请求类型编写不同的对应方法。

### 解析Node原生POST参数
```javascript
app.use(async(ctx) => {
  const postData = await parsePostData(ctx)
  ctx.body = postData
})
// 解析post数据，转化对象
function parsePostData(ctx) {
  return new Promise((resolve, reject) => {
    try{
      let postdata = ''
      // 监听数据，赋值给定义的postdata（字符串数据）
      ctx.req.on('data', (data) => {
        postdata += data
      })
      // 结束操作
      ctx.req.on('end', () => {
        const parseData = parseQueryStr(postdata)
        resolve(parseData)
      })
    } catch(error) {
    reject(error)
    }
  })
}
// 将字符串转化为对象
function parseQueryStr(queryStr){
  let queryData={};
  let queryStrList = queryStr.split('&');
  console.log(queryStrList);
  for( let [index,queryStr] of queryStrList.entries() ){
    let itemList = queryStr.split('=');
    console.log(itemList);
    queryData[itemList[0]] = decodeURIComponent(itemList[1]);
  } 
  return queryData
}
```

## koa-bodyparser中间件（更便于获取post的请求参数）

- 使用中间件的第一步需要安装

```
npm install --save koa-bodyparser
```

- 使用的时候直接引入使用即可

```javascript
const Koa = require('koa')
const bodyParser = require('koa-bodyparser')
app.use(bodyParser())
```

- 上方的代码使用后，就可以直接在 ctx.request.body 中获取POST请求的参数

```javascript
// 获取POST请求的参数
app.use(async(ctx) => {
    // 获取
    const params = ctx.request.body
    ctx.body = params
})
```

## Koa2中路由的实现

### 原生路由的实现方法

- 在koa2中，想要实现原生路由的功能，就是要根据不同的路径取进行跳转，需要借助 ctx.request.url 获取路径。

```javascript
// 获取路径
app.use(async(ctx) => {
    const url = ctx.request.url	// 路径
    ctx.body = url
})
```

- 原生路由的实现需要引入 fs 模块来读取文件。根据路由获取路径，通过 fs 读取文件，返回页面，进行渲染。

```javascript
const Koa = require('koa')
const fs = require('fs')

const app = new Koa()

app.use(async(ctx) => {
  const page = await route(ctx)
  ctx.body = page
})
// 根据路径判断要显示的页面路径
async function route(ctx) {
  const url = ctx.request.url
  let page = '404.html'
  switch(url) {
    case '/': {
      page = 'index.html'
      break
    }
    case '/':
      page = 'index.html'
      break
    case '/artical':
      page = 'artical.html'
      break
    case '/music':
      page = 'music.html'
      break
    case '/news':
      page = 'news.html'
      break
    case '/picture':
      page = 'picture.html'
      break
    default:
      break
  }
  return render(page)
}
// 通过fs获取需要渲染的页面文件
function render(page) {
  return new Promise((resolve, reject) => {
    fs.readFile(`./pages/${page}`, 'utf8', (err, data) => {
      if (err) {
        reject(err)
      } else {
        resolve(data)
      }
    })
  })
}

app.listen(3000, () => {
  console.log('starting')
})
```

### 通过koa-router中间件实现路由

#### 安装koa-router

```
npm install koa-router --save
```



#### 基本的路由实现

```javascript
const Koa = require('koa')
const Router = require('koa-router')

const app = new Koa()
const router = new Router()	// 创建路由实例
// 路由配置
router
  .get('/demo1', (ctx, next) => {
    ctx.body = 'demo1'
  })
  .get('/demo2', (ctx, next) => {
    ctx.body = 'demo2'
  })
// 添加实现路由功能
app
  .use(router.routes())		// 使用路由功能
  .use(router.allowedMethods())		// 固定路由请求类型

app.listen(3000, () => {
  console.log('starting')
})
```

#### 路由的层级关系

- 可实现多级路由

```JavaScript
const Koa = require('koa')
const Router = require('koa-router')

const app = new Koa()

const home = new Router()
// 配置路由前缀
home.prefix('/home')
home
  .get('/', async(ctx, next) => {
    ctx.body = 'home'
  })
  .get('/todo', async(ctx, next) => {
    ctx.body = 'home_todo'
  })

const main = new Router()
// 配置路由前缀
main.prefix('/main')
main
  .get('/', async(ctx, next) => {
    ctx.body = 'main'
  })
  .get('/todo', async(ctx, next) => {
    ctx.body = 'main_todo'
  })

const page = new Router()
page
  .use('/page', home.routes(), home.allowedMethods())

const router = new Router()
router
  .use('/api', home.routes(), home.allowedMethods())
  .use('/api', main.routes(), main.allowedMethods())
  .use('/api', page.routes(), page.allowedMethods())

app
  .use(router.routes())
  .use(router.allowedMethods())

app.listen(3000, () => {
  console.log('starting')
})
```

#### 路由传参

```javascript
// 地址上获取传参
router
    .get('/hello/:name', async(ctx, next) => {
		var name = ctx.params.name
        console.log(name)
        ctx.body = `hello ${name}`
    })
// ctx.query进行接收
router
	.get('/', function (ctx, next) {
    	ctx.body=ctx.query;
	})
```

## cookie

- 如果要存储用户名，保留用户登录状态，可以选择7天内不用登录，也可以选择30天内不用登录。这时就需要写入配置一些选项：

```javascript
const Koa = require('koa')
const app = new Koa()

app.use(async(ctx) => {
    if (ctx.url === '/index') {
        // 写cookie
        ctx.cookies.set(
        	'MyName', 'Hello', {
                domain: '127.0.0.1', // 写cookie所在的域名
                path: '/inde', // 写cookie所在的路径
                maxAge: 1000 * 60 * 60 * 24, // cookie有效时长
                expires: new Date('2020-07-11'), // cookie失效时间
                httpOnly: false, // 是否只用于http请求中获取
                overwrite: false // 是否允许重写
            }
        )
        ctx.body = 'cookie is ok'
    } else {
        // 读cookie
        if( ctx.cookies.get('MyName')){
            ctx.body = ctx.cookies.get('MyName')
            ctx.body = 'Cookie is ok'
        }else{
            ctx.body = 'Cookie is none'
        }
    }
})

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
})
```

## koa2模版引擎 ejs

### 安装中间件

-  在koa2中使用模板机制必须依靠中间件，我们这里选择koa-views中间件，先使用npm来进行安装。 

```
npm install --save koa-views	// 注意是 koa-views
```

### 安装 ejs 模版引擎

-  ejs是个著名并强大的模板引擎，可以单独安装。 

```
npm install --save ejs
```

-  安装好ejs模板引擎后，就可以编写模板了，为了模板统一管理，我们新建一个view的文件夹，并在它下面新建index.ejs文件。 

```html
<!--
	views/index.ejs
	使用ejs语法进行页面渲染
-->
<!DOCTYPE html>
<html>
<head>
    <title><%= title %></title>http://jspang.com/wp-admin/post.php?post=2760&action=edit#
</head>
<body>
    <h1><%= title %></h1>
    <p>EJS Welcome to <%= title %></p>
</body>
</html>
```

### 编写koa文件

- 有了模版文件，需要在js文件中配置并渲染。

```javascript
const Koa = require('koa')
const views = require('koa-views')
const path = require('path')
const app = new Koa()

// 加载模板引擎
app.use(views(path.join(__dirname, './view'), {
  extension: 'ejs'
}))

app.use( async ( ctx ) => {
  let title = 'hello koa2'
  await ctx.render('index', {
    title
  })
})

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
})
```

## koa-static静态资源中间件

-  在后台开发中不仅有需要代码处理的业务逻辑请求，也会有很多的静态资源请求。比如请求js，css，jpg，png这些静态资源请求。也非常的多，有些时候还会访问静态资源路径。用koa2自己些这些静态资源访问是完全可以的，但是代码会雍长一些。所以这节课我们利用koa-static中间件来实现静态资源的访问。 

### 安装中间件

```
npm install --save koa-static
```

###  配置获取静态文件

**新建static文件夹** 然后在static文件中放入图片，css和js文件。 

```javascript
const Koa = require('koa')
const path = require('path')
const static = require('koa-static')

const app = new Koa()

const staticPath = './static'

// 使能在服务下访问static目录下的静态文件
// 服务启动后直接通过服务名称+文件名访问即可
app.use(static(
  path.join( __dirname,  staticPath)
))

app.use( async ( ctx ) => {
  ctx.body = 'hello world'
})

app.listen(3000, () => {
  console.log('[demo] static-use-middleware is starting at port 3000')
})
```

## 连接数据库

### 连接mysql数据库

#### 项目中安装mysql模块

```
npm install mysql --save
```

#### 配置与连接

1.  createConnection （Object）方法

```JavaScript
// 引入mysql
const mysql = require('mysql')
// 连接参数
const config = {
  host: 'localhost',  // 数据库地址
  port: '3306',  // 端口号
  user: 'root',  // 用户名
  password: 'root',  // 密码
  database: 'ywhk_cmbk',  // 数据库
  connectionLimit: 100  // 最大连接数
}
// 建立连接
const connection = mysql.createConnection(config)

// 连接
connection.connect(err => {
  if (err) throw err
  console.log('连接成功: connected as id ' + connection.threadId)
})

// 查询
// 创建query函数  可用于调用
const query = (sql) => {
  return new Promise((resolve, reject) => {
    // 执行查询操作
    connection.query(sql, (error, results) => {
      if (error) {
        // 查询失败
        reject(error)
      }
      // 查询成功
      resolve(results)
    })
  })
}

// 关闭连接
connection.end(err => {
  if (err) {
    return 
  }
  console.log('[connection end] succeed')
})
```

2. 创建连接池 createPool()

```javascript
const mysql = require('mysql')
// 连接参数
const config = {
  host: 'localhost',  // 数据库地址
  port: '3306',  // 端口号
  user: 'root',  // 用户名
  password: 'root',  // 密码
  database: 'ywhk_cmbk',  // 数据库
  connectionLimit: 100  // 最大连接数
}

// 连接池连接
const pool = mysql.createPool(config)

const query = (sql) => {
  console.log(sql)
  // 连接
  return new Promise((resolve, reject) => {
    pool.getConnection((err, connection) => {
      if (err) {
        // 连接失败
        reject(err)
      } else {
        // 连接成功
        connection.query(sql, (err, rows) => {
          if (err) {
            reject(err) // 查询失败
          } else {
            resolve(rows)
          }
          // 释放连接 连接不再使用，返回到连接池
          // connection.release()
          pool.releaseConnection(connection)
        })
      }
    })
  })
}
```

### 连接sqlserver数据库

#### 项目中安装mssql模块

```
npm install mssql --save
```

#### 配置与连接

1. connect连接

```JavaScript
const sql = require('mssql') //声明插件
sql.connect(config).then(() => {
    return sql.query`select * from mytable where id = ${value}`
}).then(result => {
    //请求成功
}).catch(err => {
    //err 处理
})
sql.on('error', err => {
    //error 处理
})
```

2. pool连接池

```JavaScript
const mssql = require('mssql')
// 连接参数
const db_config = {
  user: 'sa',
  password: '123$%^A',
  server: '192.168.1.198',
  database: 'ZL_CWKLDGE',
  options: {
    encrypt: true
  },
  pool: {
    max: 10,
    min: 0,
    idleTimeoutMillis: 30000
  }
}
// 创建连接
const db_pool = new mssql.ConnectionPool(db_config);
const query = (sql) => {
  return new Promise((resolve, reject) => {
    db_pool.connect().then(pool => {
      // 连接成功
      return pool.query(sql)
    }).then(result => {
      // 查询结果
      resolve(result)
      db_pool.close()
    }).catch(err => {
      // 连接失败
      reject(err)
      db_pool.close()
    })
  })
}
```

## Koa2使用JWT进行鉴权

> Json Web Token 简称JWT，它定义了一种简洁、自包含的用于通信双方之间以JSON对象的形式安全传递信息的方法。JWT可以使用HAMC算法或者时RSA的公钥密钥对进行签名。

![img](https://pic3.zhimg.com/80/v2-2d70ab1b6246a93cb09408cc231fb7c7_720w.jpg)

1. 首先用户登录时，输入用户名和密码后请求服务器登录接口，服务器验证用户密码正确后，生成token并返回给前端，前端存储token，并在后面的请求中把token带在请求头中传给服务器，服务器验证token有效，返回正确的数据
2. 既然服务器端使用Koa2框架进行开发，除了要使用jsonwebtoken库之外，还要使用一个koa-jwt中间件，该中间件针对Koa对jsonwebtoken进行了封装，使用起来更加方便。

### 生成token

```javascript
const router = require('koa-router')()
const jwt = require('jsonwebtoken')
// 此处只显示部分代码
// 1.根据用户名和密码进行登录，判断是否存在对应的用户信息，如果没有直接返回没有的相关信息，有则生成token并返回
// 2.生成token    假设resule是查询到的用户信息  含有字段 id 和 name
const token = jwt.sign({
    name: result.name,
    id: result.id
}, 'my_token', { expiresIn: '2h' })
// token即为我们生成的token
```

在验证了用户名密码正确之后，调用jsonwebtoken的sign()方法来生成token，接收三个参数，第一个是载荷，用于编码后存储在token中的数据，也是验证token后可以拿到的数据；第二个是密钥，自己定义的，验证的时候也是要相同的密钥才能解码；第三个是options，可以设置token的过期时间。

### 获取token

调用登录接口，待登录成功之后则会返回token，然后把token存在本地，在请求服务器API的时候，把token带在请求头中传给服务器进行验证。

### 验证token

通过koa-jwt中间件进行验证。

```javascript
const koa = require('koa')
const koajwt = require('koa-jwt')
const app = new koa()

// 错误处理
app.use((ctx, next) => {
    return next().catch((err) => {
        if(err.status === 401){
            ctx.status = 401;
            ctx.body = 'Protected resource, use Authorization header to get access\n';
        }else{
            throw err;
        }
    })
})

app.use(koajwt({
    secret: 'my_token'
}).unless({
    path: [/\/user\/login/]
}));
```

通过app.use来调用该组件，并传入密钥`{secret: 'my_token'}`,unless可以指定哪些url不需要进行token验证。token验证失败的时候会抛出401错误，因此需要添加错误处理，而且要放在`app.use(koajwt())`之前，否则不执行。

如果请求时没有token或者token过期，则返回401.

### 解析koa-jwt

```javascript
module.exports = function resolveAuthorizationHeader(ctx, opts) {
    if (!ctx.header || !ctx.header.authorization) {
        return;
    }
    const parts = ctx.header.authorization.split(' ');
    if (parts.length === 2) {
        const scheme = parts[0];
        const credentials = parts[1];
        if (/^Bearer$/i.test(scheme)) {
            return credentials;
        }
    }
    if (!opts.passthrough) {
        ctx.throw(401, 'Bad Authorization header format. Format is "Authorization: Bearer <token>"');
    }
};
```

判断请求头中是否带了authorization，如果有，将token从authorization中分离出来。如果没有authorization，则代表客户端没有传token到服务器，这时候就抛出401错误状态。

### verify.js

```javascript
const jwt = require('jsonwebtoken');

module.exports = (...args) => {
    return new Promise((resolve, reject) => {
        jwt.verify(...args, (error, decoded) => {
            error ? reject(error) : resolve(decoded);
        });
    });
};
```

在verify.js中，使用jsonwebtoken提供的verify()方法进行验证并返回结果。jsonwebtoken的sign()方法是用来生成token的，二verify()方法则是用来认证和解析token。如果token无效，则会在此方法被验证出来。

### index.js

```javascript
const decodedToken = await verify(token, secret, opts);
if (isRevoked) {
	const tokenRevoked = await isRevoked(ctx, decodedToken, token);
	if (tokenRevoked) {
		throw new Error('Token revoked');
	}
}
ctx.state[key] = decodedToken;  // 这里的key = 'user'
if (tokenKey) {
	ctx.state[tokenKey] = token;
}
```

在index.js中，调用verify.js的方法进行验证并解析token，拿到上面进行sign()的数据`{name: resule.name, id: result.id}`，并赋值给`ctx.state.user`，在控制器中便可以直接通过`ctx.state.user`拿到`name`和`id`。