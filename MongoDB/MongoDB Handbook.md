# MongoDB Handbook

# MONGO DEV

## **DATABASE**

如果数据库不存在，则在第一次为该数据库存储数据时，MONGODB 会创建该数据库； 在 MONGODB 中，文档集合存在数据库当中，当要选择使用的数据库时，如下方示例：

```
use testDB
db.testCollection.insertOne( { x: 1} )
```

## **COLLECTION 如果在 COLLECTION 不存在的情况下，插入一条数据和创建一个索引都会对应隐式创建各自的 COLLECTION；**

```
db.testCollection.insertOne( { x: 1 } )
db.testCollection.createIndex( { y: 1 } )
```

MONGO 提供了一种显式创建 COLLECTION 方法，同时能够设置最大的 COLLECTION 大小或者 DOCUMENT 的验证规则；

```
db.createCollection( <name>,
    {
        capped: <boolean>,
        autoIndexId: <boolean>,
        size: <number>,
        max: <number>, storageEngine: <document>,
        validator: <document>,
        validationLevel: <string>,
        validationAction: <string>,
        indexOptionDefaults: <document>,
        viewOn: <string>,
        pipeline: <pipeline>,
        collation: <document>,
        writeConcern: <document>
    }
)
```

## **CAPPED COLLECTION**

CAPPED COLLECTION 是固定大小的 COLLECTION，支持高吞吐的插入操作和根据插入顺出的查询操作，CAPPED COLLECTION 的工作方式与 CIRCULAR BUFFERS 类似：当一个 COLLECTION 填满了被分配的空间后，则覆盖最早的文档。你可以通过以下语句来创建一个 CAPPED COLLECTION 或者将一个 COLLECTION 转换为一个 CAPPED COLLECTION：

```
# 如果 size 字段小于 4096，则该集合将具有 4096 byte 的容量
# 如果 size 的字段不是 256 的倍数，则 MONGO 会提升容量直到满足 256 的倍数为止
db.createCollection( "log", { capped: true, size: 10000} )
# 将 COLLECTION 转换为 CAPPED COLLETCION
db.runCommand( {"convertToCapped": "testCollection", size: 10000} )
# 以及可以使用以下语句来检查 COLLECTION 是否 CAPPED
db.testCollection.isCapped()
```

## **VIEW**

MONGO 从 3.4 版本开始提供了对已存在的 COLLECTION 或者 VIEW 创建 VIEW 的支持；

```
​
db.createView("testView", "testCollection", [ ])
​
```

VIEW 是只读的，你不能对 VIEW 进行写操作，同时也不可以重命名 VIEW。如果需要删除 VIEW，则使用以下语句：

```
db.testView.drop()
```

## **DOCUMENT**

DOCUMENT 的字段名称必须是字符串，_id 字段保留用作主键，且 _id 字段始终为 DOCUMENT 中的第一个字段，同时所有字段名称不能包括 null 字符，以及顶级字段名称不能以 $ 开头； MONGO 中使用 . 访问数组的元素，同时如果需要访问嵌入的文档也是通过 . 实现；

## **BSON TYPE**

| **TYPE** | **NUMBER** | **NOTES** |
| --- | --- | --- |
| Double | 1 |  |
| String | 2 |  |
| Object | 3 |  |
| Array | 4 |  |
| Binary data | 5 |  |
| Undefined | 6 | 不推荐使用。 |
| ObjectId | 7 |  |
| Boolean | 8 |  |
| Date | 9 |  |
| Null | 10 |  |
| Regular Expression | 11 |  |
| DBPointer | 12 | 不推荐使用。 |
| JavaScript | 13 |  |
| Symbol | 14 | 不推荐使用。 |
| JavaScript | 15 |  |
| 32-bit integer | 16 |  |
| Timestamp | 17 |  |
| 64-bit integer | 18 |  |
| Decimal128 | 19 | 3.4 or later |
| Min key | -1 |  |
| Max key | 127 |  |

在 MONGO 中 ObjectId 的长度为 12 个 byte，其中 4 个 byte 为时间戳，5 个 byte 为随机值，3 个 byte 为递增计数器，初始化为随机值； BSON 的 String 格式通常为 UTF-8 格式，通常在对 BSON 进行序列化和反序列时，每种编程语言的驱动都会从该语言的字符串格式转换为 UTF-8；

## **CRUD**

### **INSERT**

MONGO 中所有的写操作都是单个 DOCUMENT 级别的原子操作； MONGO 插入一个 DOCUMENT，如果 DOCUMENT 中未指定 _id 字段，则 MONGO 将具有 ObjectId 值的 _id 字段添加到新 DOCUMENT 中。在插入 DOCUMENT 成功后则会返回其插入的 DOCUMENT；

```
db.testCollection.insertOne(
    { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm"} }
)
```

MONGO 可以同时插入多个 DOCUMENT，只需要将 DOCUMENT ARRAY 传递至方法中即可；

```
db.testCollection.insertMany([
    { item: "journal", qty: 25, tags: ["blank", "red"], size: { h: 14, w: 21, uom: "cm" } },
    { item: "mat", qty: 85, tags: ["gray"], size: { h: 27.9, w: 35.5, uom: "cm" } },
    { item: "mousepad", qty: 25, tags: ["gel", "blue"], size: { h: 19, w: 22.85, uom: "cm" } }
])
```

### **SELECT**

如果想要检索 COLLECTION 中的所有 DOCUMENT，可以在 find 方法中传一个空 DOCUMENT 作为查询过滤条件：

```
db.testCollection.find( {} )
```

在查询过滤 DOCUMENT 中可以使用 KEY: VALUE 表达式实现等值查询：

```
db.testCollection.find( {item: "canvas") }
```

在查询过滤 DOCUMENT 中可以使用查询操作符来指定多个条件：

```
db.testCollection.find( { item: { $in: ["canvas", "mat"] } } )
db.testCollection.find( { item: "mat", qty: { $lt: 90 } } )
db.testCollection.find( { $or: [ { item: "mat"}, { qty: { $lt: 90 } } ] } )
db.testCollection.find ( {
    item: "mat",
    $or: [ { qty: { $lt: 90 } }, { tags: { $in: ["gray"] } } ]
} )
```

设置查询结果只返回指定字段以及 _id 字段：

```
db.testCollection.find( { item: "mat" }, { item: 1, tags: 1 } )
```

我们可以通过在映射文档中将 _id 字段设置为 0 来从结果集中去除 _id 字段，如下：

```
db.testCollection.find( { item: "mat" }, { item: 1, tags: 1, _id: 0 } )
```

或者我们可以使用映射来排除特定字段，而不是在匹配文档中列出要返回的字段：

```
db.testCollection.find( { item: "mat" }, { tags: 0,_id: 0, "size.h": 0 } )
```

### **UPDATE**

MONGO 中更新单个 DOCUMENT 示例如下：

```
db.testCollection.updateOne(
    { item: "mat" },
    {
        $set: { qty: 60, "size.h": 33.3 }
    }
)
```

同时可以使用以下语句来对多个 DOCUMENT 进行更新：

```
db.testCollection.updateMany(
    { qty: 25},
    {
        $set: { qty: 30 }
    }
)
```

### **DELETE**

如果要删除某个 COLLECTION 中的所有 DOCUMENT，只需将空的 FILTER DOCUMENT 传递到 db.collection.deleteMany() 方法即可:

```
db.testCollection.deleteMany( {} )
```

删除所有符合条件的 DOCUMENT 如下：

```
db.testCollection.deleteMany( { qty: { $lt: 35 } } )
```

要删除最多一个与指定过滤器匹配的 DOCUMENT，应使用 db.collection.deleteOne()：

```
db.test.Collection.deleteOne( { qty: 30 } )
```

即使 COLLECTION 中所有的 DOCUMENT 都被删除，删除操作也不会删除 INDEX；

## **AGGREGATE**

MONGO 可以通过 AGGREGATE 来将 DOCUMENT 进入多阶段管道，将 DOCUMENT 转换为 AGGREGATE RESULT； 以下例子实现过滤 DOCUMENT 的 status 字段，并按照 cust_id 将 DOCUMENT 分组，以计算每个唯一值 cust_id 的金额总和；

```
db.orders.aggregate([
   { $match: { status: "A" } },
   { $group: { _id: "$cust_id", total: { $sum: "$amount" } } }
])
```

如果需要统计每个用户的注册时间，并按照时间进行排序，可参照以下列子：

```
db.users.aggregate([
    { $project :
        {
            month_joined : { $month : “$joined“ },
            name : “$_id“,
            _id : 0
        }
    },
    { $sort : { month_joined : 1 } }
])
```

## **INDEX**

在创建 COLLECTION 期间，MONGO 在 _id 字段上创建唯一 INDEX，该 INDEX 可防止客户端插入两个具有相同值的 DOCUMENT，你不能将 _id 字段上的 INDEX 删除； 如果需要在 MONGO 中创建 INDEX，请使用 db.collection.createIndex()，该方法只在不存在相同规范的 INDEX 时创建。同时可以指定 INDEX 的排序方式和 INDEX 的名称，默认 INDEX 名称为 INDEX KEY 加上 INDEX KEY 的排列顺序，中间使用 _ 链接；

```
db.testCollection.createIndex(
    { "item": 1 },
    { name: "test_index" },
)
```

### **COMPOSITE INDEX**

MONGO 支持 CPMPOSITE INDEX，其中单个 INDEX 结构持有对 COLLECTION 的 DOCUEMTN 中多个字段的引用。复合索引可以支持在多个字段上匹配的查询；

```
db.testCollection.createIndex( { "item": 1, "qty": 1 } )
```

INDEX 以升序或者降序排序顺序存储对字段的引用。对于单字段索引来说，这个无关紧要，因为 MONGO 可以在任一方向上遍历索引，但是对于 COMPOSITE INDEX 来说，属性的顺序决定了索引是否支持结果集的排序； 假设我需要执行以下语句，并根据字段决定排序顺序：

```
db.testCollection.find().sort( { "item": 1, "qty": -1 } )
// 或者
db.testCollection.find().sort( { "qty": 1, "item": -1 } )
```

以下的 INDEX 即可支持这两种排序操作：

```
db.testCollection.createIndex( { "item": 1, "qty": -1 } )
```

但是上面的 INDEX 不支持以下场景：

```
db.testCollection.find().sort( { "item" 1, "qty": 1 } )
// 或者
db.testCollection.find().sort( { "item": -1, "qty": -1 } )
```

### **TTL INDEX**

MONGO 中可以使用 TTL INDEX 对一些需要在一定时间后被删除的文件进行处理：

```
db.testCollection.createIndex(
    { "item": 1 }
    { expireAfterSeconds: 3600 }
)
```

TTL INDEX 不保证过期数据会在过期后立刻被删除，DOCUMENT 过期和 MONGO 从数据库中删除 DOCUMENT 的时间之间可能存在延迟。正常情况下，删除过期的 DOCUMENT 的后台任务每 60 秒运行一次。 如果需要修改 TTL INDEX 过期时间，可参考以下示例：

```
db.runCommand( { collMod: "collection", index: { keyPattern: { "item": 1}, expireAfterSeconds: 600 } } )
```

### **TEXT INDEX**

一个 COLLECTION 中最多只可以有一个 TEXT INDEX； MONGO 提供了 TEXT INDEX 以支持对字符串内容的文本搜索查询，TEXT INDEX 可以包含任何值为字符串或字符串元素数组的字段； 如需建立多个字段的 TEXT INDEX，可参考以下示例：

```
db.testCollection.createIndex(
    {
        "item": "text",
        "xxxx": "text"
    }
)
```

# MONGO OPS

## **COMMAND**

```
// 获取 MONGO 启动时的配置信息
db.runCommand( { getCmdLineOpts: 1 } )
// 关闭 MONGO 实例
db.runCommand( { shutdown: 1 } )
```

## **AUTHENTICATE**

在启用访问控制时，务必确认你已经至少拥有一个 userAdmin 或者 userAdminAnyDatabase 角色的用户在 admin 数据库中。这个用户能够管理用户和角色； 通过 mongo shell 在 admin数据库增加一个具有 userAdminAnyDatabase 角色 和 readWriteAnyDatabase 角色的用户；

```
use admin
db.createUser(
    {
        user: "root",
        pwd: "password",
        roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
    }
)
```

当开启访问控制后，如果需要在命令行中启动 mongod，则需要在命令行选项中添加 --auth 参数：

```
mongod --auth
```

## **REPLICA SET**

MONGO 的 REPLICA SET 是一组提供冗余和高性能的 mongod 进程。通常一个 REPLICA SET 的成员有：

- 主节点：接受所有的写操作；
- 从节点：通过复制主节点的操作来维护一个相同的数据集。从节点为特殊用途的配置文件提供了额外的配置项。例如，从节点可以配置为无投票权，或者 0 优先级；

REPLICA SET 的最小推荐配置是一个包含三个数据承载成员的三成员副本集：一个主节点和两个从节点。在一个 REPLICA SET 中最多可以有 50 个成员，但仅可能有 7 个可投票成员，确保可投票成员应该是奇数； REPLICA SET 的所有成员都可以接受读操作。但是在默认情况下，应用程序会将其读操作定向至主节点； 你可以出于特殊目的来配置从节点成员，你可以配置一个从节点用于：

- 阻止它在选举中成为主节点，适用于将该节点部署在备用数据中心或者充当一个冷备节点。在这种情况下可考虑将节点配置为 0 优先级副本集成员；
- 防止应用程序从节点读取数据，适用于在该节点上运行需要与正常流量分离的应用程序。在这种情况下可考虑将节点配置为隐藏副本集成员；
- 保持一个运行的历史快照，以便从某些错误恢复时使用。在这种情况下可考虑将节点配置为延迟副本集成员；

在某些情况下（例如有一个主节点和一个从节点，但由于成本约束无法添加另一个从节点），你可以在副本集中添加一个仲裁节点。仲裁节点没有数据集的副本，并且不能成为主节点。然而，仲裁节点可以参与主节点选举。一个仲裁节点只有 1 票选举权；

### **REPLICA SET LOG**

OPLOG 是一个特殊的有限集合，它对数据库中所存储数据的所有修改操作进行滚动记录。从 MONGO 4.0 开始，与其他有限集合不同，OPLOG 可以超过其配置的限制，以避免大多数提交点被删除； MONGO 在主节点上应用数据库操作，然后将这些操作记录到主节点的 OPLOG 上。然后从节点成员会以异步的方式复制并应用这些操作。所有 REPLICA SSET 成员都包含一个 OPLOG 的副本，其位于 local.oplog.rs 集合中，该集合可以让 REPLICA SET 成员维护数据库的当前状态；

### **REPLICA SET STARTING**

```
mongod --port {{PORT}} --dbpath {{DB_DATA_PATH}} --replSet {{REPLICA_SET_NANE}}
# 在主节点 MONGO 示例启动之后使用以下语句进行 REPLICA SET 的初始化工作
rs.initiate()
# 而后使用以下名来查看 REPLICA SET 的状态
rs.status()
```

在主节点初始化 REPLICA SET 后，可以使用下列语句来向 REPLICA SET 添加成员：

```
rs.add( {{HOSTNAME}}:{{PORT}} )
```

## **SHARDED**

分片是一种将数据分配到多个机器上的方法。MONGO 通过 SHARDED 技术来支持具有海量数据集和高吞吐操作的部署方案； 在数据库系统的数据集或者应用的吞吐量比较大的情况下，会给单台服务器的处理能力带来极大的挑战，因此通常采用垂直扩展和水平扩展方式来提升整体服务的能力；

- 垂直扩展：通过增加单个服务器的能力来实现，但是并不可以无限制地增加单个机器的配置，因此垂直扩展有一个实际的最大值；
- 水平扩展：通过将系统数据集划分至多台机器，并根据需要添加服务器来提升容量，虽然单个机器的总体数据容量可能不高，但是每台机器只需要处理整个数据集的某个子集，所以可能会提供比单个高速大容量服务器更高的效率，而且机器的数量只需要根据数据集大小来进行扩张，与单个机器的高性能硬件相比，这个方案可以降低总体成本。不过，这种方式会提供基础设施部署维护的复杂性；

### **SHARD CLUSTER COMPONENT**

MONGO SHARD CLUSTER 包括以下组件：

- SHARD：每个 SHARD 包含被 SHARD 的数据集中的一个子集，每个 SHARD 可以部署为 REPLICA SET 架构；
- MMONGOS：充当路由器，在客户端应用程序和分 SHARDED CLUSTER 之间提供接口；
- CONFIG SERVER：存储了分片集群的元数据和配置信息；

在 MONGO 中可以混合使用 SHARD 和 NO SHARD COLLECTION。SHARD COLLECTION 被 SHARD 并分布在 CLUSTER 中的各个 SHARD 中，而 NO SHARD COLLECTION 仅存储在主 SHARD 中；

### **SHARD KEY**

MONGO 使用 SHARD KEY 在各个 SHARD 之间分发 COLLECTION 中的 DOCUMENT。SHARD KEY 由 DOCUMENT 中的一个或多个字段组成。

### **SHARD DEPLOYMENT**

```
mkdir -p /www/mongoDB/shard/s{0,1,2,3}
mkdir -p /www/mongoDB/shard/log
mkdir -p /www/mongoDB/shard/config

# 启动 shard
mongod --port 27020 --dbpath=/www/mongoDB/shard/s0 --logpath=/www/mongoDB/shard/log/s0.log --logappend --fork
...
mongod --port 27023 --dbpath=/www/mongoDB/shard/s3 --logpath=/www/mongoDB/shard/log/s3.log --logappend--fork

# 启动 config server
mongod --port 27100 --dbpath=/www/mongoDB/shard/config --logpath=/www/mongoDB/shard/log/config.log --logappend --fork

# 启动 route process
mongos --port 40000 --configdb localhost:27100 --fork --logpath=/www/mongoDB/shard/log/route.log --chunkSize 500

# 连接并配置
mongo admin --port 40000

db.runCommand( { addshard: "localhost": 27020 } )
...
db.runCommand( { addshard: "localhost": 27023 } )
# 设置分片存储的数据库
db.runCommand( { enablesharding: "test" } )
# 设置分片存储的 COLLECTION 和 SHARD KEY
db.runCommand( { shardcollection: "test.log", key: { id: 1, time: 1 } } )
```

在部署完成后，可以直接连接 40000 端口来访问数据库；

## **MONGODUMP & MONGORESTORE**

在 MONGO 中我们可以使用 MONGODUMP 来备份 MONGO 的数据，该命令可以导出所有数据到指定目录中：

```
mongodump -h {{HOSTNAME}} -d {{DB_NAME}} -o {{BACK_DIR}}
```

在备份完数据后，如果需要导入数据至 MONGO 中，可以采用 MONGORESTORE 来进行恢复：

```
mongorestore -h {{HOSTNAME}}:{{PORT}} -d {{DB_NAME}} {{BACK_DIR}}
```

## **SSL & TLS**

在需要 MONGO 使用 TLS 协议进行连接，可通过 mongod.conf 进行以下配置：

```
net:
  tls:
    mode: requireTLS
    certificateKeyFile: /etc/ssl/mongodb.pem
    CAFile: /etc/ssl/caToValidateClientCertificates.pem
    allowConnectionsWithoutCertificates: false
```

同时 MONGOSH 使用以下示例进行连接：

```
mongosh --tls --host {{HOSTNAME}} --tlsCAFile {{TLS_CA_FILE}} --tlsCertificateKeyFile {{TLS_CERTIFICATE_KEY_FILE}}
```

# DONE