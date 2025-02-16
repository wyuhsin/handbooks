# Redis Handbook

# **REDIS DEV**

REDIS 的每个命令的执行都是原子性的；

## **DATA TYPE**

无论是哪种类型的格式，OBJECT-KEY:ID 总是一种推荐的命名方式。同时 REDIS 的 KEY 是二进制安全的，这意味着可以使用任何二进制序列作为 KEY；

```
# if key exiset return 1, else return 0
exists {{KEY}}
# delete key, successed return 1, failed return 0
del {{KEY}}
# return the value type
type {{KEY}}
# set key ttl
expire key {{TTL}}
# get key ttl
ttl {{KEY}}

```

### **STRING**

在默认的情况下单个 REDIS STRING 最大为 512 MB；

大多数的 STRING 操作的时间复杂度为 O(1)，这意味着这些操作具有高效率。然而 SUBSTR、GETRANGE、SETRANGE 操作的时间复杂度为 O(n)；

```
set {{KEY}} {{VALUE}}
# if key do not exist, set value
set {{KEY}} {{VALUE}} nx
# if key exist, set value
set {{KEY}} {{VALUE}} xx
# set value with ttl(second)
set {{KEY}} {{VALUE}} ex {{TTL}}
# set value with ttl(millsecond)
set {{KEY}} {{VALUE}} px {{TTL}}
# set multiple values
mset {{KEY}} {{VALUE}} {{KEY}} {{VALUE}} ...
get {{KEY}}
mget {{KEY}} {{KEY}} {{KEY}} ...
# incr, incrby, decr, decrby are atomoic operate
incr {{KEY}}
incrby {{KEY}} {{VALUE}}
decr {{KEY}}
decrby {{KEY}} {{VALUE}}

```

### **LISTS**

在 REDIS 中 LIST 的最大长度为 2^32 -1 个元素；

在 LIST 中操作头元素或者尾元素的时间负责度为 O(1)，操作其他的元素的时间负责度为 O(n)。使用 LINDEX、LINSERT、LSET 这些命令时需要小心，特别是在大的 LIST中；

通过 LIST 的不同操作可以实现 QUEUE、STACK 的效果；

```

# let a list like a queue, fifo
lpush {{LIST_NAME}} {{VALUE}}
rpop {{LIST_NAME}} {{VALUE}}
# let a list like a stack, first in last out
lpush {{LIST_NAME}} {{VALUE}}
lpop {{LIST_NAME}} {{VALUE}}

# keep the elements in the range
ltrim {{LIST_NAME}} {{INDEX}} {{INDEX}}
# insert element after|before a element
linsert {{LIST_NAME}} {{BEFORE|AFTER}} {{VALUE}}
# remove latest element and join another list
rpoplpush {{LIST_NAME}} {{LIST_NAME}}
# remove element in the list
lrem {{LIST_NAME}} {{COUNT}} {{VALUE}}
# set element at index
lset {{LIST_NAME}} {{INDEX}} {{VALUE}}
# remove and return latest element
rpop {{LIST_NAME}}
# get the elements in the ranges
lrange {{LIST_NAME}} {{INDEX}} {{INDEX}}
# return list length
llen {{LIST_NAME}}
# atomic operate, move element from list a to list b
lmove {{LIST_NAME}} {{LIST_NAME}} left left
# blocking operations on lists
# wait for a element in the list, but return if after {{WAIT_TIME}} no element is available. if WAIT_TIME is 0, mean forever
brpop {{LIST_NAME}} {{WAIT_TIME}}

```

### **HASHES**

HASHES 是一个散列表，每一个 KEY 只能存放一个 VALUE；

大多数的 REDIS HASH 命令的时间复杂度为 O(1)，少部分的命令例如 HKEYS、HVALS、HGETALL 的时间复杂度为 O(n)；

每一个 HASH 最多能够存储 2^32-1 个键值对；

```
# set hash, return number of insert
hset {{HASH_NAME}} {{KEY}} {{VALUE}} {{KEY}} {{VALUE}} ...
# set hash, when key do not exist
hsetnx {{HASH_NAME}} {{KEY}} {{VALUE}}
# get a element from hash
hget {{HASH_NAME}} {{KEY}}
# get multiple elements from hash
hmget {{HASH_NAME}} {{KEY}} {{KEY}} ...
# get all elements from hash
hgetall {{HASH_NAME}}
# incrby element from hash
hincrby {{HASH_NAME}} {{KEY}} {{INCRBY_VALUE}}
# get all keys
hkeys {{HASH_NAME}}
# get all values
hvals {{HASH_NAME}}
# if key exist return 1, else return 0
hexists {{HASH_NAME}} {{KEY}}
# delete elements from hash
hdel {{HASH_NAME}} {{KEY}} {{KEY}} ...
# return hash length
hlen {{HASH_NAME}}

```

### **SETS**

SET 类型是 REDIS 中一个无序集合，其中的每个元素不可重复；

在 REDIS 中 SET 的最大长度为 2^32-1 个元素；

大部分的 SET 操作的时间复杂度都是 O(1)，包括添加、删除等，这意味着这些操作具有高效率。然而在具有成百上千个元素的 SET 中，使用 SMEMBERS 命令应该要是十分小心的，该命令的时间复杂度为 O(n)；

```
# insert elements to set, return number of inserts
sadd {{SET_NAME}} {{VALUE}} {{VALUE}} ...
# print all elements
smembers {{SET_NAME}}
# return count of set
scard {{SET_NAME}}
# remove and return a random element from sets
spop {{SET_NAME}}
# remove elements from sets
srem {{SET_NAME}} {{VALUE}} ...
# if element in set return 1, else return 0
sismember {{SET_NAME}} {{VALUE}}
# return union of tow sets and removes duplicate elements
sunion {{SET_NAME}} {{SET_NAME}}
# store the union of two sets, return number of elements
sunionstroe {{DESTINATION}} {{SET_NAME}} {{SET_NAME}}
# return inter of two sets
sinter {{SET_NAME}} {{SET_NAME}}
# store the inter of two sets, return number of elements
sinterstroe {{DESTINATION}} {{SET_NAME}} {{SET_NAME}}
# return diff on two sets
sdiff {{SET_NAME}} {{SET_NAME}}
# store the diff of two sets, return number of elements
sdiffstore {{DESTINATION}} {{SET_NAME}} {{SET_NAME}

```

### **SORTED SETS**

SORTED SETS 是一种类似于 SETS 和 HASH 的混合结构，SORTED SETS 由唯一的、不重复的字符串元素组成，由此从某种意义上来说，有序集也是一种集合；

SORTED SETS 中的每一个元素都与一个浮点数进行关联，这也是为什么 SORTED SETS 类似于 HASH，因为每一个元素映射到一个值；

此外，SORTED SETS 中的元素是按照顺序获取的。如果 A 的值比 B 的值大，则 A 大于 B。如果 A 的值等于 B 的值，那么比较它们的 VALUE；

```
# add a element
zadd {{SS_NAME}} {{SCORE}} {{VALUE}}
# incrby designated elements score
zincrby {{SS_NAME}} {{INCRBY_VALUE}} {{VALUE}}
# return count of sorted sets
zcard {{SS_NAME}}
# return all elements in the ranges
zrange {{SS_NAME}} {{INDEX}} {{INDEX}}
# return all elements in the ranges desc
zrevrange {{SS_NAME}} {{INDEX}} {{INDEX}}
# return all elements in the ranges with scores
zrange {{SS_NAME}} {{INDEX}} {{INDEX}} withscores
# return all elements with score before
zrangebyscore {{SS_NAME}} -inf {{SCORE_VALUE}}
# remove all elements in the scores
zremrangebyscore {{SS_NAME}} {{SCORE_VALUE}} {{SCORE_VALUE}}
# return the element's index
zrank {{SS_NAME}} {{VALUE}}
# return the element's -index
zrevrank {{SS_NAME}} {{VALUE}}
# return count of elements between min and max
zcount {{SS_NAME}} {{MIN}} {{MAX}}
# remove all elements with score between min and max
zremrangebyscore {{SS_NAME}} {{MIN}} {{MAX}}
# remove all elements with rank between min and max
zremrangebyrank {{SS_NAME}} {{MIN}} {{MAX}}

```

### **STREAMS**

REDIS STREAMS 是一种数据类型，其作用类似于附加日志，可以使用 STREAMS 用来实时记录。例如事件溯源、传感器监控、通知等消息存储；

在 STREAMS 添加一个元素的时间复杂度为 O(1)，访问任何一个元素的时间复杂度为 O(n)，其中 n 取决于 STREAM 的 ID；

STREAM ID 由 - 组成，ID 值必须唯一；

```
# add element to streams, like this: xadd temperatures:us-ny:10007 * temp_f 87.2 pressure 29.69 humidity 46
xdd {{STREAM_NAME}} {{VALUE}}
# add element with id
xdd {{STREAM_NAME}} {{ID}} {{VALUE}}
# get all elements in the ranges
xrange {{STREAM_NAME}} {{STREAM_ID}} {{+|-}} COUNT {{COUNT}}
# read up to {{COUNT}} new stream entries, starting at the of the stream, and block for up to {{TIME}}ms
xread count {{COUNT}} block {{TIME}} streams {{STREAM_NAME}}
# return length of streams
xlen {{STREAM_NAME}}

```

### **GEOSPATIAL**

REDIS GEOSPATIAL 能够存储地理坐标并进行搜索。此数据结构可用于查找给定半径或者边界框内的附近点；

```
# add a element, like this: geoadd locations:ca -122.27652 37.805186 station:1
geoadd {{GEO_NAME}} {{VALUE}}
# find all locations within a {{DISTANCE}} kilometer radius of a given location, and return the distance to each location
geosearch {{GEO_NAME}} fromlonlat {{LON}} {{LAT}} byradius {{DISTANCE}} km withdist
```

### **HYPERLOGLOG**

HLL 是一种估计集合基数的数据结构。作为一种概率数据结构，HLL 以趋近完美的准确性换取高效的空间利用；

REIDS HLL 实现最多使用 12KB，并允许提供 0.81% 的标准错误；

写入和读取 HLL 都是在恒定时间和空间内完成的，合并 HLL 的时间复杂度为 O(n)；

HLL 最多可以估计包含 2^64 个元素；

```
# add element
pfadd {{HLL_NAME}} {{VALUE}}
# return count of hyperloglog
pfcount {{HLL_NAME}}
# merge multiple hyperloglog into one
pfmerge {{DEST_HLL_NAME}} {{HLL_NAME}} {{HLL_NAME}}

```

### **BITMAP**

BITMAP 实际上并不是一种数据类型，而是定义在 STRING 类型上的面向位的操作。因为 STRING 是二进制安全的并且最大的长度为 512MB，所以 STRING 可以建立 2^32 个不同的位；

设置和获取 BITMAP 的时间复杂度为 O(1)，而对 BITMAP 进行位操作的时间复杂度位 O(n)；

```
# set the {{INDEX}} bit of the key
setbit {{KEY}} {{INDEX}} {{1|0}}
# get the {{INDEX}} bit of the key
gettbit {{KEY}} {{INDEX}}
# let two keys perform bit operations, operations: and | or | xor | not
bitop {{OPERATIONS}} {{DEST_KEY}} {{KEY}} {{KEY}}

```

### **BITFILEDS**

BITFILEDS 可以一次对多个位进行操作，这个命令有三个子命令：get、set、incrby，都可以对指定片段进行读写，但是最多一次只能处理 64 个连续的位，如果超过 64 个位，则要使用多个子指令；

在 BITFILED 中 无符号数字为 u，有符号数字为 i，同时应该与所需长度一起搭配使用，例如：4 位无符号整数应该写为 u4。有符号数最多可以取 64 位，而无符号数最多只能取 63 位；

```
bitfield {{KEY}} get {{TYPE}} {{START_AT}}
bitfield {{KEY}} incrby {{TYPE}} {{START_AT}} {{INCRBY_VALUE}}
bitfield {{KEY}} set {{TYPE}} {{START_AT}} {{LENGTH}} {{VALUE}}

```

## **TRANSACTION**

REDIS 的 TRANSACTION 可以一次执行多个命令，并且带有以下重要的特征：

- 批量操作在发送 EXEC 命令之前被放入队列缓存；
- 收到 EXEC 命令后进入 TRANSACTION 执行，TRANSACTION 中任意命令执行失败，其余的命令仍然被执行；
- 在 TRANSACTION 的执行过程中，其他客户端提交的命令请求不会插入到 TRANSACTION 的执行序列当中；

REDIS 的 TRANSACTION 没有添加任务维持原子性的机制，所以 REDIS 的 TRANSACTION 的执行并不是原子性的；

事务可以理解为一个打包的批量执行脚本，但批量指令并非原子性的操作，中间某条指令的失败不会导致前面一组指令的回滚，也不会造成后续的指令不执行；

```
# transaction start
multi
# some commands
...
...
...
# transaction end
exec
# terminate transaction
discard
# watch a key, terminate transation if this keys were changed by other commands before the transation executes
watch {{KEY}}
# cancel watch
unwatch {{KEY}}

```

## **PIPELINE**

REDIS 服务端是支持 PIPELINE 的，而在 CLI 中并没有提供 PIPELINE 的命令，但在各种的实现的 REDIS 客户端中基本有 PIPELINE 的实现；

与每次单条的发送命令不同，PIPELINE 可以一次发送多条记录，而 REDIS SERVER 会将请求放到队列中，执行完毕后一次性发送结果；这可以有效的减少了数据来回的传输时间；

注意事项：PIPELINE 期间将会独占链接，在此期间不能进行非 PIPELINE 类型的其他操作，知道 PIPELINE 关闭位置。如果 PIPELINE 的指令集很庞大，为了不干扰连接中的其他操作，可以考虑单独为 PIPELINE 新建一个连接；

适用场景：不适合需要立刻知道该操作是否成功的业务场景。当允许一部分比例的执行失败的时候，PIPELINE 就可以被考虑适用；

## **PUBLISH / SUBSCRIBE**

REDIS 发布订阅是一种消息通信模式，发送者发送消息，接收者接收消息。REDIS 客户端可以订阅任意数量的 CHANNEL；

```
# subscribe channel
subscribe {{CHANNEL}}
# publish channel
publish {{CHANNEL}} {{VALUE}}
# unsubscibe channel
unsubscribe {{CHANNEL}}
# subscribe to one or more channel matching the given pattern
psubscribe {{PATTERN}} {{PATTERN}} ...
# unsubscribe all channel matching the given pattern
punsubscribe {{PATTERN}} {{PATTERN}}

```

# **REDIS OPS**

```
# start redis server with config
redis-server /REDIS/CONFIG/PATH
# connect redis with password
redis-cli -a {{PASSWORD}}

```

## **PERSISTENCE**

REDIS 运行时数据保存在内存中，当设备重启时，数据将会全部丢失。因此 REDIS 提供了两种的持久化方式；

### **RDB**

RDB 持久化时生成某个时间点的快照文件。执行 RDB 持久化时，REDIS 会 FORK 出一个子进程，子进程将内存中的数据写入到一个紧凑的文件中，因此它保存的是某个时间点的完整数据；

优点：

- RDB 的文件体积比较小，适合备份及传输；
- 性能比 AOF 模式高；
- RBD 恢复比 AOF 要快；

缺点：

- 服务器故障的时候会丢失最后一次备份之后的数据；
- REDIS 保存 RDB 时， FORK 子进程操作期间，REDIS 服务会停止响应，但如果数据量大而且 CPU 时间紧张，则停止时间可能长达 1 秒；

```
# redis.conf
# at least 1 key changed within 900 seconds
save 900 1
# at least 10 key changed within 300 seconds
save 300 10
# at least 100000 key changed within 60 seconds
save 60 10000
# when an error occurs in RDB persistence, whether to continue the work
stop-writes-on-bgsave-error no
# compress rdb file
rdbcompression yes
# check rdb file
rdbchecksum yes
# rdb file name
dbfilename dump.rdb
# data dir
dir /REDIS/DATA/PATH

```

### **AOF**

AOF 是日志追加模式，本质就是将客户端每一次操作记录追加到指定的 AOF 日志文件中，在 AOF 文件体积到达一定大小时，可以自动在后台重写 AOF 文件；

AOF 持久化的 fsync 策略支持：

- 不执行 fsync：由操作系统保证数据同步到磁盘，速度最快；
- 每秒执行 1 次：最多丢失最近 1s 的数据；
- 每条命令：绝对保证数据持久化；

优点：

- 充分保证数据的持久化，正确的配置一般最多丢失 1s 的数据；
- AOF 文件内容是以 REDIS 协议格式保存；

缺点：

- AOF 文件通常大于 RDB 文件；
- 速度会慢于 RDB，具体的看具体的 fsync 策略；
- 重新启动 REDIS 会极低的概率会导致无法将数据集恢复成保存的原样；

```
# fix aof
redis-check-aof --fix
# open aof
config set appendonly yes

```

```
# redis.conf
# use aof
appendonly yes
# aof file name
appendfilename "appendonly.only"
# aof policy, no | always | everysec
appendfsync {{POLICY}}
# when rewriting, whether to append data
no-appendfsync-on-rewriter no
# what percentage of the size of the last rewritten aof file should be rewritten
auto-aof-rewrite-percentage 100
# set the minimim aof file size allowed to rewrite
# if set no, user must run redis-check-aof by self when file error
aof-load-truncated yes

```

## **AUTHENTICATE**

配置一些有关于 REDIS 安全与认证的模块；

```
# redis.conf
# protected mode, yes | no. if set yes, only allow local connect.
protect-mode no
# set password
requirepass {{PASSWORD}}

```

## **REPLICATION**

在分布式系统中为了解决单节点性能不足问题，通常会把数据复制到多个副本的其他机器，满足故障恢复和负载均衡等，REDIS 也是如此；

复制功能同样也是 REDIS 高可用的基础，包括之后的哨兵模式和集群模式都是在主从复制的基础上实现的高可用；

主从复制原理：

1. SLAVE 通过 slaveof {{MASTER_IP}} {{MASTER_PORT}} 连接 MASTER， 并发送 SYNC；
2. MASTER 接收到 SYNC，会立刻出发 BGSAVE，后台保存 RDB，并发送给 SLAVE，如果是第一次连接，则采用全量复制；
3. SLAVE 接收后首先会清空自己的数据，再载入从 MASTER 接收到的 RDB 文件到自己的内存中；
4. MASTER 会陆续将中间产生的新的操作，保存并发送给 SLAVE；
5. 到此，主从节点就可以正常工作。在此之后，MASTER 只要发生了新的操作，都会以命令传播的形式自动发送给 SLAVE；
6. 所有复制相关的信息，从 info 信息都都可以查到，即使重启任何节点，它们的主从关系依然存在；
7. 如果发送主从关系断开，且 SLAVE 数据没有任何损坏，在下次重连之后，SLAVE 发送 PSYNC 给主库，MASTER 只会将 SLAVE 缺失的部分数据同步给 SLAVE，以此达到快速恢复的效果；

```
# redis.conf
slaveof {{MASTER_IP}} {{MASTER_PORT}}

# command
# cancle slave
slaveof no one

```

### **PARTIAL COPY**

由于全量复制在 MASTER 数据量较大时效率太低，因此 REDIS 2.8 开始提供部分复制，用于处理网络中断时的数据同步；

在部分复制中有三个重要概念：

复制偏移量：执行复制的双方会分别维护一个复制偏移量 offset，MASTER 每次向 SLAVE 节点同步了 N 个字节数据后，将修改自己的 offset，从节点每次从主节点同步了 N 个字节后，将修改自己的 offset 为 offset + N；

复制积压缓冲区：MASTER 内部维护了一个固定长度的、先进先出的队列作为复制积压缓冲区，其默认大小为 1MB，在 MASTER 进行命令传播时，不仅会把命令同步到 SLAVE，还会将命令写入复制积压缓存区。由于复制积压缓存区是定长的且是先进先出，所以它保存的是 MASTER 最近执行的命令，时间较早的写命令会被挤出缓冲区。因此，当主从节点 offset 的差距超过缓冲区长度时，将无法执行部分复制，只能执行全量复制；

服务器运行 ID：每个 REDIS 节点都有其运行 ID，运行 ID 由节点在启动时自动生成，MASTER 会将直接的运行 ID 发送给 SLAVE。如果 SLAVE 保存的运行 ID 和现在 MASTER 的运行 ID 相同，说明主从节点之前同步过，会先尝试是否可以进行部分复制；如果 SLAVE 保存的运行 ID 和现在 MASTER 的运行 ID 不同，则只能进行全量复制；

## **SENTINEL MODE**

在 REDIS 的主从复制模式下，一旦主节点出现故障无法提供服务，需要人工手动的将 SLAVE 晋升为 MASTER，很多的应用场景是无法接受这种处理方式的。因此需要实现 SENTINEL MODE 来解决该问题；

在 SENTINEL MODE 下会有若干个 SENTINEL 进行监控 REDIS 集群中 MASTER 的工作状态，在 MASTER 发生故障的时候，可以实现 MASTER 和 SLAVE 的切换，实现了系统的高可用；

SENTINEL 会不断的检查你的 MASTER 和 SLAVE 是否运行正常，当某个被监控的 REDIS 节点出现问题的时候，SENTINEL 可以通过 API 向管理员或者其他应用程序发送通知。当一个 MASTER 不能正常工作时，SENTINEL 会开始一次自动的故障迁移；

自动故障迁移流程：

- 将一个可用的 SLAVE 升级为新的 MASTER，并让连接失效的 MASTER 的 SLAVE改为复制当前的MASTER；
- 当客户端试图连接失效的 MASTER 时，集群也会向客户端返回新的 MASTER 地址，使得集群可以使用现在的 MASTER 替换失效的 MASTER；
- MASTER 和 SLAVE 切换后，MASTER 的 redis.conf、SLAVE 的 redis.conf 和 sentinel.conf 的配置文件内容都会发生对应的改变；

SENTINEL 进程的工作方式：

- 每个 SENTINEL 进程以每秒钟一次的频率向整个集群的 MASTER、SLAVE、其他 SENTINEL 发送一个 PING 命令；
- 如果一个实例距离最后一次有效回复 PING 命令的时间超过 down-after-milliseconds 选项所指定的值，则这个实例会被 SENTINEL 进程标记为主观下线；
- 如果一个 MASTER 被标记为主观下线，则正在监视这个 MASTER 的所有 SENTINEL 进程要以每秒一次的评率确认 MASTER 进入了主观下线状态；
- 当有足够数量的 SENTINEL 进程在指定的时间范围内确认 MASTER 进入了主观下线状态，则 MASTER 会被标记为客观下线；
- 在一般情况下，每个 SENTINEL 进程会以每 10 秒一次的频率向集群中的所有 MASTER、SLAVE 发送 info 命令；
- 当 MASTER 被 SENTINEL 标记为客观下线时，SENTINEL 进程向下线的 MASTER 的所有 SLAVE 发送 info 的频繁从 10 秒一次改为每秒 1次；
- 若没有足够数量的 SENTINEL 进程同意 MASTER 下线，MASTER 的客观下线状态就会被移除。若 MASTER 重新向 SENTINEL 进程发送 PING 命令返回有效回复，MASTER 的主观下线状态就会被移除；

```
# INSTALL
# isntall redis on three machines
mkdir -p /opt/redis_cluster/redis_6379/conf
mkdir -p /opt/redis_cluster/redis_6379/pid
mkdir -p /opt/redis_cluster/redis_6379/logs
wget <http://download.redis.io/releases/redis-3.2.9.tar.gz>
tar zxf redis-3.2.9.tar.gz -C /opt/redis_cluster/
ln -s /opt/redis_cluster/redis-3.2.9/ /opt/redis_cluster/redis
cd /opt/redis_cluster/redis
make && make install
cd /opt/redis_cluster/redis_6379/conf
cat > redis_6379.conf << EOF
daemonize yes
bind {{IP_ADDR}}
port 6379
pidfile /opt/redis_cluster/redis_6379/pid/redis_6379.pid
logfile /opt/redis_cluster/redis_6379/logs/redis_6379.log
databases 16
dbfilename redis_6379.rdb
dir /data/redis_cluster/redis_6379
requirepass {{PASSWORD}}
masterauth 123
EOF

# start reids on each machine
redis-server /opt/redis_cluster/redis_6379/conf/redis_6379.conf

# config sentinel on each machine
mkdir -p /opt/redis_cluster/redis_26379/conf
mkdir -p /opt/redis_cluster/redis_26379/pid
mkdir -p /opt/redis_cluster/redis_26379/logs
cat > /opt/redis_cluster/redis_26379/conf/redis_26379.conf << EOF
bind {{IP_ADDR}
port 26379
daemonize yes
logfile /opt/redis_cluster/redis_26379/logs/redis_26379.log
dir /data/redis_cluster/redis_26379
sentinel monitor mymaster {{MASTER_IP_ADDR}} 6379 2
sentinel down-after-milliseconds mymaster 3000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 18000
sentinel auth-pass mymaster {{SENTINEL_PASSWORD}}
EOF

# COMMAND
# set master on each slave
redis-cli -h {{SLAVE_HOSTNAME}} -a {{PASSWORD}} slaveof {{MASTER_IP_ADDR}} {{PORT}}
# start sentinel on each machine
redis-sentinel /opt/redis_cluster/redis_26379/conf/redis_26379.conf
# get master addr by master name
redis-cli -h {{HOSTNAME}} -p {{SENTINEL_PORT}} -a {{SENTINEL_PASSWORD}} get-master-addr-by-name {{MASTER_NAME}}
# print relication info
redis-cli -h {{HOSTNAME}} -p {{SENTINEL_PORT}} -a {{SENTINEL_PASSWORD}} info replication
# set failover
redis-cli -h {{HOSTNAME}} -p {{SENTINEL}} failover {{MASTER_NAME}}
# set slave priority
redis-cli -h {{HOSTNAME}} -p {{REDIS_PORT}} config set slave-priority {{PRIORITY_VALUE}}

```

## **REDIS CLUSTER**

### **INTRODUCTION**

集群高性能：

1. 在多个 SHARD NODE 中，将 16384 个 SLOT 均匀分布分布到多个 SHARD NODE 中；
2. 在存储数据的时候，将 KEY 做 CRC16 计算，后与 16384 进行 MOD 运算，得出 SLOT；
3. 根据计算出的 SLOT 找到相应的 SHARD NODE 的主节点，存储到相应的 SLOT 上；
4. 如果 CLIENT 当时连接的 NODE 不是要存储的 SHARD NODE，SHARD CLUSTER 会将 CLIENT 连接切换至真正的存储 NODE 并进行数据存储；

集群高可用：在搭建集群时，会为每一个 SHARD 的 MASTER 对应若干个 SLAVE，以实现 salveof 的功能，同时当 MASTER 宕机的时候，实现类似于 SENTINEL 的 failover 的功能；

1. REDIS 会有多组 SHARD NODE 构成；
2. REDIS CLUSTER 使用 16384 个 SLOT 存储数据；
3. 每个 SHARD NODE 获取 16384 / SHARD NODE 数量个的 SLOT；
4. 在部署集群的时候，存储同一范围 SLOT 的 NODE 应该尽量避免部署在一台物理主机上，防止宕机造成整个 SHARD 数据丢失；

```
# INSTALL
# node1
mkdir –p /data/redis_cluster/redis_6380/conf
mkdir –p /data/redis_cluster/redis_6380/logs
mkdir –p /data/redis_cluster/redis_6380/pid
mkdir –p /data/redis_cluster/redis_6381/conf
mkdir –p /data/redis_cluster/redis_6381/logs
mkdir –p /data/redis_cluster/redis_6381/pid
cat > /opt/redis_cluster/redis_6380/conf/redis_6380.conf << EOF
bind 10.0.0.51
port 6380
daemonize yes
pidfile "/opt/redis_cluster/redis_6380/pid/redis_6380.pid"
logfile "/opt/redis_cluster/redis_6380/logs/redis_6380.log"
dbfilename "redis_6380.rdb"
dir "/data/redis_cluster/redis_6380/"
cluster-enabled yes
cluster-config-file nodes_6380.conf
cluster-node-timeout 15000
EOF

cd /opt/redis_cluster/
cp redis_6380/conf/redis_6380.conf redis_6381/conf/redis_6381.conf
sed -i 's#6380#6381#g' redis_6381/conf/redis_6381.conf
rsync -avz /opt/redis_cluster/redis_638* {{NODE01_HOSTNAME}}:/opt/redis_cluster/
rsync -avz /opt/redis_cluster/redis_638* {{NODE02_HOSTNAME}}:/opt/redis_cluster/
redis-server /opt/redis_cluster/redis_6380/conf/redis_6380.conf
redis-server /opt/redis_cluster/redis_6381/conf/redis_6381.conf
# node2
find /opt/redis_cluster/redis_638* -type f -name "*.conf"|xargs sed -i "/bind/s#51#52#g"
mkdir –p /data/redis_cluster/redis_6380
mkdir –p /data/redis_cluster/redis_6381
redis-server /opt/redis_cluster/redis_6380/conf/redis_6380.conf
redis-server /opt/redis_cluster/redis_6381/conf/redis_6381.conf
# node3
find /opt/redis_cluster/redis_638* -type f -name "*.conf"|xargs sed -i "/bind/s#51#53#g"
mkdir –p /data/redis_cluster/redis_6380
mkdir –p /data/redis_cluster/redis_6381
redis-server /opt/redis_cluster/redis_6380/conf/redis_6380.conf
redis-server /opt/redis_cluster/redis_6381/conf/redis_6381.conf
ps -ef | grep redis
# master
redis-cli -h {{NODE1_HOSTNAME}} -p 6380 CLUSTER MEET 10.0.0.51 6381
redis-cli -h {{NODE1_HOSTNAME}} -p 6380 CLUSTER MEET 10.0.0.52 6380
redis-cli -h {{NODE1_HOSTNAME}} -p 6380 CLUSTER MEET 10.0.0.52 6381
redis-cli -h {{NODE1_HOSTNAME}} -p 6380 CLUSTER MEET 10.0.0.53 6380
redis-cli -h {{NODE1_HOSTNAME}} -p 6380 CLUSTER MEET 10.0.0.53 6381
# set slot on each node
redis-cli -h {{NODE1_HOSTNAME}} -p 6380 CLUSTER ADDSLOTS {0..5461}
redis-cli -h {{NODE2_HOSTNAME}} -p 6380 CLUSTER ADDSLOTS {5462..10923}
redis-cli -h {{NODE3_HOSTNAME}} -p 6380 CLUSTER ADDSLOTS {10924..16383}
# print cluster info
redis-cli -c -h {{NODE1_HOSTNAME}} -p 6380 cluster nodes
# set replicate slave on each node
redis-cli -c -h {{NODE_HOSTNAME}} cluster replicate {{NODE_HOSTNAME}}:{{6380's ID}}

```

## **BACKUP & RESTORE**

```
# clean all data in the target server
redis-cli -h {{TARGET_HOST}} -a {{PASSWORD}} flushall
# open aof in the source server
redis-cli -h {{SOURCE_HOST}} -a {{PASSWORD}} config set appendonly yes
# get aof file dir in the source server
redis-cli -h {{SOURCE_HOST}} -a {{PASSWORD}} config get dir

# import data to target server
redis-cli -h {{TARGET_HOST}} -a {{PASSWORD}} --pipe < appendonly.aof
# close aof in the source server
redis-cli -h {{SOURCE_HOST}} -a {{PASSWORD}} config set appendonly no
```

# **DONE**