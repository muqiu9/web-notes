# VUE使用笔记
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
  mutations: {},
  actions: {},
  modules: {}
})
```
1. state
- 可以直接在vue元素上添加
```html
<div class="home">{{ $store.state.nickname }}</div>
```
- 在computed中添加
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

2. 使用mapState辅助函数
- 需要vue中引入 mapState
```javascript
import {mapState} from 'vuex'
export default {
  name: 'home',
  computed: mapState(['nickname','age','gender']) // 可直接使用
}
```
```javascript
mapState(['nickname','age','gender'])
// 类似于
nickname(){return this.$store.state.nickname}
age(){return this.$store.state.age}
gender(){return this.$store.state.gender}
```
- 当vuex模块较多时，可以设置store属性namespace: true，然后在调用的时候表示出来自哪个模块
```javascript
mapState{
  nickname: state => state.app.nockname,
  age: state => state.app.age
}
```

3. getters
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

4. Mutation
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

- mapMutations  : 跟mapState和mapGetters一样
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
### action
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

## vue中的插槽--slot