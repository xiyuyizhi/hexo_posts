---
title: mongodb笔记
date: 2016-04-06 17:26:04
tags: mongodb  
---

[mongoDB nodejs Driver API](http://mongodb.github.io/node-mongodb-native/2.2/quick-start/)
[mongodb客户端  robomongo](https://robomongo.org/)

**ubuntu下**：
默认启动程序 /usr/lib/mongod
配置文件  /etc/mongodb.conf 修改端口，开启身份认证等
dbpath  /var/lib/mongodb
dblog   /var/log/mongodb/mongodb.log
       
解决Mongod error | exception in initAndListen std::exception: 
locale::facet::_S_create_c_locale name not valid

export LC_ALL=C 

- [启动mongod](#启动mongod)
- [身份认证](#身份认证)
- [数据备份](#数据备份)
- [数据类型](#数据类型)
- [node操作mongodb](#node操作mongodb)
    - [修改](#修改)
    - [查询](#查询)
- [游标](#游标)
- [聚合](#聚合)

## 启动mongod
每个mongodb实例要有自己的数据存储目录，mongod启动时，
会在数据目录中创建mongod.lock文件，防止其他mongod进程使用此目录
mongod --dbpath // 默认数据目录 /data/db
mongod --port 
mongod --fork //以守护进程的方式运行mongodb,创建服务端进程
mongod --logpath  --logappend //日志输出路径
mongod --bind_ip //绑定的ip
mongod --config
     
## 身份认证
首先在非认证前提下，在数据库中创建用户
use mydb
db.addUser('admin','admin);
//开启认证
mongod --port=3308 --auth 

mongo访问开启认证的数据库     
mongo localhost:3307/mydb -u admin -p admin     
     

## 数据备份
mongodump 运行时备份的方法，产生的备份不一定是服务器数据的实时快照

```
    mongodump -d yf_blogs -o C:\data\backup 
     mongodump -h 192.168.20.81:3307 -d yf_blog -o C:\data\backup
```
     
mongonrestore  将mongodump备份的数据恢复到指定数据库中  

```
mongonrestore -h 192.168.20.81:3307 -d yf_blogs123 --drop C:\data\backup\yf_blogs
```
     


## 数据类型

1. null
2. boolean
3. 数值 shell默认使用64位浮点型数值{'x':3.14}
4. 字符串
5. 日期{‘x’:new Date()}
6. 正则 {‘x’:/foobar/i/}
7. 数组 {‘x’:[]}
8. 内嵌文档 {‘x’:[]}
9. 对象ID 是一个12字节的ID，是文档的唯一标示


## node操作mongodb

- #### 修改

    1. 修改器

    *$inc*  用于数字 增加减少整数、长整数、双精度浮点数
     
     ```
      db.user.find()
      // { "_id" : ObjectId("578c3efee9bbab4f5d008fb0"), "name" : "ww", "tel" :         
          "15727", "age" : 23, "fav" : "js" }
        db.user.update({"name":"ww"},{"$inc":{"age":1}});
     ```


    *$set $unset* 增加、修改、删除键

    ```
        db.user.update({"name":"ww"},{"$set":{"fav":"tttttt"}})

        db.user.update({"name":"ww"},{"unset":{"fav":1}})
    ```

    **upset**

    ```
        db.blog.update({},{},true) //不存在则创建，存在则更新,第三个参数为true
        db.attachment.update({'attId':'123'},{'$set':{'attName':'123.png'}},true)
    ```

    2. 数组修改器

    *$push $ne $addToSet*
      
      ```
        db.blog.update({'title':'test'},{'$push':{'comments':{'author':"","content":""}}})
	   $push会重复添加
	   $ne $addToSet 只会添加数组中没有的新元素
	   
	   $addToSet和$each组合使用，可以添加多个不同的值
	   db.test.update({'name':'ww'},{'$addToSet':{'email':{'$each':['155@qq.com','155@163.com']}}})
      ```
	  *删除*
      ```
        $pop  从数组任一端删除元素 {'$pop':{key:1}}//从尾删除 key:-1 //从头删除
        $pull  删除指定元素 **会将匹配的元素全都删掉**
      ```

      3. 操作文档下的数组中的指定item项的内容

      ```
        db_blog.updateOne({
            '_id':DB.generatorObjectId(params.id),
            'comments._id':DB.generatorObjectId(params.commentId)
        },{
            "$push":{"comments.$.apply":data}
        },function(err,doc){
            db.close();
            console.log(doc)
            cb(err,doc)
        })
      ```


- #### 查询

    指定返回的键
    
    ```
         通过find的第二个参数控制
        db.user.find({},{'username':1,'password':1,'age':0})
        0 1 控制
    ```

    查询条件 $lt  $lte $gt $gte  $ne不相等

    ```
        db.users.find({'age':{
          '$gte':18,
          '$lte'；30
       }})
      //查询18<= age <=30的user
    ```

    OR查询($in $nin  $or)

    ```
        $in 用来查询 *一个键* 的多个值
        $or 用来完成 *多个键值* 的任意给定值
    
        db.user.find({'age':{$in:[18,19,20]}});
         //查询age为18，19或20的
        db.user.find({'$or':[{"name":"luyun"},{"age":20}]});
        //查询name为luyun的或age为20的
    ```

    $not 是元条件句，可以用在任何其他条件上

    ```
        db.user.find( {"id_num":{"$mod":[5,1]}} )
        db.user.find( {"id_num":{ "$not":{ "$mod":[5,1] } } } )
    ```


    $exists  //键值是否存在

    ```
        db.user.find({"age":{"$exists":true}})
    ```


    查询数组

    ```
    $all 通过多个元素匹配数组
    db.food.find({'fruit':{'$all':['apple','banana']}})

    $size
    $slice //指定返回数组的条数
    db.blog,posts.findOne({'title':'123'},{'comments':{'$slice':10/-10} })
    //查询内嵌数组从第n个开始，查询m个
     db_blog.find(
                {
                    '_id': DB.generatorObjectId(params.id)
                },
                {
                    comments: {$slice: [n, m]},
                }).toArray(function(){})
    ```




##  游标

游标对象的方法

     limit
     skip
     sort({"username":1,"age":-1})//按username升序，age降序排序
     
     

## 聚合
 
  ```
    *count*
    db.users.count() //查询数量
    
    *distinct*
    
    *group*
    db.runCommand({"group":{
      'ns':'posts',//要进行分组的集合
      'key':'day',  //指定文档分组依据的键
      'initial':{'time':0}, //每一组reduce调用的初始时间
                           //每一组的所有成员都会使用这个累加器，所以改变会保留住
      '$reduce':function(doc,prev){
              //当前文档，累加器文档（每一组都有一个独立的累加器）
      },
      'condition':{'day':{'$get':'2016/08/08'}}
      'finalize':function(){} //作用于result中的每一个item,对结果进行修剪
      '$keyf':function(x){ //将函数作为键使用
        return x.category.toLowerCase()
      }
    }})
    
    *MapReduce*
  ```