# 在vue中使用Element的message组件
1. 在vue文件中使用  
```javascript
this.$message({
  message: "提示信息",
  type: "success"
})
```
2. 在js文件中使用
```javascript
ElementUI.Message({
  message: '提示信息',
  type: 'warning'
});
```