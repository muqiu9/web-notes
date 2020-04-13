# VUE使用笔记
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