# 目录
~/mongo-test

__shard0 目录__  
./s0  
./s0/log  
./s0/data  


**shard1 目录**  
./s1  
./s1/log  
./s1/data  

__config 目录__  
./c0  
./c0/log  
./c0.data  

**router server 目录**  
./r0  
./r0/log  
./r0/data  

# 节点

## shard0

host: localhost  
port 27010

## shard1

host: localhost  
port: 27011

## config

host: localhost  
port: 27020

## router server

host: localhost  
port: 27030

# 名称

## shard0

name: s0

## shard1 

name: s1

## config

name: c0

## router server

name: r0

# collection

name：zone

```json
{
  "_id": "zone",
  "zone": "china/india",
  "userId": 111/112
  "data": "liqiming_china/liqiming_india"
}

```

# database

database: test

# zone

china 

存储在s0

india

存储在s1

# index

field: zone  

sort: 1

# command

mkdir s0 s0/data s0/log  
mkdir s1 s1/data s1/log

mkdir c0 c0/data c0/log

mkdir r0 r0/data r0/log

## 启动s0

mongod --port 27010 --bind_ip localhost --logpath ./s0/log/mongo.log --dbpath ./s0/data --shardsvr --replSet s0 --fork

## 启动 s1

mongod --port 27011 --bind_ip localhost --logpath ./s1/log/mongo.log --dbpath ./s1/data --shardsvr --replSet s1 --fork

## sh 到 s0

mongosh --host localhost --port 27010

## init s0

rs.initiate(
  {
    _id : "s0",
    members: [
      { _id : 0, host : "localhost:27010" }
    ]
  }
)

## sh 到 s1

mongosh --host localhost --port 27011

## init s1

rs.initiate(
  {
    _id : "s1",
    members: [
      { _id : 0, host : "localhost:27011" }
    ]
  }
)


## 启动 c0

mongod --configsvr --replSet c0 --dbpath ./c0/data --logpath ./c0/data/mongo.log --bind_ip localhost --port 27020 --fork

## sh 到 c0

mongosh --host localhost --port 27020

## init s1

rs.initiate(
  {
    _id : "c0",
    configsvr: true,
    members: [
      { _id : 0, host : "localhost:27020" }
    ]
  }
)

## 启动 r0

mongos --config mongos.yaml

## sh  到 r0

mongosh --host localhost --port 27030

sh.addShard( "s0/localhost:27010")

sh.addShard( "s1/localhost:27011")


db.adminCommand( { enableSharding: "test" } )

sh.shardCollection("test.zone", { zone : 1, userId: 1} )

sh.disableBalancing("chat.message")

 sh.isBalancerRunning() 

 sh.addShardTag("s1", "china")

 sh.addShardTag("s0", "india")

db.zone.createIndex({"zone":1,"userId":1})

sh.updateZoneKeyRange("test.zone", { zone: "china", userId: MinKey }, { zone: "china", userId: MaxKey  }, "china")

sh.updateZoneKeyRange("test.zone", { zone: "india", userId: MinKey }, { zone: "india", userId: MaxKey }, "india")

sh.enableBalancing("chat.messages")

db.zone.insertMany(
   [{
  "zone": "china",
  "userId": 111,
  "data": "liqiming_china"
}, {
  "zone": "india",
  "userId": 112,
  "data": "liqiming_india"
}]
)

