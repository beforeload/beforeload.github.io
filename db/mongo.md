# MongoDB

### 常用命令

1. help
2. show dbs
3. use db_name
4. show collections
5. db.collection_name.find(query)
6. db.collection_name.findOne(query)
7. db.collection_name.insert(document)
8. db.collection_name.save(document)
9. db.collection_name.update(query, {$set: data})
10. db.collection_name.remove(query)
11. printjson(document)

### Node.js 原生驱动

1. 声明依赖关系 ``var mongo = require('mongodb'); var Db = mongo.Db;``
2. 定义主机和端口 ``var db = new Db('local', new Server(dbHost, dbPort), {safe: true})``
3. 建立连接 ``db.open(function(err, dbCollection) {...db.close() })``
4. 创建文档 ``dbCollection.collection('name').insert(...)``
5. 输出

```


```
