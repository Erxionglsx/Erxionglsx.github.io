# Mongdb



启动服务端：

![](https://note.youdao.com/yws/api/personal/file/C93900F540E54C49B73498A25947A385?method=download&shareKey=9d686d7c63aa553040ff7752ccbc8ce2)

连接客户端：

![](https://note.youdao.com/yws/api/personal/file/9EFBC167F45A4C9C8EC041F6673D394C?method=download&shareKey=3449e70375a109436289f76b925fbf32)

![](https://note.youdao.com/yws/api/personal/file/720D75AA13A144ADB3E207882D9BC2B9?method=download&shareKey=6e6efc48389a4b926f411be78ac57e35)

![](https://note.youdao.com/yws/api/personal/file/B73C9ED5F0484E9A833CA213F16EB47A?method=download&shareKey=eab0d07b1eb8f877e3ff29c959b56686)

Create database 创建库

Create Collection 创建表

```js
db.address.find({'name':'重庆'});//查询

db.address.insert([{'name':'山西','street':'北京路'}]);//插入一条

db.address.insert([{'name':'成都','street':'天府大道','number':120}]);

db.address.insert([//插入多条
    {'name':'上海'},
    {'name':'上海','street':'龙岗路'},
    {'name':[
        {'id':'1233'},
        {'id':'1223','street':'北京路'}
        ]}
    ]);
    
    db.address.deleteOne({'name':'山西'});//删除一条
    
    db.address.deleteMany({'name':'山西'});//删除多条
    
    db.address.update({'name':'成都'},{$set:{'name':'重庆'}},{multi:false});//修改一条
    
   db.address.update({'name':'成都'},{$set:{'name':'重庆'}},{multi:true});//修改多条

   db.address.find({$or:[{name:'重庆'},{'name':'上海'}]});//或查询
     
   db.address.find({$and:[{name:'上海'},{street:'龙岗路'}]});//与查询
```

创建连接：

```java
public class TestMongo {
    public static MongoCollection getMongoCollection(){
        MongoClientURI uri;
        //创建客户端
        MongoClient mongoClient = new MongoClient("localhost",27107);
        //获取数据库
        MongoDatabase database = mongoClient.getDatabase("study");
        //返回指定集合
        MongoCollection<Document> collection = database.getCollection("address");

        return collection;
    }
    public static void main(String[] args){
        MongoCollection collection = getMongoCollection();
        System.out.println(collection);
    }
}
```

CRUD

```java
public class TestMongo {
    public static DBCollection getMongoCollection(){
        //创建客户端
        Mongo mongo = new Mongo("localhost",27017);
        //获取数据库
        DB database = mongo.getDB("study");
        //返回指定集合
        DBCollection collection = database.getCollection("address");
        return  collection;
    }
    /**
     *添加数据
     */
    @Test
    public void testAdd(){
        DBCollection collection =getMongoCollection();
        BasicDBObject object = new BasicDBObject();
        object.append("name","成都112");
        object.append("street","北京路");
        //插入集合
        collection.insert(object);
        DBCursor dbCursor = collection.find();
        //游标遍历
        for (DBObject object1:dbCursor){
            System.out.println(object1.toString());
        }
    }
    /**
     * 删除数据
     */
    @Test
    public void testDelete(){
        DBCollection collection = getMongoCollection();
        BasicDBObject object = new BasicDBObject();
        object.append("name","成都112");
        collection.remove(object);
        DBCursor dbObjects = collection.find();
        for (DBObject dbObject: dbObjects){
            System.out.println(dbObject.toString());
        }
    }
    /**
     * 更新数据
     */
    @Test
    public void testUpdate(){
        DBCollection collection = getMongoCollection();
        BasicDBObject qobject = new BasicDBObject();
        qobject.append("street","天府大道");
        BasicDBObject uobject = new BasicDBObject();
        uobject.append("street","北京中关村");
        collection.update(qobject,uobject);
        DBCursor dbObjects = collection.find();
        for (DBObject dbObject: dbObjects){
            System.out.println(dbObject.toString());
        }
    }
    /**
     * 查询数据
     */
    @Test
    public void testSelect(){
        DBCollection collection = getMongoCollection();
        BasicDBObject object = new BasicDBObject();
        object.append("name","上海");
        DBCursor dbObjects = collection.find(object);
        for (DBObject object1:dbObjects){
            System.out.println(object1.toString());
        }
    }

    public static void main(String[] args){
        DBCollection collection =  getMongoCollection();
        System.out.println(collection);
    }
}
```





















