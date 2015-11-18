#MongoDB ObjectId详解及使用
* ObjectId构成
* 从ObjectId获取时间
* 根据时间构造ObjectId
* 根据ObjectId按照插入时间排序

MongoDB中我们经常会接触到一个自动生成的字段：”_id”，类型为ObjectId。本文会详解ObjectId的构成和使用。
***
###ObjectId构成
之前我们使用MySQL等关系型数据库时，主键都是设置成自增的。但在分布式环境下，这种方法就不可行了，会产生冲突。为此，MongoDB采用了一个称之为ObjectId的类型来做主键。ObjectId是一个12字节的 BSON类型字符串。按照字节顺序，依次代表：
*  ■4字节：UNIX时间戳
*  ■3字节：机器识别码
*  ■2字节：表示生成此_id的进程
*  ■3字节：由一个随机数开始的计数器生成的值

###ObjectId获取时间
 
从ObjectId的构造上来看，内部就嵌入了时间类型。我们肯定可以从中获取时间信息：即插入此文档时的时间。MongoDB对ObjectId对象提供了getTimestamp()方法来获取ObjectId的时间。

```
> a = new ObjectId()
ObjectId("53102b43bf1044ed8b0ba36b")
> a.getTimestamp()
ISODate("2014-02-28T06:22:59Z")
```

###根据时间构造ObjectId
 
上例是直接使用MongoDB提供的新建方法来构造ObjectId的，我们自己可不可以通过字符串来构造呢？看下例：

```
// 使用Date的字符串构造方法生成日期
// 然后使用Date对象的getTime获取毫秒数，再除以1000得到标准时间戳
> a = new Date("2012-12-12 00:00:00").getTime()/1000
1355241600
// 获取时间戳的标准十六进制表示 
> a = a.toString(16) 
50c75880

// 在后面填补16个0 
> a = a + new Array(17).join("0") 
50c758800000000000000000

// 使用24个字符串构造ObjectId 
> b = new ObjectId(a) 
ObjectId("50c758800000000000000000")

// 获取时间以验证 
> b.getTimestamp() 
ISODate("2012-12-11T16:00:00Z")
```

上述过程中 new Array(17).join(“0″)目的是生成16个0拼接的字符串。
 
这里使用了点小技巧。new Array(17）构造了一个17个元素的数组，但是数组里面没有元素，join(atr)方法的作用是连接数组元素并且以其参数分割。17个元素正好有16个间隔，所以最终拼接起来的字符串为16个。
 
###根据ObjectId按照插入时间排序
 
MongoDB默认在ObjectId上建立索引，是按照插入时间排序的。我们可以使用此索引进行查询和排序。
```
// 按序插入三个文档
> db.col.insert({"num":1})
> db.col.insert({"num":2})
> db.col.insert({"num":3})
> db.col.find().pretty()
{ "_id" : ObjectId("53102fb4bf1044ed8b0ba36c"), "num" : 1 }
{ "_id" : ObjectId("53102fb9bf1044ed8b0ba36d"), "num" : 2 }
{ "_id" : ObjectId("53102fbabf1044ed8b0ba36e"), "num" : 3 }

// 按照_id升序，即按照插入时间升序
> db.col.find().sort({"_id":1}).pretty()
{ "_id" : ObjectId("53102fb4bf1044ed8b0ba36c"), "num" : 1 }
{ "_id" : ObjectId("53102fb9bf1044ed8b0ba36d"), "num" : 2 }
{ "_id" : ObjectId("53102fbabf1044ed8b0ba36e"), "num" : 3 }

// 按照_id降序，即按照插入时间降序
> db.col.find().sort({"_id":-1}).pretty()
{ "_id" : ObjectId("53102fbabf1044ed8b0ba36e"), "num" : 3 }
{ "_id" : ObjectId("53102fb9bf1044ed8b0ba36d"), "num" : 2 }
{ "_id" : ObjectId("53102fb4bf1044ed8b0ba36c"), "num" : 1 }

// 抽取num = 2的ObjectId用来过滤
> num2 = ObjectId("53102fb9bf1044ed8b0ba36d")
ObjectId("53102fb9bf1044ed8b0ba36d")

// 找出插入时间在num2之后的数据
> db.col.find({ "_id":{$gt:num2}}).pretty()
{ "_id" : ObjectId("53102fbabf1044ed8b0ba36e"), "num" : 3 }
```

原文地址:http://www.williamsang.com/archives/2261.html?utm_source=tuicool&utm_medium=referral
