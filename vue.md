# VUE笔记
## 安装模块的时候使用npm加载慢，可以使用淘宝镜像
```
// 尽量不要使用cnpm，可能会有各种各样的bug
npm install --registry=https://registry.npm.taobao.org
```
## 一个vue项目的安装、router配置、store配置完整步骤
### 第一步：使用vue-cli安装vue项目，router和store另自行安装配置
### 第二步：安装配置router
1. 安装router
```
npm i vue-router --save-dev
```
2. 配置router
  - 在src文件夹下新建router文件夹 --> index.js
  ```
  src
    - router
      - index.js
  ```
  - 在src -> router -> index.js中配置
  ```javascript
  // 1. 引入vue和vue-router
  import Vue from 'vue'
  import Router from 'vue-router'
  // 模块路由可以写在其他的文件夹下，在index.js中引入即可
  import routes from './modules/routes'
  // 2. 在vue中使用router
  Vue.use(Router)

  // 3. 创建一个router
  const router = new Router ({
    mode: 'history',
    routes
  })

  // 4. 导航前置守卫与导航后置守卫
  // 设置路由导航前置守卫，进入路由前的操作
  router.beforeEach((to, from, next) => {
    // to: 即将前往的页面路由
    // from: 即将离开的页面路由
    // next: 必须含有，进行页面跳转
    console.log(to, from, next)
    // 在这里面可以进行路由菜单的获取
  })

  // 导航后置守卫，进入路由后的操作
  router.afterEach(() => {
    // loadingInstance.close()
  })

  // 5. 将路由导出即可
  export default router
  ```
### 第三步：状态管理(vuex)的安装与配置
1. 安装vuex
```
npm i vuex --save-dev
```
2. vuex的介绍
  - State：单一状态树，用来存储数据，和vue中的data有着相同的规则；
  ```javascript
  state: {
    name: 'zhangsan',
    age: 18
  }
  ```
  - Getter：有时候需要从store中的state派生出一些状态，例如对列表数据进行筛选及计算，Getter接受state作为其第一个参数；
  ```javascript
  getters: {
    age: state => {
      return state.age + 2 // 20 
    }
  }
  // 但是在一般使用中吧getters都可以放到一个文件中，这样在使用数据的时候能够更加明了，下面再进行介绍
  ```
  - Mutation：更改vuex中state中的状态的唯一方法就是提交mutation，mutation接受s't
    state作为其第一个参数
  ```javascript
  mutations: {
    SET_NAME: (state, name) => {
      state.name = name
    }
  }
  ```
  - Action：Action可以包含任意异步操作，mutation是同步操作，action变更状态只能提交mutation进而改变状态
  ```javascript
  actions: {
    SetName ({ commit }, name) {
      commit('SET_NAME', name)
    }
  }
  ```
  - Mudule：Vuex 允许我们将 store 分割成模块（module）
  ```javascript
  const moduleA = {
    state: { ... },
    mutations: { ... },
    actions: { ... },
    getters: { ... }
  }

  const moduleB = {
    state: { ... },
    mutations: { ... },
    actions: { ... }
  }

  const store = new Vuex.Store({
    modules: {
      a: moduleA,
      b: moduleB
    }
  })

  store.state.a // -> moduleA 的状态
  store.state.b // -> moduleB 的状态
  ```
### vuex的具体配置
- 目录
```
store
  - modules
    - app.js
  - getters.js
  - index.js
```
```javascript
// app.js
const app = {
  namespace: true,  // 命名空间
  state: {
    name: 'zhangsan',
    age: 18
  },
  mutations: {
    SET_NAME: (state, name) => {
      state.name = name
    }
  },
  actions: {
    SetName ({ commit }, name) {
      commit('SET_NAME', name)
    }
  }
}

export default app
```
```javascript
// getters.js
const getters = {
  name: state => state.app.name
}

export default getters

```
```javascript
// index.js
import Vue from 'vue'
import Vuex from 'vuex'
import app from './modules/app'
import getters from './getters'
Vue.use(Vuex)

const store = new Vuex.Store({
  modules: {
    app
  },
  getters
})

export default store
```
### main.js中引入
```javascript
import router from '@/router'
import store from '@/store'

new Vue({
  el: '#app',
  router,
  store,
  components: { App },
  template: '<App/>'
})
```
## 路由的懒加载
- 在路由组件的调用时，当有很多路由组件的时候，我们需要设置路由懒加载，但是当在开发环境下，较多的路由可能会导致路由加载较慢，所有应该分环境进行路由组件的加载。
1. 在路由文件夹下新建开发环境映入方式（_import_development.js）和生成环境（_import_production.js）引入方式
```javascript
// _import_development.js
module.exports = file => require('@/views/' + file + '.vue').default
// 开发环境下使用require的引入方式，运行时调用，使开发状态下能够更快加载路由
```
```javascript
// _import_production.js
module.exports = file => () => import('@/views/' + file + '.vue')
// 生产环境下使用import，编译时调用，实现懒加载
```
2. 设置路由的时候
```javascript
const _import = require('./_import_' + process.env.NODE_ENV)
 
export default new Router({
  routes: [{ path: '/login', name: '登陆', component: _import('login/index') }]
})
```

## vuex的相关使用

<a href="#mapState">mapState</a>,<a href="#mapActions">mapActions</a>,<a href="#mapGetters">mapGetters</a>,<a href="#mapMutations">mapMutations</a>

### vuex中数据的调用
```javascript
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
export default new Vuex.Store({
  state: { //存放状态
    nickname:'zhangsan',
    age:20,
    gender:'男'
  },
  mutations: {
      setAge(state, age) {
          state.age = age	// 修改状态
      }
  },
  getters: {
      // 类似mutations
      welcome(state) {
          return state.nickname + '欢迎回来'
      }
  },
  actions: {
      login({ commit }, username) {
          // login默认接收第一个参数vuex实例，从中可以分离出{ commit, dispatch }可供使用
          return new Promise(() => {	// 模拟异步登录
              setTimeout((resolve) => {
                  commit(username)	// 得到数据后提交mutations
                  resolve(true)
              }, 1000)
          })
      }
  },
  modules: {},	// 用于分割模块
  strict: true	// 严格模式，可防止用户手动修改，详细介绍见下文
})
```
使用

- 可以直接在vue元素上直接调用状态值
```html
<div class="home">{{ $store.state.nickname }}</div>
```
- 在computed中添加返回状态值
```javascript
<template>
  <div class="home">
    {{ nickname }}
  </div>
</template>
<script>
export default {
  name: 'home',
  computed:{
    nickname(){
      return this.$store.state.nickname
    }
  }
}
</script>
```

- 更改状态值的方法

1. 通过mutations调用对应方法更改状态值

```javascript
// 在.vue文件中使用
this.$store.commit('setAge', 18)

// 在.js文件中使用，	首先需要引入store
import store from '@/store'
store.commit('setAge', 18)
```

2. 遇到异步交互时更改状态值

vuex规定更改 Vuex 的 store 中的状态的唯一方法是提交 mutation，所以还是需要调用mutations的方法，但是需要的一些异步方法不能在mutations里面进行。

vuex提供了actions供我们去对数据进行处理。

```javascript
// .vue文件中调用actions方法
this.$store.dispatch('login', 'admin').then(res => {})
```

### 模块化

> Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割

上方代码分割成模块

```javascript
// store/index.js
import Vue from 'vue'
import Vuex from 'vuex'
import user from './user'

Vue.use(Vuex)

export default new Vuex.Store({
  modules: {
    user
  },
  // strict: true  // 严格模式，防止用户手动更改状态
  strict: process.env.NODE_ENV !== 'production'
  /* 但是尽量不要在生产环境下使用严格模式，严格模式会深度检测状态树来检测不合格的状态变更，在发布环境下关闭严格模式，以避免性能损失 */
})

```

```javascript
// user.js
export default {
    namespaced: true,	// 设置命名空间，可便于准确定位到状态值，防止各个模块状态值的污染
    state: { //存放状态
        nickname:'zhangsan',
        age:20,
        gender:'男'
    },
    mutations: {
        setAge(state, age) {
            state.age = age	// 修改状态
        }
    },
    actions: {
        login({ commit }, username) {
            // login默认接收第一个参数vuex实例，从中可以分离出{ commit, dispatch }可供使用
            return new Promise(() => {	// 模拟异步登录
                setTimeout((resolve) => {
                    commit(username)	// 得到数据后提交mutations
                    resolve(true)
                }, 1000)
            })
        }
    }
}
```

- 当加上命名空间后，数据获取的时候就可以携带上名称，名称和`modules`中定义的名字一样，

如：

```javascript
this.$store.state.user.nickname

this.$store.dispatch('user/login')
```



### vuex中的映射方法

> 此处演示的例子均为`namespace:true`的情况

1. 使用<a name="mapState">mapState</a>辅助函数

```javascript
import {mapState} from 'vuex'
export default {
  name: 'home',
  computed: {
      ...mapState('user', ['nickname','age','gender'])	// 可直接使用
  } 
}
```
2. <a name="mapActions">mapActions</a>

```javascript
import { mapActions } from 'vuex'
methods: {
    // ...mapActions('user', ['login']) //为了避免和状态值重名，也可以使用完整路径，如下
    ...mapActions(['user/login'])
}
// 在使用的时候
this['user/login']('admin').then(res => {})
```

3. <a name="mapGetters">mapGetters</a>

- 使用方法类似mapState

```javascript
import { mapGetters } from 'vuex'
computed: {
    ...mapGetters('user', ['welcome'])
}
```

4. <a name="mapMutations">mapMutations</a>

- 和mapActions类似

```javascript
import { mapMutations } from 'vuex'
methods: {
    ...mapMutations(['user/login'])
}

// 在使用的时候
this['user/login']('admin').then(res => {})
```

### 各属性介绍

#### getters

- getters属于vuex中的计算属性，通过getters进一步处理，允许传参，第一个参数就是state
```javascript
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
 
export default new Vuex.Store({
  state: { //存放状态
    nickname:'Simba',
    firstname:'张',
    lastname:'三丰',
    age:20,
    gender:'男',
    money:1000
  },
  getters:{
    realname(state){
      return state.firstname+state.lastname
    },
    money_us(state){
      return (state.money/7).toFixed(2)
    }
  },
  mutations: {},
  actions: {},
  modules: {}
})
```
- vue页面调用的时候
```javascript
computed: {
 valued (){
   return this.value/7
 },
 ...mapGetters(['name', 'age']) // 可直接使用
}
```

#### Mutations

- mutations需要通过commit来调用其里面的方法，它也可以传入参数，第一个参数是state，第二个参数是载荷（payLoad），也就是额外的参数
```javascript
mutations: { //类似于methods
  addAge(state,payLoad){
    state.age+=payLoad.number
  }
}
```
```html
<div class="home">
   <div><button @click="test">测试</button></div>
</div>
```
```javascript
methods:{
 test(){
  //  可通过前台操作触发执行mutations里面的方法
   this.$store.commit('addAge',{
     number:5
   })
 }
}
```

- mapMutations  : 跟mapActions一样
```javascript
methods:{
 ...mapMutations(['addAge'])
}
// 或者
methods: {
  addAge(payLoad){
    this.$store.commit('addAge',payLoad)
  }
}
```
#### actions

- action中属于一部操作，mutations属于同步操作
- action不要直接取操纵state，是通过操作mutations进而去改变state里面的值
- action中的方法默认的就是异步，并且返回promise
```javascript
actions: {
  getUserInfo(){
    return {
      nickname:'Simba',
      age:20
    }
  }
}
// 在actions中定义一个方法：getUserInfo，并且返回一个对象
```
```javascript
created(){
  var res = this.getUserInfo()
  console.log(res)
},
methods:{
  ...mapActions(['getUserInfo'])
}
```
```javascript
// mapActions(['getUserInfo']) 相当于以下代码
getUserInfo(){
  return this.$store.dispatch(‘getUserInfo’)
}
```
- 
```javascript
export default new Vuex.Store({
 state: { 
  nickname: '',
  age:0,
  gender: '',
  money:0
 },
 mutations: {
  setUerInfo(state,payLoad){
   state.nickname = payLoad.nickname
   state.age = payLoad.age
   state.gender = payLoad.gender
   state.money = payLoad.money
  }
},
actions: { //actions没有提供state当参数
 async getToken({commit}){
   var res = await axios.get('/token接口')
   commit('setToken',res)
 },
async getUserInfo(context){ 
//context可以理解为它是整个Store的对象.类似于this.$store，
他里面包含了state，getter，mutations，actions
  const res = await axios.get('/接口url')
  context.commit('setUerInfo',res) 
//相当于 this.$store.commit,第一个参数是方法名，第二个参数是要传入的数据
  context.dispatch('getToken') 
//actions也可以调用自己的其他方法
    },
  }
})
```

## 在非父子组件通信时，可以使用通常的bus或者使用vuex，但是实现的功能不是太复杂，而使用上面两个又有点繁琐。这是，observable就是一个很好的选择
- observable 官方定义是让一个对象可响应。Vue内部会用他来处理data函数返回的对象。
- 返回的对象可以直接用于渲染函数和计算属性内，并且在发生改变时触发响应的更新。可用于简单的场景

### 此处以一个例子进行描述
```javascript
// 可以在src根目录下创建 store文件夹 -> index.js文件，在index.js文件中写入一下内容
// 引入vue
import Vue from 'vue
// 创建state对象，使用observable让state对象可响应
export let state = Vue.observable({
  name: '张三',
  'age': 38
})
// 创建对应的方法
export let mutations = {
  changeName(name) {
    state.name = name
  },
  setAge(age) {
    state.age = age
  }
}
// 现在我们的可相应对象及其对应的操作方法创建成功，接下来直接在vue文件中引入使用即可
```
```vue
<template>
  <div>
    姓名：{{ name }}
    年龄：{{ age }}
    <button @click="changeName('李四')">改变姓名</button>
    <button @click="setAge(18)">改变年龄</button>
  </div>
</template>
import { state, mutations } from '@/store
export default {
  // 在计算属性中拿到值
  computed: {
    name() {
      return state.name
    },
    age() {
      return state.age
    }
  },
  // 调用mutations里面的方法，更新数据
  methods: {
    changeName: mutations.changeName,
    setAge: mutations.setAge
  }
}
```

## VUE+ElementUI中为el-select添加滚动加载事件
- 当一个下拉框的数据有很多时，如果我们直接把所有的数据渲染出来，肯定是不现实的，不仅加载慢还影响性能，所以需要对下拉框做滚动加载的处理
### 知识拓展  Vue.directive(id, [definition])，注册或获取全局指令，不作过多解释
- 添加滚动加载事件，就需要注册一个全局指令
```javascript
// 注册滚动条加载触发事件v-loadmore绑定
// 直接在 main.js 中引入即可
Vue.directive('loadmore', {
  bind (el, binding) {
    // 获取element-ui定义好的scroll盒子
    const SELECTWRAP_DOM = el.querySelector('.el-select-dropdown .el-select-dropdown__wrap')
    SELECTWRAP_DOM.addEventListener('scroll', function () {
      const CONDITION = this.scrollHeight - this.scrollTop <= this.clientHeight
      if (CONDITION) {
        binding.value()
      }
    })
  }
})
```
- 下面附上官方用法
```javascript
// 注册
Vue.directive('my-directive', {
  bind: function () {},
  inserted: function () {},
  update: function () {},
  componentUpdated: function () {},
  unbind: function () {}
})

// 注册 (指令函数)
Vue.directive('my-directive', function () {
  // 这里将会被 `bind` 和 `update` 调用
})

// getter，返回已注册的指令
var myDirective = Vue.directive('my-directive')
```
#### 参考<a href="https://cn.vuejs.org/v2/guide/custom-directive.html" target="_blank">自定义指令</a>

### 指令定义完了，具体怎么用呢
```vue
// 在el-select组件里面使用
<el-select
  v-model="incomValue"
  filterable  // 开启搜索
  clearable // 清空
  remote  // 开启远程搜索
  v-loadmore="incomLoadmore"  // 触底滚动加载事件
  :loading="loading"
  :remote-method="remoteMethod" // 远程搜索
  @change="incomChange" // 值改变事件
  @clear="incomClear" // 清空事件
>
  <el-option
    v-for="item in incomList"
    :key="item.fdictionaryDetailedID"
    :value="item.fname"
    :label="item.fname"
  />
</el-select>
// 在对应的事件中执行对应的方法，每次触底新加载一页
// 下拉框滚动加载完成
```

## vue中的插槽--slot

## 配置Sass
```
npm i sass --save-dev
npm i sass-loader@7.3.1 --save-dev
// 一般只需要上面两个即可
npm i style-loader --save-dev
npm i stylus-loader --save-dev

npm i node-sass --save-dev
npm i css-loader --save-dev
```

## vue开发中怎么区分依赖的归属

- vue开发过程中，哪些依赖可以划分为开发依赖呢？

1. **构建工具**

比如webpack、webpack-cli、rollup等等。构建工具是为了生成生产环境的代码，在线上使用的代码也是他们工作的结果，也就是说在线上时，并不需要他们，因此他们可以归为开发依赖。

当然他们衍生出来的插件，如xxx-webpack-plugin，也属于开发依赖。

2. **预处理器**

指的是对源代码进行一定的处理并生成最终代码的工具，常见的有css的less、scss、sass、stylus，js中的typescript、coffee-script、babel等等。

以babel为例，常见的有两种方式。其一是内嵌在webpack或者rollup构建工具中，一般以loader或者plugin的形式出现，例如babel-loader。其二是单独使用（小项目较多），例如babel-cli。babel还额外有自己的插件体系，例如xxx-babel-plugin。类似的，less也有与之对应的less-loader和lessc。这些都算作开发依赖。

在babel中还有一个注意点，那就是babel-runtime是dependencies而不是devDependencies。

3. **测试工具**

当然在线上时是用不到测试工具的，因此他们归入开发依赖，常见如chai、e2e等等。

4. **其他**

最后一类很难概括，是开发时需要使用的，实际上显示要么是已经打包成最终代码了，要么是不需要了。比如webpack-dev-server支持开发热加载，线上是不用的；babel-register因为性能原因也不能用在线上。其他还可能和具体业务相关。



## 

## v-model和sync双向绑定

