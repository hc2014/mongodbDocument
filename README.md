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
>**use mydb**这个是切换或者创建数据库(有则切换没有就创建)
>**db.createCollections**创建集合
>**show collections**显示集合
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
>**insert**插入数据
>**find**是查找数据
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
