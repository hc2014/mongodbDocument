###项目启动
安装好mongoDB后，打开命名终端进入mongodb的安装目录下的Bin文件夹</br>
**cd C:\Program Files\MongoDB 2.6 Standard\bin**</br>
然后用**-dbpath**指定数据库文件路径**</br>
**C:\Program Files\MongoDB 2.6 Standard\bin>mongod -dbpath C:\Users\hc\Desktop\nod
e\db**
这样就启动完成了(以上是本地链接的操作！远程链接我自己还没有试过！)

###基础命令
打开bin文件夹下的mongo.exe程序
**show dbs**是查看当前的数据命令
```
> show dbs
admin  (empty)
local  0.078GB
```
**use mydb**这个是切换或者创建数据库(有则切换没有就创建)</br>
**db.createCollections**创建集合</br>
**show collections**显示集合
```
> use mydb
switched to db mydb
> db.createCollection("mytb",{size:20,capped:5,max:100})
{ "ok" : 1 }
> show collection
2015-11-18T10:20:19.314+0800 don't know how to show [collection] at src/mongo/sh
ell/utils.js:729
> show collections
mytb
system.indexes
```
**insert**插入数据</br>
**find**是查找数据
```
> db.mytb.insert({id:1,age:18,name:"张三"})))
WriteResult({ "nInserted" : 1 })
> db.mytb.insert({id:1,age:18,name:"张三"})
WriteResult({ "nInserted" : 1 })
> db.mytb.find()
{ "_id" : ObjectId("564be65f0eb28040d8c48aea"), "id" : 1, "age" : 18, "name" : "
张三" }
{ "_id" : ObjectId("564be66f0eb28040d8c48aeb"), "id" : 1, "age" : 18, "name" : "
张三" }
```
**条件查询:find({id:2})相当于t_sql里面的where id=2**</br>
```
> db.mytb.insert({id:2,age:18,name:"张三"})
WriteResult({ "nInserted" : 1 })
> db.mytb.find()
{ "_id" : ObjectId("564be65f0eb28040d8c48aea"), "id" : 1, "age" : 18, "name" : "
张三" }
{ "_id" : ObjectId("564be66f0eb28040d8c48aeb"), "id" : 1, "age" : 18, "name" : "
张三" }
{ "_id" : ObjectId("564be8f90eb28040d8c48aec"), "id" : 2, "age" : 18, "name" : "
张三" }
> db.mytb.find({id:2})
{ "_id" : ObjectId("564be8f90eb28040d8c48aec"), "id" : 2, "age" : 18, "name" : "
张三" }
```
**查找指定的列find({},{id:1,name:1})**
```
> db.mytb.find({},{id:1,name:1})
{ "_id" : ObjectId("564be65f0eb28040d8c48aea"), "id" : 1, "name" : "张三" }
{ "_id" : ObjectId("564be66f0eb28040d8c48aeb"), "id" : 1, "name" : "张三" }
{ "_id" : ObjectId("564be8f90eb28040d8c48aec"), "id" : 2, "name" : "aaa" }
```
当然name也可以用true或false,当用ture的情况下河name:1效果一样，如果用false就是排除name，显示name以外的列信息。

**这里的_id是系统自动生成的，类型是objectid**更多详情可以参考objectid.md</br>
**update修改数据**</br>
```
> db.mytb.update({id:2},{$set:{name:"aaa"}},false,true)
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.mytb.find({id:2})
{ "_id" : ObjectId("564be8f90eb28040d8c48aec"), "id" : 2, "age" : 18, "name" : "
aaa" }
```

**排序sort**</br>
按照id倒序
```
> db.mytb.find().sort({id:-1})
{ "_id" : ObjectId("564be8f90eb28040d8c48aec"), "id" : 2, "age" : 18, "name" : "
aaa" }
{ "_id" : ObjectId("564be65f0eb28040d8c48aea"), "id" : 1, "age" : 18, "name" : "
张三" }
{ "_id" : ObjectId("564be66f0eb28040d8c48aeb"), "id" : 1, "age" : 18, "name" : "
张三" }
```
**查询前2条数据**</br>
```
> db.mytb.find().limit(2)
{ "_id" : ObjectId("564be65f0eb28040d8c48aea"), "id" : 1, "age" : 18, "name" : "
张三" }
{ "_id" : ObjectId("564be66f0eb28040d8c48aeb"), "id" : 1, "age" : 18, "name" : "
张三" }
```
**查询第2条以后的数据**</br>
```
> db.mytb.find()
{ "_id" : ObjectId("564be65f0eb28040d8c48aea"), "id" : 1, "age" : 18, "name" : "
张三" }
{ "_id" : ObjectId("564be66f0eb28040d8c48aeb"), "id" : 1, "age" : 18, "name" : "
张三" }
{ "_id" : ObjectId("564be8f90eb28040d8c48aec"), "id" : 2, "age" : 18, "name" : "
aaa" }
{ "_id" : ObjectId("564c08180eb28040d8c48aee"), "id" : 3, "age" : 30, "name" : "
bb" }
{ "_id" : ObjectId("564c08210eb28040d8c48aef"), "id" : 4, "age" : 30, "name" : "
cc" }
----------------------------------
> db.mytb.find().skip(2)
{ "_id" : ObjectId("564be8f90eb28040d8c48aec"), "id" : 2, "age" : 18, "name" : "
aaa" }
{ "_id" : ObjectId("564c08180eb28040d8c48aee"), "id" : 3, "age" : 30, "name" : "
bb" }
{ "_id" : ObjectId("564c08210eb28040d8c48aef"), "id" : 4, "age" : 30, "name" : "
```

**查询2-4条数据**</br>
```
> db.mytb.find().skip(1).limit(4)
{ "_id" : ObjectId("564be66f0eb28040d8c48aeb"), "id" : 1, "age" : 18, "name" : "
张三" }
{ "_id" : ObjectId("564be8f90eb28040d8c48aec"), "id" : 2, "age" : 18, "name" : "
aaa" }
{ "_id" : ObjectId("564c08180eb28040d8c48aee"), "id" : 3, "age" : 30, "name" : "
bb" }
{ "_id" : ObjectId("564c08210eb28040d8c48aef"), "id" : 4, "age" : 30, "name" : "
cc" }
```

