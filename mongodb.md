## 基本命令

- show dbs：显示已有数据库
- use admin：进入admin数据库
- use collections：显示数据库中的集合
  - 当use的数据库不存在的时候，就会建立一个数据库
- db：显示当前位置
- db.集合.insert()：新建数据集合和插入文件，如果集合没有的话就会新建一个集合，如：`db.test.insert({"name": "zhangsan"})`，新建了一个集合test并向test集合中插入数据`{name: 'zhangsan'}`，批量插入使用数组即可
- db.集合.find()：查询集合中的所有数据，如：`db.test.find()`，可传查询条件，如`db.test.find({name: 'zhangsan'})`
- db.集合.findOne()：查询第一个文件数据
- db.集合.update(查询条件, 修改内容)：`db.test.update({name: 'zhangsan'}, {name: 'zhangsan', age: 18})`
- db.集合.remove(条件)：删除数据文件
- db.集合.drop()：删除整个文件
- db.dropDatabase()：删除整个数据库
- db.randomInfo.stats()：可以查看randomInfo库的信息

> update修改器
>
> - 第二项
>   - $set：设置
>   - $unset：删除
>   - $inc：计算，可在原数据基础上修改，（正值或负值）
>   - $push：向数组中追加一个值
>   - $ne：检查要添加的值是否存在，已经存在的话就不添加，不存在的时候九添加
>   - $addToSet：效果同$ne，只是写的位置不同
>   - $each：向数组中批量追加
>   - $pop：删除数组中的值，两个值 1从尾部删除，-1 从头部删除
> - 第三项
>   - multi：默认为false
>     - true：修改所有数据
>     - false：只修改第一条数据
> - 第四项
>   - upsert：默认为false
>     - true：按条件查询时找不到的话会自动添加一条
>     - false：按条件查询找不到的话不执行其他操作
>
> db.test.update({}, {}, {multi: true, upsert: true})

## 修改状态返回与安全

+ db.listCommands()  查询所有Command命令

> db.runCommand()
>
> - getLastError:1 :表示返回功能错误，这里的参数很多，如果有兴趣请自行查找学习，这里不作过多介绍。
>
> ```json
> // 执行结果
> {
>         "connectionId" : 2,      
>         "updatedExisting" : true,
>         "n" : 2,
>         "syncMillis" : 0,        
>         "writtenTo" : null,      
>         "writeConcern" : {       
>                 "w" : 1,
>                 "wtimeout" : 0   
>         },
>         "err" : null,
>         "ok" : 1
> }
> ```
>
> 

- findAndModify - 查找并修改

> ```js
> var myModify = {
>   findAndModify: 'person', // 查找的库
>   query: {name: 'ruirui'}, // 查询条件
>   update: {$set: {age: 99}}, // 更新内容
>   new: true // 为true就会返回执行结果
> }
> 
> var ResultMessage = db.runCommand(myModify) // 执行
> printjson(ResultMessage) // 打印
> ```
>
> findAndModify属性值如下
>
> - query：需要查询的条件/文档
> - sort: 进行排序
> - remove：[boolean]是否删除查找到的文档，值填写true，可以删除。
> - new:[boolean]返回更新前的文档还是更新后的文档。
> - fields：需要返回的字段
> - upsert：没有这个值是否增加。

- 不等修饰符

> - 小于($lt):英文全称less-than
> - 小于等于($lte)：英文全称less-than-equal
> - 大于($gt):英文全称greater-than
> - 大于等于($gte):英文全称greater-than-equal
> - 不等于($ne):英文全称not-equal 我们现在要查找一下，公司内年龄小于30大于25岁的人员。看下面的代码
> - 等于($eq):英文全称equal

- 多条件修饰符

> - $in：可以轻松解决一键多值的查询情况
>
> ``````js
> // 包含关系查询
> db.workmate.find({age: {$in: [31, 33]}}, {name: true, age: true, _id: false})
> ``````
>
> - $and：用来查找几个key值都满足的情况
>
> ```js
> // 与查询
> db.workmate.find({$and: [
>     {age: {$lt: 25}},
>     {name: 'DingLu'}
>   ]},
>   {name: true, age: true, _id: false}
> )
> ```
>
> - $or：它用来查询多个键值的情况
>
> ```js
> db.workmate.find({$or: [
>   {age: {$lt: 25}},
>   {name: 'DingLu'}
> ]},
> {name: true, age: true, _id: false}
> )
> ```
>
> - $not：用来查询除条件之外的值
>
> ```js
> db.workmate.find({
>   age: {
>     $not: {
>       $lte: 30,
>       $gte: 20
>     }
>   }
> },
> {name: true, age: true, _id: false}
> )
> ```
>

- find的数组查询

> - $all - 数组多项查询，需要满足所有条件
>   - 表示即喜欢看电影又喜欢看书
>
> ```javascript
> db.workmate.find(
>     {interest:{$all:["看电影","看书"]}},
>     {name:1,interest:1,age:1,_id:0} 
> )
> ```
>
> - $in - 数组或者查询，只要满足一个条件即可
>   - 查询爱好看电影或者爱好看书的人员
>
> ```javascript
> db.workmate.find(
>     {interest:{$in:["看电影","看书"]}},
>     {name:1,interest:1,age:1,_id:0} 
> )
> ```
>
> - $size - 数组个数查询
>   - 查询爱好为5项的人
>
> ```javascript
> db.workmate.find(
>     {interest:{$size:5}},
>     {name:1,interest:1,age:1,_id:0} 
> )
> ```
>
> - $slice - 显示选项
>   - 数组显示的个数，为正数时显示前面的，为负数时显示后面的值
>
> ```javascript
> // 只显示前两项
> db.workmate.find(
>     {},
>     {name:1,interest:{$slice:2},age:1,_id:0} 
> )
> // 显示组后一项
> db.workmate.find(
>     {},
>     {name:1,interest:{$slice:-1},age:1,_id:0} 
> )
> ```

## find的参数使用方法

```
query：这个就是查询条件，MongoDB默认的第一个参数。
fields：（返回内容）查询出来后显示的结果样式，可以用true和false控制是否显示。
limit：返回的数量，后边跟数字，控制每次查询返回的结果数量。
skip:跳过多少个显示，和limit结合可以实现分页。
sort：排序方式，从小到大排序使用1，从大到小排序使用-1。
```

```javascript
// 我们把同事集合（collections）进行分页，每页显示两个，并且按照年龄从小到大的顺序排列
dbd .workmate.find({},{name:true,age:true,_id:false}).limit(0).skip(2).sort({age:1});
```

## find如何在js文本中使用

> 直接load文件即可

- hasNext循环结果

```javascript
var db = connect("company")  //进行链接对应的集合collections
var result = db.workmate.find() //声明变量result，并把查询结果赋值给result
//利用游标的hasNext()进行循环输出结果。
while(result.hasNext()){
    printjson(result.next())  //用json格式打印结果
}
```

- forEach循环结果

```javascript
var db = connect("company")  //进行链接对应的集合collections
var result = db.workmate.find() //声明变量result，并把查询结果赋值给result
//利用游标的hasNext()进行循环输出结果。
result.forEach(function(result){
    printjson(result)
})
```

## 执行js文件

> 1、可以使用`mongo文件名`执行对应文件
>
> 2、使用mongo命令进入mongodb终端，使用load(文件名)执行js文件
>

## 索引

> 建立索引目的是为了提高查询性能，但是索引这东西是要消耗硬盘和内存资源的，所以还是要根据实际需要进行建立了。

```javascript
// 建立索引
db.randomInfo.ensureIndex({username:1})
// 查看现有索引
db.randomInfo.getIndexes()
```

- 索引数量不会超过64个

### 复合索引

```javascript
// 两个索引同时查询，MongoDB的复合查询是按照我们的索引顺序进行查询的，就是我们用 db.randomInfo.getIndexes()查询出来的数组
db.randomInfo.find({username:'7xwb8y3',randNum0:565509})
// 自己指定索引优先级，这个方法就是hint()
var  rs= db.randomInfo.find({username:'7xwb8y3',randNum0:565509}).hint({randNum0:1});
// 删除索引
db.randomInfo.dropIndex('randNum0_1');//索引的唯一ID
```

### 全文检索

```javascript
// 建立全文索引
db.info.ensureIndex({contextInfo:'text'})
```

- $text：表示要在全文索引中查东西
- $search：后面跟查找的内容

```javascript
// 我们希望查找数据中有programmer，family，diary，drink的数据（这是或的关系）
db.info.find({$text:{$search:"programmer family diary drink"}})
// 我们希望不查找出来有drink这个单词的记录，我们可以使用“-”减号来取消。
dbd .info.find({$text:{$search:"programmer family diary -drink"}})
```

<b>转义符</b>

> 全文搜索中是支持转义符的，比如我们想搜索的是两个词（love PlayGame和drink），这时候需要使用\斜杠来转意

```javascript
db.info.find({$text:{$search:"\"love PlayGame\" drink"}})
```

## 用户的创建、删除与修改

- 创建用户可以用db.createUser方法来完成

```javascript
db.createUser({
    user:"luckiest",
    pwd:"123456",
    customData:{
        name:'小二郎',
        email:'1747320175@qq.com',
        age:18,
    },
    roles:['read']
})
```

- 还可以单独配置一个数据库的权限，比如我们现在要配置compay数据库的权限为读写：

```javascript
db.createUser({
    user:"luckiest",
    pwd:"123456",
    customData:{
        name:'小二郎',
        email:'1747320175@qq.com',
        age:18,
    },
     roles:[
        {
            role:"readWrite",
            db:"company"
        },
        'read'
    ]
})
```

### 内置角色

内置角色：

1. 数据库用户角色：read、readWrite；
2. 数据库管理角色：dbAdmin、dbOwner、userAdmin;
3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManage；
4. 备份恢复角色：backup、restore；
5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
6. 超级用户角色：root
7. 内部角色：__system

```javascript
// 查找用户信息， 我们直接可以使用查找的方法，查找用户信息
db.system.users.find()
// 删除用户，直接用remove就可以删除这个用户的信息和权限。
db.system.users.remove({user:"luckiest"})
// 建权：有时候我们要验证用户的用户名密码是否正确，就需要用到MongoDB提供的健全操作。也算是一种登录操作，不过MongoDB把这叫做建权。
db.auth("luckiest","123456") // 如果正确返回1，如果错误返回0。（Error：Authentication failed。）
// 启动建权，重启MongoDB服务器，然后设置必须使用建权登录。
mongod --auth // 启动后，用户登录只能用用户名和密码进行登录，原来的mongo形式链接已经不起作
```

